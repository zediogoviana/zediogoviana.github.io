+++ 
title = "Developing a keyboard heatmap and customizing keys with Karabiner" 
date = "2021-06-02" 
author = "Zé Diogo" 
cover = "img/keyboard-cover.jpeg"
coverCredit = "darts and target"
keywords = ["heatmap", "keyboard", "karabiner", "elixir"]
description = "After joining Finiam, I started looking more into how I used my keyboard, and how I could become more efficient with it. So I decided to make a heatmap of my keystrokes using Elixir, and then create aliases or easier configurations for things that I identify as the most used with the help of Karabiner-Elements."
canonical="https://blog.finiam.com/blog/keyboard-heatmap-and-customizing-keys" 
+++

***(Originally posted [here](https://blog.finiam.com/blog/keyboard-heatmap-and-customizing-keys))***

For the last 5 years, I've been using an Apple Magic Keyboard. There wasn't any major reason to be using it, except having the same layout of my laptop, but it did the job pretty nicely.

After joining Finiam, I started looking more into how I used my keyboard, and how I could become more efficient with it. A thing that was bothering me for some time was the fact that my keyboard had a Portuguese layout, and every time I wanted to make `{`, `]` characters, **Finger Gymnastics 101**, would be required. I know I could always change the input language, but it wasn't the same thing. This was just the excuse I needed to get a new keyboard and with a US layout. So I went by and bought a **Keychron k2v2** and set myself up to start looking for more ways of having it optimized for me.

The first thing that I did was installing [Karabiner-Elements](https://karabiner-elements.pqrs.org/). It's a keyboard customizer for macOS, where you can easily swap a key with another, use a base set of complex modifications developed by the community or create your own rules. I started investigating and trying new combinations... But then it came to my mind: 

"Wouldn't it be nice if I could make a heatmap of my keystrokes and then create aliases or easier configurations for things that I identify as the most used?" 

Well, of course, it would, and I can do it as a nice excuse to play with Elixir again. So let's get our hands dirty and get into some code.

## Developing the Heatmapper

First, we need to collect our keystrokes. This would be a nice challenge to do, but I'll leave it on the side for now. So, I started looking into existing keyloggers in Github (Yeah, I know this sounds a bit weird because the chosen one could be malicious - I'm aware of that possibility). After a few possibilities, I found out [caseyscarborough/keylogger](https://github.com/caseyscarborough/keylogger), went through the code and it seemed simple to use and make changes to adjust it for my specific needs. Now let's dive into the heatmap and drawing part.

From the beginning I wanted the heatmapper to draw the keyboard and not use a base SVG layout (or something similar). Zamith's [mogrify_draw](https://github.com/zamith/mogrify_draw) enables me to draw keyboard keys in a simple way using `mogrify` as the base.

Another thing was that since the code will handle drawing a keyboard, it would be an even cooler solution to be able to draw any keyboard, given the provided structure of the keys. 

The first step is to parse the log file of keystrokes provided. As I mentioned earlier, I made some changes to the keylogger and this version can be found [here](https://github.com/zediogoviana/keylogger). You can then follow the instructions in the `README` on how to use it, but it outputs to the file the following on each keystroke read:

```
<keycode>::<keyname>
```

I'll probably remove `keyname` in the future, as I'm not using it in the application itself (just for debugging purposes), and therefore reduce the size of the log file.

Looking, at the code, we first open the file and then parse its content to generate a map of frequencies. In the `parse_line/1` function, it uses a regex to get the `keycode` in each line.

```elixir
{:ok, contents} <- File.read(path)
frequencies = parse(contents)

def parse(contents) do
  contents
  |> String.split("\n", trim: true)
  |> Enum.map(&parse_line(&1))
  |> Enum.reject(&is_nil(&1))
  |> Enum.frequencies()
  |> normalize_frequencies
end
```

The `normalize_frequencies/1` function, calculates the maximum value of keystrokes (the max frequency), and then for each value in the frequencies map, returns a normalized value: `value/max`. See the example below:

```
# log file contents
31::o
45::n
8::c
31::o

# frequencies map after parse
frequencies = %{ 31: 2, 45: 1, 8: 1 }

# frequencies normalized
max_frequency = 2
normalized = %{ 31: 2/2, 45: 1/2, 8: 1/2 }
```

Now that we have normalized values, we can jump to the drawing process. The first thing to do here is to implement the logic for several keyboard layouts to be drawn, so together with the path to the log file, it's also necessary to pass which keyboard type to draw.

For organizational purposes, I decided to divide the layouts by keyboard brand first, and then by type. For example, below we have the types `:keychron`, `:macbook` and `:niz`.

```elixir
def get_keyboard_layout(model, keyboard) do
  case model do
    :keychron -> 
      keyboard_layout(Layouts.Keychron, keyboard)
    :macbook -> 
      keyboard_layout(Layouts.Macbook, keyboard)
    :niz -> 
      keyboard_layout(Layouts.Niz, keyboard)
    _ -> {:error, :no_model}
  end
end

$> get_keyboard_layout(:keychron, :k2v2)
```

Calling the function with a valid model and type returns the keyboard layout. Each layout is under `layouts/<model>.ex`, and consists of a function with the name of the keyboard type and the following structure:

```elixir
# file: layouts/keychron.ex
def k2v2 do
  [
    [
      %{size: 1, keycode: 53, name: "esc"},
      ...
    ],
    [
      %{size: 1, keycode: 50, name: "`~"},
      ...
    ],
    ...
  ]
end
```

It's basically a list of rows of the keyboard, where each row, the list of keyboard keys is defined. A keyboard key can be defined with `keycode`, `name`, and `size` (assuming that a size of 2, will draw a key with a width of **2 * DEFAULT_KEY_PX_SIZE** and a fixed height of **DEFAULT_KEY_PX_SIZE**). It is also possible to define a `height` and `width` instead of `size` if we wish to draw keys with different heights.

Now let's see how everything is drawn.

The first step is using `mogrify_draw` to paint the base image where everything will be laid next. It'll create a png with the respective width and height of the keyboard we want to draw.

```elixir
defp draw_base_image(keyboard) do
  %Mogrify.Image{path: "heatmap.png", ext: "png"}
  |> custom("size", "#{95 * keyboard_width}x#{95 * keyboard_height}")
  |> canvas("white")
end
```

To draw each keyboard key, we go through each row, and subsequently across all row's keys, keeping track of the `xx_position` and `yy_position` that we are at the moment. With this information, we know the exact place in pixels to start drawing the key in the base image. But before actually drawing, we get the respective frequency for that key and the RGB color calculated accordingly to the frequency passed. Then, we draw a rounded rectangle (the key) and a white text over it (the key name).

```elixir
defp draw_key(key, xx_position, yy_position, image, frequencies) do
    freq = frequencies[to_string(key.keycode)] || 0
    {red, green, blue} = get_rgb_color(freq)

    image
    |> custom("fill", "rgb(#{red},#{green},#{blue})")
    |> rounded_rectangle(
      xx_position * @key_size + 10,
      yy_position * @key_size + 10,
      xx_position * @key_size + @key_size * key_width(key),
      yy_position * @key_size + @key_size * key_height(key),
      10,
      10
    )
    |> custom("fill", "white")
    |> Mogrify.Draw.text(
      xx_position * @key_size + 45,
      yy_position * @key_size + 45,
      key.name
    )
  end
```

Looking at the function `rounded_rectangle` it's derived from the ones [enabled by mogrify_draw](https://github.com/zamith/mogrify_draw/blob/master/lib/mogrify/draw.ex), but using the `roundRectangle` primitive from the `-draw` given by the `mogrify` command-line tools ([check here](https://imagemagick.org/script/command-line-options.php#draw) all the primitives from `mogrify` if you need something more custom). For this primitive, it's necessary to pass the base image, the upper left and bottom right point, together with the border-radius information.

```elixir
defp rounded_rectangle(
       image,
       upper_left_x,
       upper_left_y,
       lower_right_x,
       lower_right_y,
       border_w,
       border_h
     ) do
  image
  |> custom(
    "draw",
    "roundRectangle #{
      to_string(
        :io_lib.format("~g,~g ~g,~g ~g,~g", [
          upper_left_x / 1,
          upper_left_y / 1,
          lower_right_x / 1,
          lower_right_y / 1,
          border_w / 1,
          border_h / 1
        ])
      )
    }"
  )
end
```

After recursively going through all the rows and keys, the image `heatmap.png` is created. 

- Grey keys are keys that weren't used (or the keycode is not being detected by the keylogger.)
- Stronger blue keys are the ones used with less frequency.
- Mix of blue and red are the keys with mid-frequency.
- Red keys the most used ones.

{{< image src="https://cdn.sanity.io/images/5aprln8a/production/130f6dba27df6f6eb178e33d6c16e946315a460f-1520x570.png?w=840" alt="Heatmap of keystrokes." position="center" style="border-radius: 8px;" >}}

You can check the complete code and more detailed instructions on how to run it, in this [Github repo](https://github.com/zediogoviana/keyboard_heatmap).

Looking at the final result of a week of work we can identify some keys that are heavily used, such as the arrow keys, left command, left shift, left control, and even the backspace. It's somewhat a good representation of my usage as the referred keys take part in most of the keybindings that I use for work (except the backspace - not sure of what I delete so much 😅).

## Creating custom key modifications with **Karabiner-Elements**

As I mentioned earlier, initially I made some simple modifications with **Karabiner**, but now we are in the condition of making some more complex ones, that are not provided by the community. 

To create custom modification rules, we need to create a file under `~/.config/karabiner/assets/complex_modifications/` where you can have only one file with all the rules or group them in different files. After having valid rules they will appear as available to enable in the **Complex modifications** tab.

As an example, below, I'm creating a simple rule to change the `caps_lock` key to be a `control` key and just be a `caps_lock` when pressed together with `shift`.

`karabiner/assets/complex_modifications/capsToControl.json`
```json
{
  "title": "Change caps key",
  "rules": [
    {
      "description": "Change caps_lock key to control. (Use shift+caps_lock as caps_lock)",
      "manipulators": [
        {
          "type": "basic",
          "from": {
            "key_code": "caps_lock",
            "modifiers": {
              "mandatory": [
                "shift"
              ],
              "optional": [
                "caps_lock"
              ]
            }
          },
          "to": [
            {
              "key_code": "caps_lock"
            }
          ]
        },
        {
          "type": "basic",
          "from": {
            "key_code": "caps_lock",
            "modifiers": {
              "optional": [
                "any"
              ]
            }
          },
          "to": [
            {
              "key_code": "right_control"
            }
          ]
        }
      ]
    },
  ]
}
```

To see more on how to write these rules, you can the [Karabiner Configuration Reference Manual](https://karabiner-elements.pqrs.org/docs/json/) or use this [online editor](https://genesy.github.io/karabiner-complex-rules-generator/) to generate them.

## Wrapping up

There's still some stuff to do to make the heatmap more accurate and robust, such as making it work for more complex keyboard layouts, like split keyboards or with vertical gaps between keys. But I'll leave it for later when I have the time.

This was a fun and simple project to try some ideas and make something generic that could be used by other people. The experiments with **Karabiner-Elements** weren't all described here, but they were about trying more multiple key combinations, using only keys that I don't find necessary for my daily use, such as the `home`, `end`, `page down` and `page up`.

Hope you also liked reading it and that you give it a try.

Have a nice one and see you in the next post 👋

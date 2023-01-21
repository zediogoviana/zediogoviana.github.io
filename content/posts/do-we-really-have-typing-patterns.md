+++ 
title = "Giving TypingDNA API a spin - do we really have typing patterns?" 
date = "2021-08-11" 
author = "Zé Diogo" 
cover = "img/typing-dna-cover.jpeg"
coverCredit = "colorful test tubes"
keywords = ["typingdna", "patterns", "2fa"]
description = "Some time ago, we found TypingDNA. It was a different approach to MFA (multi-factor authentication) or SCA (strong customer authentication), and so we got interested in giving it a try between ourselves and see how it works." 
+++

Some time ago, we found [**TypingDNA**](https://www.typingdna.com/). It was a different approach to MFA (multi-factor authentication) or SCA (strong customer authentication), and so we got interested in giving it a try between ourselves and see how it works.

**TypingDNA** records typing information. This information represents how the user types, and then it's used to store and learn about the user's typing pattern.

So, as hinted, this blogpost is slightly different from the others. It's a joint experiment with me and some coworkers, Rafael and Davide, to test the **TypingDNA** API, and find answers for the big question: "Do we really have distinct enough typing patterns to use as authentication?"

There are two typing patterns that **TypingDNA** can collect.

 - same text patterns: when you want to authenticate a user on exactly the same text that was used when they were enrolled. (e.g. emails, usernames, passwords, etc)
 - any text patterns: when you want to authenticate a user on a different text than the one used for enrollment (e.g. emails, documents, etc)

You can get more information on typing types [here](https://www.typingdna.com/docs/types-of-typing-patterns.html).

## Initial Setup

In the **TypingDNA** docs, you can find some ways to test the API, and the one that we'll be using for this blogpost is through **Postman** with a [request collection](https://www.typingdna.com/docs/how-to-make-first-APIcall-postman.html) provided by them that you can download and start using almost right out of the box.

After setting up an account, we can start experimenting with it. To get our typing patterns we can use the [demo typing pattern viewer](https://www.typingdna.com/docs/typing-pattern-viewer.html) where we will type our sample texts and get an array of the flight and dwell duration combinations (typing behaviour). Below we can see the result of the same text pattern type, for **"Enter some text here"** typed by me.

```
0,3.1,0,0,20,1992813573,0,-1,-1,0,-1,-1,3,54,9,2,106,16,3,134,53,1,0,0,1,2,1,902248182,1,1,0,0,0,2,2560,1440,2,1015,88,0,1670821354|224,112|316,65|214,77|44,78|170,67|126,51|134,53|65,77|67,78|91,66|168,44|202,67|57,78|226,88|100,90|327,66|135,78|45,100|193,66|167,66
```

Note that the result by the previewer tool also includes an array of values for any text pattern type, separate from the one above. It was just omitted for simplicity purposes.

To start saving these patterns and also verifying them, there are docs for all the available requests, but we'll be using the [auto endpoint](https://api.typingdna.com/index.html#api-API_Services-Standard-auto). This endpoint takes care of automatically enrolling a new user and the new pattern as well as verifying a new one against the existing ones.

So let's put our hands to work and start saving our patterns.

## Same text pattern

First, we'll start by testing the same text patterns. For this, each one of us is saving our own email 3 times, and then we will write them a 4th time to verify if it matches or not (as a control for the experiment). Then, each one of us will try to verify others' emails and check if it matches, or if it helps to confirm the premise that we have indeed a typing pattern.

Time to verify,

|        |  Diogo  | Davide | Rafael |
|:------:|:-------:|:------:|:------:|
|  Diogo |   **true**  |  false |  false |
| Davide |  false  |  **true**  |  false |
| Rafael |  false  |  false |  **true**  |

 - **true** a successful match
 - **false** an unsuccessful match

Looking at the results table, it's clear that the control verification worked, and we weren't able to impersonate the others with this pattern.

## Any text pattern

Now it's time to check how things go with any text pattern type. In this test, each one of us is submitting the following 3 excerpts:

 - *To write good code is a worthy challenge and a source of civilized delight. -- stolen and paraphrased from William Safire*
 - *My favorite sandwich is peanut butter, baloney, cheddar cheese, lettuce, and mayonnaise on toasted bread with catsup on the side. -- Senator Hubert Humphrey*
 - *The sooner you fall behind, the more time you have to catch up.*

After saving these 3 sentences, we then repeat the previous process, where we try to verify against our own submissions (control) and the others'. The new sentence will be:

 - *No amount of careful planning will ever replace dumb luck.*
 
(Just a side note, these sentences were randomly selected using [fortune command](https://linux.die.net/man/6/fortune))

|            |  Diogo  | Davide | Rafael |
|:----------:|:-------:|:------:|:------:|
|  **Diogo** |  **true**   |  false |  false |
| **Davide** |  false  |  **true**  |  **true**  |
| **Rafael** |  false  |  false |  **true**  |

 - **true** a successful match
 - **false** an unsuccessful match

The results for any text pattern remain the same as for the same text, with the exception that Davide also matched positive for Rafael - the reason for this we believe it can be explained by the first and second paragraph of the next section.
The control verifications are always true, also.

## Wrapping up

Please bear in mind that we only submitted the bare minimum of required patterns to be able to verify. In a real-world situation with more submissions, results would get a lot better. For example, every time we got a negative match (in both pattern types), the confidence was 1 (high), but for positives, the confidence was 0 (low). In the **Advanced API**, (we were only using the standard one), there is also a [quality parameter](https://api.typingdna.com/index.html#api-guidelines-quality) that can help adjust for better results or better UX.

As seen above, the results were also good assuming the conditions mentioned, so we don't see it as a negative outcome. For example, Davide's result for any text pattern, may have matched `true` for Rafael because of the few cases we submitted, therefore the model was still far from perfect. There is the chance that if more had participated in the experience, with so few submissions, results would be even more random.

Overall it was a nice experience with **TypingDNA**. The API is well documented and it's very simple to start using and trying things.

Will **TypingDNA** replace 2FA over messages or any other conventional methods? We're not quite sure yet, but we believe that it's a nice alternative to have, if you wish to provide customers a different experience.

The dashboard page is also organized and clean. During the time we used it for this small test, we clearly noticed some differences and evolutions, which was nice to see. But there are still some things missing that would be cool to have; e.g. an overall view of the enrolled users - instead of having to look for them manually every time (note that everything works without this, but it helps to debug and check if things are OK without using the API). 

Give the developer version a try and see for yourself. So, that's it for today. Have a nice one and see you in the next blogpost 👋

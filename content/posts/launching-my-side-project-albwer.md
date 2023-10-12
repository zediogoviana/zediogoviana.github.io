+++
title = "Launching my side project Albwer"
date = "2023-10-11"
author = "Zé Diogo"
cover = "img/albwer_logo.png"
coverCredit = "albwer owl logo"
keywords = ["albwer", "dead mans switch", "deadmanswitch", "schedule messages", "messages"]
description = "If something happened to you now, and you couldn’t communicate your actions for a long time, what would you need to say to your family, friends or co-workers? Albwer allows you to configure messages to be delivered when you are absent for a certain amount of time. In its essence, it works as a Dead Man's Switch, but we believe it can be much more than that."
+++ 

Since the beginning of this year, a great friend and I, have been working on a project we decided to name [**Albwer**](https://www.albwer.com). I'll explain the origin of the naming further down, but first I'd like to expand on what this project is supposed to be.

## The problem

When explaining to some family members, or even other friends what is this new project we've been working on, during our free time and weekends, we generally introduce this question to the other person:

> Let's hope not, but if something was going to happen to you later today, that rendered you unable to communicate your actions for a long time (or even forever), what would you need to say to your family, friends or co-workers?

The base idea for this question is that **sh\*t happens**, either through an accident, a sudden disease, or we get ourselves unreachable for a certain amount of time. But are we prepared for a situation like that?

In an ever-growing digital world, almost everyone has a digital bank account that our significant other/parents don't know about, a crypto wallet stashed somewhere obscure, life insurance from the old ages, a Steam account full of skins, or just something that we'd like to see done when we won't be able to.

Some people already have some kind of mechanism to overcome the problems mentioned above. Some are more sophisticated than others, ones more manual than others, but overall, almost all solutions we saw implemented have flaws.

## The idea

Our idea starts with the approach to build a solution for the problem, but *as a Service* that users (including ourselves, family and friends) can rely and trust on.

That's where **Albwer** comes in. By the way, the name comes from joining ***Tyto Alba*** (the scientific name for our mascot, the Barn Owl) + ***Tower*** (where these owls can mostly be found). Why a Barn Owl, you may ask? Well, it's very much a joke from when we met back in high school. Maybe one day we talk more about it.

This way, with **Albwer** if something happens, and we can't guide our loved ones into what to do next, our platform should be responsible for warning those previously selected people with some kind of instructions, information, wishes, etc. To make that possible, the platform needs to have some kind of recurring check (automatic or not) to validate if the person is still reachable.

Having a set of instructions can help ensure that everyone knows who to contact in an emergency and how to get in touch with them. This can be especially important if you are not able to communicate with the message recipients directly. For example, if you were to become incapacitated or pass away, your instructions could include things like how to access important documents and accounts, what your final wishes are, or any specific guidelines you have for the respective recipients.

The main concern is that **Albwer** needs to be accurate so that these messages aren't sent before they're supposed to and that messages can be stored safely.

However, all of this doesn't mean the instructions defined in **Albwer** should have access codes or any sensitive information. You, as a user, can write whatever you wish, in a way that your recipient understands what you mean. Instead of having direct information, maybe just explain how to obtain those access codes you don’t want to reveal if you don’t fully trust the service.

As stated, wanting to be end users ourselves, we started to think about what we'd look for in a service/platform like this one.

## The proposed solution

The goal of this platform is to operate similarly to a Dead Man’s Switch ([Wikipedia](https://en.wikipedia.org/wiki/Dead_man%27s_switch#:~:text=.%5B16%5D-,Software,-%5Bedit%5D)), but we believe that it can be more than just that. We want this platform to be accessible to all non-tech-experts without needing to set up their own infrastructure and manage security, but also to be a valid solution and the go-to platform for the experts by providing different levels of trust to write their messages, together with customisation of the service.

To achieve that, we want to start simple and have a base setup for people who don't want to worry about a new service. At the end of the day, it should just be:
 - a customizable recurring check in your email account.
 - a set of messages and respective recipients.
     - each message can have an individual maximum *time away* and get individually triggered for being sent.

For the more experienced users, we want to provide different levels of trust for each message, an automatic way to *incinerate* the account, and other ideas that may come up.

### Who needs a service similar to a Dead Man's Switch?

 - Tech-savvy users that use a lot of services and have several bank accounts/crypto wallets/other digital services they want their family members to know about.
 - Contractors that want to ensure their employer has the necessary information to find a replacement or make other arrangements to continue with the work that they were doing.
 - Business leaders that want to leave a set of general instructions to their next-in-line person, on what they need/should do next to keep the business running smoothly.
 - People who want to leave a personal message to a friend or family member in the situation of something happening.
 - Other use cases you can think of… We plan to implement several degrees of customisation in the future (in the next section I'll explain what we are aiming for in the MVP launch). However, the platform should give you plenty of options to adapt to your situation.

## Small overview on the Road Map

We officially launched the first version on October 7th of this year (2023) [on Product Hunt](https://www.producthunt.com/products/albwer), so be sure to check it out. Or, you can go directly to [our Website](https://www.albwer.com).

After the MVP we plan to add new features like:

 - Possibility of choosing between email or automatic checks through an API, if you don't like receiving many emails.
 - Possibility of adding phone number recipients, and not just email ones.
 - Different levels of trust for a **message** that you want to store with us.
     -  A trust level represents the trust you require for the contents of a specific message. If you wish to have a message with sensitive content, and you are afraid that its contents are misused, you can try to use different trust levels that we provide. With each different level, you have less trust in our platform, but at the same time, it requires more configuration from your side. (You can learn more about these at our [FAQs](https://www.albwer.com#faqs).
 -  Many more...

By the way, it may be relevant or not, but of course, this is all built with Elixir and Phoenix, so you can already understand how we're dealing with the resilience of the platform. If you don't know what I'm talking about, you can check [this blog post](https://zediogoviana.github.io/posts/how-to-build-concurrent-and-resilient-service-in-elixir/) I wrote some time ago 😅 

## How is Albwer different from similar services?

- We introduced the concept of Trust Levels, already mentioned before.
- Messages are encrypted at applicational and database levels. Even if a bad actor gains access to our database, they still wouldn't be able to see what's written there.
- We don't keep your messages for more than the necessary time. When a message is triggered to be sent after the maximum time away has been reached, we completely delete it.
- Possibility to incinerate an account when all existing messages have been sent. This cancels your subscription and deletes your user account, completely losing the trace of what was sent before.
- Lots of possible configurations of the service.

## Wrapping up

Do we have your interest, but you still have some questions? Let us know through the [Product Hunt launch page](https://www.producthunt.com/products/albwer), or feel free to reach out in one of my socials below. You can ask questions, or even make suggestions. We're happy to evaluate if they fit in the current road map!

Overall, this has been a massive experience and enabled us to develop even more skills that we can apply in our daily jobs. Some days we were **designers**, others we were **product managers**, **DevOps**, **stakeholders**, **marketeers**, and many more. I'll probably write in more depth about this whole experience later on.

But for now, as always, hope you have enjoyed, and stick around for the next one 👋

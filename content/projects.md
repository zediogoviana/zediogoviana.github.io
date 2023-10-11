+++
title = "Side Projects"
description="In this page you can check some of my Personal projects, together with others I helped to develop, when I was at finiam."
keywords=["projects", "zediogoviana", "finiam", "remote", "elixir", "thepipetracker", "hackathons"]
+++

I've always been very pro-active and wanting to try out new things. So, in this page you can check some of my **Personal** projects, together with others I helped to develop, when I was at **[finiam](https://finiam.com)**.

Most of these are available on my **[GitHub page](https://github.com/zediogoviana)**, but I'll describe them shortly here also.

## Personal Projects

- **[Albwer](https://www.albwer.com)** \
If something happened to you now, and you couldn’t communicate your actions for a long time, what would you need to say to your family, friends or co-workers? Albwer allows you to configure messages to be delivered when you are absent for a certain amount of time. In its essence, it works as a Dead Man's Switch, but we believe it can be much more than that. Check [this post for more information](/posts/launching-my-side-project-albwer)\
Fully built using Elixir + Phoenix.

&nbsp;
- **[ThePipeTracker](https://www.tipoprado.thepipetracker.com)** \
A Production line tracking for SMEs. It's a Web Application for clients of an SME to track orders/packages inside a production pipeline. Together with production analysis plots and a dedicated module for delivery date prediction using Discrete Events Simulation theory (I wrote a blog post about this [here](https://zediogoviana.github.io/posts/simulations-with-elixir-and-the-actor-model/)). \
API built with Elixir and Frontend built with React. Running in production since 2019 and still helping the company with production efficiency.

&nbsp;
- **[Ethcule Poirot](https://github.com/zediogoviana/ethcule-poirot)** \
Elixir app to explore Ethereum transactions using a Neo4J database to store addresses and respective relationships, in a Graph structure. Its goal is to work as a tooling service, by plug-and-playing different APIs with it. \
I was invited to talk about this project in a **Neo4j Live** (a live stream hosted by Neo4j in their socials). You can check the video in the [Media Page](/media).

&nbsp;
- **[Keyboard Heatmap](https://github.com/zediogoviana/keyboard-heatmap)**\
It's an Elixir script to obtain an heatmap of your keystrokes. Keystrokes can be collected for the amount of time you choose. The idea is to generate an image of any keyboard layout, and for that, it's possible to add the configuration for new keyboard types and models, and then just choose the one we want to see "painted". It uses [zamith/mogrify_draw](https://github.com/zamith/mogrify_draw) to draw each key.


## Finiam's Open Source Projects

- **[Secrets](https://github.com/finiam/secrets.finiam.com)**\
A simple web app that transmits E2E encrypted messages safely. The way it works is by encrypting the user info locally and then generating a URL with a private key embbeded on it, through the hash in the URL, which is never sent to servers by browsers or any HTTP client. When you generate a secret, the webapp posts the encrypted information to the API, which in turn stores that encrypted information and assigns it an `ID` that we like to call `roomId`. The API is built with Elixir's Phoenix. 

&nbsp;
- **[Detris](https://github.com/finiam/ethamsterdam-detris)**\
An hackathon project we did for [ETHAmsterdam](https://amsterdam.ethglobal.com/) in April 2022. Detris, is a playable NFT that serves as an interface to play Tetris. What does that mean? It means that the NFT can run an instance of a game of Tetris. The asset the Detris NFT is retrieving is actually our implementation of the game of Tetris. We use an `iframe` to display it wherever we want. And since OpenSea supports iframe too, we can actually [play Tetris on OpenSea](https://opensea.io/assets/ethereum/0xbdc105c068715d57860702da9fa0c5ead11fba51/2). With this project we were one of the 13 finalists and winners of the hackathon. We ended up winning a monetary prize per team member, and got the chance to present it on stage, in front of hundreds of others participants! It was a really cool experience. (Check [this blogpost by Davide](https://blog.finiam.com/blog/finiam-goes-to-amsterdam) to know more in detail.)

&nbsp;
- **[Dora, the TipsetExplorer](https://github.com/finiam/dora-the-tipset-explorer)**\
An hackathon submitted for [FVM Space Warp](https://ethglobal.com/events/spacewarp) in February 2023. This project is a [The Graph](https://thegraph.com/en/)-like event indexer, for the FEVM, where you specify Handlers (files written/generated in Elixir that instruct Dora on how to deal with events) by Smart Contract or default Handlers for a specific Event. Indexing of blockchain events is crucial for dApps as it enables quick and efficient access to relevant data stored on the blockchain, which is required for executing Smart Contracts and providing a seamless user experience.\
At the moment of submission on the Hackathon, there was nothing similar to this Project working on FEVM, and for that reason this project was a big success, winning the `Filecoin & IPFS — 🥇 FVM Spaceships` prize of $10,000.\
Be sure to also check the [public submission page](https://ethglobal.com/showcase/dora-the-tipsetexplorer-uwg3o).

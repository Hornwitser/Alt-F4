---
author: Hornwitser
editor: Names of who edited this post [optional]
image:
  url: Path to thumbnail for this ALT-F4 post (URI)
  transcription: Small transcription of the image thumbnail
title: "Alt-F4 #X - The Road to Clusterio 2.0"
lang: en
discuss:
  forum: Link to forum thread [optional]
  reddit: Link to reddit thread [optional]
  discord: Link to discord chat [optional]
---

Hello, I'm Hornwitser and I want to tell the story of how I ended up spending a year developing Clusterio 2.0 which still has a long way to go before a release.
If you haven't heard of Clusterio before it's a piece of server software that enables mods to interact across servers.
It is perhaps best known from the Clusterio 60k event in 2018 where teleport chests were used to transfer items between some 46 Factorio servers in order to build a factory that could do 60k science per minute.
These teleportation chests work like active provider and requester chests, one removes items from the game and put them in shared cloud storage and the other takes items requested from that cloud storage and put them in the game.

[Insert clusterio v1 video here]
[Caption: Iron ore being mined on the server on the left, then sent to the server on the right via Clusterio teleportation chests.]

In July 2019 The Gridlock Cluster event was held.
Instead of teleportation chests for transporting items between servers, there were trains that could teleport from the edge of one server to the edge of another server using teleporting train stops.
This was also implemented with the help of Clusterio, but it wasn't added as a feature to the Clusterio mod, instead, it was a separate mod that made use of the same Clusterio server infrastructure as the teleportation chests.

[Insert train video here]
[Caption: Train teleporting from the edge of one server to the edge of another server.
As cool as these train teleports are, just the thought of having to deal with the buggy train teleportation code gives me shivers.]

Clusterio has always been composed of two parts: the gameplay interactions implemented in mod code that runs inside the game, and the server-side infrastructure which deals with moving data between the game servers.
In the beginning, the server-side was coded around handling the teleportation chests only, but as development has progressed the idea of what Clusterio _is_ has changed from teleporting chests to a pluggable server-side platform for making such cross-server gameplay elements.
And indeed, the server-side code for handling the train teleports is implemented as a plugin to Clusterio.

But the Clusterio devs' vision of a reliable and stable clustered Factorio server manager with an easy to install and use interface, that can enable the easy development of cross-server mods that you can install at a push of a button is one that has been far from the reality with Clusterio.
And that is where I enter the picture.


Humble Beginnings
-----------------

I started out hacking on Clusterio back in July 2019 as part of trying to help the Gridlock team with fixing issues they had.
One of the first things I noticed installing Clusterio to get a test cluster on my machine was the one thousand packages it pulled in as dependencies from npm.
Node.js was new to me then, but it didn't take an experienced developer to see the sad state of affair the Clusterio code base was in, and naive as I was I took it upon me to "fix" it.
After all, how complicated could it be?

My humble beginnings were a janitor kind of work like removing the need for installing some 700 needles dependencies (244 MB) when running Clusterio, most of it being libraries not used or libraries where one easily re-implementable function was used.
There was even a case where the reimplementation of the library function used was less code than using the library in the first place.
The cleanup of dead code and refactoring of existing code that I did gradually built up in scope and ambition, and lead up to the first major feature I implemented for 2.0: Save patching.


Save patching
-------------

Before I talk about save patching I want to mention what it was set out to replace.
The Gridlock cluster used scenario code to implement the train teleportation and in-game server switcher, this is often called soft-modding as it means you don't need to download any mods and restart Factorio to connect to the servers.
The code was constantly being improved as the event went on so it would not have been sufficient to put the code into a scenario and call it a day.
Updating a scenario on a server mid-game without the use of mods is kind of tricky.

So the code was loaded into the game via a scenario called Hotpatch (aka Server-side Multi-mod Scenario).
Conceptually Hotpatch is a cool thing, it lets you load in a mod like code while the game is running and it'll execute that code in an environment that emulates the Factorio mod environment.
But there were major issues with Hotpatch: it's poorly documented making it hard to use correctly, the implementation was incomplete and buggy, and most troublesome was that all the code had to be sent as a command on startup.
Factorio is very slow with long commands which leads to the game running for quite a while on the old code while the new code is being sent over.

In search of a solution for the problems with Hotpatch I looked at how the game stores scenario code in a save.
It's remarkably simple: a saved is a regular zip file and the Lua files of the scenario are stored in it.
Replace those Lua files in the save and the code for the scenario has been updated.  

With some more thought to the issue, I came up with a simple module system based on the event\_handler Lua library that Factorio comes with.
Clusterio plugins can define their own modules with Lua code and before starting a Factorio server Clusterio will write those modules into the save and add code to make sure they are executed when the save is loaded.


----------------------------------

I hope you liked this little glimpse into the Clusterio 2.0 development, I have many more things that I would like to talk about in the coming weeks.
Please note that 2.0 is not yet ready for public use, though if you're interested in the development, check out our [Discord server](https://discord.gg/5XuDkje) and [GitHub repo](https://github.com/clusterio/factorioClusterio).



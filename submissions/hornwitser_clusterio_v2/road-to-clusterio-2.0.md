The Road to Clusterio 2.0: Humble Beginnings
--------------------------------------------

Hello, I'm Hornwitser, the current lead developer of Clusterio 2.0 and responsible for most of the code written for the 2.0 branch.
I thought I'd share some of the experiences I've had writing code for that elusive 2.0 release for over a year now.

I started out hacking on Clusterio back in July 2019 as part of trying to help the Gridlock team with fixing issues they had.
One of the first things I noticed installing Clusterio to get a test setup on my machine was the one thousand packages it pulled in as dependencies from npm.
Node.js was new to me then, but it didn't taken an experienced developer to see the sad state of affair the Clusterio code base was in, and naive as I was I took it upon me to "fix" it.
After all, how complicated could it be?

My humble beginnings was janitor kind of work like removing the need for installing some 700 needles dependencies (244 MB) when running Clusterio, most of it being libraries not used or libraries where one easily reimplementable function was used.
There was even a case where the reimplementatiation of the library function used was less code than using the library in the first place.
The cleanup of dead code and refactoring of existing code that I did gradually built up in scope and ambition, and lead up to the first major feature I implemented for 2.0: Save patching.


### Save patching in Clusterio 2.0

Before I talk about save patching I want to mention what it was set out to replace.
The Gridlock cluster used scenario code to implement the train teleportation and in-game server switcher, this is often called soft-modding as it means you don't need to download any mods and restart Factorio to connect to the servers.
The code was constantly being improved as the event went on so it would not have been sufficent to put the code into a scenario and call it a day.
Updating a scenario on a server mid game without the use of mods is kind of tricky.

So the code was loaded into the game via scenario called Hotpatch (aka Server-side Multi-mod Scenario).
Conceptually Hotpatch is a cool thing, it lets you load in mod like code while the game is running and it'll execute that code in an environment that emulates the Factorio mod environment.
But there was major issues with Hotpatch: it's poorly documented making it hard to use correctly, the implementation was incomplete and buggy, and most troublesome was that all the code had to be sent as a command on startup.
Factorio is very slow with long commands which leads to the game running for quite a while on the old code while the new code is being sent over.

In search of a solution for the problems with Hotpatch I looked at how the game stores scenario code in a save.
It's remarkably simple: a saved is a regual zip file and the lua files of the scenario are stored in it.
Replace those lua files in the save and the code for the scenario has been updated.  

With some more thought to the issue I came up with a simple module system based on the event\_handler interface that Factorio comes with.
Plugins can define their own modules with lua code and before starting a Factorio server Clusterio will write those modules into the save and add code to make sure they are executed when the save is loaded.

----------------------------------

I hope you liked this little glimpse into the Clusterio 2.0 development, I have many more things that I would like to talk about in the coming weeks.
Please note that 2.0 is not yet ready for public use, though you're interested in the development check out our [Discord server](https://discord.gg/5XuDkje) and [GitHub repo](https://github.com/clusterio/factorioClusterio).


Minecrift Vive
==============

This is a modified version of the Minecrift VR mod that focuses on the Vive and room scale VR. It adds a teleporting method of locomotion and tracked controller support to interact with the world.

This mod uses phr00t's JOpenVR wrapper from his JMonkeyVR project. Be sure to check that out! Thanks also go to StellaArtois, Mabrowning and everyone else who worked on the Minecrift mod.

Also take a look at [jrbudda's fork](https://github.com/jrbudda/minecrift/releases) of this project, which adds a number of improvements and is being worked on a lot at the moment! You can also find a pre-built installer there.

Controls
========

- Right controller:
  - Trigger - Attack (equivalent of left mouse button)
  - Press touchpad - Use (equivalent of right mouse button)
  - Grip - If you have torches on your hotbar, this quickly places a torch

- Left controller:
  - Trigger - teleport
  - Swipe touchpad - switch between hotbar items
  - Press touchpad - toggle inventory
  - Menu button - game menu (equivalent of escape key)
  - Grip - Switches to the 1st hotbar slot

You can also swing your pickaxe at blocks or swing your sword at enemies to hit them.


Multiplayer
===========

Multiplayer will work if all clients and the server are running this mod. If you connect to a vanilla Minecraft server, it will fall back to a traditional movement scheme with continuous camera movement, which typically causes some amount of nausea for VR users. This is because vanilla servers don't allow clients to teleport as a form of cheat protection. You can also force the traditional movement scheme in singleplayer by pressing Right CTRL+R.


Performance
===========

If you're seeing lots of judder, try lowering your Minecraft video settings. Setting graphics from Fancy to Fast or reducing the view distance should help.


Building the Installer
======================

* Install Java JDK 1.6, 1.7 or 1.8. The Java JRE will NOT work, it MUST be the JDK. This has been tested with JDK1.6.0_38 x64. [Download from oracle.com](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase6-419409.html#jdk-6u38-oth-JPR)
* Set the JAVA_HOME environment variable to the JDK directory
* Add %JAVA_HOME%\bin to your PATH environment variable
* Install Python 2.7.x (NOT 3.x). Be sure to tick the 'add python to your PATH' option during install. [Download from python.org](https://www.python.org/downloads/)
* Download this repo with the "Download ZIP" button in the top right and extract the files somewhere (or clone the repo if you have Git)
* Open a command prompt and navigate to the extracted files
* Run install.bat
* Run build.bat

You should then have a Vive installer .exe you can run to install the mod. 


Troubleshooting
===============

* It is normal to see errors in the middle of the install.bat stage.
* Make sure you don't have Scala on your path.
* Make sure you don't have a HTTP_PROXY/HTTPS_PROXY environment variable set.
* See below for a more detailed description of the build process.


Technical Implementation Notes
==============================

For programmers/contributors:

Since this branch focuses on room scale, it makes a number of invasive changes to Minecrift that make it unsuitable for merging back into the main branch for now. These changes were made in a quick and dirty fashion to easily experiment and get something playable. They are all commented with "// VIVE". They should serve to highlight the problematic areas of vanilla Minecraft for a control scheme like this. Lots of small changes were made to avoid nausea and support tracked controllers independent of the player. The Matrix/Vector/Quaternion operations are kind of a mess, since there are multiple implementations of these classes available with slightly different features (Vec3 in particular is heavily used in Minecraft 1.7.10 but has a pretty poor feature set) and quite a lot of Minecraft's code uses coordinates relative to something that isn't helpful for independent objects.

Some major new concepts are:

1) Room origin - these are the coordinates in the world corresponding to the centre of your room. These coordinates and the player's coordinates are updated whenever you teleport around.

2) Player movement - the player entity's X/Z coords are set continuously as the VR headset moves around the room. This keeps the player entity at your current real world position (offset from the room origin).

3) Aim source - this is the 3D position in the world of each controller.



Below is the original Minecrift README:


Minecrift Mod for Minecraft
===========================

Current Versions
================

The latest maintained code can be found in the branches:

Minecrift 1.7 (with Forge support): 'port_to_1_7_10'
Minecrift 1.8:                      'port_to_1_8_1'

Getting the source
==================

A typical set of commands to retrieve the source correctly (including all required
submodules) is shown below (you'll require a newish version of git installed and setup for
commandline operation) e.g. for branch 1.7.10:

For OSX / Linux:

> git clone -b port_to_1_7_10 https://github.com/mabrowning/minecrift.git ~/minecrift-public-1710
> cd ~/minecrift-public-1710
> git submodule update --init --recursive

For Windows:

>git clone -b port_to_1_7_10 https://github.com/mabrowning/minecrift.git c:\minecrift-public-1710
>cd /D c:\minecrift-public-1710
>git submodule update --init --recursive

Setting up
==========

Install build prerequisites
---------------------------

- Java JDK 1.6, 1.7 or 1.8 (the Java JRE will NOT work, it MUST be the JDK)
- JAVA_HOME should be set to the JDK directory
- ${JAVA_HOME}\bin or %JAVA_HOME%/bin must be added to your path
- Python 2.7.x (NOT 3.x)
- Scala is NOT required (and currently for Windows should NOT be present on your path to avoid
  build issues)
- On Linux, install astyle or the patching process will fail.

Installing
----------

The build process has been tested on Windows 8.1, OSX 10.10, and Ubuntu 14.10. It utilises the
MCP (Minecraft Coders Pack). To install and build a clean checkout of this repo,
you should run the following from the root of the repo directory:

For OSX / Linux:

> ./install.sh
> ./build.sh

For Windows:

> install.bat
> build.bat

NOTE: Build errors will be seen in the console during the install process (the initial MCP rebuild will
fail). This is normal - the code is later patched to compile correctly.

These scripts will generate deobfuscated Minecrift source in mcpxxx/src/minecraft (with the 'unaltered'
source in mcpxxx/src/minecraft_orig). Required libs and natives will be in lib/<mcversion>. A versioned
installer will also be created.

Setting up a build / debug environment
--------------------------------------

This is currently a manual process (if anyone has maven / gradle experience and is willing to help create an
automated project setup process let us know). NOTE: Assumes the project working & current directory
is the root of this repo.

Add the following to your Eclipse / Idea / whatever project:

Non-Forge
+++++++++

Java Source (in order):

- Add ./JRift/JRift/src
- Add ./JMumbleLib/JMumble/src
- Add ./Sixense-Java/SixenseJava/src
- Add ./mcpxxx/src/minecraft

Libraries:

- Add all libraries in ./lib/<minecraft_version>

Run Configuration:

Main class: Start
JVM args:
Linux:
-Djava.library.path=./JRift/JRiftLibrary/natives/linux;./Sixense-Java/SixenseJavaLibrary/natives/linux;./JMumbleLink/JMumbleLibrary/natives/linux;./lib/<minecraft_version>/natives/linux
OSX:
-Djava.library.path=./JRift/JRiftLibrary/natives/osx:./Sixense-Java/SixenseJavaLibrary/natives/osx:./JMumbleLink/JMumbleLibrary/natives/osx:./lib/<minecraft_version>/natives/osx
Windows:
-Djava.library.path=.\JRift\JRiftLibrary\natives\windows;.\Sixense-Java\SixenseJavaLibrary\natives\windows;.\JMumbleLink\JMumbleLibrary\natives\windows;.\lib\<minecraft_version>\natives\windows

Program args (these are optional; but allow you to test on Minecraft multiplayer servers):
--username <minecraft_account_mailaddress_or_username> --password <minecraft_account_password>

TBC: How to setup the minecraft assets for the debugger.

Forge
+++++

This is somewhat more complicated! TBD.

Testing changes, and generating patches
---------------------------------------

- Surround any code changes with /** MINECRIFT **/ and /** END MINECRIFT **/
- Keep changes to the original source to a minimum, add new functions /classes ideally so that minimal changes
occur to the existing source. Do not refactor existing source, this will make future ports to new Minecraft
versions very tricky. Larger changes to mtbs package classes are less problematic however.
- Test your changes in the debugger.
- Run build.sh (or build.bat) to create a release installer. Run the release installer against
a real Minecraft install to test the reobfuscated changes via the Minecraft launcher.
- Run getchanges.sh (or getchanges.bat) to create patches between your modified ./mcpxxx/src/minecraft files
and the original ./mcpxxx/src/minecraft_orig files. The patches are copied to ./patches and new files copied
into the ./src directory. Check-in your changes and create a pull request for them.

License
-------

See [The License File](LICENSE.md) for more information.

StellaArtois, mabrowning 2013, 2014, 2015


*********************************************
Detailed Information
*********************************************

The Build Process
=================

It consists of a number of stages (and associated build scripts):

- Install
This is used to install the Minecrift source from a clean checkout of this repo, to a deobfuscated
source environment.
MCP is extracted, and patched where necessary. Optifine is merged into the Minecraft jar, and then
the MCP decompile / build process is run. This initial build will fail due to Optifine induced
build errors. We patch those errors (the first stage patch), then rebuild and generate the
client MD5s that MCP will use to determine which files are modified. Clean Minecraft + Optifine source
(with build erros corrected) will be present in mcpxxx/src/minecraft_orig.
Finally we apply the actual Minecrift patches (the second stage patch). Minecrift deobfuscated source
will now be present in mcpxxx/src/minecraft.

- Build
This builds the obfuscated Minecrift jar, and builds the versioned installer.
The scripts update the Minecrift version numbers in the source, as read from minecriftversion.py. Then
MCP recompiles the Minecrift source (checking for build errors), reobfuscates any changed files
(as compared to the source in mcpxxx/src/minecraft_orig) and then these files are added to a minecrift.jar.
The installer is build, versioned and minecrift.jar embedded within.

More to come...

# DCS Standalone on Linux Via Wine(Lutris)/Proton(Steam)
#### Any and all refrences to Steam, are not relating to the Steam version of the game.

DCS World can run on linux through Wine and Proton, though it does take some
work to get running and if you want to use VR I highly recommend using Proton(Steam) as opposed to Wine, if anything dosen't work, make sure I cannot stress this enough
CHECK all your log files.

Thanks to everyone who has helped getting the game running and debugging issues
in the [Proton issuetracker](https://github.com/ValveSoftware/Proton/issues/1722)
and in the [Matrix chat](https://matrix.to/#/#dcs-on-linux:matrix.org).  
Unfortunately, workarounds easily get buried there and the OG of this Doc is outdated  
(I will try to keep this one up to date but I make no promises)  

## Contents

   * [Installation](#Getting-it-installed-via-Lutris)
      * [Lutris](#getting-it-working-with-Lutris)
      * [Black screen Launcher bypass](#Black-screen-launcher-bypass)
      * [Voice-Chat-Bug](#Voice-Chat-Bug)
      * [Porting to Steam](#Porting-to-Steam)
   * [Bugs and Fixes](#known-issues-and-fixes)
      * [Broken Contrails](#Contrails-are-puffy/broken-up)
      * [fx_5_0 error](#fx_5_0-error-shaders-not-compiling)
   * [Vr References](#Vr-References)
   * [3rd party programs/tools](3rd-party-programs/tools)
      * [Opentrack](Opentrack)
   * [Installing Mods](Installing-Mods)
## Getting it installed via Lutris

There are [two install scripts
for standalone](https://lutris.net/games/dcs-world/) on Lutris
![Lutris Install Scripts](images/DCS.openbeta.png)
I used the latter labeled Standalone Open Beta version, but they both should work
as DCS no longer has an "OpenBeta" and I believe that the install scripts may be borked.
this will download, the DCS updater and you should install the game

### Getting it working with Lutris

Standalone install needs some Winetricks applied.    
Start the game once first to create the prefix([here](https://wiki.archlinux.org/title/Wine#WinePREFIX) for an explination of what a prefix is), then use lutris's Winetricks
to add these .dll and font.
```
vcrun2019 corefonts xact d3dcompiler_43
```
you can open Winetricks in lutris by clicking on DCS Do not open, 1 LMB click only, then click on the Wine glass at the bottom of the screen  
![Winetricks](images/Winetricks.png)

You need to add a "dll override" aswell. As of 2.9.12, `wbemprox=n` needs to be overridden.
In lutris, you can do so under "runner options".
![wbemprox](images/wbemprox.png)

#### (updated for 2.9.12.5336)

If you plan on using DCS with a VR headset you'll want to use Proton and the easiest way i've found
to get it working, is through Steam(not the Steam version of the game but adding
the game to Steam) however keep working with Wine and Lutris for now

If you would rather use headtracking via opentrack  
I'd recommend you keep using Wine as opentrack does not support the steam method

before you move on, you should have an install via lutris that opens the DCS_Updater.exe
make sure you have used it to install the game files at this point and duplicate the confg
in lutris, then change the duplicated lutris config to launch the DCS.exe MT or not, dosen't matter
we will be working with the duplicated one, not the original

## Black screen launcher bypass

So at this point you should get a black screen a little bit smaller than your display that'll be the launcher
you'll need to add this the the launch arguments of Lutris `--no-launcher`
you can do so under "game options" in the arguments field


With that change, you should be able to log in but once the game starts you
will see a black screen. you have two options from here, proceede with Wine or use Proton under Steam(Recommended for VR)

## Voice Chat Bug
### NOTE: the following dosen't seem to be an issue if you use Proton(Via Steam or lutris)
#### If you proceede using Wine you will have to redo this step every time you repair the game and possibly when ED updates the optionDB.lua
see [Porting to Steam](#Porting-to-Steam) for using Proton(Via Steam), just skip this step

I have a workaround for this problem however it removes the in game voice chat functionality
witch I find to be a downside but `¯\_(ツ)_/¯`

you'll need to modify the optionsDB.lua(modified ver included at the top of page) located at   
```/INSTALL_DIR/MissionEditor/modules/Options/optionsDb.lua```  
and remove the calls to voicechat on lines 118-129 and 453 look for the lines highlighted in the following 2 pictures,
![examplecode](images/118-129.png)
![examplecode](images/453.png)

The game should now start.


## Porting-to-Steam

so first thing you're gonna want to do is add the DCS.exe as a Steam game

![Porting to Steam](images/DCStoSteam.png)

then add these launch options(some debug info, and the no launcher option from before)  
```WineDLLOVERRIDES="wbemprox=n" WineDEBUG="+timestamp,+pid,+tid,+seh,+debugstr,+module" %command% --no-launcher```  
optionally you can add the gamemoderun command, however that requres you have gamemode installed  
```WineDLLOVERRIDES="wbemprox=n" WineDEBUG="+timestamp,+pid,+tid,+seh,+debugstr,+module" gamemoderun %command% --no-launcher```  

also I found the most sucess with Proton experimental but try different ones out see what works

now that that's done, launch it to create the prefix in Steam
you need to add the userdata from the Wine prefix to the Steam prefix

Steam Proton prefixes are stored in the compatdata folder usually around here   
```/home/<USRNAME>/.local/share/Steam/Steamapps/compatdata/```   
now once you're there, youll see a lot of numbered folders, one of those is the
new DCS prefix, it's probably going to be one of the longer ones  
it might be easier to find yours if you sort by creation date it should be the first/last one.

now that you've found the Steam Proton prefix, you need to link(I used a symlink) the Saved Games folder   
```/pfx/drive_c/users/Steamuser/Saved Games/```   
to the one in your lutris install for instance the command I used in arch was:   
```<USERNAME>@PC ~> ln -s /home/<USERNAME>/Games/dcs-world/drive_c/users/<USERNAME>/Saved\ Games/ /home/<USERNAME>/.local/share/Steam/Steamapps/compatdata/2824223594/pfx/drive_c/users/Steamuser/```  
### On Arch a filepath with spaces and either be added with ["/File Path/"] or [/File\ Path/] different systems may vary, check your respective wiki
(honesly linking everything from the Wine prefix will be neccisary but if your lazy like me just copy paste all the relevant folders from the Wine prefix to the Proton one).

## Known issues and fixes

If things go wrong, the primary thing to look for is the game log - 
`drive_c/users/$USERNAME/Saved Games/DCS<possibly openbeta>/Logs/dcs.log`.
After crashes, the crash reporter will spam a bit about various DLLs being used
recently, and just before that, the cause of the crash should be visible and if that
yelds no result try turning it off and back on again(your PC/Device) lol.

for troublshooting issues with steam try adding these environment variables
PROTON_LOG=1
PROTON_LOG_DIR=/<path-to-desired-directory>/
set that directory and steam will dump log files there

Sometimes crashes happen before the game gets far enough to create a log file.
Then your best bet is to read the Proton output. In both Lutris and Steam, you can easily get
this by starting them from a terminal.

If you can't find an issue, or have found a solution for one, please discuss it in
the .[matix](https://matrix.to/#/#dcs-on-linux:matrix.org) chat so I can update the guide

## Contrails are puffy/broken up
yeah it's like that, i dont know why but it can be ignored  
if you fix it let me know

## fx_5_0 error shaders not compiling
this is usually caused by a missing Wine/Proton trick, make sure you have all of the Wine/Proton tricks

# Vr References

As far as VR on linux is concerned your milage may vary but, if you havent at least attempted it before
this should get you started .[LVRA](https://discord.gg/qdUWFe4RDV)

# 3rd party programs/tools

##Opentrack
Opentrack is a 3rd party headtracking software that can use TrackIR hardware
https://github.com/markx86/opentrack-launcher?tab=readme-ov-file#with-Steam-flatpak

# Installing Mods
If you are using Lutris/Steam, just navigate to the Original prefix and install them as you would on windows

# DCS Standalone on Linux Via Wine(lutris)/Proton(steam) any and all refrence to steam are not related to the steam version of the game


DCS World can run on linux through Wine and Proton, though it does take some
work to get running and if you want to use VR I highly recommend using proton(steam) as opposed to wine, if anything dosen't work, make sure I cannot stress this enough
CHECK all your log files.

Thanks to everyone who has helped getting the game running and debugging issues
in the [proton issuetracker](https://github.com/ValveSoftware/Proton/issues/1722)
and in the Matrix chat: https://matrix.to/#/#dcs-on-linux:matrix.org. Unfortunately,
workarounds easily get buried there and the OG of this Doc is outdated
(I will try to keep this one up to date but I make no promises),
so I decided to edit with known, up-to-date methods for getting things to work.
note: This doc will only cover getting the game running via the process I used

## Contents#Black screen Launcher bypass

   * [Before we start](#Before-we-start)
   * [Installation](#Getting-it-installed-via-Lutris)
      * [Lutris](#getting-it-working-with-Lutris)
      * [Black screen Launcher bypass](#Black-screen-launcher-bypass)
      * [Voice-Chat-Bug](#Voice-Chat-Bug)
      * [Porting to Steam](#Porting-to-Steam)
   * [Bugs and Fixes](#known-issues-and-fixes)
      * [Broken Contrails](#Contrails-are-puffy/broken-up)
   * [Vr References](#Vr-References)

# Before we start

programs you will need:  
`Steam`  
`Lutris`  
`Wine`  

## Getting it installed via Lutris

There are [two install scripts
for standalone](https://lutris.net/games/dcs-world/) on Lutris
![Lutris Install Scripts](images/DCS.openbeta.png)
I used the latter labeled Standalone Open Beta version, but they both should work
as DCS no longer has an "OpenBeta" and I believe that the install scripts may be borked.
this will download, the DCS updater and you should install the game

### Getting it working with Lutris

Standalone install needs some winetricks applied.    
Start the game once first to create the prefix([here](https://wiki.archlinux.org/title/Wine#WINEPREFIX) for an explination of what a prefix is), then use lutris's winetricks
to add these .dll and font.
```
vcrun2019 corefonts xact d3dcompiler_43
```
you can open winetricks in lutris by clicking on DCS Do not open, 1 LMB click only, then click on the wine glass at the bottom of the screen  
`image here`

You need to add a "dll override" aswell. As of 2.9.12, `wbemprox=n` needs to be overridden.
In lutris, you can do so under "runner options".
`image here`

#### (updated for 2.9.12.5336)

If you plan on using DCS with a VR headset you'll want to use proton and the easiest way i've found
to get it working, is through Steam(not the steam version of the game but adding
the game to Steam) however keep working with Wine and Lutris for now

before you move on, you should have an install via lutris that opens the DCS_Updater.exe
make sure you have used it to install the game files at this point and duplicate the confg
in lutris, then change the duplicated lutris config to launch the DCS.exe MT or not, dosen't matter
`image here`

## Black screen launcher bypass

So at this point you should get a black screen a little bit smaller than your display
```image here```
that'll be the launcher
you'll need a options.lua to bypass the launcher, you may not have a savedgames dir yet so install the
options.lua(included on this page at the top(it's my personal one so your settings will need to be changed)) in   
```/drive_c/users/<USRNAME>/Saved Games/DCS<.openbeta?>/Config/options.lua```


With that change, you should be able to log in but once the game starts you
will see a black screen. you have two options from here, proceede with Wine or use Proton under steam(Recommended for VR)

## Voice Chat Bug
### NOTE: the following dosen't seem to be an issue if you use proton(Via Steam)  
#### If you proceede using wine you will have to redo this step every time you repair the game and possibly when ED updates the optionDB.lua
see [Porting to Steam](#Porting-to-Steam) for using proton(Via Steam), just skip this step

I have a workaround for this problem however it removes the in game voice chat functionality
witch I find to be a downside but `¯\_(ツ)_/¯`

you'll need to modify the optionsDB.lua(modified ver included at the top of page) located at   
```/INSTALL_DIR/MissionEditor/modules/Options/optionsDb.lua```  
and remove the calls to voicechat on lines 118-129 and 453 look for the lines highlighted in the following 2 pictures,
![examplecode](images/118-129.png)
![examplecode](images/453.png)

The game should now start.


## Porting-to-Steam

so first thing you're gonna want to do is add the DCS.exe as a steam game

![Porting to Steam](images/DCStoSteam.png)

then add these launch options(some debug info and the .dll override from earlier)  
```WINEDLLOVERRIDES="wbemprox=n" WINEDEBUG="+timestamp,+pid,+tid,+seh,+debugstr,+module" %command%```

also I found the most sucess with proton experimental but try different ones out see what works

now that that's done, launch it to create the prefix in steam
you'll notice that it still has the launcher issue, thats because you need
to add the userdata from the wine prefix to the steam prefix

steam proton prefixes are stored in the compatdata folder usually around here   
```/home/<USRNAME>/.local/share/Steam/steamapps/compatdata/```   
now once you're there, youll see alot of numbered folders, one of those is the
new DCS prefix, it's probably going to be one of the longer ones mine is /compatdata/2946498850/pfx

now that you've found the Steam proton prefix, you need to link(I used a symlink) the Saved Games folder   
```/pfx/drive_c/users/steamuser/Saved Games/```   
to the one in your lutris install for instance the command I used in arch was:   
```budderpard@PC ~> ln -s /home/budderpard/Games/dcs-world/drive_c/users/budderpard/Saved\ Games/ /home/budderpard/.local/share/Steam/steamapps/compatdata/2824223594/pfx/drive_c/users/steamuser/```  
### On Arch a filepath with spaces and either be added with ["/File Path/"] or [/File\ Path/] different systems may vary, check your respective wiki
(honesly linking everything from the wine prefix will be neccisary but if your lazy like me just copy paste all the relevant folders from the wine prefix to the proton one)

## Known issues and fixes

If things go wrong, the primary thing to look for is the game log - 
`drive_c/users/$USERNAME/Saved Games/DCS<possibly openbeta>/Logs/dcs.log`.
After crashes, the crash reporter will spam a bit about various DLLs being used
recently, and just before that, the cause of the crash should be visible and if that
yelds no result try turning it off and back on again(your PC/Device) lol.

Sometimes crashes happen before the game gets far enough to create a log file.
Then your best bet is to read the Proton output. In both Lutris and Steam, you can easily get
this by starting them from a terminal.

If you can't find an issue, or have found a solution for one, please discuss it in
the .[matix](https://matrix.to/#/#dcs-on-linux:matrix.org) chat so I can update the guide

## Contrails are puffy/broken up
yeah it's like that, i dont know why but it can be ignored
if you fix it let me know

# Vr References

As far as VR on linux is concerned your milage may vary but, if you havent at least attempted it before
this should get you started .[LVRA](https://discord.gg/qdUWFe4RDV)

# 3rd party programs/tools

##Opentrack
Opentrack is a 3rd party headtracking software that can use TrackIR hardware
https://github.com/markx86/opentrack-launcher?tab=readme-ov-file#with-steam-flatpak

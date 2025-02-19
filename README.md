# DCS Standalone on Linux Via Wine/Proton(steam)

DCS World can run on linux through Wine and Proton, though it does take some
work to get running, if anything dosen't work, make sure, I cannot stress this enough
CHECK all your log files.

Thanks to everyone who has helped getting the game running and debugging issues
in the [proton issuetracker](https://github.com/ValveSoftware/Proton/issues/1722)
and in the Matrix chat: https://matrix.to/#/#dcs-on-linux:matrix.org. Unfortunately,
workarounds easily get buried there and the OG of this Doc is outdated
(I will try to keep this one up to date but I make no promises),
so I decided to edit with known, up-to-date methods for getting things to work.
note: This doc will only cover getting the game running via the process I used

## Contents

   * [Installation](#Getting-it-installed-via-Lutris)
      * [Lutris](#getting-it-working)
      * [Porting to Steam](#Porting-to-Steam)
   * [Bugs and Fixes](#known-issues-and-fixes)
      * [Smoke](#white-smoke-and-some-other-particles-renders-weirdly)
      * [F16 RWR](#f16-rwr-shows-a-opaque-square-on-the-rwr-over-the-priority-contact)
      * [Server List](#missing-multiplayer-server-list)
      * [F10 Crash](#crash-on-f10)
      * [Disabled Modules](#module-disabled-by-user)
      * [Controls](#control-issues)
   * [Vr References](#Vr-References)

## Getting it installed via Lutris

An easy way to get started is to use Lutris. There are [two install scripts
for standalone](https://lutris.net/games/dcs-world/) on Lutris
![Lutris Install Scripts](images/DCS.openbeta.png)
I used the latter labeled Standalone Open Beta version, but they both should work
as DCS no longer has an "OpenBeta" and i believe that the install scripts are borked.

### Getting it working

Both versions need some winetricks applied.

Start the game once first to create the prefix, then use lutris's winetricks
to add these .dll and font.
```
<tricks command> vcrun2019 corefonts xact d3dcompiler_43
```

#### (updated for 2.9.12.5336)

For now, this guide assumes you use the standalone version, if you plan on using
DCS with a VR headset, you'll need to use proton and the easiest way i've found
to get it working is through Steam(not the steam version of the game but adding
the game to Steam)

First, some variables to avoid repetition:

- `$USERNAME`: refers to the wine user. On standalone, this is your normal
  username and on steam it is `steamuser`.
- `$INSTALL_DIR`: the location in program files where the game is installed.
  On standalone: `drive_c/Program Files/Eagle Dynamics/DCS` or `DCS World OpenBeta`.
- `$CONFIG_DIR`: the place where user config stuff is stored
  `drive_c/users/$USERNAME/Saved Games/DCS<possibly openbeta>`.
- `$LOG`: the game log file `$CONFIG_DIR/Logs/dcs.log`.

You need
to add a few "dll overrides" for the game to work. As of 2.9.12, `wbemprox` needs to be overridden.
In lutris, you can do so under "runner options".

For wine and steam proton, you can do so using the `WINEDLLOVERRIDES`
flag https://wiki.winehq.org/Wine_User's_Guide#WINEDLLOVERRIDES.3DDLL_Overrides

```
WINEDLLOVERRIDES='wbemprox=n'
```
before you move on, you should have an install via lutris that opens the DCS_Updater.exe
make sure you have used it to intall the game files at this point and to duplicate the confg
in lutris, then change the duplicated lutris config to launch the DCS.exe MT or not dosent matter

So at this point you should get a black screen a little bit smaller than your display, that'll be the launcher
you'll need a options.lua to bypass the launcher, you may not have a savedgames dir yet so install the
options.lua in ``` /drive_c/users/<USRNAME>/Saved Games/DCS<.openbeta?>/Config/options.lua```

## NOTE: the following dosen't seem to be an issue if you use proton
see the next section for using proton, just skip this step

With that change, you should be able to log in but once the game starts you
will see a black screen. To fix this, ~create a symlink from
`$INSTALL_DIR/bin/webrtc_plugin.dll` to `$INSTALL_dir/webrtc_plugin.dll`.~

$INSTALL_dir isn't defined anywhere soooo....
but I have a different workaround for this problem however it removes the in game voice chat functionality
witch I find to be a downside but 
```
¯\_(ツ)_/¯
```
you'll need to modify the optionsDB.lua located at 
```/INSTALL_DIR/MissionEditor/modules/Options/optionsDb.lua```
and remove the calls to voicechat on lines 118-129 and 437,

The game should now start.

You may also see a crash when loading a mission. This might be caused by a
Arial missing font which can not be distributed with Wine, if you can 
just grab a copy from a windows install

### Porting-to-Steam

So now you have a working install via lutris but it dosent detect your VR heaset 
well let's fix that,

the easiest way ive found to do so is to port your wine prefix to steam

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
new DCS prefix, it's probably going to be one of the longer ones mine is 
/compatdata/2946498850/pfx

now that you've found the Steam proton prefix, you need to link(I used a symlink) the Saved Games folder
```/pfx/drive_c/users/steamuser/Saved Games/``` 
to the one in your lutris install for instance the command I used in arch was:
```budderpard@PC ~> ln -s /home/budderpard/Games/dcs-world/drive_c/users/budderpard/Saved\ Games/ /home/budderpard/.local/share/Steam/steamapps/compatdata/2824223594/pfx/drive_c/users/steamuser/```
(honesly linking everything from the wine prefix would probably be a good idea but im just lazy)

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
the [proton issue](https://github.com/ValveSoftware/Proton/issues/1722).

### White smoke and some other particles renders weirdly

This is a long standing issue, most likely related to texture loading and tesselation. 
Luckily, it is just a visual artefact that can be (largely) ignored.

### F16 RWR shows a opaque square on the RWR over the priority contact

This issue occurs because some textures fail to load for an unknown reason. The
fix is simple: open the file
`${INSTALL_DIR}/Mods/aircraft/F-16C/Cockpit/IndicationResources/RWR/indication_RWR.tga`
with an image editor (GIMP or Krita have been used successfully), then just
re-export the file. The RWR should now render correctly.

### Missing multiplayer server list

For a few 2.5.6 versions, the server browser did not work, and neither did
directly connecting to servers using connect by IP. However, there is a
workaround.

Edit `$INSTALL_DIR/MissionEditor/modules/mul_password.lua`. Find the function `onChange_btnOk` and add the
line `onlyConnect = true` to the start of the function like so.

```lua
function onChange_btnOk()  
    onlyConnect = true -- This line was added
	if onlyConnect == true then
	-- ...
end
```

Now you should be able to use the connect by IP button to join servers, but the
server list is still broken. Luckily, a server list is available if you log in
on https://www.digitalcombatsimulator.com/, and from there you can get the IP
of servers.

### Crash on F10

For many DCS versions and/or Wine versions, if you press F10 (the default
binding to bring up the map) the game will crash ("permanently" on steam, see
fixing steam permanent crashing (above) for a fix).  Luckily, the problem is with the
F10 key itself, not the map, so rebind it to something else you see fit. The
same applies for the communication menu.

### Module disabled by user

You probably won't run into this, but if you do, there is a fix.

One of your modules is missing, it is not shown in the list at the bottom of
the main menu, and you can't use it. On standalone, check if it is enabled in
the module manager. On steam however, things are a bit more tricky. If you
copied your configs between standalone and steam, module manager disabled mods
will be disabled in steam too. This information is stored in
`$CONFIG_DIR/enabled.lua` or something similar. Remove it to fix the issues.

### Control issues

Due to the various differences between distributions, issues with (HOTAS) controls can be hard to nail down,
especially when Wine is involved - adding another layer of potentional problems. Users experiencing issues with
controllers are advised to read through [the information here](https://github.com/bradley-r/Linux-Controller-Fixes/).

## Vr References

As far as VR on linux is concerned your milage may vary but, if you havent at least attempted it before
this should get you started https://discord.gg/qdUWFe4RDV

# DCS Standalone on Linux Via Wine/Proton

DCS World can run on linux through Wine and Proton, though it does take some
work to get running, if anything dosen't work, make sure, I cannot stress this enough
CHECK all your log files.

Thanks to everyone who has helped getting the game running and debugging issues
in the [proton issue
tracker](https://github.com/ValveSoftware/Proton/issues/1722) and in the 
Matrix chat: https://matrix.to/#/#dcs-on-linux:matrix.org. Unfortunately,
workarounds easily get buried there and the OG of this Doc is outdated
(I will try to keep this one up to date but I make no promises),
so I decided to edit with known, up-to-date methods for getting things to work.
note: This doc will only cover getting the game running via the process I used

Outside the proton thread, additional credit goes to @akp for the initial revision
of the Opentrack instructions, and @bradley-r for the Linuxtrack, Scratchpad and V4L2 info.

To chat about DCS World on Linux there is a Matrix chat available:
* https://matrix.to/#/#dcs-on-linux:matrix.org

## Contents

   * [Installation](#installation)
      * [Lutris](#getting-it-working)
   * [Bugs and Fixes](#known-issues-and-fixes)
      * [Smoke](#white-smoke-and-some-other-particles-renders-weirdly)
      * [F16 RWR](#f16-rwr-shows-a-opaque-square-on-the-rwr-over-the-priority-contact)
      * [Server List](#missing-multiplayer-server-list)
      * [F10 Crash](#crash-on-f10)
      * [Disabled Modules](#module-disabled-by-user)
      * [Controls](#control-issues)

## Installation

### Getting it installed via Lutris

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

#### Open Beta (updated for 2.9.12.5336)

For now, this guide assumes you use the standalone version. The steam version
may also work, but I have not tested it in a while. Currently, Wine 6.0 rc1 or
the Lutris version of that release are what work best but other wine versions
may also work.

First, some variables to avoid repetition:

- `$USERNAME`: refers to the wine user. On standalone, this is your normal
  username and on steam it is `steamuser`.
- `$INSTALL_DIR`: the location in program files where the game is installed.
  On standalone: `drive_c/Program Files/Eagle Dynamics/DCS` or `DCS World OpenBeta`. On Steam, it's
  `/home/frans/.local/share/Steam/steamapps/common/DCSWorld`
- `$CONFIG_DIR`: the place where user config stuff is stored
  `drive_c/users/$USERNAME/Saved Games/DCS<possibly openbeta>`.
- `$LOG`: the game log file `$CONFIG_DIR/Logs/dcs.log`.

You need
to add a few "dll overrides" for the game to work. As of 2.7.9, both `wbemprox` and `msdmo` need to be overridden.
In lutris, you can do so under "runner options".

For wine and steam proton, you can do so using the `WINEDLLOVERRIDES`
flag https://wiki.winehq.org/Wine_User's_Guide#WINEDLLOVERRIDES.3DDLL_Overrides

```
WINEDLLOVERRIDES='wbemprox=n;msdmo=n'
```

With that change, you should be able to log in but once the game starts you
will see a black screen. To fix this, create a symlink from
`$INSTALL_DIR/bin/webrtc_plugin.dll` to `$INSTALL_dir/webrtc_plugin.dll`.
good job whoever wrote this cause it makes no sense, where is $INSTALL_dir, hmmmm?
anyway if any of you figure it out feel free to let me know, but I have a different
workaround for this problem however it removes the in game voice chat functionality
witch I find to be a downside however $INSTALL_dir isn't defined anywhere soooo
```
¯\_(ツ)_/¯
```

The game should now start.

You may also see a crash when loading a mission. This might be caused by a
Arial missing font which can not be distributed with Wine.

## SteamVR

The game seems to work fine with steamVR. This is only possible in the steam
version, and seems to currently only work in proton 6.3.8 (possibly in future
proton versions, but not GE or TKG)

## Known issues and fixes

If things go wrong, the primary thing to look for is the game log - 
`drive_c/users/$USERNAME/Saved Games/DCS<possibly openbeta>/Logs/dcs.log`.
After crashes, the crash reporter will spam a bit about various DLLs being used
recently, and just before that, the cause of the crash should be visible.

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

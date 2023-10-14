# The Homebrew Launcher

#### Presentation

The Homebrew Launcher (hbmenu for short) is the main menu used to list and launch homebrew applications. It is essentially a graphical shell around an existing homebrew loading mechanism. The following entrypoints are supported:

- [Luma3DS Rosalina](https://github.com/LumaTeam/Luma3DS) **(recommended)**: Works on all system versions from 4.0 onwards; it provides unrestricted access to 3DS system resources as well as nice extra features such as remote debugging (GDB). For more information read the [Rosalina documentation](https://github.com/LumaTeam/Luma3DS/wiki/Rosalina).
- [Legacy \*hax 2.x](https://smealum.github.io/3ds/): This is a now-obsolete homebrew loading system that only provides limited access to 3DS system resources, as it only attacks a low privilege level. Support for this entrypoint is deprecated and may be removed in a future release.

> [!NOTE]
> 3DS homebrew is built and distributed as executables with the `.3dsx` extension.

> [!IMPORTANT]
> Note that you may encounter files with the `.cia` extension - these are **not** homebrew executables that can be loaded using hbmenu.

#### Usage

To install hbmenu, simply copy `boot.3dsx` to the root of your SD card. If you are using a recent version of [Luma3DS](https://github.com/LumaTeam/Luma3DS) you probably already have a copy of hbmenu installed, as it comes bundled with it.

Use the D-Pad, Circle Pad or the touchscreen to select an application, and press A or touch it again to start it. Use the C-Stick alternatively on New 3DS to scroll the list of applications.

hbmenu supports starring applications, so that they are shown at the beginning of the list. The SELECT button stars/unstars the currently selected homebrew application.

On \*hax 2.x, it is not possible to go back to the 3DS HOME menu using the HOME button. As an alternative, you can press the START button where you can reboot your console or relaunch HOME menu.

hbmenu starts in the sdmc:/3ds/ directory for applications and it will look for 3dsx files inside it. You can navigate the directory tree and open/browse folders as you would expect. Additionally, folders containing a 3dsx file with the same name as the folder (or alternatively `boot.3dsx`) will be detected as an application bundle folder, and it will be presented as a single icon that can directly launch the application.

Here is an example directory structure that hbmenu will have no trouble recognizing:

- sdmc:/
  - 3ds/
    - games/
	    - Hermes.3dsx
      - cubemadness.3dsx
    - Checkpoint/ *(this folder will be detected as an application bundle)*
      - Checkpoint.3dsx
      - ...
    - ftpd.3dsx
    - mgba.3dsx
    - 3dscraft.3dsx
    - blargSNES.3dsx
    - gameyob.3dsx
    - 3dnes.3dsx

If hbmenu does not find an icon file (either embedded in the executable or provided separately) to associate with a given 3dsx, it will display a default icon and the path to the executable as a fallback.

hbmenu also allows you to create "shortcuts" which are xml files containing a path to a 3dsx file and optional arguments to pass to the .3dsx. This file can also include a path to icon data as well as name, description and author text using tags as follows:

    <shortcut>
        <executable>The path to the 3dsx file goes here.</executable>
        <icon>path to smdh icon data</icon>
        <arg>Place arguments to be passed to 3dsx here.</arg>
        <name>Name to display</name>
        <description>Description of homebrew app</description>
        <author>Name of the author</author>
    </shortcut>

Arguments are space or tab separated but can use single or double quotes to contain whitespace.

Name, description and author will be read from the .3dsx if it has embedded SMDH data or from the supplied icon path. The fields in the xml file will then override their respective entries.

You should not hotswap the SD card while hbmenu is running since it compromises the 3DS OS's stability amongst other things. It is recommended that you instead use a file transfer homebrew application such as ftpd to transfer files without rebooting.

#### Technical notes

hbmenu does all its rendering in hardware thanks to the [citro3d](https://github.com/fincs/citro3d) library. The 3DS system font is also used to render all text.

hbmenu uses some funky mechanisms to launch 3dsx files. If you're interested in launching 3dsx files from your own application, you should look here; although these mechanisms may change in the future.

#### Netloader

hbmenu contains support for the 3dslink protocol, which allows you to remotely load applications.
Press Y to activate as usual then run `3dslink <3dsxfile>` if your network can cope with UDP broadcast messages.
If 3dslink says 3DS not found then you can use `-a <ip address>` to tell it where to send the file.

All the other arguments you give 3dslink will be passed as arguments to the launched 3dsx file. You can also specify argv[0] with `-0 <argument>` which is useful for
setting the current working directory if you already have data files in a particular place, i.e. `3dslink myfile.3dsx -0 sdmc:/3ds/mydata/`

3dslink is provided with devkitARM or you can download binaries from [WinterMute's website](http://davejmurphy.com/3dslink/).

#### Usage

Binaries of hbmenu can be downloaded from the [Releases](https://github.com/fincs/new-hbmenu/releases) page.

#### Building from source

##### Newcomers

If this is your first time building a homebrew application for the Nintendo 3DS, please consult the following resources:
- [devkitPro pacman (devkitpro.org)](https://devkitpro.org/wiki/devkitPro_pacman): Introduction to devkitPro's modified version of the [Arch Linux package manager](https://wiki.archlinux.org/index.php/pacman), which is used to simplify the distribution of homebrew toolchains and dependencies. You *must* use it to compile this project.
- [Getting Started (devkitpro.org)](https://devkitpro.org/wiki/Getting_Started): Installation tutorial for devkitPro's toolchains.
- [portlibs (devkitpro.org)](https://devkitpro.org/wiki/portlibs) (Advanced): *portlibs* is used to distribute useful libraries for software ports.

You will also have to use the well-known [`make` utility](https://www.gnu.org/software/make/manual/make.html) in order to build this software. (We would like to encourage you to find out how to install it on your own.

##### Software Dependencies

hbmenu uses *zlib* for compression and *tinyxml2* for XML parsing. To install them, please execute the following command:

```shell
dkp-pacman -S 3ds-zlib 3ds-tinyxml2 3ds-libconfig
```

If you are using *Arch Linux* with a [modified install of pacman](https://devkitpro.org/wiki/devkitPro_pacman#Customising_Existing_Pacman_Install), remember to run `pacman` instead of `dkp-pacman`.

After installing those dependencies, you should be able to compile the launcher by running `make`.

##### Environment Variables

> [!NOTE]
> Problems with environment variables are most likely to happen on Linux-based or Unix-like operating systems.

> [!WARNING]
> If you are experiencing issues with environment variables on **macOS** after using the [devkitPro installer](https://devkitpro.org/wiki/devkitPro_pacman#macOS), remember to reboot your system!

You may get an error asking you to run either `export DEVKITPRO=<path to>devkitPro` or `export DEVKITARM=<path to>devkitARM`; this means that `make` does not know where to find the files it needs to compile `hbmenu`.

> Environment variables are named strings available to all applications. Variables are used to adapt each application's behavior to the environment [...]. You might define paths for files, language options, and so on.
>
> *— AnttiM and JeffRoush, [Debian Wiki](https://wiki.debian.org/EnvironmentVariables)*

Assuming that devkitPro's toolchains are present in the directory `/opt/devkitpro`, you should set the following variables:
- `DEVKITPRO=/opt/devkitpro`
- `DEVKITARM=/opt/devkitpro/devkitARM`

> [!NOTE]
> On Linux, macOS and BSD-like systems, there are two ways to set a variable until you exit the command line:
> - prepending the variables before running `make` (e.g. `DEVKITPRO=/opt/devkitpro DEVKITARM=/opt/devkitpro/devkitARM make`)
> - the `export` command before running `make` (e.g. `export DEVKITPRO=/opt/devkitpro`)
>
> On Windows, the equivalent of `export` is `set`. In order to set the environment variables permanently for your user account, please consult your operating system's documentation.

#### File Associations

This is a feature backported from [nx-hbmenu](https://switchbrew.org/wiki/Homebrew_Menu#File_Associations). However, there is one notable difference: icons must be a 48x48 t3x-generated file with GPU_RGB565 as its color format.

#### Contributing

hbmenu is looking for contributors! We're making this repository public so that you, the community, can make hbmenu into the menu of your dreams. Or show you how to make your own, better menu! Of course we'd rather you improved hbmenu rather than went off and started fragmenting the userbase, but any contributions to the homebrew scene are welcome. Feel free to use code from hbmenu for your own projects, so long as you give credit to its original authors.

#### Credits

- smea: code & original hbmenu version
- fincs: code & rewrite
- GEMISIS: code
- mtheall: code
- WinterMute: netloader code
- Fluto: graphics
- Arkhandar: graphics
- dotjasp: graphics (regionfree icon)
- gruetzkopf, TuxSH, AuroraWright, Soph1a7, SentientTurtle, Yami-chan, d3m3vilurr, daedreth, JixunMoe, yy-codes, MCPE-PC: translations

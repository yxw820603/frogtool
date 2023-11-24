自用，PDD上一款MD的游戏机内使用zmd，但是图片格式用的是BGRA，因此修改图片格式。
使用需要先准备SF2000的目录结构，在   https://pan.baidu.com/s/1t_mOfEhxQTRWDfgtkibaTQ?pwd=1234
之后把经过zip压缩的 md 或者 bin 格式的rom放到 MD目录下
然后去网上搜索游戏的图片一张，文件名要和zip文件一样。
然后运行：
```python3 frogtool.py /home/user/temp/roms MD```
只需要注意路径换成sf2000的目录
运行后，软件会删除zip和图片文件，生成zmd文件，要注意备份

FROGTOOL v0.2.1
===============

by taizou  
https://github.com/tzlion/frogtool

This tool allows you to rebuild the preset game lists on the SF2000 emulator handheld, so you can add (or remove) ROMs
in the proper system categories instead of only being able to add them in the user folder.

Also now supports thumbnails!


DISCLAIMER
----------

This program is experimental and you should use with caution!

It is not developed or authorised by any company or individual connected with the SF2000 handheld and is based on my own
reverse engineering of its file formats.

Although it will back up the files it modifies, you should make your own backup of the Resources folder and ideally your
whole SD card so you can restore the original state of your device if anything goes wrong.


LIMITATIONS
-----------

The following functionality from the stock menu will be lost by using this program:

1. Chinese translations of game names (including searching by pinyin initials).
   Game names will be taken from the filename regardless of language setting.

2. Any custom sorting of games in the menu (e.g. popular games placed at the top).
   All games will be sorted alphabetically instead.

I will look to support these features in the future, but for the time being, if you would like to retain these features,
please do not use this program.

Additionally, this program does not currently support changing the shortcuts in the main menu. If you remove or rename
the ROMs those shortcuts point to, they will remain in the menu but become non-functional.


Compatible versions
-------------------

This tool has been tested & confirmed to work on the following SF2000 firmware/SD card image versions:
* Launch version (with English & Chinese languages only)
* 2023-04-20 update (with 17 languages)
* 2023-05-12 update (adds keymapping, favourites, history) - This works but can mess up the favourites and history
  sections as they're based on position in the list rather than filename. For the time being, if you have problems,
  delete Resources/Favorites.bin and Resources/History.bin from the SD card. This won't stop the features from working
  but will remove any existing favourites or history you have.


Use on any later versions is at your own risk!


Download/installation
---------------------

Download the latest release from https://github.com/tzlion/frogtool/releases.

Get the version named -win.zip for the Windows executable or -py.zip for the Python script.

(Sorry the Windows executable is like 5MB, apparently that's just how compiled Python goes)


Basic usage steps
-----------------

1. Insert the SF2000 SD card in a card reader connected to your computer
2. Drag and drop your desired ROMs into the respective system folders (ARCADE, FC, GB, GBA, GBC, MD, SFC) 
3. Run this program as per the following section


Running the program
-------------------

This program can be run in multiple ways, depending on the version you have and your preferences.

### Windows executable (.exe)

Either
* Double click from Windows Explorer, it will ask you to enter the drive and system and then proceed
* Or run it on the command line as follows:  
  `frogtool [drive] [system] [-sc]`  
  Example: `frogtool H: FC`  
On other platforms you may run it through WINE although this has not been tested.

### Python script (.py)

You will need to have a Python interpreter installed, this was developed against version 3.10 & should at least work
with this and later versions.  
In order to use the thumbnail generation functionality you will additionally need to have Pillow installed.  
Then either
* If you have .py files associated with your interpreter, you can double click and run it
* Otherwise, run it on the command line as follows:  
  `python frogtool.py [drive] [system] [-sc]`  
  Example: `python frogtool.py H: FC`  
  (the "python" command depends on your setup, you may need to use "py" or "python3")

### Command line arguments

* `drive`:  the location of your SF2000 SD card (e.g. a drive letter on Windows or a mount point on Linux/Mac)
* `system`: the system to rebuild, one of `ARCADE`, `FC`, `GB`, `GBA`, `GBC`, `MD`, `SFC` or `ALL`
* `-sc`:    skip confirmations (otherwise, it will ask you to confirm before processing, and press enter once finished)

If you omit either drive or system, it will ask you to enter them when run.


Backups
-------

On first run, this program will automatically create backups of all game list files for the selected system(s).
These will be placed alongside the originals and have "_orig" appended to their name.

So, if something goes wrong or you just want to roll back your device to its original state, you can delete the files
created by this program, and rename the "_orig" ones back to their original names.

(See "List file reference" section to see which files are which)


Supported files
---------------

The SF2000 OS will load the following file extensions:

| Type        | Extensions                                                      |
|-------------|-----------------------------------------------------------------|
| Zipped      | bkp, zip                                                        |
| Thumbnailed | zfc, zsf, zmd, zgb, zfb (see "Thumbnails & .zxx files" section) |
| SFC/SNES    | smc, fig, sfc, gd3, gd7, dx2, bsx, swc                          |
| FC/NES      | nes, nfc, fds, unf                                              |
| GB/GBC      | gbc, gb, sgb                                                    |
| GBA         | gba, agb, gbz                                                   |
| MD/GEN/SMS  | bin, md, smd, gen, sms                                          |

It doesn't generally care if you put the wrong system's roms in the wrong folder, it will load them in the correct
emulator according to their file extension.

Master System games are "secretly" supported by the Mega Drive emulator, it recognises the .sms extension but will
actually run these games with any of the Mega Drive file extensions too. Game Gear games don't work properly.

Both .bkp and .zip extensions seem to function as normal ZIP files, I think .bkp was used for obfuscated zip files on 
another system. Any supported ROM inside a ZIP file will be treated the same as an uncompressed ROM, and loaded in the
appropriate emulator.  
(Arcade game ZIP files are weird, see the "Arcade games" section below.)

In the launch firmware version, filenames may contain Chinese and Japanese characters and these will be correctly
displayed in the list even when English is selected. In the 2023-04-20 update, supported characters depend on the
selected language, as it uses different fonts per language.


Arcade games
------------

Arcade ROMs work differently from any others, since they consist of multiple ROM dumps inside a ZIP file, and emulators
typically recognise them by their filename and the contained files' checksums. This means they usually can't be renamed.

For the SF2000 OS's purposes, in order to display the full names of the games in the menu instead of the shortened
emulator-standard filenames (eg. "Metal Slug X -Super Vehicle-001" instead of mslugx) their approach is to use .zfb
files named with the full name, these files contain a thumbnail image plus the actual filename, which refers in turn to
the actual rom zip file which is found in the ARCADE/bin subfolder.  
(See the "Thumbnails & .zxx files" section for more info on this format)

The OS will still recognise arcade ROM ZIP files placed directly in the ROM folders with their emulator-standard
filenames, but I don't know which ROM set it expects; even the manufacturers don't seem to know, they preloaded three
Sonic Wings ROMs in the user "ROMS" folder and only one of them loads! Internal strings suggest the emulator is some
version of Final Burn Alpha if that helps.

Many preloaded arcade games also have ".skp" files in the ARCADE/skp folder, these appear to be savestates which are
automatically loaded when booting a game, to skip its boot sequence and bring you straight to the title screen with a
coin already inserted. However it seems these only function correctly when the ROM is loaded using a .zfb file; if you
place an arcade ROM ZIP directly in the ARCADE folder and index it using this tool, and there is a corresponding .skp
file in the skp folder, the game will crash after loading the ROM. Personally I prefer to delete these files anyway as
I'd rather see the original attract mode instead of jumping straight to the title screen.


Thumbnails & .zxx files
-----------------------

The following file formats: zfc, zsf, zmd, zgb, zfb are a custom format created for this and similar devices, containing
both a thumbnail image used in the menu and a either a zipped ROM or a pointer to one. Collectively I will refer to them
as .zxx files for now.

They correspond to the following systems:
* .zfc = FC
* .zsf = SFC
* .zmd = MD
* .zgb = GB, GBC, GBA
* .zfb = ARCADE

This tool now supports generating these files, so you can use your own custom thumbnails! (Except arcade games for now.)

Just drop a zipped rom and an image (png, jpg, gif) with the same filename in the same folder, run frogtool and it will
automatically combine the two into an appropriate .zxx file. (The source image and zip file will be deleted.)  

Example: "Bubsy.zip" and "Bubsy.jpg" would be combined to "Bubsy.zsf" if placed in the "SFC" folder.

Thumbnails in this system are 144 x 208 pixels, your image will be resized to those dimensions if necessary.

### Technical details

All .zxx files except .zfb are laid out as follows:
* First 0xEA00 bytes: Thumbnail image, RGB565 RAW format, 144x208px
* Rest of the file: A zipped rom in "WQW" format

"WQW" is an obfuscated zip file format found on several emulation devices, with filenames scrambled and headers modified
to inhibit use by ordinary zip software. However, you can actually use a normal unmodified zip inside a .zxx file and
the OS will load it just fine. (That's what this tool does when generating these files.)

So, if you want to manually create a .zxx file you can do so by creating a thumbnail in the specified RAW format, and
then appending a zipped ROM to it.

.zfb files for arcade games are different: they contain the same kind of image, but don't contain the actual game ROM.
Instead the image is followed by four 00 bytes, then the actual filename of the ROM in the "bin" folder, then two
further 00 bytes.


List file reference
-------------------

The game list files are found in the Resources folder alongside graphical and audio assets and other resources used by
the OS. However, the files in this folder are all given meaningless or misleading names.
For the game lists this is as follows:

| System | Filenames | Chinese   | Pinyin    |
|--------|-----------|-----------|-----------|
| ARCADE | mswb7.tax | msdtc.nec | mfpmp.bvs |
| FC     | rdbui.tax | fhcfg.nec | nethn.bvs |
| GB     | vdsdc.tax | umboa.nec | qdvd6.bvs |
| GBA    | vfnet.tax | htuiw.nec | sppnp.bvs |
| GBC    | pnpui.tax | wjere.nec | mgdel.bvs |
| MD     | scksp.tax | setxa.nec | wmiui.bvs |
| SFC    | urefs.tax | adsnt.nec | xvb6c.bvs |

Explanation of the list types:
* Filenames = The names of each ROM file. These are also used for the English menus, with file extensions stripped off.
* Chinese = Chinese translations of each game name, UTF-8 encoded, used for the Chinese menus
* Pinyin = Pinyin initials of each Chinese game name, used for the Chinese search function

As mentioned above, this tool does not currently support generating the Chinese game lists, and will replace them with
duplicate English lists. This means the console will still work in Chinese mode, but all game titles and search
functionality will be in English.

If you are manually restoring these files from a backup, you should ensure that each set of three files is kept in sync:
for example, if you want to restore a backup for GB, you should restore vdsdc.tax, umboa.nec and qdvd6.bvs.

Credits
-------

Developed by taizou

Contributors: Evan Clements

RGB565 conversion code based on PNG-to-RGB565 (c) 2019 jpfwong
https://github.com/jimmywong2003/PNG-to-RGB565

Frog icon from public domain photo by LiquidGhoul
https://commons.wikimedia.org/wiki/File:Australia_green_tree_frog_(Litoria_caerulea)_crop.jpg

Special thanks to the firmware devs

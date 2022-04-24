# StarParse on Linux (Updated 4/24/22 - Works for SWToR 7.0 updates)
Manual Instructions on installing StarParse on Linux machines using Wine, and Java

## Overview
This guide is used to install and orchestrate the use of Ixale's StarParse. A combat log parser for Star Wars - The Old Republic. The intended use of this guide is for folks that migrated over to Linux, installed SWTOR through either steam or lutris. I found myself really missing StarParse in raiding the second I moved over. I built this guide trying to find the simplest way possible to get it up and running. I am sure that there are better ways of doing this. If you believe you have a better solution, please create an issue with examples, code, and instructions.

[Misc. Addendums](https://github.com/Rash-in/StarParseLinux/wiki/Misc-Wiki-Index)

### High Level Overview:
- Use wine to pull the StarParse application out of the .exe
- Use java to initially run the StarParse Updater that is built in.
- Finally, use java to run the client application

### Disclaimer:
There is nothing official about this. It is a hackey workaround to get a third party app to function for a 10 year old game. If you break it, the onus is on you to fix it. Be a good 'puter person and make backups just in case. :P

### Updates:
This walkthrough is meant for first time installation. If you are re-installing StarParse based on the previous instructions. Or just cleaning house and starting over, I would recommend the easiest option which is prior to starting the below instructions, delete the `~/.StarParse` folder. That will wipe everything out that was done previous. At the end of this walkthrough, you will have to re-add your raid groups, and any timers that you manually entered, but you should have those noted down somewhere first **(HINT HINT WINK WINK)**

### Kudos
* @EeVee0 - For showing me the light for java linux native.

### Requirements:
* Ubuntu or similar debian based variant.
  * (I am not universally knowledgable across distros for variants.)
* Star Wars - The Old Republic (Installed and playable) - Too many other guides out there to do this.
  * Steam or Lutris
* Wine 64Bit -  latest-stable (Tested with wine-5.0.3 on Ubuntu 21.10 64bit)
  * sudo apt install wine64
* Winetricks (version 0.0+20210206-2 installed through Ubuntu Software app) 
  * Version shouldn't matter too much for this as we are using it for a single feature function.

### Downloads - Ensure both of these are in your `~/Downloads` folder
1) [Java](https://java.com/en/download/manual.jsp) - At the time of this article latest is: jre-8u321-linux-**x64**.tar.gz
      - Note: Please make sure that it is the x64 version of java. It will say it at the end of the file name before the extension
3) [StarParse](http://cdn.ixparse.com/dl/StarParse-1.0.exe)

### Contents:
1)  [Create Temporary Wine Bottle & folder structure](#1-create-temporary-wine-bottle--folder-structure)
2)  [Install StarParse: Part 1 - Extraction](#2-install-starparse-part-1---extraction)
3)  [Install StarParse: Part 2 - App Updater & Test Launch](#3-install-starparse-part-2---app-updater--test-launch)
4)  [Finalizing install and final shortcuts](#4-finalizing-install-and-final-shortcuts)
5)  [Addendum: Fonts and Font Sizes](#5-addendum-fonts-and-font-sizes)
6)  [Addendum: Overlays](#6-addendum-overlays-gnome)
------------------

### 1) Create Temporary Wine Bottle & folder structure

Install Wine Bottle:
* Command: `WINEARCH=win64 WINEPREFIX=~/.StarParse winetricks fontsmooth=rgb`

Move Downloads to appropriate folder
* Command: `mkdir ~/.StarParse/StarParse && cp ~/Downloads/StarParse-1.0.exe ~/.StarParse/StarParse && cp ~/Downloads/jre-8u321-linux-x64.tar.gz ~/.StarParse/StarParse && cd ~/.StarParse/StarParse && tar zxvf jre-8u321-linux-x64.tar.gz`
* Note: I am making the Java Runtime local to the app itself for simplicity. Normally Java installs can be made local to the machine meaning all apps can use it. But it is easier for the guide to make it app specific for shortcuts, etc.
------------------

### 2) Install StarParse: Part 1 - Extraction
Start the installer via Wine.   HUGE NOTE:  This WILL fail. This is an intended result to extract the files from the installer. This is the hackey part of the workaround from this point forward. Once run and got the expected errors you can close out of the java applet that popped up. It should return you to the command prompt when done. If the command prompt is stuck on the System Error, it is safe to press:  ctl+c     in order to force out of the command.

* Command: `WINEPREFIX=~/.StarParse wine ~/.StarParse/StarParse/StarParse-1.0.exe`

Remove some uneeded files
* Command: `rm ~/Desktop/StarParse.lnk && rm ~/Desktop/StarParse.desktop`

Move app files from inside wine to our folder
* Command: `cp -R ~/.StarParse/drive_c/users/$USER/Local\ Settings/Application\ Data/StarParse/* ~/.StarParse/StarParse/`
------------------

### 3) Install StarParse: Part 2 - App Updater & Test Launch

Next we will be running the java app agaist the StarParse app updater to get the current app. You will see the StarParse applet launch and start downloading the update files. NOTE: At the end you will see an error trying to launch the app. This is intended as we will be launching the app seperately.
* Command: `cd ~/.StarParse/StarParse/app`
* Command: `~/.StarParse/StarParse/jre1.8.0_321/bin/java -jar starparse-launcher.jar`
* Manually close out of the StarParse applet with the error message. The background terminal will exit back to the prompt. At the end of this, there will be a new folder in `~/.StarParse/StarParse/app` called `client` which houses the actual application.
* Ensure file permissions are yours - Command: `chown -R $USER:$USER ~/.StarParse`

Testing launch: At this point StarParse should load. You will see the changelog window pop up and can click through to the main app. YAY!
* Command: `cd /home/$USER/.StarParse/StarParse/app/client/app && ~/.StarParse/StarParse/jre1.8.0_321/bin/java -jar starparse-client.jar`

Close down StarParse App and will start to wrap things up

------------------

### 4) Finalizing install and final shortcuts.

Remove Wine Bottle but keep our folders and structure
* Command: `cd ~/.StarParse && rm -rf dosdevices && rm -rf drive_c && rm system.reg && rm user.reg && rm userdef.reg && rm winetricks.log && rm .update-timestamp`
* Command: `cd ~/.local/share/applications/wine/Programs && rm -rf Ixale`


Create files to be able to start and show app.
* Command: `cd ~/.StarParse && touch StarParse.desktop && touch startStarParse.sh && chmod +x StarParse.desktop && chmod +x startStarParse.sh`

Open `startStarParse.sh` with your favorite text editor and put in data. Then save and exit.:
```
#!/bin/bash

cd "/home/$USER/.StarParse/StarParse/app/client/app" &&
nohup /home/$USER/.StarParse/StarParse/jre1.8.0_321/bin/java -jar starparse-client.jar & 2> /dev/null;exit;
```

Open `StarParse.desktop` with your favorite text editor and put in data.Then save and exit:
* Note: Where it has `<user>` you will need to put in your linux user name
```
[Desktop Entry]
Name=StarParse
Exec=nohup /home/<user>/.StarParse/startStarParse.sh &
Type=Application
Terminal=false
StartupNotify=true
Path=/home/<user>/.StarParse/
Icon=/home/<user>/.StarParse/StarParse/StarParse.ico
StartupWMClass=com.ixale.starparse.gui.StarparseApp
```

Copy `StarParse.desktop` file to where OS can read it:
* Command: `cp ~/.StarParse/StarParse.desktop ~/.local/share/applications/`

Check your applications in your app launcher.
* Open your applications menu and you should see the file with the appropriate icon. Click on it to ensure that it works.

The last thing that you need to do is find out where your combat logs are being stored in either steam or in Lutris. 

In StarParse click File -> Settings -> In Log Directory Click Choose and it will open a popup.
In the popup you are going to navigate to one of two default locations depending on how you installed SWTOR
* Lutris default location: `~/Games/Star\ Wars\ -\ The\ Old\ Republic`
* Steam default location: `~/.steam/debian-installation/steamapps/compatdata/1286830/pfx`
* Open the respective applications either Steam or Lutris and find out where the install locations are if you are unsure. Navigate to them using the StarParse Browse Popup.
* Note: Once you start to see drive_c it will be the same in both apps. (caveat - steams USERNAME is steamuser, Lutris will be yours specifically)

`WHEREVERINSTALLED/drive_c/users/USERNAME/Documents/Star\ Wars\ -\ The\ Old\ Republic/CombatLogs`
- If you do see The Star Wars folder but not CombatLogs, you will need to go in-game, turn on combat logging from the preferences, and go whack something with a stick so that you have at least one started log.
http://ixparse.com/img/swtor-combat-logging.png
------------------

### 5) Addendum: Fonts and Font Sizes
When you start parsing, you will quickly notice that the font size is making viewing the numbers difficult to read on the side panel as well as the overlay. This is an issue with default fonts and font sizes with Java. There is a fix for this. Of course this is optional and will continue to work without it. But if you are like me and it makes you cringe like a fedora wearing katana wielding neckbeard to see it; Use the below steps

Use package manager to install TTF Fonts.
* Command: `sudo apt get install ttf-mscorefonts-installer`

Using your favorite text editor (needs admin rights), edit the font definitions file:
* Location: `/etc/fonts/conf.avail/60-latin.conf`

This part is a little tedious. You will need to add 3 lines to this file. There are 3 groups in this file. `serif`, `sans-serif`, and `monospace`. Each of these will have a `<prefer>` block. The top spot of the prefer block is where you will need to make your addition.

* For the `serif` family, add the top prefer line as:
`<family>Times New Roman</family>`

* For the `sans-serif` family, add the top prefer line as:
`<family>Arial</family>`

* For the `monospace` family, add the top prefer line as:
`<family>Courier New</family>`

* A snippit of what the first edit looked like after the change.
```
  <description>Set preferable fonts for Latin</description>
        <alias>
                <family>serif</family>
                <prefer>
                        <family>Times New Roman</family>   <--I added this line
                        <family>DejaVu Serif</family>
                        <family>Bitstream Vera Serif</famil
```

Save and close the text editor. We will need to apply the new config in order for it to take affect.
* Command: `sudo dpkg-reconfigure fontconfig`
* Close StarParse and re-launch.

------------------

### 6) Addendum: Overlays (Gnome)
Note: For KDE, look [here](https://github.com/Rash-in/StarParseLinux/wiki/Overlays-with-KDE)
* In StarParse open the interface you are trying to use for the overlay - stats, timer, raid HoTs, etc
* Click the window so that it is the window in focus
* Keypress: `alt + space`
* In the context menu select Always on Top
* Move the overlay window to where you want it while game is running.
* Optional: lock overlays from the interface menu on StarParse.

## Fin

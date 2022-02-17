# StarParse on Linux (Updated 2/17/22 - Works for SWToR 7.0 updates)
Manual Instructions on installing StarParse on Linux machines using Wine, and Java

## Overview
This guide is used to install and orchestrate the use of Ixale's StarParse. A combat log parser for Star Wars - The Old Republic. The intended use of this guide is for folks that migrated over to Linux, installed SWTOR through either steam or lutris. I found myself really missing StarParse in raiding the second I moved over. I built this guide trying to find the simplest way possible to get it up and running. I am sure that there are better ways of doing this. If you believe you have a better solution, please create an issue with examples, code, and instructions.

### Disclaimer:
There is nothing official about this. It is a hackey workaround to get a third party app to function for a 10 year old game. If you break it, the onus is on you to fix it. Be a good 'puter person and make backups just in case. :P

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
1) [Java](https://java.com/en/download/manual.jsp) - At the time of this article latest is: jre-8u321-linux-x64.tar.gz
2) [StarParse](http://cdn.ixparse.com/dl/StarParse-1.0.exe)

### Contents:
1)  Create Temporary Wine Bottle & folder structure
2)  Install StarParse: Part 1 - Extraction
4)  Install StarParse: Part 2 - App Updater & Test Launch
5)  Finalizing install and final shortcuts.
------------------

### 1. Create Temporary Wine Bottle & folder structure

Install Wine Bottle:
* Command: `WINEARCH=win64 WINEPREFIX=~/.StarParse winetricks fontsmooth-rgb`

Move Downloads to appropriate folder
* Command: `cp ~/Downloads/StarParse-1.0.exe ~/.StarParse/`
* Command: `mkdir ~/.StarParse/java && cp /home/$USER/Downloads/jre-8u321-linux-x64.tar.gz ~/.StarParse/java && cd ~/.StarParse/java && tar zxvf jre-8u321-linux-x64.tar.gz`
* Note: I am making the Java Runtime local to the app itself for simplicity. Normally Java installs can be made local to the machine meaning all apps can use it. But it is easier for the guide to make it app specific for shortcuts, etc.
------------------

### 2) Install StarParse: Part 1 - Extraction
Start the installer via Wine.   HUGE NOTE:  This WILL fail. This is an intended result to extract the files from the installer. This is the hackey part of the workaround from this point forward. Once run and got the expected errors you can close out of the java applet that popped up. It should return you to the command prompt when done. If the command prompt is stuck on the System Error, it is safe to press:  ctl+c     in order to force out of the command.

* Command: `WINEPREFIX=~/.StarParse wine ~/.StarParse/StarParse-1.0.exe`
------------------

### 3) Install StarParse: Part 2 - App Updater & Test Launch
Next we will be running the java app agaist the StarParse app updater to get the current app. You will see the StarParse applet launch and start downloading the update files. NOTE: At the end you will see an error trying to launch the app. This is intended as we will be launching the app seperately.
* Command: `~/.StarParse/java/jre1.8.0_321/bin/java -jar ~/.StarParse/drive_c/users/$USER/Local\ Settings/Application\ Data/StarParse/app/starparse-launcher.jar`


Manually close out of the StarParse applet with the error message. The background terminal will exit back to the prompt. At the end of this, there will be a new folder in `~/.StarParse/java` called `client` which houses the actual application.


Testing launch: At this point StarParse should load. You will see the changelog window pop up and can click through to the main app. YAY!
* Command: `~/.StarParse/java/jre1.8.0_321/bin/java -jar ~/.StarParse/java/client/app/starparse-client.jar`
------------------

### 4) Finalizing install and final shortcuts. (WIP - Below is still old content that does not apply.)
First thing to do. Remove Wine Bottle as there is nothing more needed with Wine.
* Command: `cd ~/.StarParse && rm -rf dosdevices && rm -rf drive_c && rm system.reg && rm user.reg && rm userdef.reg && rm winetricks.log && rm .update-timestamp`

Create `.desktop` file so you have a clickable link to run the app.
* Command `cd ~/.StarParse && touch StarParse.desktop && chmod +x StarParse.desktop`

Open `StarParse.desktop` with your favorite text editor and put in data.
* Note: Where it has `<user>` you will need to put in your linux user name
```
[Desktop Entry]
Name=StarParse
Exec=/home/<user>/.StarParse/java/jre1.8.0_321/bin/java -jar /home/<user>/.StarParse/app/StarParse/client/app/starparse-client.jar
Type=Application
StartupNotify=true
Path=/home/<user>/.StarParse/app/StarParse/client/app/starparse-client.jar
Icon=/home/<user>/.StarParse/app/StarParse/StarParse.ico
```


Using your favorite text editor open ~/StarParse/StarParse.desktop
* Line 3 for exec: replace with this. Then change out where it says USERNAME with your user name

```
Exec=env WINEPREFIX="/home/USERNAME/.StarParse" wine-stable C:\\\\windows\\\\command\\\\start.exe /Unix /home/USERNAME/.StarParse/dosdevices/c:/users/USERNAME/Local\\ Settings/Application\\ Data/StarParse/app/client/app/starparse-client.jar
```

Line 7 for Icon: replace with this. Then change out where it says USERNAME with your user name

Line 7 = `Icon=/home/USERNAME/.StarParse/StarParse.ico`

The overall file will look like this but with USERNAME replaced.
```
[Desktop Entry]
Name=StarParse
Exec=env WINEPREFIX="/home/USERNAME/.StarParse" wine-stable C:\\\\windows\\\\command\\\\start.exe /Unix /home/USERNAME/.StarParse/dosdevices/c:/users/USERNAME/Local\\ Settings/Application\\ Data/StarParse/app/client/app/starparse-client.jar
Type=Application
StartupNotify=true
Path=/home/USERNAME/.StarParse/dosdevices/c:/users/USERNAME/Local Settings/Application Data/StarParse
Icon=/home/USERNAME/.StarParse/StarParse.ico
StartupWMClass=starparse.exe
```

* Once you save this. Right click on the StarParse.desktop file with nautilus/ File explorer and choose Properties -> permissions. Check the box that says: Allow executing file as a Program

* Copy it to your applications directory
Command: `cp ~/.StarParse/StarParse.desktop ~/.local/share/applications/`

* Open your applications menu and you should see the file with the appropriate icon. Click on it to ensure that it works.
* The last thing that you need to do is find out where your combat logs are being stored in either steam or in Lutris. 

In StarParse click File -> Settings -> In Log Directory Click Choose and it will open a popup.
In the popup you are going to navigate to one of two default locations depending on how you installed SWTOR
* Lutris default location: `~/Games/Star\ Wars\ -\ The\ Old\ Republic`
* Steam default location: `~/.steam/debian-installation/steamapps/compatdata/1286830/pfx`
* Open the respective applications either Steam or Lutris and find out where the install locations are if you are unsure. Navigate to them using the StarParse Browse Popup.
* Once you start to see drive_c it will be the same in both apps. (caveat - steams USERNAME is steamuser, Lutris will be yours specifically)

`WHEREVERINSTALLED/drive_c/users/USERNAME/Documents/Star\ Wars\ -\ The\ Old\ Republic/CombatLogs`

## Fin

# StarParseLinux
Instructions on installing StarParse on Linux machines using Wine

## Overview

Requirements:
- Ubuntu or similar debian based variant. (I am not universally knowledgable across distros for variants.)
- Star Wars - The Old Republic (Installed and playable) - Too many other guides out there to do this.
  -- Steam or Lutris
- Wine 64Bit -  latest-stable (Tested with wine-5.0.3 on Ubuntu 21.10 64bit)
  -- sudo apt install wine64
- Winetricks (version 0.0+20210206-2 installed through Ubuntu Software app) 
  -- Version shouldn't matter too much for this as we are using it for a single feature function.

This guide is used to install and orchestrate the use of Ixale's StarParse. A combat log parser for Star Wars - The Old Republic. The intended use of this guide is for folks that migrated over to Linux, installed SWTOR through either steam or lutris. I found myself really missing StarParse in raiding the second I moved over. I built this guide trying to find the simplest way possible to get it up and running. I am sure that there are better ways of doing this. But this was the end result of what I had found at the time. I will be updating this as soon as I improve the method (possibly some scripting).

Additionally, an email was sent to Ixale directly to see if they would be able to buld out the Java packages in a linux port since really it shouldn't have any Windows Only dependencies. Hopefully the email didn't get autodeleted :P or thrown in spam. First time emailing so who knows.

Disclaimer: There is nothing official about this. It is a hackey workaround to get a third party app to function for a 12 year old game. If you break it, the onus is on you to fix it. Be a good 'puter person and make backups just in case. :P

## Contents:
1) Create Wine Bottle with the necessary file structure and appropriate feature functions
2) Install Java
3) Install StarParse: Part 1
4) Install StarParse: Part 2
5) Finalizing install and final shortcuts.

1. Create Wine Bottle.

I dislike installing everything in wine to ~.wine. Every build should have its own bottle so that you don't necessarily overwrite things or run into more compatibility issues. I am creating a folder inside the home directory called ~/.StarParse   . All files for this will be inside this directory going forward. Needs to be 64-bit installation as that is the archetecture that StarParse is. The below command also installs the fontsmooth-rgb winetricks pagage. This allows for anti-aliasing of the fonts. Nothing will be legible in the application otherwise. 

Command:
`WINEARCH=win64 WINEPREFIX=~/.StarParse winetricks fontsmooth-rgb`

Navigate to the folder and make a new folder for some downloads. Note: this is just a personal preference of mine. I keep downloads so that I know the version numbers if I need to check for updates (for java). I don't do the whole auto-update with Java. Whole 'nother topic tho. The rest of this instruction assumes that you create the folder as listed.
`mkdir ~/.StarParse/dls`
---
2) Install Java
Download the latest Java Runtime. (At the time of this article: jre-8u311-windows-x64.exe)
Note that it needs to be 64-bit and the Windows variant. On the site it says that it is the Offline variant. Place that file into the folder you just created.
(https://java.com/en/download/manual.jsp)

`cp ~/Downloads/jre-8u311-windows-x64.exe ~/.StarParse/dls/`
`cd ~/.StarParse/dls`
---
Start the install file in silent mode. Please Note that you will see a bunch of terminal based errors on this. This is expected and most all of them are normal to installing something without clicking through it. (As far as I know, that is). The argument '/s' is for silent mode. It would normally pop up with a window with the usual java installer otherwise.

Command: (replace with your filename if the version is different)
`WINEPREFIX=~/.StarParse wine jre-8u311-windows-x64.exe /s`
---
3) Install StarParse: Part 1
Download StarParse from Ixale's site and move the file into the same folder as the Java installer.
(http://ixparse.com/)

`cp ~/Downloads/StarParse-1.0.exe ~/.StarParse/dls/`
`cd ~/StarParse/dls`
---
Start the installer via Wine.   HUGE NOTE:  This WILL fail. This is an intended result to run the application. This is the hackey part of the workaround from this point forward. Once run and got the expected errors you can close out of the java applet that popped up. It should return you to the command prompt when done. If the command prompt is stuck on the System Error, it is safe to press:  ctl+c     in order to force out of the command.

`WINEPREFIX=~/.StarParse wine StarParse-1.0.exe`
---
Explanation for the workaround:  Wine is normally used to run an app. Java is the app that we are going to be running so using wine to access StarParse is going to fail. Running Wine to run Java to run StarParse is what we are working towards.
---
4) Install StarParse: Part 2
Next we will be symlinking our app folder to where wine installed it so that we don't need to jump through the hoops of finding the nested folder structure (It gets waaaay too long.)

$USER is shorthand to whatever you are logged in as.

`ln -s ~/.StarParse/drive_c/users/$USER/Local\ Settings/Application\ Data/StarParse/app ~/.StarParse/`
---
Now to make all the Linux people angry:    Please open file navigator (nautilus in Unbuntu) so that you can visually see the inside your home directory. You will need to enable hidden files in the view options so that you can see ~/.StarParse directory.

Double click on the 'app' folder and you should see a couple files and a lib folder. The application we are looking at is starparse-launcher.jar    this is the main startup application on first install. You should see a java icon. Right click on the file and  you should see:

Open with Java(TM) Platform SE Binary

If you don't see that choose "Open with Other Application". click view all and scroll through the list until you find it.
--
Once you either double click the file or run it with Java (TM) Platform SE Binary you will see the initial application start to download updates. In the folder another folder is created called "Client". This is the end result that we will be accessing to run StarParse. Go ahead and close out of StarParse by clicking the x in upper right like a normal windows app.
--
5) Finalizing install and final shortcuts.
Now we make another link to the main folder so that we can access the client folder directly. The previous one created is no longer necessary to keep a shortcut for as the main application is further down.  Additionally copy the icon file over to the base folder since it is no longer being used by anything.

(Remove Old Link)
`rm ~/.StarParse/app`

(Add New Link)
`ln -s ~/.StarParse/drive_c/users/$USER/Local\ Settings/Application\ Data/StarParse/app/client/app ~/.StarParse/`

(Get Icon File)
`cp ~/.StarParse/drive_c/users/$USER/Local\ Settings/Application\ Data/StarParse/StarParse.ico ~/.StarParse/`

(Getting rid of the java shortcut links installed in your applications directory) <-Pet Peeve of mine
`rm -R ~/.local/share/applications/wine/Programs/Java`

(Copying StarParse shortcut link to app folder)
`cp ~/.local/share/applications/wine/Programs/Ixale/StarParse.desktop ~/.StarParse`
`rm -R ~/.local/share/applications/wine/Programs/Ixale`

Using your favorite text editor open ~/StarParse/StarParse.desktop
--Line 3 for exec: replace with this. Then change out where it says USERNAME with your user name

`Exec=env WINEPREFIX="/home/USERNAME/.StarParse" wine-stable C:\\\\windows\\\\command\\\\start.exe /Unix /home/USERNAME/.StarParse/dosdevices/c:/users/USERNAME/Local\\ Settings/Application\\ Data/StarParse/app/client/app/starparse-client.jar`
---
--Line 7 for Icon: replace with this. Then change out where it says USERNAME with your user name

`Icon=/home/USERNAME/.StarParse/StarParse.ico`
---
The overall file will look like this but with USERNAME replaced.
`
[Desktop Entry]
Name=StarParse
Exec=env WINEPREFIX="/home/USERNAME/.StarParse" wine-stable C:\\\\windows\\\\command\\\\start.exe /Unix /home/USERNAME/.StarParse/dosdevices/c:/users/USERNAME/Local\\ Settings/Application\\ Data/StarParse/app/client/app/starparse-client.jar
Type=Application
StartupNotify=true
Path=/home/USERNAME/.StarParse/dosdevices/c:/users/USERNAME/Local Settings/Application Data/StarParse
Icon=/home/USERNAME/.StarParse/StarParse.ico
StartupWMClass=starparse.exe`
---
Once you save this. Right click on the StarParse.desktop file with nautilus/ File explorer and choose Properties -> permissions. Check the box that says: Allow executing file as a Program
--
(Copy it to your applications directory)
`cp ~/.StarParse/StarParse.desktop ~/.local/share/applications/`

Open your applications menu and you should see the file with the appropriate icon. Click on it to ensure that it works.
---
The last thing that you need to do is find out where your combat logs are being stored in either steam or in Lutris. 

In StarParse click File -> Settings -> In Log Directory Click Choose and it will open a popup.
In the popup you are going to navigate to one of two default locations

Lutris - `~/Games/Star\ Wars\ -\ The\ Old\ Republic`
Steam - `~/.steam/debian-installation/steamapps/compatdata/1286830/pfx`

Open the respective applications either Steam or Lutris and find out where the install locations are if you are unsure. Navigate to them using the StarParse Browse Popup. Once you start to see drive_c it will be the same in both apps. (caveat - steams USERNAME is steamuser, Lutris will be yours specifically)

`WHEREVERINSTALLED/drive_c/users/USERNAME/Documents/Star\ Wars\ -\ The\ Old\ Republic/CombatLogs`

Fin.

Like I mentioned above, I plan on makeing some changes to automate a lot of this to something easier in the future. Or if I can get some assistance from Ixale and a port should be able to do this natvely in Linux. My Hope is for Ixale but this and future edits should suffice until then.

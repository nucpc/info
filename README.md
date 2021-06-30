# NUCPC

### Here's the step by step instructions
```
Brand new unit
Let it reboot a few times once it's complete.
You'll be prompted with setup wizard.
For the initial setup you'll need the account info and password.
Once you enter the account info the unit will reboot.
After reboot, go ahead click on More. Do worry about any error you see.
We still need to configure the unit. Now click on Settings.
You should be prompted to enter Administrator password which is sfb.
In the Settings window, make a note of the current IP Address and Host name.
Click on Windows Settings. Again you will be prompted to enter Administrator password, enter sfb.
Now you'll be in window screen select administrator and enter sfb for the password.
Once you're in windows desktop, press windows key + I, then select System.
Select Remote Desktop, then Eanble Remote Destkop. 
Under Remote Deskto you see "About", click About, click "Rename this PC"
For PC name please use this format "SiteCodeTRSRoomName"
Enter the new PC name and follow the prompt but don't reboot yet, leave the window open.
Update the Time/Date and Timezone, and use the NTP Server 172.17.32.253 or 132.189.40.119
Select Account make sure you have the correct account and password.
Under "Supported meeting mode" make sure its set to "Microsoft Teams (default)"
then click Save and exit.
```

````
Timezone
-Get the current time zone-
Get-Timezone
or
Get-CimInstance Win32_TimeZone | select *

-Get time zones that match a specified string-
Get-TimeZone -Name "*pac*"

-Get all available time zones-
Get-TimeZone -ListAvailable

--Set the time zone by Id--
Set-TimeZone -Id "Pacific Standard Time" -PassThru

w32tm / query / configuration
w32tm /config /manualpeerlist:132.189.40.119 /syncfromflags:manual /reliable:yes /update
w32tm /config /manualpeerlist:172.17.32.253 /syncfromflags:manual /reliable:yes /update
w32tm /config /manualpeerlist:time.windows.com /syncfromflags:manual /reliable:yes /update 'Use this if the time doesn't fixed it.
W32tm /resync /force
````
###### Added in 2021

```
In Windows Task Scheduler
Create Daily Task

Called it "Daily Auto Force shutdown"
schtasks /create /sc daily /tn "Daily auto force shutdown" /tr "c:\windows\system32\shutdown.exe /t 0 /s /f" /st 21:59



Now get in to NUC BIOS and get to the Power Setting
change the following

After Power Failure "Power On"
Deep S4/5" "Check the mark"
Wake on LAN from S4/S5 "Power On - Normal Boot"
Wake System from S5 "Check the mark"
Recurrence "Daily"
Wakeup Hour "23"
Wakeup Minutes "59"
Wakeup Second"59"
PCIe ASPM Support "unCheck the mark"
The reset of the field should be set to default.
```
[Install Logitech Sync new version](https://ufile.io/pkldq4a3)

[Install Logitech Sync](https://download01.logi.com/web/ftp/pub/techsupport/cameras/LogiSyncInstaller_2.0.412.exe)

[Install VNC Viewer](https://www.tightvnc.com/download/2.8.27/tightvnc-2.8.27-gpl-setup-64bit.msi)

Enable Remote Desktop

Execute this command in NUC PC

##### Commands in the elevated Powershell:
```
Enable-PSRemoting -SkipNetworkProfileCheck -Force
Set-Item WSMan:\localhost\Client\TrustedHosts * -Force
Restart-Service WinRM
Get-Item WSMan:\localhost\Client\TrustedHosts
 
Get-NetConnectionProfile | Set-NetConnectionProfile -NetworkCategory Private
Set-NetFirewallProfile -Profile Private -Enabled False


Test-WSMan {enter ComputerName or IP Address}
winrs -r:IP Address or Computername -u:Computername\admin -p:password cmd or powershell

Commands in the elevated command prompt:
netsh advfirewall firewall add rule name="ICMP allow incoming V4 echo request" protocol=icmpv4:8,any dir=in action=allow
```
##### This command will stop windows update
```
net stop wuauserv
net stop bits
net stop dosvc
sc config wuauserv start=disabled
```

###### If you want to re-enable the Windows updates at any later time, then give these commands in the elevated command prompt:
```
sc config wuauserv start=auto
sc start wuauserv
net start wuauserv
net start bits
net start dosvc
```
###### Schedule automatic restart
```
schtasks /create /sc daily /tn "Daily auto force shutdown" /tr "c:\windows\system32\shutdown.exe /t 0 /s /f" /st 21:59

schtasks /create /sc daily /tn "Auto force reboot 3am" /tr "c:\windows\system32\shutdown.exe /t 0 /r /f" /st 03:00
```

````
Make sure when to used powershell command
winrs -r:IP Address or Computername -u:Computername\admin -p:password powershell

Invoke-WebRequest -Uri https://www.gyan.dev/ffmpeg/builds/ffmpeg-release-essentials.zip -OutFile c:\yt\test1\test2\ffmpeg.zip
````


```
shutdown /t 0 /r /f
```
###### FFMPEG command
[FFMPEG](https://trac.ffmpeg.org/wiki/DirectShow)
```
Example to list dshow input devices:
ffmpeg -list_devices true -f dshow -i dummy

Example to print a list of options from a selected device:
ffmpeg -f dshow -list_options true -i video="Integrated Camera"


Example to use a dshow device as an input:
ffmpeg -f dshow -i video="Integrated Camera" out.mp4

Example to use audio and video dshow device as an input:
ffmpeg -f dshow -i video="Integrated Camera":audio="Microphone name here" out.mp4
```

````
Logitech Rally System

ffmpeg -f dshow -i video="Logi Rally Camera":audio="Echo Cancelling Speakerphone (3- Logi Rally Audio)" LRally.mp4
````

###### Logitech Meetup System

```
ffmpeg -f dshow -i video="Logitech MeetUp":audio="Echo Cancelling Speakerphone (Logitech MeetUp Speakerphone)" LMeetup.mp4


TRS.BAT
@echo off
rem net use x: \\fileserver\Sharefolder /user:domain\username password-here
c:
mkdir c:\ffmpeg
cd\ffmpeg

curl https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-20200324-e5d25d1-win64-static.zip -O ffmpeg
powershell expand-archive -path "c:\ffmpeg\ffmpeg-20200324-e5d25d1-win64-static.zip" -DestinationPath c:\ffmpeg\
copy c:\ffmpeg\ffmpeg-20200324-e5d25d1-win64-static\bin\*.* c:\ffmpeg\
ffmpeg -list_devices true -f dshow -i dummy
Rem  the command below will only record meetup camera without audio.
ffmpeg -f dshow -i video="Logitech MeetUp" meetupcam.mp4
Rem this will only record video without audio.  => ffmpeg -f dshow -i video="Logi Rally Camera" Rallycam.mp4
copy webcam.mp4 x:\it\filename
```


```
this is command from your PC 
ffmpeg -f dshow -i video="Logitech MeetUp":audio="Echo Cancelling Speakerphone (Logitech MeetUp Speakerphone)" -profile:v high -pix_fmt yuvj420p -level:v 4.1 -preset ultrafast -tune zerolatency -vcodec libx264 -r 10 -b:v 512k -s 640x360 -acodec aac -ac 2 -ab 32k -ar 44100 -f mpegts -flush_packets 0 udp://192.177.6.85:5000?pkt_size=1316

note: the IP Address 192.177.6.85 is your PC, that where you want to send the video feed
so now open up another cmd again.
change dir to yt
type in ffplay -i udp://0.0.0.0:5000
you'll get alot of error
it's okay just wait until you see the video feed.
```

```
ffmpeg -f dshow -video_size 1280x720 -framerate 30 -vcodec mjpeg -i video="Logitech MeetUp":audio="Echo Cancelling Speakerphone (Logitech MeetUp Speakerphone)" -preset ultrafast -tune zerolatency -vcodec libx264 -r 10 -b:v 512k -acodec aac -ac 2 -ab 32k -ar 44100 -f mpegts -flush_packets 0 udp://192.177.7.70:8090

same PC
type in this
ffplay udp://127.0.0.1:8090

ffplay -f dshow -video_size 1280x720 -framerate 30 -vcodec mjpeg -i video="Logitech MeetUp":audio="Echo Cancelling Speakerphone (Logitech MeetUp Speakerphone)" 
```



###### Startup Command
shell:startup    ' current default account startup

shell:common statrup ' common startup
 
###### Sample command for capturing Video and Audio with Logitech Rally Plus System

                   [ Using Logitech Rally ]               [ Using Logitech Rally Plus Microphones ]  Filename 4 output         
`ffmpeg -f dshow -i video="Logi Rally Camera" -f dshow -i audio="Echo Cancelling Speakerphone (Logi Rally Audio)" output.mp4`


### Manually update MTR apps via Powershell command
[See section -Software updates via Powershell](https://docs.microsoft.com/en-us/microsoftteams/rooms/rooms-operations)
 
Note the trick is to download the [updated MSI](https://go.microsoft.com/fwlink/?linkid=851168) from the endpoint itself, while logged in with admin.

After you download the latest MSI install it, leave it as default location for installation folder. (Microsoft Teams Room App v4.5.35.0)
Once installation is complete then create new share, follow the instruction below

```
Open Explorer in C:\Program Files (x86)
Now select and right click on “Skype Room System Deployment Kit” folder, 
Select properties, click tab Sharing, click Advanced Sharing.
Click Share this folder, in Share name: called it MTR, 
Click permissions, check allow for Full control, 
Change and read. Click Apply, ok, Apply.
```
Now right click windows logo in the lower left corner, 
Select Windows Powershell(admin).
In the Powershell Window Terminal, 
Create new map to x by using net use example below.

Just copy and paste the command below to your powershell window
 
copy and paste the command, then press enter to execute it.
``` 
Net use x: \\localhost\mtr
```
Now let’s do the update by copying the command below and pasting it to your powershell window
 
copy and paste the command, then press enter to execute it.
```
Add-AppxPackage  -ForceApplicationShutdown -Path 'x:\$oem$\$1\Rigel\x64\Ship\AppPackages\*\*.appx' -DependencyPath (Get-ChildItem 'x:\$oem$\$1\Rigel\x64\Ship\AppPackages\*\Dependencies\x64\*.appx' | Foreach-Object {$_.FullName})
``` 

Once it’s complete reboot the MTR system, it will reboot twice 
That’s it, you done.

## Logitech Recommended NUC updates process.

There are a few primary things that need to be done to alleviate this situation but I am going to provide you the recommended list of NUC updates that are recommended to minimize Teams related system issues.

### Recommended NUC updates process
On the NUC PC perform the following steps: (We recommend that the NUC power settings be set to always On never shutdown due to Tap Screen going to sleep and not waking due to Intel Power management of the internal motherboard USB/HDMI ports.

#### A. Boot into the Windows Admin account and change the Power setting (see attached) Note the power settings may be different depending on the version of Windows 10 your are using. The Intel Motherboard has power save options enabled that reduce power to sections of the circuits which cause issues with the displays and USB connections. So all power save options must be disabled.

In addition to the power setting changes on the attached document Microsoft has recently recommended going into the Device Manager and  then disable the "turn this device off to save power" feature on all USB Root Hubs on a given device. Select the USB Controllers - Root Hub device then right click then select "Properties" then select the "Power management" tab and un-check " Allow computer to turn this device off to save power".

Boot into Windows
To boot into Windows connect a Keyboard to the NUC then reboot it while it is coming up tap the Windows Key over and over until the Windows Login comes up on the screen. The select the Teams Admin account - password is sfb once completed with changes logout then login to the User account same password.

#### B. Install the intel driver and support assistant (Intel DSA) in the Windows Admin account and run the Intel Device Driver Assistant to ensure that the NUC is running the most updated versions of all required drivers.

[Link - Intel Support Assistant](https://www.intel.com/content/www/us/en/support/detect.html)

```
By default, the Intel® Driver & Support Assistant places downloaded drivers in
C:\ProgramData\Intel\DSA\Downloads
```

###### C. Update the NUC HDMI driver: (alleviates Teams related display issues) - [click HERE to download](https://downloadcenter.intel.com/download/29911/Intel-Graphics-DCH-Driver-for-NUC8i3BE-NUC8i5BE-NUC8i7BE?wapkw=HDMI%20nuc8i7beh)
old link
[click HERE to download](https://downloadcenter.intel.com/download/29472/HDMI-Firmware-Update-Tool-for-NUC8i3BE-NUC8i5BE-NUC8i7BE?v=t)


#### Note - Please ensure ALL instructions for followed when updating the HDMI drivers. Specifically, an HDMI device must be connected to the NUC HDMI port and powered on. These instructions can be found in the extracted driver folder or at this link.

#### D. Update the Displaylink device drivers [click HERE to download](https://www.displaylink.com/downloads/windows) then select "DisplayLink USB Graphics Software for Windows" Download and install these make sure the Tap is connected and functional as these manage the Tap display.

#### E: Update the Intel NUC BIOS: [click HERE to download - Intel-NUC-Kit-NUC8i5BEH](https://downloadcenter.intel.com/product/126148/Intel-NUC-Kit-NUC8i5BEH) 

#### F: Setup a second nightly reboot to ensure the Teams Room is up and ready after any windows/teams updates occurring during the night. As an example, if the Teams nightly reboot is set for 2 or 3 AM, adding one at 7 AM shortly before the office opens should ensure stability. I've also include the steps below to add another reboot task.

```
Press WinKey+R > type taskschd.msc 
and press Enter (it will launch Task Scheduler)
Left click from the right pane on the Task Scheduler Library > 
Open Action from the top menu > New Folder... > name it MyTasks > click OK
Left click on the MyTasks > choose Action from menu > 
Create Basic Task... (it will open task wizard)
You can enter the Name , for example "Restart", and press Next
In Trigger section you can specify when you want to run your task, for example:
Select Daily , and press Next
Now you can specify the day, the hour and set to recur every day, 
After that press Next
In Action section you can choose what you want to run, 
for example restart your system, to do this:
choose Start a program , 
and press Next in the Program/script field 
type shutdown /r , and press Next
And that's all, you can press Finish

or can be done via CMD, script below.
schtasks /create /sc daily /tn "Daily auto force reboot" /tr "c:\windows\system32\shutdown.exe /t 0 /r /f" /st 23:59

```


#### Thinksmart Hub 500 - Type 10V5 
[Link - Lenovo Support](https://smartsupport.lenovo.com/us/en/products/SMART/SMART-OFFICE/THINKSMART-HUB-500/10V5/10V50000US/YH010GGE)

```
https://smartsupport.lenovo.com/us/en/products/SMART/SMART-OFFICE/THINKSMART-HUB-500/10V5/10V50000US/YH010GGE

```



##### Windows Shell Commands
[Shell Commands for Windows 10: Navigating Common Folders in Windows](https://www.mirazon.com/shell-commands-for-windows-10-navigating-common-folders-in-windows/)

#### Create God Mode folder in your Desktop

``` 
Create a folder in your desktop called it "GodMode.{ED7BA470-8E54-465E-825C-99712043E01C}"

You call also run it by hold Windows Key + R to open Run command and then paste this
shell:::{ED7BA470-8E54-465E-825C-99712043E01C}

```

###### Good to remember command with shell commands

```
shell:Startup               - Current User Startup
shell:Common Startup        - All Startup
shell:connectionsfolder     - It will bring you to the network connection
shell:Downloads
shell:Favorites
shell:Profile
shell:UserProfiles
shell:Windows
```

[myForm](https://forms.office.com/Pages/ResponsePage.aspx?id=g7S4tZdV50qOJ_zEZKO1hAhlnEPJ44dOhJgtFWeMOhNUMkcxWEZGTVA2UjVVVUswTkM0MFdMRlNVTC4u)
```
find it (Get-WmiObject -query ‘select * from SoftwareLicensingService’).OA3xOriginalProductKey
```

```
[sitecode]bscdcp10.
get-pnpdevice -friendlyname *logi* | select status, Class, FriendlyName, InstanceID
```

```
batch file
@echo off
cls
runas /noprofile /user:sitecode-1-1-recep-01@.com "C:\Progra~2\Microsoft\Edge\Application\msedge.exe "https://teams.microsoft.com/_#/calls/speed-dial""
```
```
New Batch File but users will be prompted for username and password
@echo off
C:\Windows\System32\cmd.exe /c start shell:AppsFolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge -private https://teams.microsoft.com/_#/calls/speed-dial
```

```
To view RAW content of a photo in github use the URL below as an example:
https://raw.githubusercontent.com/nucpc/pc1/main/MTWR.png
```

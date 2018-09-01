
# Step 1: Preparing your Raspberry Pi
In this step, we will prepare our Pi for our Py/OpenCV build. We will cover hardware setup, flashing Raspbian, SSH, and freeing up space on our drive. If you're more advanced than this, don't lose faith: shit will hit the fan soon. Just skim this and feel free to move on to the Step 2 file.


### Flashing Raspbian & Formatting Drives

If you have not done so already, please head to [Etcher](https://etcher.io/) and download the proper package for your machine. Once that is finished, head [here](https://www.raspberrypi.org/downloads/raspbian/) to download the latest version of Raspbian Stretch. At the time of this writing, I have downloaded the .zip 4.14 release. 

Note on etcher- You are more than welcome to install Raspbian via NOOBS or some other method, but I recommend etcher because it's stupid simple and pretty to look at. 

Once you have completed the above, we need to format our drives and flash our MicroSD card with the Raspbian image we just downloaded. 

In your disk utility, format both your MicroSD Card and USB Stick to exFAT and name it whatever your heart desires. (Note: FAT32 and NTFS would be fine also, I just happened to use exFAT.) 

Next, launch Etcher. Leave your SD Card inserted, but eject your USB stick and set it aside for later. Once in etcher, we have 3 steps to complete:
  1. Select Image (The Raspbian .zip you downloaded)
  2. Select Drive (Ensure your SD drive is selected, it may autopopulate)
  3. Click "Flash!" and crack a cold one! (maybe a cold brew coffee instead, we have still have a long journey ahead!)
  
Once the flash has completed, go ahead and eject your SD card from your machine.

### Hardware Setup

Next step is to put all the pieces together. This is quite intuitive, so I will be quick here and drop a picture to reference.
  1. Insert SD card into the port (Located on underside of PI)
  2. Insert USB Drive & Other USB Peripherals 
  3. Connect Pi to display with HDMI
  4. Finally, connect your Pi to power
  
### Initialization & Disk Utilities

Once the Pi boots up for the first time, you will be shown a setup screen. Go ahead and fill that in and make note of system name, new passwords and all that jazz. Ensure you connect to the same Wifi as the machine you will SSH into Pi with. 

Reboot.
```
$ sudo shutdown now -r
```

If you formatted your disk to FAT32, skip to the next section, "SSH Setup." If you formatted your disk to exFAT or NTFS, we need to install some utilities before we move on.

Open terminal on RPI.

For exFAT formats, run:
```
$ sudo apt-get install exfat-fuse exfat-utils
```
and for NTFS formats, run:
```
$ sudo apt-get install ntfs-3g
```
Reboot.
```
$ sudo shutdown now -r
```

Great. Now your RPI should properly recognize your SD/USB devices! 
  
### SSH Setup

For the remainder of this project, we will be utilizing SSH to program our Pi. You could do all of this on your Pi directly if you'd like, but I'd highly recommend using SSH unless you don't have another machine to do the work on.

To set up SSH, we must first allow SSH connections within Pi. In your terminal:
```
$ sudo raspi-config
```
On the menu, select "Interfacing Options" >> Navigate to "SSH" >> Select "Yes" >> Hit "OK" >> Hit "Finish"

Great. Now that we can connect via SSH, we need the IP address for our Pi. In terminal once more:
```
$ ifconfig
```
The IP address we are looking for is the xxx.xx.x.xx next to "wlan0"

We now have everything we need to SSH into our Pi. On your non-pi machine (Mac, Windows Machine, etc) open up Terminal and type :
```
$ ssh pi@{Your Pi IP Address}
```
In my case, this looks like:
```
$ ssh pi@10.0.0.136
```
Enter the password you set during the initial setup of Raspbian. If you did not set a password, the default is "raspberry."

Note: Get a crazy man-in-the-middle warning that looks like this?
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
bx:fa:bx:b4:51:fe:xe:c7:1f:xx:ff:bf:4a:47:68:4a.
Please contact your system administrator.
Add correct host key in /Users/myname/.ssh/known_hosts to get rid of this message.
Offending RSA key in /Users/myname/.ssh/known_hosts:1
RSA host key for 192.168.xx.xx has changed and you have requested strict checking.
Host key verification failed.
```

Never fear! This is easily fixed by running the following command:
```
ssh-keygen -R {Your Pi IP Address}
```
Attempt to SSH back into the Pi again. This should fix you right up!


### A Few Last Configurations

Raspbian is a fantastic OS, but like other great OS's, it has it's share of crapware. Namely, Wolfram and LibreOffice. Purging them will free up over 1GB on our SD drive. In terminal:
```
$ sudo apt-get purge wolfram-engine
$ sudo apt-get --purge libreoffice*
$ sudo apt-get clean
$ sudo apt-get autoremove
```

Next, let's open up our file system a bit more to include all available memory. In terminal:
```
$ sudo raspi-config
```
On the menu, select "Advanced Options" >> Navigate to "Expand File System" >> Hit "Enter" >> Hit "Finish"

And lastly, let's update and upgrade all of our packages. In terminal:
```
$ sudo apt-get update && sudo apt-get upgrade
```

Reboot.
```
$ sudo shutdown now -r
```

## Next Up..
Let's move on to Step 2 in our project- The Build. See you there!

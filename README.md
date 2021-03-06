# Terminals

Follow the instructions below to set up the first and any additional terminals. Just pay attention to minor differences in configuration for additional terminals which will be noted in the steps.

## Requirements

* Raspberry Pi 3 with built-in wi-fi
* Micro SD card (minimum 4 GB) and an adapter to insert the card into a computer
* A computer with SSH client (all Macs have the Terminal app, on Windows you can use PuTTY)
* A DHCP-enabled network (preferably with Bonjour as well)

**Important!** While most of this setup can be done offline, the Pi *must* be connected to a network with access to the internet for the installation of node.js and Node RED.

## Installation

1. Download [FullPageOS](http://docstech.net/FullPageOS/2017-06-24_2017-06-21-fullpageos-jessie-lite-0.7.0.zip) (direct download link).
2. Unzip the image and "burn" it to your Micro SD card [like any other Raspberry Pi image](https://www.raspberrypi.org/documentation/installation/installing-images/README.md).
3. Before ejecting the Micro SD card from the computer, configure the wi-fi connection for FullPageOS by editing `fullpageos-network.txt` on the root of the flashed card. (This can of course be configured or updated later by plugging the micro SD card into any computer again.)
4. Insert the Micro SD card into a Raspberry Pi and connect the Pi to a power adapter (and network cable if you didn't configure wi-fi in step 3).
5. Wait a moment for the Pi to boot and then log on to the Pi via SSH: `ssh pi@fullpageos.local` (default password is "raspberry").

**Please note:** The address `fullpageos.local` is only available if your router supports Bonjour. If you can't connect to the Pi using the command in step 5, log in to your router and determine the Pi's assigned IP address in the router's list of connected devices, then replace `fullpageos.local` with the IP address.

## Configuration

### raspi-config settings

1. Run `sudo raspi-config`.
2. Select option 1 and change password to something other than "raspberry" (you need to relaunch raspi-config after this step)
3. Select option 2 and set hostname of machine to `t1`. (For additional terminals set hostname to `t2`, `t3`, etc. Also be aware that this changes the `.local` address you use to SSH into the Pi)
4. Select option 4, then on the next screen option 1 and then locate `sv_SE.UTF-8 UTF-8` in the list, press <kbd>Space</kbd> to select it and then <kbd>Return</kbd> to continue. When prompted on the next screen, choose `en_GB.UTF-8` as the system default.

### Installing node.js and Node RED

1. Copy and paste the following command into the SSH session with your Pi to automatically install both node.js and Node RED: `bash <(curl -sL https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/update-nodejs-and-nodered)` (**Heads up!** This process will probably take 10-20 minutes depending on the speed of your internet connection.)
2. When installation is completed, set Node RED to autostart: `sudo systemctl enable nodered.service`

### Set FullPageOS browser URL

Open `/boot/fullpageos.txt` with `vi`, `vim` or `nano` (i.e. `nano /boot/fullpageos.txt`) and replace the URL in this file with: `http://t1.local:1880/chat?id=1`. (When setting up additional terminals, change `?id=1` to `?id=2`, `?id=3`, etc.)

**Please note:** If your router doesn't support Bonjour as mentioned above, you will need to replace `t1.local` in the URL with the IP address that was assigned to the *first* terminal in your setup.

### Set keyboard layout to Swedish and disable modifier keys

Run `sudo nano /etc/default/keyboard` and set `XKBLAYOUT="se"` (<kbd>Ctrl</kbd>+<kbd>X</kbd> and then <kbd>Y</kbd> and <kbd>Return</kbd> to save).

Then also run `sudo nano ~/.profile` and add `setxkbmap se` to the bottom of the file and save.

To disable modifier keys and prevent user from using keyboard shortcuts like <kbd>Ctrl</kbd>+<kbd>W</kbd> to close the tab, run `sudo nano /usr/share/X11/xkb/symbols/pc` to open default keymap file. Then set the value inside brackets for the following lines to `NoSymbol`:

```
key <LCTL> {        [ Control_L             ]       };
key <LWIN> {        [ Super_L               ]       };

key <RTSH> {        [ Shift_R               ]       };
key <RCTL> {        [ Control_R             ]       };
key <RWIN> {        [ Super_R               ]       };
```
Like this:
```
key <LCTL> {        [ NoSymbol              ]       };
key <LWIN> {        [ NoSymbol              ]       };

key <RTSH> {        [ NoSymbol              ]       };
key <RCTL> {        [ NoSymbol              ]       };
key <RWIN> {        [ NoSymbol              ]       };
```

### Import the terminal flow (code) to Node RED and start chat server/client

1. Open a browser on a computer connected to the same network as the Pi and go to http://t1.local:1880. (If you are setting up an additional terminal change `t1` in this address to `t2` or whatever the hostname is for the terminal you are configuring.)
2. Click the "hamburger" menu in top right, point your cursor to *Import* and then click *Clipboard*.
3. Copy the contents of [t1.flow](https://raw.githubusercontent.com/vtamm/terminals/master/t1.flow) and paste it in the text area. Click *Import* and then click anywhere in the flow to drop all drop all the imported nodes into the flow. (For additional terminals after the first one, copy the contents of [tx.flow](https://raw.githubusercontent.com/vtamm/terminals/master/tx.flow) instead.)
4. Click *Deploy* to deploy the code. Done!
5. SSH into the PI and run command `rm -rf ~/.config/chromium/Singleton*` to prevent "profile in use" message from Chromium. Then reboot the Pi with `sudo reboot` and the Pi should restart into the chat page, ready to go. That's all!

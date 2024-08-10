# Who is this guide For

This guide is the path I took to download and install services on my personal Network Attached Server (NAS). This guide is written from the perspective of someone who is somewhat tech literate with Linux and windows but by no means an expert. I am attempting to write these guides so that anyone somewhat tech literate can follow them. I acknowledge that not everyone will be able to follow these guides that i create due to a vast diversity of IT knowledge.

---

# The Server

In these guides I am using a HP Z240 server with

- E3-1245 V5 CPU

- 16 GB DDR4 2133 MHz RAM

- 1, 256 GB m.2 NVME SSD

- 1, 512 GB SATA SSD

- 2, 8TB SATA HDDs

---

_22/05/2024_

# Initial Install of Open Media Vault

This guide outlines the path to installing Open Media Vault (OMV) on to the boot drive of an X86 based computer. It must be noted that as of writing, arm platforms have a different install method which can be found on the [OMV Documentation Pages](https://docs.openmediavault.org/). More information not contained in this document can also be found in those pages.

## ISO Installer Setup

To install open media vault to a boot drive, the best method is to use an ISO image from the [OMV Webpage](https://www.openmediavault.org/). Enter the download sub page and download the latest stable ISO image. Save this in a known location.

### File Integrity Check

Although checking the ISO file is important, it can be difficult for some users. This section is optional but highly recommended.

It is important to check the file hash and PGP key are correct. These both verify that the installer file has been downloaded correctly and the creator of the file is as expected. As I used a windows PC this part of the guide is windows specific but a similar process should be available on MAC and Linux.

On windows the command to get the SHA256 string is to open the command prompt and type in `certutil -hashfile [file name] SHA256` where `[file name]` is fully replaced with the actual file name. It should be noted that this command assumes you have navigated to the folder in the command prompt already. An easier way to do this is to open the File Explore and drag the file into the command prompt. This replaces the  `[file name]` component with the full file path of the file. The overall output should look something like `certutil -hashfile C:\Users\Ethan\Downloads\ISO_OMV\openmediavault_7.0-32-amd64.iso SHA256`. The result of the SHA256 has string can then be checked against the SHA256 provided on the [OMV Webpage](https://www.openmediavault.org/). If they match the file has been downloaded correctly.

On windows to verify to PGP key, you must first install [GPG4WiN](https://gpg4win.org/). This program will help verify the PGP key so that we know the ISO downloaded came from the open media vault organization. When GPGWIN is installed, it also downloads a program called "Kleopatra". We will used this to verify the PGP key.

1) If not done already, create a new personal Key pair in Kleopatra. This should be found either in the certificates menu or under the file tool bar. Follow the prompts entering a name and/or email. If you want attach a secure pass phrase do that as well.

![](Initial_OMV_Install/Create_Personal_PGP_Key_Pair.png)

2) Next we will need the .asc and .key files for this specific ISO. These can be found linked in the [OMV Download Subpage](https://www.openmediavault.org/download.html). Sort through the [OMV source forge folders](https://sourceforge.net/projects/openmediavault/files/) finding the relevant ISO files folder. Once in the folder, download the .asc and .key files and save them to the same folder as the ISO file.

3) The .key file will then be imported into Kleopatra. Click the import button and select the .key file previously downloaded. You will be asked to certify the key file, accept this and select the open media vault option. In the certify certificate screen, the PGP fingerprint of which is also in the [OMV Download Subpage](https://www.openmediavault.org/download.html) can be found and compared.

![](Initial_OMV_Install/Import_Open_Media_Vault_Key.png)
![](Initial_OMV_Install/Certify_Certificate.png)

4) To verify the ISO file, click the Decrypt/Verify button and through the file explorer, select the ISO file. After some time, a confirmation screen should appear confirming the validity of the signature.

![](Initial_OMV_Install/Decrypt_Verify.png)
![](Initial_OMV_Install/Valid_Signature.png)

### Flashing Drive

Once the ISO file integrity has been checked (optional but recommended), the ISO file must be flashed to a USB. A common flashing tool is [Balena Etcher](https://etcher.balena.io/) which can be downloaded on Windows, MAC and Linux. This is the tool I used with a windows PC.

1) Insert a USB stick into the computer the ISO file and Balena Etcher has been downloaded to.

2) Launch Balena Etcher and select the flash from file option. Choose the relevant ISO file in it's saved location using file explorer pop up.

![](Initial_OMV_Install/Balena_Etcher_Flash_From_File.png)

3) Select the target USB that was inserted in step 1. **Be Warned,** double check you choose the correct target drive as selecting any of your PCs Drives will cause issues and/or break your PC. Etcher should warn you if you are about to this this.

![](Initial_OMV_Install/Etcher_USB_Selection.png)

4) Now just flash the drive. After some time, there should be a confirmation that the ISO has been flashed to the drive. Once that is the case. remove the drive.

## Installing on Server boot Drive

Now that the ISO USB drive has been setup, the OMV can be installed onto the Server. Plug the USB drive into one of the USB ports and turn on the server. During the boot process enter the BIOS/ Startup Menu of your system. This is typically done by spamming esc or delete on the keyboard but, check your motherboard or systems manual for details. Once in the menu, locate the boot menu and make sure the system will boot to the ISO USB inserted. This will typically be labeled USB Disk or something to that effect. To help locate the correct Drive, disconnecting all drives besides the boot and ISO USB may be helpful.

Once the USB drive boots, you will be presented with a Graphical User Interface (GUI) to take you through the install process. Follow the GUI to install OMV. The order of each menu is as follows. For what they look like it is recommended to read the latest [OMV installation instructions](https://docs.openmediavault.org/en/stable/installation/via_iso.html) in their documentation.

1) System Language Selection.

2) System Locale.

3) Keyboard Setup.

4) Host Name of system. Make it recognizable on the Network for later configuration (eg. HPZ240NAS).

5) Domain Name setup. Typically left as local for most users. Advance users can change this to something else.

6) Root Password setup. This password should be strong (see online about strong passwords) and kept in something like a password manager. [KeePassXC](https://keepassxc.org/) is a good option for this as it is self hostable and it what I personally use. There are many other options available and an investigation into the one best for you will be beneficial. Do not forget this password as you may have to login as root later on.

7) Select the boot drive. **be careful** selecting the incorrect drive will mean all data on that drive is wiped. It is recommended to select a SATA SSD or NVME as a boot drive due to their faster speeds. In my case I have an NVME which my system will boot from. If you cannot determine the correct drive to boot from, shut down the system, unplug all drives that are not boot drives and start the process over.

8) Package Manager configuration. It is best to choose a package manager archive geographically close to you. The deb.debian.org archive is normally a safe bet.

9) Proxy Config. For advanced users who use a proxy to access services on the public internet. Normally ok to leave it blank.

10) Reboot. Once the system starts to reboot. Remove the ISO USB to allow the system to boot in OMV.

## Basic System Configuration

### Network Setup

An IP Address is a numerical number that gives a network interface identification and location addressing for all computers in a network. This is how we can figure out how to connect to any computer in the world. In a typical home network, your router will assign an IP address randomly to any device that connects to it using DHCP. The IP address will change in set intervals if a device connects via DHCP. For our server we want it to keep the same IP address so that we always know where it is in the network and therefore gives us the ability to know how to connect to it. To do this we give our server a static IP Address.

To set a static IP address for this server there are two main ways:

1) Though the terminal/ command line and edit the network interface settings

2) Through the router setting an address reservation for the server.

I have opted to go through the router to set my static IP address. Therefore, the next section of this guide goes through that.

#### Router static IP Address setup

To set a static IP address for this server on your network through a router, you will first have to know how to connect to the router through the browser. Normally, a router is located at 192.168.0.1. However, this is not always the case depending on the network setup in your home. Some phones/ devices can detect the router of the network that they are connected to and allow the user to connect to it. This can help you to connect to the web User Interface (UI) of the router. There are also apps to help detect devices on the network. One of these apps is [Vernet](https://github.com/osociety/vernet). When you scan the network you are connected to, one of the devices will be called router/ gateway. The IP address of this device is what we want.

However you get the IP address of your router, type it into the address bar our your browser of choice. **make sure it is in the address bar and not the search bar**. A web UI should appear with a login prompt.

Login to your router using the password you previously set, the original admin password being user: admin, password: password, or the password possibly written on your router. If the password set is the default password set by your router manufacture, you should change it to a strong password and keep it in a password manager like [KeePassXC](https://keepassxc.org/). It must be noted there are many password managers out there and each choice is dependent on each individuals requirements.

It is best to read the router manual to help navigate your specific configuration. I will detail the process I took to set a static IP address for my server. Once login, find the DHCP page and open it. Find the list of connected clients. In the list of clients, you should see the name of your server. In the adjacent columns, there should be a column showing the MAC address of the client and the IP address of the server.

![](Initial_OMV_Install/DHCP_Client_List.png)

In the address reservation page, define the MAC address recorded from the previous client page of the server and assign it to an IP address of your choice (eg. 192.168.1.112). The IP address you assign could be the address it first got when connecting. I would recommend that approach. You can set another IP address if you desire but I leave that to advanced users. What ever Address you set remember it so you know what to connect to.

![](Initial_OMV_Install/Router_Address_Reservation_Page.png)

### Connecting for the first time

Now that you have selected the assigned IP address, go into your browser and enter it into the address bar **NOT THE SEARCH BAR**. You should enter a login screen.

![](Initial_OMV_Install/OMV_Login_Screen.png)

The default login is User Name: admin, password: openmediavault. Double check the default login is still this by reading the [OMV Documentation Pages](https://docs.openmediavault.org/). When first login, you will be presented with the dashboard page. Before making any edits, the admin login password should change. To do this enter the user settings page by hovering on the person image on the top right. Then click the Change Password option to set a strong password.

![](Initial_OMV_Install/First_Login_Dashboard_Page.png)

Once in the page, set a strong admin password for the web UI. Preferably use a password manager (eg. [KeePassXC](https://keepassxc.org/)) to generate and store this password. Once completed, you will be sent to the home page of the web UI.

It is likely you will have to complete some software updates on your server. Luckily, OMV has a update tool that you can use. To access this tool on the left hand side on the web UI, you will see three lines. Click on it and then click the system option.

![](Initial_OMV_Install/Navigation_Panel.png)

Once in that page you will see and Update Management Option. Open that page, then the Updates page option given. This page will allow you to update your server from the web UI.

![](Initial_OMV_Install/System_Page_Overview.png)

![](Initial_OMV_Install/Update_Page.png)

In the updates page you will see all the available updates for your server and on the left there will be a download button to install the latest updates. Click that button and confirm to install the updates.

![](Initial_OMV_Install/Avaliable_Updates_Page.png)

A pop up command line will appear showing the system logs taking place. Once the updates are complete, a close button will appear available.

![](Initial_OMV_Install/Pop_Up_Command_Line.png)

Sometimes when performing updates, a Pending configuration changes panel will appear at the top of the screen. I would recommend applying these changes after applying updates. Just click on the check mark to apply. A pop up will appear of the logs and once complete, a close button will become available.

![](Initial_OMV_Install/Pending_Confireration_Changes.png)

When performing updates to the server. It is best practice to reboot the server. Luckily, in the Web UI on the top right, there will be a power button option. when it drops down there will be a reboot option. Reboot the server.

### Dashboard

When logging in to the server web interface, you are first taken to the dashboard page. This page can be customized show the current state of the server. Some information you can include is system load, file system used and unused space, currently running services and many more. To edit the dashboard, click on the user settings button on the top right (icon of a person) and click on the dashboard option in the drop down. 

![](Initial_OMV_Install/First_Login_Dashboard_Page.png)

Once in the dashboard page, it will give you options to enable widgets that will detail various properties of the server. Enable the widgets that you want on your dashboard page.

![](Initial_OMV_Install/Dashboard_Wiget_Options.png)

Once all the relevant widgets have been chosen, click save at the bottom of the page. This will take you back to dashboard page and you will now see the widgets you choose to appear.

![](Initial_OMV_Install/Filled_Dashboard.png)

### System Settings Overview

This section briefly goes over the system settings options and defines what I personally changed or added for my use case.

---

### Network Settings Overview

This section briefly goes over the network settings options and what I personally changed or added for my use case.

---

### Drive Setup

Now that the server has the most basic setup completed, further drives can be mounted to the server. Mounting a drive allows the server to use the drive through the operating systems (Open Media Vault) file system to make files/ folders to store or run services.

In this guide, I have a single SATA Solid State Drive (SSD), which will be used for fast storage for the services run on the server. I also have 2, 8TB Hard Disk Drives (HDDs) which will be put in a [RAID](https://en.wikipedia.org/wiki/RAID) setup. For my use case, I will use a RAID 1 / mirrored setup. This means that the data written on both drives will be identical. This means that if 1 drive fails, the server can still run with no loss in data as the other HDD contained all the data on the failed drive. It would is best practice to replace a failed drive as soon as possible but having [redundancy](https://en.wikipedia.org/wiki/Redundancy_(engineering)) in the server helps to keep it running. For more information on this check out the [RAID Wikipedia](https://en.wikipedia.org/wiki/RAID) page.

The first step of mounting these drives is to enter the storage settings page. To do this, you must first click on the three lines on the top left on the login OMV web interface. Then in the Navigation pane that appears you must click the "Storage" option.

![](Initial_OMV_Install/Navigation_Panel.png)

Once you are in the 

![](Initial_OMV_Install/Storage_Web_Interface_Options.png)

#### Mounting Single Drive

---

#### Mounting RAID Array

---

##### Standard RAID Array

---

##### ZFS

---

### Services

---

### Users

---

### Diagnostics

---

# Remote Access Zero tier

---

# Back Up Setup

---

# First Services

---

# 

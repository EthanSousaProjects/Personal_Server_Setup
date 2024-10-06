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

_11/08/2024_

This section briefly goes over the system settings options and defines what I personally changed or added for my use case. When making changes, a Pending configuration changes panel will appear at the top of the screen. Just click on the check mark to apply.

![](Initial_OMV_Install/Pending_Confireration_Changes.png)

The options are as follows:

- Workbench
  
  - Web UI Port - Default of 80 I personally changed to 2000 as I will have nginx proxy manager set to port 80 in the future. If changing make sure to connect via <IP address>:<Port Number>
  
  - Auto Logout - Default of 5 minutes, changed to 15 mins.
  
  - Secure Connection Options - This is to setup a https connection instead of a http connection. Https is a secure encrypted connection to the server. I currently do not have this enabled as this server is on a network with only  my devices. I may set it up in the future.
    
    - SSL/TLS enabled - Enable http connection
      
      - Certificate - Choosing which generated certificate to use. Allows you to create new one as well.
      
      - Port - port off Web UI - Port connection option to https connection.
    
    - Force SSL/TLS - Make https only available connection option.

- Date & Time
  
  - Time zone - set your time zone. Should be setup from initial install.
  
  - Use NTP server - NTP(Network Time protocol) are servers that connect to you server to keep the server time accurate.
    
    - Time Servers - Comma separated list of time servers. Should have been setup on initial install. I use `pool.ntp.org` which should work everywhere. research servers to choose what is best for you.
    
    - Allowed Clients - Client IP address/ host names that can connect to NTP server. I leave mine blank.

- Notification
  
  - Settings
    - Enabled - Enable email notifications from server. I do not have them enabled.
      - SMTP server - Server address to use
      - SMTP port - port number of server
      - Encryption Mode - Options of `None, SSL/TLS, STARTTLS, Auto` you should have some encryption enabled to help protect data during transit on the internet.
      - Sender email - User Email notification emails will be sent from.
      - Authentication Required - Does the email account need to be logged into.
        - User name
        - Password
      - Recipient - Email that will receive notification email.
        - Primary email
        - Secondary Email
      - Test Button - To send a test email out.
  - Events - Options of things that will send notification emails out.
    - CPU usage
    - File systems
    - Load Average
    - Memory usage
    - Process monitoring
    - S.M.A.R.T.

- Power Management
  
  - Settings
    
    - Monitoring - Enable monitoring of the system to specify CPU status and select appropriate level. I have it enabled.
    
    - Power Button - Specify what your systems power button does. options are `Nothing, power Off, Standby`. I set to power off.
    
    - Standby mode - specifies what your system should do when put in standby mode. Options are `Nothing, power Off, Standby`. i put mine to power off.
  
  - Scheduled Tasks - In this section you can create scheduled power based tasks. You can create a `standby, shutdown or reboot` task. These tasks can be executed at:
    
    - A specific date time down to minutes, 
    
    - Every N minute, hour, day of month.
    
    - Or Hourly, Daily, Weekly, Monthly, Yearly
    
    - I personally have a reboot task that runs monthly. This normally means my server reboots every first day on the month.

- Monitoring
  
  - Enabled - Specifies if performance stats are collected and graphed. I enable it to see performance over time.

- Scheduled Tasks - Here you can create cron tasks on your server. These are custom commands to do what ever you want. These tasks can be executed at:
  
  - A specific date time down to minutes,
  
  - Every N minute, hour, day of month.
  
  - Hourly, Daily, Weekly, Monthly, Yearly.
  
  - Or at reboot.
  
  - I personally do not run any cron tasks.

- Certificates
  
  - SSH - In this page you can create or import SSH certificates. This is used to secure SSH access. I do not have any SSH certificates.
  
  - SSL - In this page you can create or import SSL certificates. This is used to secure an https connection. I do not have any SSL certificates.

- Update Management
  
  - Updates - In this page you can check for new updates and Install them.
  
  - Settings
    
    - Pre-release updates - I do not have this enabled.
    
    - Community-maintained updates - I do not have this enabled.

- Plugins - Here you can install plugins to add functionality to you server. Have a look through it to see if any are interesting to you. There are more plugins in OMV extras which must be installed separately.

#### Plugins OMV Extras

There are extra plugins for OMV which are called [OMV extras](https://wiki.omv-extras.org/doku.php?id=start). These extras expand the functionality of OMV. I personally use it and it is simple to install. The instructions to install this are detailed below:

1) Connect to the server via SSH (Secure Shell) on an account with root/ sudo access. A common method is to use [PuTTY](https://putty.org/). You can also use things like the terminal or command prompt (name depends on platform). i will be using the windows command prompt.
   
   1) To do this I open the command prompt in windows and use the command `SSH <root/user account>@<IP Address/ Host Name>`. 
   
   2) It will ask you about a key finger print if you have not connected before. Type yes then enter to approve the connection to the server.
   
   3) It will then ask you for the password to the account you want to login to. Type the password out, it will not appear, then hit enter. You are now connected via SSH to your server.

2) You can install OMV extra via the command on the [OMV extras](https://wiki.omv-extras.org/doku.php?id=start) website. When I installed OMV extras on 11/08/2024 the command was as follows: `wget -O - https://github.com/OpenMediaVault-Plugin-Developers/packages/raw/master/install | bash`. For a non root account make sure you have `sudo` at the start of the command. Once you hit enter the install should run.

3) It is good practice to reboot your server when performing an install like this. You can reboot it from the OMV web interface or, you can reboot from the command line through the command `reboot`. Remember to use `sudo` for non root accounts.

OMV extras is now installed. You can now install more plugins and install docker under the omv-extras page in the system settings panel.

### Network Settings Overview

_11/08/2024_

This section briefly goes over the network settings options and what I personally changed or added for my use case. The options are as follows:

- General
  
  - Hostname - Name given to the server, setup on initial install but changeable here.
  
  - Domain Name - If you have a public domain e.g. google you can use that here. I do not and this server is local only so I use local.

- Interfaces - Here you can create or identify different network interfaces and see all their info. You can also set things like static IP addresses here as well. I have not changed the default options of my system.

- Proxy - Here you can enable a http, https or ftp proxy. I do not have any proxy setup.

- Firewall
  
  - Rules - Here you can setup firewall rules on IPV4 and IPV6 network interfaces. I do not have any firewall rules setup.

### Drive Setup/ Storage Page

Now that the server has the most basic setup completed, further drives can be mounted to the server. Mounting a drive allows the server to use the drive through the operating systems (Open Media Vault) file system to make files/ folders to store or run services.

In this guide, I have a single SATA Solid State Drive (SSD), which will be used for fast storage for the services run on the server. I also have 2, 8TB Hard Disk Drives (HDDs) which will be put in a [RAID](https://en.wikipedia.org/wiki/RAID) setup. For my use case, I will use a RAID 1 / mirrored setup using [ZFS](https://openzfs.org/wiki/Main_Page). This means that the data written on both drives will be identical. This means that if 1 drive fails, the server can still run with no loss in data as the other HDD contained all the data on the failed drive. It would is best practice to replace a failed drive as soon as possible but having [redundancy](https://en.wikipedia.org/wiki/Redundancy_(engineering)) in the server helps to keep it running. For more information on this check out the [RAID Wikipedia](https://en.wikipedia.org/wiki/RAID) page.

The first step of mounting these drives is to enter the storage settings page. To do this, you must first click on the three lines on the top left on the login OMV web interface. Then in the Navigation pane that appears you must click the "Storage" option.

![](Initial_OMV_Install/Navigation_Panel.png)

Once you are in the storage panel you will see 4 options.

![](Initial_OMV_Install/Storage_Web_Interface_Options.png)

- Disks - Allows you to see and manage all the physically connected drives to the server normally through a SATA connection.
  
  - You can wipe the data off the drives from this page. It is best practice to wipe a drive before setting it up. Therefore you should wipe all drives that are not your boot drive. A quick wipe should suffice. A secure wipe should be used when the drive is no longer used and you maybe selling it. This makes it so that data cannot be recovered like it can on a quick wipe.
  
  - In this page you can also edit the drives power, acoustic, Spin down and write-cache settings.
    
    - As i want to reduce the power draw of my system for my 2 8Tb HDDs i enabled the `1 -Minimim power usage with standby (spindown)` option. This will spin the drives down reducing power consumption considerably. I will apply the `Minimum performance, minimum acoustic output` option for the acoustic management setting and set Spin down time to 20 minutes. I will be enabling write-cache to reduce writes to my drives.
    
    - For my NVME and SSD drives I will apply the option of `127 - Intermediate power usage with standby` for the power management setting. I will apply the `Maximum performance, maximum acoustic output` for acoustic option, spin down time set to 20 mins and enable write-cache.
    
    - Make sure to apply all these changes by clicking the tick on the yellow top banner.

- S.M.A.R.T.
  
  - Settings
    
    - Enabled - Enable the monitoring of the drives. This makes your server check the drives S.M.A.R.T. Data periodically for errors or warnings. I have this enabled to monitor my drive health on the dashboard.
    
    - Check interval - sets how often the drives health will be checked. In seconds. I set mine to 50 minutes which it 3000 seconds.
    
    - Power mode - This setting specifies when the drive should not have it's S.M.A.R.T. data checked. You can disable checking if the drive is in sleep, standby and idle. I choose the option `Standby` which does not check the drives data if the drive is in sleep and standby as I would like to keep my drives spun down if they are not in use.
    
    - Temperature monitoring
      
      - Difference - Report if temperature varies by more than the defined amount of degrees C. I disable this.
      
      - Maximum - Report if drive is greater than or equal to specified temperature. I set this to 60 degrees C as that is the environmental specifications of my drives.
  
  - Devices - Here you can see all the drive data and we can edit the drive and enable them for monitoring. I have enabled monitoring for all my drives and used the global settings for both temperature difference and maximum temperature.
  
  - Scheduled Tasks - Here we can create tasks to perform test on your drives. You can create: `Short self-test, Long self-test, Conveyance self-test, Offline immediate test`. You can set it to execute on certain hours, days in month, days of week or on certain months. I have 2 tests that are setup for all my drives.
    
    - The first is a weekly Short self-test performed on Sunday.
    
    - The second is a monthly Long self-test performed on the 2nd of every month.
    
    - These tests should help me monitor the heath of my drives and notice errors sooner.

#### Mounting File Systems

To use the extra drives on a server we must mount the drives to the server. You can mount the drives as single drives or as a RAID array.

##### Single Drive

I will be mounting my SSD as a single drive. This will be used for services that need fast read and/or write speeds. I will be using a EXT4 file system as it is a common file system used in Linux and it works well with small files which this drive is likely to hold in the future.

In the `File Systems` sub page of the storage page you can click on the plus button with a circle. Then click on the file system of choice (EXT4).

![](Initial_OMV_Install/Mounting_Single_File.png)

You will then be directed to a page where you can choose your drive. choose your drive of choice then click save. A pop up will appear of the drive mounting. Let it run, it may take a while depending on your drive size. Once completed click the close button.

You will now be in a mount page, select the drive you just setup and set a Usage warning Threshold. I set my Threshold to 85%. Make sure that a drive does not fill up 100% as if it does, you cannot write to the log file and you have to wipe the drive to work with it again.

Once you have clicked save and applied changes, you have mounted a drive to your server.

To use the drive in OMV you must create a Shared folder in the drive. To do this, go into the Shared Folders sub page in the Storage page and click on the circle with a plus in it. You will be taken to a create folder page.

![](Initial_OMV_Install/Share_Folders_Create.png)

In this page you can: name your folder, set which file system (drive in this case) it will be added to, the relative path of the folder on the drive and permissions of who can access the file.

For my SSD storage I will setup a folder named SSD_Storage with 2 folders in it named Remote_Content and Local_Only_Content. This will help me locate content from external machines and content that is only one the server. if you want to replicate this make sure your relative path is something along the lines of `<First Folder>/<Second Folder>/`, making sure a `/` is always at the end.

![](Initial_OMV_Install/SSD_Directory_Tree.png)

![](Initial_OMV_Install/SSD_Starting_Folders_Setup.png)

Once you have completed creating the shared folder, it can then be used on the server. It is best practice to create a new folder for each service so that you know exactly where all the data for that service is stored. 

##### RAID ZFS Array

_27/09/2024_

I will be mounting my 8TB hard drives in a mirrored ZFS array. To accomplish this  you first need to install the ZFS plugin for OMV. This can be found in the `System>Plugins` page.

![](Initial_OMV_Install/ZFS_Plugin.png)

Once installed, Navigate to the page `Storage>zfs>Pools` , here we will create our mirrored raid array. Click on the add pool button.

The options I will select in the create pool page are as follows:

- Name - `Mass_Storage`

- Pool type - `Mirror`

- Devices - I selected my 2 Hard drives which have an ID of `/dev/sda` and `/dev/sdb`.

All other settings I left as their default. I then clicked save. Once saved apply the changes through the prompt.

Now a ZFS mirrored array is mounted to the server. We can now make Shared folders that our services can use just like we did for the single SSD drive.

I will have a file system with the layout described in the image bellow to help organize my services so that it is easier to find via SSH. If you want to replicate this make sure your relative path is something along the lines of `<First Folder>/<Second Folder>/`, making sure a `/` is always at the end.

![](Initial_OMV_Install/HDD_Directory_Tree.png)

It is best practice to create a new folder for each service so that you know exactly where all the data for that service is stored. Once you have created all of your folders remember to apply all the changes.

### Services

_28/09/2024_

This page shows all the installed services that are running and allows you to change their settings. This is where the installed plugin settings will most likely be. For now I will leave it as it is until I am ready to setup my services.

### User Management

In this page we can setup new users on our server. The different page options are as follows:

- Settings - Allows you to enable the users home directory and specify a location.

- Users - This is where you can create, import, and edit users which can be used for certain tasks instead of using the admin user to run all tasks. When it is relevant I will create a new user.

- Groups - This is where you can create, Import and edit user groups. This is where you can group together users for specific tasks.

### Diagnostics

This page gives system information to understand why an issue maybe occurring on your server. There are many pages which are as follows:

- System Information - Describes Basic Information about your server.

- System Logs - View the system logs.

- Processes - Like Task manager for windows or top in the command line for Linux.

- Services - Status of services running.

- Report - Generate a report about your system.

- Performance statistics - Graphs to show how your system is performing hourly, daily, weekly, monthly and yearly.
  
  - CPU - CPU usage.
  
  - File System Usage - drive used and free storage.
  
  - Load Average - Power usage of system.
  
  - Memory Usage - Free and Used RAM.
  
  - Network Interface - Network Traffic.
  
  - Uptime - Server powered on Time.

# Docker Install

_06/10/2024_

Open media vault 7 has a built in [docker management interface](https://wiki.omv-extras.org/doku.php?id=omv7:docker_in_omv). I will personally be using this interface for my use case but you can use another management interface like [portainer](https://www.portainer.io/) which is another popular docker management system or just use the command line. Documentation on this docker plugin can be found [here](https://wiki.omv-extras.org/doku.php?id=omv7:docker_in_omv).

## Folder Setup

The first step in this process is to setup the folders that docker will install/ save to. This is to separate docker data from the operating system data. This helps with backups and helps maintain the life of the OS storage drive.

The following folders will be created:

- Compose_Files - Where the compose files created will be stored. This will be put on the SSD drive so that the OS has faster access to it compared to the HDD array.

- Container_Data - This folder will be where persistent container data will be held. As I want this to be accessed quickly I will keep this on the SSD drive. 

- Docker_Backup - OMV docker management plugin has the option to automatically backup container data. I will use this feature and will make this folder on the HDD array as i do not need fast access to that data.

- Docker_Storage - This folder will be the files that docker uses internally. I will run this on the SSD for fast file access. Note that the Absolute Path is required instead of clicking on the respective folder from a drop down like the rest of the folders.

On the SSD, I will organize the folders like the diagram bellow under the Local_Only_Content folder.

![](Docker_Install/SSD_Based_Folder_Structure.png)

On the HDD array, I will just place the Docker_Backup folder under the `Local_Only_Content` folder on the HDD array.

 ![](Docker_Install/HDD_Based_Folder_Structure.png)

For all the folders created I will have the permissions set to `Administrator: read/write, Users: read/write, Others: no access`. Remember to change the relative folder path and to apply the changes when completed.

![](Docker_Install/Example_Folder_Setup.png)

## Installing Docker

Now that the folders have been setup, it is time to install docker onto the server. navigate to `System > omv-extras`. One this page tick the docker repo check box and hit save.

![](Docker_Install/Docker_Install.png)

Now navigate to `System >Plugins` to install the compose plugin.

![](Docker_Install/Compose_Install.png)

Now we can setup the install folders we set up earlier. Navigate to `Services > Compose > Settings`. Fill in the respective folders setup previously in the relevant areas. I left the compose files to be owned and edited only by the root/admin user so that only the admin account can access those files. Remember that the Docker storage path needs to be the absolute one so go into `Storage > Shared Folders` to find what it is. Once you are happy with your selections click save at the bottom, then hit apply changes on the yellow bar.

![](Docker_Install/Compose_Settings_Pt1.png)

![](Docker_Install/Compose_Settings_Pt2.png)

![](Docker_Install/Compose_Settings_Pt3.png)

Once you have applied the changes you will see under docker that it is installed and running. It will also show your docker and docker compose versions.

![](Docker_Install/Docker_Installed_Running.png)

## Creating a Docker User

I will create a docker user that will be used for only docker containers. In docker there is an important property called [PUID and PGID](https://docs.linuxserver.io/general/understanding-puid-and-pgid/#why-use-these) which is important for access to the file system, process management, etc. As I currently only have 1 user created, that being root, I do not want each of my docker containers to have full access to my system through the root account. I also do not want any folders or files created by the docker containers to be only owned by root. Therefore, by making a new user, I can access files and folders without having to login to root and it will also help reduce the risk of a container compromising my server.

To create a new user, login to the OMV web interface and navigate to `User Management > Users`. In this page click the plus button in the blue circle to create a new user.

I will give the user the following properties:

- Name - Name given to the user. I have used `DockerUser`

- Password - The password to the user account. Make sure this is a strong password and it is saved securely in something like a password manager.

- Confirm password - confirming password put in.

- Groups - This is the group permissions that the user has. By default the `users` group is added and it cannot be removed. For my use case I do no need it to have any other group permissions. Depending on your use case you may have to add the user to other groups depending on the docker containers you will run.

- Disallow account modification - I have enabled this to avoid the user being able to modify its account helping with security.

I am creating only 1 user as my system will only be available on my local network and my zero tier network which is only used by myself. If you are using this on a more public network I would recommend reading the [OMV docker documentation](https://wiki.omv-extras.org/doku.php?id=omv7:docker_in_omv) to understand how to make a more secure user for each container.

![](Docker_Install/User_Creation.png)

Once all relevant fields have been filled, save and apply the changes.

I want to restrict this docker users folder access so that it cannot modify the backup snapshots that I have created. To do this click on the docker user and click the folder with the key on it at the top.

![](Docker_Install/User_Folder_Permissions_Pt1.png)

You will be taken to a page where you can see all the shared folders created and change it's permissions. By default, the user can read and write to all the folders so I will take the Backup Folders and Set them to read only in case I use a docker container to sync the data. I also Set the compose files folder to read only so that i know they can only be edited by an admin.

![](Docker_Install/User_Folder_Permissions_Pt2.png)

Once folder permissions have been adjusted, save and apply the changes.

Now back at the users page make sure that you can see the UID and GID columns. Take note of the numbers for when you need them in docker.

![](Docker_Install/User_UID_GID.png)

## Overview of Plugin

Docker has now been fully setup on the server. Navigating to `Services>Compose` you can manage docker.

- Settings - Described in the install process above. Allows choosing where different docker elements will be installed/ saved.

- Files - Allows management of docker compose files. Create, Edit and delete compose files. Build from template. Check, Up, Stop, Down, Pull, Ps. Global environment variables, Prune, Tools, docs.

- Services - See what is running, monitor logs, restart, download logs.

- Stats - Like [top](https://www.geeksforgeeks.org/top-command-in-linux-with-examples/) but for all containers that are currently running. Name, CPU, Memory, Net, etc.

- Images - Manage docker images. Delete, Inspect, tag, push.

- Networks - Manage docker networks. Add, Delete, Inspect.

- Volumes - Manage docker volumes. Delete, inspect.

- Containers - See status of currently running containers. Name, Image, State, Status, Ports, Mounts. Restart, logs.

- Dockerfiles - Create and manage docker files. Create, edit, delete. Build, pull and build, tag.

- Schedule - Schedule backups, updates and prune to the docker containers that are installed on the system. Can be set on an automatic schedule.

- Restore - Restore a container from a backup.

# Data Backup

_28/09/2024_

One of the first services I will setup is making sure that the data on my server is backed up. Backing up data is very important incase of server failure or corruption where data on the primary drives has been lost. This allows us to still have access to the data through our backups.

The recommended strategy for building a backup system is to take into account the [3-2-1 backup rule](https://www.seagate.com/gb/en/blog/what-is-a-3-2-1-backup-strategy/). This rule states there must be:

- 3 copies of your data

- 2 Different mediums

- 1 remote/ offsite backup.

The 1 remote/ offsite backup is one many people forget to implement. It is normally only used in disaster scenarios like the building your main and local backup servers are in have burned down. The remote backup will save you here. Your remote backup can be something like a cloud storage service or your own server at a friends house.

For 2 different mediums this can be something like SSDs vs HDDs or even tape storage but, the way I interpret it is to use 2 systems that do not share any components. So I use another completely separate server with a different power supply, motherboard, drives, etc. This is so that if my main server fails. My other server will have all the data backed up onto it.

For 3 different copies if you accomplish the 1 remote and 2 different mediums you should have 3 copies: one on the server running services, one on the local backup server and one remote copy.

## My server backup Plan

My Backup plan for my server involves creating snapshots of all my data on the local server and then syncing these files to my local backup server and my remote one.

I will use the [openmediavault-backup plugin](https://github.com/OpenMediaVault-Plugin-Developers/openmediavault-backup) to backup the Open Media Vault system it's self. I will then backup the data drives using the  [rsnapshot plugin](https://github.com/OpenMediaVault-Plugin-Developers/openmediavault-rsnapshot). All data snapshots will be saved to the 2, 8TB HDD mirrored array in a common folder which is then synced to both the local backup server and the remote backup server through likely something like [Syncthing](https://syncthing.net/). A diagram of how my server backup plan will work is shown bellow.

![](Data_Backup/Server_Backup_Plan_Chart.png)

### Main Server OMV System Backup

To backup my OMV system data, I will first install the [openmediavault-backup plugin](https://github.com/OpenMediaVault-Plugin-Developers/openmediavault-backup) through the OMV plugin section.

![](Data_Backup/OMV_System_Backup_Plugin.png)

Once installed, you can find the backup settings in `System > Backup` page. Here we can adjust the settings for the OMV system backup. Firstly, I will make a new shared folder in the main HDD_Storage space to locate the backed up disc images.

![](Data_Backup/OMV_System_Backup_Shared_Folder.png)

Remember to apply the pending configuration change.

Once that change has been made I will go back to the `System > Backup` page will apply the following settings:

- Settings - Page to adjust OMV system backup settings
  
  - Shared Folder - Target for where backups will be saved.
  
  - Method - I will be using `dd full disk` as it will clone my entire boot drive to a compressed image file which can be used if the boot drive fails. There are other options available like dd, fsachiver, borgbackup and rsync which maybe better for your use case.
  
  - Root device - I will not change this setting
  
  - Keep - Describes the number backup image snapshots it should keep. I will have 4 for my use case.

- Scheduled Backup - Page to adjust when backups will run.
  
  - I will enable Scheduled Backups to run Weekly.
  
  - Remember to apply the pending change.

### SSD and HDD storage Snapshots

I will be creating snapshots of all my data which will then be syncing to my local and remote backup servers. These snapshots will be of the SSD and HDD storage areas and stored on the HDDs. I will first install the rsnapshot plugin.

![](Data_Backup/Rsnapshot_Plugin.png)

Once Installed I will navigate to the page `Services > Rsnapshot` to add rsnapshot jobs.

The first job will be for anything stored on the HDDs. The data on the HDDs will be more longer term files compared to the SSD based files. Therefore, I will keep 5 monthly snapshots which will translate to approximately one snapshot a week. All other settings I left as their defaults or set to zero.

![](Data_Backup/HDD_Snapshot_Settings.png)

Next I will create a snapshot job for the SSD data. As this data on the SSDs will update frequently in comparison to the HDDs, I will run 4 weekly snapshots and 3 monthly snapshots. This should give me a snapshot of the data nearly every other day. I will also gain some slightly longer term snapshots just incase i make a mistake in a file and the weekly snapshot gets over written before it is recovered. All other settings I left as their defaults or set to zero.

![](Data_Backup/SSD_Snapshot_Settings.png)

Remember to apply the configuration changes once the jobs have been created.

### Local and Remote snapshot copies

# Remote Access Zero tier

To do this likely have to install curl `sudo apt install curl`.

--- 

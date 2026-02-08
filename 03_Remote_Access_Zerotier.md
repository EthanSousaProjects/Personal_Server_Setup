# Remote Access Zero tier

TODO: Fix linting/formatting in doc 01

TODO: Fix spelling and add to dictionary relevant data in doc 01

_12/10/2024_

For remote access to my server, I will be using [ZeroTier](https://www.zerotier.com/). ZeroTier is a Virtual Private Network which allows direct connection to any devices you make part of it's network. This is just a simpler VPN solution where you do not have to have any of your own servers publicly accessible to the internet. ZeroTier offer a free tier which can be used by hobbyist and a self hostable option but i find the service amazing and recommend paying for their online service so you have none of your services publicly accessible to the wider internet.

## Account Setup

The first thing to do is to setup an account on the [ZeroTier central webpage](https://my.zerotier.com/). What ever password you use make sure it is strong and stored in something like a password manager. You could also sign in with your google, Microsoft or GitHub account, i do not recommend signing in through on of those accounts to distance yourself from data collecting companies and make it harder for a hacker to gain access to all your accounts.

Once your account is made, login to ZeroTier central. You will see all of your networks. You should have none so click on the create a network button.

![](Remote_Access_Zerotier/Create_Network.png)

You should now be able to name your network, see your network ID and all connected devices. You can also see all of your settings for that specific network. I leave most of them the same but advanced users may want to adjust some of them. I only add a description to my network and I set access control to `Private` so that an admin must approve you joining the network to access everything. This just makes the network more secure. Have a look at their [Documentation](https://docs.zerotier.com/central/) for further information.

You will need your network ID later on when ZeroTier is installed on your server and any devices you want to connect (eg. phone, windows, mac, Linux, etc).

For more information and configuration options of the ZeroTier network have a read of their [documentation](https://docs.zerotier.com/).

## Connecting to ZeroTier Network

### Server (Linux Debian)

Now to install onto the server, connect to a sudo/ root enabled account through SSH on the server. I recommend using something like [Putty](https://putty.org/) to use SSH but I will use the windows command prompt. Use the command `ssh <user account>@<IP address/ host name of server>` then insert your password for the account being used.

Once you are logged into your server, run `sudo apt update` and then `sudo apt upgrade` if you have packages to upgrade. Remember to run `sudo restart` if you upgraded a package. You may no have to but, `curl` is required to install zero tier. To make sure that it is on your system, run the command `sudo apt install curl`. Restart if it does install.

Looking at the [ZeroTier download page](https://www.zerotier.com/download/) we can run the command `curl -s https://install.zerotier.com | sudo bash` to install ZeroTier onto our system. Please double check the command when you install as it may have changed since this doc was written. If you have GPG installed as well you can use a more secure command found on the [download page](https://www.zerotier.com/download/). Make sure to restart your server after the install.

Now that ZeroTier is installed we can connect to our network with the following command `zerotier-cli join <Network ID>`.  Run the command `zerotier-cli info`. You will get a result similar to: `200 info <Device ID> 1.10.2 Status` This will tell you at least your Device ID. This will be important in a little bit. Running the command `zerotier-cli listnetworks`, we can see all the networks we are trying to connect to. we may see an `Access Denied` message besides the network we entered above. This will be because we set the Access control in the web interface to private. We have to enter the web panel and approve our server.

![](Remote_Access_Zerotier/Zero_Tier_Approve_Device.png)

Thank you to the zero tier page for this picture instead of using a picture of my own network. We cab edit/ view a few of the device properties in this interface like Name, description, last seen, etc.

We have now officially added a device/ our server to our ZeroTier Network. Add all the devices you want.

### Windows/ Mac Install

One of the devices I will connect to my ZeroTier network is a windows and MAC computer. The process to connect these devices are very similar. To connect it to our network we can download the installer from the [ZeroTier download page](https://www.zerotier.com/download/) or we could use winget for windows and Homebrew for MAC.

- MAC Homebrew command = `brew install --cask zerotier-one`

- Windows winget command = `winget install --id ZeroTier.ZeroTierOne`

Once installed we should see a ZeroTier logo at the top of the screen for mac and on the bottom right for windows. Click that button to open it's settings.

![](Remote_Access_Zerotier/Windows_MAC_Zerotier_One.png)

You should be able to see a button to ask you to join a new network. Click that button and enter your network ID from your account setup. Thank you to ZeroTier for their screen shots.

![](Remote_Access_Zerotier/Join_New_Network.png)

![](Remote_Access_Zerotier/Enter_Network_ID.png)

once you have entered and joined the network you may not be connected to it yet, you must approve the device on the web interface if you set the Access control to Private. This is shown in the previous Linux install section.

### Android & iPhone

To Install ZeroTier download ZeroTier-one from either the [Google Play Store](https://play.google.com/store/apps/details?id=com.zerotier.one&hl=en_US) or the [App Store](https://apps.apple.com/us/app/zerotier-one/id1084101492). Once installed, open the app and click the add network button.

Enter the network ID in the setup menu and save it. When ready, activate the connection to connect the the ZeroTier network. Just like the windows/MAC/Linux setups you may have to approve the connection.

Note that ZeroTier acts like a VPN on mobile platforms so if you want to use another VPN you must disable ZeroTier to use it. I tend to only connect to to ZeroTier on mobile if i need to and tend not to keep it active.

# Personal Server Setup

Date Written: 19/04/2026 TODO: Update

TODO: fix all of the md issues and the organization of the repo

## License

There are two licenses for this project. All code falls under the AGPLV3 license and all documentation/ write up falls under CC-BY-SA license.

I have no affiliation with any project mentioned in these write ups. I just like using the software.

## Who is this guide for

This guide is the path I took to download and install services on my personal Network Attached Server (NAS). This guide is written from the perspective of someone who is somewhat tech literate with Linux and windows but by no means an expert. I am attempting to write these guides so that anyone somewhat tech literate can follow them. I acknowledge that not everyone will be able to follow these guides that i create due to a vast diversity of IT knowledge.

---

TODO: Write on how the repo is organized and link to the other md files with there titles for easy navigation. Slight explaination of each chapter and why it has been made.

TODO: Write on how VS codium is setup for this project.

TODO: Write on the extensions used to manage this repo through vs code/ codium.

TODO: Fix the linting/ formating of all my docs

TODO: Fix all the spelling in my docs and add the dictionary to the workspace so that they can carry over to git.

TODO: Discuss spelling thing

## The Server

In these guides I am using a HP Z240 server with

- E3-1245 V5 CPU

- 16 GB DDR4 2133 MHz RAM

- 1, 256 GB m.2 NVME SSD

- 1, 512 GB SATA SSD

- 2, 8TB SATA HDDs

## Guide Chapters

This guide is organized into markdown chapters. Each chapter guides a part of the setup process for that specific service. Having these chapters breaks down each step of the process into manageable chunks. The following list are the chapters in this guide (with links) with a brief description of what it is about.

1) [Installing OMV](01_Installing_OMV.md) - Installing the OS onto the HP Z240 SFF with Open Media Vault (OMV) with initial setup.
2) [Installing Docker](02_Docker_Install.md) - Installing the container engine called Docker to install more services later.
3) [Installing Zerotier for Remote Access](03_Remote_Access_Zerotier.md) - Installing Zerotier so that the server can be accessed from anywhere securely.
4) [Creating a Dashboard through Heimdall](04_Dashboard_Heimdall.md) - Installing Heimdall to create a services dashboard for the server.
5) [Server Backup strategy and configuration](05_Server_Backup.md) - The backup strategy for the server and how it is setup.
6) [Installing Urbackup for Windows and Linux Backups](06_Windows_and_Linux_Backups_With_UrBackup.md) - Installing Urbackup to configure backups (files and images) of external windows and Linux clients.
7) [Mac backups using Time Machine](07_Time_Machine_Mac_Backups.md) - Setting up a share to be used by time machine to backup an external Mac.
8) [File Browser](08_File_Browser.md) - Setting up File browser to navigate and view files in a web browser.
9) [DNS and adblocking with Pihole and Unbound](09_DNS_Adblocking_Pihole_With_Unbound.md) - Setting up Pihole with Unbound as a network DNS server for custom DNS entries and blocking of ads/tracking/malware sites.
10) [Network Speed test to server](10_To_Server_Speed_Test_Librespeed.md) - Setting up libre speed test to conduct internet speed tests to the server for testing.
11) [File synchronization between devices and server using Syncthing](11_Syncing_Files_Syncthing.md) - Setting up Syncthing to sync files between personal PCs and phone to the server to act as a backup like target/ remote access option.
12) [Web photo collection via Photoprism](12_Web_Photo_Collection_Photoprism.md) - Setting up photo prism to view pictures through a web based interface with automatic categorization.
13) [Personal Finance tracking with Firefly III]() - Setting up Firefly III to Keep track of personal finances. TODO: Write up and install this service.

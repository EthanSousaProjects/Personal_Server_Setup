# My server backup Plan

TODO: Rewrite the plan as it has changed drastically since this was made. Also, mention the backup server repo as part of this.

TODO: Fix linting/formatting in doc 05

Date Written: 31/05/2026
TODO: Update date

This guide outlines how my server is backed up. The content included in these backups is everything that is on the NAS which includes:

- OS system drive
- Docker container configurations
- Container content (data and databases)
- External data synced to the NAS

This guide does not go over the configuration or the external machines that data gets synced to. My [local backup server config](https://github.com/EthanSousaProjects/Backup_Server_Setup) can be found which goes over this configuration.

## My robust backup strategy

When you are trying to make robust backups of a sever especially a self hosted NAS, you must create and follow a strong backup strategy. This sub section goes over that strategy which will be applied to all the data.

### 3-2-1 backup rule

The most important aspect of your strategy is to follow the [3-2-1 backup rule](https://www.backblaze.com/blog/the-3-2-1-backup-strategy/). This rule is the defacto standard for robust backups. The basic principle is that to have robust backups you must have:

- __3__ copies of your data on at least
- __2__ different mediums with at least
- __1__ off site

In terms of mediums, traditionally this meant different storage formats. For example HDDs and Tape drives. In modern times, this means 2 different computers. This is so that if one computer fails, your data is safe on the other computer. The other important aspect is the 1 off site. This is so that if something like a fire happens in your local home lab, your data is safe offsite.

For my setup, I will be using 2 more minimal servers with HDDs. One will be on the local network while the other will be in another home. I will connect to the remote one via [Zerotier](https://www.zerotier.com/). This will be done via [Rsync](https://docs.openmediavault.org/en/latest/administration/services/rsync.html). This NAS is available 24/7 therefore, the other servers will pull data from this NAS to allow them to shutdown when not pulling data. This saves on power draw reducing my home lab power draw.

Most people use some cloud service as their remote backup target as they are unable to backup content to a server in another home either due to how much data they have to backup or not knowing anyone that is willing to host a server for them. I personally wanted to control all aspects of my home lab as I do not trust uploading data to a cloud service. I also believe it goes against self hosting your data in the first instance. This is why I took my approach. I do however understand that it is not always feasible to take my approach which is while this was mentioned.

### Snapshots

Sometimes, data gets deleted by mistake or corrupted without getting noticed for some time. Therefore, to have a robust backup strategy, you must have snapshots. Snapshots show you the data being backed up at a certain point in time.

I will be using mostly [rsnapshot](https://github.com/OpenMediaVault-Plugin-Developers/openmediavault-rsnapshot) for my snapshotting tool due to it's simplicity and ease of use. Rsnapshot configuration and setup will not be done in this guide. It can however be found in my [local backup server config](https://github.com/EthanSousaProjects/Backup_Server_Setup) guide.

Some other tools within OMV contain snapshotting tools which will be used in addition to [rsnapshot](https://github.com/OpenMediaVault-Plugin-Developers/openmediavault-rsnapshot). They will be discussed in the relevant section.

As snapshots can be storage intensive, this will only be used for the most likely data to need it. Thinks like docker container databases and configuration data will be snapshotted while images and videos from phones will not be snapshotted. Every backup will be analyzed on a case by case basis.

### Backup frequency

The last major component to think about in a backup strategy is how frequent the backups need to occur. Some data, like docker databases, need to be synced frequently due to rapid changes. This is in contrast to things like image and video folders which do not change frequently.

From this, I have come up with three main categories of backups. These categories are as follows:

- __Biweekly__ - For backups of data that can change daily. All data within this category will be snapshotted.
- __Weekly__ - For backups of data that change frequently. Data within this category will not be snapshotted but may contain already snapshotted backups.
- __Bimonthly__ - For backups of data that rarely change. None of this data will be snapshotted due to the storage requirement to do so.

Each backup will first be defined by it's category on the external servers to easily identify what type of backup it should be and to keep alike backups organized. For completeness and reference, the bellow lists show the categories of backups and the folder path/s that sits under them. They will be updated as the NAS system and guides develop.

TODO: Fix this lists

- Biweekly
  - `/SSD_Storage/Remote` - to do ther
- Weekly
  - `the s` - theas
- Bimonthly
  - `thest` - thes

TODO: OS System Drive fix section fix and write up better.
TODO: Docker Container configurations and databases
TODO: External/ large data synced to NAS.

TODO: Fix spelling and add to dictionary relevant data in doc 05

## OS System Drive

I will use the [openmediavault-backup plugin](https://github.com/OpenMediaVault-Plugin-Developers/openmediavault-backup) to backup the Open Media Vault system it's self. I will then backup the data drives using the  [rsnapshot plugin](https://github.com/OpenMediaVault-Plugin-Developers/openmediavault-rsnapshot). All data snapshots will be saved to the 2, 8TB HDD mirrored array in a common folder which is then synced to both the local backup server and the remote backup server through likely something like [Syncthing](https://syncthing.net/). A diagram of how my server backup plan will work is shown bellow.

## Main Server OMV System Backup

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

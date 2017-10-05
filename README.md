# osmc-backup
Backup scripts for copying osmc installations to and from system partitions

The backup function within OSMC backs up only your libraries and Kodi user settings. A number of people have asked what is the best way to do a complete backup of OSMC. Here is a script which makes a backup of both partitions of a standard RPi SD card install. The backup device is assumed to be a local disc or a network share mounted in the /media/ directory and formatted as ext4. If backing up via nfs or smb (cifs) extended attributes are not copied but osmc does not need them. Each backup is dated by the name of the directory it is in – backups are assumed to be not more frequent than daily – and is a complete copy of the files on the two partitions, except those generated by the system which are not needed for a full restore. Hard links are used to minimise duplication of files. It can also make a separate copy of just the files that have changed or been added since the last backup.

As written, the script throws away backups after a certain time or so as to keep only the last n backups. You can ‘freeze’ a previous backup by renaming its directory from say 2017-09-30 to 2017-09-30beforeSeptupgrade. There is code to check how much disc space is being used and it could be modified so as to delete old backups when they are are taking up too much space on the backup media.

Some checks are included, for the existence of the backup media for example, but I can’t claim it’s bomb-proof. There are some progress messages which can be re-directed to a log file for unattended operation. The script does a search for the devices mounted at / and /boot so should work with a USB stick install.

As at version 0.1.9 of the backup script and 0.1.5 of the restore script, vero4k is supported. I don’t have any earlier veros but imagine the backup, at least, will work as is. There is also now a facility to shut down kodi, tvheadend and any other services (user specified) that may write to the filesystem while the backup is happening.

Everyone will have their own take on how and when to make backups. This script can keep daily copies of your SD card for ever which would be overkill but you can set up your own schedule. I keep backups for a week. The only dependency is rsync, which is in the repository, and for unattended operation cron, which is in the App Store. A suitable crontab line looks like:

15 2 * * * root /home/osmc/osmc-backup >> /home/osmc/backlog

# osmc-restore
The restore script is interactive and is invoked with:

osmc-restore [-f] path/to/backup/dir [destination]

You can specify the destination (eg sda) on the commandline or you will be prompted for it. The switch -f forces a format of the destination sd card. If there aren’t suitable partitions on the card, you will be prompted to format it. You can also run osmc-restore / to clone your working system to another card, which does not have to be the same size.

The restore for vero is currently a bit clunky - the script makes a tarball of the backup directory so it can be installed using a recent copy of a dtb.img and kernel.img from an installation image. These have to be copied to the sd card manually. There is no clone option for vero.

Comments and suggestions for improvement are welcome.

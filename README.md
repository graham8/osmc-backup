# osmc-backup
Backup scripts for copying osmc installations to and from system partitions

The backup function within OSMC backs up only your libraries and Kodi user settings. A number of people have asked what is the best way to do a complete backup of OSMC. Here is a script which makes a backup of both partitions of a standard RPi SD card install. The backup device is assumed to be a local disc or a network share mounted in the /media/ directory and formatted as ext4. If backing up via nfs or smb (cifs) extended attributes are not copied but osmc does not need them. Each backup is dated by the name of the directory it is in – backups are assumed to be not more frequent than daily – and is a complete copy of the files on the two partitions, except those generated by the system which are not needed for a full restore. Hard links are used to minimise duplication of files. It can also make a separate copy of just the files that have changed or been added since the last backup.

As written, the script throws away backups after a certain time or so as to keep only the last n backups. There is code to check how much disc space is being used and it could be modified so as to delete old backups when they are are taking up too much space on the backup media.

Some checks are included, for the existence of the backup media for example, but I can’t claim it’s bomb-proof. There are some progress messages which can be re-directed to a log file for unattended operation. The script does a search for the devices mounted at / and /boot so should work with a USB stick install. I don’t have a Vero but imagine it would not take much to tweak for that. I use a similar script for my linux laptop.

Everyone will have their own take on how and when to make backups. This script can keep daily copies of your SD card for ever which would be overkill but you can set up your own schedule. I keep backups for a week. The only dependency is rsync, which is in the repository, and for unattended operation cron, which is in the App Store. A suitable crontab line looks like:

	15 2 * * * root /home/osmc/osmc-backup >> /home/osmc/backlog

There is a small risk that files can arrive at the backup corrupted if they are written to by the system while being transfered by rsync, or files being copied in an inconsistent state. I have not noticed any problems running osmc from a restored SD card but you could add a wrapper script which stops mediacenter and any other things which might write to their data files (eg tvheadend), runs osmc-backup then restarts the services.

	systemctl stop mediacenter
	/path/to/osmc-backup >> /path/to/backlog
	systemctl start mediacenter

# osmc-restore
This script copies backed-up files back to an SD card, optionally formatting the card before the copy. It prompts for the device to write to and checks that it has the right partitions and enough space on each.

You can also just clone your working SD card to another card by specifying / as the source directory.

Comments and suggestions for improvement are welcome.

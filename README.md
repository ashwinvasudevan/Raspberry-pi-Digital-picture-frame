Raspberry PI Digital Picture Photo Frame
===================

Instructions for LCD mounting and connections can be found at [Instructables](http://www.instructables.com/id/How-to-Make-a-Raspberry-Pi-Media-Panel-fka-Digita/%29).  I also found this to be an  [interesting build](http://www.instructables.com/id/Ikea-Digital-VideoPicture-Frame-1/)

**lsyncd** is used for syncing the photos between your linux box and the PI. Alternatively you can also choose to download photos using the **Dropbox** or the **Google Photos API.**

![Raspberry PI digital picture photo frame](http://i.imgur.com/AZBah.jpg)

----------
Host Computer
-------------

Lsyncd is designed to be run using the root account. So first task is to login into the root account on your Ubuntu Desktop

```
su root
```
Install lsyncd and rsync using the following commands
```
apt-get update
apt-get install rsync
apt-get install lsyncd
```
Create a directory the lsyncd log files to be stored
```
sudo mkdir /var/log/lsyncd
touch /var/log/lsyncd/lsyncd.{log,status}
```
Let us edit the configuration file 
```
sudo vi /etc/lsyncd/lsyncd.conf.lua
```
Add the following to the file
Replace source and target directories based on your requirements.
```
settings = {
 logfile = "/var/log/lsyncd/lsyncd.log",
 statusFile = "/var/log/lsyncd/lsyncd.status"
}
sync{
 default.rsync,
 source="/home/username/Pictures",
 target="192.168.1.104:/home/pi/pictures",
 rsync={rsh ="/usr/bin/ssh -l root -i   /root/.ssh/id_rsa",}    
}
```

You need setup SSH keys to so that files are synced without a the requirement of a password. Check out the following guide for setting up [SSH keys](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)

Start lsyncd 
```
service lsyncd start
```
On the Raspberry pi
-------------

```
su root
apt-get install rsync
apt-get install fbi
```
Check if the files are synced using the ls command.
```
ls
```

> **Note:**

> - Make sure the directories do exist 
> - If there is any error, check the log files that you created before. 
> - tail -n5 /var/log/syslog is always your friend. 

If the files have synced then proceed with the following. 
```
git clone https://github.com/ashwinvasudevan/Raspberry-pi-Digital-picture-frame 

cd scripts
```
Edit crontab to schedule to restart the Pi and enable the slideshow
```
crontab -e
```
Add the following
```
 "0 0 * * * /home/pi/pictures/restart.sh"
```

To enable the slideshow at startup 
```
sudo vi /etc/rc.local

Before the line "exit 0"
3.1 bash /home/pi/pictures/slideshow.sh
```

```
To enable auto login as user Root
sudo vi /etc/inittab

Find the line 1:2345:respawn:/sbin/getty --noclear 38400 tty1

set to #1:2345:respawn:/sbin/getty --noclear 38400 tty1

Append after this line: 1:2345:respawn:/bin/login -f root tty1 
```

Enjoy!
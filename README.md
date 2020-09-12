# BananaPis
BananaPi Usage

The original BananaPi (now sometimes called BananaPi M1) 
is still suitable for many applications.

## Operating System
Armbian is in my experience the easiest to install and most
up-to-date and reliable operating system available for the BananaPi.

Armbian for the original BananaPi can be downloaded from 
https://www.armbian.com/bananapi/. I currently prefer the "Buster 
minimal" image (see bottom of that download page).

The default hostname of the OS when booting the bananapi is "bananapipro".
This is the name of a different board, also supported by Armbian.
But still the images are not the same: An Armbian image downloaded for
"bananapipro" will not boot on the "bananapi".

For first Login (Either ssh or console), username is "root"
and password is "1234". Before being dropped to a shell, one has
to change the root password and create a regular user interactively
as instructed.

## Music server in hifi rack
One of my BananaPi sits inside the case of an old, cheap DVD player.
The DVD player was broken and I have removed all of its parts from the
case and replaced them with

* Power supply providing 12V and 5V DC
* Bananapi running ARMBIAN
* 640 GB rotating SATA HDD for storing my music files
  * Last 10 GB are for the rootfs of armbian (because SD cards fail so often):
  * Create a 10 GB primary partition (gparted helps to shrink the existing partition to make room)
  * Invoke nand-sata-install to transfer rootfs from sd card to hard disk.
* LCD screen displaying composite video signal

`mkdir /sda1` # mount point for HDD partition containing music files

Edit `/etc/fstab`, add a line to mount `/dev/sda1` to mout point `/sda1`.
Make `/var/lib/mpd` a symlink to the directory beneath `/sda1` where mpd
should store its data, e.g.
```
mv /var/lib/mpd/ /var/lib/mpd.original
ln -s /sda1/var/lib/mpd/ /var/lib/mpd
``` 
Restart mpd. Add to rc.local:
```
# spin down harddisk after 5 minutes (60*5sec)
/sbin/hdparm -S 60 /dev/sda
# start mpd console client on LCD screen
/bin/openvt -s  -- /bin/su -c /usr/bin/ncmpcpp pi
exit 0

For enabling the hardware volume control on the BananaPi's built-in sound,
edit `/etc/mdp.conf`:
```
audio_output {
        type            "alsa"
        name            "BananaPi BuiltIn"
        device          "hw:0,0"
        mixer_type      "hardware"
        mixer_device    "default"
        mixer_control   "Power Amplifier"
}
```
nmtui helps to change host name and use static IP address.

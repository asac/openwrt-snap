# Building - snap

This snapcraft.yaml can consume prebuilt openwrt rootfs tarballs hosted at a remote location.

To build the snap just run:

```
snapcraft snap
```

# Building - OpenWRT ROOTFS

To produce the example rootfs tarball we used openwrt build system and selected the
OMAP target in menuconfig. Afterwards just build openwrt with make and you will
find the tarball we used in your bin/ directory.

For now you need to patch procd so openwrt init can coexist with udev/systemd on same host
and you have to make sure that the tarball has no dangling symlinks. For the purpose of
getting started you can find a ready to use the snappy-devonly-patches branch from github:
<https://github.com/asac/openwrt>

If you plan to use mac80211 wireless dongles, ensure you also enable all the wireless
drivers as modules in menuconfig as that will influence the features that hostapd 
and the management scripts will have in the rootfs.

We will work on integrating the openwrt build into the snapcraft.yaml so this
becomes even easier.

# Prebuilt snaps

If you don't want to build the snap by yourself you can find recent ones at
<http://bit.ly/1SuUn9h>. 

# Installing

To install you need to scp or wget the snap on your device and sideload it using
devmode until we have the interfaces and sandbox properly designed ...

```
snap install --devmode openwrt-devmode_1_armhf.snap
```

## Warning

The snap will hijack all your net interfaces. This means that if you use a LAN
connection to connect to device, that LAN connection will go down after
installing openwrt snap. Read the **Usage** section below to understand how to
connect to your device after installing this snap.

# Usage

## DHCP Server

After install your pi2/beaglebone has become a dhcp server. Simply connect your computer through its ethernet port and you will get a DHCP lease with IP.

## Luci / WebAdmin

Now you can go and use luci web admin <http://192.168.1.1/> and have fun...

![alt text](https://github.com/asac/openwrt-snap/raw/master/docs/openwrt-luci-snap.png "Luci on snappy Core")

Things to do here included setting your password, changing your dropbear ssh port to something != 22
which allows you to directly ssh into the openwrt snap and potentially enable your wifi interface.

## Wireless AP

The snap allows you to make a wireless AP out of your pi2 or beaglebone. For that to work you
need AP capable wifi dongle. The dongle we used for testing is <http://amzn.to/1JFAaHt> but
others should work too. Key is that your kernel has __PHY config options enabled so that your
radio shows up as "phy0" when running `iw list`.

With that go to Network-> WIFI in Luci (<https://192.168.1.1/cgi-bin/luci/admin/network/wireless>), edit
the config so it has the SSID and security you want and then "enable" the interface.

Shortly after you should see your AP on your wifi network and be able to connect to it.

![alt text](https://github.com/asac/openwrt-snap/raw/master/docs/openwrt-luci-wifi-working.png "Luci WIFI on snappy Core")

## SSH

You can ssh into the snappy core system as usual

```
# password: ubuntu
ssh 192.168.1.1 -lubuntu
ubuntu@openwrt's password: 
Welcome to Ubuntu Xenial Xerus (development branch) (GNU/Linux 4.3.0-1006-raspi2 armv7l)

 * Documentation:  https://help.ubuntu.com/
Welcome to snappy Ubuntu Core, a transactionally updated Ubuntu.

 * See https://ubuntu.com/snappy

It's a brave new world here in snappy Ubuntu Core! This machine
does not use apt-get or deb packages. Please see 'snappy --help'
for app installation and transactional updates.

Last login: Tue Apr 19 14:49:39 2016 from 192.168.1.141
ubuntu@localhost:~$
```

# chroot into openwrt environment

**BROKEN IN 16 DUE TO CONFINEMENT & LAUNCHER**

If you are logged in to your snappy Ubuntu Core system through ssh you can switch into the openwrt environment using the convenience `openwrt-devonly.sh` tool:

```
# need to be root; use sudo ...
ubuntu@localhost:~$ sudo openwrt-devonly.sh 


BusyBox v1.24.1 () built-in shell (ash)

/ # ps
  PID USER       VSZ STAT COMMAND
    1 root      1328 S    /sbin/procd
   37 root       968 S    /sbin/ubusd
   38 root       676 S    /sbin/askfirst /bin/ash --login
  290 root       964 S    /sbin/logd -S 16
  299 root      1244 S    /sbin/rpcd
  333 root      1428 S    /sbin/netifd
  354 root      1080 S    /usr/sbin/odhcpd
  396 root      1312 S    /usr/sbin/uhttpd -f -h /www -r OpenWrt -x /cgi-bin -
  448 root      1028 S    /usr/sbin/ntpd -n -S /usr/sbin/ntpd-hotplug -p 0.ope
  517 nobody     864 S    /usr/sbin/dnsmasq -C /var/etc/dnsmasq.conf -k -x /va
  610 root      1028 S    /bin/sh
  611 root      1028 R    ps
/ # 
```

# TODO
* upstream procd fixes
* add config option to use one host iface as wan bridge without managing it
* develop generic rootfs architecture target to openwrt and upstream
  * identify what a good generic openwrt config set would be (e.g. enable iw etc.)
* investigate how to closer integrate openwrt with a host ubuntu core snapd
* validate wifi feature with hostapd
* add support for generating default configs for wifi and lan usb adapters on first boot (or better hotplug, but hard)
* general definition of what configs should be exported to snappy land
* make x86/amd64/arm64 variants


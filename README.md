# Building - snap

This snapcraft.yaml can consume prebuilt openwrt rootfs tarballs hosted at a remote location.

To build the snap just run:

```
snapcraft snap
```

# Building - OpenWRT ROOTFS

To produce the example rootfs tarball we used openwrt build system and selected the OMAP target in menuconfig. Afterwards just build openwrt with make and you will
find the tarball we used in your bin/ directory.

For now you need to patch procd so openwrt init can coexist with udev/systemd on same host.

The patch for procd is: <https://git.io/vw3RD> and a recent openwrt trunk snapshot with this patch is <https://git.io/vw3RA>

# Installing

To play with it, you should sideload the snap:

 `snap install YOURLOCAL.snap`


# Using

After install your pi2 has become a dhcp server. Simply connect your computer to it with ether and you will get an IP.

Now you can go and use luci web admin <http://OpenWRT/> and have fun...

![alt text](https://github.com/asac/openwrt-snap/raw/master/openwrt-luci-snap.png "Luci on snappy Core")

# Attention

The snap will highjack all your net interfaces. This means that if you use a LAN connection to connect to device, that LAN connection will go down after installing openwrt snap. 

You will be able to connect to it by using it as your new dhcp server. Afterwards you can find your pi2 using the hostname "OpenWRT", e.g.

```
ssh ubuntu@OpenWRT
```

will work!

# TODO

* upstream procd
* add config option to use one host iface as wan bridge without managing it
* develop generic rootfs architecture target to openwrt and upstream
* investigate how to closer integrate openwrt with a host ubuntu core snapd


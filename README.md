# p2traveller - OpenWrt Travel Router

My adventures setting up a custom travel router with [OpenWrt](https://openwrt.org/). A collection of OpenWrt configuration files & gists.

My initial router is a [TP-Link](https://www.tp-link.com/au/) [TL-WR902AC v3](https://www.tp-link.com/au/home-networking/wifi-router/tl-wr902ac/), but that has proved insufficient for my demands.

I have a [GL-XE300 Puli](https://www.gl-inet.com/products/gl-xe300/) on order from AliExpress. The GL-XE300 Puli has OpenWrt as factory firmware. **DO NOT** buy direct from their website. You may be hit with hidden charges. Instead use their [AliExpress store](https://www.aliexpress.com/store/5100113) or a trusted reseller of your choice.

I will be adding two disks. The first will be a SD card formatted like...

partition | name / description | size
--------- | ------------------ | ----
`/dev/sda1` | ExtRoot / `/overlay` formatted `Ext4` | 256 MB
`/dev/sda2` | `swap` formatted as Linux swap. | 192 MB
`/dev/sda3` | Remaining capacity formatted `exFAT` that I use as `/webdev`.

If you're not using a `swap` volume, `/dev/sda2` will be the remaining space, & you won't have `/dev/sda3`.

The second will be a 5 TB hard drive for Time Machine backup.

## Major Objectives

1. Mobile hotspot - to get online wherever I happen to be.
2. China proof - unrestricted internet access in countries with heavy restrictions.
3. Time Machine - so I don't have to plug a drive into my Mac for backups.
4. LAMP stack - for my little bits of web development.
5. File server - as an aid to web development.

## Objectives/Wishlist Detail

1. one
2. two
3. three
4. four
5. five

## Steps Taken

1. [Flash OpenWrt to Router](#step_1)
2. [Initial Router Setup](#step_2)
3. [Some Preparation](#step_3)
4. [Set Up ExtRoot & Swap](#step_4)
5. [Set up Travelmate](#step_5)
6. [Install OpenVPN](#step_6)
7. [Step](#step_7)
8. [Step](#step_8)
9. [Step](#step_9)
10. [Step](#step_10)

4. Set up TravelMate - turns OpenWrt onto a travel router.

---

###<a id="step_1"></a> 1. Flash OpenWrt to Router

* [Instructions for TL-WR902AC](https://gist.github.com/p2made/c46602a4439f0263e3697e7f04a42e30).
* Not necessary on a router with OpenWrt pre-installed.

###<a id="step_2"></a> 2. Initial Router Setup

1. Go to `Luci > Network > Wireless` to configure WiFi.
2. Set up broadcast WiFi for both 2.4 GHz & 5 GHz.
3. Set up one connection, on 2.4 GHz or 5 GHz, to a local WiFi with public internet (will be deleted later).

###<a id="step_3"></a> 3. Some Preparation

These mount points aren't needed yet, making them now is least trouble. I need volumes mounted for Time Machine & web development.

```
mkdir -p /webdev
chmod 776 /webdev
touch /webdev/USB_DISK_NOT_PRESENT
chmod 444 /webdev/USB_DISK_NOT_PRESENT
mkdir -p /TimeMachine
chmod 776 /TimeMachine
touch /TimeMachine/USB_DISK_NOT_PRESENT
chmod 444 /TimeMachine/USB_DISK_NOT_PRESENT
```

###<a id="step_4"></a> 4. Set Up ExtRoot & Swap

* [Instructions](https://gist.github.com/p2made/4f38be302043d51fe2e08aa601a8d2e3).
* I'm installing enough that ExtRoot is essential.
* Swap might not necessary on a more capable router.

***Before*** rebooting...


```
block info
```

Output...

```
root@OpenWrt:~# block info
...
/dev/sda1: UUID="9b150345-5a3e-4164-b494-3e91dccfde8d" LABEL="overlay" VERSION="1.0" MOUNT="/mnt/sda1" TYPE="ext4"
/dev/sda2: UUID="0c01be5c-efb0-4032-9dba-87aada184546" LABEL="swap" VERSION="1" TYPE="swap"
/dev/sda3: UUID="1536-CA6E" LABEL="web_dev" TYPE="exfat"
```

Using those UUIDs, edit `fstab` to look like...

```
config global
	option anon_swap            '0'
	option auto_swap            '1'
	option anon_mount           '1'
	option auto_mount           '1'
	option delay_root           '5'
	option check_fs             '0'

config 'mount'
	option target               '/overlay'
	option uuid                 '9b150345-5a3e-4164-b494-3e91dccfde8d'
	option enabled              '1'

config 'swap'
	option uuid                 '0c01be5c-efb0-4032-9dba-87aada184546'
	option enabled              '1'

config 'mount'
	option target               '/webdev'
	option uuid                 '1536-CA6E'
	option enabled              '1'
```

* Keep a copy of this, because it seems to get overwritten readily.
* A section for Time Machine will be added later.

Upgrading installed packages messes up the target entries in `fstab`, so edit to fix or `scp` the correct file to OpenWrt, then remount.

```
block umount
block mount
```

Ignore the error on `/dev/sda3` for now.

###<a id="step_5"></a> 5. Set up Travelmate

Install Travelmate & a few extras...

```
opkg install dnsmasq iwinfo curl fdisk qrencode msmtp
opkg install -force-overwrite wpad
opkg install travelmate luci-app-travelmate
```
To complete Travelmate setup the local connection added in 3 of step 2 should be removed.

1. If logged into Luci, log out.
2. Log into Luci & there is now a `Services` menu with a single item, `Travelmate`.
3. Select that item, & click `Interface Wizard...`.
4. Add wireless stations in the `Wireless Stations` tab.

###<a id="step_6"></a> 6. Install OpenVPN

```
opkg update
opkg install -force-overwrite openvpn-openssl luci-app-openvpn
```

Log back into Luci & there will now be a menu `VPN > OpenVPN`.

#### ExpressVPN Setup

Log into your [ExpressVPN](https://expressvpn.com/) account & navigate to **Manual Configuration**.



\*<a id="ovpn_firewall"></a> Firewall...


###<a id="step_7"></a> 7.

###<a id="step_8"></a> 8.

###<a id="step_9"></a> 9.

###<a id="step_10"></a> 10.






## Gists

* [OpenVPN UCI Directives Cheat Sheet](https://gist.github.com/p2made/e4af00211cee21e27bd26eb5442a6168)


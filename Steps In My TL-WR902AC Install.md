# Steps In My Install 2021-05-27

## 1 -

Initial software additions & set up ExtRoot...

```
opkg update
opkg install block-mount curl e2fsprogs fdisk kmod-fs-ext4 kmod-usb-ohci kmod-usb-storage kmod-usb-uhci kmod-usb2 kmod-usb3 usbutils nano
ls -al /dev/sd*
block detect | uci import fstab
uci set fstab.@mount[0].enabled='1'
uci set fstab.@global[0].anon_mount='1'
uci commit fstab
/etc/init.d/fstab boot
mount /dev/sda1 /mnt ; tar -C /overlay -cvf - . | tar -C /mnt -xf - ; umount /mnt

block detect > /etc/config/fstab; \
   sed -i s/option$'\t'enabled$'\t'\'0\'/option$'\t'enabled$'\t'\'1\'/ /etc/config/fstab; \
   sed -i s#/mnt/sda1#/overlay# /etc/config/fstab; \
cat /etc/config/fstab
nano /etc/config/fstab
```

Now reboot...

```
reboot
```
## 2 -

Check...

```
grep -e /overlay /etc/mtab
df /overlay /
```
Desired output...

```
root@Qazaqstan:~# grep -e /overlay /etc/mtab
/dev/sda1 /overlay ext4 rw,relatime,data=ordered 0 0
overlayfs:/overlay / overlay rw,noatime,lowerdir=/,upperdir=/overlay/upper,workdir=/overlay/work 0 0

root@Qazaqstan:~# df /overlay /
Filesystem           1K-blocks      Used Available Use% Mounted on
/dev/sda1               225632      5520    202624   3% /overlay
overlayfs:/overlay      225632      5520    202624   3% /
```

Move `opkg-lists` to `/overlay`, & update opkg.

```
sed -i -e "/^lists_dir\s/s:/var/opkg-lists$:/usr/lib/opkg/lists:" /etc/opkg.conf
opkg update
```

Update installed packages.

```
opkg list-upgradable | cut -f 1 -d ' ' | xargs opkg upgrade
```

## 3 -

Install `Travelmate `.

```
opkg install travelmate luci-app-travelmate luci-i18n-travelmate-en
```

1. Log out & back in, then go to `Luci > Services > Travelmate`.
2. Click `Save & Apply`.
3. 



```
opkg install avahi-utils curl hfsfsck kmod-fs-exfat kmod-fs-hfs kmod-fs-hfsplus mkhfs netatalk sshfs shadow-groupadd shadow-groupmod shadow-useradd shadow-usermod
opkg install openvpn-openssl luci-app-openvpn luci-i18n-openvpn-en luci-mod-dashboard
opkg install samba4-admin samba4-client samba4-libs samba4-server samba4-utils luci-app-samba4 luci-i18n-samba4-en
opkg install libacl luci-app-acl luci-i18n-acl-en
opkg install luci-app-opkg luci-i18n-opkg-en
opkg install dnsmasq iwinfo curl wpad qrencode openvpn msmtp
opkg install p910nd luci-app-p910nd luci-i18n-p910nd-en
opkg install wireguard-tools kmod-wireguard vpn-policy-routing wg-installer-client luci-app-wireguard luci-i18n-wireguard-en luci-proto-wireguard
```

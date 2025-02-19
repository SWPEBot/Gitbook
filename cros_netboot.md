# Chrome OS Netboot

**Before 2018:**

_Python framework, Before 2018 Project Used._

Netboot firmware in your factory bundle netboot_firmware/image.net.bin

```bash
./factory_setup/update_firmware_settings.py \
--clobber \
--input netboot_firmware/image.net.bin \
--output netboot_firmware/image.net.bin.INPROGRESS \
--omahaserver=http://30.30.1.50:8080/update \
--tftpserverip= 30.30.1.50
```

**Now:**

Server Client:

1.Decide a folder to put TFTP files, for example, /var/tftp

```bash
sudo mkdir /var/tftp
sudo chown "${USER}" /var/tftp
mkdir -p "/var/tftp/chrome-bot/${BOARD}"
```

2.Create a dnsmasq setup config in TFTP root, for example,
/var/tftp/dnsmasq.conf, with the following contents:

```bash
interface=eth2
tftp-root=/var/tftp
enable-tftp
dhcp-leasefile=/tmp/dnsmasq.leases
dhcp-range=30.30.200.50,30.30.200.150,12h
port=6666
```

3.Assuming that you are running Ubuntu, you can setup static IP for eth2 by
adding a file /etc/network/interfaces.d/eth2.conf

```bash
auto eth2
allow-hotplug eth2
iface eth2 inet static
    address 30.30.200.1
    netmask 255.255.255.0
```

Reload the configuration by running:

`sudo ifup eth2`

5.Make sure you have dnsmasq installed on Linux machine (e.g. Ubuntu / Debian)

`sudo apt-get install dnsmasq`

6.In the tftp-root, create sub folder under chrome-bot using the board name.

For example, reef board should be /var/tftp/chrome-bot/reef/. Copy the netboot
kernel into tftp board folder with name vmlinuz. If you are setting up with a
factory zip, the netboot kernel is in path factory_shim/netboot/vmlinuz. So you
have to copy it into tftp board folder manually. For example:

`cp factory_shim/netboot/vmlinuz /var/tftp/chrome-bot/reef/vmlinuz`

7.If you are setting up with a factory bundle (prepared by finalize_bundle
command and is usually a tar.bz2 archive), the tftp folder is already prepared
in netboot/tftp. So you have to copy everything to your tftp root, or start
dnsmasq server from there. For example:

`cp -r netboot/tftp/* /var/tftp/`

8.The location of factory server can be specified in omahaserver\_\${BOARD}.conf
in to level of tftp, with its content set to what you'll set in CHROME_AUSERVER.
For example, in /var/tftp/omahaserver_reef.conf:

`http://192.168.200.1:8080/update`

9.Start DHCP & TFTP server

`sudo dnsmasq -d -C /var/tftp/dnsmasq.conf`

Netboot BIOS ip&port Setting method:

`./image_tool netboot --input ./image-board.net.bin --tftpserverip 30.30.1.62 --board octopus --factory-server-url 30.31.1.62:6666`

## Netboot bios download server Configure &netboot bios IP/Port setting method

Server side environment configuration:

1.Decide a folder to put TFTP files, for example, /var/tftp

```bash
sudo mkdir /var/tftp
sudo chown "${USER}" /var/tftp
mkdir -p "/var/tftp/chrome-bot/${BOARD}"
```

2.Create a dnsmasq setup config in TFTP root, for example,
/var/tftp/dnsmasq.conf, with the following contents:

```bash
interface=eth2
tftp-root=/var/tftp
enable-tftp
dhcp-leasefile=/tmp/dnsmasq.leases
dhcp-range=30.30.200.50,30.30.200.150,12h
port=6666
```

3.Assuming that you are running Ubuntu, you can setup static IP for eth2 by
adding a file /etc/network/interfaces.d/eth2.conf

```bash
auto eth2
allow-hotplug eth2
iface eth2 inet static
    address 30.30.200.1
    netmask 255.255.255.0
```

Reload the configuration by running:

`sudo ifup eth2`

5.Make sure you have dnsmasq installed on Linux machine (e.g. Ubuntu / Debian)

`sudo apt-get install dnsmasq`

6.In the tftp-root, create sub folder under chrome-bot using the board name.

For example, reef board should be /var/tftp/chrome-bot/reef/. Copy the netboot
kernel into tftp board folder with name vmlinuz. If you are setting up with a
factory zip, the netboot kernel is in path factory_shim/netboot/vmlinuz. So you
have to copy it into tftp board folder manually. For example:

`cp factory_shim/netboot/vmlinuz /var/tftp/chrome-bot/reef/vmlinuz`

7.If you are setting up with a factory bundle (prepared by finalize_bundle
command and is usually a tar.bz2 archive), the tftp folder is already prepared
in netboot/tftp. So you have to copy everything to your tftp root, or start
dnsmasq server from there. For example:

`cp -r netboot/tftp/* /var/tftp/`

8.The location of factory server can be specified in omahaserver\_\${BOARD}.conf
in to level of tftp, with its content set to what you'll set in CHROME_AUSERVER.
For example, in /var/tftp/omahaserver_reef.conf:

`http://192.168.200.1:8080/update`

9.Start DHCP & TFTP server

`sudo dnsmasq -d -C /var/tftp/dnsmasq.conf`

Netboot BIOS ip&port setting method:

`./image_tool netboot --input ./image-board.net.bin --tftpserverip 30.30.1.62 --board octopus --factory-server-url 30.31.1.62:6666`

## 更改 netboot firmware IP

_Python framework, Before 2018 Project Used._

Netboot firmware in your factory bundle netboot_firmware/image.net.bin

```bash
./factory_setup/update_firmware_settings.py \
--clobber \
--input netboot_firmware/image.net.bin \
--output netboot_firmware/image.net.bin.INPROGRESS \
--omahaserver=http://30.30.1.50:8080/update \
--tftpserverip= 30.30.1.50
```

# Chrome OS RMA Shim

## How to Install USB/RMA Image

1.Copy the RMA shim image to a USB stick.

2.Turn on developer mode on your Chromebook device and insert the RMA shim.

3.Invoke recovery mode and reboot. (Use Ctrl-U boot if you are using dev-key
signed RMA shim for test purpose)

4.You should see the system started with a text mode screen (same as the factory
installer) and then start copying things to SSD. You can remove the RMA shim
when the copy is complete (the text color changes from yellow to green).

5.The Chromebook will reboot when the installation is completed and then bring
up the factory test UI (or whatever you put in the --factory image).

6.Registration is applied from Google CPFE , and is distinguished by project
name

7.How to use RMA shim

8.Flash the RMA shim image to a USB stick (using cros flash or dd).

9.Turn on developer mode on your Chromebook device and insert the RMA shim.

10.Invoke recovery mode and reboot. (Use Ctrl-U boot if you are using dev-key
signed RMA shim for test purpose)

11.You should see the system started with a text mode screen, press I to
install.

12.The Chromebook will reboot when the installation is completed and then bring
up the factory test UI (or whatever you put in the --factory image).

## Make Python RMA Shim

1.Create the RMA Shim Image Under the chroot, in bundle/factory_setup folder:

```bash
$ ./make_factory_package.sh \
--usbimg output_file_name \
--install ../bundle/factory_shim/bin_file \
--factory ../bundle/factory_test/chromiumos_factory_image.bin \
--release ../bundle/release/bin_file \
--firmware_updater ../bundle/firmware/chromeos-firmwareupdate \
--hwid_updater ../bundle/hwid/sh_file
```

2.Install Insert USB stick and enter into DEV mode. Press Ctrl+U to boot from
USB, the installation can be started automatically.

## Factory image modified for RMA shim （Python)

For main.py:

1.Modified factory_environment = False

2.Modified enable_shop_floor = False

3.For test list: Remove Get_Device_info

4.Add below setting in HWID item Dargs={'generate' : True, 'skip_shpfloor':True,
'rma_mode':True}

## How to Build Json format RMA Shim

_After 2018 New Project._

### Overview

RMA stands for Return Merchandise Authorization. When there's a problem that the
end user cannot solve, the user returns the device to the partner's service
center for diagnosis and repair. The service center may swap components and
reinstall the firmware and/or software image. For Chromebooks, that means the
service center may need to disable write protection and change the HWID to match
the new configuration.

### What the RMA

Return Merchandise Authorization (RMA) is the process of having a Chrome device
repaired or replaced within the product's warranty period.

Reference link :
<https://www.google.com/chromeos/partner/fe/docs/rma/rmashiminage.html>

### What is the RMA Shim Image

Chromebooks are highly secured, with write protection enabled. It's difficult
for the service center to run diagnosis and repair programs (usually built and
customized by partners) because those won't be signed by Google. Service centers
may also have limited (or even no) network access. In general, what the partner
needs is a tool that fulfills these requirements:

* The tool is signed by Google. An operator can boot the device by turning on
  the developer mode, attaching a USB stick and invoking recovery mode.
* The tool can run the partner's customized tool programs to check and verify
  components, very similar to the way the factory process works.

The RMA shim image is designed to meet these requirements. An RMA shim image is
a combination of existing Chrome OS factory bundle components: a factory install
shim, factory test image, and release OS image (FSI), all combined into one disk
image. The service center can use the shim image to install everything without
access to a network.

### Creating the RMA Shim Image

1.Prepare PVT stage factory bundle for RMA shim build

Within 1 week of FCS, SW Team need release RMA shim, RMA shim need based on
final factory bundle and final HWID bundle to build.

2.Modify factory toolkit for RMA

Toolkit modification details：

(1)Unpack final toolkit for RMA build

(2)Active you RMA main test list

`echo main_rma >> /usr/local/factory/py/test/test_lists/ACTIVE`

(3)Create main_rma.test_list.json

If you want modification RMA FFT or RMA RunIn, please change label to Main Test
List for RMA FFT/ RMA RunIn, and created
main_rma_fft.test_list.json/main_rma_runin.test_list.json to add test item.

(4)Modify RMA engineer password

RMA shim engineer password is _cros_

```bash
`cd /usr/local/factory/py/test/test_lists/`

echo –n cros | sha1sum >> newpassword.log

vi main.test_lists.json to modify
```

(5)Modify main_rma.test_list.json

You need setting `enable_factory_server = false` and `phase PVT` for RMA shim,
and setting tests for your want import test_lists.

eg: FFT/RunIn, Need add definitions FATP on `main_fft/runin.test_list.json`
,Import you test item, and need mark or skip sync factory server under RMA FFT
RunIn.

(6)Packing you modification factory toolkit

When your test list modification done, please pack your RMA toolkit

e.g. :
`./usr/lcoa/factory/py/toolkit/installer.py --pack-into ./install_factory_toolkit_rma.run`

(7)Build RMA shim

Under chroot to make usbimg, please beforehand prepare you development
environment and factory bundle.

```shell
./setup/make_factory_package.sh \
--board coral \
--usbimg Example_RMA_20180101.bin \
--factory_shim ./factory_shim/chromeos_xxxx_factory_dev-channel_mp.bin \
--release_image release_image/chromeos_xxxx_recovery_dev-channel_premp.bin \
--test_image test_image/chromiumos_test_image.bin \
--toolkit toolkit/install_factory_toolkit.run \
--hwid ./hwid/hwid_v3_bundle_xxxx_2018_xx_xx_xx_xxx.sh
```
### Extracted toolkit from the RMA Shim Image
## 从RMA_SHIM_Image中提取Toolkit 的命令
```
~/trunk/src/platform/factory/setup/image_tool payload toolkit  -i image.bin --unpack ./repack
```

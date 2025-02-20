# Chrome OS Factory Bundle

Offical Document [Factory Bundle](https://chromium.googlesource.com/chromiumos/platform/factory/+/HEAD/setup/BUNDLE.md)

## Make Factory Image (Python List)

**Before 2018 project, used python framework, need make factory image.**

Patch factory toolkit into test image (under chroot)

1. Find the board name and build version under
   [CPFE](https://www.google.com/chromeos/partner/fe/)
2. Download FACTORY_IMAGE_ARCHIVE and unzip
3. Download TEST_IMAGE_ARCHIVE and put it under
   factory_test/chromiumos_test_image.bin
4. Install factory toolkit into test image

   `./factory_toolkit/install_factory_toolkit.run ./factory_test/chromiumos_test_image.bin --yes`

5. Rename test image to factory image

   `mv ./factory_test/chromiumos_test_image.bin ./factory_test/chromiumos_factory_image.bin`

## Clean current state and make master

**Before 2018.**

```shell
gooftool verify_keys
gooftool verify_rootfs
rm /var/log/eventlog.txt  && factory_restart -a && halt
```

## Debug make factory bundle show image type is usb（mini-omaha download error）

**Python Framework, make image type shoould be recovery.**

```bash
factory_setup/lib/cros_image_common.sh

grep -w “root=“ | grep -w “cros_recovery”)”   #Fail
grep -w “root” | grep -w “cros_recovery”)”    #Pass
```

## Python Architecture Make Pre Flash Fail Processing

repo sync the chroot environment is up to date

`factory_bundle/factory_setup/lib/chromeos-common.sh` replace
with`chromiumos/chroot/usr/share/misc/chromeos-common.sh`

## Force install test image to disk

`chromeos-install --dst /dev/mmcblk0`

## Only install shipping image

1. Press {Esc + Refresh + Power} to enter recovery mode then boot USB disk.
2. Will show chromeos missing screen or recovery mode.
3. insert usb disk , will auto install recovery image.

### Commit your changes to MANIFEST.yaml and README

Follow the steps below to create a factory bundle from scratch.

1. Download factory bundle from CPFE (e.g.
   ChromeOS-factory-R57-9940.0.0-coral.zip)
2. Copy MANIFEST.yaml and README from previous bundle, and remember to update
   necessary fields (e.g. bundle_name, test_image_version in MANIFEST.yaml and
   CHANGES in README).
3. Copy recovery image into release/recovery_image.bin
4. If you want to use a local built factory toolkit, copy your
   install_factory_toolkit.run into toolkit/.
5. Replace factory_shim/netboot/image.net.bin with a working one.
6. Execute command “shopfloor/factory.par finalize_bundle ./”

If only a few files need to be updated, you can start from your previous bundle,
update necessary files, and run finalize_bundle command.

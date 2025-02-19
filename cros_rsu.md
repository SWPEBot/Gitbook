# Chrome OS Google RSU

## RSU (RMA Server Unlock)

* The RMA Server Unlock (RSU) process covers the steps of disabling hardware write protect and enabling developer mode on devices being RMAed. This will allow you to perform RMA on devices without having to open them and even if the device is still enterprise enrolled with developer mode disabled. The other steps of the RMA process are not impacted.

> 注: 该文档 [Google Source](https://drive.google.com/file/d/15VpzuenKE_6ItTdRy-GvEvGRUf4zBjlm/view?usp=sharing)

### Requirements

* Updated RMA shim with RSU process included
* QR code scanner or QR code scanning app
* 具有 sudo 权限的用户帐户
* Secondary device with an internet connection and a Chrome web browser
* Whitelisted RSU account with associated security key (basic U2F device)
  * [What is U2F](https://developers.yubico.com/U2F/)

## Step-by-step on how to perform RSU

* When the device boots up, press ESC+F3(refresh)+Power to enter recovery mode (or Power+Vol Up+Vol Down for 10 secs on a tablet). There will be a few seconds of black screen before entering recovery mode. Sometimes the key combo is not detected and the device simply reboots into login screen. Try again if it happens
* Press Ctrl+D to access the OS verification screen (or Vol Up+Vol Down on a tablet, and then navigate with volume to confirm disabling OS verification)
* Press Enter to turn off OS verification

# RSU Process cont’d

* When the device reboots, you’ll see a “OS verification is off” screen. Press ESC+F3+Power to enter recovery mode again (or Power+Vol Up+Vol Down on a tablet)

* Plug in the USB shim

* If the device has a cr50 firmware version < 0.3.11, the RMA shim will automatically update the firmware and reboot (this takes about 1 minute). After reboot, manually enter the recovery screen by
pressing ESC+F3+Power (or Power+Vol Up+Vol Down for 10 secs on a tablet). Since the USB shim is still plugged in, it will automatically boot into RMA shim. If the latest cr50 firmware is already on the
device, this step will be skipped

* A QR code will appear on the device. This contains an URL to our RSU server to acquire an authentication code to unlock the device

* Open a Chrome browser window and scan the QR code in the address bar (use your scanning device or a QR code scanning app). The QR code web link will appear in the bar

* You will be invited to touch your security key. Make sure it’s inserted and tap it.
  * `Note: if you get an error, verify that yourregistered RSU account is being used in the top right corner, next to the sign out button`

* An 8-digit unlock code will appear

* Type in the 8-digit unlock code and press enter (if you’re using a tablet and has only one USB port, unplug the USB and plug in an external keyboard to enter the code)

* A message “RMA unlock succeeded” will appear and the device will reboot. Once the screen goes black, manually enter the recovery screen by pressing ESC+F3+Power (or Power+Vol Up+Vol Down on a tablet for 10 secs)

* The RMA shim will automatically install the payloads in the shim. It takes a few minutes

* Once all the tests are passed, the factory toolkit will wipe the device and shutdown. The RMA process is completed

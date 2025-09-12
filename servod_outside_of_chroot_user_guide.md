# Chroot 之外的 Servod  用法示例

## 获取最新 ChromiumOS source code

* Follow the [ChromiumOS Developer Guide](https://www.chromium.org/chromium-os/developer-library/guides/development/developer-guide/) and create a chroot.

## 安装Docker环境及依赖
```bash 
sudo apt-get update
sudo apt-get install docker.io
sudo apt-get install python3-docker
sudo apt-get install minicom tmux tree

```

## 设置docker和tty的用户权限
```bash
sudo usermod -aG tty $USER && sudo usermod -aG docker $USER

```
## 设置你的环境变量bash or zsh 
```bash 
#设置bash

grep "src/third_party/hdctools/scripts" ~/.bashrc || echo "export PATH=~/chromiumos/src/third_party/hdctools/scripts:\$PATH" >> ~/.bashrc

#设置zsh 
grep "src/third_party/hdctools/scripts" ~/.zshrc || echo "export PATH=~/chromiumos/src/third_party/hdctools/scripts:\$PATH" >> ~/.zshrc

#生效变量

source ~/.bashrc  or source ~/.zshrc

```

## Start Servod 连接9999 
```bash
start-servod --channel=latest --board=<board name>

# --channel 这是一个Docker映像，当新CL降落在hdctools的主要分支中时，它会重建大约有20分钟内容接近TOT

Example:start-servod --allow_offine --channel=latest --board=<board name> -n brox_servo 

# 允许离线

```

## Check servo type
```bash
dut-control -- servo_type

Output:
servo_type:ccd_cr50

```

## Stop servod
```bash 
stop-servod -n <name>-servo

```

## Enter the docker container

```bash
# check the running docker container
servod-ps

# enter the running container
docker exec -it <name> bash

Example: docker exec -it 1700772381-docker_servod bash

```


## Flash BIOS/EC/Cr50 with Suzy Q or Servo V4

_Connect 9999:_

```bash 
start-servod --channel=latest --allow_offline --mount=<Firmware_PATH>:/tmp/firmware_to_flash -n <name>_servo --board {board_name}

docker exec -it <name>_servo-docker_servod bash

```
_Flash BIOS:_

```bash
futility update --servo --image=/tmp/firmware_to_flash/bios.bin

```

_Flash EC:_
```bash
flash_ec --board=brox --image /tmp/firmware_to_flash/ec.bin

# flash ec need at the same time npcx.bin 

```

_Flash Cr50:_

```bash
gsctool ti50.prepvt

```

_Old:_
```bash
sudo I_NEED_SERVOD=1 servod -b {board}

#9999
```



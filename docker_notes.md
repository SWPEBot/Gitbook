# Ubuntu 安装 docker 和 docker-compose

1. 安装 docker

   ```bash
   sudo apt install docker docker-compose
   ```

2. 解决执行 docker 实例时需要使用 root 用户

    ```bash
    # 将用户添加到Docker用户组,这样做可以让您运行Docker命令，而无需每次都调用sudo。
    sudo usermod -aG docker ${USER}
    #完成上述命令后需重启当前 environment, 或重启 host

    # 测试
    docker images
    docker ps
    ```

3. 重启Docker守护进程

    ```bash
    systemctl restart docker
    ```

# 如何在 ubuntu 16.04 上安装 npm 和 nodejs 最新 / 稳定版本

Base Ubuntu 16.04 LTS

## 从官方存储库安装 nodejs 和 npm，node

```bash
sudo apt install nodejs
sudo apt install npm
#sudo apt install nodejs-legacy # check if needed
```

## 通过 `n` 安装最新的稳定版 `npm` 和 `nodejs`

```bash
sudo npm install -g n
#用n安装nodejs稳定或最新版本

# sudo n latest
sudo n stable
```

## 异常解决

[/usr/local/lib/node_modules/npm/bin/npm-cli.js:84](http://kselax.ru/en/npm-errors/)

## Remove all node versions

```bash
# Remove latest n version
sudo n prune
sudo npm uninstall -g n
sudo rm -r /usr/local/n
sudo rm /usr/local/bin/node

# Remove latest nodejs version
sudo apt-get purge -y nodejs npm

# Remove nodejs-legacy version
sudo apt-get purge -y nodejs-legacy npm

sudo apt -y autoremove

# Remove nvm
sudo rm -rf ~/.nvm

# Remove nodejs files
sudo rm -rf /usr/local/lib/node_modules/
sudo rm -rf /usr/local/lib/node
sudo rm -rf /usr/local/bin/node
sudo rm -rf /usr/local/bin/npm
sudo rm -rf /usr/bin/node
sudo rm -rf /usr/local/n/versions/node
sudo rm -rf /usr/local/share/man/man1/node
sudo rm -rf /usr/local/lib/dtrace/node.d
sudo rm -rf ~/.npm
sudo rm -rf ~/.node-gyp
sudo rm -rf /opt/local/bin/node /opt/local/include/node /opt/local/lib/node_modules
sudo rm -rf /usr/local/include/node/
sudo rm -rf /usr/local/share/man/man1/node*
```

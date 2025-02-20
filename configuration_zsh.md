# 安装 ZSH 及配置 oh-my-zsh 主题

> Debian Base Linux

1. 安装 zsh

   ```bash
   sudo apt-get install zsh
   ```

2. 将默认 bash 切换为 zsh

   ```bash
   chsh -s /bin/zsh
   ```

3. 安装 weget 和 curl

   ```bash
   sudo apt-get install curl wget
   ```

4. 通过 curl 安装 oh my zsh

   ```bash
   sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
   ```

   或者通过 wget 安装 oh my zsh

   ```bash
   sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
   ```

5. 设定 `oh my zsh` 主题

   ```bash
   sudo vim ~/.zshrc
   ZSH_THEME="agnoster"
   ```

   _oh my zsh 需要 power line 字体支持_

  安装`powerlevel10k`主题
   ```bash
   git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

   # Set ZSH_THEME="powerlevel10k/powerlevel10k" in ~/.zshrc.
   p10k configure 
   # Set your favorite style
   ```
   _powerlevel10k 需要 Nerd Font 字体支持_
   
   [powerlevel10k github](https://github.com/romkatv/powerlevel10k#fonts)

   # Set Terminal font MesloLGS NF

6. 安装 powerline 字体

   ```bash
   sudo apt-get install powerline
   sudo apt-get install fonts-powerline

   # git clone https://github.com/powerline/fonts.git
   # bash fonts/install.sh
   ```

   **在上述安装步骤完成后，请将你的 PC 重启**

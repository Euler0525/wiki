# Configuration

## Windows

```powershell
irm https://get.activated.win | iex
```

### PowerShell

### Alias

```powershell
. : File C:\Users\...\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1 cannot be loaded because
running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
At line:1 char:3
+ . 'C:\Users\...\Documents\WindowsPowerShell\Microsoft.PowerShel ...
+   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : SecurityError: (:) [], PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess

>> get-executionpolicy
Restricted
>> set-executionpolicy remotesigned
Y
>> ## set-executionpolicy restricted  # recover
```

### Oh My Posh

```powershell
 winget install JanDeDobbeleer.OhMyPosh -s winget
```

### This PC

```shell
## .reg
;取消此电脑"3D对象"文件夹
[-HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\MyComputer\NameSpace\{0DB7E03F-FC29-4DC6-9020-FF41B59E513A}]
;取消此电脑"文档"文件夹
[-HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\MyComputer\NameSpace\{d3162b92-9365-467a-956b-92703aca08af}]
;取消此电脑"图片"文件夹
[-HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\MyComputer\NameSpace\{24ad3ad4-a569-4530-98e1-ab02f9417aa8}]
;取消此电脑"视频"文件夹
[-HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\MyComputer\NameSpace\{f86fa3ab-70d2-4fc7-9c99-fcbf05467f3a}]
;取消此电脑"音乐"文件夹
[-HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\MyComputer\NameSpace\{3dfdf296-dbec-4fb4-81d1-6a3438bcf4de}]
```

### Start Menu

```
C:\ProgramData\Microsoft\Windows\Start Menu\Programs
C:\Users\Euler0525\AppData\Roaming\Microsoft\Windows\Start Menu\Programs
```

### MATLAB

- 默认初始工作路径

```matlab
% ...\MATLAB\toolbox\local\matlabrc.m
cd("C:\Syncdisk");
```

### nvm&nodejs

- [nvm-windows](https://github.com/coreybutler/nvm-windows)

- [Hexo](https://hexo.io/zh-cn/docs/)

## Linux

### SSH

```shell
sudo pacman -S open-ssh
sudo systemctl start sshd
sudo systemctl enable sshd

sudo vim /etc/ssh/sshd_config  # Port 22
sudo ufw allow 22/tcp

sudo ps -e | grep ssh
```

### zsh&Powerlevel10k

#### oh-my-zsh

```shell
sudo apt-get install zsh
sh -c "$(curl -fsSL https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"
chsh -s /bin/zsh  # 修改默认终端为zsh

git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k  # 安装Powerlevel10k
vim ~/.zshrc # 修改ZSH_THEME="powerlevel10k/powerlevel10k"
```

#### Plug-ins

```shell
# zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
# zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

vim ~/.zshrc
# plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
source ~/.zshrc
```

### [Conda](https://repo.anaconda.com/archive/)

#### Installation

```shell
cd ~/Download
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2024.02-1-Linux-x86_64.sh
bash Anaconda3-2024.02-1-Linux-x86_64.sh
conda update -n base -c defaults conda  # 更新（第一次安装不需要）
source ~/.bashrc  # 若为.zshrc需要添加 export PATH=/home/username/anaconda3/bin:$PATH
conda config --set auto_activate_base false  # 取消自动激活

# Source
vim.condarc
channels:
  - defaults
show_channel_urls: true
channel_alias: https://mirrors.tuna.tsinghua.edu.cn/anaconda
default_channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/r
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/pro
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/msys2
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  msys2: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  bioconda: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  menpo: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  pytorch: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
  simpleitk: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
auto_activate_base: true
```

#### env

```shell
conda create -n python3 python=3.10  # 创建虚拟环境
source activate python3  # 激活虚拟环境
conda remove -n python3 --all  # 删除虚拟环境
conda env list  # 查看虚拟环境
```

### Git

```shell
sudo git apt install git
```

#### Git&GitHub

```shell
# user.name & user.email
git config --global user.name ""           # git config user.name  查看用户名
git config --global user.email ""  # git config user.email 查看邮箱
ssh-keygen -t rsa -C ""
```

- 在GitHub中添加公钥

在用户名目录下的`.ssh`目录下`cat id_rsa.pub`，将内容复制；

进入GitHub-setting-SSH and GPG keys-New SSH key；

将刚刚复制的内容粘贴到key中，（Title任意）；

执行`ssh -T git@github.com`，输入`yes`，显示如下成功；

```
Hi ...! You've successfully authenticated, but GitHub does not provide shell access.
```

### WSL

```powershell
# 迁移
wsl --export Ubuntu D:\Ubuntu\Ubuntu.tar
wsl --import Ubuntu C:\Ubuntu\ C:\Ubuntu\Ubuntu.tar --version 2
```

### Docker

```shell
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
sudo usermod -aG docker ${USER}  # 将当前用户加入Docker用户组
sudo service docker start    # 启动docker服务
```

### Nginx

```shell
sudo apt install nginx -y
# 启动
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx
# 开放端口
sudo ufw enable # 打开防火墙
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw status
```

### TeXLive

- 开源镜像站：[清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/Images/)

```shell
❯ mkdir ~/Program/TeXLive
❯ mount .iso ~/Program/TeXLive && cd ~/Program/TeXLive
❯ ./install-tl
❯ umount ~/Program/TeXLive && rm -r ~/Program/TeXLive
❯ vim ~/.zshrc  # ↓
❯ source ~/.zshrc
❯ tex -v  # 验证安装成功
```

```shell
# Add TeX Live to the PATH, MANPATH, INFOPATH
export PATH=/usr/local/texlive/2024/bin/x86_64-linux:$PATH
export MANPATH=/usr/local/texlive/2024/texmf-dist/doc/man:$MANPATH
export INFOPATH=/usr/local/texlive/2024/texmf-dist/doc/info:$INFOPATH
```


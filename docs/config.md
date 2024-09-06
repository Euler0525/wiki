# Configuration

## source.list

```
# Ubuntu20.04
$ sudo vim /etc/apt/sources.list

# tuna.tsinghua
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse 
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse 
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse 
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse 
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse 
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse 
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse 
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-security main restricted universe multiverse

# aliyun
deb http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ jammy main restricted universe multiverse 
deb http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ jammy-security main restricted universe multiverse 
deb http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ jammy-updates main restricted universe multiverse 
deb http://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ jammy-proposed main restricted universe multiverse 
deb http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse 
deb-src http://mirrors.aliyun.com/ubuntu/ jammy-backports main restricted universe multiverse

# ustc
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy main restricted universe multiverse 
deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy main restricted universe multiverse 
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse 
deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse 
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse 
deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse 
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-security main restricted universe multiverse 
deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-security main restricted universe multiverse 
deb https://mirrors.ustc.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse 
deb-src https://mirrors.ustc.edu.cn/ubuntu/ jammy-proposed main restricted universe multiverse
```

## SSH

```
$ sudo apt update
$ sudo apt install openssh-server -y
$ sudo ps -e | grep ssh
$ sudo service ssh start
$ sudo systemctl enable --now ssh
$ sudo vim /etc/ssh/sshd_config  # PermitRootLogin yes
$ sudo service sshd start
# 开机自启
$ sudo systemctl enable ssh
$ reboot
```

## zsh&Powerlevel10k

### oh-my-zsh

```
$ sudo apt-get install zsh
$ sh -c "$(curl -fsSL https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"
$ chsh -s /bin/zsh  # 修改默认终端为zsh

$ git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k  # 安装Powerlevel10k
$ vim ~/.zshrc # 修改ZSH_THEME="powerlevel10k/powerlevel10k"
```

### Plug-ins

```
# zsh-autosuggestions
$ git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
# zsh-syntax-highlighting
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

$ vim ~/.zshrc
# plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
$ source ~/.zshrc
```

## [Conda](https://repo.anaconda.com/archive/)

### Installation

```
$ cd ~/Download
$ wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2024.02-1-Linux-x86_64.sh
$ bash Anaconda3-2024.02-1-Linux-x86_64.sh
$ conda update -n base -c defaults conda  # 更新（第一次安装不需要）
$ source ~/.bashrc  # 若为.zshrc需要添加 export PATH=/home/username/anaconda3/bin:$PATH
$ conda config --set auto_activate_base false  # 取消自动激活

# Source
$ vim.condarc
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

### env

```
$ conda create -n python3 python=3.10  # 创建虚拟环境
$ source activate python3  # 激活虚拟环境
$ conda remove -n python3 --all  # 删除虚拟环境
$ conda env list  # 查看虚拟环境
```

## Git

```
$ sudo git apt install git
```

### Git&GitHub

```
# user.name & user.email
$ git config --global user.name "Euler0525" 
$ git config --global user.email "euler0525@139.com"
$ ssh-keygen -t rsa -C "euler0525@139.com"
```

- 在GitHub中添加公钥

在用户名目录下的`.ssh`目录下`cat id_rsa.pub`，将内容复制；

进入GitHub-setting-SSH and GPG keys-New SSH key；

将刚刚复制的内容粘贴到key中，（Title任意）；

执行`ssh -T `[`git@github.com`](mailto:git@github.com)，输入`yes`，显示如下成功；

```
Hi ...! You've successfully authenticated, but GitHub does not provide shell access.
```

## Docker

```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
sudo usermod -aG docker ${USER}  # 将当前用户加入Docker用户组
sudo service docker start    # 启动docker服务
```

## Nginx

```
$ sudo apt install nginx -y
# 启动
$ sudo systemctl start nginx
$ sudo systemclt enable nginx
$ sudo systemctl status nginx
# 开放端口
$ sudo ufw enable # 打开防火墙
$ sudo ufw allow 80/tcp
$ sudo ufw allow 443/tcp
$ sudo ufw status
```

## MATLAB

- 默认初始工作路径

```matlab
% ...\MATLAB\toolbox\local\matlabrc.m
cd("D:\Syncdisk");
```


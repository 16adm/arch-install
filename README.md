# arch-install

## 准备
### 可引导U盘
#### ISO地址: https://mirrors.tuna.tsinghua.edu.cn/archlinux/iso/latest/
#### ventoy地址: https://github.com/ventoy/Ventoy/releases
#### 制作可引导盘
##### tar -xzvf #.tar.gz
##### sudo sh Ventory2Disk.sh -i /dev/sdb
##### cp *.iso /dev/sdb1
### 安装基本系统
#### 更改源:
##### sed -i "1i Server = https://mirrors.163.com/archlinux/$repo/os/$arch' /etc/pacman.d/mirrorlist
#### 安装GIT:
##### pacman -Sy git
#### GIT安装脚本:
##### git clone git://github.com/16adm/arch-install
##### cd arch-install
##### ./fifo [fn:1]
#### 用脚本安装基本系统:
##### keymap : us
##### editor : vi
##### mirrorlist : china
##### partition : gdisk [fn:2]
##### fstab : PARTUUID
##### hostname : z97d
##### timezone : asia/shanghai
##### hardware clock : local
##### locale : en_US.UTF8
##### mkinitcpio : default
##### bootloader : sytemd-boot
###### /boot/loader/loader.conf
    default arch
    timeout 1
    console-mode max
    editor no
    auto-entries 0
    auto-firmware 0'
###### /boot/loader/entries/arch.conf
    title Arch
    linux /vmlinuz-linux
    initrd /intel-ucode.img
    initrd /initramfs-linux.img
    options root=PARTUUID=xxxxxxxxxxxxxxxxxxxxxxxx rw quiet systemd.show_status=0
##### root passwd : ######
##### Ctrl+c
#### 重启进入新系统: reboot
    [fn:1]fifo安装：base base-devel linux linux-headers intel-ucode linux-firmware usbutils dhcpcd vi git gptfdisk efibootmgr dosfstools
    [fn:2]boot分区必须标识为EFI，否则无法安装，在gdisk里的命令为：t -> 1 -> EF00
## 后续系统完善
#### 添加用户:
##### useradd -m -g users -G wheel -s /bin/bash $username
##### chfn $username
##### passwd $username
##### visudo
##### logout ; login as $username
#### Git:
##### git clone git://github.com/16adm/dotfile-z97d
#### Locale
##### sudo vi /etc/locale.gen
##### en_US.UTF-8 ; zh_CN.UTF-8 ; ko_KR.UTF-8 ; fr_FR.UTF-8
##### sudo locale-gen
#### DHCP网络:
##### sudo vi /etc/systemd/network/20-wired.network
    [Match]
    Name=enp3s0
    [Network]
    DHCP=ipv4
##### sudo vi /etc/systemd/network/25-wireless.network
    [Match]
    Name=wlp0s20u13
    [Network]
    DHCP=ipv4
##### sudo systemctl enable systemd-networkd.service
##### sudo sed -i '$a 199.232.69.133 raw.githubusercontent.com' /etc/hosts
#### 安装yay:
##### sudo vi /etc/pacman.d/mirrorlist
##### Arch Linux repository mirrorlist
    Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
    Server = http://mirrors.163.com/archlinux/$repo/os/$arch
##### sudo sed -i '$a [archlinuxcn]' /etc/pacman.conf
##### sudo sed -i '$a Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch' /etc/pacman.conf
##### sudo rm -rf /etc/pacman.d/gnupg
##### sudo pacman-key --init
##### sudo pacman-key --populate archlinux
##### sudo pacman -Sy archlinux-keyring archlinuxcn-keyring
##### sudo pacman -S pacman-contrib
##### sudo pacman -S yay
#### 安装vim:
##### yay -S neovim python-pynvim xclip
##### yay -D --asdeps python-pynvim xclip
##### sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
##### nvim
##### :PlugInstall
#### 安装emacs:
##### yay -S emacs
##### git clone --depth 1 https://github.com/hlissner/doom-emacs ~/.emacs.d
##### ~/.emacs.d/bin/doom install
##### doomsy
##### yay -S ripgrep
#### 安装字体
##### yay -S otf-nerd-fonts-fira-code otf-mplus adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts
#### 安装输入法
##### yay -S fcitx fcitx-rime fcitx-qt5 fcitx-hangul
##### yay -D --asdeps fcitx
#### 安装WM
##### yay -S nvidia-utils nvidia-dkms
##### yay -D --asdeps nvidia-utils
##### yay -S alsa-utils alsa-plugins pulseaudio pulseaudio-alsa pulsemixer
##### yay -D --asdeps alsa-plugins pulseaudio pulseaudio-alsa
##### yay -S i3-gaps xorg-xinit
##### yay -D --asdeps xorg-xinit
#### 安装应用
##### yay -S rofi polybar alacritty picom qutebrowser nitrogen mpv
##### yay -S zathura zathura-pdf-poppler
##### yay -D --asdeps zathura
##### yay -S ranger ffmpegthumbnailer w3m
##### yay -D --asdeps ffmpegthumbnailer w3m
##### yay -S lxappearance-gtk3 lxrandr-gtk3 pcmanfm-gtk3 gruvbox-dark-icons-gtk gruvbox-dark-gtk
##### yay -S screenkey
#### 终端小工具
##### yay -S neofetch xdotool htop unimatrix exa sxiv
##### yay -D --asdeps xdotool
#### 解压
##### yay -S zip unzip unrar p7zip
#### 配置键盘
##### yay -S interception-caps2esc
#### 配置自动挂载
##### yay -S udskie udisks2 gvfs-mtp gvfs-gphoto2 [fn:2]
#### 配置蓝牙
##### yay -S bluez rofi-bluetooth-git
#### 其他
##### yay -S expressvpn syncthing redshift
##### yay -S wps-office-cn wps-office-mui-zh-cn google-chrome
#### 自动登录
##### sudo mkdir /etc/systemd/system/getty@tty1.service.d
##### sudo vi /etc/systemd/system/getty@tty1.service.d/override.conf
    [Service]
    ExecStart=
    ExecStart=-/usr/bin/agetty --autologin shieh --noclear %I $TERM
    [fn:2]如果连接android设备或u盘有权限问题，则lsusb找到设备，chmod 666 $device

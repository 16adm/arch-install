# arch-install

## 准备

#### ISO地址: https://mirrors.tuna.tsinghua.edu.cn/archlinux/iso/latest/
#### ventoy地址: https://github.com/ventoy/Ventoy/releases
#### tar -xzvf ventoy-{version}.tar.gz
#### sudo sh Ventory2Disk.sh -i /dev/sdb
#### cp archlinux-{version}.iso /dev/sdb1

## 安装最小系统

#### sed -i "1i Server = https://mirrors.163.com/archlinux/$repo/os/$arch' /etc/pacman.d/mirrorlist
#### pacman -Sy git
#### git clone git://github.com/16adm/arch-install
#### cd arch-install & ./fifo
##### keymap : us
##### editor : vi
##### mirrorlist : china
##### partition : gdisk
    boot分区必须标识为EFI，否则无法安装，在gdisk里的命令为：t -> 1 -> EF00
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
##### reboot
      base base-devel linux linux-headers intel-ucode linux-firmware 
      usbutils vi git gptfdisk efibootmgr dosfstools wpa_supplicant net-tools

## 安装基本系统

#### 用户
##### useradd -m -g users -G wheel -s /bin/bash $username
##### chfn {username}
##### passwd {username}
##### visudo
##### logout ; login as {username}

#### LOCAL
##### sudo vi /etc/locale.gen
    en_US.UTF-8 ; zh_CN.UTF-8 ; ko_KR.UTF-8 ; fr_FR.UTF-8
##### sudo locale-gen

#### 内核
##### sudo pacman -S --asdeps linux-zen linux-zen-headers
##### sudo pacman -D --asdeps git gptfdisk dosfstools linux linux-headers linux-firmware

#### 网络
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
##### sudo vi /etc/wpa_supplicant-wlp0s20u13.conf
    ctrl_interface=/run/wpa_supplicant
    ctrl_interface_group=wheel
    update_config=1
    
    network={
                        ssid=""
                        psk=""
                        }
    
    network={
                        ssid=""
                        psk=""
                        }
##### sudo systemctl enable wpa_supplicant@wlp0s20u13
##### sudo systemctl enable systemd-networkd.service
##### sudo sed -i '$a 199.232.69.133 raw.githubusercontent.com' /etc/hosts
##### sudo pacman -U xv-{version}.pkg.tar.xz
##### git clone git://github.com/16adm/dotfile-z97d

#### YAY 
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
##### sudo pacman -S --asdeps pacman-contrib
##### sudo pacman -S yay

#### 蓝牙
##### yay -S bluez pulseaudio-bluetooth bluez-utils
##### sudo sed -i '$a ### Enable bluetooth' /etc/bluetooth/pulse/system.pa
##### sudo sed -i '$a load-module module-bluetooth-policy' /etc/bluetooth/pulse/system.pa
##### sudo sed -i '$a load-module module-bluetooth-discover' /etc/bluetooth/pulse/system.pa
##### sudo sudo vi /etc/bluetooth/main.conf
    [General]
    DiscoverableTimeout = 0
    Discoverable=true
    AlwaysPairable=true
    [Policy]
    AutoEnable=true
##### sudo systemctl enable bluetooth.service
##### sudo systemctl start bluetooth.service
##### pulseaudio -k
##### bluetoothctl
    >>power on
    >>agent on
    >>defualt-agent
    >>scan on
    >>pair xx:xx:xx:xx:xx:xx
    >>trust xx:xx:xx:xx:xx:xx
    >>connect xx:xx:xx:xx:xx:xx
    >>quit

#### 键盘
##### yay -S interception-caps2esc interception-space2meta
##### sudo vi /etc/udevmon.yaml
    - JOB: "intercept -g $DEVNODE | caps2esc | space2meta | uinput -d $DEVNODE"
       DEVICE:
           EVENTS:
               EV_KEY: [KEY_CAPSLOCK, KEY_ESC, KEY_SPACE]

#### VIM 
##### yay -S neovim
##### yay -S --asdeps python-pynvim xclip
##### sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
##### nvim
##### :PlugInstall

#### EMACS
##### yay -S emacs ripgrep
##### git clone --depth 1 https://github.com/hlissner/doom-emacs ~/.emacs.d
##### ~/.emacs.d/bin/doom install
##### doomsy & doomck

#### 字体
##### yay -S otf-nerd-fonts-fira-code otf-mplus adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts

#### 输入
##### yay -S fcitx-rime fcitx-qt5 fcitx-hangul

#### 解压
##### yay -S zip unzip unrar p7zip

#### WM
##### yay -S nvidia-dkms alsa-utils pulsemixer xorg-xinit polybar alacritty picom qutebrowser mpv ranger rofi
##### yay -S --asdeps pulseaudio-alsa i3-gaps nitrogen zathura-pdf-poppler ffmpegthumbnailer w3m bash-completion
##### yay -S lxappearance-gtk3 lxrandr-gtk3 pcmanfm-gtk3 gruvbox-dark-icons-gtk gruvbox-dark-gtk
##### yay -S man-db screenkey scrot unclutter neofetch unimatrix-git exa sxiv redshift adhosts tealdeer
##### yay -S --asdeps xdotool imagemagick

##### 下载
##### yay -S aria2-fast ariang-allinone you-get

#### 挂载
##### yay -S udiskie syncthing
##### yay -S -asdeps gvfs-mtp gvfs-gphoto2

#### 其他 
##### yay -S wps-office-cn google-chrome
##### yay -S --asdeps wps-office-mui-zh-cn

#### 登录
##### sudo mkdir /etc/systemd/system/getty@tty1.service.d
##### sudo vi /etc/systemd/system/getty@tty1.service.d/override.conf
    [Service]
    ExecStart=
    ExecStart=-/usr/bin/agetty --autologin shieh --noclear %I $TERM

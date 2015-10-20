# arch-korean
Dell Inspiron Mini 10 Arch Linux 설치과정
* txt파일 링크
* https://dl.dropboxusercontent.com/u/54450962/archlinux%20%EC%84%A4%EC%B9%98%EA%B3%BC%EC%A0%95

1)하드디스크 정돈
* ping -c 3 www.google.com
* ip addr or ip link - 에서 enp4s0 (유선기기 확인)
* systemctl start dhcpcd@enp4s0
* umount /dev/sda*
* cfdisk /dev/sda
* /dev/sda1 7000MB - bootable
* /dev/sda2 1024 MB - type 82 - swap 
* /dev/sda3 나머지 
* mkfs.ext4 /dev/sda1
* mkfs.ext4 /dev/sda3
* mkswap /dev/sda2
* swapon /dev/sda2
* mount /dev/sda1 /mnt
* mkdir /mnt/home
* mount /dev/sda3 /mnt/home
* mkdir /mnt/boot
* mount /dev/sdax /mnt/boot

2) 시스템 설정 
* nano /etc/pacman.d/mirrorlist
* ## Korea
* Server = http://mirror.premi.st/archlinux/$repo/os/$arch
* Server = http://ftp.kaist.ac.kr/ArchLinux/$repo/os/$arch

* pacstrap /mnt base base-devel
* genfstab -p /mnt >> /mnt/etc/fstab
* nano /mnt/etc/fstab

3) 지역화 & 개인화
* arch-chroot /mnt
* echo arch > /etc/hostname
* nano /etc/timezone
* Asia/Seoul
* nano /etc/locale.gen
* en_US.UTF-8 UTF-8
* ko_KR.UTF-8 UTF-8
* locale-gen
* hwclock --systohc --utc
* pacman -S grub-bios
* grub-install --target=i386-pc --recheck /dev/sda
* grub-mkconfig -o /boot/grub/grub.cfg
* mkinitcpio -p linux
* passwd
* useradd -m -g users -G storage,power,wheel -s /bin/bash 원하는 ID
* passwd 원하는 ID
* pacman -S sudo gksu
* nano /etc/sudoers 또는 EDITOR=nano visudo
* ## User privilege specification
* ##
* root ALL=(ALL) ALL
* 원하는 ID ALL=(ALL) ALL
* ## Uncomment to allow members of group wheel to excute any command
* %wheel ALL=(ALL) ALL
* exit
* umount /mnt/{boot,home,}
* reboot

4)x-server 설치
* ip addr or ip link
* systemctl start dhcpcd@enp4s0
* pacman -S xorg-server xorg-server-utils xorg-xinit xorg-twm xorg-xclock xterm
* pacman -S xf86-video-intel
* pacman -S mesa
* pacman -S xf86-input-synaptics
* pacman -S ttf-dejavu
* startx
* pkill x

5) Desktop-Environment 설치
* pacman -S kde 
* https://wiki.archlinux.org/index.php/Kde

* pacman -S gnome gnome-shell gnome-extra
* https://wiki.archlinux.org/index.php/Gnome

* pacman -Syy mate mate-extra mate-netbook 
* https://wiki.archlinux.org/index.php/Mate

* pacman -S cinnamon 
* https://wiki.archlinux.org/index.php/cinnamon

테마등의 별도 서버 더하기
* nano /etc/pacman.d/mirrorlist
* ## [cinnamon]
* Server = http://archlinux.zoelife4u.org/cinnamon/$arch

6) DE 설정 팁
* kdm
* pkill kdm
* systemctl start gdm.service

Cinnamon을 설치했습니다.
* pacman -S cinnamon
로그인 매니저로 gdm을 설치했습니다.
* pacman -S gdm

7) virtualbox-guest 설치하기
* pacman -S virtualbox-guest-utils
* modprobe -a vboxguest vboxsf vboxvideo
* nano /etc/modules-load.d/virtualbox.config
* vboxguest
* vboxsf
* vboxvideo
* reboot

8) 한글 폰트 & ibus ibus-hangul
* pacman -S file-roller
* 아래에서 NanumFont_Font_All.zip 파일을 다운받아서 ~/.fonts 에 압축을 풀어둠
* yaourt 설치후에 "yaourt -S ttf-nanum" 하면 설치됩니다.
* 지금은 chromium으로 이 문서를 먼저 열어보기 위해서 이렇게 합니다.
* https://aur.archlinux.org/packages.php?ID=49832
* pacman -S ibus ibus-hangul
* 시작프로그램에 등록
* ibus-daemon
* nabi는 yaourt 설치후에 "yaourt -S nabi" 하면 됩니다.

9) Gstreamer
* pacman -S tilda gedit chromium
* https://wiki.archlinux.org/index.php/GStreamer
* pacman -S gstreamer gst-libav gst-plugins-bad gst-plugins-base gst-plugins-good gst-plugins-ugly gst-vaapi

* 이것을 설치하시면 라디오 스트림을 포함해서 음악이 재생됩니다.
* pacman -S gstreamer0.10 gstreamer0.10-bad-plugins gstreamer0.10-base-plugins gstreamer0.10-ffmpeg gstreamer0.10-good-plugins gstreamer0.10-ugly-plugins gstreamer0.10-vaapi

10) yaourt 설치 
* http://archlinux.fr/yaourt-en
* curl -O https://aur.archlinux.org/packages/pa/package-query/package-query.tar.gz
* tar zxvf package-query.tar.gz
* cd package-query
* makepkg -si
* cd ..
* curl -O https://aur.archlinux.org/packages/ya/yaourt/yaourt.tar.gz
* tar zxvf yaourt.tar.gz
* cd yaourt
* makepkg -si
* cd ..

11) chromium 에 pepper flash plugin 설치 
* yaourt -S chromium-pepper-flash

* 수동으로 설정하기
* libpepflashplayer.so
* https://dl.dropboxusercontent.com/u/54450962/libpepflashplayer.so

* cp libpepflashplayer.so /usr/lib/chromium
* gedit /etc/chromium/default
* # Options to pass to chromium-browser
* CHROMIUM_FLAGS="--ppapi-flash-path=/usr/lib/chromium/libpepflashplayer.so"

12) 무선 네트워크 설정
* broadcom-wl 설치
* yaourt -S broadcom-wl
* sudo pacman -S networkmanager network-manager-applet
* sudo systemctl start NetworkManager
수동 설정)
* sudo pacman -S dialog
* sudo wifi-menu wlp3s0(무선 네트워크 - ip addr)

13) covergloobus 설치
* yaourt -S covergloobus

14) plank 설치
* sudo pacman -S plank

15)locale.conf 설정 - 한국어로 나오게 하기위함입니다. 
* nano /etc/locale.conf
* LANG=ko_KR.UTF-8
* LANGUAGE="ko_KR.UTF-8:en_US.UTF-8"
* LC_CTYPE="ko_KR.UTF-8"
* LC_NUMERIC=ko_KR.UTF-8
* LC_TIME=ko_KR.UTF-8
* LC_COLLATE="ko_KR.UTF-8"
* LC_MONETARY=ko_KR.UTF-8
* LC_MESSAGES="ko_KR.UTF-8"
* LC_PAPER=ko_KR.UTF-8
* LC_NAME=ko_KR.UTF-8
* LC_ADDRESS=ko_KR.UTF-8
* LC_TELEPHONE=ko_KR.UTF-8
* LC_MEASUREMENT=ko_KR.UTF-8
* LC_IDENTIFICATION=ko_KR.UTF-8
* LC_ALL=

16) ttf-nanum 설치하기 (나눔폰트)
* yaourt -S ttf-nanum

17) 시작할때 gdm 으로 시작하며 , 네트워크 자동시작하기
* sudo systemctl enable gdm
* sudo systemctl enable NetworkManager

18) 어플들
* sudo pacman -S smplayer smtube clementine plank variety geary gnome-screenshot shotwell
* sudo pacman -S p7zip unrar zip arj gnome-calculator 
* sudo pacman -S gparted dcfldd moc flashplugin nautilus-open-terminal nautilus-actions ntfs-3g  
* yaourt -S covergloobus focuswriter radiotray

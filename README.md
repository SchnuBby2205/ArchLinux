# ArchLinux
Installation of ArchLinux

ArchLinux in Dual Boot with Windows10
=====================================
(Source: https://www.youtube.com/watch?v=L1B1O0R1IHA)

- Windows 10 Already installed.
  - Deactivate Secure Boot / Fast StartUp 
  - Open Diskmanagement
  - Shrink Drive to make Space for Arch / Or use already free Space on Drive
  - DO NOT TOUCH: Recovery or the 99 MiB EFI Partition
 
- Download Arch Iso:
  - https://www.archlinux.de/download (Preferred -> cause it's always the newest Release)
  - Direct Link: https://archlinux.org/releng/releases/2023.04.01/torrent/ (torrent)
  - Make bootable USB Drive with balena etcher
  
- Reboot PC -> Boot from Arch ISO

- Configurate Arch
  
  - Keymaps
    - localectl list-keymaps [ENTER]
    - or localectl list-keymaps | grep de [ENTER]
    - loadkeys <your locale> eg: loadkeys de_CH-latin1 [ENTER]
  
  - Internet connection
    - ip a [ENTER] (On Cable there should already be an IP)
    
    ![grafik](https://user-images.githubusercontent.com/80288097/229470952-4e0f0bf9-d175-4425-bcfa-5bd82ec0bf4b.png)
    - On WiFi -> wifi-menu [ENTER]
      - Select WiFi enter Password -> ip a [ENTER] should show an IP
  
  - Date and Time
    - timedatectl set-ntp true [ENTER]
  
  - Mirrors
    - pacman -Syyy [ENTER]
    - pacman -S reflector [ENTER]
    - reflector -c <Country> -a 6 --sort rate --save /etc/pacman.d/mirrorlist [ENTER]
      (reflector -c Germany -a 6 --sort rate --save /etc/pacman.d/mirrorlist [ENTER])
    - pacman -Syyy [ENTER]
  
  - Partitioning
    - lsblk [ENTER]
    
    ![grafik](https://user-images.githubusercontent.com/80288097/229472279-5eabd6a4-1e84-4c2c-b5ea-416aadbd5033.png)
    - Free Space is not showing up here -> That's completly normal
    - cfdisk /dev/sda [ENTER]
    - Here the Free Space shows up
    - Select Free space -> New -> Assign all free Space (Swap will be created later)
    - Choose "Write" in the bottom
    - Quit Program
    - lsblk [ENTER]
    - Free Space now shows up 
  
    ![grafik](https://user-images.githubusercontent.com/80288097/229473029-f828023e-b6a3-462e-af1f-cc767a4be9d5.png)
  
  - Formatting
    - mkfs.ext4 <Partition> eg: mkfs.ext4 /dev/sda5 [ENTER]
    - mount /dev/sda5 /mnt [ENTER]
    - EFI Mounting:
      - mkdir /mnt/boot [ENTER]
      - mount /dev/sda2 /mnt/boot [ENTER]
      - /dev/sda2 is the 99MiB Partition from Windows EFI
    - Windows10 Mount
      - mkdir /mnt/windows10 [ENTER]
      - mount /dev/sda4 /mnt/windows10 [ENTER]
      - /dev/sda4 is the Windows Partition - Repeat this Step for any Win Partition you want to have access to in Arch
    - lsblk [ENTER]
    
    ![grafik](https://user-images.githubusercontent.com/80288097/229474316-b10edff4-0b59-44e3-b176-dc40bc28969c.png)
  
  - Installing Base System
    - pacstrap /mnt base linux linux-firmware nano (intel-ucode / amd-ucode) [ENTER]
    eg Intel Proz: pacstrap /mnt base linux linux-firmware nano intel-ucode [ENTER]
    eg AMD Proz: pacstrap /mnt base linux linux-firmware nano amd-ucode [ENTER]
  
  - Generate FSTab to keep the mounted Partitions
    - genfstab -U /mnt >> /mnt/etc/fstab [ENTER]
    - Control: cat /mnt/etc/fstab
    
    ![grafik](https://user-images.githubusercontent.com/80288097/229475245-fcbc257e-5747-497e-ba9f-86790974545d.png)
  
  - CHROOT
    - arch-chroot /mnt [ENTER]
    - NEXT STEPS MAY NOT WORK ON KERNEL 5.7
      - fallocate -l 2GB /swapfile [ENTER]
      - chmod 600 /swapfile [ENTER]
      - mkswap /swapfile [ENTER]
      - swapon /swapfile [ENTER]
      - nano /etc/fstab [ENTER]
      - insert in last line:
      /swapfile none swap defaults 0 0 -> save File
    - IF THE STEPS WONT WORK DO:
      - dd if=/dev/zero of=/swapfile bs=1M count=2048 status=progress [ENTER]
      - chmod 600 /swapfile [ENTER]
      - mkswap -U /swapfile [ENTER]
      - swapon /swapfile [ENTER]
      - nano /etc/fstab [ENTER]
      - insert in last line:
      /swapfile none swap defaults 0 0 -> save File

   - Locales
    - timedatectl list-timezones [ENTER]
    - timedatectl list-timezones | grep <Cityname> [ENTER]
    - ln -sf /usr/share/zoneinfo/Europe/Zurich /etc/localtime [ENTER]
    - hwclock --systohc [ENTER]
    - nano /etc/locale.gen [ENTER]
    - Search Locale (best with UTF-8) uncomment the line -> save File
    - locale-gen [ENTER]
    - echo "LANG=<Your Locale>" >> /etc/locale.conf [ENTER]
    eg: echo "LANG=en_US.UTF-8" >> /etc/locale.conf [ENTER]
    - echo "KEYMAP=<Your Locale from Keymaps>" >> /etc/vconsole.conf [ENTER]
    eg: echo "KEYMAP=de_CH-latin1" >> /etc/vconsole.conf [ENTER]
  
  - Hostname
    - nano /etc/hostname [ENTER]
    - Type in PC Name eg: myPC -> save File
    - nano /etc/hosts [ENTER]
    - New Lines at end
    - 127.0.0.1 <tabstopp> localhost
    - ::1 <tabstopp><tabstopp> localhost
    - 127.0.1.1 <tabstopp> <pcname>.localdomain <tabstopp> <pcname> -> save file
  
  - Root Password
    - passwd [ENTER]
    - Set Password  
  
  - Install GRUB
    - pacmans -S grub efibootmgr os-prober ntfs-3g networkmanager network-manager-applet (wireless_tools / wpa_supplicant) dialog mtools dosfstools base-devel linux-headers git (bluez / bluez-utils / pulseaudio-bluetooth) cups openssh [ENTER]
    - wireless_tools / wpa_supplicant -> W-LAN
    - bluez / bluez-utils / pulseaudio-bluetooth -> bluetooh
    - If Audio wont work install pulseaudio
    - grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB [ENTER]
    - grub-mkconfig -o /boot/grub/grub.cfg [ENTER]
  
  - Services
    - systemctl enable NetworkManager [ENTER]
    - systemctl enable bluetooth [ENTER]
    - systemctl enable org.cups.cupsd [ENTER]
    - systemcrl enable sshd [ENTER]
  
  - New User
    - useradd -mG wheel <name> [ENTER]
    - passwd <name> [ENTER]
    - SUDO Persmission for new User
      - EDITOR=nano visudo [ENTER]
      - Find line %wheel ALL=(ALL) ALL -> uncomment the Line -> Save File
  
  - Reboot
    - exit [ENTER]
    - umount -a [ENTER]
    - reboot [ENTER]
  
  - Reboot in Arch with the new User
    - Check IP ip a [ENTER]
    - nmtui -> for WiFi
 
  - Graphics Driver
    - Intel Graphics: sudo pacman -S xf86-video-intel
    - AMD Graphics: sudo pacman -S xf86-video-amdgpu
    - NVIDIA Graphics: sudo pacman -S nvidia nvidia-utils
  
  - Display Server
    - sudo pacman -S xorg xorg-init
  
  - DisplayManager (KDE) - Steps dont needed if you just use a Window Manager and a Terminaml Emu DMenu
    - sudo pacman -S sddm [ENTER]
    - sudo systemctl enable sddm [ENTER]
    - sudo pacman -S plasma kde-applications packagekt-qt5 [ENTER]
    - Stopping KDE from Autostart
      - sudo systemctl disable sddm [ENTER]
      - Be careful there will be NO Desktop anymore only Terminal Prompt
  
  - Internet Browser
    - sudo pacman -S firefox [ENTER]
  
  - YAY
    - git clone https://aur.archlinux.org/yay.git [ENTER]
    - cd yay/ [ENTER]
    - makepkg -si PKGBUILD [ENTER]
    - yay -S ttf-ms-fonts -> 1 -> N
  
  - Backup software
    - yay -S timeshift -> N -> N
  
  - Reboot
    - exit [ENTER]
    - reboot [ENTER]
  
  
REMOVE Arch Linux
=====================================
  - Go to Windows Diskmanagement
  - Delete the Linux Partition
  - Open PowerShell as Admin
    - diskpart [ENTER]
    - list volume [ENTER]
    - select volume <number> -> number is the Number of 99MiB Partition
    - assign letter=x
  - Open Taskmanager
    - File -> Run New Task -> Browse
    - Select Partition X:
    - Delete the GRUB Folder
    - Open the EFI Folder
    - Delete the GRUB Folder if it's there
  - Back to Powershell
    - remove letter=x 
    - exit 
  
  Arch Linux and GRUB should now be completly removed from the System.

  
  Window Manager (NOT A DESKTOP ENVIROMENT)
  Means Login Through Terminal no Desktop only WindowsManager and DMenu
  =========
  (source: https://www.youtube.com/watch?v=pouX5VvX0_Q)
  - Change These to Wallpaper or Window Manager of your liking
    - nitrogen -> for backgrounds
    - picom -> for compositor
    - yay -S dwm-distrotube-git (Window Manger)
    - yay -S st-distrotube-git (Terminal)
    - yay -S dmenu-distrotube-git (dmenu)
    - yay -S nerd-fonts-mononoki (Fonts) (Dependency for dmenu st and dwm)
  
  - cp /etc/X11/xinit/xinitrc /home/[username]/.xinitrc -> At the End of the File are the Autostart's
  
  ![grafik](https://user-images.githubusercontent.com/80288097/229516414-f390e4d8-a436-4417-b512-1e152effc671.png)
  
  ![grafik](https://user-images.githubusercontent.com/80288097/229516772-809fe9a1-e987-4e28-869b-1f69452f329f.png)

  - startx -> If startx isnt working you need sudo pacman -S xinit
  - Resolution:
    - xrandr [ENTER]
    - xrandr -s 1920x1080 [ENTER]

  - AUTOSTART
    - sudo nano ~/.bash_profile
    - insert last line:
    - [[$(fgconsole 2>/dev/null) == 1]] && exec startx == vt1

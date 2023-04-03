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
  
- Reboot PC -> Boot from Arch ISO

- Configurate Arch
  - Keymaps
    - localectl list-keymaps <ENTER>
    - or localectl list-keymaps | grep de <ENTER>
    - loadkeys <your locale> ie: loadkeys de_CH-latin1 <ENTER>
  - Internet connection
    - ip a <ENTER>
    - ![grafik](https://user-images.githubusercontent.com/80288097/229470952-4e0f0bf9-d175-4425-bcfa-5bd82ec0bf4b.png)

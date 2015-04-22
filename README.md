# Beaglebone-black-setup
Beaglebone black setup(Debian flash, host control, SD card extension, internet connection)

1. The lastest Debian system for Beaglebone black can be downloaded from official website and this is also GUI system with LXDE built in. (http://beagleboard.org/latest-images)
This image is only for installation on micro SD card, or you need to download the image only for emmc flash(BBB Rev C (4GB eMMC) 4GB eMMC Flasher), of which the information can be found on http://elinux.org/Beagleboard:BeagleBoneBlack_Debian#BBB_Rev_C_.284GB_eMMC.29_4GB_eMMC_Flasher

2. The downloaded image file is .img.xz format, and to extract the file, 7zip is a good tool, which can be downloaded from http://www.7-zip.org/

3. After extraction, the image files can be written to micro-SD card. The capacity of the SD card I used is 32gb, and Win32 Disk Imager writer is a good tool to write the image files to sd card. 

4. Hold the boot button (S2) on the top right (near the sd card slot) and while holding the button insert the power. After powering up the board, the four LEDs will flash initially, but then they will all light up together shortly. After this you can release the Boot Button. (On an early board I had more success with the 5V jack than with the USB power, but it doesn’t seem to matter how you power the board). Just wait until the installation successes. 

5. It would be helpful to have a monitor the keep an eye on the progress of system installation. The video port on BBB is mini HDMI, and if by accident it is a VGA monitor, you need a adaptor from mini HDMI to female VGA, but be careful not all the adaptor works and it is recomended to purchase 'Cable matters' adaptor, which has a seperate usb cable to provide extra power. 

6. If you want to share internet with host computer, it is advised to access BBB by ssh over USB with the command: ssh -CX root@192.168.7.2
To access internet on BBB with usb wire, follow the steps.
On BBB, type commands:
  # /sbin/route add default gw 192.168.7.1
  # echo "nameserver 8.8.8.8" >> /etc/resolv.conf
On Linux host(I used Ubuntu 14.04):
  # sudo iptables -A POSTROUTING -t nat -j MASQUERADE
  # sudo echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward > /dev/null

After this, you can update and upgrade Debian like normal PC. 

Similar host control can also happens in Window machine by using Putty. But I got the problem with X11 problem even after I enable X11 in Putty. 

7. Even though the micro SD card is 32gb, but the Debian system only takes up within 4gb, so if you want to work with the whole memory, you have to expand the SD card storage (http://elinux.org/Beagleboard:Expanding_File_System_Partition_On_A_microSD). 
# ls /dev/mmcblk*
/dev/mmcblk0
/dev/mmcblk0p1
/dev/mmcblk0p2

two partitions can be found on SD card, which labelled by mmcblk0p1 and mmcblk0p2

# fdisk /dev/mmcblk0

Command (m for help): p

Disk /dev/mmcblk0: 8270 MB, 8270118912 bytes
4 heads, 16 sectors/track, 252384 cylinders, total 16152576 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x80000000

        Device Boot      Start         End      Blocks   Id  System
/dev/mmcblk0p1   *        2048        4095        1024    1  FAT12
/dev/mmcblk0p2            4096     3751935     1873920   83  Linux

Command (m for help):

The blocks form mmcblk0p2 varies depending on the size of the SD card.

Next, enter 'd' to delete a partition and then enter '2' for partition 2 (/dev/mmcblk0p2)
Entering 'p' again will show that you have deleted /dev/mmcblk0p2
Command (m for help): p

Disk /dev/mmcblk0: 8270 MB, 8270118912 bytes
4 heads, 16 sectors/track, 252384 cylinders, total 16152576 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x80000000

        Device Boot      Start         End      Blocks   Id  System
/dev/mmcblk0p1   *        2048        4095        1024    1  FAT12

Command (m for help):

Now create a new partition by entering 'n' then 'p' and then '2'
You should hit enter to have your default start sector used. 

You can enter 'p' again to see that the /dev/mmcblk0p2 has re-appeared.
Command (m for help): p

Disk /dev/mmcblk0: 8270 MB, 8270118912 bytes
4 heads, 16 sectors/track, 252384 cylinders, total 16152576 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x80000000

        Device Boot      Start         End      Blocks   Id  System
/dev/mmcblk0p1   *        2048        4095        1024    1  FAT12
/dev/mmcblk0p2            4096    16152576     8074240   83  Linux

Command (m for help):

If you are satisfied with your changes at this point you can enter 'w' to commit to your changes. If you want to cancel or restart just enter <CTRL>+Z

Command (m for help): w

The partition table has been altered!

Calling ioctl() to re-read partition table.

WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.

# reboot

Lastly, after your BeagleBone Black reboots, you need to expand the file system. Enter the command 'df'
# resize2fs /dev/mmcblk0p2
This will take about a minute to complete 
resize2fs 1.42.5 (29-Jul-2012)
Filesystem at /dev/mmcblk0p2 is mounted on /; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 1
The filesystem on /dev/mmcblk0p2 is now 2018560 blocks long.


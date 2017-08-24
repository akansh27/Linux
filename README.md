# Linux
### Unix

Unix is a family of multitasking, multiuser computer operating systems that derive from the original AT&T Unix, developed starting in the 1970s at the Bell Labs research center by Ken Thompson, Dennis Ritchie, and others.

Unix was designed to be portable, multi-tasking and multi-user in a time-sharing configuration. Unix systems are characterized by various concepts: the use of plain text for storing data; a hierarchical file system; treating devices and certain types of inter-process communication (IPC) as files; and the use of a large number of software tools, small programs that can be strung together through a command-line interpreter using pipes, as opposed to using a single monolithic program that includes all of the same functionality. These concepts are collectively known as the "Unix philosophy". 

Under Unix, the operating system consists of many utilities along with the master control program, the kernel. The kernel provides services to start and stop programs, handles the file system and other common "low-level" tasks that most programs share, and schedules access to avoid conflicts when programs try to access the same resource or device simultaneously. To mediate such access, the kernel has special rights, reflected in the division between user space and kernel space.

Linux distributions, consisting of the Linux kernel and large collections of compatible software have become popular both with individual users and in business. Popular distributions include Red Hat Enterprise Linux, Fedora, SUSE Linux Enterprise, openSUSE, Debian GNU/Linux, Ubuntu, Linux Mint, Mandriva Linux, Slackware Linux, and Gentoo.

##### Kernel
Kernel is the heart of the Os. It interacts with hardware and most of the tasks like memory management, task scheduling and file management.

##### Shell
Shell is a utility that processes the requests. It interprets the command you type and calls the program you want. The Shell uses syntax for all commands.

##### Commands & Utilities
These are various commands and utilities used in day today activities. There are 250+ standard commands.

##### Files & Directories
All data in Unix is organized into files. All files are organized into directories. These directories are organized into a tree like structure called file system.



## Important Topics in Linux
### * Linux Boot Process
### * File System Hierarchy

### Linux Boot Process

Sequence of booting a machine starts with Power-On Self Test (POST), before control passes to the sector zero of the hard disk.

![Alt text](https://github.com/farashahamad/Linux/blob/master/bootprocess.png?raw=true "Optional Title")

#### 1. BIOS

* BIOS stands for Basic Input/Output System
* Performs some system integrity checks
* Searches, loads, and executes the boot loader program.
* It looks for boot loader in floppy, cd-rom, or hard drive. You can press a key (typically F12 of F2, but it depends on your system) during the BIOS startup to change the boot sequence.
* Once the boot loader program is detected and loaded into the memory, BIOS gives the control to it.
* So, in simple terms BIOS loads and executes the MBR boot loader.

#### 2. MBR

* MBR stands for Master Boot Record.
* It is located in the 1st sector of the bootable disk. Typically, /dev/hda, or /dev/sda
* MBR is less than 512 bytes in size. This has three components
```
1) primary boot loader info in 1st 446 bytes
2) partition table info in next 64 bytes
3) mbr validation check in last 2 bytes.
```
* It contains information about GRUB (or LILO in old systems).
* So, in simple terms MBR loads and executes the GRUB boot loader.

#### 3.GRUB

* GRUB stands for Grand Unified Bootloader.
* If you have multiple kernel images installed on your system, you can choose which one to be executed.
* GRUB displays a splash screen, waits for few seconds, if you don’t enter anything, it loads the default kernel image as specified in the grub configuration file.
* GRUB has the knowledge of the filesystem (the older Linux loader LILO didn’t understand filesystem).
* Grub configuration file is /boot/grub/grub.conf (/etc/grub.conf is a link to this). The following is sample grub.conf of CentOS.
```
#boot=/dev/sda
default=0
timeout=5
splashimage=(hd0,1)/grub/splash.xpm.gz
hiddenmenu
title CentOS (2.6.32-696.el6.x86_64)
        root (hd0,1)
        kernel /vmlinuz-2.6.32-696.el6.x86_64 ro root=/dev/mapper/centos-lv_root rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16  KEYBOARDTYPE=pc KEYTABLE=pt-latin1 crashkernel=auto rd_LVM_LV=centos/lv_swap rd_NO_DM rd_LVM_LV=centos/lv_root rhgb quiet
        initrd /initramfs-2.6.32-696.el6.x86_64.img
```
* As you notice from the above info, it contains kernel and initrd image.
* So, in simple terms GRUB just loads and executes Kernel and initrd images.

#### 4. Kernel

* Mounts the root file system as specified in the “root=” in grub.conf
* Kernel executes the /sbin/init program
* Since init was the 1st program to be executed by Linux Kernel, it has the process id (PID) of 1. You can check pid using ‘ps -ef | grep init’
* Initrd (Initial RAM Disk) is used by kernel as temporary root file system until kernel is booted and the real root file system is mounted. It also contains necessary drivers compiled inside, which helps it to access the hard drive partitions, and other hardware.

#### 5. Init
* Looks at the /etc/inittab file to decide the Linux run level.
* Following are the available run levels
```
0 – halt
1 – Single user mode
2 – Multiuser, without NFS
3 – Full multiuser mode (CLI)
4 – unused
5 – X11 or GUI
6 – reboot
```
* Init identifies the default initlevel from /etc/inittab and uses that to load all appropriate program.
* Execute ‘grep initdefault /etc/inittab’ on your system to identify the default run level
* If you want to get into trouble, you can set the default run level to 0 or 6. Since you know what 0 and 6 means, probably you might not do that.
* Typically, you would set the default run level to either 3 or 5.

#### 6. Runlevel programs
* When the Linux system is booting up, you might see various services getting started. For example, it might say “starting postfix …. OK”. Those are the runlevel programs, executed from the run level directory as defined by your run level.
* Depending on your default init level setting, the system will execute the programs from one of the following directories.
```
- Run level 0 – /etc/rc.d/rc0.d/
- Run level 1 – /etc/rc.d/rc1.d/
- Run level 2 – /etc/rc.d/rc2.d/
- Run level 3 – /etc/rc.d/rc3.d/
- Run level 4 – /etc/rc.d/rc4.d/
- Run level 5 – /etc/rc.d/rc5.d/
- Run level 6 – /etc/rc.d/rc6.d/
```
* Please note that there are also symbolic links available for these directory under /etc directly. So, /etc/rc0.d is linked to /etc/rc.d/rc0.d.
* Under the /etc/rc.d/rc*.d/ directories, you would see programs that start with S and K.
* Programs starts with S are used during startup. S for startup.
* Programs starts with K are used during shutdown. K for kill.
* There are numbers right next to S and K in the program names. Those are the sequence number in which the programs should be started or killed.
* For example, S12rsyslog is to start the syslog daemon, which has the sequence number of 12. S80postfix is to start the postfix daemon, which has the sequence number of 80. So, rsyslog program will be started before postfix.

### File System Hierarchy

![Alt text](https://github.com/farashahamad/Linux/blob/master/Linux-Filesystem-Hierarchy-Standard.png?raw=true "Optional Title")
![#f03c15](https://placehold.it/15/f03c15/000000?text=+) **`Simple`**

![Alt text](https://github.com/farashahamad/Linux/blob/master/linux-filesystem.png?raw=true "Optional Title")
![#f03c15](https://placehold.it/15/f03c15/000000?text=+) **`Detailed`**

![Alt text](https://github.com/farashahamad/Linux/blob/master/1EJLOqd.png?raw=true "Optional Title")
![#f03c15](https://placehold.it/15/f03c15/000000?text=+) **`More Detailed`**


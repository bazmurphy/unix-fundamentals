# The Boot Process

# Power Supply Unit

PSU
Self test
ATX
4/8 pin to CPU
24 pin to Motherboard

PSU sends signal to the CPU on 4/8 pin to power on

# BIOS and CMOS

Basic Input Output System "BIOS"

Integrated Circuit Located on the computers motherboard that can be programmed with firmware.

Firmware is software that is programmed into the Electrically
Erasable Programmable Read-Only Memory "EEPROM"

- Firmware facilitates the booting of an operating system and configure basic hardware

- An Integrated Circuit "IC" is what you would think of as a stereotypical computer chip

- BIOS is the lowest level interface you get to the hardware in the computer

- BIOS performs Power On Self Test "POST"

- CPU Boots
- Makes first call to BIOS
- BIOS ensures that the minimum hardware exists

1. CPU
2. Memory
3. Video Card

- Once it checks for existence and confirms, must be configured

- BIOS has it's own memory storage Complementary
  Metal Oxide Semiconductor "CMOS"

- CMOS contains all the settings the BIOS needs to save

  - CPU Frequency Multiplier
  - Memory Speed
  - Location/Config of Drives/Devices

- BIOS takes the memory frequency and attempts to set it on the memory controller

- BIOS multiplies the memory frequency by the CPU frequency

- This is the speed the CPU is set to run
- You can overclock a CPU by telling it to run at a higher multiplier than it was designed to

# POST tests

- BIOS determines which device to use to start the Operating System
- BIOS can read boot information from the devices:
- floppy disk
- cd-rom
- usb flash drive
- hard drive
- a network

- Two partition table formats
  - Master Boot Record "MBR"
  - GUID Partition Table "GPT"

## Master Boot Record (old way)

- Once the BIOS has identified which drive it should attempt to boot

  - looks at the First Sector on that Drive
  - those sectors should contain the Master Boot Record

  - the Master Boot Record has two component parts:

    - boot loader information block (448bytes)
    - partition table (64 bytes)

  - boot loader information block is where the first pgoram the computer can run is stored
  - partition table stores information about how the drive is logically laid out

- the MBR is heavily limited in its design - can only occupy first 512bytes of space on the drive
- limits the tasks the boot loader can do
- the execution of the boot loader literally starts from the first byte

- defined in the partition table, windows have drives labelled C: and D: represent different logical partitions on the drive

## GPT The GUID Partition Table (new way)

- the design of the IBM Compatible BIOS is an old design with limitations
- the United Extensible Firmware Interface "UEFI" was created along with GPT a new partition format

Advantages of GPT

- each partition has a unique ID
- unlimited partitions
- ability to boot from storage devices greater than 2TB (MBR couldn't do this)
- backup copy of the table that can be used if primary copy is corrupted (backup is stored at the end of the disk)

## Bootloader

- the purpose of a bootloader is to load the initial kernel and supporting modules into memory
- there are a few common bootloaders
- GRand Unified Bootloader "GRUB" is used in many linux distributions
- GRUB is a "chain bootloader" initialises in stages
  - Stage 1 - this is the very tiny application that can exist in the first part of the drive, it exists to load the larger part of GRUB
  - Stage 1.5 - contains the drivers necessary to access the filesystem with stage 2
  - Stage 2 - this stage loads the menu and configuration options for GRUB

### On an MBR-formatted drive and standard BIOS

- these stages must fit in that first 448bytes of the boot loader information block table
- stage 1 and stage 1.5 are small enough, they contain logic to allow the loader to read the filesystem that stage 2 is located on

### On a GPT-formatted drive and UEFI

- UEFI motherboards are able to read FAT32 filesystems and execute code
- The system firmware looks for an image file that contains the boot code for Stages 1 and 1.5 so that Stage 2 can be managed by the operating system

## The Kernel and the Ramdisk

- the kernel is the main component of any operating system
- the kernel acts as the lowest-level intermediary between the hardware on your computer and the applications running on it
- the kernel abstracts away such resource management tasks as memory and processor allocation
- the kernel and other software can access peripherals such as disk drives via device drivers

- the Initial RAM Filesystem / Ramdisk
- all the different devices in the world with different connectors, manufactures, interfaces etc all neeed their own device driver to bridge hardware to software
- can't keep all of that info in the kernel
- initial ramdisk provide module support to the kernel for the boot process
- allows the kernel to load just enough drivers to read from filesystem, where it can find other device drivers
- with the kernel and ramdisk loaded into memory, we can attempt to access disk drive and continue booting our linux system

## OS Kernel and Init

- organisational scheme for determining load order for system services during boot process is referred to as an "init system"
- traditional and still most common init system in Linux is called "System V init"
- after initial ramdisk sets the stage for the kernel to access the disk drive, we execute the first process that will "rule them all" `/bin/init`
- the `init` process reads `/etc/inittab` to figure out what script should be run to initalize the system
- this is a collection of scripts that vary based on the desired `"runlevel"` of the system

## Runlevels

- various `runlevels` has been defined to bring the system up in different states
- general the following `runlevels` are consistent to most linux distributions:

  - 0 Halt the system
  - 1 Single User Mode
  - 6 Reboot the Machine

- across distributions there can be various meanings for runlevels

### Multiuser vs. Single user runlevels

- in some runlevels multiple users can use the machine versus one user in single mode
- in multiuser runlevels:

  - the system boots as normal
  - all standard services such as SSH and HTTP daemons load in the order defined in the init system
  - the network interfaces, if configured are enabled

- in single user mode the bare minimum of services are enabled (no networking)

- you will need single user mode when:

  - something breaks
  - something you configured interferes with the boot process and you need to turn it off
  - a key filesystem is corrupt and you need to run a disk check

- the only available access is via the console
  - not limited to physical presence
  - remote console access by way of serial consoles and similar devices is common management tool for data centers

## Getty

- After all the system initialization scripts have run, we present the user with a prompt to login
- the method of doing this is to provide a login prompt on a Teletype "TTY"
- this is a holdover from the days that a user running a UNIX-based operating system sat at a serially-connected teletype machine
- a TTY can be a physical or virtual serial console, such as the various terminals you'd be presented with if you use the ALT+F# on the console of a Linux machine

- Getty is often used to continuously spawn `/bin/login` which reads the username and password of the user and if authentication succeeds, spawn the user's preferred shell. At this point the boot and login process has completed.

---

## Notes:

Power On Sequence:

- Pressing the power button closes the circuit and sends power from the PSU to the motherboard and components

- The CPU initializes and sends a power good signal to the motherboard

- The motherboard BIOS/UEFI runs a power on self test (POST) to check hardware

- The BIOS/UEFI loads settings from CMOS chip and looks for bootable devices

- The GPT (GUID partition table) is loaded from the disk drive

- Bootloader loads kernel and initial RAM disk into memory

- Kernel initializes and starts essential processes and looks for root partition

- Root filesystem is mounted and init process starts userspace applications

- User logs in and operating system loads, ready for use

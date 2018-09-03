So a lot of people here (and there) think that multibooting is quite hard with macOS, which actually the case with some systems (stupid mobos) and quite tricky in legacy mode, but as far as you're using UEFI, it's very ***VERY*** easy.

To begin with, I will start with UEFI mode, which is the easiest setup to do. This guide will cover different situations, and I will try to implement linux too for people who are into that. I don't know if I will put a legacy dualboot (with GPT, and not MBR) until I do some extensive tests.

***
### Disclaimer:
*Neither I, nor anyone here, is responsible for what you're doing. Any material/moral loss is under the user's responsibility, save anything worth saving before proceeding, by following this guide, you accept these terms, you have been warned.*

**PLEASE READ THOUGHTFULLY BEFORE ASKING.**
***

# UEFI+ GPT

To understand how it works, unlike the old legacy days where there was a sector that the bios had to read to load the bootloader, UEFI systems need an `EFI program/application` that is either the bootloader or the one that will load the bootloader (like clover, clover is not a real bootloader most of the time, it loads macOS's `boot.efi` which is the real macOS bootloader) which is presented as a `file` instead of a sector. This eases fixing boot issues and can help save and recover boot elements by simply copying them else where and can be edited. All the files are in a FAT32 formatted partition, mostly EFI partition on system disks, or any FAT32 partitions even on MBR formatted disks.

So what's makes it that easy? If you take the example of grub2 and winloader in a legacy system, to use one you need to replace the other with it, so you can't just switch from grub2 to winloader (or the opposite) without replacing the boot sector of the other, but in UEFI systems (mostly desktop setups, and some laptops, like ASUSs and MSIs...) you can "choose" which file to load, so you can boot directly to windows by loading `bootmgfw.efi` in your EFI partition or load GRUB2 or even CLOVER by selecting their respective files. This way, you can have all the booting files, without the need to replace them, and thanks to clover you can load these applications and files directly from it.

What's the issue then? The issue is not in the files in the EFI partitions, the issue is how the disk is partitioned and how the systems will react to it. For example, macOS disk utility cannot (for whatever reason) format a partition on a GPT disk if the EFI partition is not at least 200MB, while windows and linux are satisfied with whatever size you give them, as long as it can hold the boot files, usually, if you format the disk from within their respective installers, they automatically make a 100MB EFI partition, which is fine for them but not for macOS. This is where it gets tricky and where macOS becomes a bit bitchy about.

So I'll divide this guide into these situations:

* I have 2 disks and I use each OS on a disk
* I have 1 disk with windows/linux already installed in it
* I have blank disk or with macOS installed already in it and I want to add windows/linux

***

## I- I have two disks and each OS is on a drive

This is straightforward:

1. Install Windows/Lx (or dual boot them) on disk 1
2. Install macOS on the other drive
3. Install clover (or copy your existing clover files) to the EFI partition of the macOS disk
4. DONE

Easy! Right?

### TiP:
* If you want to install windows and you have multiple disks, either disable them from the firmware setup (aka bios setup) or physically disconnect them, windows on GPT is quite bitchy and wants to be alone with his disk. You can connect the drives later once done.
* To install windows on a UEFI system:
 1. Download Windows 10/8.1 ISO from M$ website (they are free)
 2. Mount it OR open it with 7zip/winrar(ew)
 3. Plug a 8GB USB and format it as FAT32
 4. JUST copy the contents of the ISO into the root of your USB drive
 5. Boot it on your computer (make sure it's set to UEFI mode)
 6. DONE

***

## II- I have 1 disk with windows/lx and I don't want to remove/reinstall them

This situation is widely faced when you get a new laptop or coming from a "normal" setup.

### In Windows

You need this:

* Minitool Partition Wizard [MPW](https://www.partitionwizard.com/free-partition-manager.html)
* Your computer
* Your brain

What you need to do

1. Open MPW
2. Check your partitions for the EFI partition
 * On some systems it may be named EFI, on others (like Sony) SYSTEM
 * It's a FAT32 partition
 * On regular windows install (clean from m$) it may have 100MB, on OEM installs, it may get to 450MB
 * It doesnt have a drive letter
 * Dont confuse it with Recovery partition of Windows (which is 450MB up to 800+)
3. if it's >=200MB, just go ahead, repartition your disk and install macOS, it shouldnt bother you ***BUT*** if it's <200MB, you'll see that you have *EFI* then *MSR* (M$ reserved partition) and then *YOUR SYSTEM DRIVE* (note that the recovery partition can be at the very beginning or at the end of the list, if you have it anyways), then you will have to:
 1. Resize your system partition from the left by 200MB (that's enough, if you feel it isn't, add as much as you can to make the EFI drive >250MB, 400MB in a EFI partition is too much tbh)
 2. Select the MSR partition, select Copy Partition, then choose the blank space, the software will ask then where to specifically copy it in that blank space (since MSR cannot be resized), move it to the far right, to be sticked to the System partition.
 3. Delete the old MSR
 3. Now resize your EFI partition to fill the rest of the blank space
 4. Now before applying, right click on the new MSR partition, select "Change partition type" and choose from the list "Microsoft Reserved Partition" then OK
 5. Apply now, and it will ask for a reboot

Your system should now reboot, and depending on what data is stored in those parts of the disk, it may be quick or very slow (some reported waiting about an hours, which is a lot, so if you think that it's stuck, keep an eye on the HDD/SSD LED activity if you have one).

Once done, you can test if windows boots up and then resize your partition, then launch the macOS installer and do the rest. (check [Pre-Intall](https://github.com/camielverdult/Ramblings-of-a-hackintosher-High-Sierra/blob/master/Pre-Install.md#pre-install) and [Post-install](https://github.com/camielverdult/Ramblings-of-a-hackintosher-High-Sierra/blob/master/Post-Install.md#post-install))

### In Linux

Well since you're using linux, I bet you have your way with `gdisk`,`parted` and `gparted`, use one of them to:

1. Resize the partition next to `EFI` from the left by the amount needed to get >=250MB in `EFI`.
2. Apply and install macOS
3. I dont know what you're still doing here, go do it now.

***
## III- I have a blank disk or with macOS installed on it and I want to install windows/lx next to macOS

For this situation: 

1. Install macOS first and partition your drives to HFS+/APFS partitions. ***DO NOT MAKE A FAT32 DISK FOR EITHER WINDOWS OR LINUX, KEEP IT HFS+/APFS (better HFS+, highly recommended).*** *Reason:* stupidly from Apple, when you make a FAT32 partition, it converts the GPT to a hybrid MBR, and it's meant usually for Bootcamp purposes, since Windows (on pre-2016 Apple device) is generally installed in legacy, and windows is picky so it prefers MBR on legacy and strictly GPT on UEFI. So keep in mind not to make a FAT32 partition.
2. Once done and it's all prepared, make your Windows install with bootcamp utility (it will be made for Legacy and UEFI installing) or use unetbootin for linux (or do both). Also remember your 2nd partition's size.
3. Boot to your second OS installer
4. On windows installer, usually in the disk selection step, you'll a 619MB partition (if on HFS+, on APFS, you'll just see a block with your macOS partition size), that's your macOS Recovery HD, and right after it, you'll see a partition with the size you wanted to give to windows, select it, select "Delete" (you can choose format, but it wont make some extra partitions for windows, nor recommended), then choose the unallocated space, hit next and it will automatically make needed partitions and install windows. 
On Linux, if you want to dualboot, just make sure you choose the correct partition, format/resize/swap and everything you need on linux. If you want to triple boot with both macOS and Windows, make sure you leave Linux to the last, and do as you usually prefer.

***

## Fix Clover

On some cases, especially laptops, they are hard coded to start `\EFI\Microsoft\Boot\bootmgfw.efi` instead of `\EFI\BOOT\BOOTX64.EFI`, and this to lock the computer (somewhat) to M$'s botnet OS. To fix that you can:

* Mount EFI partition, rename `bootmgfw.efi` in `EFI > Microsoft > Boot` to anything else than `bootmgfw.efi` (like bootmgfww.efi or something). Remember that naming. Now, the computer will not find `bootmgfw.efi` and look for `bootx64.efi` or whatever is the next boot option.
* Rename `CLOVERX64.EFI` to `bootmgfw.efi` then rename M$'s `bootmgfw.efi` to anything else, then copy the fake `bootmgfw.efi` to `EFI > Microsoft > Boot`. On some laptops it is needed to force load anything named `bootmgfw.efi` to boot anything.
* On ***SOME*** UEFI systems, like AMI Aptio, you can add and change boot options from the firmware setup (aka, BIOS setup) and change boot file priority, use this instead of renaming if you have this option. If you can do this, no need to configure clover any further.
* IF you dont have this option, you can use [EasyUEFI](https://www.easyuefi.com/index-us.html) to edit your UEFI boot options, add/remove and change priority. Use this software to make a Clover entry by choosing in the OS selection `Other` and filename `\EFI\CLOVER\CLOVERX64.EFI` (using the Browse button). This will work on some UEFI firmwares like AMI Aptio, but won't on some stupid InsydeH2O crappy lockedass firmware. If you can do this, no need to configure clover any further.

* Manually Add Clover Entry:
	* Enter EFI Shell.
	* As shell loads, note the label of the HDD/SSD your EFI and macOS/OSX are installed on, or type `map` to see it
	* Then `bcfg boot dump`
	* VERY CAREFULLY add a new entry after the highest one in the list. I had to type `bcfg boot add XX FSX:\EFI\CLOVER\CLOVERX64.EFI "CloverBoot"`, where `FSX` is your macOS/OSX EFI, `XX` is the new entry and `"CloverBoot"` is the name of the entry, can be changed to anything as long as it's between `" "`
 * Then you can delete old `bcfg` entry that points to `/BOOT/BOOTX64.EFI` with `bcfg boot rm XX` where `XX` is the number identifier seen when you do `bcfg boot dump`
 * *Optional:* You can then boot into macOS/OSX and rename /BOOT to /BOOT.bak

*Source: https://sourceforge.net/p/cloverefiboot/tickets/226/#d1a9/0222/8cc1*

*(Credits to /u/corpnewt and /u/Ressetkk for mentioning this to me, maybe I'll add `efibootmgr` guide too, most linux users know how to use google tho)*

**Keep in mind:**

* On ANY windows cumulative or seasonal update/upgrade, your fake `bootmgfw.efi` will be replaced by a new one, or if you renamed it, a new one will be copied over, so you will need to rename/replace that file.
* If you like to use Grub2 to load linux, make sure you load GRUB2 from Clover and not the opposite (feasible, works sometimes, not recommended).

Now you will get to CLOVER screen but without windows access mostly (I'm sure you dont need to rename grubx64 for linux users, as most OEMs dont lock the firmware on it). So to add ANY EFI program to the boot list, you need to add this `Custom Entry` to clover's `config.plist` as shown here:
```
    <key>GUI</key>
    <dict>
     ...
      <key>Custom</key>
      <dict>
        <key>Entries</key>
        <array>
          <dict>
            <key>Path</key>
            <string>\EFI\path\to\file.efi</string>
            <key>Title</key>
            <string>*insert title here*</string>
            <key>Type</key>
            <string>Windows OR Linux</string>
            <key>Volume</key>
            <string>*Insert partition GUID here if on different DISK*</string>
          </dict>
           ... to other entries ...
        </array>
      </dict>
     ...
    </dict>
```
So for example `bootmgfw.efi` renamed to `bootmgfww.efi`
```
    <key>GUI</key>
    <dict>
     ...
      <key>Custom</key>
      <dict>
        <key>Entries</key>
        <array>
          <dict>
            <key>Path</key>
            <string>\EFI\Microsoft\Boot\bootmgfww.efi</string>
            <key>Title</key>
            <string>Windows Bootloader</string>
            <key>Type</key>
            <string>Windows</string>
            <key>#Volume</key>
            <string>I DONT NEED IT</string>
            <key>#Comment</key>
            <string>When I add a "#" before the naming of the key, it's disabled</string>
          </dict>
        </array>
      </dict>
      ...
    </dict>
```
Save your config.plist and try it out. You can replace path with `\EFI\grub\grubx64.efi` to load GRUB2 or look where `lilo` file is located and add it too.

*To know more, go to:*

http://www.insanelymac.com/forum/topic/298027-guide-aio-guides-for-hackintosh/?p=2029540

https://clover-wiki.zetam.org/configuration/gui#gui_custom
***

## InS - QnA

This section will gather known issues and will to fix them.

>I try to install windows, but it says that I have an MBR disk, but macOS is already installed, so it should GPT, right?

Yes and No. Apparently, your disk has been changed to **Hybrid MBR**, and to fix it, have a good read here http://www.insanelymac.com/forum/topic/298027-guide-aio-guides-for-hackintosh/?p=2098111.

>I lost `bootmgfw.efi`, am I retarded?

Maybe, hopefully not, how did you even lose it? But to get it back you need to make a windows installer disk, and follow **Step number 4** in this guide http://www.insanelymac.com/forum/topic/298027-guide-aio-guides-for-hackintosh/?p=2098111.

>I'm on an MBR partitioned disk, and my `$(setup)` supports UEFI, why was I stupid enough not to install it as UEFI? and how can I make it UEFI bootable and not lose anything?

http://www.insanelymac.com/forum/topic/298027-guide-aio-guides-for-hackintosh/?p=2098111 << ok ? There is a specific paragraph for that.

>... but dude! I'm the laziest shit on this earth, and I have the Fall Creators Update!

https://docs.microsoft.com/en-us/windows/deployment/mbr-to-gpt << Good news! No need for extra steps!

>I have clover in legacy and I have a GPT formatted disk, I want to install winblows and it wont saying it's GPT and needs MBR! HOW?

(Win10 only, maybe will work on win8.1, win 7 is harder) Make your USB in the UEFI way (8GB FAT32 USB, copy the contents of the ISO into the USB, or use bootcamp to make it on macOS). In Clover GUI, if you see "Boot bootx64.efi from <something that hints to USB", go for it, if it doesn't load, or does nothing

1. Hit "S" to open shell command
2. Follow this album https://imgur.com/a/U4lgW (note: these pics were taken when I made a guide for Win7/8.X installation on Legacy to UEFI (quite the complicated stuff), and we had to download `bootx64.efi`, on Windows 10 installer, you dont need to download it, it's already in its folder)

>I'm stuck in windows and I need to edit stuff on EFI, how do I do that?

1. Open Command Prompt as administrator (or PowerShell if you're "modern")
2. Type `mountvol X: /s`. This will mount the EFI partition to `X:`, you can change the letter to anything else as long as it's not used
3. Download Explorer++ (9oo9le 1t)
4. Run it as administrator
5. ...
6. Edit

>... but I need to edit my config.plist dude! How can I do that????

1. Copy your config.plist somewhere else than the EFI partition
2. Use either:
	* Geany (9oo9le)
	* Sublime Text (9oo9le)
	* jsPlistor http://tustin2121.github.io/jsPlistor/ (a JS plist editor, you need to open the config and copy its contents to the "Import" box)
	* CCE http://cloudclovereditor.altervista.org/cce/index.php (A better alternative to CC and it's used on the wed directly, you'll just need to upload your file)
3. Save
4. Replace your old one using Explorer++
5. Hope you didnt break it.

***

... > *TO BE UPDATED*

***

# Legacy + GPT

\*\*\*TO DO***
***

Sources for this guide and credits:

* fusion71au for his guides and help
* InsanelyMac to host stuff
* Apple for their crappyexpensive laptops that made us hackintosh
* Every user than helped in the links I gave above

This guide will be updated for more information later.

GOOD LUCK!
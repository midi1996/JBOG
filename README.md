# NO, This is not a make-it-all guide
This is basically a mix of guides made by me or other people, that may or may not be updated (but we'll try anyways), that will help little poor you get some idea about Hackintosh and whatnot.

# The hell is this hackintosh?
It's a stupid word made from Macintosh and Hack (ikr, stupid but what else can we name this? Issue your ideas, be creative).
Basically install macOS on a Winders/Loonix computer.

If you have that ideology that a Mac is not a PC like what Apple would make you understand, then go look for what a PC means and come back here.

# Iz it EZ?
Shot answer: No

Long answer: No.

It is Ez as long you know how to google. Seriously, the amount of people with fast and reliable internet that still can't properly google things when they're stuck is just flabbergasting. Mostly 85% of hackintosh issues are solved with a simple google search.

# Do I need something before starting?
Oh yeah you do! 
* a brain with enough IQ
* time, lots of, can be a week, can be a month, can be longer, you can do this in intervals, but *never* *ever:tm:* expect it to be done in 24h unless you're experienced in this.
* dem patience! a lot of! like time, it's a lot.
* serious g00gle skilz

Physical crap:
* a victim computer
* a non-victim computer (to get stuff done when debugging/making things work) [OPTIONAL]
* and reading skilz, without this, just stop and go install winders.

# So I can make fulfill one of the requirements, wat do?

If you could at least have a decent amount of IQ, then I'm happy to tell you that you can follow through (hopefully) as this hackintosh thingy is as stupid as installing linux on an old computer with obscure hardware.

So basically here are the main steps you take on ***ANY*** hackintosh setup:
1) Get a macOS installer (in this guide, it's a recovery environment, other people may need the full macOS installer that you can get from the AppStore on a macOS environment)
2) Make a USB partitioned to receive both *Clover* (a boot manager, NOT a bootloader) and macOS Installer (which contains its own bootlaoder, that Clover will load.)
3) Make (or get) an appropriate config.plist for Clover and fix your UEFI drivers (.efi) for Clover (to properly load drives/macOS...) and macOS drivers (.kext) (to properly load devcies and macOS)
4) Do some changes on your UEFI Firmware (aka BIOS) Setup
5) Boot Clover, start macOS installer, install macOS, reboot twice, boot macOS
6) ...
7) Happy hacky hack hack.

# Ok! Gotcha (not really). Haw do?

That's the whole point of this repo, the first guide here is just help you to make an installer of macOS from windows, without a VM, and stay vanilla.

Remember, if you use any \*beast products, distros (Niresh, iATKOS, HackintoshZone), just go commit not living. You will not be helped or supported by the people here, so just dont try. But if you want our support, you can just scrap that crap from your hardware and start over from here or any approved:tm: vanilla guide.

You'll find here a file named [muh_vanilla.md](muh_vanilla.md) to start making your installer USB, and Multiboot.md is a cute addition for (*obviously*) multiboot purposes (read that first before the installer guide).

### Good hacky hack hack.

Discord and shit: http://discord.io/hackintosh (midi#4911, dont you dare PM me, ill commit not keeping you alive).

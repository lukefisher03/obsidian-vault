Luke Fisher
M14058588
January 13, 2025

*__1.1:__ Take a selfie of yourself posing in front of the display showing a Ubuntu 24.04.1 LTS Desktop.  You should run the settings application in Linux and click the "about" menu item in that application's side bar.  An info/status window will appear.  You may need to scroll down a bit to show which OS is running.  You should have this displayed in the selfie with you.  Paste the selfie image into your writeup.  For reference, my screen when running the "settings" application and the status item looks like this:*
![[IMG_0247.jpg |400]]
*__1.2:__ Write a paragraph that describes how you did the installation.  Be sure to document any difficulties you faced.  Reported difficulties and their solutions will be used to update this homework writeup for future students.  You will not lose points because you had difficulties. On the contrary, overcoming difficulties is a good thing and telling us so we can keep materials up to date is very much appreciated.*

I used VMware Fusion as my VM client. The primary challenge is installation on an ARM architecture, I'm using an M2 MacBook Air (*I am aware that it's not recommended*). The Ubuntu Desktop for Ubuntu 24 (Noble Numbat) is available as an ISO that only supports x86 machines. However, this can be easily circumvented by installing the ARM Ubuntu Server ISO image and then manually configuring a GUI on top. As a side note, I later found out that if I had used UTM rather than VMware Fusion, I could have used the Apple Virtualization Framework which may be faster than QEMU leading to better VM performance. Outside of this, the process was generally smooth. Another note, you may want to use `systemctl` to disable some services that slow down your boot times such as `systemd-networkd-wait-online.service`. There are a few others that I disabled to speed up boot time.

*__2.1:__ You are asked to create a file directory called “team_dropbox” that has the property that any member of a file permission group can create or read a file, but no one can delete a file except for the person who created it.  Show all the Bash shell commands you could type to create this directory.  Explain what each command does and how each command contributes to achieving the desired effect.  Note: There is more than one correct way to accomplish this task.*

You can achieve this by running these commands:
```
$ mkdir team_dropbox
$ chmod 1774 team_dropbox
```
Linux file permissions are broken down into 3 categories. The categories relate to the file / directory owner, the file groups, and all other users. When looking at a file's permissions you can use this command:
```
$ ls -l
```

The 774 in chmod is broken down like this:
- The `1` represents the `sticky` bit. The sticky bit disables deleting within the directory. Only the owner (or root) of a file can delete a file.
- The `7` represents all permissions granted. Read, write, execute.
- The `7` represents all permissions granted. Since it's the second digit (after the special permissions), it applies to groups.
- The `4` represents only read permissions. Since it's the final digit it represents any user.

You can find a list of what the different numbers mean online. In short they use a number to represent each permission (read, write, execute) and then use the combination of them to form a single number that encapsulates all the permissions for a specific category. The 1 in the beginning limits the file permissions so that only individual file owners can delete their own respective files within the directory.

*__2.2:__  You are asked to find every file in anywhere in your current working directory that were modified after midnight of the current day.  Your solution should recursively descend all subdirectories reachable from your current working directory and list any filename associated with a file that satisfies the search criterion.  Show all the Bash shell commands you would employ and explain what each command does and how each contributes to achieving the desired effect. Note: There is more than one correct way to accomplish this task, though the "find" command may be helpful.*

The `find` command in bash has an argument called `newerXY`. According to the man page `find` will compare files it's searching through with the date passed in. The XY are placeholders that modify the argument's behavior. Since we want to find all files in our current working directory modified after midnight, we could run this command:
```
$ find . -type f -newermt 00:00:00
```
This command will look for files (excluding directories) that were modified after midnight of the current day. The flag `newerXY` changed to `newermt` where the `m` means "modified" and the "t" meaning "reference time". The time format follows standard `findutils` date input formats.

*__3.1:__ Take a selfie of yourself posing in front of the display showing a GRUB boot menu showing both the original and new kernels in the menu options. Paste this image into your writeup.*
![[IMG_0252.jpg|300]]
*__3.2:__ Write a paragraph that describes which, if any, of the steps caused you difficulty and how you overcame that difficulty. Note, reported difficulties will be used to update this homework writeup for future students. You will not lose points because you had difficulties. On the contrary, overcoming difficulties is a good thing.  If you had no difficulties, just tell us that AND instead suggest how we could write the directions to build a kernel more clearly.*

There weren't really any difficulties aside from having to stop and start the build process multiple times as I had to move around campus. I, for some reason that I can't recall anymore, decided to compile using LLVM (`clang` and `ld.lld`) rather than GCC. Maybe because I was running on an ARM machine it felt better, not sure. Potentially some more information on directory structure could have sped up the process a little bit. I assumed that the configuration file needed to be named `.config` and eventually realized that the kernel required that. I could potentially see that being a point of confusion, especially since the file is hidden. I originally had kernel version `6.8.0-51-generic` installed, but then I built and installed just `6.8.0`.

*__4.1:__ Take a selfie of yourself posing in front of the display showing a GRUB boot menu that doesn't show the kernel you installed.  When you are done with the selfie, you can put it back the kernel you built if you like*

![[IMG_0253.jpg|300]]

*__4.2:__ Write clear directions that explain to another HOW to remove a kernel.  Attempt to be clear in your descriptions and treat this like you're documenting the process clearly enough for someone else to use.  You may embed pictures or diagrams if you think them useful.  You will be graded on the clarity of your directions and an assessment if they could be followed successfully by another person to accomplish the task.*

In order to remove the manually built kernel, you will need to run these commands (Or some variation close to it). These commands require elevated privileges.

1. Remove any related files in `/boot` that have the kernel version in it that you wish to remove.
	1. This will generally include these files: `config-<VER>`, `initrd.img.old`, `initrd.img-<VER>`, `System.map-<VER>`, `vmlinuz.old`, `vmlinuz-<VER>`. Replace `<VER>` with your kernel version.
2. Remove the module directory from `/lib/modules/your-kernel-version-here`
3. Run `sudo update-grub` to remove the kernel from the boot loader
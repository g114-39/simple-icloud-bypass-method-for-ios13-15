# Simple-iCloud-Bypass-Method
Simple iCloud Bypass method utilizing the rootful palera1n jailbreak, patched mobileactivationd, and applera1n's modified SSH Ramdisk
____________________________

This is how I bypassed iCloud lock on my 32GB iPhone 7 on iOS 15.8.8 with a broken home button.

I got the iOS 13 mobileactivationd from [here](https://github.com/exploit-development/iOS-Hacktivation-Toolkit/tree/master/bypass_scripts) (Yes, the old version works on iOS 15)

And have put it here for easier access

Then I built the applera1n ramdisk, used it then ran these commands:



```shell
mount_filesystems_nouser
/sbin/mount_apfs /dev/disk0s1s8 /mnt8
cd /mnt8/usr/libexec/
mv mobileactivationd mobileactivationdBackup
```
From outside the ssh ramdisk, I copied the ```bypass/mobileactivationd_13_x/mobileactivationd``` to ```/mnt8/usr/libexec/mobileactivationd``` through the ```scp``` command
Then went back into the ramdisk to execute these last commands

```shell
cd /mnt8/usr/libexec/
chmod 755 mobileactivationd

ldid -e /mnt8/usr/libexec/mobileactivationdBackup > mobileactivationd.plist
ldid -Smobileactivationd.plist mobileactivationd

rm -fv mobileactivationd.plist
launchctl unload /System/Library/LaunchDaemons/com.apple.mobileactivationd.plist
launchctl load /System/Library/LaunchDaemons/com.apple.mobileactivationd.plist
```

These commands were all I needed to bypass iCloud Activation on the rootful palera1n jailbroken environment.

When you boot the jailbroken environment, you will either see the language selection or the hello screen. Progress the setup until you see the wifi selection page, where you will see/press the next button on the top left which isn't present on iCloud locked phones. When you see this next button, you will know the bypass succeeded in the fakefs.

You will need to execute ```palera1n -f``` every single time you want to boot the rootful iCloud bypassed environment, or else the phone will boot the sealed root partition on which the mobileactivationd can't be modified where it is iCloud locked.


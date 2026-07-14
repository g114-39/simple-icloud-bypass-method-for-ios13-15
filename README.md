# Simple-iCloud-Bypass-Method
Simple iCloud Bypass method utilizing the rootful palera1n jailbreak, patched mobileactivationd, and applera1n's modified SSH Ramdisk
____________________________

This is how I bypassed iCloud lock on my 32GB iPhone 7 on iOS 15.8.8 with a broken home button on Linux.

[NOTE]: This method will only work inside the rooful palera1n environment, it won't work outside it. Any work done will be inside this environment. You will need to boot the palera1n environment every single time if you want to use your iPhone without the iCloud Activation lock.

I got the iOS 13 mobileactivationd from [here](https://github.com/exploit-development/iOS-Hacktivation-Toolkit/tree/master/bypass_scripts) (Yes, the old version works on iOS 15)

And have put it here for easier access

I first put my iPHone in DFU mode and then installed a rootful [palera1n](https://palera.in/) jailbreak on my iPhone using this command
```shell
sudo palera1n -cf
```

Then I modified the ```sshrd.sh``` from [applera1n](https://github.com/Hackt1vator/applera1n) and built the ramdisk. ( I have also put the modified ```sshrd.sh``` here )

Then put my iPhone back into DFU mode if it wasn't already and executed these commands to create the ramdisk.

```shell
cd applera1n/palera1n/ramdisk
mv sshrd.sh sshrd.sh.bak
cp <Location of the `sshrd.sh` from here> sshrd.sh

chmod +x sshrd.sh && ./sshrd.sh <your iPhone's iOS version>
```

Then in a separate terminal, executed: 
```shell
sudo iproxy 2222 22
```

And went back to the original terminal to execute this command with my iPhone connected in DFU mode
```shell
bash ./sshrd.sh boot
```

And then finally, ssh into the iPhone.

```shell
./Linux/sshpass -p 'alpine' ssh -o StrictHostKeyChecking=no -p2222 root@localhost 
```

If you can't ssh into your phone then you might need to kill the ```usbmuxd``` systemd daemon and in a separate terminal, manually run it using this command
```shell
sudo usbmuxd -p -f
``` and make sure iproxy is running on the port you specified, I used ```2222```

In the ssh, I used these commands.
```shell
mount_filesystems_nouser
/sbin/mount_apfs /dev/disk0s1s8 /mnt8
cd /mnt8/usr/libexec/
mv mobileactivationd mobileactivationdBackup
exit
```
Now, after exiting the ssh ramdisk, I copied the ```bypass/mobileactivationd_13_x/mobileactivationd``` to ```/mnt8/usr/libexec/mobileactivationd``` through the ```scp``` command

You can do this by executing
```shell
./Linux/sshpass -p 'alpine' scp -o StrictHostKeyChecking=no -P2222 <patched mobileactivationd location> root@localhost:/mnt8/usr/libexec/mobileactivationd 
```

Then went back into the ramdisk to execute these last commands

```shell
cd /mnt8/usr/libexec/
chmod 755 mobileactivationd

ldid -e /mnt8/usr/libexec/mobileactivationdBackup > mobileactivationd.plist
ldid -Smobileactivationd.plist mobileactivationd

rm -fv mobileactivationd.plist
launchctl unload /System/Library/LaunchDaemons/com.apple.mobileactivationd.plist
launchctl load /System/Library/LaunchDaemons/com.apple.mobileactivationd.plist
exit 
```

Now finally reboot the iPhone:
```shell
./Linux/sshpass -p 'alpine' ssh -o StrictHostKeyChecking=no -p2222 root@localhost "/sbin/reboot"
```
The phone will naturally boot into the orignal root partition if you reboot it, but we need to tell it to boot from the palera1n rootful fakefs, the one where we put the matched ```mobileactivationd``` to.

From here on, in order to do this, I put my phone back into DFU mode and executed the palera1n command to boot the fake fs every time I rebooted it:
```shell
sudo palera1n -f
```

These commands were all I needed to bypass iCloud Activation on the rootful ```palera1n``` jailbroken environment.

-------------------------------------
When you first time boot the jailbroken environment, you will either see the language selection or the hello screen. Progress the setup until you see the wifi selection page, where you will see/press the next button on the top left which isn't present on iCloud locked phones. When you see this next button, you will know the bypass succeeded in the fakefs.



You will need to execute ```palera1n -f``` every single time you want to boot the rootful iCloud bypassed environment, or else the phone will boot the sealed root partition on which the mobileactivationd can't be modified where it is iCloud locked.


If you use this method and it works on your iPhone, put in the issue 
your iphone name so that I can add it to the list of tested devices or you can commit the change directly.

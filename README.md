# trackpoint-speed-linux
How to adjust trackpoint speed on Ubuntu

As it turns out, adjusting the slider translates into numeric values which live in files in Linux's /sys filesystem. I think this should be consistent across X1 Yoga systems but in my case those files were:  

/sys/devices/platform/i8042/serio1/serio2/speed  
/sys/devices/platform/i8042/serio1/serio2/sensitivity  

I didn't record the actual values but, from memory, the mouse config applet was injecting values in the range of 90 through 120's into the files where the min:max values are 0:255. You can "echo" new numeric values into these files to tune your mouse sensitivity to your liking like so:  

echo 255 | sudo /usr/bin/tee /sys/devices/platform/i8042/serio1/serio2/speed  
echo 175 | sudo /usr/bin/tee /sys/devices/platform/i8042/serio1/serio2/sensitivity  

However be aware of two things:  

This will often cause a momentary freakout of your mouse behavior. It takes a second or two for things to settle down.  
Changes made in the /sys filesystem do not persist across reboot so you're going to need to put these changes somewhere when they will persist.  
One way to make these changes persist is by adding a config file in /etc/tmpfiles.d. Apparently systemd will re-apply settings placed here at every reboot. Here's the contents of my /etc/tmpfiles.d/trackpoint.conf:  

w /sys/devices/platform/i8042/serio1/serio2/speed - - - - 255  
w /sys/devices/platform/i8042/serio1/serio2/sensitivity - - - - 175  

I found the following command to work and thus avoid the reboot:  

$ sudo systemd-tmpfiles --prefix=/sys --create  

You can also read [this post](https://askubuntu.com/questions/596085/increase-trackpoint-sensititvity-with-systemd-ubuntu-15-04-or-later) for more information.

If you are like me, your hand will thank you for making these adjustments.  

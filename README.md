# keyd-ascii-codes
Enable ALT + Ascii Code character support like in Windows for Linux using the NUMPAD number keys. keyd works on a kernel level so this is a very lightweight, ZERO package dependency config file. It works on Wayland or X11 without any extra compatibility packages or layers.

# REQUIREMENTS AND DEPENDENCIES
Just one: [keyd](https://github.com/rvaiya/keyd)

# ABOUT
Makes it possible to use ALT+AsciiCode for the most popular French accentuated characters as well as a few other examples like space, enter, pipe, tilde, space and some special characters just for demonstration. These codes all use the universal standard ascii codes.

# INSTALLATION (Debian or other apt base Linux distro)
1. In command line, login as root
2. Run these commands to install, run and setup keyd as a background service:
```
apt update
apt install -y keyd
systemctl daemon-reload
systemctl start keyd
systemctl enable keyd
```
3. Put the file (default.conf) under this path: /etc/keyd/default.conf
4. To apply settings of this file, you need to reload keyd.

   **NOTE:** keyd is the process name but the executable is named "keyd.rvaiya". Reload keyd by running this command as root:
```
keyd.rvaiya reload
```
5. Changes are immediate and should be available system-wide.

# EXTRA NOTES
These are small trade-off when you move from Windows to linux but I made sure that the trade-offs were minimal and not too hard for your brain to adapt.

1. keyd does not yet have a "on-key-release" option. This means that as soon as you typed the third number of the ascii code, the character will instantly type itself before you releas the ALT key.
2. For single and 2 digit ascii codes, you NEED to preceed the number with their leading ZEROS. so ALT+32 becomes ALT+032 and ALT+9 becomes ALT+009.
3. In windows, you can preceed an ascii code with a ZERO to type non-standard characters. This file permits the same thing with the exception of using a leading 5 instead of 0 which I have nicknamed in a very original name "#5-trigger". For example, the bullet character which is normally done by ALT+0149 becomes ALT+5149. This file only contains the bullet character support and the ENTER (5013 instead of 013 on Windows).
4. Finally, you may see a subtle typing of the unicode codes since the system simulates the pressing of Ctrl+Shift+u+Unicode+Enter. This means that you may see in a split second the letters "u00E9" before the "é" appears which is normal but universally accepted on most major Linux distros and does not hinder anything aside from possible OCD people wanting absolute invisible behind the scenes workarounds. Its a small price to pay for Windows like integrations on a kernel-level on Linux and for me, it's very acceptable.

# FINAL NOTE AND DISTRIBUTION RIGHTS
You can by all means extend this file, change it and resdeistribute it as you wish. Linux is free, keyd is free, so is this file. It's a proof of concept that works for my personal needs. If you have other needs or you want to take time and support the entire 255 characters of the standard ascii table, please be welcomed.

# CUSTOMIZING, ADDING OR CHANGING CHARACTERS
You can add or change the key combinations to your liking. Here's how the file is built and it's logics.

1. Detect the ALT key (both left and right are supported and detected)
2. Detect the first digit ON THE NUMPAD or the first number after the #5-trigger.
3. Detect the first number.
4. Detect the third number and apply the right keycode.

keyd works with layer names so the layers were named by the numbers that were pressed already. Example:
1. If number 1 is pressed, goto [num1]
2. If number 3 is pressed, goto [num13] because now, numbers 1 and 3 have been pressed consecutively.
3. If number 0 is pressed, type the character "é" and end detection

In the case of the special characters like the bullet point, it goes in a similar way but with 1 extra layer.
1. If the number 5 is pressed, goto the special ascii detection layer [ascii_special]
2. Proceed with the same logics as above but with the layer names preceeded with "s" for special. So If 1 is pressed, goto [snum1] instead.

In practical, for the letter "é" it is coded like this:
```
# Detect both ALT keys
[main]
# GOTO ascii_code layer
leftalt = layer(ascii_code)
rightalt = layer(ascii_code)

# Detect first number
[ascii_code:A]
kp1 = swap(num1)

# First number was "1", detect second number
[num1]
kp3 = swap(num13)

# Second number is 3 and the first was 1 so we hav 13 already entered
# Detect final number
[num13]
# Write the character é using it's unicode value 00E9 sending the key sequence Ctrl+Shift+u+0+0+E+9+Enter
# GOTO the EXIT layer ascii_done
kp0 = swapm(ascii_done, macro(C-S-u 0 0 e 9 enter))

# End of ascii code detection, exit
[ascii_done]
```

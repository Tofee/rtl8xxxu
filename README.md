# rtl8xxxu

These are patches to fix the mainline Linux rtl8xxxu wireless driver to work with the Cube i9 which has a Realtek rtl8723bu wireless IC.

The first commit is a patch of contents of directory
https://git.kernel.org/cgit/linux/kernel/git/jes/linux.git/tree/drivers/net/wireless/realtek/rtl8xxxu?h=rtl8xxxu-devel
as of 30 October 2016

Although the kernel is listed as a fork of linux-2.6.git, the contents of the above directory is from the Linux maintainers tree for the wireless rtl8xxxu driver.

Linux maintainers and their SCM trees are listed in https://www.kernel.org/doc/linux/MAINTAINERS

The patches have been sent to relevant Linux maintainers and kernel mailing lists.

# How to build and install the driver

You need sudo, git, linux-headers and basic devlopment tools, such as base-devel for Archlinux and Antergos or build-essential for Debian and Ubuntu.

You also need to check you have firmware at /lib/firmware/rtlwifi/rtl8723bu_nic.bin

You can get the firmware from
https://git.kernel.org/cgit/linux/kernel/git/firmware/linux-firmware.git/tree/rtlwifi/rtl8723bu_nic.bin

The firmware location is hard coded into the driver and is the same whether or not bluetooth is used as well as WLAN. For this driver the firmware size used is 32108 bytes and is v35.0

For Debian the non-free package firmware-realtek is available.

From a terminal as non root user:

```
git clone https://github.com/johnheenan/rtl8xxxu,git
cd rtl8xxxu
make install

```

The patched driver has the same system name as the mainline driver: rtl8xxxu. 

The alternative working driver (that must be installed separately if you want to use it or run tests with it) has the system name 8723bu. If you were using the 8723bu driver then check *.conf files in /etc/modprobe.d and ensure you have entries such as

```
cat /etc/modprobe.d/blacklist.conf
# blacklist rtl8xxxu
blacklist 8723bu

```
to ensure only 8723bu is blacklisted. This only affects booting. The # before blacklist prevents blacklisting

You can switch between drivers using rmmod and modprobe or by changing what is blacklisted and rebooting

You can check only one of rtl8xxxu or 8723bu has been loaded with command

```
lsmod | grep usbcore
```
The top line should have one of either 8723bu or rtl8xxxu in it. If none or both are in the line then this is incorrect.


# What the patch does

The patch works by forcing a fuller initialisation and forcing it to occur more often in code paths (such as occurs during a low level authentication).

Due to the scant information available on the IC, the fix should be considered a temporary workaround until someone with access to full details of the IC may become involved in patching

There are only two patched files
* Makefile
* rtl8xxxu_core.c

The Makefile was edited to allow the kernel module to be built and installed outside of kernel source tree

File rtl8xxxu_core.c had a number of small changes. One change was to move part of an initilisation sequence elsewhere (closer to where needed and potentially reoccuring more often) and the other was to ignore an apperently bogus result that was interpreted to mean a full initialisation was not necessary.

File rtl8xxxu_8723b.c is relevant but was not patched

None of the other three .c files are relevant to the Cube i9


# Practical issues

There is evidence userspace packages may interfere with functionality so for testing it is better to start with as minimal an install as practical.

Known to work with Archlinux, wpa_supplicant and dhcpcd with no additional networking userspace packages. Archlinux which uses whatever the current Kernel release is.

It is only in recent kernels that support for Surface Pro type tablets is emerging. So it may be pointless trying anything except very recent kernels with the Cube i9.


# Kernels

Known to work with more recent series kernels as of October 2016. Due to recent kernel changes it is possible this patched driver may not work with linux 4.7 or earlier. 


# Alternatives and acknowledgements

There ia an alternative working driver at https://github.com/lwfinger/rtl8723bu that is based on original manufactuer driver code by Realtek. The system name of this driver is 8723bu.

This alternative working driver was very helpful in helping developing this fix.


John Heenan

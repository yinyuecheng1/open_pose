# install ubuntu kernel manually

## install 
- downloads the kernel( http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.9/ )
- 3 files.`  linux-headers-4.9.0-040900_4.9.0-040900.201612111631_all.deb`,
` linux-headers-4.9.0-040900-generic_4.9.0-040900.201612111631_amd64.deb`,
` linux-image-4.9.0-040900-generic_4.9.0-040900.201612111631_amd64.deb`
- ` uname -r`  to check the version of current kernel.
- `sudo dpkg -i *.deb`


## remove

- check kernel image files in OS, `dpkg --list|grep linux-image`,  `dpkg --list|grep linux-headers`
- remove the specific kernel version. `sudo apt-get purge linux-image-3.19.0-15`,
`sudoapt-get purge linux-headers-3.19.0-15`


- remove old kernel(https://www.linuxidc.com/Linux/2015-09/123227.htm)

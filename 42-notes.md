
# VirtualBox Debian Install
Installing debian in a virtualbox was relatively easy.  First download the iso from the [debian.org site](https://www.debian.org/distrib/) and from the "Small Installation System" I chose the `64bit PC netinst iso`.

After that I open up Virtualbox  and created a new VM with the following config:
- 2048MB of RAM
- 8GB Dynamically allocated VDI

Next, I went into the settings and mounted the debian ISO to the optical drive and then started the machine. I went through the install process, accepting the defaults and when asked about software, I unselected all options (like GUI, print server, etc...) except the standard software pkgs at the bottom.

Once the server was created, I performed the following installs:
```
$ apt-get update
$ apt-get upgrade
```
```
$ apt-get install sudo
```
```
$ sudo apt-get install git-core
$ git config --global user.name "Nilson Molina
$ git config --global user.email nmolina@student.42.us.org
$ git config --global -l
``` 
```
$ sudo apt-get install valgrind
```

## Install Guest Additions
1. **Install kernel header and build dependencies for kernel**  
Simply run the following commands:
```
$ sudo apt install -y linux-headers-amd64
$ sudo apt build-dep -y linux
```
2. **Mount Guest Additions CD image**  
Insert iso file of VirtualBox Guest Additions from window menu of VirtualBox and then mount it.
> Devices -> Insert Guest Additions CD image 
```
$ sudo mount -t iso9660 /dev/sr0 /media/cdrom
```
3. **Build Guest Additions**  
Simply run the following commands:
```
$ cd /media/cdrom
$ sudo sh ./VBoxLinuxAdditions.run
```
4. **Reboot Machine**  
```
$ sudo reboot
```
5. **Confirm Results**
```
$ lsmod | grep vbox
vboxsf                 45056  0
vboxvideo              49152  2
ttm                    98304  1 vboxvideo
drm_kms_helper        139264  1 vboxvideo
drm                   360448  5 ttm,drm_kms_helper,vboxvideo
syscopyarea            16384  2 drm_kms_helper,vboxvideo
sysfillrect            16384  2 drm_kms_helper,vboxvideo
sysimgblt              16384  2 drm_kms_helper,vboxvideo
vboxguest             282624  8 vboxsf,vboxvideo
```

Reference:  
https://www.hiroom2.com/2017/07/02/debian-9-vbox-guest-additions-en/

# Install Valgrind on macOS High Sierra
In order to do the build, you need automake, so let's install that first:
```
$ brew install automake
```
After that, we can build the current valgrind repository:
```
$ git clone git://sourceware.org/git/valgrind.git
$ cd valgrind
$ ./autogen.sh
$ ./configure --prefix=$HOME/.valgrind
$ make
$ make install
```
Valgrind is now built, and to start using it, we simply add it to our PATH environment:
```
$ vim .zshrc
```
> Add `$HOME/.valgrind/bin:` to the export PATH - you can put it before the `$PATH`

Thats it! You can safely remove the git clone at this point and start using valgrind. 

Reference:  
http://valgrind.org/downloads/repository.html
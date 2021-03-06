
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
$ sudo apt-get install vim
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

## Add Shared Folder to Debian Virtual Machine
For VirtualBox with a Linux guest, there are a few steps to mount a shared folder.In order to use shared folder functionality few prerequisites need to be met:

- Make sure that Guest Additions are properly installed on the guest OS.
- Users in a guest Ubuntu must be in the group vboxsf to be able to access shares.
- Define a directory on the host that will be used in the virtual machine using the settings dialogue of Virtual Box.
- Do not share personal folders like /home/username or My Documents.
- Avoid special characters or empty spaces in the path to the shared folder
- Use different names for share and mountpoint.
- Create a mountpoint on the guest OS (best in your HOME directory).

Testing shared folders functionality can thus be done by creating a shared directory on the host (e.g. ~/share), define this as a shared folder for your guest system in Virtual Box settings, create a mount point in your guest os (e.g.  mkdir ~/host) and mount this in the guest OS with the command:
```
$ sudo mount -t vboxsf -o rw,uid=1000,gid=1000 share ~/host
```

Now it works!  Unfortunately, a restart unmounts the shared folder.  So a workaround is to have the mount command run on login.

1. Create a file for your login script and save your script in `/etc/profile.d/`
```
$ sudo vim /etc/profile.d/<NAME_OF_SCRIPT>
```
**Example script**
```
#!/bin/sh
sudo mount -t vboxsf -o rw,uid=1000,gid=1000 share ~/host
```
2. Make the script executable
```
sudo chmod 755 /etc/init.d/<NAME_OF_SCRIPT>
```

Reference:  
https://askubuntu.com/questions/30396/error-mounting-virtualbox-shared-folders-in-an-ubuntu-guest

# Install Valgrind on macOS High Sierra
As of March 2018, Valgrind is **NOT** officially supported on macOS High Sierra.  However, we can get it to work if we build the current repo ourselves.  

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

# C Tips
Show files in binary **(replace `libft.a` with your binary)**
```
ar -t libft.a
```

# Brew Issues
My particular issue did not allow me to use brew.  It would complain that it was running an update process, but restarts and so forth would not work.  The problem turned out to be an issue with the sym links, so the following line fixed it for me:
```
ln -s /tmp/.$(whoami)-brew-locks $HOME/.brew/var/homebrew/locks
``` 
### Install Brew
This is the process Marvin instructs to install brew on the iMacs:
```
mkdir $HOME/.brew && curl -fsSL https://github.com/Homebrew/brew/tarball/master | tar xz --strip 1 -C $HOME/.brew
mkdir -p /tmp/.$(whoami)-brew-locks
mkdir -p $HOME/.brew/var/homebrew
ln -s /tmp/.$(whoami)-brew-locks $HOME/.brew/var/homebrew/locks
export PATH="$HOME/.brew/bin:$PATH"
brew update && brew upgrade
```
And then we need to add the following to our zsh:
```
mkdir -p /tmp/.$(whoami)-brew-locks
export PATH="$HOME/.brew/bin:$PATH"
```

# Docker
Install docker using brew:
```
$ brew install docker docker-machine
```

### VboxManage Error
If you get the following error when trying to run docker:
```
$ docker-machine create -d virtualbox Char
Error with pre-create check: "VBoxManage not found. Make sure VirtualBox is installed and VBoxManage is in the path"
```

If you have VirtualBox installed already, then simply add the VBoxManage to your path:
```
export PATH=/Applications/Virtualbox.app/Contents/MacOS/:$PATH;
```

# Speed up zsh in iterm
1. Open iTerm and open up **Preferences** (cmd+,)

1. Click on **Profiles**

1. In **General** look at the **Command** section, and in **Command** type in: `zsh`

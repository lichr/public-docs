Setup Development Environment: VirtualBox + Fedora + I3
=======================================================
Summary
-------
- create virtual box VM
- install fedora
- install i3
- install vscode


Install fedora
--------------

### disable Wayland
- edit **/etc/gdm/custom.conf**
    ~~~conf
    # Uncoment the line below to force the login screen to use Xorg
    WaylandEnable=false
    ~~~
- reboot

### install virtual box guest addition
[reference](https://www.if-not-true-then-false.com/2010/install-virtualbox-guest-additions-on-fedora-centos-red-hat-rhel/)

- update kernel
    ~~~sh
    dnf update kernel*
    reboot
    ~~~

- in virtual box, click: **devices -> insert guest additions cd image**
- install guest additions
    ~~~sh
    # install dependencies
    dnf install gcc kernel-devel kernel-headers dkms make bzip2 perl

    # set KERN_DIR
    export KERN_DIR=/usr/src/kernels/`uname -r`/build

    # mount cd image
    mkdir /media/VirtualBoxGuestAdditions
    mount -r /dev/cdrom /media/VirtualBoxGuestAdditions

    # run installer
    cd /media/VirtualBoxGuestAdditions
    ./VBoxLinuxAdditions.run

    # reboot
    reboot
    ~~~

### Enable file sharing between host and guest (optional)

[reference](https://forums.virtualbox.org/viewtopic.php?t=15868)

- In vm's shared folder setting, add new shared folder
- In guest system
    ~~~
    sudo mount -t vboxsf -o rw,uid=1000,gid=1000 share ~/host
    ~~~

    > If you want to have it mount automatically upon each boot, put the mount command in /etc/rc.local (Debian based distro's), or whatever script is run at the end of the boot process. The Shared Folders service should mount them automatically, but that doesn't always happen.

Install i3
----------
- prepare
    - ~/Pictures/wallpaper.jpg
- install
    ~~~sh
    dnf install -y i3 feh
    ~~~
- customize
    edit **/.config/i3/config**
    ~~~conf
    # start a terminal
    bindsym $mod+Return exec gnome-terminal

    # my settings
    bindsym $mod+z exec firefox --new-window
    bindsym $mod+x exec nautilus --new-window
    bindsym $mod+m exec feh --bg-fill /home/lichr/Pictures/wallpaper.jpg
    exec feh --bg-fill /home/lichr/Pictures/wallpaper.jpg
    ~~~

install vscode
--------------
[reference](https://code.visualstudio.com/docs/setup/linux)
- install key and repository
    ~~~sh
    sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
    sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'
    ~~~
- install
    ~~~sh
    dnf check-update
    sudo dnf install code
    ~~~
- update (optional)
    ~~~sh
    sudo dnf update code
    ~~~

### Increase watch file limitation

[reference](https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc)

- View current watch limitation
    ~~~
    cat /proc/sys/fs/inotify/max_user_watches
    ~~~

- edit: **/etc/sysctl.conf**
    ~~~conf
    fs.inotify.max_user_watches=524288
    ~~~
    > While 524288 is the maximum number of files that can be watched, if you're in an environment that is particularly memory constrained, you may wish to lower the number. Each file watch takes up 540 bytes (32-bit) or ~1kB (64-bit), so assuming that all 524288 watches are consumed that results in an upper bound of around 256MB (32-bit) or 512MB (64-bit).

- enable the config
    ~~~
    sudo sysctl -p
    ~~~

- reboot


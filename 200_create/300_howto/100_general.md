# General

In this section we explain howtos that are not specific to any template
or target but apply to all of them. Most relate to firstboot scripts.

* This will become a table of contents (this text will be scraped).
{:toc}

## How to uncompress/extract downloaded appliances

To reduce bandwidth and downloading time, all appliances are compressed
in the [gzip][wiki-gzip] [tarball][wiki-tar] format (.tar.gz).


### Unix/Linux/Mac users

Users can unpack .tar.gz files with built-in system tools. Examples of
such graphical applications include [Ark][wiki-ark] and [File
Roller][wiki-fileroller], which are included in KDE and GNOME
respectively. Alternatively, you can extract the .tar.gz file with the
following command in the console terminal:

    $ tar xf <FILE_NAME>

For example, the compressed archive file `LAMP_Server.i686-1.0.0.vmx.tar.gz` 
can be extracted in verbose mode by executing:

    $ tar xvf LAMP_Server.i686-1.0.0.vmx.tar.gz
    LAMP_Server-1.0.0/
    LAMP_Server-1.0.0/LAMP_Server.i686-1.0.0.vmdk
    LAMP_Server-1.0.0/LAMP_Server.i686-1.0.0.vmx


### Windows users

Users will need to install 3rd party utilities like WinZip,
[WinRAR][winrar], or [7-Zip][7-Zip] (freeware).

These tools have File Explorer integration and a graphical user
interface, but they are unable to uncompress the .tar.gz in one step -
you need to extract it twice, first for gzip uncompression and then tar
extraction.

NOTE: If you get a "corrupt archive" error message during tar extraction
even though the archive MD5 checksum matches, it is likely that the tar
file contains files larger than 8 GB. You need WinRAR 3.9 and above, or
7-zip 9.16 beta and above. [LibArchive/BSD tar][libarchive] also works.

We recommend using [LibArchive/BSD tar][libarchive-tool] (freeware). It
only has a command line interface, but is easy to use:

`$ bsdtar.exe -xf <FILE_NAME>`

Here is a step-by-step guide for
`LAMP_Server.i686-1.0.0.vmx.tar.gz`:

1. Download and install LibArchive:
   [http://downloads.sourceforge.net/gnuwin32/libarchive-2.4.12-1-setup.exe]
[libarchive2].
2. Add the LibArchive path (default is `C:\Program Files\GnuWin32\bin`)
   to your [system %PATH% variable][win-sys-path].
3. Extract the tarball by executing the following in the command
   terminal: `$ bsdtar.exe -xf LAMP_Server.i686-1.0.0.vmx.tar.gz`
The files will be unpacked to the `LAMP_Server-1.0.0` directory.

Note: Legacy Windows file systems like FAT32 have several limitations,
in particular the lack of sparse file support (required by some build
formats, such as Xen) and a maximum file size of 4 GB.


## How to login for the first time

* Use the SUSE Studio openSUSE default root password of linux

    `username: root or tux`
    `password: linux`

* You can change this in SUSE Studio on the configuration page
  'susestudio.com/appliance/edit/NNNN#tab-configuration'


## How to install proprietary drivers on first boot

Write a script that uses lspci and grep commands to see which
hardware you have. Then use zypper to add a repo and
install the needed rpms. Once you have this script, upload it to the
overlay section and add the script to the firstboot section. (See How
to run firstboot scripts.)


## How to run local scripts

* If you want your script to run during every boot, either use the "Scripts" 
  option on the "Configuration" Tab or write your own service.
* By using the "Scripts" option, your script will be run before any
  services are started. If that is want you want, check the "Run script 
  whenever the appliance boots" option and write your script.
* If you to run your script after the other services,
  create your own service. To do so, copy the
  `/etc/init.d/skeleton` file and adapt it to your needs. Set the
  required_start to $ALL so it runs after all the other services. Then
  upload this file to the overlay section and add an "insserv
  script_name" line on the "firstboot section". (See How to run firstboot
  scripts.)


## How to run firstboot scripts

* Like "How to run local scripts ", firstboot scripts can be run before
  any services or after. For the first option, use the "Scripts" option
  on the "Configuration" tab. There is an "if" section that
  looks for a /etc/init.d/suse_studio_firstboot file. Put your commands
  there.
* For the second option, do like in "How to run local scripts" and make
  your own service. Add a "insserv script_name -r" line at the end of
  this service so it removes itself after firstboot.


## How to configure graphics on first boot

If you want to configure graphics on your firstboot, write a firstboot
script (see " How to run firstboot scripts ") that launches sax2, which
will configure your graphics hardware.


## How to add a user to the sudoers

Add the sudo rpm and upload a custom /etc/sudoers like the following in the
overlay section:

    Defaults always_set_home Defaults env_reset

    Defaults env_keep = "LANG LC_ADDRESS LC_CTYPE LC_COLLATE LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE LC_TIME LC_ALL LANGUAGE LINGUAS XDG_SESSION_COOKIE" LC_MEASUREMENT LC_MESSAGES LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE LC_TIME LC_ALL LANGUAGE LINGUAS XDG_SESSION_COOKIE XMODIFIERS GTK_IM_MODULE QT_IM_MODULE QT_IM_SWITCHER" Defaults targetpw # ask for the password of the target user i.e. root ALL ALL=(ALL) ALL # WARNING! Only use this together with 'Defaults targetpw'! root ALL=(ALL) ALL tux ALL=(ALL) NOPASSWD:ALL


## How to customize yast2-firstboot

* If you want to have your own yast2-firstboot, upload a file to 
/var/lib/YaST2/reconfig_system and edit the
  /etc/yast2-firstboot.xml file according to your needs. Make sure the
  yast2-firstboot package is installed.
* If you just want to run one module, you can simply add the line
  "yast2 modulename" at firstboot (see How to run firstboot scripts).


## How to set up crontab

Upload your custom crontab files to /etc/cron.daily, /etc/cron.hourly,
/etc/cron.weekly, /etc/cron/monthly. Check that you have the crontab rpm
installed.


## How to setup a custom firewall

You have two options. Either use the SuSEfirewall2 or the iptools.

    ** For the first option, edit the /etc/sysconfig/SuSEfirewall2 and
       the /etc/sysconfig/SuSEfirewall2.d files.
    ** If you prefer iptools, remove the SUSEfirewall2 package,
       install the iptools one and add a custom iptools script on boot
       (see How to run local scripts).


## How to add AutoYaST for automatic configurations while booting

* Copy an autoyast profile to /var/lib/autoinstall/autoconf/autoconf.xml.
* Create an /etc/install.inf file with "AutoYaST:" in it.
* Create an empty file at /var/lib/YaST2/runme_at_boot.
* More info on AutoYaST and how to create profiles at the AutoYaST homepage.


## How to add services

Run `insserv servicename` on firstboot (see How to run firstboot scripts).


## Minimal X (Icewm) howtos

These howtos are specific to the minimal X template. They basically
focus on creating KIOSK-like applications. There are different ways to
achieve this and different ways to control the window sizes. As the
Minimal X has the Icewm window manager, most of them relate to this
window manager.


## How to implement a KIOSK-like application without window manager

In some cases you may not want to use any window manager, for example if
your application can be run in fullscreen mode and it is a single
window application. If so, edit the `/etc/inittab` file and
the .bashrc file in the user home directory.

**/etc/inittab**

    # The default runlevel is defined here
    id:3:initdefault:

    # First script to be executed, if not booting in emergency (-b) mode
    si::bootwait:/etc/init.d/boot

    # /etc/init.d/rc takes care of runlevel handling
    #
    # runlevel 0  is  System halt   (Do not use this for initdefault!)
    # runlevel 1  is  Single user mode
    # runlevel 2  is  Local multiuser without remote network (e.g. NFS)
    # runlevel 3  is  Full multiuser with network
    # runlevel 4  is  Not used
    # runlevel 5  is  Full multiuser with network and xdm
    # runlevel 6  is  System reboot (Do not use this for initdefault!)
    #
    l0:0:wait:/etc/init.d/rc 0
    l1:1:wait:/etc/init.d/rc 1
    l2:2:wait:/etc/init.d/rc 2
    l3:3:wait:/etc/init.d/rc 3
    #l4:4:wait:/etc/init.d/rc 4
    l5:5:wait:/etc/init.d/rc 5
    l6:6:wait:/etc/init.d/rc 6

    # what to do in single-user mode
    ls:S:wait:/etc/init.d/rc S
    ~~:S:respawn:/sbin/sulogin

    # what to do when CTRL-ALT-DEL is pressed
    ca::ctrlaltdel:/sbin/shutdown -r -t 4 now

    # special keyboard request (Alt-UpArrow)
    # look into the kbd-0.90 docs for this
    kb::kbrequest:/bin/echo "Keyboard Request -- edit /etc/inittab to let this work."

    # what to do when power fails/returns
    pf::powerwait:/etc/init.d/powerfail start
    pn::powerfailnow:/etc/init.d/powerfail now
    #pn::powerfail:/etc/init.d/powerfail now
    po::powerokwait:/etc/init.d/powerfail stop

    # for ARGO UPS
    sh:12345:powerfail:/sbin/shutdown -h now THE POWER IS FAILING

    # getty-programs for the normal runlevels
    # «id»:«runlevels»:«action»:«process»
    # The "id" field  MUST be the same as the last
    # characters of the device (after "tty").

    1:2345:respawn:/sbin/mingetty --autologin tux --delay 1 tty1   # <<------CHANGED LINE--

    3:2345:respawn:/sbin/mingetty tty3
    4:2345:respawn:/sbin/mingetty tty4
    5:2345:respawn:/sbin/mingetty tty5
    6:2345:respawn:/sbin/mingetty tty6
    #
    #S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
    #cons:1235:respawn:/sbin/smart_agetty -L 38400 console

    #
    #  Note: Do not use tty7 in runlevel 3, this virtual line
    #  is occupied by the programm xdm.
    #

    #  This is for the package xdmsc, after installing and
    #  and configuration you should remove the comment character
    #  from the following line:
    #7:3:respawn:+/etc/init.d/rx tty7


    # modem getty.
    # mo:235:respawn:/usr/sbin/mgetty -s 38400 modem

    # fax getty (hylafax)
    # mo:35:respawn:/usr/lib/fax/faxgetty /dev/modem

    # vbox (voice box) getty
    # I6:35:respawn:/usr/sbin/vboxgetty -d /dev/ttyI6
    # I7:35:respawn:/usr/sbin/vboxgetty -d /dev/ttyI7

    # end of /etc/inittab


**/home/tux/.bashrc:**

    while true do WINDOWMANAGER=/home/tux/bin/start.sh startx done


##     How to implement a KIOSK-like application with window manager

* If you want to use a window manager, select runlevel 5 in the Configuration 
section and add an
  autostart program for user tux. This will overwrite the .xinitrc file
  of user tux.
* If you want this autostart program to run for all users, copy the
  generated /home/tux/.xinitrc to the /etc/skeleton/.xinitrc file.


## How to launch an application in fullscreen mode

Some applications may not be started in fullscreen mode. One way to control
this behavior is to use the icewm-ctrl utility in the `.xinitrc` (see How
to implement a KIOSK-like application with window manager). The icewm-ctrl 
utility needs the window id.

Here is an example of how to achieve this with the gbrainy application :

    gbrainy & while [ `wmctrl -l | grep gbrainy | wc -l` != '1' ]; do echo 
"waiting for gbrainy" » /tmp/start.sh.log ;done wmctrl -r gbrainy -b 
toggle,fullscreen echo "done" »» /tmp/start.sh.log

## How to customize icewm taskbar and windows

* Icewm can be customized in many ways, including the
  taskbar, window size and design. To customize the window manager
  for user tux, add the needed files to the
  .icewm directory. If you want to apply these changes to all users, copy this 
directory to
  `/etc/skeleton/`.
* The easiest way to create these files is to use the icewm control
  panel application. Add the icewmcp rpm to your
  appliance, then build it and run it. Open an xterm and run icewmcp and
  customize your icewm. After that, copy the `.icewm` to the overlay
  section.


## How to add X for JeOS and Server templates

* To get X up and running for JeOS and Server-based templates,
  you have to add several packages to your appliance:

       - xorg-x11-server - xorg-x11-driver-video - xorg-x11-fonts - xorg-x11-
driver-input - sax2

* Then go to the Configuration tab section and, under Startup, choose
  5:Graphical login.
* Note: by default you will have IceWM as window manager!



[wiki-gzip]: http://en.wikipedia.org/wiki/Gzip
[wiki-tar]: http://en.wikipedia.org/wiki/Tar_(file_format)
[wiki-ark]: http://en.wikipedia.org/wiki/Ark_(software)
[wiki-fileroller]: http://en.wikipedia.org/wiki/File_Roller
[winrar]: http://www.rarlab.com/
[7-zip]: http://www.7-zip.org/
[libarchive]: http://gnuwin32.sourceforge.net/packages/libarchive.htm
[libarchive2]: http://downloads.sourceforge.net/gnuwin32/libarchive-2.4.12-1-setup.exe
[libarchive-tool]: http://gnuwin32.sourceforge.net/packages/libarchive.htm
[win-sys-path]: http://vlaurie.com/computers2/Articles/environment.htm#editing

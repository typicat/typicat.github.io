# Post installation configuration OpenBSD
![Puffy OpenBSD logo](images/puffy.png)
### General Information
This is how I setup general desktop usage with OpenBSD on a ThinkPad x13.
Using -current with weekly sysupgrades unless something is broken.


- Mailing lists
[marc.info](https://marc.info)
[misc](https://marc.info/?l=openbsd-misc) - [tech](https://marc.info/?l=openbsd-tech) - [cvs](https://marc.info/?l=openbsd-cvs) - [bugs](https://marc.info/?l=openbsd-bugs)

Last update 03/09/2026

### 1. System configuration
rcctl enable apmd  # keeping this for zzz
rcctl set apmd flags=-A
```
as of lately my laptop has been running with apmd_flags=-A only with good results

Enable touchpad tapping
wsconsctl mouse.tp.tapping=1
Put into /etc/wsconsctl.conf to keep it at boot


#### sysctl.conf
```
```
kern.shminfo.shmmax=2147483647
kern.shminfo.shmall=524288
kern.shminfo.shmmni=2048
kern.shminfo.shmseg=2048

kern.seminfo.semmns=4096 
kern.seminfo.semmni=1024 

kern.maxproc=8192
kern.maxfiles=16384
kern.maxvnodes=100000

kern.somaxconn=1024 
net.inet.udp.recvspace=262144 
net.inet.udp.sendspace=262144
net.inet.tcp.mssdflt=1460 
net.inet.tcp.keepidle=300 
net.inet.ip.ifq.maxlen=4096

kern.audio.record=1
```

#### login.conf
Changes to staff only, I leave rest as is.
Keep in mind these values goes for every logged in user on the system, hence if more than 1 user I would not use these values.

``` usermod -L staff username ```

```
staff:\
	:datasize-cur=12288M:\
	:datasize-max=infinity:\
	:maxproc-max=4096:\
	:maxproc-cur=1024:\
	:openfiles-cur=8192:\
	:openfiles-max=16384:\
	:stacksize-cur=16M:\
	:stacksize-max=32M:\
	:ignorenologin:\
	:requirehome@:\
	:tc=default:```

### 3. X configuration
For X related errors after xenodm login ``` cat ~/.xsession-errors ```

#### .xsession
```
ulimit -Sc 0  # no app core dumping, rm if you want core dumps

export LANG=en_us.UTF-8
export BROWSER=firefox
export MOZ_WEBRENDER=1
export MOZ_ACCELERATED=1

eval `dbus-launch --sh-syntax`
if [ -x ${PREFIX}/bin/dbus-launch -a -z "${DBUS_SESSION_BUS_ADDRESS}" ]; then
          eval `dbus-launch --sh-syntax --exit-with-x11`
          fi

xrdb -merge .Xresources
xset b off
xset r rate 250 50 # ratio for speedy keyrate
xidle &
picom -b
exec cwm
```

#### .Xresources
```
Xft.autohint  : 0
Xft.antialias : 1
Xft.hinting   : 1
Xft.hintstyle : hintslight
Xft.lcdfilter : lcddefault
Xft.rgba      : rgb
Xft.dpi       : 96

XTerm*faceName          : xft:CaskaydiaCove Nerd Font:pixelsize=13
XTerm*allowBoldFonts    : false
XTerm*borderWidth       : 0
XTerm*internalBorder    : 10
XTerm*termName          : xterm-256color
XTerm*vt100.metaSendsEscape  : true
XTerm*vt100.scrollBar   : false
XTerm*renderFont        : true
XTerm*dynamicColors     : true
XTerm*charClass         : 33:48,36-47:48,58-59:48,61:48,63-64:48,95:48,126:48
XTerm*saveLines         : 10000
XTerm*highlightSelection: true
XTerm*metaSendsEscape   : true
XTerm*altSendsEscape    : true
XTerm*loginshell        : true

! set theme with #include "colorscheme-file"

Fonts goes into $HOME/.fonts
GTK themes goes into $HOME/.themes

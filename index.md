# Post installation configuration OpenBSD
![Puffy OpenBSD logo](images/puffy.png)
### 1. General Information
General desktop usage with OpenBSD on ThinkPad T450s.
Usually using -current with weekly sysupgrades unless something is broken.

No guarantees that all of this will work on your system. It is not a guide, but reference - will not cover installation of OpenBSD nor hardware support or how to setup wifi etc..

- Mailing lists
[misc](https://marc.info/?l=openbsd-misc) - [tech](https://marc.info/?l=openbsd-tech) - [cvs](https://marc.info/?l=openbsd-cvs) - [bugs](https://marc.info/?l=openbsd-misc)



### 2. System configuration
Tweaks for quieter and cooler system: [obsdfreqd](https://dataswamp.org/~solene/2022-03-21-openbsd-cool-frequency.html)
```
pkg_add obsdfreqd
rcctl enable obsdfreqd
rcctl set obsdfreqd flags=-T 85,55
rcctl enable apmd  # keeping this for zzz
rcctl set apmd flags=-L
```

Enable touchpad tapping
``` wsconsctl mouse.tp.tapping=1 ```
put ``` mouse.tp.tapping=1``` into /etc/wsconsctl.conf for persistens


##### sysctl.conf
Worth checking out: [reddit thread](https://www.reddit.com/r/openbsd/comments/exm01m/how_to_calculate_shared_memory_limits_and/)

```
kern.shminfo.shmall=6291456
kern.shminfo.shmmax=999999999
kern.shminfo.shmmni=2048

kern.shminfo.shmseg=2048
kern.seminfo.semmns=4096
kern.seminfo.semmni=1024

kern.maxproc=3250
kern.maxfiles=8192
kern.maxthread=5240

machdep.allowaperture=2

net.inet.udp.recvspace=262144
net.inet.udp.sendspace=262144
```
#### login.conf
Changes only, leave rest as is. [resource](https://sohcahtoa.org.uk/openbsd.html) 
``` usermod -L staff username ```
```
staff:\
datasize-cur=8192M:\
datasize-max=infinity:\
stacksize:16M:\
maxproc-max:512:\
maxproc-cur:1024:\
```

### 3. X configuration
For X related errors after xenodm login ``` cat ~/.xsession-errors ```

fvwm3 config: [github](https://github.com/typicat/dots/blob/main/config)


#### .xsession
```
ulimit -Sc 0  # no app core dumping, rm if you want core dumps

export MOZ_WEBRENDER=1
export MOZ_ACCELERATED=1
export LANG=en_us.UTF-8

eval `dbus-launch --sh-syntax`
if [ -x ${PREFIX}/bin/dbus-launch -a -z "${DBUS_SESSION_BUS_ADDRESS}" ]; then
          eval `dbus-launch --sh-syntax --exit-with-x11`
          fi

xrdb -merge .Xresources
xset dpms 0 0 0
xset s off
xset b off
xset r rate 350 35

xidle &
picom --config=$HOME/.config/picom.conf -b

exec fvwm3
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

XIdle*position : sw
XIdle*delay    : 1
XIdle*timeout  : 300

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

! set theme here
#include ".local/apprentice"
```


### 4. Software
```
pkg_add colorls zsh git curl clang-tools-extra neovim firefox lxappearance
```

#### .zshrc
```
export PATH=$HOME/.local/bin:$PATH
export CLICOLOR=1
export TERM=xterm-256color
export PAGER=less
export BROWSER=firefox

export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="robbyrussell"

plugins=(git)

source $ZSH/oh-my-zsh.sh

alias ls="colorls -F"
alias ll="ls -la"
alias l="ls -s"
alias la="ls -la"

alias vim="nvim"
```
#### picom.conf
``` 
backend="glx";
vsync=true;

mark-wmwin-focused = true;
mark-ovredir-focused = true;
detect-rounded-corners = true;
detect-client-opacity = true;
detect-transient = true;
use-damage=true;

glx-no-stencil = true;
glx-no-rebind-pixmap = true;

shadow=true;
shadow-opacity=0.5;
shadow-execlude = [
    "name = 'firefox' && argb",
    "_GTK_FRAME_EXTENTS@:c"

]

wintypes:
{
  tooltip = { shadow=false; full-shadow=false; };
  dock = { shadow=false; clip-shadow-above=true; }
  dnd = { shadow=false; }
  popup_menu = { shadow=false; }
  dropdown_menu = { shadow=false;; }
};
```

Fonts: ``` $HOME/.fonts/ ```

GTK themes:  ``` $HOME/.themes/ ```

Icons/cursors: ``` $HOME/.icons/ ```

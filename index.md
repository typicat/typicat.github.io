# Post installation configuration OpenBSD
![Puffy OpenBSD logo](images/puffy.png)
### General Information
This is how I setup general desktop usage with OpenBSD on ThinkPad T450s.
Using -current with weekly sysupgrades unless something is broken.


- Mailing lists
[marc.info](https://marc.info)
[misc](https://marc.info/?l=openbsd-misc) - [tech](https://marc.info/?l=openbsd-tech) - [cvs](https://marc.info/?l=openbsd-cvs) - [bugs](https://marc.info/?l=openbsd-bugs)

Last update 04/10/2025

### 1. System configuration
obsdfreqd for quieter and cooler system: [obsdfreqd](https://dataswamp.org/~solene/2022-03-21-openbsd-cool-frequency.html)
```
pkg_add obsdfreqd
rcctl enable obsdfreqd
rcctl set obsdfreqd flags=-T 85,55
rcctl enable apmd  # keeping this for zzz
rcctl set apmd flags=-L
```
as of lately my laptop has been running with apmd_flags=-A only with good results

Enable touchpad tapping
``` wsconsctl mouse.tp.tapping=1 ```
Put ``` mouse.tp.tapping=1``` into /etc/wsconsctl.conf to keep it at boot



#### sysctl.conf
Worth checking out: [reddit thread](https://www.reddit.com/r/openbsd/comments/exm01m/how_to_calculate_shared_memory_limits_and/)
```
kern.maxproc=3250
kern.maxfiles=8192
kern.maxthread=5240

kern.seminfo.semmni=1024
kern.seminfo.semmns=4096
kern.shminfo.shmseg=1024

net.inet.udp.recvspace=262144
net.inet.udp.sendspace=262144
```

#### login.conf
Changes only, leave rest as is.
Keep in mind these values goes for every logged in user on the system, hence if more than 1 user I would not use these values.

``` usermod -L staff username ```
```
staff:\
datasize-cur=15G:\
datasize-max=infinity:\
stacksize:32M:\
maxproc-max:512:\
maxproc-cur:1024:\
```

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


```
### 4. Software
```
pkg_add colorls zsh git curl clang-tools-extra neovim firefox lxappearance fzf
```

#### .zshrc
```
export PATH=$HOME/.local/bin:$PATH
export CLICOLOR=1
export TERM=xterm-256color
export PAGER=less

export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="robbyrussell"

plugins=(git fzf)

source $ZSH/oh-my-zsh.sh
eval "$(fzf --zsh)"

alias ls="colorls -Fh"

alias vim="nvim"

```
#### picom.conf
``` 
backend="xrender"; # or xrender
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
shadow-exclude = [
    "_GTK_FRAME_EXTENTS@:c"
]

wintypes:
{
  tooltip = { shadow=false; full-shadow=false; };
  dock = { shadow=false; }
  dnd = { shadow=false; }
  popup_menu = { shadow=false; }
  dropdown_menu = { shadow=false; }
  utility = { shadow=false; }
};
```

Fonts goes into ``` $HOME/.fonts/ ```

GTK themes goes into:  ``` $HOME/.themes/ ```

Icons/cursors goes into: ``` $HOME/.icons/ ```

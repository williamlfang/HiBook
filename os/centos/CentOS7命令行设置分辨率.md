
> **[info]**
>`CentOS` 可以通过命令行来设置（修改）屏幕分辨率大小。使用的命令是
> 
> xrandr

## xrandr 命令

<!--more-->

```bash
[trader@localhost ~]$ xrandr --help
usage: xrandr [options]
  where options are:
  --display <display> or -d <display>
  --help
  -o <normal,inverted,left,right,0,1,2,3>
            or --orientation <normal,inverted,left,right,0,1,2,3>
  -q        or --query
  -s <size>/<width>x<height> or --size <size>/<width>x<height>
  -r <rate> or --rate <rate> or --refresh <rate>
  -v        or --version
  -x        (reflect in x)
  -y        (reflect in y)
  --screen <screen>
  --verbose
  --current
  --dryrun
  --nograb
  --prop or --properties
  --fb <width>x<height>
  --fbmm <width>x<height>
  --dpi <dpi>/<output>
  --output <output>
      --auto
      --mode <mode>
      --preferred
      --pos <x>x<y>
      --rate <rate> or --refresh <rate>
      --reflect normal,x,y,xy
      --rotate normal,inverted,left,right
      --left-of <output>
      --right-of <output>
      --above <output>
      --below <output>
      --same-as <output>
      --set <property> <value>
      --scale <x>x<y>
      --scale-from <w>x<h>
      --transform <a>,<b>,<c>,<d>,<e>,<f>,<g>,<h>,<i>
      --off
      --crtc <crtc>
      --panning <w>x<h>[+<x>+<y>[/<track:w>x<h>+<x>+<y>[/<border:l>/<t>/<r>/<b>]]]
      --gamma <r>:<g>:<b>
      --brightness <value>
      --primary
  --noprimary
  --newmode <name> <clock MHz>
            <hdisp> <hsync-start> <hsync-end> <htotal>
            <vdisp> <vsync-start> <vsync-end> <vtotal>
            [flags...]
            Valid flags: +HSync -HSync +VSync -VSync
                         +CSync -CSync CSync Interlace DoubleScan
  --rmmode <name>
  --addmode <output> <name>
  --delmode <output> <name>
  --listproviders
  --setprovideroutputsource <prov-xid> <source-xid>
  --setprovideroffloadsink <prov-xid> <sink-xid>
  --listmonitors
  --listactivemonitors
  --setmonitor <name> {auto|<w>/<mmw>x<h>/<mmh>+<x>+<y>} {none|<output>,<output>,...}
  --delmonitor <name>
```

## 显示当前桌面的分辨率

直接使用命令 `xrandr` 来查看当前的分辨率大小：

```bash
VGA-1 connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 476mm x 267mm
   1600x900      60.00  
   1280x1024     75.02    60.02  
   1152x864      75.00  
   1024x768      75.03    60.00  
   800x600       75.00    60.32  
   640x480       75.00    59.94  
   1920x1080_60.00  59.96* 
```

其中标记 `*` 的就是当前的参数设置。我们可以看到当前系统可以支持多个显示设置。

## 选择某个设置

使用选项 `-s` 来指定某个设置

```bash
xrandr -s 0 // 1600x900
```

## 直接设置分辨率

也可以在命令行直接指定设置：

```bash
xrandr -s 1920x1080_60.00
```

## `errors. BadName`

```bash
Xrandr errors. BadName (named color or font does not exist)
```

出现这个问题，一般是由于之前已经有一个显示的配置了，导致重命名。

>**[danger]** 
> 
> I had a similar problem, I believe it was because I had already created that setting before (then rebooted). If I skip that stage and go straight to:
> 

可以参考 [SO: Xrandr errors. BadName (named color or font does not exist) [closed]
](https://stackoverflow.com/questions/851704/xrandr-errors-badname-named-color-or-font-does-not-exist)

## 集成脚本`display.sh`

```bash
## 首次需要建立一个 --newmode
## 以后就不需要了，可以注释掉
## ------------------------
xrandr --newmode "1920x1080_20.00"  173.00  1920 2048 2248 2576  1080 1083 1088 1120 -hsync +vsync

xrandr --addmode VGA-1 "1920x1080_20.00"

xrandr -s 1920x1080
```

## 开机自动调整分辨率

把以上的 `display.sh` 添加到 `~/.bashrc` 配置文件(run configure)。这样，每次开机后，会优先读取 `.bashrc` 文件，然后启动 `display.sh`。

```bash
echo "bash ~/Desktop/display.sh" >> ~/.bashrc
```

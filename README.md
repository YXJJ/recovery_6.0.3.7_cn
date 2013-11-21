cwm-recovery-6.0.3.7中文源码

作者：Ruling

采用Ruby生成字库方法汉化（感谢最初做出汉化代码的大神）

编译recovery以及汉化全过程。

1、创建一个存放源码的目录(我创建的目录名字叫cm10.1)，cm10.1要改为自己的源码目录,下面每一步也一样
mkdir -p ~/cm10.1
然后定位到目录
cd ~/cm10.1
输入以下代码:
PATH=~/bin:$PATH
用repo获取列表
CM10.1命令
repo init -u git://github.com/CyanogenMod/android.git -b cm-10.1
repo sync -j16
2、同步完成后，执行下面命令，完成环境的安装
获取预编译应用
命令:
cd ~/cm10.1/vendor/cm
命令:
./get-prebuilts
准备指定设备的代码
当源代码下载完成后，输入：
命令（.后面是有个空格的）:
cd ~/cm10.1
. build/envsetup.sh
3、下一步，make我们后面要用到的工具
make -j16 otatools
会生成out目录，在/out/host/linux-x86/bin中有后面要用到的工具。
4、将/out/host/linux-x86/bin加入环境变量，变量具体位置要改成你的：
gedit ~/.bashrc
在文本最后添加：
export PATH=$PATH:~/cm10.1/out/host/linux-x86/bin
保存退出，执行下面命令，让刚加的环境变量生效：
source ~/.bashrc
5、由内核获取Device
build/tools/device/mkvendor.sh htc m4 boot.img
6、开始编译，执行命令(把cp2dug替换为自己的机型):
. build/tools/device/makerecoveries.sh full_m4-eng
下一步命令，编译开始：
make -j16 recoveryimage
输出的目录为：out/target/product/endeavoru/recovery.img文件，就是我们要的。

注释：
recovery_ui.c 这个文件里面是手机各按键对应的按键值，这样写入之后才能正确选择按键。
参考资料：
Porting CWM to other devices：
http://www.acsyndicate.net/how-t … er-devices-windows/
Creating a custom ClockWorkMod (CWM)Recovery image：
http://www.twobitcoder.com/?p=158
Porting Clockwork Recovery to ourGarminfone：
http://mygarminfone.blogspot.com … ecovery-to-our.html
Porting Clockwork Recovery to New Devices：
http://www.koushikdutta.com/2010 … ecovery-to-new.html


7、汉化
1)、准备工作：
a、获取字库生成脚本以及fonts文件
https://github.com/ruling/recovery_6.0.3.7_cn.git
字库生成文件在minui下：
graphics.c
chinese.c
chinese.h
这三个是已经修改好的c和头文件;
你需要手动创建fonts文件夹，放一个.ttf文件，命名为a.ttf
chinese_gen.rb 是ruby下的执行脚本，需要安装ruby运行环境：
b、安装ruby运行环境，在终端下操作：
sudo apt-get install ruby1.9.1 ruby1.9.1-dev libmagickwand-dev gperf
sudo gem1.9.1 install rmagick
fonts自己找字体就是了;
准备工作做好了，开始正是工作，汉化分三步
2)、进入recovery主目录，依次打开各个目录下的.c文件，找到print、MENU_ITEM、MENU_HEADERS 等字样，适当翻译成汉语。
3)、进入minui目录执行：
ruby chinese_gen.rb 10 18 18
自动生成相关c文件、头文件、以及字库
将生成的文件复制到源代码里面的minui目录
4)、修改minui下的Android.mk，在LOCAL_SRC_FILES := events.c resources.c 后加上chinese.c，修改后就是：LOCAL_SRC_FILES := events.c resources.c chinese.c
5)、修改Device下的BoardConfig.mk，加上BORAD_RECOVEY_USE_CHINESE := true ，强制使用中文，然后修改BOARD_USE_CUSTOM_RECOVERY_FONT := \"font_10x18.h\"来改变默认字体大小
6)、编译调试
重新编译recovery，刷入，检查各个部分是否汉化完全。

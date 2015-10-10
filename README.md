# mac_plugin
mac_public_plugin
mac类似yum、apt-get的工具:homebrew、port
#homebrew 安装
直接到http://brew.sh/index_zh-cn.html获取ruby安装命令即可
mac中远程连接终端item2 是一款不错的工具 支持lrzsz工具，还有各种快捷键
#item2 install
下载地址：http://www.iterm2.cn/ 
下载后直接安装即可，然而rz/sz命令并不可以用一直停留在错误提示环节。
解决方案：安装lrzsz工具
#item2 lrzsz
brew install lrzsz
安装
iterm2-recv-zmodem.sh、iterm2-send-zmodem.sh两个脚本，放到/usr/local/bin/目录下
然后修改配置文件

iterm2-recv-zmodem.sh

#!/bin/bash
# Author: Matt Mastracci (matthew@mastracci.com)
# AppleScript from http://stackoverflow.com/questions/4309087/cancel-button-on-osascript-in-a-bash-script
# licensed under cc-wiki with attribution required
# Remainder of script public domain
 
FILE=`osascript -e 'tell application "iTerm" to activate' -e 'tell application "iTerm" to set thefile to choose folder with prompt "Choose a folder to place received files in"' -e "do shell script (\"echo \"&(quoted form of POSIX path of thefile as Unicode text)&\"\")"`
if [[ $FILE = "" ]]; then
    echo Cancelled.
    # Send ZModem cancel
    echo -e \\x18\\x18\\x18\\x18\\x18
    echo \# Cancelled transfer
    echo
else
    echo $FILE
    cd "$FILE"
    #下面，修改为lrzsz安装目录的命令 homebrew安装无需修改路径
    /usr/local/bin/rz
    echo \# Received $FILE
    echo
fi

iterm2-send-zmodem.sh

#!/bin/bash
# Author: Matt Mastracci (matthew@mastracci.com)
# AppleScript from http://stackoverflow.com/questions/4309087/cancel-button-on-osascript-in-a-bash-script
# licensed under cc-wiki with attribution required
# Remainder of script public domain
 
FILE=`osascript -e 'tell application "iTerm" to activate' -e 'tell application "iTerm" to set thefile to choose file with prompt "Choose a file to send"' -e "do shell script (\"echo \"&(quoted form of POSIX path of thefile as Unicode text)&\"\")"`
if [[ $FILE = "" ]]; then
    echo Cancelled.
    # Send ZModem cancel
    echo -e \\x18\\x18\\x18\\x18\\x18
    echo \# Cancelled transfer
    echo
else
    echo $FILE
    #同上面
    /usr/local/bin/sz "$FILE"
    echo \# Received $FILE
    echo
fi

修改iTerm2的default trigger （command+,）即可 profiles=>Advanced=>trigger=>edit
添加如下规则
Regular expression: \*\*B0100
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-send-zmodem.sh
 
Regular expression: \*\*B00000000000000
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-recv-zmodem.sh
大功告成

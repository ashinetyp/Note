1#
裝 Android Studio 會包含SDK跟Platform tool以及獨立的Java環境,還有usb debug driver
https://developer.android.com/studio

2#
裝 Homebrew 透過這個command line tool,才能裝相容於M1/M2的舊版的java
https://brew.sh/index_zh-tw

3#
透過 homebrew 裝git
brew install git 
更新設定檔
export PATH="/usr/local/bin:$PATH"

4#
透過 homebrew 裝快速切換java環境的工具, 由於RocketChat sdk需要舊版java
https://github.com/jenv/jenv
brew install jenv

更新設定檔
使用Bash的情况
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(jenv init -)"' >> ~/.bash_profile
使用Zsh的情况
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
$ echo 'eval "$(jenv init -)"' >> ~/.zshrc

安裝java8
brew install --cask temurin8
安裝java11
brew install --cask temurin

查看目前有安裝的java的位置
/usr/libexec/java_home -v

將加入的另一個java環境加入設定中
$ jenv add /System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home/

查看目前環境有安裝的java版號
jenv versions
切換版號(例如11.0.2)
jenv global 11.0.2

5# optional
拉RocketChat的舊版code做參考
https://github.com/RocketChat/Rocket.Chat.Android
需要使用java 8
brew install --cask temurin8
jenv global temurin64-1.8.0.362

6# optional
拉RocketChat的現行reactNative做參考
https://github.com/RocketChat/Rocket.Chat.ReactNative
需要用最新版java (11.0是ok的)
brew install --cask temurin
jenv global 11.0

安裝node
brew install node
安裝yarn
brew install yarn
裝整包案子相依插件yarn install
跑起來yarn android

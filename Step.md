1#<br>
裝 Android Studio 會包含SDK跟Platform tool以及獨立的Java環境,還有usb debug driver<br>
https://developer.android.com/studio<br>
<br>
2#<br>
裝 Homebrew 透過這個command line tool,才能裝相容於M1/M2的舊版的java<br>
https://brew.sh/index_zh-tw<br>
<br>
3#<br>
透過 homebrew 裝git<br>
brew install git <br>
更新設定檔<br>
export PATH="/usr/local/bin:$PATH"<br>
<br>
4#<br>
透過 homebrew 裝快速切換java環境的工具, 由於RocketChat sdk需要舊版java<br>
https://github.com/jenv/jenv<br>
brew install jenv<br>

更新設定檔<br>
使用Bash的情况<br>
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile<br>
$ echo 'eval "$(jenv init -)"' >> ~/.bash_profile<br>
使用Zsh的情况<br>
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc<br>
$ echo 'eval "$(jenv init -)"' >> ~/.zshrc<br>

安裝java8<br>
brew install --cask temurin8<br>
安裝java11<br>
brew install --cask temurin<br>

查看目前有安裝的java的位置<br>
/usr/libexec/java_home -v<br>

將加入的另一個java環境加入設定中<br>
$ jenv add /System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home/<br>

查看目前環境有安裝的java版號<br>
jenv versions<br>
切換版號(例如11.0.2)<br>
jenv global 11.0.2<br>

5# optional<br>
拉RocketChat的舊版code做參考<br>
https://github.com/RocketChat/Rocket.Chat.Android<br>
需要使用java 8<br>
brew install --cask temurin8<br>
jenv global temurin64-1.8.0.362<br>

6# optional<br>
拉RocketChat的現行reactNative做參考<br>
https://github.com/RocketChat/Rocket.Chat.ReactNative<br>
需要用最新版java (11.0是ok的)<br>
brew install --cask temurin<br>
jenv global 11.0<br>

安裝node<br>
brew install node<br>
安裝yarn<br>
brew install yarn<br>
裝整包案子相依插件yarn install<br>
跑起來yarn android<br>

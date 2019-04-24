# front React-Native
## react-native环境搭建
### 1. jdk
`https://download.oracle.com/otn-pub/java/jdk/8u191-b12/2787e4a523244c269598db4e85c51e0c/jdk-8u191-windows-x64.exe`
### 2. node
```
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global

#### 安装yarn代替npm
npm install -g yarn react-native-cli

#### 安装完 yarn 后同理也要设置镜像源：
yarn config set registry https://registry.npm.taobao.org --global
yarn config set disturl https://npm.taobao.org/dist --global
```

### 3. python2(目前【2019-01-14】最新版：2.7.15)
`https://www.python.org/downloads/release/python-2715/`
下载地址：`https://www.python.org/ftp/python/2.7.15/python-2.7.15.amd64.msi`


## 问题
1.`preview is unaliveable until after a successful project sync`
解决方案：`https://blog.csdn.net/ancientear/article/details/81091342`

2.安装完成后，最好重启下电脑

3.`unable to load script from assets 'index.android.bundle'`
执行：`react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/`

4.开启热更新，

真机测试需要摇动手机，模拟器的话是menu键（windows: ctrl + M ，macOS: command + M）

设置host:port（例如我的是192.168.1.129:8081）

5.vs code安装react-native tools插件，使用vs感觉比较方便~

6.title居中问题
设置：`headerTitleStyle:{flex:1,textAlign:'center'}`

7.同时显示头部与底部tabbar问题

8.页面传值问题

9.A problem occurred configuring project ':app'，还提示找不到SDK目录，我的目录是存在的
查看了之前的项目，需要新建local.properties文件，里面写上：`sdk.dir=D\:\\Android\\SDK`

10.null is not an object evaluating 'RNGestureHandlerModule.State'
yarn add react-native-gesture-handler
react-native link

11.RNCWebView was not found in the UIManager
```
yarn add react-native-webview
react-native link react-native-webview #安装完成，需要关联下
```
关联成功后，需要修改settings.gradle文件中的“\”为“/”，否则路径错误。

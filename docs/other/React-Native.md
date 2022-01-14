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

1. `preview is unaliveable until after a successful project sync`
解决方案：`https://blog.csdn.net/ancientear/article/details/81091342`

2. 安装完成后，最好重启下电脑

3. `unable to load script from assets 'index.android.bundle'`（新版已修复）
执行：`react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/`

4. 开启热更新，（模拟器直接双击R）

真机测试需要摇动手机，模拟器的话是menu键（windows: ctrl + M ，macOS: command + M）
设置host:port（例如我的是192.168.1.129:8081）

5. 编辑器我用VS Code，安装react-native tools插件，使用vs感觉比较方便~

6. title居中问题
设置：`headerTitleStyle:{flex:1,textAlign:'center'}`

7. 同时显示头部与底部tabbar问题
参见项目：https://github.com/lypeng29/RN_news

9. A problem occurred configuring project ':app'（新版已修复）
还提示找不到SDK目录，我的目录是存在的
查看了之前的项目，需要新建local.properties文件，里面写上：`sdk.dir=D\:\\Android\\SDK`

10. null is not an object evaluating 'RNGestureHandlerModule.State'
yarn add react-native-gesture-handler
react-native link

11. RNCWebView was not found in the UIManager
```
yarn add react-native-webview
react-native link react-native-webview #安装完成，需要关联下
```
关联成功后，需要修改settings.gradle文件中的“\”为“/”，否则路径错误（新版已修复）。

12. 不开Android studio，直接启动模拟器
新建一个a.bat文件，内容如下
```
C:\Users\Administrator\AppData\Local\Android\Sdk\tools\emulator.exe -netdelay none -netspeed full -avd Nexus_5_80
```
说明，前面是你的模拟器EXE文件位置，后面的Nexus_5_80是你新建的模拟器名称~

8. 页面传值
以及页面跳转推荐使用：`react-native-router-flux`

13. 图标 `react-native-vector-icons`
安装完link关联下即可

14. 滚动可以用 `react-native-scrollable-tab-view`
参见：`https://github.com/lypeng29/react-native-books`，图书电影切换

15. 布局，一行两个或三个。。。
`https://www.lypeng.com/view/164.html`

16. Warning: Failed child context type: Invalid child context virtualizedCell.cellKey of type number supplied to CellRenderer, expected string.
key必须是字符串： `keyExtractor={(item, index) => index.toString()}`

17. 下拉刷新与上拉加载
参见：`https://www.lypeng.com/view/168.html`


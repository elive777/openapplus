# OpenApplus

## 官方网站
http://www.openapplus.com
## 捐赠热线:
   <img src="https://raw.githubusercontent.com/linwaiwai/openapplus/master/wechat.jpeg" alt="Donate with Alipay or Wechat Pay" title="Donate with Alipay or Wechat Pay" width="200"/>
捐赠的同学如果要联系我，麻烦在付款方备注留下您的微信联系方式，以便我加你。

## 参考链接
http://www.jianshu.com/p/9c289f784928
## iOS集成
### Cocoapods 安装

推荐使用 [CocoaPods](http://cocoapods.org/) 的方式安装使用。

[CocoaPods](http://cocoapods.org/) 是一个广泛适用于Objective-C依赖管理工具，能够自动配置项目，简化你配置Openapp+的过程，使用以下命令行安装

 $ gem install cocoapods

### OpenApplus 安装

使用CocosPods集成Openapp+到Xcode

#### 在Podfile文件加入
```ObjectiveC
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.0'
#忽略引入库的警告
inhibit_all_warnings!

target 'openapplus-ios-demo' do
    pod 'OpenApplus', '~> 1.3.8'
end
```

#### 在工程中Info.plist文件中添加如下项
```
<key>NSAppTransportSecurity</key>
<dict>
  <key>NSAllowsArbitraryLoads</key>
   <true/>
</dict>
```

### 运行

在 AppDelegate.m 里按顺序调用三个方法：

1、调用 +startWithAppKey: ，参数为第一步获得的 AppKey。

2、调用 +sync 方法检查包更新。

在AppDelegate.m或ViewController.m中调用 navigateToMiniProgram: 加载小程序项目，参数为在平台中创建的项目的名称。

```ObjectiveC
  #import <"openapplus/openapplus.h">
  @implementation AppDelegate
  - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
      UINavigationController *navigationController = [[UINavigationController alloc] initWithRootViewController:testViewController];
      self.window.rootViewController = navigationController;
      [self.window makeKeyAndVisible];

      [OpenApplus startWithAppKey:@"test"];
      [OpenApplus sync];
      [OpenApplus setNavigationController:navigationController];
      // JS_APPID 为小程序的APP_ID
      [OpenApplus navigateToMiniProgram:@"openapplus://jsApp/#JS_APPID#" completion:^{
        
      }];

      ...
  }
  @end
```
 
上述例子是把 Openapplus 同步放在 -application:didFinishLaunchingWithOptions: 里，若希望包能及时推送，可以把 [OpenApplus sync] 放在 -applicationDidBecomeActive: 里，每次唤醒都能同步更新 OpenApplus 包，不需要等用户下次启动。

## Android集成
### Android Studio集成
下载SDK功能组件，解压.zip文件得到相应组件包（openapplus-release.aar），在Android Studio的项目工程libs目录中拷入相关组件jar包。
右键Android Studio的项目工程—>选择Open Module Settings —>在 Project Structure弹出框中 —>选择 Dependencies选项卡 —>点击左下“＋”—>选择组件包类型—>引入相应的组件包。
### 或者使用gradle
```
buildscript {
    repositories {
        jcenter()
    }
}

allprojects {
    repositories {
        jcenter()
    }
}
```
```
dependencies{
   implementation ('com.openapplus:openapplus:1.0.3')
   implementation 'com.eclipsesource.j2v8:j2v8:6.1.0@aar'
}
```
详细添加位置请参考DEMO[https://github.com/linwaiwai/openapplus-demo/blob/master/Android/]
### 运行
在项目工程的自定义application中的onCreate方法中添加以下两个方法：
注意：一定要在主进程进行该项操作
```
OpenApplus.registerApp(this, SampleContants.APPID, SampleContants.APP_SECRET);
OpenApplus.sync();
OpenApplus.setCallback(new OpenApplusCallback() {
            @Override
            public void invoke(OACallbackType type, JSONObject data, OpenApplusNotify notify) {
                if (type == OACallbackType.OACallbackTypeAuthUser){
                    // 该接口仅供测试使用，请使用服务端发送给授权请求
                    OARequestWrapper requestWrapper = OpenApplus.makeRequestWrapper(SampleContants.SERVER_APPID, SampleContants.SERVER_APP_SECRET);
                    OAAuthDtoWrapper dto = new OAAuthDtoWrapper();
                    dto.setUid("1");
                    try {
                        dto.setCode(data.getString("code"));
                    } catch (JSONException e) {
                        e.printStackTrace();
                    }
                    String deviceID = Settings.Secure.getString(WXEnvironment.sApplication.getApplicationContext().getContentResolver(),
                            Settings.Secure.ANDROID_ID);
                    dto.setDeviceid(deviceID);
                    requestWrapper.sendObject(dto, notify);
                }
            }
});
````
注意：

参数1:上下文，必须的参数，不能为空

参数2:OpenApplus app key，必须参数。

参数3:OpenApplus app secret，必须参数。

### 添加相关权限
``` xml

<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.CAMERA"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="com.android.launcher.permission.INSTALL_SHORTCUT"/>
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
<uses-permission android:name="android.permission.SYSTEM_OVERLAY_WINDOW"/>
<uses-permission android:name="android.permission.VIBRATE"/>
<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
<uses-permission android:name="android.permission.READ_LOGS"/>
<!-- 这个权限用于进行网络定位 -->
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<!-- 这个权限用于访问GPS定位 -->
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>

<uses-permission android:name="android.permission.ACCESS_GPS"/>

<uses-feature android:name="android.hardware.camera"/>
<uses-feature android:name="android.hardware.camera.autofocus"/>

<uses-permission android:name="getui.permission.GetuiService.com.bmdoctor.jyt"/>

<!--amap额外权限-->
<uses-permission android:name="android.permission.ACCESS_LOCATION_EXTRA_COMMANDS"/>

```
### 代码混淆
如果您的应用使用了混淆， 请添加
```shell

-keep class com.openapplus.** {*;}

```
### 添加Activty入口
在AndroidManifest.xml中添加

```xml
<activity android:name="com.openapplus.activity.OATinyProgramActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW"/>

        <category android:name="android.intent.category.DEFAULT"/>
        <category android:name="com.benmu.weex.example.categoty.page"/>

        <data android:scheme="http"/>
        <data android:scheme="https"/>
    </intent-filter>
</activity>

```

### 启动小程序容器
```JAVA

Intent intent = new Intent(SplashActivity.this, OATinyProgramActivity.class);
intent.putExtra("tiny","openapplus://jsApp/xxxxx");
startActivity(intent);
finish();

```

### Codova支持
#### 自动安装
##### 安装cordova
```
> npm install -g cordova
> cordova create MyApp
> cd Cordova-Demo
> cordova platform add ios
> cordova plugin add @openapplus/cordova-openapplus-plugin
```

##### 编写codova插件调用函数
```javascript
function onButtonClicked(){
    var CordovaOpenApplusPlugin = cordova.plugins.CordovaOpenApplusPlugin;
    CordovaOpenApplusPlugin.navigateToMiniProgram("openapplus://jsApp/Lr12JhPrE9bg7N4uoABBXY");
}
var app = {
    // Application Constructor
    initialize: function() {
        document.addEventListener('deviceready', this.onDeviceReady.bind(this), false);
    },

    // deviceready Event Handler
    //
    // Bind any cordova events here. Common events are:
    // 'pause', 'resume', etc.
    onDeviceReady: function() {
        this.receivedEvent('deviceready');
        var CordovaOpenApplusPlugin = cordova.plugins.CordovaOpenApplusPlugin;
        CordovaOpenApplusPlugin.start("oa606f78973e434c78933b869d8ba73c59","$2a$10$4bbR9ZNAhm74x3sckSLf/uPyR.E2JQvACCHP4xKtLt/OOpbJsiq9q");
        CordovaOpenApplusPlugin.enableLogging(true);

        document.querySelector('button').addEventListener('click', onButtonClicked);
    },

    // Update DOM on a Received Event
    receivedEvent: function(id) {
        var parentElement = document.getElementById(id);
        var listeningElement = parentElement.querySelector('.listening');
        var receivedElement = parentElement.querySelector('.received');

        listeningElement.setAttribute('style', 'display:none;');
        receivedElement.setAttribute('style', 'display:block;');

        console.log('Received Event: ' + id);
    }
};

app.initialize();
```

##### 完整Demo下载地址
[https://github.com/linwaiwai/openapplus-demo/](https://github.com/linwaiwai/openapplus-demo/)

#### 手动安装
##### 下载插件
1、在Demo中找到插件下载CordovaOpenApplusPlugin
2、修改插件配置文件plugin.xml
```xml
<?xml version='1.0' encoding='utf-8'?>
<plugin id="cordova-openapplus-plugin" version="0.0.4" xmlns="http://apache.org/cordova/ns/plugins/1.0" xmlns:android="http://schemas.android.com/apk/res/android">
	<name>CordovaOpenApplusPlugin</name>
	<js-module name="CordovaOpenApplusPlugin" src="www/CordovaOpenApplusPlugin.js">
		<clobbers target="cordova.plugins.CordovaOpenApplusPlugin" />
	</js-module>
	<platform name="ios">
		<config-file parent="/*" target="config.xml">
			<feature name="CordovaOpenApplusPlugin">
				<param name="ios-package" value="CordovaOpenApplusPlugin" />
			</feature>
		</config-file>
		<source-file src="src/ios/CordovaOpenApplusPlugin.m" />
		<!-- <framework src="lib/OpenApplus.framework" embed="true" custom="true" /> -->
		<podspec>
		  <config>
		    <source url="https://github.com/CocoaPods/Specs.git"/>
		  </config>
		  <pods use-frameworks="true">
		  <!--   <pod name="SDWebImage"  spec="3.7.5" />
			<pod name="SSZipArchive"  spec="1.6.2" />
			<pod name="SVProgressHUD"   spec="2.1.2" />
			<pod name="UMengUShare/Social/WeChat"   spec="6.3.0" />
			<pod name="MJRefresh"   spec="3.1.12" />
			<pod name="libextobjc"   spec="~> 0.4.1" />
			<pod name="AFNetworking"   spec="~> 3.2.1" /> -->
			<pod name="OpenApplus"   podspec="../../plugins/cordova-openapplus-plugin/podspec/OpenApplus.podspec" />
		  </pods>
		</podspec>
	</platform>
</plugin>

``` 
为
```
<?xml version='1.0' encoding='utf-8'?>
<plugin id="cordova-openapplus-plugin" version="0.0.4" xmlns="http://apache.org/cordova/ns/plugins/1.0" xmlns:android="http://schemas.android.com/apk/res/android">
	<name>CordovaOpenApplusPlugin</name>
	<js-module name="CordovaOpenApplusPlugin" src="www/CordovaOpenApplusPlugin.js">
		<clobbers target="cordova.plugins.CordovaOpenApplusPlugin" />
	</js-module>
	<platform name="ios">
		<config-file parent="/*" target="config.xml">
			<feature name="CordovaOpenApplusPlugin">
				<param name="ios-package" value="CordovaOpenApplusPlugin" />
			</feature>
		</config-file>
		<source-file src="src/ios/CordovaOpenApplusPlugin.m" />
		<framework src="lib/OpenApplus.framework" embed="true" custom="true" />
		<podspec>
		  <config>
		    <source url="https://github.com/CocoaPods/Specs.git"/>
		  </config>
		  <pods use-frameworks="true">
		        <pod name="SDWebImage"  spec="3.7.5" />
			<pod name="SSZipArchive"  spec="1.6.2" />
			<pod name="SVProgressHUD"   spec="2.1.2" />
			<pod name="UMengUShare/Social/WeChat"   spec="6.3.0" />
			<pod name="MJRefresh"   spec="3.1.12" />
			<pod name="libextobjc"   spec="~> 0.4.1" />
			<pod name="AFNetworking"   spec="~> 3.2.1" /> -->
		  </pods>
		</podspec>
	</platform>
</plugin>
```
3、创建lib目录，下载OpenApplus.framework 到 lib目录下
4、cordova plugin add path/to/CordovaOpenApplusPlugin

### 问题

1、lipo -info OpenApplus.framework/OpenApplus 如果发现缺少arm64支持，请下载压缩包的内容覆盖，可能是下载的问题。


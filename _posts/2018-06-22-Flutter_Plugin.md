---
layout:     post
title:      Flutter_Plugin
subtitle:   
date:       2018-06-22
author:     XueKai
header-img: 
catalog: true
tags:
---
使用平台通道编写平台特定的代码

接下来用一个小项目说明这个过程，这个项目是用来获取IOS和安卓设备的当前电池电量。

#### 1.Plugins Code

```

//通道的客户端和宿主通过通道构造函数中传递的通道名称(samples.flutter.io/battery)进行连接
static const platform = const MethodChannel('samples.flutter.io/battery');

//功能函数(获取电池电量)
static Future<String> get platformVersion async {
    final String version = await _channel.invokeMethod('getBatteryLevel');
    return version;
}

```

#### 2.IOS Code（OC）

```
//1.导入Flutter（注意Pod）

#import <Flutter/Flutter.h>

//2.打通通道

- (BOOL)application:(UIApplication *)application
    didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  [GeneratedPluginRegistrant registerWithRegistry:self];
    FlutterViewController* controller = (FlutterViewController*)self.window.rootViewController;
    FlutterMethodChannel* batteryChannel = [FlutterMethodChannel
                                            methodChannelWithName:@"com.xuekai/getbattry"
                                            binaryMessenger:controller];
    [batteryChannel setMethodCallHandler:^(FlutterMethodCall* call, FlutterResult result) {
        // TODO
        if ([@"getBatteryLevel" isEqualToString:call.method]) {
            int batteryLevelInt = [self getBatteryLevel];
            NSString *batteryLevel = [NSString stringWithFormat:@"%d%%",batteryLevelInt];
            if (batteryLevelInt == -1) {
                result([FlutterError errorWithCode:@"UNAVAILABLE"
                                           message:@"Battery info unavailable"
                                           details:nil]);
            } else {
                result(batteryLevel);
            }
        } else {
            result(FlutterMethodNotImplemented);
        }
    }];
  return [super application:application didFinishLaunchingWithOptions:launchOptions];
}

//3.IOS功能函数(获取电量)

- (int)getBatteryLevel {
    UIDevice* device = UIDevice.currentDevice;
    device.batteryMonitoringEnabled = YES;
    if (device.batteryState == UIDeviceBatteryStateUnknown) {
        return -1;
    } else {
        return (int)(device.batteryLevel * 100);
    }
}

```

#### 3.Android Code（Java）

```
//1.导入Flutter

import io.flutter.app.FlutterActivity;
import io.flutter.plugins.GeneratedPluginRegistrant;
import io.flutter.plugin.common.MethodCall;
import io.flutter.plugin.common.MethodChannel;
import io.flutter.plugin.common.MethodChannel.MethodCallHandler;
import io.flutter.plugin.common.MethodChannel.Result;

//2.建立通道

private static final String CHANNEL = "com.xuekai/getbattry";

new MethodChannel(getFlutterView(), CHANNEL).setMethodCallHandler(
            new MethodCallHandler() {
              @Override
              public void onMethodCall(MethodCall methodCall, Result result) {
                if (methodCall.method.equals("getBatteryLevel")) {
                  int batteryLevel = getBatteryLevel();

                  if (batteryLevel != -1) {
                    result.success(String.valueOf(batteryLevel));
                  } else {
                    result.error("UNAVAILABLE", "Battery level not available.", null);
                  }
                } else {
                  result.notImplemented();
                }
              }
            }
    );

//3.功能函数

private int getBatteryLevel() {
    int batteryLevel = -1;
    if (VERSION.SDK_INT >= VERSION_CODES.LOLLIPOP) {
      BatteryManager batteryManager = (BatteryManager) getSystemService(BATTERY_SERVICE);
      batteryLevel = batteryManager.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY);
    } else {
      Intent intent = new ContextWrapper(getApplicationContext()).
              registerReceiver(null, new IntentFilter(Intent.ACTION_BATTERY_CHANGED));
      batteryLevel = (intent.getIntExtra(BatteryManager.EXTRA_LEVEL, -1) * 100) /
              intent.getIntExtra(BatteryManager.EXTRA_SCALE, -1);
    }
    return batteryLevel;
  }
  
```

#### 4.Test Code
```
platformVersion = await Getbattry.platformVersion
```

---
layout: post
title:  iOS下iBeacon总结
date:   2017-06-13 17:26:22
description: 
tags: iOS
categories: programming
---

## 权限
要使用iBeacon监控，要先做以下检查
- 检查设备是否支持Beacon扫描
{% highlight objc %}
if ([CLLocationManager isMonitoringAvailableForClass:[CLBeaconRegion class]]) {
}
{% endhighlight %}
- 必须获取Always定位权限
{% highlight objc %}
if ([CLLocationManager authorizationStatus] != kCLAuthorizationStatusAuthorizedAlways) {
    [CLLocationManager requestAlwaysAuthorization];
}
{% endhighlight %}
- 且必须打开后台获取地理位置更新权限
> Important
Apps must have always authorization to use region monitoring, and they must be configured with the Location updates background mode to be launched.

## Beacon属性
- proximityUUID （The unique ID of the beacons being targeted.）
- major （The value identifying a group of beacons.）
- minor （The value identifying a specific beacon within a group.）

## 实现
一般先检测Beacon的进出（Enter/Exit)，再获取具体距离（Ranging)，且苹果建议只在Foreground情况下Ranging
{% highlight objc %}
CLBeaconRegion* region = [[CLBeaconRegion alloc] initWithProximityUUID:uuid identifier:uuidStr];
region.notifyEntryStateOnDisplay = YES;
[locationManager startMonitoringForRegion:region];

// CLLocationManagerDelegate
- (void)locationManager:(CLLocationManager *)manager didEnterRegion:(CLRegion *)region {
    [manager startRangingBeaconsInRegion:(CLBeaconRegion *)region];
}

- (void)locationManager:(CLLocationManager *)manager didExitRegion:(CLRegion *)region {
    [manager stopRangingBeaconsInRegion:(CLBeaconRegion *)region];
}

- (void)locationManager:(CLLocationManager *)manager didRangeBeacons:(NSArray *)beacons inRegion:(CLBeaconRegion *)region
{
}
{% endhighlight %}


## 关于后台运行
当App不在前台或者被杀死是，也可以感应iBeacon信号，系统会唤醒App并给予一小段时间(10秒左右）进行处理。
这时可以使用 `  [UIApplication beginBackgroundTaskWithExpirationHandler:]  ` 请求更多后台执行时间

## 1个App最多能同时监控20个regions
> Core Location limits to 20 the number of regions that may be simultaneously monitored by a single app.




---
layout: post
title: 寻宝游戏 - 利用Beacon特性设计的iOS线下寻宝游戏 - 物联网小游戏
category: IoT
tags: IoT
description: 寻宝游戏 - 利用Beacon特性设计的iOS线下寻宝游戏 - 物联网小游戏
---

## 作者简介
科科香，程序员

方向：IoT，方案集成，喜好各种新鲜东东

**转载请注明出处**

## iBeacon简介
iBeacon（下面简称Beacon）是Apple在2013年9月发布的基于iOS7（及以上）的新功能。其工作方式是，配备有低功耗蓝牙（BLE）通信功能的使用BLE技术向周围发送自己特有的ID，接收到该ID的应用软件会根据该ID采取一些行动。

WWDC 14 之后，对 iBeacon 加大了基数支持和对其用于室内地图的应用有个更明确的规划。苹果公司公布了 [iBeacon for Developers](https://developer.apple.com/ibeacon/)和 [Maps for Developers](https://developer.apple.com/maps/)等专题页面。

想要了解iBeacon的更多信息，见[维基百科](https://en.wikipedia.org/wiki/IBeacon)。

## 游戏简介

将iBeacon设备作为需要寻找的宝物，隐藏于各个地方，玩家需要安装上开发的app，带着手机找寻宝物，集齐宝物，获取奖励，[游戏演示视频](http://v.youku.com/v_show/id_XMTI1MzI0MDk1Mg==.html)。

## Here We Go!

介绍完了背景，进入正题，如何制作这款游戏？

首先，你需要购入Beacon设备（这个游戏是寻找龙珠主题，所以买了7个，什么牌子不说了，有广告嫌疑）。淘宝上可以搜到很多Beacon卖家。

Beacon准备好之后，开始开发，这款游戏只做了iOS版本。

## 创建项目
在Xcode中创建项目，选择Simple View项目。输入名称dragonball-safari和组织名称。
![](http://img.blog.csdn.net/20151229164130396)

导入CoreLocation.framework。
![](http://img.blog.csdn.net/20151229164248034)

创建好项目并添加完库之后，需要删除ViewController.h和ViewController.m两个文件。并且添加几个新的项目文件：

 - TabBarViewController.h 
 - TabBarViewController.m
 - SafariViewController.h
 - SafariViewController.m
 - PickerViewController.h
 - PickerViewController.m

整个项目结构如下：
![这里写图片描述](http://img.blog.csdn.net/20151229164459091)

在模拟器上运行项目，效果如下。因为现在没有添加任何的视图元素，所以是一个黑屏。

### 界面
本文主要介绍如何应用iBeacon技术，界面工作在此不做介绍。下图是最后需要展示的界面。
![这里写图片描述](http://img.blog.csdn.net/20151229164712632)
![这里写图片描述](http://img.blog.csdn.net/20151229164726460)

上述的所有源代码在此[下载](https://github.com/and1985129/dragonball-safari/tree/starter-version)。

## 寻找龙珠
下载好上述的项目工程，并打开项目运行，你会得到上面展示的界面。下面，我们开始添加iBeacon的部分，让整个游戏开始正常运行。

开始之前，有几个地方先说明一下。

### 硬件准备
我用的是[BrightBeacon](http://brtbeacon.com/main/index.shtml)的产品（好吧，还是说了厂家，不过选择其他厂家产品也没有影响，Beacon的接口和属性都是通用的，这是因为Apple做了规范）,一共买了7个，需要把7个Beacon的Minor值设置成从1到7，分别代表七颗龙珠。否则无法进行游戏。

### SDK
因为用的BrightBeacon的产品，所以直接用的他们提供的[SDK](https://github.com/BrightBeacon/BrightBeacon_iOS_SDK)。

下载并将他的SDK和include文件引入项目工程中，如下图。
![这里写图片描述](http://img.blog.csdn.net/20151229165223811)

准备工作做完之后，下面开始进行编码。

打开AppDelegate.m文件，引入BRTBeaconSDK.h文件和UserDefaultTool.h文件。自定义一个用于SDK的App Key。这个key需要在BrightBeacon的官方网站上申请。

```
#import "BRTBeaconSDK.h"
#import "UserDefaultTool.h"
```
之后注册app，并进行初始化工作。UserDefaultTool这个文件是预先写好的，用于控制龙珠的查找和初始化工作。
```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
 
    [BRTBeaconSDK registerApp:BRT_SDK_KEY onCompletion:^(NSError *error) {
        NSLog(@"%@", error.description);
    }];
    
    [UserDefaultTool initUserDefaultData];

    return YES;
}
```
完成这一步之后，运行一下。在模拟器中，App会告知用户需要访问用户的地理位置信息，如果没有打开蓝牙的话，也会提示用户打开蓝牙。是不是很有意思：）
![这里写图片描述](http://img.blog.csdn.net/20151229165425237)

注册好应用之后，下面开始监测Beacon吧。

在SafariViewController.m里面，依次加入这些方法。

```
- (void)startToFindBeacons{
    [BRTBeaconSDK startRangingWithUuids:@[[[NSUUID alloc] initWithUUIDString:DEFAULT_UUID]] onCompletion:^(NSArray *beacons, BRTBeaconRegion *region, NSError *error){
        if (!error) {
            [self reloadData:beacons];
        }
    }];
}

- (void)reloadData:(NSArray *)beacons{
    self.beacons = beacons;
    [self.dragonBallTableView reloadData];
    [self setTopViewData];
    [self checkFoundBall];
}

// 监控选择龙珠的距离
-(void)setTopViewData
{
    self.topViewBeacon = nil;
    
    if (self.beacons.count > 0) {
        for (BRTBeacon * beacon in self.beacons) {
            if ([beacon.minor longValue] == self.nowRow + 1) {
                self.topViewBeacon = beacon;
            }
        }
        
    }
    [self setTopViewDistance];
}

- (void)setTopViewDistance{
    if (self.topViewBeacon == nil) {
        self.distanceLaber.text = @"?  ?  ?";
    }else
    {
        self.distanceLaber.text = ([self.topViewBeacon.distance floatValue] > 10) ? @">10m" : [NSString stringWithFormat:@"%.1f m",[self.topViewBeacon.distance floatValue]];
    }
}

// 找到龙珠
-(void)checkFoundBall
{
    for (BRTBeacon* beacon in self.beacons) {
        if ([beacon.distance floatValue] < 0.3) {
            NSMutableArray* array = [UserDefaultTool nowFoundBallsMutableArray];
            
            if ([[array objectAtIndex:[beacon.minor intValue] - 1] isEqual:@(0)]) {
                [array setObject:@(1) atIndexedSubscript:[beacon.minor intValue] - 1];
                [UserDefaultTool setUserDefaultDataWithArray:array];
                [self AfterFindABallCallBack:[beacon.minor intValue]];
            }
        }
    }
}

-(void)AfterFindABallCallBack:(int)minor
{
    if ([UserDefaultTool nowFoundBallsCount] == 7) {
        //召唤神龙
//        [self callTheDragon];
    }else{
        UIAlertView* alertView = [[UIAlertView alloc]initWithTitle:@"恭喜你" message:[NSString stringWithFormat:@"找到%d星球啦",minor] delegate:nil cancelButtonTitle:@"好的" otherButtonTitles:nil, nil];
        [alertView show];
    }
}
```

并在viewDidLoad中调用startToFindBeacons：

```
- (void)viewDidLoad {
    …
    // Start monitoring and ranging
    [self startToFindBeacons];
    [self setUpAudioPlay];
}
```
在DragonBallTableViewCell.m里面加入方法：

```
- (void)setBeacon:(BRTBeacon *)beacon{
    [self setDistanceByBeacon:(BRTBeacon *)beacon];
}

- (void)setDistanceByBeacon:(BRTBeacon *)beacon{
    
    NSArray* array = [UserDefaultTool nowFoundBallsArray];
    
    if ([[array objectAtIndex:_rowNum-1] isEqual:@(1)]) {
        //这个beacon已经找到过
        [self setDistanceLabelWithText:@"囊中之物" andColor:[UIColor greenColor]];
    }else{
        //没找到过这个beacon
        if(beacon == nil)
        {
            [self setDistanceLabelWithText:@"千里之外" andColor:[UIColor grayColor]];
        }else{
            if ([beacon.distance floatValue] < 2) {
                [self setDistanceLabelWithText:@"一步之遥" andColor:kNearTextColor];
                [self thereIsANearBeacon];
            }else if([beacon.distance floatValue] < 6){
                [self setDistanceLabelWithText:@"十步之遥" andColor:kNearTextColor];
            }else{
                [self setDistanceLabelWithText:@"百步之遥" andColor:kNearTextColor];
            }
        }
    }
}

-(void)thereIsANearBeacon
{
    if (!self.isShocked) {
        //通知代理
        if ([self.delegate respondsToSelector:@selector(CellDidCheckCloseToABall)]) {
            [self.delegate CellDidCheckCloseToABall];
        }
        self.isShocked = 1;
    }
}
```
完成在真机上运行，就可以看到龙珠距离我们的位置并且可以找到它啦：）当拿着一个Beacon靠近手机的时候，就会有下面的显示了。看，游戏的雏形已经出来了。
![这里写图片描述](http://img.blog.csdn.net/20151229165758932)

说明：这块最关键的核心就是通过下面方法，来监测Beacon，根据找到Beacon的距离信息，来判断是否找到龙珠。这个方法默认是每秒回调一次。

> [BRTBeaconSDK startRangingWithUuids: onCompletion:]
找到了龙珠之后，我们要在神龙碎片那里可以看到神龙的真面目。在PickerViewController.m中，添加方法：

```
- (void)setUpCurrentDragonFragment{
    self.dragonFragImages = @[_dragonFragImage1,
                              _dragonFragImage2,
                              _dragonFragImage3,
                              _dragonFragImage4,
                              _dragonFragImage5,
                              _dragonFragImage6,
                              _dragonFragImage7
                              ];
    NSArray* array = [UserDefaultTool nowFoundBallsArray];
    for (int i = 0 ; i < array.count; i++) {
        NSNumber* isFound = [array objectAtIndex:i];
        UIImageView* dragonView = self.dragonFragImages[i];
        if ([isFound isEqual:@(1)]) {
            dragonView.hidden = NO;
        }
    }
}
```
并在viewDidLoad中调用：

```
- (void)viewDidLoad {
    [super viewDidLoad];
    
    [self setUpCurrentDragonFragment];
}
```
这样，在神龙碎片中就可以看到找到了多少碎片了。
![这里写图片描述](http://img.blog.csdn.net/20151229170439667)

### 完成游戏
游戏设定在最后，找到所有龙珠之后，神龙出现。现在让我们完成最后一部分吧。

创建DragonMovieViewController文件，召唤神龙。
![这里写图片描述](http://img.blog.csdn.net/20151229170625653)

```
#import "DragonMovieViewController.h"
#import <MediaPlayer/MediaPlayer.h>

@interface DragonMovieViewController ()

@property (nonatomic, strong) MPMoviePlayerController *movie;

@end

@implementation DragonMovieViewController

- (void)viewDidLoad {
    
    self.view.backgroundColor = [UIColor whiteColor];
    [self setUpMoviePlay];
}

-(void)setUpMoviePlay
{
    //找到文件路径
    NSString* path = [[NSBundle mainBundle]pathForResource:@"dragon_appear" ofType:@"MP4"];
    NSURL* url = [NSURL fileURLWithPath:path];
    
    //
    MPMoviePlayerController* movC = [[MPMoviePlayerController alloc]initWithContentURL:url];
    movC.controlStyle = MPMovieControlStyleNone;
    movC.scalingMode = MPMovieScalingModeAspectFill;
    movC.view.frame=self.view.bounds;
    [self.view addSubview:movC.view];
    self.movie = movC;
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(dragonMovieFinished:)
                                                 name:MPMoviePlayerPlaybackDidFinishNotification
                                               object:movC];
    [movC play];
    
    
}

-(void)dragonMovieFinished:(NSNotification *)notify
{
    //视频播放对象
    MPMoviePlayerController* theMovie = [notify object];
    //销毁播放通知
    [[NSNotificationCenter defaultCenter] removeObserver:self
                                                    name:MPMoviePlayerPlaybackDidFinishNotification
                                                  object:theMovie];
    [theMovie.view removeFromSuperview];
    
    [self dismissViewControllerAnimated:YES completion:nil];
    
    UIAlertView* alertView = [[UIAlertView alloc]initWithTitle:@"恭喜恭喜恭喜你" message:@"你已经成功召唤神龙!" delegate:nil cancelButtonTitle:@"噢耶" otherButtonTitles:nil, nil];
    
    [alertView show];
}
```
召唤神龙的地方准备就绪，回到SafariViewController.m文件，引入DragonMovieViewController，并在最后加上方法：

```
-(void)callTheDragon
{
    DragonMovieViewController* dC = [[DragonMovieViewController alloc]init];
    [self presentViewController:dC animated:YES completion:nil];
}
```
在AfterFindABallCallBack里面，召唤神龙：

```
-(void)AfterFindABallCallBack:(int)minor
{
    if ([UserDefaultTool nowFoundBallsCount] == 7) {
        //召唤神龙
        [self callTheDragon];
    }else{
        UIAlertView* alertView = [[UIAlertView alloc]initWithTitle:@"恭喜你" message:[NSString stringWithFormat:@"找到%d星球啦",minor] delegate:nil cancelButtonTitle:@"好的" otherButtonTitles:nil, nil];
        [alertView show];
    }
}
```
这样整个游戏就完成了：）
赶快开始游戏吧，看看谁是召唤神龙的第一人！
![这里写图片描述](http://img.blog.csdn.net/20151229170933832)

### 源代码
整个项目的源代码上传到了我的github里面，欢迎大家下载。

## 结语
这里的Beacon游戏只是物联网应用的一个初步尝试，还有很多应用有待大家深挖和细挖。如果你有更好的建议，也请给我留言。因为是个人力量，不保证响应速度但保证响应质量：）

下一篇文章准备写关于物联网的一个实际应用小demo。
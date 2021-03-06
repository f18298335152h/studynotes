# 前言
> 说实话我正在同时读几本书《Git权威指南》、《大话设计模式》还有这本《AVFoundation开发秘籍》。
> 实际上自我感觉同时看并不冲突，虽然平常常用svn和git，但是说实话看看《Git权威指南》这本书还是挺有好处的，至少不会糊里糊涂的用git。至于《大话设计模式》这本书，看看其实挺有帮助的，虽然本身对设计模式有部分了解，但是了解的不是很全面，对至少现在看的部分对自己还是有点帮助的，这本书看完之后就是《设计模式之禅》这本书了。毕竟设计模式这个东西在有一定的开发经验之后再去看才更容易去理解。至于这本《AVFoudation开发秘籍》，说实话工作这么久，感觉正常工作中没办法进阶了，每天的工作除了UI还是UI，一直就是在搭界面，这本书呢，是我在刚开始工作的时候买的，早些时间也看过了部分，感觉理解不深刻，现在重新回炉这部分的知识，哈哈哈哈。

#第一部分 AVFoundation基础
## 第一章 AVFoundation入门 
AVFoudation 是MacOS和iOS系统中**用于处理基于时间的媒体数据的高级OC框架。** 这个框架包含的类超过100个，大量的协议的集合及许多不同功能和常量。学习起来真的是很不容易。

这个框架处于UIKit和AppKit框架之下，所以很好的兼顾了iOS和MacOS之间的兼容性他们之间也好互相移植。处于`CoreAudio`，`CoreVideo`，`CoreMedia`和`CoreAnimation`这四个框架之上，所以同时提供了相对于这四个框架更高级的API。

**核心功能：**

- 音频播放和记录
   -  在与AVFoudation这一层框架中苹果有开发一套音频专用类，这是AVF提供的关于音频处理的早期功能。
   - `AVAudioPlayer`和`AVAudioRecorder`，在开发中提供了一套更简单的整合音频播放和记录的功能，这不是AVF唯一的处理方式，但是学习起来简单而且强大。

- 媒体文件检查
    - 获取媒体文件的相关技术参数和判断能否回放和是否能编辑和导出。

- 视频播放
    - 可以让你从本地或者远程流中获取视频资源。
    - 核心类：`AVPlayer`和`AVPlayerItem`

- 媒体捕捉
    - 核心类：`AVCaptureSession`
    - 摄像头相关了

- 媒体编辑
    - 该框架允许创建可以将多个音频和视频资源进行组合的应用程序。
    - 允许修改和编辑独立的媒体片段，随时修改音频文件的参数。

- 媒体处理
    - 当需要更高级的媒体处理任务时，可以使用`AVAssetReader`和`AVAssetWriter`类来实现。这些类提供直接访问视频帧和音频样本的功能，所以能对媒体资源进行任何更高级的处理。


本章小结：
个人认为本章了解`AVFoudation`的地位就行。处于低于`UIkit`，高于Core框架的中间层。当然还存在一层与`UIKit`或者`AppKit`处于同一层的框架`AVKit`。
了解视频编码器：`H.264`和`AppleProRes`
了解音频编码器：`ACC`
了解容器格式：`QuickTime`和`MPEG-4`

---


## 第二章 播放和录制音频
 
 - 音频会话：`AVAudioSession`
   - 在程序和操作系统之间扮演这中间人的角色，提供了一种简单实用的方式使OS得知应用程序应该如何与iOS音频环境惊醒交互。
   - 一个单例类，这个类在程序的生命周期中是可以修改的，但是通常只需要配置一次就行，书上说在`didFinishLaunchingWithOption`这个方法中配置就行，我认为自己决定就好，不一定要在这里配置，主要还是看自己App的功能。
   - AVFoundation定义了7种分类来描述应用程序所使用的音频行为
    
    分类 | 作用 | 是否允许混音 | 音频输入 | 音频输出
    --------- | ------------- | ------------- | ------------- | ------------- 
    Ambient | 游戏、效率应用程序 | ☑️ |  | ☑️ |
    Solo Ambient（默认） | 游戏、效率应用程序 |  |  | ☑️ |
    Playback | 音频和视频播放器 | 可选 |  | ☑️ |
    Record | 录音机和音频捕捉 |  | ☑️ |  |
    Play and Record | VoIP、语音聊天 | 可选 | ☑️ | ☑️ |
    Audio Processing | 离线会话和处理 |  |  |  |
    Multi-Route | 使用外部硬件的高级A/V应用程序 |  | ☑️ | ☑️ |
    
    ```objc
    //这段代码是全局设置 自己考虑 应该放在哪里（不知道放在哪就放在Appdelegate.m中）
    AVAudioSession *session = [AVAudioSession sharedInstance];
    NSError *error;
    if(![session setCategory:AVAudioSessionCategoryPlayback error:&error])
    {
    
    }
    if(![session setActive:YES error:&error])
    {
    
    }
    ```
    AVAudioSession提供了与应用程序音频会话交互的接口，所以开发者需要去的指向改单例的指针。通过设置合适的分类，开发者可以为音频的播放指定需要的音频会话，其中定制一些行为。最后告知该音频会话激活改配置。

- AVAudioPlayer的简单介绍
    - 这个类提供了两种方法进行创建，使用包好音频的内存版本的NSData或者本地音频文件的NSURL。这个类是AVFoudation中提供音频播放功能的类。 
    - AVAudioPlayer构建于Core Audio中的`C-Based Audio Queue Services`的最顶层。
    - 除非 需要从网络流中播放音频、访问原始音频样本或者需要非常低的时延，这个类基本上能胜任平常需要用到的功能。
    - 在创建AVAudioPlayer的实例之后可以通过调用`prepareToplay`对音频进行预加载，降低调用`play`方法时和听到的声音之间的时延。推荐调用`prepareToplay`
    - `pause`和`stop`能实现停止当前的播放行为。当然主要的区别是：调用`stop`会撤销调用`prepareToplay`时所做的设置。
    
    ```
    1. 可以通过修改实例的volume属性调节音量。
    2. 通过修改pan属性调节立体音。
    3. 通过修改rate属性修改音频的播放速率。
    4. 通过修改currentTime属性修改音频的播放进度。
    5. 通过修改numberOfLoops设置音频的循环播放次数。当属性 == -1时，会导致播放器的无限循环播放。
    ```


    - 处理中断事件
注册`AVAudioSession`发送的通知`AVAudioSessionInterruptionNotification`

    ```
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(TTT:) object:[AVAudioSession sharedInstance]]
    //在TTT:这个方法中接受到的notification对象中能取userInfo
    
    AVAudioSessionInterruptionType type = [notification.userInfo[AVAudioSessionInterruptionTypeKey] intValue];
    
    //这个type就是中断的类型，通过这个参数可以知道当前的中断具体事什么状态。如果是began则是中断开始，此时处理音频暂停的逻辑（调用音乐暂停，并处理UI相关逻辑）。如果是end则是终端结束，此时处理终端结束的逻辑（根据实际情况判断音频是不是需要重新开启）
    ```

    - 线路改变通知
注册`AVAudioSession`发送的通知`AVAudioSessionRouteChangeNotification`
同样在userInfo中取`notification.userInfo[AVAudioSessionRouteChangeReasonKey]`得到是不是有新的设备接入火接入设备是否改变。
从中判断当前切换的线路，根据实际情况的线路决定音频播放与否
![IMG_1385](media/IMG_1385.jpg)


- 音频录制 `AVAudioRecorder`
    类似于`AVAudioPlayer`的`prepareToplay`预加载功能，`AVAudioRecorder`也有`prepareToRecord`
    1. 音频格式
    2. 采样率
    3. 通道数
    4. 指定格式的键
    



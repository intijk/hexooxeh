---
title: Adobe Audition 学习笔记
id: 1230
categories:
  - 其它笔记
date: 2013-05-30 03:44:24
tags:
---

<!--:zh-->**课程时间安排**
1\. 45分钟
2\. 60分钟
3\. 75分钟
4\. 几个小时
5\. 45分钟
6\. 45分钟
7\. 45分钟
8\. 30分钟
9\. 70分钟
10\. 25分钟
11\. 50分钟
12\. 60分钟
13\. 50分钟
14\. 60分钟
15\. 90分钟
16\. 50分钟

# 第一课

## 不同的音频接口

XLR 三头对三头
1/4 大头音频，像电子琴的输出那样
1/8 minijack 普通3.5mm接口
Combo XLR和1/4的合体
AES/EBU接口，据说是高端接口，和XLR是兼容的，所以外观尺寸和XLR完全一样。

## 延迟，buffer

小buffer小延迟，但是可能会听到杂声

Bit resolution 采样位数
16位，20位，24位
44.1kHz + 24-bit是常见的采样频率和采样位数组合

Windows上常见的驱动类型MME, WDM/KS, ASIO，从前到后越来越高端，ASIO不是windows的一部分。
ASIO4ALL软件可以从网上下载。

## 采样率

32,000 数字广播
44,100 音乐录音
48,000 广播录音
88,200 很少用
96,000 DVD，高端音响
176,400 192,000 又大又笨没什么用

## 各种声卡的接口类型

USB
FireWire
PCIe card
ExpressCard
Thunderbolt

# 第二课

## 多轨录音和波形编辑是两种软件

多轨录音软件主要负责多轨的混音，要求每个轨占CPU不高
波形编辑重在对波形的修改编辑，占CPU高

## 图形界面元素

Workspace，Frame，Panel

Tabbed Frame里每一个tab是一个panel
拖拽一个Panel到另一个Panel或者Frame里的时候，会出现5向落点(Drop Zone)
中央落点是合并两者合并为一个Tabbed Frame
周围4点是保持两个Panel或Frame的独立关系，形成新的移动位置

可以Undock Panel，Undock Frame，形成独立的窗口
独立窗口通过拖拽Gripper，也可以重新移动回界面中

## 快捷键设置

Alk+K或者Edit -&gt; Keyboard Shortcuts

## Marker 位点

CTI(Current Time Indicator)放到想要的时间，点击大写M，就会在Marker Panel里面出现一个Marker，双击

这个Marker就可以跳到相应的位点

有了位点以后，返回到开始/返回到结束两个按钮就会在位点上跳。

# 第三课

## Zero-Crossing

波形是个电平在时间上的连续量，如果删除一个选区，左边界和右边界一正一负，就会产生click声。解决办法

是让选取的边界靠近最近的0点，这样就避免了Zero-Crossing。

静音，选中选区-&gt;Edit-&gt;Insert-&gt;Silence-&gt;OK, 原来有波形的地方就变成了0。

删除选区的时候，要保证
1\. 选区的长短位置合理
2\. 不出现Zero-crossing

一般把选区静音，用Skip Selection播放的方法，调整合适边界之后，Delete，即可保证以上两点。

## 剪贴板

Ctrl+1/2/3/4/5
AU一共提供了5个剪贴板，以上快捷键可以快速在这些剪贴板之间切换。

## Mixed Paste

Edit-&gt;Mixed Paste
可以把剪贴板中的音频波形直接合成在当前音轨上。
一般多轨编辑是后期混缩，Mixed Paste功能相当于提供了一个编辑时混缩。

如果混缩两个波形，不能让两个的音量都是100%，波形采用数值叠加，这样混缩会超过100%而失真。
所以类似的混缩要两者各取50%的混缩前音量，防止失真。

## 从片段创建文件的两种方法

Edit-&gt;Paste to New
File-&gt;Save Selection to New

## 关于杂音的单词

hum, hiss, uh, throat clearing, click, p-pops,
sibilant 咝音

# 第四课

## 三种音效调节方法：

1.  效果架 多个效果级联，可增删换序这些效果
2.  效果菜单 对波形直接应用一种效果
3.  喜好菜单 对波形应用预设的效果

## Dry Or Wet

干声是处理前的波形，湿声是处理后的波形。
百分号的滑条可以调节比例，有点像图像的Alpha通道。

## 词汇

treble 高音
midrange 中音
bass 低音
hi-hat 踩镲

## 效果架 Effects Rack

效果架的前级输出作为后级的输入，任意效果可以Apply之后，产生对波形的修改，效果也从列表中消失。

## Amplitude and Compression effects

1.  Studio Reverb 回声效果
2.  Analog Delay 延时效果，延时后和原波形叠加，可以做出重奏效果。
3.  Parametric Equalizer EQ
4.  Amplify 声音变强或变柔。
5.  Channel Mixser 通道的左右调节，互换，或设置为单通道等。
6.  DeEsser 降噪效果器，选通合适的频段，令其高于一音量门限才发生，可去除Sibilants，使用中发现频宽
最多只能到7500Hz
7\. Dynamics Processing 动态处理器，是一个特殊的放大器，一般的放大器是线性的，动态放大器能根据原先

的音量进行放大，和EQ不同，EQ的横坐标是频率，而动态放大器的横坐标是音量，输出还是音量。
8\. Hard Limiter 强压限幅，Release time 太短会 choppy， 声音会不稳定。
9\. Single-band Compressor 单段压限器，其实就是动态处理器的一种特殊情况，Threshold值，当音量大于

Threshold值的时候开始起作用，大于的部分被压缩ratio倍。
10\. Tube-Modeled Compressor 电子管压限器，单段压限器出来的声音太脆，电子管压限器就一些。
11\. Multiband Compressor 多段压限器，从高频到低频分为四段，对每段分别作压限。
12\. Spech Volume Leveler 演讲者音量电平，主要针对人声，可以调节增益和噪音门限，可以让声音更加统一

，音量大小均匀。

## Delay and echo effects

1.  Dely 就是波形在时序上的整体平移
2.  Analog Delay 其实就是回声，可以调节回声的时长以及和原声的叠加程度。很神奇的是用预设的配置可以
把一段鼓做出这么多不同的效果。
3\. Echo 看名字就知道是回声，但和Analog Delay不同的是不能调节原声的比例（Dry), 但是可以调节各个不

同频段的

Beats， 拍数，就是一段音乐的四分音的数量
Beats Per Minute（BPM）一段音乐行进的速度，每分钟消耗多少个四分音
在AU里要算Beat的时长，就是一个四分音占多少时间。显然就是BPM的倒数，但BPM的倒数是每排的分钟数，所

以要*60，000得到微妙数。所以计算的时候直接用60000/BPM 就得到微秒时长了。

## Filter and EQ effects

Parametric Equalizer 参数均衡器，它可以通过设定参数和拖动曲线调节均衡器
Graph Equalizer 就是现实世界中的”推子“，AU提供了10频，20频，30频三种
FFT Filter 自由度很高，可以在频谱图上任意拖动点做出自己想要的效果

## Modulation Effects

Chorus 和声特效，和声作为背景音的各个参数
Flanger 镶边，把原声和一系列的时移叠加起来
Phaser 声相，把一系列的声相移动和原序列叠加起来

## Noise reduction/restoration

第四章，独立成章

## Reverb effects(混响）

Convolution Reverb 卷积混响, 给一个房间环境，让你设定房间的参数
Studio Reverb 工作室混响，指定一个频段，然后设定混响衰减时间
Reverb

Full Reverb Convolution Reverb+Studio Reverb

Surround Reverb

## Special

Distortion 模拟出破音失真的效果
Guitar Suite 吉它专用的效果
Mustering 母带特效，感觉像个大杂烩，包括EQ，混响在内什么都有

## Time and Pitch effect

Automatic Pitch Correction 变调，调节人生的音高，伴奏和音高不配的时候很有用

## Phase（声相）

声相并不是网上以讹传讹的“左声道右声道偏移”，而是波形上的概念，如果波形非常简单，是个标准正弦波

，那么声相就是三角函数里相位的概念了。

## Invert

Invert操作把声波上下反向也认为是phase操作，其实只有对称周期波形反向才是phase操作，这个invert其实

在数学意义上不是phase。。。。不过不纠结了，Invert就当是180phase操作好了。

## Reverse

网上流传的，如果某某歌反向会如何的视频和音频就是这么做的，把波形从[0,t]这个空间，反向成[t,0]空间

。

第五章 声音恢复

===

Effects -&gt; Noise Reduction &amp; Restoration -&gt; Hiss Reduction

## Hiss

电路造成的，像磁带录音，麦克风增益等。

## clicks/crackles(ticks and pops)

黑胶的哔哔声, 波形上是一个尖脉冲

## Hmm

类似hiss的，但是频率比较低的 波波波波波 声音。。。<!--:--><!--:en-->**课程时间安排**
1\. 45分钟
2\. 60分钟
3\. 75分钟
4\. 几个小时
5\. 45分钟
6\. 45分钟
7\. 45分钟
8\. 30分钟
9\. 70分钟
10\. 25分钟
11\. 50分钟
12\. 60分钟
13\. 50分钟
14\. 60分钟
15\. 90分钟
16\. 50分钟

# 第一课

## 不同的音频接口

XLR 三头对三头
1/4 大头音频，像电子琴的输出那样
1/8 minijack 普通3.5mm接口
Combo XLR和1/4的合体
AES/EBU接口，据说是高端接口，和XLR是兼容的，所以外观尺寸和XLR完全一样。

## 延迟，buffer

小buffer小延迟，但是可能会听到杂声

Bit resolution 采样位数
16位，20位，24位
44.1kHz + 24-bit是常见的采样频率和采样位数组合

Windows上常见的驱动类型MME, WDM/KS, ASIO，从前到后越来越高端，ASIO不是windows的一部分。
ASIO4ALL软件可以从网上下载。

## 采样率

32,000 数字广播
44,100 音乐录音
48,000 广播录音
88,200 很少用
96,000 DVD，高端音响
176,400 192,000 又大又笨没什么用

## 各种声卡的接口类型

USB
FireWire
PCIe card
ExpressCard
Thunderbolt

# 第二课

## 多轨录音和波形编辑是两种软件

多轨录音软件主要负责多轨的混音，要求每个轨占CPU不高
波形编辑重在对波形的修改编辑，占CPU高

## 图形界面元素

Workspace，Frame，Panel

Tabbed Frame里每一个tab是一个panel
拖拽一个Panel到另一个Panel或者Frame里的时候，会出现5向落点(Drop Zone)
中央落点是合并两者合并为一个Tabbed Frame
周围4点是保持两个Panel或Frame的独立关系，形成新的移动位置

可以Undock Panel，Undock Frame，形成独立的窗口
独立窗口通过拖拽Gripper，也可以重新移动回界面中

## 快捷键设置

Alk+K或者Edit -&gt; Keyboard Shortcuts

## Marker 位点

CTI(Current Time Indicator)放到想要的时间，点击大写M，就会在Marker Panel里面出现一个Marker，双击

这个Marker就可以跳到相应的位点

有了位点以后，返回到开始/返回到结束两个按钮就会在位点上跳。

# 第三课

## Zero-Crossing

波形是个电平在时间上的连续量，如果删除一个选区，左边界和右边界一正一负，就会产生click声。解决办法

是让选取的边界靠近最近的0点，这样就避免了Zero-Crossing。

静音，选中选区-&gt;Edit-&gt;Insert-&gt;Silence-&gt;OK, 原来有波形的地方就变成了0。

删除选区的时候，要保证
1\. 选区的长短位置合理
2\. 不出现Zero-crossing

一般把选区静音，用Skip Selection播放的方法，调整合适边界之后，Delete，即可保证以上两点。

## 剪贴板

Ctrl+1/2/3/4/5
AU一共提供了5个剪贴板，以上快捷键可以快速在这些剪贴板之间切换。

## Mixed Paste

Edit-&gt;Mixed Paste
可以把剪贴板中的音频波形直接合成在当前音轨上。
一般多轨编辑是后期混缩，Mixed Paste功能相当于提供了一个编辑时混缩。

如果混缩两个波形，不能让两个的音量都是100%，波形采用数值叠加，这样混缩会超过100%而失真。
所以类似的混缩要两者各取50%的混缩前音量，防止失真。

## 从片段创建文件的两种方法

Edit-&gt;Paste to New
File-&gt;Save Selection to New

## 关于杂音的单词

hum, hiss, uh, throat clearing, click, p-pops,
sibilant 咝音

# 第四课

## 三种音效调节方法：

1.  效果架 多个效果级联，可增删换序这些效果
2.  效果菜单 对波形直接应用一种效果
3.  喜好菜单 对波形应用预设的效果

## Dry Or Wet

干声是处理前的波形，湿声是处理后的波形。
百分号的滑条可以调节比例，有点像图像的Alpha通道。

## 词汇

treble 高音
midrange 中音
bass 低音
hi-hat 踩镲

## 效果架 Effects Rack

效果架的前级输出作为后级的输入，任意效果可以Apply之后，产生对波形的修改，效果也从列表中消失。

## Amplitude and Compression effects

1.  Studio Reverb 回声效果
2.  Analog Delay 延时效果，延时后和原波形叠加，可以做出重奏效果。
3.  Parametric Equalizer EQ
4.  Amplify 声音变强或变柔。
5.  Channel Mixser 通道的左右调节，互换，或设置为单通道等。
6.  DeEsser 降噪效果器，选通合适的频段，令其高于一音量门限才发生，可去除Sibilants，使用中发现频宽
最多只能到7500Hz
7\. Dynamics Processing 动态处理器，是一个特殊的放大器，一般的放大器是线性的，动态放大器能根据原先

的音量进行放大，和EQ不同，EQ的横坐标是频率，而动态放大器的横坐标是音量，输出还是音量。
8\. Hard Limiter 强压限幅，Release time 太短会 choppy， 声音会不稳定。
9\. Single-band Compressor 单段压限器，其实就是动态处理器的一种特殊情况，Threshold值，当音量大于

Threshold值的时候开始起作用，大于的部分被压缩ratio倍。
10\. Tube-Modeled Compressor 电子管压限器，单段压限器出来的声音太脆，电子管压限器就一些。
11\. Multiband Compressor 多段压限器，从高频到低频分为四段，对每段分别作压限。
12\. Spech Volume Leveler 演讲者音量电平，主要针对人声，可以调节增益和噪音门限，可以让声音更加统一

，音量大小均匀。

## Delay and echo effects

1.  Dely 就是波形在时序上的整体平移
2.  Analog Delay 其实就是回声，可以调节回声的时长以及和原声的叠加程度。很神奇的是用预设的配置可以
把一段鼓做出这么多不同的效果。
3\. Echo 看名字就知道是回声，但和Analog Delay不同的是不能调节原声的比例（Dry), 但是可以调节各个不

同频段的

Beats， 拍数，就是一段音乐的四分音的数量
Beats Per Minute（BPM）一段音乐行进的速度，每分钟消耗多少个四分音
在AU里要算Beat的时长，就是一个四分音占多少时间。显然就是BPM的倒数，但BPM的倒数是每排的分钟数，所

以要*60，000得到微妙数。所以计算的时候直接用60000/BPM 就得到微秒时长了。

## Filter and EQ effects

Parametric Equalizer 参数均衡器，它可以通过设定参数和拖动曲线调节均衡器
Graph Equalizer 就是现实世界中的”推子“，AU提供了10频，20频，30频三种
FFT Filter 自由度很高，可以在频谱图上任意拖动点做出自己想要的效果

## Modulation Effects

Chorus 和声特效，和声作为背景音的各个参数
Flanger 镶边，把原声和一系列的时移叠加起来
Phaser 声相，把一系列的声相移动和原序列叠加起来

## Noise reduction/restoration

第四章，独立成章

## Reverb effects(混响）

Convolution Reverb 卷积混响, 给一个房间环境，让你设定房间的参数
Studio Reverb 工作室混响，指定一个频段，然后设定混响衰减时间
Reverb

Full Reverb Convolution Reverb+Studio Reverb

Surround Reverb

## Special

Distortion 模拟出破音失真的效果
Guitar Suite 吉它专用的效果
Mustering 母带特效，感觉像个大杂烩，包括EQ，混响在内什么都有

## Time and Pitch effect

Automatic Pitch Correction 变调，调节人生的音高，伴奏和音高不配的时候很有用

## Phase（声相）

声相并不是网上以讹传讹的“左声道右声道偏移”，而是波形上的概念，如果波形非常简单，是个标准正弦波

，那么声相就是三角函数里相位的概念了。

## Invert

Invert操作把声波上下反向也认为是phase操作，其实只有对称周期波形反向才是phase操作，这个invert其实

在数学意义上不是phase。。。。不过不纠结了，Invert就当是180phase操作好了。

## Reverse

网上流传的，如果某某歌反向会如何的视频和音频就是这么做的，把波形从[0,t]这个空间，反向成[t,0]空间

。

第五章 声音恢复

===

Effects -&gt; Noise Reduction &amp; Restoration -&gt; Hiss Reduction

## Hiss

电路造成的，像磁带录音，麦克风增益等。

## clicks/crackles(ticks and pops)

黑胶的哔哔声, 波形上是一个尖脉冲

## Hmm

类似hiss的，但是频率比较低的 波波波波波 声音。。。<!--:-->
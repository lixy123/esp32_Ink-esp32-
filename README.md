
esp32搭配墨水屏做的墨水屏显示器， 可供树莓派usb串口连接使用  <br/>
直传bmp二位黑白图像, 从树莓派接收到文字到显示至墨水屏平均1-2秒，局刷换图，效果不错.<br/>
树莓派也可以接收手机或某它信息来源的文字。<br/>

思路:  <br/>
1.esp32驱动墨水屏调用字库显示任意汉字目前没有成熟方案. <br/>
2.树莓派输出图像到墨水屏连接不紧凑,不适合摆放在桌面<br/>
于是想到把二者结合。 利用树莓派的强大功能处理任意文字(汉字)并生成图像,esp32负责把二进制图像展示.<br/>
2硬件通过usb线连接<br/>

效果图:
<br/>
<img src= 'https://raw.githubusercontent.com/lixy123/esp32_Ink-esp32-/master/ink_3.jpg' /> 

<br/>
<img src= 'https://raw.githubusercontent.com/lixy123/esp32_Ink-esp32-/master/ink_1.jpg' /> 
<br/>
用电实测:
<br/>
<img src= 'https://raw.githubusercontent.com/lixy123/esp32_Ink-esp32-/master/ink_2.jpg' /> 
<br/>
 注：墨水屏一般不需要频繁刷新，不刷新时将esp32进入睡眠模式，电流可减至17ms
 <br/>
 目录：
 
 1.esp32_Ink_420: esp32硬件的arduino源码，编译并烧录到esp32
 
 2.raspberry_client: 树莓派上运行的py源码，把文字格式化成bmp图像文件，通过usb串口把文件信息传给esp32供墨水屏显示, 其中，树莓派也可以接收手机或某它信息来源的文字。
 
 
  一.硬件:

  1.esp32墨水屏驱动板

         https://item.taobao.com/item.htm?spm=a1z1r.7974869.0.0.269c3ad4sJNvrL&id=608962598880

         T5 V2.4 版本      

     硬件资料：

         https://github.com/Xinyuan-LilyGO/T5-Ink-Screen-Series

         https://github.com/lewisxhe/TTGO-EPaper-Series      

  2.电子墨水屏：

     4.2寸屏

         https://item.taobao.com/item.htm?spm=a1z10.5-c.w4002-1995619223.11.19fe1061mPBsST&id=533910866519

     硬件资料:

         4.2寸屏 资料

         http://www.e-paper-display.cn/products_detail/productId=333.html

     太小的屏显示内容受限，太大的屏价格贵

     注意：支持局刷很重要，全刷刷屏时效果太差。

  二.接线

     墨水屏排线接到esp32墨水屏驱动板

  三.编译：

  1.必备 arduino 软件 最新版 1.8.12

  2.库:  arduino for esp32 (来源 GitHub)

         https://github.com/espressif/arduino-esp32

         GxEPD (来源 GitHub)

         https://github.com/ZinggJM/GxEPD

  3.esp32上传文件到 esp32 SPIFFS分区的软件

         https://github.com/me-no-dev/arduino-esp32fs-plugin

  3.编译：
         arduino 打开目录esp32_Ink_420下的arduino源码

         开发板: esp32 dev module,

         Partition Schemo: Default 4Mb with spiffs (1.2MB APP/ 1.5Mb SPIFFS)

         PSRAM: 随意，如果esp32板没有PSRAM硬件就不要选Enabled

         选择好端口，执行编译上传

         arduino 菜单 工具/ESP32 Sketch Data upload把示例bmp图传到esp32

  四.代码说明

  交互协议步骤

  1.收到 jpg>\r\n   串口返回 ok 进入bmp准备接收状态

  2.收到 字节长度字串\r\n 串口返回 ok 获取到bmp文件大小

  3.接收 bmp 数据文件到缓存 

  4.当收到字节数等于第二步得到的字节数后结束，将缓存数据存入 spiffs 文件名tmp.bmp

  5.将tmp.bmp文件解析成点阵数据，输出点阵图片到墨水屏

  处理速度

  1. bmp图像 400*280 约14k字节 (注意是2位色深， 不是8位色深，甚至24色深)

     921600串口速度，用时120ms秒   14000*8/921600=0.12秒

  2.bmp显示图像约170ms

  共用时: 290ms

  四.用电

  esp32: 60ma (可考虑在接收bmp完毕后esp32进入休眠模式，当收到串口信息后自动唤醒，待机电流可降到15ma,节能版本基本开发完)

  墨水屏: 工作状态约10-20ma,工作完自动进入休眠,体眠状态用电几乎为0

  合计：刷屏时75ma, 非刷屏60ma （注：没启用wifi）

  五.其它资料:          

     http://www.waveshare.net/wiki/4.2inch_e-Paper_Module

     用arduino/树莓派 驱动 墨水屏 介绍怎么用arduino,树莓派驱动墨水屏

     https://github.com/ZinggJM/GxEPD

     用GxEPD驱动 墨水屏 显示，示例很清楚

# Socket网络编程基础

### 什么是网络编程？

编写程序以便实现网络通信的过程。

### 网络模型

#### 七层网络模型—OSI

<img src="https://pic4.zhimg.com/80/v2-794aa7d2b0da939f8559f29ae28136c7_720w.webp" title="" alt="" width="366">

- 应用层：负责提供用户和应用程序之间的接口，最直观的理解就是人机交互界面，或者说是系统程序窗口。即微信好友聊天界面。

- 表示层：负责数据的格式转换、数据加密和压缩。。

- 会话层：管理会话的建立、维护和终止，以确保通信双方之间的有效对话。

- 传输层：提供应用进程间的数据传输。

- 网络层：确定目标设备位置和选择到达目标设备的数据传输路径。

- 数据链路层：负责直接相连的设备间的数据传输。

- 物理层：将数据变成信号进行实际的数据传输。

- 数据到达目标主机后，开始进行一个逆向的过程。即数据到达对方主机后，从物理层传输到数据链路层–网络层–传输层–会话层–表示层–应用层。

<img src="file:///C:/Users/唐飞龙/AppData/Roaming/marktext/images/2023-10-13-17-15-14-image.png" title="" alt="" width="510">

#### 四层网络模型—TCP/IP

<img src="https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/%E7%BD%91%E7%BB%9C/https/%E7%BD%91%E7%BB%9C%E6%8E%A5%E5%8F%A3%E5%B1%82.png" title="" alt="" width="342">

### Socket

- 应用编程接口API：能发起系统调用的接口。

- API的特点：封装了各种实现细节，开发人员只需要知道功能是什么就行，直接拿来用。

- Socket是一个用于网络通信的编程接口，应用程序通过Socket可以在不同计算机之间进行数据交换。

- Socket编程通常会使用到一组API函数，这些API函数提供了各种功能，让开发人员能够创建、配置、发送和接收网络数据包。另外，这些API函数通常由操作系统提供，以便应用程序可以使用它们来建立网络连接、发送和接收数据。

- Soket API，面向多种协议簇，常被用于客户和服务器之间的通信。

- Socket的作用：唯一标识两个<u>端点</u>（IP地址+端口号）之间的连接。

- Socket所在的层次
  
  <img title="" src="https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C/%E9%94%AE%E5%85%A5%E7%BD%91%E5%9D%80%E8%BF%87%E7%A8%8B/7.jpg" alt="" width="342">

- Socket之TCP： 1. 通信前需要通过三次握手建立连接  2.只能一对一通信
  
  <img src="file:///C:/Users/唐飞龙/AppData/Roaming/marktext/images/2023-10-13-18-01-02-image.png" title="" alt="" width="415">

- Socket之UDP：1.  通信前无需建立连接 2. 数据包要携带源端口号和目的端口号 3. 可以进行广播
  
  <img title="" src="file:///C:/Users/唐飞龙/AppData/Roaming/marktext/images/2023-10-13-18-01-14-image.png" alt="" width="402">

  ---

1. 外界要查找socket需要IP地址+端口号，而内部查找socket只需要<u>socket描述符</u>。

<img src="file:///C:/Users/唐飞龙/AppData/Roaming/marktext/images/2023-10-12-21-21-28-image.png" title="" alt="" width="521">

7. Socket相关：

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-12-21-33-37-image.png)

**TCP/IP协议簇中的端点地址变量的结构：**

<img src="file:///C:/Users/唐飞龙/AppData/Roaming/marktext/images/2023-10-12-21-38-23-image.png" title="" alt="" width="532">

不同协议族，端点地址（TCP/IP协议族端点地址是IP地址+端口号）是不一样的，所以在该结构中使用了地址族变量来记录协议族类型

---

<img src="file:///C:/Users/唐飞龙/AppData/Roaming/marktext/images/2023-10-13-09-56-51-image.png" title="" alt="" width="548">

发送数据

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-13-10-03-49-image.png)

接收数据

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-13-10-04-03-image.png)

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-13-10-05-18-image.png)

---

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-13-10-05-53-image.png)

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-13-10-06-07-image.png)

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-13-10-16-14-image.png)

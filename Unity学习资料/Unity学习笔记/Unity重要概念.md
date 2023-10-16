## Unity程序重要概念

---

GameObject和Component

- 游戏场景里存在的东西都是一个GameObject

- 每个GameObject的功能都由他身上的Component们组成

- 我们的代码继承自MonoBehaviour,而MonoBehaviour继承自Component,所以我们的
  
  代码也是一个组件，start函数就是继承下来的函数。

- 我们要是不想继承Component的代码，就不继承MonoBehaviour就好了

Hierarchy和Inspector界面

1. Hierarchy对应GameObject
- 显示场景里面都有哪些GameObject

- 新建/删除/重命名/开关GameObject

- 建立物体之间的父子关系
2. Inspector对应Component
- 显示选中的GameObject有哪些Component？

- 添加/删除/开关Component

- 修改Component的属性
  
  ---

<img title="" src="file:///C:/Users/唐飞龙/AppData/Roaming/marktext/images/2023-10-09-17-31-13-image.png" alt="" width="269"><img title="" src="file:///C:/Users/唐飞龙/AppData/Roaming/marktext/images/2023-10-09-17-32-56-image.png" alt="" width="292" data-align="inline">

---

<img title="" src="file:///C:/Users/唐飞龙/AppData/Roaming/marktext/images/2023-10-09-17-34-04-image.png" alt="" width="723" data-align="left">

- 这些生命周期函数会在游戏运行时的不同阶段自动调用。

- 这些生命周期函数帮助管理游戏对象的行为，确保在适当的时间执行必要的操作，从而实现游戏逻辑和互动效果。

- [事件函数的执行顺序 - Unity 手册](https://docs.unity.cn/cn/2019.4/Manual/ExecutionOrder.html)

---

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-09-19-08-03-image.png)

---

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-09-19-19-14-image.png)

---

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-09-19-23-22-image.png)

---

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-09-19-25-41-image.png)

---

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-09-19-33-00-image.png)

---

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-09-19-41-00-image.png)

---

Layer

- Layer是一个int类型的数据。

- 每一个GameObject都有一个Layer，Layer的值默认为“Default”。

- 可以在编辑器里面添加/删除Layer。

- Layer可以给所有GameObject进行分组，它最大的作用是：
  
  1. 让相机只渲染一些Layer 。
  
  2. 让物理运算只在一些Layer中进行。

- 访问Layer：gameObject.layer

- 这个Layer并非Sorting Layer!

Colloider

- 碰撞盒，定义了一个物体的物理体积

- Unity中有一堆不同形状的碰撞盒：Box，Circle，Capsule等等

- 作用：模拟物体碰撞效果，不过需要物体还需要有RigidBody2D

- Collider组件有两种模式：Collidr和Trigger

Rigidbody（刚体）

- 定义了物体的物理行为。

- 最直观的效果是添加了刚体的物体会受重力影响。

- 有三种模式：1.Dynamic（完全受物理效果影响） 2.Kinematic (不会主动移动，需要用代码控制移动)  3. Static（完全不会移动）

- 常用参数：Gravity（物体受到的重力大小）、Constrains：锁定位移/旋转、Material：控制弹力与摩擦

事件函数

---

![](C:\Users\唐飞龙\AppData\Roaming\marktext\images\2023-10-11-22-37-59-image.png)

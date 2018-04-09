# 3D-4
## 操作与总结  

* 参考 Fantasy Skybox FREE 构建自己的游戏场景  
  <img src="http://imglf5.nosdn.127.net/img/aHBnT05NNXVUK2dTNTU5Ky8waFJERldnNTNEeURtM2g4eEtMeWVKSDZmWkRMVkpIZXMzdUdRPT0.png?imageView&thumbnail=500x0&quality=96&stripmeta=0"  />      
  <img src="http://imglf3.nosdn.127.net/img/aHBnT05NNXVUK2dTNTU5Ky8waFJESGNRWnNvU1VIejk5cGVlaWhmZjUwRGFWQk5BMURCWFhBPT0.png?imageView&thumbnail=500x0&quality=96&stripmeta=0"  />  
  <img src="http://imglf3.nosdn.127.net/img/aHBnT05NNXVUK2dTNTU5Ky8waFJESllET0dSTjhEaUFacTlZZFZ2eU9NNXRQUHVkY1FMOTRBPT0.png?imageView&thumbnail=500x0&quality=96&stripmeta=0"  />  

* 写一个简单的总结，总结游戏对象的使用  

> 游戏对象是在Unity编辑器中最重要的概念。游戏中的每个对象都是一个游戏对象，从角色和可收藏的物品到灯光，相机和特殊效果。然而，一个游戏对象本身无法做任何事情; 你需要给它的属性，才能成为一个角色，环境或特殊效果。  
>  
> 可以通过实例化预设体创造对象。  预设体的概念: 组件的集合体 , 预制物体可以实例化成游戏对象。创建预设体的作用: 可以重复的创建具有相同结构的游戏对象。
>  
> 可以通过设置游戏对象的Transform，Component 改变对象的属性和表现形式。 
>  
> 常见的对象： Empty （不显示却是最常用对象之一）；Camera 摄像机，观察游戏世界的窗口；Light 光线，游戏世界的光源；3D 物体:  基础 3D 物体     构造 3D 物体;Audio 声音;UI 基于事件的 new UI 系统（专题介绍);Particle System 粒子系统与特效  
>  
> 游戏对象都有的属性 :Active不活跃则不会执行update() 和 rendering ;Name 对象的名字，不是 ID。ID 使用 GetInstanceID(); Tag  字串，有特殊用途。如标识主摄像机 ;Layer [0..31]，分组对象。常用于摄像机选择性渲染等  


## 编程实践  

* 牧师与魔鬼 动作分离版

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

<img src="http://imglf4.nosdn.127.net/img/aHBnT05NNXVUK2ppYXhIbGYzeU5IZG9TSFRia2xRRUhQbkV1YVc1bkt0TmlLMmFxM3RCTmJ3PT0.png?imageView&thumbnail=500x0&quality=96&stripmeta=0"  />  
  <img src="http://imglf3.nosdn.127.net/img/aHBnT05NNXVUK2ppYXhIbGYzeU5IVC95eVgxeThObXZOVjJKRGZIY0pOZ3ZhdE9jeHBvRXF3PT0.png?imageView&thumbnail=500x0&quality=96&stripmeta=0"  />  
  
> * 附上视频链接： https://v.qq.com/x/page/a0626tmt6l3.html
> 
> 分析与总结： 
> 具体改进措施  
> 1. 动作模块管理由ActionManager统一管理  
```  
public class ActionManager:MonoBehaviour{
		fromBoat fromBoatAction;
		toBoat toBoatAction;
		moveBoat moveBoatAction;
		void Start(){
			moveBoatAction=gameObject.AddComponent<moveBoat>();
			toBoatAction=gameObject.AddComponent<toBoat>();
			fromBoatAction=gameObject.AddComponent<fromBoat >();
		}
		public void toBoatController(string characterType,Character[] characters,Boat temp){
			toBoatAction.realizeToBoatAction (characterType, characters, temp);
		}
		public void fromBoatController(string characterType,Character[] characters,Boat temp){
			fromBoatAction.realizeFromBoatAction(characterType, characters, temp);
		}
		public void moveBoatController(Boat boat){
			moveBoatAction.realizeMoveBoatAction (boat);
		}
	}
```  
> 2. 各个子动作交由各个子动作类来具体实现  ，例如上船和移动船，将动作与物体独立开来
```  
public class toBoat:MonoBehaviour{
		
		private Character characterTemp;
		private Vector3 middle;
		private Vector3 dest;
		private Boat boatTemp;
		private bool toBoatIsCall;	

		void Start(){
			characterTemp = new Character();
			boatTemp = new Boat ();
		}
		void Update(){
			if (toBoatIsCall == false) {
				return;
			}
			if (characterTemp.movingStatus == 1) {
				characterTemp.characterObject.transform.position = Vector3.MoveTowards (characterTemp.characterObject.transform.position, middle, characterTemp.speed * Time.deltaTime);
				if (characterTemp.characterObject.transform.position == middle) {
					characterTemp.movingStatus = 2;
				}
			}
			if (characterTemp.movingStatus == 2) {
				characterTemp.characterObject.transform.position = Vector3.MoveTowards (characterTemp.characterObject.transform.position,dest, characterTemp.speed * Time.deltaTime);
				if (characterTemp.characterObject.transform.position ==dest) {
					characterTemp.movingStatus = 0;
					toBoatIsCall = false;
				}
			}
		}
		public void realizeToBoatAction(string characterType,Character[] characters,Boat temp){
			toBoatIsCall = true;
			boatTemp = temp;
			if (temp.boatStack.Count >= 2) {
				Debug.Log ("ToBoatAction the boat is full");
				return;
			} else if (characterType == "priest") {
				for (int i = 0; i < 3; i++) {
					if (characters [i].status == temp.getBoatStatus()) {//在船的同一侧
						characterTemp=characters [i];
						characterTemp.movingStatus = 1;//从岸到middle

						if (temp.boatStack.Count == 0) {
							dest=temp.boat.transform.position + new Vector3 (-1, 1, 0);
							middle=temp.boat.transform.position +new Vector3 (-1, 1, 0);
						} else {//左边有乘客
							dest=temp.boat.transform.position + new Vector3 (0, 1, 0);
							middle=temp.boat.transform.position +new Vector3 (0, 1, 0);
						}
						characters [i].characterObject.transform.parent = temp.boat.transform;
						characters [i].status = 0;//修改状态在船上
						temp.boatStack.Push (characters [i]);
						return;
					}
				}
			}
			else if(characterType == "devil"){
				for (int i = 3; i < 6; i++) {
					if (characters [i].status ==temp.getBoatStatus()) {//在船的同一侧
						characterTemp=characters [i];
						characterTemp.movingStatus = 1;//从岸到middle
						if (temp.boatStack.Count == 0) {
							//characters [i].characterObject.transform.position = temp.boat.transform.position + new Vector3 (-1, 1, 0);
							dest=temp.boat.transform.position + new Vector3 (-1, 1, 0);
							middle=temp.boat.transform.position +new Vector3 (-1, 2, 0);

						} else {//左边有乘客
							//characters [i].characterObject.transform.position = temp.boat.transform.position + new Vector3 (0, 1, 0);
							dest=temp.boat.transform.position + new Vector3 (0, 1, 0);
							middle=temp.boat.transform.position +new Vector3 (0, 2, 0);
						}
						characters [i].characterObject.transform.parent = temp.boat.transform;
						characters [i].status = 0;//修改状态在船上
						temp.boatStack.Push (characters [i]);
						return;
					}
				}	
			}

		}
}
```  

```  
public class moveBoat:MonoBehaviour{
		private bool isMoving = true;
		private Vector3 rightCoast = new Vector3 (3,0, 0);
		private Vector3 leftCoast = new Vector3 (-0.5f, 0, 0);
		Boat boatTemp;
		//callbackFunc callbackTemp;

		void Start(){
			boatTemp = new Boat ();
		}
		public moveBoat(){
		}

		void Update(){
			if (isMoving) {
				if (boatTemp.getBoatStatus() == -1 && boatTemp.boatStack.Count!=0) {
					boatTemp.boat.transform.position = Vector3.MoveTowards (boatTemp.boat.transform.position, rightCoast, boatTemp.getBoatSpeed()*Time.deltaTime);
					if(boatTemp.boat.transform.position==rightCoast){
						isMoving = false;
						boatTemp.setBoatStatus (1);
					}
				}
				else if(boatTemp.getBoatStatus() == 1){
					boatTemp.boat.transform.position = Vector3.MoveTowards (boatTemp.boat.transform.position, leftCoast, boatTemp.getBoatSpeed()*Time.deltaTime );

					if(boatTemp.boat.transform.position==leftCoast){
						isMoving = false;
						boatTemp.setBoatStatus (-1);
					}
				}
			}

		}
			
		public void realizeMoveBoatAction(Boat temp){
			Debug.Log (" realizeMoveBoatAction");

			boatTemp = temp;
			isMoving = true;
		}
	}
```  
> 不足与反思：
> * 动作分离与老师的框架还有一定差距，受限于智商，有一些架构看了很久还是不清楚，所以就按照自己的分离方法分离了。  
> 
> * 各个不同的动作一开始相互干扰了，后来使用了private bool fromBoatIsCall和private bool toBoatIsCall来明确是那个动作正在被调用
> 
> * UI设计也没有很走心，至于为何过河的场景放到一片大草原上，当时下了几个场景，看了这个场景觉得还行，与别人也不一样，就用了。

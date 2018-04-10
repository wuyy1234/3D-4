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
  <img src="http://imglf3.nosdn.127.net/img/aHBnT05NNXVUK2p2eE95UFRvRURVcWo2SHF3bnVjMkR2RXVpcHQyTTBYcjgxdXlBYVRMbllRPT0.png?imageView&thumbnail=500x0&quality=96&stripmeta=0"  />   
  
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
> 
> * 代码仍然不够简洁，由于采用按钮交互而未采取点击物体反馈，所以在代码的判断行上较冗余，同时没有用list存取子动作以及没有用dictionary存储待执行序列，而只是分成单个子动作，在动作序列的处理上仍然有所欠缺。  

* 附上源代码：
```  
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Com.Model;

namespace Com.Model{
	
	public class ActionManager:MonoBehaviour{
		fromBoat fromBoatAction;
		toBoat toBoatAction;
		moveBoat moveBoatAction;
	
		void Start(){
			moveBoatAction=gameObject.AddComponent<moveBoat>();
			toBoatAction=gameObject.AddComponent<toBoat>();
			fromBoatAction=gameObject.AddComponent<fromBoat >();
		}
		void Update(){

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

	public class fromBoat:MonoBehaviour{

		private Character characterTemp;
		private Vector3 middle;
		private Vector3 dest;
		private Boat boatTemp;
		private bool fromBoatIsCall;

		void Start(){
			characterTemp = new Character();
			boatTemp = new Boat ();
		}
		void Update(){
			if (fromBoatIsCall == false) {
				return;
			}	

			if (characterTemp.movingStatus == 1) {

				Debug.Log ("111  middle: "+  middle);
				Debug.Log ("pos: " + characterTemp.characterObject.transform.position);
				Debug.Log ("tag: " +characterTemp.tag);

				characterTemp.characterObject.transform.position = Vector3.MoveTowards (characterTemp.characterObject.transform.position, middle, 20* Time.deltaTime);
				if (characterTemp.characterObject.transform.position == middle) {
					characterTemp.movingStatus = 2;
				}
			}
			if (characterTemp.movingStatus == 2) {
				Debug.Log ("222 dest: "+dest);

				characterTemp.characterObject.transform.position = Vector3.MoveTowards (characterTemp.characterObject.transform.position,dest, characterTemp.speed * Time.deltaTime);
				if (characterTemp.characterObject.transform.position ==dest) {
					characterTemp.movingStatus = 0;
					fromBoatIsCall = false;
				}
			}

		}

		public void realizeFromBoatAction(string characterType,Character[] characters,Boat temp){
			fromBoatIsCall = true;
			boatTemp = temp;
			if (temp.boatStack.Count <= 0) {
				Debug.Log ("the boat is empty");
				return;
			} else if (characterType == "priest") {
				for (int i = 0; i < 3; i++) {
					if (characters [i].status == 0) {//在船上
						characterTemp=characters [i];


						if (boatTemp.getBoatStatus () == -1) {//左岸
							middle = new Vector3 ((-6 + i), 1, 0);
							dest = new Vector3 ((-6 + i), 1, 0);
							characters [i].status = -1;
						} 
						else {//右岸
							middle=new Vector3( (5.8f+i),1,0 );
							dest=new Vector3( (5.8f+i),1,0 );
							characters [i].status = 1;
						}
						characterTemp.movingStatus = 1;//从船到middle
						characterTemp.characterObject.transform.parent = null;	
						boatTemp.boatStack.Pop();
						return;
					}
				}
			}
			else if(characterType == "devil"){
				for (int i = 3; i < 6; i++) {
					if (characters [i].status ==0) {//在船
						characterTemp=characters [i];

						if (boatTemp.getBoatStatus () == -1) {//左岸
							middle =characterTemp.characterObject.transform.position+new Vector3 (0, 1, 0);
							dest = new Vector3 ((-9 + i), 2, 0);
							characters [i].status = -1;
						} 
						else {//右岸
							middle =characterTemp.characterObject.transform.position+new Vector3(0,1,0 );
							dest=new Vector3( (2.8f+i),2,0 );
							characters [i].status = 1;
						}
						characterTemp.movingStatus = 1;//从船到middle
						boatTemp.boatStack.Pop();
						characterTemp.characterObject.transform.parent = null;	

						/// 
						return;
					}
				}	
			}
		}
	}
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
	public class Character {
		public GameObject characterObject;
		public int status;
		public int movingStatus;
		public int tag;//用来标记是第几个牧师或鬼
		public string name;
		public int speed=20;
		public Character(){
		}
		public Character(string type,int i){
			if (type == "devil") {
				characterObject = GameObject.Instantiate (Resources.Load ("Prefabs/Devil", typeof(GameObject)), new Vector3 ((5.8f + i), 2, 0), Quaternion.identity, null) as GameObject;
				name="devil";
				status = 1;
				tag = i;
			} else if (type == "priest") {
				characterObject = GameObject.Instantiate(Resources.Load ("Prefabs/Priest", typeof(GameObject)),new Vector3((5.8f+i),1,0), Quaternion.identity,null) as GameObject ;
				name="priest";
				status = 1;
				tag = i;
			}
		}


	}
	public class Boat :MonoBehaviour  {
		public GameObject boat;
		private int BoatSpeed =20;
		Vector3 rightCoast = new Vector3 (3,0, 0);
		Vector3 leftCoast = new Vector3 (-0.5f, 0, 0);
		private int BoatStatus;//-1  左岸，1  右岸
		private bool isMoving;

		public Stack<Character> boatStack;

		public int getBoatStatus(){
			return BoatStatus;
		}
		public void setBoatStatus(int status){
			BoatStatus = status;
		}
		public int getBoatSpeed(){
			return BoatSpeed;
		}

		void Start(){
			boat=GameObject.Instantiate(Resources.Load ("Prefabs/Boat", typeof(GameObject)),new Vector3(3,0,0), Quaternion.identity,null) as GameObject ;
			BoatStatus=1;
			boatStack = new Stack<Character> ();
			isMoving=false;
		}

		public int GetPassengerNum(){
			return boatStack.Count;
		}
		void Update(){
			
		}
		public int checkWin(Character[] characters){//判断输赢 ,-1为输，1为赢
			int leftDevil=0;
			int rightDevil = 0;
			int leftPriest = 0;
			int rightPriest = 0;
			int boatPriest = 0;
			int boatDevil = 0;

			for (int i = 0; i < 6; i++) {
				if (characters [i].name == "devil" && characters [i].status == 1) {//鬼在右边
					rightDevil++;
				} else if (characters [i].name == "devil" && characters [i].status == 0) {//鬼在船上
					boatDevil++;
				} else if (characters [i].name == "devil" && characters [i].status == -1) {//鬼在左边
					leftDevil++;
				} else if (characters [i].name == "priest" && characters [i].status == 1) {//牧师在右边
					rightPriest++;
				} else if (characters [i].name == "priest" && characters [i].status == 0) {//牧师在船上
					boatPriest++;
				}
				else if (characters [i].name == "priest" && characters [i].status == -1) {
					leftPriest ++;
				}
			}
			if (BoatStatus == 1) {
				if (((rightDevil + boatDevil) > (rightPriest + boatPriest)) && (rightPriest + boatPriest) != 0) {
					return -1;
				}
				if (leftDevil > leftPriest && leftPriest != 0) {
					return -1;
				}
			}
			if(BoatStatus == -1) {
				if (rightDevil > rightPriest && rightPriest != 0) {
					return -1;
				}
				if ((leftDevil + boatDevil) > (leftPriest + boatPriest) && (leftPriest + boatPriest) != 0) {
					return - 1;
				}
			}
			if (leftDevil == 3 && leftPriest == 3) {
				return 1;
			}

			return 0;
		}

	}
	public class Coast{//用coast代替shore
		GameObject leftCoast;
		GameObject rightCoast;
		public Coast(){
			leftCoast =GameObject.Instantiate(Resources.Load ("Prefabs/Shore", typeof(GameObject)),new Vector3(8.2f,-0.5f,0), Quaternion.identity,null) as GameObject ;
			rightCoast=GameObject.Instantiate(Resources.Load ("Prefabs/Shore", typeof(GameObject)),new Vector3(-6,-0.5f,0), Quaternion.identity,null) as GameObject ;
		}

	}

}

```  
```  
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Com.Model;

public class View : MonoBehaviour {
	GUIStyle style=new GUIStyle();
	void Start(){
		style.fontSize = 40;
	}
	void OnGUI(){
		GUI.Label (new Rect (Screen.width / 2-100, Screen.height / 2 - 250, 100, 50), "Priest and Devil",style);

	}
}

```  

```  
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Com.Model;

public class Controller : MonoBehaviour {
	View view;
	GUIStyle buttonStyle;
	GUIStyle style=new GUIStyle();
	private int status;
	Boat boatObject;
	Coast coast;
	Character[] characters;
	ActionManager actionManager;

	void Start () {
		view = gameObject.AddComponent <View>() as View;
		boatObject = gameObject.AddComponent<Boat>() as Boat;//实例化

		actionManager= gameObject.AddComponent<ActionManager>() as ActionManager;

		style.fontSize = 40;
	}

	void Awake(){
		Debug.Log ("Controller Awake");
		coast = new Coast ();
		/*GameObject a = new GameObject ();
		a= GameObject.Instantiate (Resources.Load ("Prefabs/Devil", typeof(GameObject)), new Vector3 ((4f), 2, 0), Quaternion.identity, null) as GameObject;
		Debug.Log (a);
		*/
		characters = new Character[6];
		for(int i=0;i<3;i++){
			characters [i] = new Character ("priest",i);
			characters [i+3] = new Character ("devil",i);
		}

	}
	/*int get_status(){
		return status;
	}
	void Update(){
		
	}*/
	void OnGUI(){
		if (boatObject.checkWin (characters) == 1) {//赢
			GUI.Label (new Rect (Screen.width / 2, Screen.height / 2 + 50, 100, 50), "win",style);
		}
		if (boatObject.checkWin (characters) == -1) {//输
			GUI.Label (new Rect (Screen.width / 2, Screen.height / 2 +50, 100, 50), "lose",style);
		}


		if (GUI.Button (new Rect (Screen.width / 2 + 250, Screen.height / 2 - 200, 100, 50), "牧师(圆）上")) {
			//boatObject.toBoat ("priest", characters);

			actionManager.toBoatController ("priest", characters, boatObject);
		}
		if (GUI.Button (new Rect (Screen.width/2+150,Screen.height/2-200 ,100, 50), "魔鬼（方）上")) {
			//boatObject.toBoat ("devil", characters);
			actionManager.toBoatController ("devil", characters, boatObject);
		}

		if (GUI.Button (new Rect (Screen.width/2-250,Screen.height/2-200 ,100, 50), "牧师(圆）下")) {
			//boatObject.fromBoat ("priest", characters);
			actionManager.fromBoatController("priest", characters, boatObject);
		}
		if (GUI.Button (new Rect (Screen.width/2-150,Screen.height/2-200,100, 50), "魔鬼（方）下")) {
			//boatObject.fromBoat("devil", characters);
			actionManager.fromBoatController("devil", characters, boatObject);

		}

		if ( GUI.Button (new Rect (Screen.width/2,Screen.height/2-200 ,100, 50), "开船") ) {
			//Debug.Log ("Boat");
			//boat.MoveBoat ();
			actionManager.moveBoatController(boatObject);
		} 
	}
}

```  



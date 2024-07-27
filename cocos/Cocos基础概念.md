### 一、向量与标量
#### 1、概念
标量：只有大小的量
向量：即有大小，也有方向
向量的摸：向量的大小
单位向量：大小为1的向量【常用于只关心方向，不关心向量大小的时候】
单位化，归一化：就是把普通向量转化为单位向量的过程

#### 2、运算
（1） 加法、减法【向量之间】
```
a(x1,y1) + b(x2,y2) = c(x1+x2,y1+y2)
满足平行四边形法则

a(x1,y1) - b(x2,y2) = c(x1-x2,y1-y2)
```

（2） 乘法、点乘
```
向量于标量：乘法,不会改变向量的方向
a(x1,y1) * b = c(x1*b,y1*b)


点乘
a(x1,y1) . b(x2,y2) = x1*x2+y1*y2 = n = |a|*|b|*cos&        &为两个向量之间的夹角

```

### 二、脚本
#### 1、属性
（1） 可见性
```
1、在 Cocos Creator 中，要使脚本属性在编辑器中可见，需要在 cc.Class 定义的 properties 字段中声明属性

2、如果属性不是基础数据类型,需要在property后面加上类型,例如:@property(cc.Label)
```

####        2、创建节点
```typescript
 // 创建节点
 let node:cc.Node = new cc.Node("hello");
 node.active = true
 // 设置父节点，必须需要设置这个，要不然这个节点就像无根浮萍
 // 无法显示
 node.setParent(this.node)
 node.setPosition(300,300)

 // 为节点添加组件
 let com = node.addComponent(cc.Label);
 com.string = "测试添加组件"
```

### 三、预制体
#### 1、属性的修改
(1)   使用预制体创建对象
 创建的对象在修改预制体之前，不受影响
 创建的对象在修改预制体之后，新创建的对象保存预制体最新的修改
```typescript
// 通过预制体创建节点对象

const {ccclass, property} = cc._decorator;

@ccclass
export default class Node3 extends cc.Component {

    @property(cc.Prefab)
    prefab: cc.Prefab = null;

    start () {
        let node = cc.instantiate(this.prefab);
        node.setParent(this.node)
    }
}
```


### 四、动态加载资源
所有需要通过脚本动态加载的资源
#### 1、资源位置
通常我们会把项目中需要动态加载的资源放在 `resources` 目录下（固定的）
#### 2、代码示例
```typescript
/**--------------------2.4版本之前的实现-------------------------------*/
var self = this;
cc.loader.loadRes("test assets/image", cc.SpriteFrame, function (err, spriteFrame) {
    self.node.getComponent(cc.Sprite).spriteFrame = spriteFrame;
});


/**--------------------2.4版本之后推荐的实现-------------------------------*/
// 加载 SpriteFrame
var self = this;
cc.resources.load("test assets/image", cc.SpriteFrame, function (err, spriteFrame) {
    self.node.getComponent(cc.Sprite).spriteFrame = spriteFrame;
});

// 加载 SpriteAtlas（图集），并且获取其中的一个 SpriteFrame
// 注意 atlas 资源文件（plist）通常会和一个同名的图片文件（png）放在一个目录下, 所以需要在第二个参数指定资源类型
cc.resources.load("test assets/sheep", cc.SpriteAtlas, function (err, atlas) {
    var frame = atlas.getSpriteFrame('sheep_down_0');
    sprite.spriteFrame = frame;
});
```


### 五、场景管理
#### 1、切换场景
```typescript
  // 加载第二个场景(只适合加载小场景)
  cc.director.loadScene("game2");

  // 对于加载大场景中使用
  cc.director.preloadScene("game2",(completedCount: number, totalCount: number, item: any)=>{
     // 已经加载到内存，还没有使用
     cc.director.loadScene("game2");
  });
```
####      	2 、永久保留节点
某一些节点，切换多个场景依然希望保存
```typescript
// 添加到常驻节点
cc.game.addPersistRootNode(this.node)
// 取消常驻节点(在执行当前代码的场景里面不会消失,切换到下一个场景里面会被移除)
cc.game.removePersistRootNode(this.node);
```

### 六、事件

1、鼠标事件
```typescript
@ccclass
export default class NewClass extends cc.Component {

    start () {
        // this.node.on()用来监听事件方法,针对某一个节点来进行监听
        this.node.on(cc.Node.EventType.MOUSE_DOWN,(event)=>{
            if(event.getButton() == cc.Event.EventMouse.BUTTON_LEFT){
                console.info("点击鼠标左键,位置",event.getLocation())
                return;
            }
            
        });
    }

}
```
2、键盘事件
```typescript

const { ccclass, property } = cc._decorator;

@ccclass
export default class NewClass extends cc.Component {

    start() {
        cc.systemEvent.on(cc.SystemEvent.EventType.KEY_DOWN, (event) => {
            console.info("按下键盘事件", event.keyCode)
            // ts类型声明文件 中cc.macro.KEY值都是0,这个比较奇怪
            if(event.keyCode == cc.macro.KEY.w){
                console.info("按下键盘W")
            }
        });
    }

}
```
3、触摸事件
```typescript
@ccclass
export default class NewClass extends cc.Component {

    
    start () {
        this.node.on(cc.Node.EventType.TOUCH_START,(event)=>{
            // 第一个手指触摸id为0,第二、第三个依次叠加
            console.info("触摸事件",event.getID())
        });
    }
}
```
4、自定义事件
```typescript
@ccclass
export default class NewClass extends cc.Component {


    start() {
        this.node.on(cc.Node.EventType.MOUSE_DOWN, () => {
            // 不好用,需要事件还得自己定义
            // 只能给本节点发事件
            this.node.emit("myEvent", 2);
            // 可以给本节点、以及本节点的父节点发事件
            // this.node.dispatchEvent(new cc.Event.EventCustom("myEvent", true))
        })
        this.node.on("myEvent", (event) => {
            console.info("自定义事件", event)
        });
    }
}

```

### 七、碰撞
碰撞检测需要碰撞组件来实现
```typescript
@ccclass
export default class NewClass extends cc.Component {
    start () {
        // 获取碰撞管理器  设置开启碰撞,如果不开启碰撞检测,不会发生碰撞事件
        // 两个节点都得添加碰撞XXCollider组件
        cc.director.getCollisionManager().enabled = true
    }
  
    // 产生碰撞
    onCollisionEnter(other,self){
        // 通过tag来区分碰到不同的物体 
        console.info("碰撞发生tag = ",other.tag)
    }

    // 当碰撞产生后，碰撞结束前的情况下，每次计算碰撞结果后调用 onCollisionStay
    // 当碰撞结束后调用 onCollisionExit
}
```

### 八、音频
#### 1、使用 AudioSource 组件播放

#### 2、使用 AudioEngine 播放

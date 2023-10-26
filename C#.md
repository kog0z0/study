## study C#:book:

### 变量调用

public可以在函数外调用（窗口可以看到）

private不可以

`public bool ***`默认是false，勾选后相反

public `GameObject `"名字"

`想编辑那个conponent,就生成那个conponent的类并取名`

### 函数

#### start

点击开始会执行

#### update

每秒执行多少帧的次数

#### Awake

不点开始也会执行

### 后台执行

`Debug.Log()`

输出：`Debug.Log(a+"is"+b)`

在输出里面计算要加括号，支持中文

### 获得输入

`Input.Get****(***)`

要放入==update==中才能实现每次输入都有反应

### 调整位置

按住`ctrl`调整物体位置

试试代码：

```c#
while(transform.position.x<transform.position.x)
{
    transform.position=new Vector2(transform.position.x+1,0);
}
```

### 输出

`outputText.text = "***"`

在屏幕上显示

### 数组

1. `类型[] 名字=new 类型[数量]`
2. `public 类型[]名字={***}`
3. `public Gameobject[] cubes`

（将物体拖动到相应位置）

`cubes=GameObject.FindGameObjectsWithTag("***")`

有s为复数，再用数组进行操作

### Lifecycle

[MonoBehavior](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html )

1. Awake

2. OnEnable(被勾选启用就会执行)

3. Start

4. FixedUpdate(物理运动，每帧都会执行)

5. OnTrigger(触发器)

6. OnCollision(碰撞器)

7. OnMouse(神奇鼠标)

8. Updata

9. LateUpdata(每帧渲染后执行)

10. OnUGI(屏幕上渲染用户界面)

11. OnDestroy

    (会反复循环执行，去链接看看)

    执行顺序与写的顺序没有关系:rotating_light:

***

### 总结

目前来看跟c语言基础部分差不多捏:poodle:




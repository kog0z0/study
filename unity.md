## study unity:slightly_smiling_face:

### 导入素材

1. window,asset store,buy
2. window,package manger,my assets,import

### Tilemap(编辑素材)

网格 unit

1. 调整像素点Pixels Per Unit
2. 拖拽背景(回到原始位置Transform,reset)
3. 2D object,tilemap,取消back勾选
4. window,2D,Tile palette,create new palette
5. 如果是多元素素材，选中后将Sprite Mode改为multiple,然后进行Sprite Editor，选择type,进行切割==注意此时的像素点==,apply确定
6. 选择笔刷填充

### 调整尺寸

1. Game中调整aspect
2. Main Camera中调整size

### 图层

1. sorting layer(分置前后)，新建==在下面则更靠前==，分配图层
2. 在同一图层中则可以Order in layer,选择顺序==数字越大越在前面==

### 建立角色

1. 如果有素材库，可以直接==调整像素==后拖拽

2. 也可以2D object,sprites,将素材拖入sprite框

3. 让player动起来:package:

   Add Component

   * Rigidbody 2D(让人物生动起来 )

   * Collider(碰撞体)

     edit设计实际碰撞大小，设计前可关闭其他图层，back可直接找到相应的Tilemap Collider

### 角色移动

1. 查看每个键的功能：edit,project settings,,input manager,axes
2. add component,new script,在assets里寻找，~~记得建立一个新文件夹并丢进去~~
3. 用刚体控制物体移动：`public rigidbody 2D 命名`并拖拽到指令框，`public float speed`
4. 创建movement函数，在updata中使用
5. 锁定方向：rigidbody 2D,constraints,勾选
6. 试玩过程中改变的参数，可以点齿轮复制之后再粘贴

### 判断方向:arrow_heading_up:

transform,scale,可以改变人物面向

#### Horizontal(水平方向)

1. GetAxis:获得-1到1之间某个值，可以用来操控速度
2. GetAxisRaw：获得-1，0，1某个值，可用来控制人物面朝方向

#### 使运动平滑

1. 将update改为fixedupdate
2. 将移动距离*实际时间变化率`Time.deltaTime`
3. 如果人物移动速度太慢记得更改速度

#### 跳跃吧鲤鱼王:fishing_pole_and_fish:

1. 定义一个jumpforce`public float jumpforce`
2. 接受玩家反馈：`if(Input.GetButtonDown("Jump"))`
3. 可调整jumpforce和gravity scale来改变跳起高度捏
4. ==按空格键有时跳跃有时不动解决方法==不能同时使用fixedupdate和 time.deltatime(建议将time.deltatime改为==time.fixedDeltatime==并将fixedupdate改为==update==)

### 动画效果

1. add component,animator
2. 创建文件：assets,animator,player,animator controller(拖拽到对应位置)
3. player,window,Animation,animation,命名一个文件，拖动动画到对应帧数(按住shift再点击首末文件可多选)，记得改unit为16
4. 减慢速度：(1)直接拖拽帧数条(2)改变samples(右上角三个点选择show samples)
5. 如果只运行一次记得勾选Loop time

#### 控制运行的动画

1. window,animation,animator
2. 创建判断条件：animator,parameters,选择变量类型,设置conditions
3. 看情况是否勾选has exit time,是否需要transition duration
4. 在代码中编写条件，将animator拖入框中控制

#### 实现跳跃的动画效果

1. 分别创建fall down两个动画，设置动画之间关系，并设计两个bool型变量来控制

2. `控制的名字.Set类型("控制项目"，实现条件);`

3. jump到fall,应为jumping false,falling ture

4. fall到idle,应为falling false,idle ture

   ```c#
    void SwitchAnim()
    {
        if (anim.GetBool("jummping"))
        {
            if (rb.velocity.y < 0)
            {
                anim.SetBool("jumping", false);
                anim.SetBool("falling", true);
            }
        }
    }
   ```

   5. 实现落地后效果的改变

      * 判断是否在地上(碰撞)` public LayerMask ground;`,用layermask来判断哪个图层是地面

      * tilemap,layer,添加新图层ground,tilemap选中,player选中

      * 获得碰撞体`public Collider2D coll;`代码部分

        ```c#
        else if (coll.IsTouchingLayers (ground))
                {
                    anim.SetBool("falling", false);
                    anim.SetBool("idle", true);
                }
        ```

      * 避免回到地上后状态一直不动，需要先设置` anim.SetBool("idle", false);`

      * 将box collider 2D拖入coll框


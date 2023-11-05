## study unity:slightly_smiling_face:

### 导入素材

1. 在商店购买：window,asset store,buy
2. 导入：window,package manger,my assets,import

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

1. 查看每个键的功能：edit,project settings,input manager,axes
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
5. 如果想避免只运行一次记得勾选Loop time

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

### 修复移动错误

避免人物与地面接触时产生卡顿：同时使用box collider与circle collider,将代码中所获得的coll改为circle collider

### 简化代码

将不会更改的组件改为private,并写入start,使游戏在一开始获得

```c#
 rb = GetComponent<Rigidbody2D>();
 anim = GetComponent<Animator>();
```

如果想方便debug但仍保持私有，可在private前加[SerializeField]

看看这个[unity手册](https://docs.unity.cn/cn/current/Manual/index.html)

### 镜头控制

1. 使main camera的x,y轴跟player一致

   * 注意大小写，大写是函数，小写是所取的值

   * 避免视野超出边界，如果跳跃幅度不大的话可将y改为0`transform.position = new Vector3(player.position.x,0, -10f);`
2. window,package manager,all packages,cinemachine
   * 在gameobject里面，添加2D camera
   * 镜头大小调lens,ortho size
   * 镜头在一定范围内不动body,dead zone
   * 添加背景 ctrl+D,w拖动，设置一个新文件夹create empty管理
   * add extension,cinemachineconfiner
   * background,add component,polygon collider,edit,不需要的点按住ctrl点击
   * cinemachineconfiner，查找选择background
   * 在background里面点击trigger

### 物品收集

1. 创建item,并在sorting layer里选择图层使其显示出来，添加相应的动画和cotroller

2. 在新创建的物品里面选择添加碰撞体，调整好碰撞大小后，将其设为触发器“is trigger"

3. 编写触发代码`private void OnTriggerEnter2D(Collider2D collision)`,给cherry添加tag以便认出来

   ```c#
   if (collision.tag == "Collection")
   {
       Destroy(collision.gameObject);
   }
   ```

   注意tag的大小写:label:

4. 写一个计数代码

5. 创建一个预制文件夹"perfabs",将制作好的人物及其他素材拖进来，以便下一个卡关能直接使用

6. 创建一个新的文件夹，收纳collections,并用ctrl+D不断复制粘贴到想要的位置

### 优化游戏体验

1. 快速放大找到物体：边栏选中相应物体，按F和view tool
2. coll之间存在摩檫力，导致碰撞后不能下落，需更改材质
   * assets,create,2D,physic material 2D
   * 更改fiction,拖动到可能产生碰撞后静止的player coll上

3. 解决超级跳跃问题：使player只能从地面起跳，`coll.IsTouchingLayers(ground)`

### UI

1. 创建UI，canvas
2. canvas,UI,legacy,text,按F键，将text拖动到合适位置，输入文字，调整字体，大小，颜色等
3. 创建另一个可变的量，需要重复以上操作，代码部分：需先引用`using UnityEngine.UI;`,然后创建`public Text CherryNum;`,将需要改变的UI量拖入`CherryNum`框中
4. 编写代码`CherryNum.text = cherry.ToString();`==将整形变量转化为字符型：`***.ToString()`==
5. 全屏游玩：game右上角选择paly maximized
6. 改变屏幕比例使其适应游玩画面：在每一个canvas子项目中进行定位点选择

### 增加敌人

1. 先创建好人物，添加碰撞体和静止动画效果

2. 不能用on trigger模式，判断碰撞效果(踩到敌人时敌人消失，并再次跳跃）：添加tag

   ```c#
     private void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.tag == "frog")
        {
            if (anim.GetBool("falling"))
            {
                Destroy(collision.gameObject);
                rb.velocity = new Vector2(rb.velocity.x, jumpfroce * Time.fixedDeltaTime);
                anim.SetBool("jumping", true);
            }
        }
    }
   ```

   (getbool是ture的时候才能运行)

### 受伤效果

1. 添加受伤动画,与动画关系

2. 判断如果不是跳跃的时候碰撞就受到伤害，切换受伤与不受伤`private bool isHurt;`,如果受伤则跳过movement函数，并在受伤移动后使ishurt变为ture,在动画效果中如果移动让ishurt变为false

   ```c#
   else if(isHurt)
           {
               if (Mathf.Abs(rb.velocity.x) < 0.1f)
               {
                   isHurt = false;
               }
           }
   ```


3. 编写hurt与idle动画切换
4. 使受伤后能直接回到idle模式：在使hurt切换为true之后设置``anim.SetFloat("running", 0);`

### AI敌人移动

1. 给敌人添加一个新的代码，拖动到对应敌人

2. 获得刚体，设置左右移动位置：右键create new,创建子项目==子项目会跟随主项目一起移动==，点W拖动移动范围，可以点击右框左上角立方体切换颜色，使其醒目

3. 获得left right两个点的值：`public Transform leftpoint,rightpoint;`,将两个量拖动到对应框中

4. 做到让人物面向那边就向那边移动：`private bool Faceleft = true;`(先设置图标方向为真值)，在编写movement代码(使移动可调节需要先`public float speed;`)

   ```c#
    void Movement()
    {
        if (Faceleft)
        {
            rb.velocity = new Vector2(-speed, rb.velocity.y);
            if(transform.position .x<leftpoint.position.x)
            {
                transform.localScale = new Vector3(-1, 1, 1);
                Faceleft = false;
            }
        }
        else
        {
            rb.velocity = new Vector2(speed, rb.velocity.y);
            if (transform.position.x > rightpoint.position.x)
            {
                transform.localScale = new Vector3(1, 1, 1);
                Faceleft = true;
            }
        }
    }
   ```

   ==使子项目脱离主项目(让两个标记点不动):==

   1. `transform.DetachChildren();`

   2. 避免play之后出现多个left和right的方法：~~获得两个值之后直接销毁~~先创建两个新变量来接收值:` public float leftx, rightx;`,交换后销毁原先的变量：

      ```c#
      leftx = leftpoint .position.x;
      rightx = rightpoint.position.x;
      Destroy(leftpoint.gameObject);
      Destroy(rightpoint.gameObject);
      ```

      之后全部用新变量来代替

5. 想要重复使用，可以拖动到预制中

### Animation Events

1. 从高处平台下落也能消灭敌人：

   ```c#
   if(rb.velocity.y < 0.1f && !coll.IsTouchingLayers(ground))
   {
       anim.SetBool("falling", true);
   }
   ```

2. 用代码写jumping代码
3. idle到jump:用animation events来控制动画的切换，使一项动画播放结束后再调用函数执行下一项动画，可将update中调用的函数（movement）删除

### class 调用

1. 创建敌人死亡动画，任何动作时都可能死亡，选择any state,将其声明为一个trigger
2. 再不同的人物代码里相互调用`frog frog = collision.gameObject.GetComponent<frog>();`,之后可以任意调用frog里面的函数，例如：`frog.JumpOn();`

3. 创建一个父集，使所有的敌人都可以获得相同的效果

   ```c#
   public class enemy : MonoBehaviour
   {
       protected Animator anim;
       // virtual是为了让子集其他函数也能实现
       protected virtual void Start()
       {
           anim = GetComponent<Animator>();
       }
       //这里全部使用public
       public void Death()
       {
           Destroy(gameObject);
       }
       public void JumpOn()
       {
           Debug.Log(anim);
           anim.SetTrigger("death");
       }
   }
   ```

4. 子集改动：
   * 将类改为父集名称，例如：`public class frog : enemy `
   * 将`void start`修改为` protected override void Start()`
   * 再start后添加`base.Start();`以调取父集中的函数

### 音效Audio

1. 去asser store中寻找音频和bgm,导入

2. 再player中添加audio source,如果要使之后角色都带有对应音效，可再有边框上方找到ovcerrides选择apply all

3. 选择一个~~卡瓦~~合适（:triangular_flag_on_post:)的bgm托入audio clip窗口中，好好看看下面的选项，是否让游戏一开始就播放(play on awake)并且循环(loop),调整声音大小(volume)和节奏(pitch)

4. 设置敌人爆炸的声音(?):点击角色后面的箭头进入prefab模式，重复第三步，在代码中实现

   ```c#
   protected AudioSource deathAudio;
   deathAudio = GetComponent<AudioSource>();
   deathAudio.Play();
   ```

5. 设置另一个人物的audio source时可以直接copy component,paste component as new
6. 添加人物跳跃的声音，再给player加一个audi source,重复以上操作，`public AudioSource JumpAudio;`,拖动对应音频到代码框


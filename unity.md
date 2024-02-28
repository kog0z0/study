## study unity:star2:

### 导入素材

1. 在商店购买：window,asset store,buy
2. 导入：window,package manger,my assets,import



### Tilemap(编辑素材)

网格 unit

1. 调整像素点Pixels Per Unit
2. 拖拽背景(回到原始位置Transform,reset)
3. 2D object,tilemap,取消back勾选
4. window,2D,Tile palette,create new palette
5. 如果是多元素素材，选中后将Sprite Mode改为multiple,然后进行Sprite Editor，选择type,进行切割==注意此时的像素点==,apply
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

####  Horizontal(水平方向)

1. GetAxis:获得-1到1之间某个值，可以用来操控速度
2. GetAxisRaw：获得-1，0，1某个值，可用来控制人物面朝方向

#### 使运动平滑

1. 将update改为fixedupdate
2. 将移动距离*实际时间变化率`Time.deltaTime`
3. 如果人物移动速度太慢记得更改速度
4. 避免人物与地面接触时产生卡顿：同时使用box collider与circle collider,将代码中所获得的coll改为circle collider



### 跳跃:jack_o_lantern:

1. 定义一个jumpforce`public float jumpforce`
2. 接受玩家反馈：`if(Input.GetButtonDown("Jump"))`
3. 可调整jumpforce和gravity scale来改变跳起高度捏
4. ==按空格键有时跳跃有时不动解决方法==
   * fixedupdate对应time.fixeddeltatime
   * update对应time.deltatime
   * :exclamation:rb在fixedupdate中使用
   * :exclamation:getbutton在update中使用

####  实现跳跃的动画效果

​	==idle是初始动画，以下相关操作可以不进行==

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

#### 二段跳(其实是三段跳)

```c#
//先在让人物脚下创建一个子项目，检测是否在地上
public bool isground;
isground = Physics2D.OverlapCircle(groundcheck.position,0.2f,ground);//这个在fixedupdate里面执行
public int extraJump;
void newJump()//这个放到update里面去
 {
     if (isground)
     {
         extraJump = 2;
     }
     if(Input.GetButtonDown("Jump")&&extraJump > 0)
     {
         rb.velocity = Vector2.up * jumpfroce;
         extraJump--;
         anim.SetBool("jumping", true);
     }
     if(Input.GetButtonDown ("Jump")&&extraJump ==0&&isground)
     {
         rb.velocity = Vector2.up * jumpfroce;//建议jumpforce设小一点
         anim.SetBool("jumping", true);
     }
 }
```



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
                transform.localScale = new Vector3(-1, 1, 1);//改变方向
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
   if(rb.velocity.y < 0.1f &&!coll.IsTouchingLayers(ground))
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
            GetComponent<Collider2D>().enabled = false;//避免二次碰撞
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

   （一次性重命名ctrl+R）



### 对话框Dialog

1. canvas,UI,panel,更改颜色和定位位置

2. 在panel中创建text,可以去store里面找找字体，然后疯狂调整颜色和大小直到在Game窗口中看得合理

3. 设置一关到下一关的触发点：在相应位置添加碰撞体(例如：the door(?)),并将其设置为trigger

4. 设置为碰撞到才会启动对话框，离开时对话框消失：为碰撞体新增一个代码，命名为enterdialog,选择人物的tag

   ```c#
   public class enterDialog : MonoBehaviour
   {
       public GameObject enter;//将需要的对话框拖动过来
       private void OnTriggerEnter2D(Collider2D collision)
       {
           if (collision.tag == "Player")
           {
               enter.SetActive(true);
               //setactive使gameobject显示或不显示
           }
       }
       private void OnTriggerExit2D(Collider2D collision)
       {
           if (collision.tag == "Player")
           {
               Enter.SetActive(false); ;
           }
   	}
   }
   ```

   （tag请务必注意大小写，QWQ，差点死在这里了）

5. 先将需要后续触发的对话框勾选掉

6. 让dialog有渐入的效果：创建一个animation,拖动到enter dialog,点击红色按钮开始录制，设置框和文字的不透明度来达到效果



### 增加趴下状态

1. 点开gizmos可以看到运动中的碰撞体
2. 趴下时上方coll消失，起立再重现：将要关掉的coll设置为discoll,便于调整`public Collider2D Discoll;`,趴下动画切换时`Discoll.enabled = false;//禁用效果`,同样设置重现
3. 当上方有物体时不能起立：再趴下动画之前先判断`if (!Physics2D.OverlapCircle(cellingcheck.position,0.2f,ground))`
   * 第一个获取项是定位点，用子项目的方法设置在角色头顶处
   * 第二个获取项是判断的范围
   * 第三个获取项是需要判断的碰撞体



### 场景控制sceneManager

1. 人物掉落到边界，触发死亡效果，并重置游戏：

   *  创建一个新项目，添加coll，按住alt以中心点为轴拖动到边框处，设置为trigger

   * 让有戏重置需要使用`using UnityEngine.SceneManagement;`

   * ```c#
      if (collision.tag == "Deadline")
      {
          SceneManager.LoadScene(SceneManager.GetActiveScene().name);//获得当前场景开始的效果
      }
     ```

   * 设置delay，死亡时音乐消失

     ```
     if (collision.tag == "Deadline")
     {//延迟播放（字符型函数名，延迟时间）
         Invoke("Restart", 2f);
     }
      void Restart()
      {
       GetComponent<AudioSource>().enabled = false;    SceneManager.LoadScene(SceneManager.GetActiveScene().name);
      }
     ```


2. 碰到门时按下按键进入下一个场景：

   ```c#
   using UnityEngine.SceneManagement;
   
   public class Enterdoor : MonoBehaviour
   {
       void Update()
       {
           if(Input.GetKeyDown(KeyCode.E))
           {
               SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex + 1);//当前场景编号加一
           }
       }
   }
   ```

   编号查看：file,bulid settings

   将代码加载在提示框中，使只有提示框出现时才能有效触发按键，进入下一个场景



### 2D光效

1. 需要先暗下去才能亮起来(?)：选中所有grid在tilemap render中将material改为default-diffuse(散射光的材质)
2. 给其他物体(例如player和environment)自己做一个material,直接在assets中右键创建一个material，shader中选择diffuse，拖到第二个场景的material中(不要在perfabs里面改动)
3. 给画面添加光源：在右边栏里面添加light,point light
4. ==Grid里面线的修改==：
   * 第一关蓝线未被渲染：将cell size改为0.99 0.99
   * 第二关灯光照射出蓝色的线：将cell size改为1 1
5. 我们的光是3D的:waxing_crescent_moon:
   * 将scene的2D关掉可以体验到3D效果
   * 按鼠标右键加W A S D可以四处乱逛
   * 如果有必要能调整一下灯的远近
6. 做出一个明亮的火焰！：调节光的range,color,intensity(强度)，将point light指向火焰，ctrl+D添加到其他火焰上
7. 让player亮起来：给player加一个子项目(point light)跟player一起移动,并调整z轴距离
8. 给其他东西加一点light



### 使collections数量正常

(避免在人物运动太快的时候不计数)

* 设置一个collection消失的动画，在动画结束后加上animation events,实现death效果

  ```c#
  public class cherry : MonoBehaviour
  {
      public void Death()
      {
          FindObjectOfType<playercontroller>().CherryCount();//调用别的object里面的函数
          Destroy(gameObject); 
      }
  }
  ```

* 在player代码中播放动画

  ```c#
  if (collision.tag == "cherry")
  {
      GetcherryAudio.Play();
      collision.GetComponent<Animator>().Play("isGet");
      //CherryNum.text = cherry.ToString();
      //这一行在update里面使用，每帧更新一次
  }
  ```



### 视觉差parallax

(让画面更有层次感)

1. 新建一个文件，用于继承background的coll，将其拖到virtual camera缺失框中

2. 让背景，中景，前景跟随摄像机的移动：新将一个代码parallax

   ```c#
   public class NewBehaviourScript : MonoBehaviour
   {
       public Transform Cam;//main camera
       public float moveRate;//移动范围
       private float startPoint;//初始点
       
       void Start()
       {
           startPoint = transform.position.x;
       }
   
       void Update()
       {
           transform.position = new Vector2(startPoint + Cam.position.x * moveRate, transform.position.y);
       }
   }
   ```
   
   将其放进不同的objects中，调整不同的moverate
   
3. 有时需要y轴也跟随镜头移动，修改代码

   ```c#
    public Transform Cam;
    public float moveRate;
    private float startPointX, startPointY;
    public bool lockY;//判定是否需要打开y轴视觉差
    
    void Start()
    {
        startPointX = transform.position.x;
        startPointY = transform.position.y;
      //需要这两个变量先定义一边，避免数据变化产生错误
    }
   
    void Update()
    {
        if (lockY)
        {
            transform.position = new Vector2(startPointX + Cam.position.x * moveRate, transform.position.y);
        }else
        {
            transform.position = new Vector2(startPointX + Cam.position.x * moveRate , startPointY + Cam.position.y * moveRate);
        }
    }
   ```



### 主菜单main menu

1. 创建一个新的场景menu，添加一个panel(圆角矩形)，将source image改为none,使其完全填充

2. 将准备好的图片拖动到panel框中，改变亮度

3. 再调一个panel滤镜来承接按钮们，记得改名来区分一下，给这个项目添加一个子项目UI，button—textmeshpro(后缀是让字体更丰富)
   * 调整button及其text的大小和位置
   * 调整text字体，字体样式及其颜色(可以选择渐变色color gradient)
   * 调整button的颜色(不要在image里面更改)，将normal color透明度改为0，可使其在鼠标未到达时没有颜色:heavy_check_mark:
   * 创建一个title

4. 用代码来控制：

   ```c#
   using UnityEngine.SceneManagement;
   
   public class Menu : MonoBehaviour
   {
       public void Play()
       { SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex + 1);//记得在bulid settings里面添加
       }
       public void Exit()
        {
            Application.Quit();
        }
    }
   ```
   
   * 先将代码赋给mainmenu(包含button和title的项目)
   * button想要调用其中的函数，在下方on click,将mainmenu拖动过来，选中相应函数
   
5. 让游戏滤镜画面缓慢出现：先将source image改为none,使其完全填充，[接下来看这里](#对话框Dialog),将动画效果的loop删掉

5. 颜色出现以后，文字才出现：将所有的文字塞进新的空项目里，来控制这个项目的启动(命名为UI)

   ```c#
   public void UIEnable()
   {
      GameObject.Find("Canvas/Mainmenu/UI").SetActive(true);//寻找到这个项目并将其开启
   }
   ```
   



### 暂停菜单AudioMixer

1. 先创建一个pause的button

2. 再来一个panel,当作pause的menu,包含一个text(PauseMenu),一个滑动条，一个退出按钮

   * 滑动条:UI,slider background(背景)，fidd area(填充区域)，handle slide area(滑动的钮)

3. 用代码实现：

   ```c#
   public GameObject pauseMenu;//先获得
    public void PauseGame()
    {
        pauseMenu.SetActive(true);
    }
   ```

   可将代码挂载再canve下面，再在pause按钮里面加载函数

   退出实现：

   ```c#
   public void ResumeGame()
   {
       pauseMenu.SetActive(false);
   }
   ```

   在resume里面获得

4. 想要暂停时游戏人物及其他物体都不运动，需要控制时间比例：`Time.timeScale = 0f;`,恢复可改为1，慢速可设置为0.5

5. 用slider来调整声音：
   * 在assets创建一个AudioMixer
   
   * window,audio,audiomixer,打开调音台
   
   * 将bgm的output改为master，即可在调音台里操作，将滑动条的最大和最小值与调音台相同，最小改为-80,最大改为0
   
   * link:先调用`using UnityEngine.Audio;`
   
     ```c#
     public void SetVolume(float value)//读入声音
     {
         audioMixer.SetFloat("Mainvolume", value);//（调音台函数名，在这个函数里面的命名）
     }
     ```
   
     :exclamation:想要获得audiomixer的函数调用，需要选中需要的部分，在inspector框中，右键此部分选则expose,然后返回Audiomixer在exposed parameters里面查看并重命名(回车)
   
   * slider选择时记得选择没有引用量的那一个
   
   * 哪个声音需要可调就把其output改为master



### 手机控制

（啊？好神奇）

1. 去file,build settings,switch platform



### 游戏生成Build

1. 在file,build setings里面选择player settings更改游戏信息
   * default icon游戏开始的图片
   * cursor hotspot游戏光标



### 多对象池

*大量重复物体被使用和销毁（冲锋残影）时使用，避免溢出，提高效率。*

先将每个残影设计好，并SetActive(false),设计UI按钮，玩家按下按钮时，按照队列顺序插入在人物本身的位置，随时间不透明度不断降低，不透明度为0后，将这个残影取消，SetActive(false)

+ 先创建一个空项目命名为Shadow ,获得Sprite Render,使其能获得图片，颜色，大小，透明度等信息，挂载ShadowScript代码

```c#
ublic class ShadowScript : MonoBehaviour
{
    private Transform player;//不使用拖拽获得，而是在物体启动时直接实时获得

    private SpriteRenderer thisSprite;
    private SpriteRenderer playerSprite;

    private Color color;

    [Header("时间控制参数")]
    public float activeTime;//显示时间
    public float activeStart;//显示开始的时间点

    [Header("不透明度控制")]
    private float alpha;
    public float alphaSet;//初始值
    public float alphaMultiplier;//变化乘积，透明度乘以乘积，不断变浅直到消失

    private void OnEnable()//启动时获得
    {
        player = GameObject.FindGameObjectWithTag("Player").transform;
        thisSprite = GetComponent<SpriteRenderer>();
        playerSprite = player.GetComponent<SpriteRenderer>();

        alpha = alphaSet;

        thisSprite.sprite = playerSprite.sprite;

        transform.position = player.position;
        transform.localScale = player.localScale;
        transform.rotation = player.rotation;//转身时的正负值

        activeStart = Time.time;
    }
    
    void FixedUpdate()//保持帧率
    {
        alpha *= alphaMultiplier;

        color = new Color(0.5f, 0.5f, 1, alpha);//R,G,B,透明度，效果偏蓝

        thisSprite.color = color;

        if (Time .time>=activeStart +activeTime)//时间超过显示时间
        {
            //返回对象池
            ShadowPool.instance.ReturnPool(this.gameObject);
        }
    }
}
```

+ 先不设置图片，放回预制体中

+ 重建一个空物体，取名为ShadowPool,控制所有的对象，并挂载相应代码

  ```c#
  public class ShadowPool : MonoBehaviour
  {
      public static ShadowPool instance;//设置为单例模式，方便调用
  
      public GameObject shadowPrefab;
  
      public int shadowCount;
      
      private Queue<GameObject> availableObject = new Queue<GameObject>();//队列
  
      void Awake()
      {
          instance = this;//单例
          
          //初始化对象池
           FillPool();
      }
      
       public void FillPool()
   {
       for(int i = 0; i < shadowCount; i++)
       {
           var newShadow = Instantiate(shadowPrefab);//预制体成为临时对象
           newShadow.transform.SetParent(transform);//均生成为当前对象池的子集
           
           //生成之后就取消启用，返回对象池
           ReturnPool(newShadow);
       }
   }
      
          public void ReturnPool(GameObject gameObject)
      {
          gameObject.SetActive(false);
  
          availableObject.Enqueue(gameObject);
      }
         public GameObject GetFormPool()//出队列并显示
      {
             
          if (availableObject .Count == 0)
          {
              FillPool();
          }//避免速度，时间超过限制，再次填充
          var outShadow = availableObject.Dequeue();
  
          outShadow.SetActive(true);
          return outShadow;
      }
  }
  ```


+ 在playercontroller中设置冲锋有关参数

  ```c#
   [Header("Dash参数")]
   public float dashtime;//dash时长
   private float dashTimeLeft;//冲锋剩余时间
   private float lastDash=-10f;//上一次dash时间点（先设为负值避免游戏开始时不能冲锋）
   public float dashCoolDown;//冷却时间
   public float dashSpeed;
  
   public bool isDashing;
  
   void Dash()//在Update中调用
  {
      if (Input.GetKeyDown(KeyCode.J))
      {
          if(Time.time >=(lastDash +dashCoolDown))
          {
              //执行dash
              ReadyToDash();
          }
      }
  }
  
   void ReadyToDash()
  {
      isDashing = true;
      dashTimeLeft = dashtime;
      lastDash = Time.time;
  }
  
  void GoDash()//在fixedUpdate中使用
  {
      if(isDashing)
      {
          if(dashTimeLeft > 0)
          {
              rb.velocity = new Vector2(dashTimeLeft * horizontalMove, rb.velocity.y);
              dashTimeLeft -= Time.fixedDeltaTime;
              ShadowPool.instance.GetFormPool();
          }
          if (dashTimeLeft <= 0)
          {
              isDashing = false;
          }
      }
  }
  ```

  

### 泛型单例

+ 单例：便于访问和引用

+ 泛型单例：工具类的捏——迅速让某个类实现单例

  例如[通知所有敌人人物已经死亡](BV1FQ4y1d7fG)

  ```c#
  public class Singleton<T>: MonoBehaviour where T:Singleton <T>//Templet
  {
      private static T instance;
  
      public static T Instance
      {
          get { return instance; }
      }
  
      protected virtual void Awake()
      {
          if (instance != null)
              Destroy(gameObject);
          else
              instance =(T) this;
      }
  
      public static bool IsInitialized
      {//判断当前单例模式是否生成过
          get { return instance != null; }
      }
      
       protected virtual void OnDestroy()
       {//销毁
           if (instance ==this)
           {
               instance = null;
           }
       }
  
  }
  ```

  + 直接继承在代码名字后面，来使用单例



### 观察者模式

+ 订阅&通知——创建一个事件中心来订阅和通知消息

```c#
public class EventCenter //唯一的即设计为单例模式
{
    private static EventCenter instance;
    //存储所有事件

    private Dictionary<string, IEventInfo> _eventDic = new Dictionary<string, IEventInfo>();//添加字典来存储事件列表，字典的key对应订阅的名称，value对应订阅的列表（封装的事件响应）


    public static EventCenter Instance
    {
        get
        {
            if (instance == null)
            {
                instance = new EventCenter();
            }
            return instance;
        }
    }
    
    public void AddEventListener(string name,UnityAction action)
    {//添加订阅消息——判断事件名是否在字典中
        if (_eventDic.ContainsKey(name))
        {
            (_eventDic[name] as EventInfo).actions += action;
        }
        else
        {
            _eventDic .Add (name ,new EventInfo(action));
        }
    }

    public void EventTrigger(string name)
    {//通知消息——订阅了的均发出通知
        if (_eventDic .ContainsKey(name))
        {
            if ((_eventDic[name] as EventInfo).actions != null)
            {
                (_eventDic[name] as EventInfo).actions.Invoke();
            }
        }
    }

    //清除无用的消息——封装Remove和Clear方法
    public void RemoveEventListerer(string name,UnityAction actiom)
    {
        if (_eventDic.ContainsKey (name))
        {
            (_eventDic[name ]as EventInfo).actions -= actiom;
        }
    }

    public void Clear()
    {
        _eventDic.Clear();
    }
}

    
    public interface IEventInfo//通知给感兴趣的订阅者
	{

	}
    
    public class EventInfo:IEventInfo//用UnityAction方法封装响应事件
    {
        public UnityAction actions;

        public EventInfo (UnityAction action)
        {
            actions += action;
        }

    }

```

+ 以上为无参方法，仅通知发生了变化，不通知具体内容

  + 有参方法则用泛型

  ```c#
  public class EventInfo<T> : IEventInfo
  {
      public UnityAction<T> actions;
      public EventInfo (UnityAction<T> action)
      {
          actions += action;
      }
  }
  
  //添加带事件的监听
  public void AddEventListener<T>(string name,UnityAction<T> action)
  {
      //旧事件
      if (_eventDic.ContainsKey(name))
          (_eventDic[name] as EventInfo<T>).actions += action;
      //新事件
      else
          _eventDic .Add (name ,new EventInfo<T>(action));
  }
  
  //移除带参数事件的监听
  public void RemoveEventListerer<T>(string name,UnityAction<T> actiom)
  {
      if (_eventDic.ContainsKey (name))
          (_eventDic[name ]as EventInfo).actions -= actiom;
  }
  
  //分发带参数的事件
  public void EventTrigger<T>(string name,T info)
  {
      if (_eventDic .ContainsKey(name))
          if ((_eventDic[name] as EventInfo<T>).actions != null)
              (_eventDic[name] as EventInfo<T>).actions.Invoke(info);
  }
  ```

+ 就可以不用Update来每帧判断力
  + 发生变化的地方用EventTrigger,检测变化的地方用EventListener

### 字典

~~?总感觉上面用过~~

+ 字典方法Dictionary——通过key找到value

  ```c#
  Dictionary<keyType, valueType>命名 = new Dictionary<keyType, valueType>();//初始化一下
  {//如果有初始值可以先定义
      {key1,value1},
      {key2,value2}
  };
  
  	//添加&删除
  	命名.Add(key,value);
  	命名.Remove(key);
  	
  	//输出
  	print(命名[key]);
  
  	//修改
  	命名[key]=xxx;
  
  	//判断字典中有无某个key
  	if(命名.ContainsKey[key])
      
  ```

  

### 背包

+ UI部分

  在canvas下创建一个空物体命名为PackagePanel，设置几个特殊位置的物体并定位好，按键——单独设置选中状态,正常状态和选中高亮效果；

  滚动容器——创建Scroll View,选择滚动条的位置和显示，在content中使用Grid Layout Group和Content Size Fitter来调整，其下添加空物体，命名为Package UI item，拖入perfabs中(使其成为预制键)

  Package UI item——设置Selet DeletSelet 两种状态，分别调整图标，划分为top和buttom两个部分

  DetailPanel——在PackagePanel下的Center中再建新物体，命名为DetailPanel，用于放置详细介绍，同样进行位置划分。添加文本时可将Vertical Overflow 改为Overflow(文本超过最大宽度时自动换行)

  功能按钮——删除和查看详情
  
  + 删除：删除状态指示(DeletePanel),包含背景图片，返回按钮，多选按钮，选中数量提示，确认删除按钮等
  
  
  
  可用方法：Horizontal Layout Group(水平排序) Content Size Fitter(自适应宽度)

### 例子系统

### 事件委托




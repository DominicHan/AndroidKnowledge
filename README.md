# AndroidTips

总结Android知识点

##1.RxJava
RxJava就是观察者模式的扩展，它就是观察者Observer或Subscriber订阅subscribe被观察者Observable，接受被观察者发出的事件（事件可以是你自定义的对象）
这些事件发送的形式也可以有多种形式（依次发送）just、（数组拆分）from等等。还可以将事件序列中的对象或整个序列进行加工处理，转换成不同的事件或事件序列
它同时支持通过线程控制（调度器）Scheduler灵活的切换线程

##2.MVC
视图（View）：用户界面。

控制器（Controller）：业务逻辑

模型（Model）：数据保存

View 传送指令到 Controller

Controller 完成业务逻辑后，要求 Model 改变状态

Model 将新的数据发送到 View，用户得到反馈


##3.Gilde
对比Picasso默认Bitmap格式是RGB_565低于ARGB_8888，内存开销要低很多

##4.图片加载
不同来源图片加载

图片的压缩

图片缓存LruCache

任务队列 调度策略 LILO

##5.内存泄露检测

##6.Design Support Library
FloatingActionButton  	MD风格的圆形按钮，来自于ImageView

Snackbar 类似Toast，添加了简单的单个Action

TabLayout 选项卡

NavigationView 	DrawerLayout的SlideMenu

CoordinatorLayout 	超级FrameLayout


##7.屏幕适配
1、使用wrap_content、match_parent、weight；

2、使用相对布局，尽量不使用绝对布局；

3、使用限定符，如：layout-large/xxx.xml，这样大屏设备就会自动使用该布局；

4、使用最小宽度限定符，如：layout-sw700dp/xxx.xml，这样当屏幕的宽度超过700dp时就会使用该布局；

5、针对不同的分辨率，提供具体的布局，如：res/layout-800x480/xxx.xml；

6、使用布局别名
如：layout下面有个布局mylayout.xml
在layout-large/xxx.xml里面使用mainlayout作为别名

7、使用.9图片，只需要有一张放在drawable下面即可；

8、使用dp，sp而不是px来作为长度单位；

9、提供大小不同的位图，如：mdpi，hdpi等目录下面要有相对应的位图

10、判断加载的布局来决定走不同的流程

##8.View的绘制过程
measure（测量视图的大小），layout（确定视图的位置）draw（绘制视图的内容）

1、这三个过程都是从上而下，从父到子的，即：先设置父视图，然后遍历子视图，并对其设置。

2、自定义view时，我们可以重写onMeasure（非必须）和onDraw方法，在onMeasure的实现里调用setMeasuredDimension或者super.onMeasure来设置视图大小。

3、自定义ViewGroup时，我们可以重写onLayout（必须）方法，在里面调用view的layout方法设置视图的位置。

	通过上面分析可以看出measure过程主要就是从顶层父View向子View递归调用view.measure方法（measure中又回调onMeasure方法）的过程。


##9.SurfaceView 

##10.handler vs AsyncTask
AsyncTask,是android提供的轻量级的异步类,可以直接继承AsyncTask,在类中实现异步操作,并提供接口反馈当前异步执行的程度(可以通过接口实现UI进度更新),最后反馈执行的结果给UI主线程。

优点:
	简单,快捷
	过程可控   
    
缺点:
	在使用多个异步操作和并需要进行Ui变更时,就变得复杂起来


Handler 异步实现时,涉及到 Handler, Looper, Message,Thread四个对象，实现异步的流程是主线程启动Thread（子线程）thread(子线程)运行并生成Message-Looper获取Message并传递给Handler，Handler逐个获取Looper中的Message，并进行UI变更。

优点：
	结构清晰，功能定义明确
	对于多个后台任务时，简单，清晰

缺点：
	在单个后台异步处理时，显得代码过多，结构过于复杂（相对性）

	
##11.设计模式
看看“懒汉式”单例和“饿汉式”单例，一目了然。“饿汉式”单例只有在你使用到该单例的时候，才会去实例化一个对象，并一直保存在内存中。
而“饿汉式”单例则是在程序一启动的时候就被加载到了内存中，是一种优先使用系统资源的方式。

###单例：
饿汉式
	public class Singleton {
		private Singleton() {}
		private static Singleton mInstance = new Singleton();
		public static Singleton getInstance() {
			return mInstance;
		}
	}

懒汉式

加上synchronized
	public static synchronized Singleton getInstance(){}

会降低整个访问的速度，而且每次都要判断。可以用双重检查加锁。

双重加锁机制，指的是：并不是每次进入getInstance方法都需要同步，而是先不同步，进入方法过后，先检查实例是否存在，如果不存在才进入下面的同步块，这是第一重检查。进入同步块后，再次检查实例是否存在，如果不存在，就在同步的情况下创建一个实例。这是第二重检查。
					
双重加锁机制的实现会使用一个关键字volatile，它的意思是：被volatile修饰的变量的值，将不会被本地线程缓存，所有对该变量的读写都是直接操作共享内存，从而确保多个线程能正确的处理该变量。

	public class Singleton {
		private Singleton() {}
		/*
		*	对保存实例的变量添加volitile的修饰
		*/
		private volatile static Singleton mInstance = null;
		public static Singleton getInstance() {
			//先检查实例是否存在，如果不存在才进入下面的同步块
			if (mInstance == null) {                    // Single Checked
				//同步块，线程安全的创建实例
				synchronized(Singleton.class) {
					//再次检查实例是否存在，如果不存在才真正的创建实例
					if (mInstance == null) {            // Double checked
						mInstance = new Singleton();
					}
				}
			}
			return mInstance;
		}
	}

	/*
	 *	静态持有者模式
	 */
	public class Singleton {
		 /**
	     * 类级的内部类，也就是静态类的成员式内部类，该内部类的实例与外部类的实例
	     * 没有绑定关系，而且只有被调用时才会装载，从而实现了延迟加载
		 */
		private Singleton() {}
		private static class SingletonHolder() {
			 /**
	         * 静态初始化器，由JVM来保证线程安全
	         */
			private static Singleton mInstance = new Singleton();
		}
		public static Singleton getInstance() {
			return SingletonHolder.mInstance;
		}
	}

	/**
	 * 使用枚举来实现单例模式的示例
	 */
	public enum Singleton {
	    /**
	     * 定义一个枚举的元素，它就代表了Singleton的一个实例
	     */
	    uniqueInstance;
	    /**
	     * 示意方法，单例可以有自己的操作
	     */
	    public void singletonOperation(){
	        //功能树立
	    }
	}

###工厂：http://blog.csdn.net/yehui928186846/article/details/51318071

应用：现在的app大多数都是由少数几个activity和众多的fragment组成，那么针对这些fragment，我们可以开辟一个工厂，针对不同的需求生产不同的fragment

简单工厂：

     一个总代理类，
     确定好参数，传入，传出。
     确定好使用到的方法，以便子类重写
     
     所有实现具体算法的类都要继承这个代理类
     内部实现具体的算法
     按照设计模式的四大原则，必须实现父类的全部方法
     
     提供一个供外部调用的类
     外部传入参数，这个类根据参数的不同获取不懂的算法，得到不懂的结果
     而方法的多少是根据之前写好的实现类而定

抽象工厂：
	1，抽象工厂（包含所有抽象产品）
	2，抽象产品
	3，工厂实现类（包含实例产品）
	4，产品实现类（具体实现产品的逻辑）

抽象工厂的目的是把工厂和产品都变成抽象。
这样，不管你是新增加工厂还是新增加某个工厂下的产品都只需要实现抽象的接口就可以了。 
抽象工厂就简单工厂和工厂方法模式来说，每个具体工厂可以生产多种产品。而同时又可以拥有多个工厂。


###观察者：

观察者模式的特点：

1，将一个系统的功能分割成一系列相互协作的类。当一个通知者对象的状态发送改变时，所以依赖于它的观察者对象都得到通知并被自动更新。

2，一个通知者可以有无数个依赖于它的观察者。通知者发出通知的时候不需要知道他的观察者是谁。

3，任何一个具体的观察者也不需要知道其他通知者的存在。

观察这模式使用的场景

1，当一个对象的改变需要同时改变其他对象的时候。并且在程序不知道到底目标对象有多少个的时候，就应该使用。

2，当一个逻辑中一个方面要依赖于另一个方面的时候，这个时候就应该用观察者模式将两个具体逻辑封装在独立的对象，这样可以让他们独自改变和复用。

一、需求确定

在android APP中我们有一些设置项目，在设置完了以后，希望在主页面能够立即响应，例如QQ的清空聊天记录，我们希望设置了以后回到主页面后会自动清理，或许你会认为这是一件很简单的事情，回到主页面直接读缓存就好了，缓存里面是什么就是什么，但这种方案存在2个问题：

1、什么时候读取缓存，是每次回到主页面都去刷新吗，这样会太消耗资源，用户体验也不好。

2、不能局部刷新数据。

因此从功能和代码结构上面来看我的需求主要有以下几点：

1、能够在某些页面设置完了后直接通知其他监听了这个事件的页面立即刷新，而不需要用户回到某些页面的时候再刷新。

2、能够区分是哪些事件通知的，从而针对不同的事件进行不同的处理。

3、能够动态的扩展事件类型，可以让调用者很快的注册和监听事件。

二.系统设计

一个完整的观察者模式需要这些对象：

1、Subject 抽象主题角色：也就是抽象的被观察者对象，里面保存了所有的观察者对象引用列表，提供了注册和反注册的功能。

2、ConcreteSubject具体的主题角色：将有关状态存入各ConcreteObserver对象    当它的状态发送改变时，向它的各个观察者发出通知 。

3、Observer 抽象观察者 ：为所有的具体观察者定义一个接口，在得到通知时更新自己。

4、ConcreteObserver 具体观察者：维护一个指向ConcreteObserver对象的引用 ，存储有关状态，这些状态应与目标的状态保持一致，实现Observer的更新接口是自身状态与目标的状态保持一致。

Android设计模式之观察者模式的应用实践.gif

针对在android中需要设计一个一个抽象的BaseObserverActivity，让所有的Activity页面都去继承它，从本质上来看继承这个类的所有的Activity都是一个观察者，然后再观察者对象中去定义需要监听是什么类型的事件和根据对应的事件的处理。

###代理：
代理模式的目的是：对其他对象提供一种代理以控制对这个对象的访问。
代理模式有很多种，使用场景。
1，远程代理。为一个对象在不同的地址空间提供一个局限性的代表。这样可以隐藏一个对象存在于不同地址空间的事实。
外部使用这个局限性的代表，而不知道真实的对象存在于哪里。

2，虚拟代理。如果一个对象开销很大，可以创建这个对象的代理来存放实例化需要很长时间的这个对象。

3，安全代理。用来控制真实对象访问时的权限。一般用于对象需要不同的访问权限的时候。

4，智能指引。当调用真实对象的时候，代理处理另外一些事情。

让proxy类代理了RealSubject需要做的一些事情。同时，RealSubject也可以有多个代理类，每个代理类可以做不同的逻辑处理，这样，需要什么样的逻辑处理就实例化相对应的代理类。

###命令：
命令模式的概念：将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；让封装的这个对象可以对请求排队或记录请求日志，以及支持可撤销的操作。

通俗点：命令模式的本质是对命令进行封装，将发出命令的责任和执行命令的责任分割开。

	对于命令模式来说，命令接受者（Receiver ）中定义了很多方法。举个栗子：
	1，命令接受者（Receiver ）就像一个遥控器。其中有加音量的方法（CommandA）,跳频道的方法（CommandB）等等。
	2，当我们拿起遥控器的时候，就相当于实例化了一个遥控器CommandA（）对象，并把加音量，跳频道的方法放在了遥控器里面。
	3，当我们手指按下加音量按键的时候就相当于实例化了一个Invoker（）命令控制对象，把加音量这个具体命令放进去了。
	4，按键按下的时候就发送了加音量的命令（invoker.executeCommand();）

	也就是说，Receiver 中包含了所有供外部调用的方法。但是这个类不允许外部调用，创建一个命令类供外部发出命令，命令执行Receiver 的哪些方法。
	这样，有助于记录某步操作具体执行了哪些方法。并且可以选择哪些命令可以执行可以过滤。

	所以，我们来总结一下命令模式的作用：
	1，比较容易的设计出一个命令队列
	2，在需要的情况下可以将命令记录下来
	3，允许接收请求的一方决定是否拒绝请求
	4，很容易对请求实现撤销和重做
	5，加入新的具体命令不会影响其他类，容易加入新的具体命令类

总结：把请求操作的对象与执行操作的对象分割开,每一个命令都是一个操作：请求的一方发出请求，要求执行一个操作；接收的一方收到请求，并执行操作。

###适配器：
基本概念：
将一个类的接口转换成程序需要的另一个接口。adapter 使得原本由于接口不兼容而不能一起工作的那些类一起工作。
适配器模式有两种，类适配器和对象适配器

使用场景：
想要使用一个已经存在的类，它的方法或者所做的事情基本相同的时候就可以考虑使用适配器模式。当然，前提是这两个类都不太容易修改的时候使用。

使用目的：
当两段十分相似的逻辑无法合并的时候就需要使用适配器模式来让相同的接口下适配不同的方法结构。说到底就是为了减少程序接口，统一接口方法的一种优化方法。

###组合：

###访问者：

###建造者：


##12.Git
常用命令

git init 

git add

git commit

git status

git diff

git log    git reflog

git reset --hard commit_id

git checkout -- file 直接丢弃工作区的修改

git reset HEAD file  (git add后)

git remote add origin git@server-name:path/repo-name.git  要关联一个远程库

合并某分支到当前分支：git merge <name>


##13.hotfix
android类加载过程是 PathClassLoader 其核心方法是findClass方法 

##14.事件分发机制
其实事件分发机制，可以分为两个过程，一个是向下分发过程，一个是向上返回过程，其中向下分发是靠dispatchTouchEvent 方法，从Activity的dispatchTouchEvent 直到目标视图的dispatchTouchEvent ，然后进入目标视图的onTouchEvent，向上返回则是靠onTouchEvent，从目标视图的onTouchEvent直到Activity的onTouchEvent。
而在向下分发的过程可能会提前结束，这受onInterceptTouchEvent影响，也可能是ViewGroup没有子视图了，在两个因素都可以使该过程提前结束，从而提前进入向上返回的过程。向上返回过程也可能提前结束，如果事件在onTouchEvent中返回true，即事件被消费，那么事件不会继续向上传递，该过程提前结束。

##15.https
http://www.jianshu.com/p/93fdebe5fef1

超文本传输协议HTTP协议被用于在Web浏览器和服务器之间传递信息。

    HTTP协议以明文方式发送内容，不提供任何方式的数据加密，如果攻击者截取了Web浏览器和网站服务器之间的传输报文，就可以直接读懂其中的信息，因此HTTP协议不适合传输一些敏感信息，比如信用卡号、密码等。

    为了解决HTTP协议的这一缺陷，需要使用另一种协议：安全套接字层超文本传输协议HTTPS。为了数据传输的安全，HTTPS在HTTP的基础上加入了SSL协议，SSL依靠证书来验证服务器的身份，并为浏览器和服务器之间的通信加密。


HTTP的之下加入了SSL

    认证用户和服务器，确保数据发送到正确的客户机和服务器；(验证证书)

    加密数据以防止数据中途被窃取；（加密）

    维护数据的完整性，确保数据在传输过程中不被改变。（摘要算法）

HTTPS和HTTP的区别主要为以下五点：

    https 用的 443 端口， http 用的 80 端口

    https协议需要到ca申请证书，一般免费证书很少，需要交费。

    http是超文本传输协议，信息是明文传输，https 则是具有安全性的ssl加密传输协议。

    http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。

    http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。


##16.框架搭建
搭建页面框架，使用空白页

常用工具类

base


##17.android数据库
greenDAO是一个可以帮助Android开发者快速将Java对象映射到SQLite数据库的表单中的ORM解决方案，通过使用一个简单的面向对象API，开发者可以对Java对象进行存储、更新、删除和查询。

##18.属性动画

##19.进程间通讯方法
跨进程访问activity，需要action(android.intent.action.CALL)和uri，例如拨打电话的activity

Content Provider

Broadcast

AIDL 


##20.sleep wait的区别
sleep（100L）意思为：占用CPU，线程休眠100毫秒

wait（100L）意思为：不占用CPU，线程等待100毫秒 

##21.线程安全
如果你的代码所在的进程中有多个线程在同时运行，而这些线程可能会同时运行这段代码。如果每次运行结果和单线程运行的结果是一样的，而且其他的变量的值也和预期的是一样的，就是线程安全的。

##22.LruCache
LruCache中Lru算法的实现就是通过LinkedHashMap来实现的。LinkedHashMap继承于HashMap，它使用了一个双向链表来存储Map中的Entry顺序关系，这种顺序有两种，一种是LRU顺序，一种是插入顺序，这可以由其构造函数public LinkedHashMap(int initialCapacity,float loadFactor, boolean accessOrder)指定。所以，对于get、put、remove等操作，LinkedHashMap除了要做HashMap做的事情，还做些调整Entry顺序链表的工作。LruCache中将LinkedHashMap的顺序设置为LRU顺序来实现LRU缓存，每次调用get(也就是从内存缓存中取图片)，则将该对象移到链表的尾端。调用put插入新的对象也是存储在链表尾端，这样当内存缓存达到设定的最大值时，将链表头部的对象（近期最少用到的）移除。

##23.MeasureSpec.specMode
EXACTLY：一般是设置了明确的值或者是MATCH_PARENT

AT_MOST：表示子布局限制在一个最大值内，一般为WARP_CONTENT

UNSPECIFIED：表示子布局想要多大就多大，很少使用

1)不论父View 的specMode是EXACTLY、UNSPECIFIED、AT_MOST的哪一种，如果子view在xml文件里面把layout_width或者layout_height这是了具体
  的dimen值、或者在代码里调用相应的方法为view的宽度或者高度设置了具体的值，那么此时widthSpec的mode 或者heightSpec的mode就是EXACLTY,size就等于layout_width或者
  layout_height的值。此时子view的specMode值不受父View的specMode的影响。

2)当父View的specMode为EXACTLY的时候：父View强加给子View一个确切的大小,有如下两种情况
    2.1)子View的layout_width或者layout_height设置为MATCH_PARENT的时候，子View的specMode为EXACTLY
    2.2)子View的layout_width或者layout_height设置为WRAP_CONTENT的时候，子View的specMode为AT_MOST
    2.3)不论子view为match_parent或者wrap_content，resultSize都等于父类的size.

3)当父view的specMode为AT_MOST的时候：父View强加给一个最大的size给子view，最大的size也就是父view的size
    3.1)此时不论子view的为match_parent或者wrap_content，子view的specMode都为AT_MOST
    3.2)resultSize的大小被设置为父view的大小

4)当父view的specMode为UNSPECIFIED的时候：
    4.1) 此时不论子view的为match_parent或者wrap_content，子view的specMode都为UNSPECIFIED
    4.3)此时reusltSize = 0

5)从对UNSPECIFIED分析可以知道,在1)情况之外的情况下，要是想让子view自己决定自己的宽度或者高度的大小，MeasureSpec.makeMeasureSpec(0,UNSPECIFIED)这么调用来获取宽度和高度
的spec

6)根据方法的参数以及方法的实现可以知道，视图的大小是由父视图和子视图共同决定的，前提是子视图没有设置具体的dimen值

##24.布局优化
避免Overdraw 过度绘制

1.合理选择控件容器  
	LinearLayout易用，效率高，表达能力有限。RelativeLayout复杂，表达能力强，效率稍逊。

2.按需去掉window的默认背景 
	
3.去掉其他不必要的背景

4.ClipRect & QuickReject
	通过canvas.clipRect()来帮助系统识别那些可见的区域，在clipRect区域之外的绘制指令都不会被执行

5.ViewStub
	



##25.定位项目中，如何选取定位方案，如何平衡耗电与实时位置的精度？

方案1： 考虑到应用中有多处地方需要使用位置请求，在Application类中开始定位，Application持有一个全局的公共位置对象，然后隔一定时间自动刷新位置，每次刷新成功都把新的位置信息赋值到全局的位置对象，然后每个需要使用位置请求的地方都使用全局的位置信息进行请求。 该方案好处：请求的时候无需再反复定位，每次请求都使用全局的位置对象，节省时间。 该方案弊端：耗电，每隔一定时间自动刷新位置，对电量的消耗比较大。

方案2：按需定位，每次请求前都进行定位。这样做的好处是比较省电，而且节省资源，但是请求时间会变得相对较长。


##26.MD5加密算法
    对MD5算法简要的叙述可以为：MD5以512位分组来处理输入的信息，且每一分组又被划分为16个32位子分组，经过了一系列的处理后，算法的输出由四个32位分组组成，将这四个32位分组级联后将生成一个128位散列值。

     第一步、填充：如果输入信息的长度(bit)对512求余的结果不等于448，就需要填充使得对512求余的结果等于448。填充的方法是填充一个1和n个0。填充完后，信息的长度就为N*512+448(bit)；

     第二步、记录信息长度：用64位来存储填充前信息长度。这64位加在第一步结果的后面，这样信息长度就变为N*512+448+64=(N+1)*512位。

     第三步、装入标准的幻数（四个整数）：标准的幻数（物理顺序）是（A=(01234567)16，B=(89ABCDEF)16，C=(FEDCBA98)16，D=(76543210)16）。如果在程序中定义应该是（A=0X67452301L，B=0XEFCDAB89L，C=0X98BADCFEL，D=0X10325476L）。有点晕哈，其实想一想就明白了。

     第四步、四轮循环运算：循环的次数是分组的个数（N+1） 

##27.base64编码
	base64的编码都是按字符串长度，以每3个8bit的字符为一组，
	然后针对每组，首先获取每个字符的ASCII编码，
	然后将ASCII编码转换成8bit的二进制，得到一组3*8=24bit的字节
	然后再将这24bit划分为4个6bit的字节，并在每个6bit的字节前面都填两个高位0，得到4个8bit的字节
	然后将这4个8bit的字节转换成10进制，对照Base64编码表 （下表），得到对应编码后的字符。

##28.对Context的理解
        1、它描述的是一个应用程序环境的信息，即上下文。
        2、该类是一个抽象(abstract class)类，Android提供了该抽象类的具体实现类(后面我们会讲到是ContextIml类)。
        3、通过它我们可以获取应用程序的资源和类，也包括一些应用级别操作，例如：启动一个Activity，发送广播，接受Intent信息 等。。

##29.StrongReference、WeakReference和SoftReference的认识
强引用（StrongReference）：就是在代码中普遍存在的，类似Object obj = new Object()这类的引用，只要强引用还存在，GC永远不会回收掉被引用的对象。

软引用（SoftReference）：用来描述一些还有用但非必须的对象。对于软引用关联着的对象，在系统将要发生内存溢出异常时，将会把这些对象列入回收范围之中进行第二次回收。如果这次回收还没有足够的内存，才会抛出内存溢出异常。在JDK 1.2之后，提供了SoftReference类来实习软引用。

弱引用（WeakReference）：也是用来描述非必须对象的，但是它的强度比软引用更弱一些，被弱引用关联的对象只能生存到了下一次GC发生之前。当GC工作时，无论当时内存是否足够，都会回收只被弱引用关联的对象。在JDK 1.2之后，提供了WeakReference类来实现弱引用。

虚引用（PhantomReference）：这个引用po主没有提到，不过也可以顺带了解一下。虚引用也称幽灵引用或者幻影引用，它是最弱的一种引用关系。一个对象是否有虚引用的存在，完全不会对其生存时间构成影响，也无法通过虚引用来取得一个对象实例。为一个对象设置虚引用的唯一目的就是在这个对象被GC回收是收到一个系统通知。在JDK 1.2之后提供了PhantomReference类来实现虚引用。

##30.get请求和post请求
根据HTTP协议。他们只有一点根本区别，简单点儿说，一个用于获取数据，一个用于修改数据。具体的请参考RFC文档。
get请求的数据会附在URL之后，POST把提交的数据则放置在是HTTP包的包体中


##31.


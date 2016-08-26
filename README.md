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
单例：http://blog.csdn.net/yehui928186846/article/details/51317744

工厂：http://blog.csdn.net/yehui928186846/article/details/51318071

观察者：

代理：

命令：

适配器：

组合：

访问者：

装饰：

建造者：


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
HTTP的之下加入了SSL

    认证用户和服务器，确保数据发送到正确的客户机和服务器；(验证证书)

    加密数据以防止数据中途被窃取；（加密）

    维护数据的完整性，确保数据在传输过程中不被改变。（摘要算法）


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


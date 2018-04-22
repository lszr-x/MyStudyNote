---
title:人生总有想贴东西到屏幕上的时候，咦，我的屏幕脏了——Android悬浮窗初探
---
# 人生总有想贴东西到屏幕上的时候，咦，我的屏幕脏了——Android悬浮窗初探

>  作者：不洗碗工作室 - lszr

>  文章出处：[Android声音相关总结](https://lszr-x.github.io/)

>  版权归作者所有，转载请注明出处

很多时候，你会想在你的屏幕上加些东西，<del>比如一个ingress游戏的画图工具</del>,或者在小窗播放视频的同时干些别的东西，虽然现在的很多的Android系统已经支持分屏效果，但是，有时候需要自己能够改变悬浮窗的位置和大小，或者在别的应用界面实现一些快捷功能，又不想去一直打开app占据整个或者一半的屏幕，这时候悬浮窗就是一个不错的选择（shortcut：还有我呢！！！）。这里首先介绍一些预防针。

## 预防针

### 1、Window：万恶之源

Window就是字面上的意思，窗口，根据书上和比如的博客所说的，Window是所有的显示在外的布局的基础，像什么Activity，View啊什么的，都是它的延伸，也就是说，基本上所有的UI界面都和它有关，<del>它就是万恶之源，这磨人的小妖精</del>,有关Window的实现原理这里不作讨论（其实还没看完2333），只需要知道，如果想要添加悬浮窗，是需要使用WindowManager这个管理器进行进行一系列乱七八糟的骚操作就可以实现一个悬浮窗了。
	
	
### 2、WindowManager：一个超好用的管理器

windowManager字面意思就是Window的管理器，但是它主要肩负的责任只有三个，就是addView，removeView，updateViewLayout，换句话说就是增，删，改（没有查），但是一般来讲，对于一个展示出来的View，有这三个方法也就够了。但是，天降降大任于管理器，它这么点功能肯定是够用，但是是不是有点太单调了，而且有的属性不去设置的话可能引起错误，举个简单的例子：我使用WindowManager添加了两个View，一个叫view1，一个叫李狗蛋，而且都是充满屏幕的，那么怎么去分辨谁该在上面，谁该在下面呢？（正常的上下关系，李狗蛋很直的）这时，就需要一个大杀器，也就是WindowManager中的一个特别特别大，特别特别长的内部类了，也就是LayoutParams这个类，param本意是参数，所以我们一般来使用这个类来进行很多Window所添加的View的参数的设置。
	
顺便一提，对于WindowManager来说，获取它的实例需要调用getSystemService(WINDOW_SERVICE)这个方法，在后面会用到的
	
### 3、LayoutParams：强大的参数类（LayoutParams：我不是，我没有，别瞎说啊）

![](http://p34is8180.bkt.clouddn.com/18-4-21/57292700.jpg)

上图只是LayoutParams中参数的冰山一角，全部参数长得让人没有想看下去的欲望，于是去找官方文档和别人的博客总结，官方的关于LayoutParams的总结真的是<del>又臭</del>又长，但是讲道理，官方对于参数的解释很详尽而且准确，可以当做字典来看看，很全面。LayoutParams中有两大主要参数，一个是type，一个是<del>各种比如打完就结婚这种的</del>flags。

对于type来说，它所决定的是你想要使用的Window的形式，这里就要先引入一个概念，就是Window有哪些种类，一般来讲，Window有三个种类：


<font color="#87CEFF">

+ 	应用类Window，也叫Application Window，就是Activity那一级的Window，换句话说，<del>每有一个Activity被杀害，
	就有一个应用类Window被销毁</del>

	
+ 	子类Window，也叫Sub-windows ，这个级别要高一些了，一般是Dialog那一级的Window，也就是那些骑在Activity上面作威作
   福的Window类就是这个级别

	
+ 	系统类Window，也叫System windows，这个级别就更高了，<del>比前面那些高到不知道哪里去了</del>,这种一般是Toast和系
   统状态栏这种Window的级别。
   
</font>

综上，type可以被大概得分为这三种形式，分别对应1~999，1000~1999，2000~2999的int值，然后，数值越高自然等级更高，显示得就更上面了，也就是常说的站得高看得远的道理（吧？）。

对于type来说，有很多可供选择的属性，由于属性较多，在[官方文档](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html)中解释的较为清楚，这里不再给出具体的列表<del>（其实是我懒。。。。我觉得就算加上也没人会看完的说。。。。）</del>

对于flags来说呢，更像是一种属性的集合，一般用的比较多的有这些属性：

+	FLAG_NOT_FOCUSABLE：表示Window不需要获取焦点，也不需要接收各种输入事件，此同时会启动FLAG_NOT_TOUCH_MODAL，最终事件会直接传递给下层具有焦点的Window。

+	FLAG_NOT_TOUCH_MODAL：在此模式下，系统会将当前Window区域外的单击事件传递给底层的window，当前Window区域内的单击事件则自己处理，这个标记很重要，一般来说，都要开启这个标记，因为不开启这个标记，其他Window就获取不到单击事件了。

同样的，flags的可选的属性也是又！多！又！长！，这里不列出所有的了<del>（占地方）</del>

现在预防针打完了，让我们进入正题吧（感觉前面的就我不知道了。。。萌新瑟瑟发抖）

## 少年，开始<del>（让你的屏幕变脏吧）</del>实现你的悬浮窗吧

### 总路线：
+ 要实现的目标：实现可以在其他页面或者app上进行移动的悬浮窗
+ 首要目标：实现普通在本应用的悬浮窗
+ 附加目标：使用service进行前台服务的请求，外加手势监听


### 首先我们要实现一个<del>小目标</del>悬浮窗
 根据前面的准备知识来看，我们要实现一个悬浮窗，需要使用WindowManager添加一个View，并且设置它的type至少在Activity之上，然后设置对应的flag。上面的是一个大概的思路，具体实现的时候，首先要考虑一点，就是悬浮窗的这个类，继承哪一个父类去实现这个功能呢，Activity肯定是不行的，同理fragment也不行（辣鸡，哼），但是有一个大佬是可以做到的，那就是<del>李狗蛋</del>，啊呸，Service，这个大佬是可以实现悬浮的窗的效果的，身为Android四大组件之一，身兼重要的职位，有着巨大的能力<del>（虽然我也不知道他有什么能力）</del>，下面就请Service开始表演：
 
 Service是服务，一般在页面中调用startService启动服务，然后调用Service中的onCreate方法，因此，我们写一个NormalSuspensionService类来继承Service：
 
 ![](http://p34is8180.bkt.clouddn.com/18-4-22/28214432.jpg)
 
好，那么现在我们建立了应该继承自Service的类，然后重写onCreate，onDestroy，onBind方法，这是Service被调用需要执行的方法：

![](http://p34is8180.bkt.clouddn.com/18-4-22/87873820.jpg)

首先，onCreate这个方法是启动Service以后会被调用的第一个方法，如果多次启动活动的话，onCreate不会被多次调用，会多次调用onStartCommand方法，这里不作过多探讨（关于Service的东西等我有时间再总结一个吧。。。）
现在，方法也重写好了，准备也妥当了，那么我们就开始<font color="#87CEFF">happy</font>吧。
首先建立对应要使用的对象：来来来，WindowManager走一个，LayoutParams来一套，左手一个View，右手一个ImageView：

	private WindowManager mWindowManager;
    private WindowManager.LayoutParams mLayoutParams;
    private View mView;
    private ImageView mImageView;

建立完对象好比资源找好了，钱充足了，要开战之前还需要什么，当然是人了：先写个layout，这就是悬浮窗的布局了，然后用LayoutInflate绑一下，美滋滋，悬浮窗就写完一半了：

	mView= LayoutInflater.from(this).inflate(R.layout.suspension_window_layout,null);
	
然后呢，想加点击事件还是什么操作的就直接往上招呼就行，跟在Activity里面写没啥区别，简单易学，轻松愉快，我的布局是这样的：

![](http://p34is8180.bkt.clouddn.com/18-4-22/65379064.jpg)

一个很简单的布局，图片传的是这张：

![](http://p34is8180.bkt.clouddn.com/18-4-22/83015421.jpg)
 
<del>02厨无所畏惧.jpg</del>

之后的工作就很简单了，有个WindowManager，初始化来一发：

	mWindowManager=(WindowManager)getSystemService(WINDOW_SERVICE);
	
有个LayoutParams，你也来一次：

	mLayoutParams=new WindowManager.LayoutParams();
	//看见了吗，这熟悉的参数，是预防针的香气
    mLayoutParams.type= WindowManager.LayoutParams.TYPE_APPLICATION_OVERLAY;
    mLayoutParams.flags= WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE| 
    							WindowManager.LayoutParams.FLAG_NOT_TOUCH_MODAL;
    mLayoutParams.width=500;
    mLayoutParams.height=250;
 
 
LayoutParams设置了参数和大小以后，调用WindowManager的addView方法，悬浮窗，启动！

	mWindowManager.addView(mView,mLayoutParams);
	
咦，等等，还没设置startService，启动毛啊，连个监听事件都没有，打开MainActivity，加个button，写个点击监听，加一句：<del>宇宙战舰，启动</del>

	startService(new Intent(MainActivity.this,NormalSuspensionWindow.class));
	
但是，有时候，有时候~，我会相信一切不能启动~，反而会让Activity崩掉。这是怎么回事呢，查看log发现是这样的错误：

![](http://p34is8180.bkt.clouddn.com/18-4-22/39576586.jpg)

![](http://p34is8180.bkt.clouddn.com/18-4-22/20704558.jpg)

这个原因是因为没有申请权限，而且这个权限不是普通的权限，和那些什么储存啊，拍照啊，麦克风这些妖艳贱货不一样的权限，是关于Window能在页面上展示的权限，很高端的，所以要用骚操作：

	Intent intent2 = new Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION);
    intent2.setData(Uri.parse("package:" + getPackageName()));
    startActivityForResult(intent2,100);

会打开一个对应的权限页面，至于是否允许权限判断，这里就不在给出，对了，还要在manifest中加入：

	    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />

声明权限，上面的一切都做完以后，运行app，点击主页按钮，在同意权限请求以后，你就会发现，你的手机<del>死机了</del>中间出现了02的特写

![](http://p34is8180.bkt.clouddn.com/18-4-22/25352044.jpg)

![](http://p34is8180.bkt.clouddn.com/18-4-22/52897623.jpg)

<del>我觉得我的手机壁纸挺好看的</del>

好，现在悬浮窗就讲完了，什么，你说上面的关于移动悬浮窗的部分还没写吗？哎呀，这种东西很简单的，就是写个触摸事件就ok啦，轻轻松松就写完了，还不会吗，就是给你的悬浮窗的控件加个触摸监听，重写个ontouch方法，里面写下移动的逻辑就好，对了，记得使用updateViewLayout方法来更新你的悬浮窗的位置和状态，要不然肯定是动不了的啦。

等等，好像还有一件事，对了，关于悬浮窗的保活的相关问题，这个问题要分情况，要是列出来估计又是一大段，大家就不要相互折磨了，关注下我们不洗碗工作室，估计近期就会更新，到时候会和（伪）后台录制视频结合起来一起讲的，再见了。

对了，这是我第一次用这种文风写总结，如有冒犯/失敬/对不起的地方，<del>过来打我啊.jpg</del>，在此郑重表示我诚挚的歉意。

最后的最后，还有一件事，欢迎大家来我的博客玩（虽然博客很烂，文章也不多，但是我会努力的。。。）

>参考博客目录：
>
>[WindowManager和WindowManager.LayoutParams的使用以及实现悬浮窗口的方法](https://blog.csdn.net/core_ice/article/details/52464125)
>
>[WindowManager.LayoutParams的type属性](https://blog.csdn.net/zyjzyj2/article/details/53823447)
>
>[理解Window和WindowManager](https://www.cnblogs.com/cr330326/p/6364328.html)
>
>[ Android 带你彻底理解 Window 和 WindowManager](https://blog.csdn.net/yhaolpz/article/details/68936932)
>
>[WindowManager和WindowManager.LayoutParams的使用以及实现悬浮窗口的方法](https://blog.csdn.net/core_ice/article/details/52464125)



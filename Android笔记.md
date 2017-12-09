# 笔记
## 2017-11-21
### Scrollview嵌套问题
	Scroview内部嵌套控件时只能嵌套单个，可以把内部多个控件用LinearLayout或者Relativelayout包含起来
### Nestedscroview嵌套Recylerview问题
	recylerviewTeamMember.setNestedScrollingEnabled(false);
	可以解决问题，但是注意item的最外层控件的height要是wrap_content，不然不会显示第二项
##2017-11-27
### TooBarActivity中改变样式的方法
	布局文件中添加一个width为0的TextView，然后添加对应的set方法，在继承的activity文件
	中写一个initTitle方法，对于此方法，使用findViewById时要提前调用getToolBar方法然
	后再调用findViewById方法
	然后后面点击事件一般正常使用即可
### 关于Activity退出相关事宜（singleTask)
	方法一：
	它的功能就是
	如果要启动的Activity不在，就创建新的然后放到栈顶。
	如果要启动的Activity已经在栈顶，就跟singleTop模式一样。
	如果要启动的Activity已经在于栈中，就会干掉这个Activty上面的所有其他Activty。
	
	举个栗子：
	1.A-B-C-D------E
	A-B-C-D-E
	
	2.A-B-C-D------D
	A-B-C-D------D
	
	3.A-B-C-D------B
	就变成
	A-B
	
	（作者：eiun
	链接：http://www.jianshu.com/p/5ecf6955dac8
	來源：简书）
#

	方法二：使用静态变量：
	在 ActivityA 里面设置一个静态的变量instance,初始化为this
	在 ActivityB 里面, ActivityA.instance.finish();（未尝试）
	具体实现例子见：http://blog.sina.com.cn/s/blog_6e334dc701018m2v.html
### EditText光标位置问题
	当EditText使用时，如果height高于textSize时，光标默认居中，需要设置
		android:gravity="start"
	使光标位于开头（剩余属性未尝试）
### inflate初步接触
	使用：
	可以在一个java文件中查找xml布局文件，从而达到在不同的java文件中使用xml布局文件中的
	id的效果，第一次实际使用时在AlertDialog中自定义布局文件以后，要绑定不同控件的id，
	直接使用findViewById会闪退，所以要用inflate先查找到xml文件，然后再调用，例子如
	下：
![](http://i4.bvimg.com/620700/5cacf686f3d43ec3.png)

##2017-12-0
	

	
---
title: Android中活动间通信总结
---


>  作者：不洗碗工作室 - lszr

>  文章出处：[Android中活动间通信总结](https://lszr-x.github.io/)

>  版权归作者所有，转载请注明出处

## 一般传输方法
一般来讲，在Android中使用intent进行活动间的跳转，如果要在Activity间进行数据的传输，可以使用Intent的putExtra()函数，可以看到，putExtra()函数可以传递多种类型的参数

![](http://oz3rf0wt0.bkt.clouddn.com/18-1-21/68456980.jpg)

这个函数使用类似哈希的对应方式，以键值对来进行数据的获取，比如，从Activity1到Activity2传一个字符串可以这样写

	Intent intent=new Intent(MainActivity.this,TestActivity.class);
	intent.putExtra("keyName","testString");
	startActivity(intent);

然后在另一个Activity中调用getIntent()方法去接收数据，然后使用getStringExtra方法，传入键值参数,返回对应的数据

	Intent intent=getIntent();
	String testString=intent.getStringExtra("keyName");
	Log.d("lastActivityData",testString);

log出来以后可以看到接收到了前一个Activity传过来的数据

![](http://oz3rf0wt0.bkt.clouddn.com/18-1-21/962905.jpg)

或者可以这样写，也可以获得正确的数据

	Intent intent=getIntent();
	String testString=intent.getExtras().getString("keyName");
	Log.d("lastActivityData",testString);

这种方式和前面的区别就是调用了getExtras方法，getExtras方法返回的是Bundle类型的数据，所以需要调用getString方法获得对应的数据，关于getExtras这个方法，后面还会用到

传递String类型的数据就可以简单地这样调用，传递其他类型的数据也是这样的方法，但是在获取其他类型的数据如int类型的数据的时候，使用getIntExtra方法的时候会有第二个参数defaultValue，这个参数是当没有从前一个Activity传递这个数据或者键值对应数据为空的时候返回这个值，而getStringExtra方法已经在内部实现判空的流程。

在《第一行代码》中，关于Activity跳转及其传递数据有一种较好的方法，减小了不同的Activity之间的耦合度，就是在Activity1中调用Activity2中的静态方法，直接把需要传递的数据作为参数传递进去，可以这样写：


TestActivity中：

	public static void startTestActivity(Context context,String dataString){

        Intent intent =new Intent(context,TestActivity.class);
        intent.putExtra("keyName",dataString);
        context.startActivity(intent);
    }
    
在前一个Activity中调用

	TestActivity.startTestActivity(MainActivity.this,"testData");
	
这样的方法和前一个方法的区别在于，在要启动的Activity中定义要使用的前一个Activity中的数据，以参数的形式传入对应的数据，这样做的好处在于在写两个Activity间的跳转时，不需要阅读第二个Activity的代码也可以直接进行跳转并传入数据。

以上是普通的传递数据的方法，但是在实际开发中，会有各种各样类型的数据，如果数据的数量比较大的时候，一个一个去传入参数会很麻烦而且不易于代码的阅读，因此需要用别的方法去传递数据。

有一种方法是使用Bundle来传递数据，Bundle可以把数据进行类似hash的绑定，以键值对的形式来储存数据到Bundle类型里面，可以在Intent的putExtra方法中直接传递一个Bundle类型的数据，然后在第二个Activity中调用getExtras方法获得对应的Bundle数据，可以这样写：

在Activity1中创建Bundle类型数据并且作为参数传入：

	Bundle bundle=new Bundle();
	bundle.putString("bundleKeyName","testString");
	TestActivity.startTestActivity(MainActivity.this,bundle);

在Activity2中的启动Activity静态方法：

	public static void startTestActivity(Context context,Bundle bundle){
	    Intent intent =new Intent(context,TestActivity.class);
	    intent.putExtra("keyName",bundle);
	    context.startActivity(intent);
	}
	
在Activity2中获取对应的Bundle类型的数据并打印到日志上面：

	Intent intent=getIntent();
	Bundle bundle=intent.getBundleExtra("keyName");
	Log.d("lastActivityData",bundle.getString("bundleKeyName"));

通过日志来看获取到了对应的数据，以上就是使用Bundle类型在Activity间进行通信，以上这种方法调用的是Intent类的putBundleExtra方法和getBundleExtra方法，然后调用Bundle类的getString方法，也可以使用对应的putExtras方法和getExtras方法，可以省去一个key值，此处就不列出具体代码了。

我们可以看到使用Bundle类型的数据，可以有效地减少调用跳转静态方法的时候的对应的参数个数，只传一个Bundle类型对应的数据就可以了，但是，首先如果想要知道要传几个，传什么类型的参数就只能去阅读第二个Activity的代码或者对应的开发人员，会变得比较繁琐，除此之外，对于Bundle类型的绑定也较为麻烦，和使用Intent直接传值区别不大。

## 传输对象

那么，对于数据较多，较为繁琐的时候，我们要怎么样去传递对应的数据呢？首先，较多和较为繁琐的数据一般来讲可以建立对应的Model类，使用对象去储存，那么现在需要解决的就是怎样使用Intent去传递对象，有以下三个方法：

### 1、使Model类型实现Serializable接口

我们需要把要传递的数据进行整理，建立一个对应的类：

	public class PersonModel implements Serializable {
	    public String personName;
	    public String personId;
	    public String personGender;
	
	    public String getPersonName() {
	        return personName;
	    }
	
	    public void setPersonName(String personName) {
	        this.personName = personName;
	    }
	
	    public String getPersonId() {
	        return personId;
	    }
	
	    public void setPersonId(String personId) {
	        this.personId = personId;
	    }
	
	    public String getPersonGender() {
	        return personGender;
	    }
	
	    public void setPersonGender(String personGender) {
	        this.personGender = personGender;
	    }
	}

如上，建立了应该PersonModel类，包含name，id，gender这三个属性，而且要实现对应的接口。然后在Activity1中建立对应的PersonModel对象，然后设置其对应的数据，即要传入的数据，然后调用Activity2中对应的静态方法，传入PersonModel的对象：

	PersonModel mpersonModel =new PersonModel();
	mpersonModel.setPersonGender("男");
	mpersonModel.setPersonName("lszr");
	mpersonModel.setPersonId("123456");
	TestActivity.startTestActivity(MainActivity.this,mpersonModel);
	
然后，在Activity2中的静态方法里面，调用Intent的putExtra方法传入对应的对象：

	Intent intent =new Intent(context,TestActivity.class);
	intent.putExtra("person",personModel);
	context.startActivity(intent);

查看对应的方法源码，可以看到，是调用了putSerializable方法来传入实现Serializable接口的类的对象

	public @NonNull Intent putExtra(String name, Serializable value) {
	    if (mExtras == null) {
	        mExtras = new Bundle();
	    }
	    mExtras.putSerializable(name, value);
	    return this;
	}

说明Intent类在实现的时候重载了各种参数，因此，对应的也会有getSerializableExtra方法来获得传递的对象，具体实现如下：

	Intent intent=getIntent();
	PersonModel personModel=new PersonModel();
	personModel=(PersonModel)intent.getSerializableExtra("person");
	Log.d("lastActivityData",personModel.getPersonGender());
	Log.d("lastActivityData",personModel.getPersonId());
	Log.d("lastActivityData",personModel.getPersonName());
	
可以看到日志中所打印出来的数据，与预期得到的数据相符，证明这种传递对象的方法是可以的

![](http://oz3rf0wt0.bkt.clouddn.com/18-1-21/43259533.jpg)


### 2、使Model类实现Parcelable接口

同样的需要在Model类中实现这个接口，但是有所不同的是，需要在再实现对应的几个方法，Model的代码如下：

	public class PersonModel implements Parcelable {
	    public String personName;
	    public String personId;
	    public String personGender;
	
	    public PersonModel(){
	
	    }
	
	
	    protected PersonModel(Parcel in) {
	        personName = in.readString();
	        personId = in.readString();
	        personGender = in.readString();
	    }
	
	    public String getPersonName() {
	        return personName;
	    }
	
	    public void setPersonName(String personName) {
	        this.personName = personName;
	    }
	
	    public String getPersonId() {
	        return personId;
	    }
	
	    public void setPersonId(String personId) {
	        this.personId = personId;
	    }
	
	    public String getPersonGender() {
	        return personGender;
	    }
	
	    public void setPersonGender(String personGender) {
	        this.personGender = personGender;
	    }
	
	    @Override
	    public int describeContents() {
	        return 0;
	    }
	
	    @Override
	    public void writeToParcel(Parcel dest, int flags) {
	
	        dest.writeString(personName);
	        dest.writeString(personId);
	        dest.writeString(personGender);
	
	    }
	
	    public static final Creator<PersonModel> CREATOR = new Creator<PersonModel>() {
	        @Override
	        public PersonModel createFromParcel(Parcel in) {
	            return new PersonModel(in);
	        }
	
	        @Override
	        public PersonModel[] newArray(int size) {
	            return new PersonModel[size];
	        }
	    };
	}

可以看到，除了正常的get和set方法外，还需要实现对应的describeContents，writeToParcel，createFromParcel，newArray方法，在writeToParcel方法中需要去对对应的数据进行写入，也就是Parcel类型的writeString等方法，剩下的方法都是对应生成的，不需要改变，这里还有一点需要注意的是在生成的构造方法中的类成员的赋值的顺序需要和writeToParcel方法中Parcel对象的写入需要把顺序进行对应，否则顺序会改变，在Activity1中这样写：

	PersonModel mpersonModel =new PersonModel();
	mpersonModel.setPersonGender("男");
	mpersonModel.setPersonName("lszr");
	mpersonModel.setPersonId("123456");
	TestActivity.startTestActivity(MainActivity.this,mpersonModel);
	
然后在Activity2，静态方法中：

	Intent intent =new Intent(context,TestActivity.class);
	intent.putExtra("person",personModel);
	context.startActivity(intent);

获取对应的数据：

	Intent intent=getIntent();
	PersonModel personModel=new PersonModel();
	personModel=(PersonModel)intent.getParcelableExtra("person");
	Log.d("lastActivityData",personModel.getPersonGender());
	Log.d("lastActivityData",personModel.getPersonId());
	Log.d("lastActivityData",personModel.getPersonName());
	
从网上关于Parcelable和Serializable的性能比较之下，Parcelable的性能和速度是要高于Serializable的，但是实现Parcelable接口写起来较为繁琐，如果结合第三方关于Parcelable的库的话可以大大简化实现过程，这里不再赘述。

### 3、使用Json来传递对象

这个方法主要是使用Gson类（需要导入依赖）的toJson类把对象转换成对应的Json类型的数据，然后通过Intent进行传值，代码如下：

PersonModel类：

	public class PersonModel{
	    public String personName;
	    public String personId;
	    public String personGender;
	
	    public PersonModel(){
	
	    }
	
	    public String getPersonName() {
	        return personName;
	    }
	
	    public void setPersonName(String personName) {
	        this.personName = personName;
	    }
	
	    public String getPersonId() {
	        return personId;
	    }
	
	    public void setPersonId(String personId) {
	        this.personId = personId;
	    }
	
	    public String getPersonGender() {
	        return personGender;
	    }
	
	    public void setPersonGender(String personGender) {
	        this.personGender = personGender;
	    }
	}
	
Activity1中：

	PersonModel mpersonModel =new PersonModel();
	mpersonModel.setPersonGender("男");
	mpersonModel.setPersonName("lszr");
	mpersonModel.setPersonId("123456");
	TestActivity.startTestActivity(MainActivity.this,mpersonModel);
	
Activity2中：

静态方法：

	Intent intent =new Intent(context,TestActivity.class);
	String jsonData=new Gson().toJson(personModel);
	intent.putExtra("keyName",jsonData);
	context.startActivity(intent);
	
获取数据：

	Intent intent=getIntent();
	PersonModel personModel= new Gson().fromJson(intent.getStringExtra("keyName"),PersonModel.class);
	Log.d("lastActivityData",personModel.getPersonGender());
	Log.d("lastActivityData",personModel.getPersonId());
	Log.d("lastActivityData",personModel.getPersonName());
	
从代码中可以看出，调用了Gson类的toJson方法和fromJson方法来进行数据的转换，这种方法最简单，但是相较前两个方法速度和性能都要差一些，关于性能的问题这里不再赘述。

综上，这就是基本的关于Android活动间进行数据通信的几个方法。

参考：《Android第一行代码》

[Android中传递对象的三种方法](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0104/2256.html)

[Gson的详细使用](http://blog.csdn.net/oqihaogongyuan/article/details/50944755)

[Android中Intent传递对象的3种方式详解](http://www.jb51.net/article/93762.htm)























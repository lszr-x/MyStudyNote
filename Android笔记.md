---
title: Android笔记第一版
---


# 笔记第一版
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

##2017-12-10
###GET网络请求
> 使用retrofit框架还是有点不熟：

+ 在写response的时候，要能符合它的规范和字段，比如说使用List，使用时要用list.get(i)来获取List对象的item
	

##2017-12-11
###intent传递int值
> 和使用intent传递string类型有所不同

+ 传递string类型时

首先用原intent对象去putExtra("keyString",T)
在接收端去用intent对象使用getIntent()
然后令string类型的变量=intent.getStringExtra("keyString")即可

+ 传递int类型数据时

最后获得数据的时候要调用这个方法intent.getExtras().getInt("keyString");
	
	否则数据会变成0，在直接使用getIntExtra的时候？存疑

getIntExtra方法的第二个参数的含义是什么？


##2017-12-13
###使用intent在Activity和fragment之间进行传递
> 传递普通的单一类型很简单，按照正常的putExtra就可以

+ 传递类型的集合的时候



第一种方法：

一个一个值去putExtra()，因为类似哈希的传值方式，所以可以使用这种方式进行传值，但是
过于繁琐
	
第二种方法：

+ 使用Bundle去传值：

用Bundle的实例化对象去调用putString()方法，然后把所有变量都与
Bundle的对象进行类似哈希的绑定（与第一种方法类似），然后调用intent的putExtras()
的方法，在接收时，先接收intent的getExtras()方法，然后再调用Bundle的get系列方法
	
第三种方法：

+ 使用传递对象的方法：

使用intent传递对象，和传递String类型的值在用法上区别不大，但是
需要对传递的对象的model类继承接口，有两种接口：

> 
	









## 2017-12-14
+ 判断字符串是否为空的方法：

> 字符串为null的时候使用equals的方法会报错（尚未实验），所以一般判断是否为空的时候要先判断是否为null，然后再看是否为空字符串，即：

	if (string==null||string.length()<=0)
且前后顺序不能乱。











##2018-3-13
###TaskGo项目知识点总结

+   mine模块：
	+   圆形头像:
	
	使用如下控件为圆形头像，和正常使用imageView没有区别，一样使用就可以
	
			de.hdodenhof.circleimageview.CircleImageView
            
	+ 加载网络外链图片：

			Glide
                .with(MineFragment.this)
                .load(mineInformationModel.getAvatar())
                .into(imgPortrait);
                
		with后加当前活动的context，load参数为外链地址，into参数为加载到的控件。
		
	+ 刷新页面重写方法：

		onResume()方法,在此方法内进行数据的再次绑定
		
	+ 获取下一个Activity传回的数据方法：使用startActivityForResult方法：

		和调用startActivity方法一样调用此方法，但是参数要多一个requestCode，为自定义数字，为回调时的判断用，在结束新的Activity时要使用setResult方法，参数为第一个自定义的数字（resultCode）和一个Intent（使用putExtra方法传值后），然后在原Activity方法中重写onActivityResult方法，然后在这个方法中获取对应的数据。代码如下：
		
		启动新Activity的方法：
		
		    public static void startChangeNameActivity(Activity activity, Context context) {
		        Intent intent = new Intent(context, ChangeNameActivity.class);
		        activity.startActivityForResult(intent, 10);
		
		    }
	    
		
		新Activity结束时的方法：
			
			Intent intent = new Intent();
	        intent.putExtra("newName", editChangeName.getText().toString().trim());
	        setResult(RESULT_OK, intent);
	        finish();
	
		原Activity中重写的方法：
		
		
				@Override
				protected void onActivityResult(int requestCode, int resultCode, Intent data) {
				 super.onActivityResult(requestCode, resultCode, data);
				
		        if (resultCode == RESULT_OK) {
		
		            switch (requestCode) {
		
		                case FileUtil.CAMERA_REQUEST:
		                    imgPortrait.setImageBitmap(BitmapFactory.decodeFile(photoPath));
		                    UpLoadHelper.upLoadImgToQiNiu(photoPath, this);
		                    mProgressDialog = new DialogUtil().new NativeProgressDialog();
		                    mProgressDialog
		                            .initDialog(EditDataActivity.this)
		                            .setMessage("请稍后")
		                            .showDialog();
		                    hasImg = true;
		                    break;
		                case FileUtil.ALBUM_REQUEST:
		                    String picturePath = FileUtil.getSelectedPicturePath(data, EditDataActivity.this);
		                    imgPortrait.setImageBitmap(BitmapFactory.decodeFile(picturePath));
		                    photoPath = picturePath;
		                    UpLoadHelper.upLoadImgToQiNiu(photoPath, this);
		                    mProgressDialog = new DialogUtil().new NativeProgressDialog();
		                    mProgressDialog
		                            .initDialog(EditDataActivity.this)
		                            .setMessage("请稍后")
		                            .showDialog();
		                    hasImg = true;
		                    break;
		                case 10:
		                    if (resultCode == RESULT_OK) {
		                        String newName=data.getStringExtra("newName");
		                        mTxtMineName.setText(newName);
		                        mPresenter.requestUpdateInformation(newName,mineInformationModel.getAvatar(),mineInformationModel.getSex(),mineInformationModel.getBirth());
		                        mineInformationModel.setName(newName);
		                    }
		                    break;
		                default:
		                    break;
		            }
		        }
		   	 }
		   	 
		   	 
	+ 日期选择控件：

		使用TimePickerView控件进行日期选择：
		
		导入依赖：		compile 'com.contrarywind:Android-PickerView:3.2.5'
		
		具体使用：
		
				TimePickerView timePickerView = new TimePickerView
	                .Builder(EditDataActivity.this, new TimePickerView.OnTimeSelectListener() {
	            @Override
	            public void onTimeSelect(Date date, View v) {
	                //设置日期显示格式
	                SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
	                //获得对应选择的日期
	                String format = simpleDateFormat.format(date);
	                //TODO：写对应的选择完成后的操作
	            }
	        })
	                //设置显示的列的数量，即日期的精确程度
	                .setType(new boolean[]{true, true, true, false, false, false})
	                .build();
	        timePickerView.show();
	        
    拓展：
    
    		Calendar selectedDate = Calendar.getInstance();
			Calendar startDate = Calendar.getInstance();
			startDate.set(2013,1,1);//设置起始年份
			Calendar endDate = Calendar.getInstance();
			endDate.set(2020,1,1);//设置结束年份
			
			
			.setCancelText("取消")//取消按钮文字
          .setSubmitText("确定")//确认按钮文字
          .setContentSize(18)//滚轮文字大小
          .setTitleSize(20)//标题文字大小
          .setTitleText("选择时间")//标题文字
          .setOutSideCancelable(true)//点击屏幕，点在控件外部范围时，是否取消显示
          .isCyclic(true)//是否循环滚动
          .setTitleColor(Color.BLACK)//标题文字颜色
          .setSubmitColor(R.color.hui)//确定按钮文字颜色
          .setCancelColor(R.color.hui)//取消按钮文字颜色
          .setTitleBgColor(0xFF666666)//标题背景颜色 Night mode
          .setBgColor(0xFF333333)//滚轮背景颜色 Night mode
          .setRange(calendar.get(Calendar.YEAR) - 20, calendar.get(Calendar.YEAR) + 20)//默认是1900-2100年
          .setDate(selectedDate)// 如果不设置的话，默认是系统时间*/
          .setRangDate(startDate,endDate)//起始终止年月日设定
          .setLabel("年","月","日","时","分","秒")
          .isCenterLabel(false) //是否只显示中间选中项的label文字，false则每项item全部都带有label。
          .isDialog(false)//是否显示为对话框样式
          .build();

+ 工具类模块：
	+ 文件工具类（FileUtil）：
		+ 动态申请权限：
			
			Google将权限分为两类，一类是Normal Permissions，这类权限一般不涉及用户隐私，是不需要用户进行授权的，比如手机震动、访问网络等；另一类是Dangerous Permission，一般是涉及到用户隐私的，需要用户进行授权，比如读取sdcard、访问通讯录等。
			
			Normal Permissions：
			
				ACCESS_LOCATION_EXTRA_COMMANDS
				ACCESS_NETWORK_STATE
				ACCESS_NOTIFICATION_POLICY
				ACCESS_WIFI_STATE
				BLUETOOTH
				BLUETOOTH_ADMIN
				BROADCAST_STICKY
				CHANGE_NETWORK_STATE
				CHANGE_WIFI_MULTICAST_STATE
				CHANGE_WIFI_STATE
				DISABLE_KEYGUARD
				EXPAND_STATUS_BAR
				GET_PACKAGE_SIZE
				INSTALL_SHORTCUT
				INTERNET
				KILL_BACKGROUND_PROCESSES
				MODIFY_AUDIO_SETTINGS
				NFC
				READ_SYNC_SETTINGS
				READ_SYNC_STATS
				RECEIVE_BOOT_COMPLETED
				REORDER_TASKS
				REQUEST_INSTALL_PACKAGES
				SET_ALARM
				SET_TIME_ZONE
				SET_WALLPAPER
				SET_WALLPAPER_HINTS
				TRANSMIT_IR
				UNINSTALL_SHORTCUT
				USE_FINGERPRINT
				VIBRATE
				WAKE_LOCK
				WRITE_SYNC_SETTINGS
				
		Dangerous Permissions:

				group:android.permission-group.CONTACTS
			  permission:android.permission.WRITE_CONTACTS
			  permission:android.permission.GET_ACCOUNTS
			  permission:android.permission.READ_CONTACTS
			
			group:android.permission-group.PHONE
			  permission:android.permission.READ_CALL_LOG
			  permission:android.permission.READ_PHONE_STATE
			  permission:android.permission.CALL_PHONE
			  permission:android.permission.WRITE_CALL_LOG
			  permission:android.permission.USE_SIP
			  permission:android.permission.PROCESS_OUTGOING_CALLS
			  permission:com.android.voicemail.permission.ADD_VOICEMAIL
			
			group:android.permission-group.CALENDAR
			  permission:android.permission.READ_CALENDAR
			  permission:android.permission.WRITE_CALENDAR
			
			group:android.permission-group.CAMERA
			  permission:android.permission.CAMERA
			
			group:android.permission-group.SENSORS
			  permission:android.permission.BODY_SENSORS
			
			group:android.permission-group.LOCATION
			  permission:android.permission.ACCESS_FINE_LOCATION
			  permission:android.permission.ACCESS_COARSE_LOCATION
			
			group:android.permission-group.STORAGE
			  permission:android.permission.READ_EXTERNAL_STORAGE
			  permission:android.permission.WRITE_EXTERNAL_STORAGE
			
			group:android.permission-group.MICROPHONE
			  permission:android.permission.RECORD_AUDIO
			
			group:android.permission-group.SMS
			  permission:android.permission.READ_SMS
			  permission:android.permission.RECEIVE_WAP_PUSH
			  permission:android.permission.RECEIVE_MMS
			  permission:android.permission.RECEIVE_SMS
			  permission:android.permission.SEND_SMS
			  permission:android.permission.READ_CELL_BROADCASTS
          
          
		首先要检查权限：
			
			if (ContextCompat.checkSelfPermission(thisActivity,
                Manifest.permission.READ_CONTACTS)!= PackageManager.PERMISSION_GRANTED) {
			}else{
			    //
			}
		此处的checkSelfPermission方法即为检查权限是否开启的方法，然后进行权限请求：
		
				ActivityCompat.requestPermissions(activity, PERMISSIONS_STORAGE, REQUEST_EXTERNAL_STORAGE);
				
				
				

		


##20180411
+ Android文件相关：
	+ File类：
		+ 构造方法：参数为文件路径
		+ 手机存储空间根目录：Environment.getExternalStorageDirectory().getCanonicalFile()
		+ 创建新文件方法，首先判断是否有这个文件，如果没有，调用createNewFile()方法创建文件，或者调用mkdirs()方法创建目录：
		
				mRecorderFile = new File(Environment.
											getExternalStorageDirectory().
											getCanonicalFile() + 
											"/projectTest/sound.amr");
	                       if(!mRecorderFile.exists()){
	                           mRecorderFile.createNewFile();
	                       }
	                       

>参考网站：	         
>
>[https://blog.csdn.net/lixiang_Y/article/details/54946199?locationNum=2&fps=1](https://blog.csdn.net/lixiang_Y/article/details/54946199?locationNum=2&fps=1)              
>
>[https://blog.csdn.net/guoqingshuang/article/details/52443423](https://blog.csdn.net/guoqingshuang/article/details/52443423)




















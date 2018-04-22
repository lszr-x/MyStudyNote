---
title: android声音
---




# Android声音相关总结


>  作者：不洗碗工作室 - lszr

>  文章出处：[Android声音相关总结](https://lszr-x.github.io/)

>  版权归作者所有，转载请注明出处




## 1、控制系统声音

Android中系统声音可以直接去控制，不需要获取系统权限，使用方法也很简单，下面看例子：

	public class VolumeControlClass extends AppCompatActivity {
	    public void changeVolume(){
	        //声明AudioManager对象并初始化
	        AudioManager audioManager=(AudioManager) getSystemService(Context.AUDIO_SERVICE);
	
	        //控制闹钟音量
	        audioManager.setStreamVolume(AudioManager.STREAM_ALARM,AudioManager.ADJUST_LOWER,0);
	        //控制DTMF音调的声音
	        audioManager.setStreamVolume(AudioManager.STREAM_DTMF,0,0);
	        //媒体声音
	        audioManager.setStreamVolume(AudioManager.STREAM_MUSIC,0,0);
	        //系统提示音
	        audioManager.setStreamVolume(AudioManager.STREAM_NOTIFICATION,0,0);
	        //电话铃声
	        audioManager.setStreamVolume(AudioManager.STREAM_RING,0,0);
	        //系统音量
	        audioManager.setStreamVolume(AudioManager.STREAM_SYSTEM,0,0);
	        //通话语音音量
	        audioManager.setStreamVolume(AudioManager.STREAM_VOICE_CALL,0,0);
	    }
	}
	
+ 参数说明：

	第一个参数用来指定控制的相关的音量类型，即什么的音量，第二个参数用来指定音量的大小或者说调整方向，第三个参数是附加参数，有如下几个参数：
	
	![](http://p34is8180.bkt.clouddn.com/18-3-28/19143263.jpg)
	
	FLAG\_ALLOW\_RINGER\_MODES			在更改音量时是否包含铃声模式作为可能的选项。

	FLAG\_PLAY\_SOUND					更改音量时是否播放声音。

	FLAG_REMOVE\_SOUND\_AND\_VIBRATE	删除可能在队列中或正在播放的任何声音/振动（与更改音量有关）。

	FLAG\_SHOW_UI						显示包含当前音量的Toast。

	FLAG\_VIBRATE						如果进入振动振铃模式，是否振动。
	
	
## 2、播放音频

	
### 一、使用MediaPlayer播放本地音频


Android中播放音频可以用MediaPlayer进行音频和视频的播放，这里先说播放本地音频的方法：

首先要进行权限请求

	//判断是否有阅读缓存的权限
	if (ContextCompat.checkSelfPermission(playMusicActivity.this, 
										Manifest.permission.WRITE_EXTERNAL_STORAGE) 
										== PackageManager.PERMISSION_GRANTED) {
			//初始化本地播放
            initPlay();
        } else {
            ActivityCompat.requestPermissions(
	            playMusicActivity.this, 
	            new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE}, 
	            1);
        }
        
        
同时要在manifest中加入：
	
	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
	    
作为权限申请。，下面是播放本地视频的初始化条件

	/**
     * 初始化本地播放
     */
    private void initPlay() {
        try {
        	//获取file对象
            File file = new File(Environment.getExternalStorageDirectory(), "music.mp3");
			//设置对应的文件路径
            mMediaPlayer.setDataSource(file.getPath());
          //播放准备
            mMediaPlayer.prepare();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }
    
能够看到，MediaPlayer对象在初始化时应该设置播放路径，然后调用prepare()方法或者prepareAsync()方法，来进行初始化，也就是播放准备，这两个方法区别在于是否异步，prepareAsync()方法是异步的，在播放网络视频中一般调用这个方法来初始化。


![](http://p34is8180.bkt.clouddn.com/18-4-8/58129573.jpg)

这是MediaPlayer的生命周期，关于其中的一些解释如下：

**Idle 状态**：当使用new()方法创建一个MediaPlayer对象或者调用了其reset()方法时，该MediaPlayer对象处于idle状态。这两种方法的一个重要差别就是：如果在这个状态下调用了getDuration()等方法（相当于调用时机不正确），通过reset()方法进入idle状态的话会触发OnErrorListener.onError()，并且MediaPlayer会进入Error状态；如果是新创建的MediaPlayer对象，则并不会触发onError(),也不会进入Error状态。

 

**End 状态**：通过release()方法可以进入End状态，只要MediaPlayer对象不再被使用，就应当尽快将其通过release()方法释放掉，以释放相关的软硬件组件资源，这其中有些资源是只有一份的（相当于临界资源）。如果MediaPlayer对象进入了End状态，则不会在进入任何其他状态了。

 

**Initialized 状态**：这个状态比较简单，MediaPlayer调用setDataSource()方法就进入Initialized状态，表示此时要播放的文件已经设置好了。

 

**Prepared 状态**：初始化完成之后还需要通过调用prepare()或prepareAsync()方法，这两个方法一个是同步的一个是异步的，只有进入Prepared状态，才表明MediaPlayer到目前为止都没有错误，可以进行文件播放。

 

**Preparing 状态**：这个状态比较好理解，主要是和prepareAsync()配合，如果异步准备完成，会触发OnPreparedListener.onPrepared()，进而进入Prepared状态。

 

**Started 状态**：显然，MediaPlayer一旦准备好，就可以调用start()方法，这样MediaPlayer就处于Started状态，这表明MediaPlayer正在播放文件过程中。可以使用isPlaying()测试MediaPlayer是否处于了Started状态。如果播放完毕，而又设置了循环播放，则MediaPlayer仍然会处于Started状态，类似的，如果在该状态下MediaPlayer调用了seekTo()或者start()方法均可以让MediaPlayer停留在Started状态。

 

**Paused 状态**：Started状态下MediaPlayer调用pause()方法可以暂停MediaPlayer，从而进入Paused状态，MediaPlayer暂停后再次调用start()则可以继续MediaPlayer的播放，转到Started状态，暂停状态时可以调用seekTo()方法，这是不会改变状态的。

 

**Stop 状态**：Started或者Paused状态下均可调用stop()停止MediaPlayer，而处于Stop状态的MediaPlayer要想重新播放，需要通过prepareAsync()和prepare()回到先前的Prepared状态重新开始才可以。

 

**PlaybackCompleted状态**：文件正常播放完毕，而又没有设置循环播放的话就进入该状态，并会触发OnCompletionListener的onCompletion()方法。此时可以调用start()方法重新从头播放文件，也可以stop()停止MediaPlayer，或者也可以seekTo()来重新定位播放位置。

 

**Error状态**：如果由于某种原因MediaPlayer出现了错误，会触发OnErrorListener.onError()事件，此时MediaPlayer即进入Error状态，及时捕捉并妥善处理这些错误是很重要的，可以帮助我们及时释放相关的软硬件资源，也可以改善用户体验。通过setOnErrorListener(android.media.MediaPlayer.OnErrorListener)可以设置该监听器。如果MediaPlayer进入了Error状态，可以通过调用reset()来恢复，使得MediaPlayer重新返回到Idle状态。


在初始化完成后，在button监听中写下对应的开始，暂停，停止播放的操作，即调用start(),pause(),stop()方法：

	public void playLocalMusicListener() {
        mBtnStart.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
            		//开始播放
                mMediaPlayer.start();
            }
        });
        mBtnSuspend.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
            		//暂停播放
                mMediaPlayer.pause();
            }
        });
        mBtnStop.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
            		//停止播放
                mMediaPlayer.stop();
                //停止后重新prepare()
                try {
                    mMediaPlayer.prepare();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        });
    }

可以看到，在调用stop()方法后需要重新调用prepare()方法，否则会导致无法重新播放。
如上就是使用MediaPlayer播放本地音频的方法。

### 二、使用MediaPlayer播放网络音频

与播放本地音频不同的是，播放网络视频的时候一般需要进行异步的prepare，因此，需要实现四个接口：

		MediaPlayer.OnCompletionListener,
   		MediaPlayer.OnErrorListener,
        MediaPlayer.OnBufferingUpdateListener,
        MediaPlayer.OnPreparedListener
        
        
这些方法分别为播放完成，错误，缓冲中，缓冲完成的回调方法，在类中这样去重写

	/**
     * 播放完音频后调用的方法
     *
     * @param mp
     */
    @Override
    public void onCompletion(MediaPlayer mp) {
        mBtnNetworkStart.setEnabled(false);
        mBtnNetworkPause.setEnabled(false);
        mBtnNetworkStop.setEnabled(false);
        mMediaPlayerNetwork.prepareAsync();
    }


    @Override
    public boolean onError(MediaPlayer mp, int what, int extra) {

        return false;
    }

    /**
     * 缓冲中调用的方法
     *
     * @param mp
     * @param percent
     */
    @Override
    public void onBufferingUpdate(MediaPlayer mp, int percent) {

    }


    /**
     * 播放远程音频时缓存完成后的操作
     *
     * @param mp
     */
    @Override
    public void onPrepared(MediaPlayer mp) {
        mBtnNetworkStart.setEnabled(true);
        mBtnNetworkPause.setEnabled(false);
        mBtnNetworkStop.setEnabled(false);

    }
    
    
这些方法中我根据按钮点击的逻辑设置了不同的按钮的可点击与不可点击事件的方法。
同样的，播放网络音频也需要像本地音频一样的初始化，代码如下：
	
	/**
     * 初始化网络音频播放
     */
    public void initNetworkPlay() {
        mMediaPlayerNetwork.setOnCompletionListener(this);
        mMediaPlayerNetwork.setOnErrorListener(this);
        mMediaPlayerNetwork.setOnBufferingUpdateListener(this);
        mMediaPlayerNetwork.setOnPreparedListener(this);
        Uri mUri = Uri.parse("http://music.163.com/song/media/outer/url?id=317151.mp3");
        try {
            mMediaPlayerNetwork.setDataSource(this, mUri);
            mMediaPlayerNetwork.prepareAsync();
            mBtnNetworkStart.setEnabled(false);
            mBtnNetworkPause.setEnabled(false);
            mBtnNetworkStop.setEnabled(false);

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
首先要和四个实现的方法进行绑定，然后调用setDataSource()方法，但是之后要调用异步的prepareAsync()。在调用prepareAsync()方法时，需要在对应的缓冲中方法和缓冲完成的方法中调用对应的指令，我在这里是在缓冲完成的回调中是开始播放按钮可点击，暂停和停止不可点击。如下的按钮绑定事件也是有类似的逻辑：

	/**
     * 绑定播放网络音频监听
     */
    public void playNetworkMusicListener() {
        mBtnNetworkStart.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mMediaPlayerNetwork.start();
                mBtnNetworkPause.setEnabled(true);
                mBtnNetworkStop.setEnabled(true);
            }

        });

        mBtnNetworkPause.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mMediaPlayerNetwork.isPlaying()) {
                    mBtnNetworkPause.setEnabled(false);

                    mMediaPlayerNetwork.pause();
                }
            }
        });

        mBtnNetworkStop.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mMediaPlayerNetwork.stop();
                mBtnNetworkStart.setEnabled(false);
                mBtnNetworkPause.setEnabled(false);
                mBtnNetworkStop.setEnabled(false);
                mMediaPlayerNetwork.prepareAsync();

            }
        });
    }
    
如上就是使用MediaPlayer播放网络音频的方法
### 三、使用SoundPool播放音频

SoundPool一般用来 播放密集，急促而又短暂的音效，比如特技音效：Duang~，游戏用得较多，这里根据网上的教程，使用老式的SoundPool的构造方法是可以直接用的，老式的构造方法有三个参数，第一个为指定支持多少个声音，SoundPool对象中允许同时存在的最大流的数，第二个为指定声音类型，流类型可以分为STREAM_VOICE_CALL, STREAM_SYSTEM, STREAM_RING,STREAM_MUSIC 和 STREAM_ALARM四种类型，可以指定使用不同类型的系统音量去播放音效，比如通话音量和媒体音量，在AudioManager中定义，第三个为指定声音品质（采样率变换质量），一般直接设置为0。

使用SoundPool一般都会使用HashMap来进行音效和id之间的对应关系，同时在put进map的时候直接调用load方法进行加载：

		HashMap<Integer, Integer> musicId = new HashMap<Integer, Integer>();

        //通过load方法加载指定音频流，并将返回的音频ID放入musicId中

        musicId.put(1, sp.load(this, R.raw.first, 0));

        musicId.put(2, sp.load(this, R.raw.second, 0));

        musicId.put(3, sp.load(this, R.raw.third, 0));
        
        
        
然后在按钮的点击事件中调用play方法来播放音频，关于play方法，有几个不同的参数：

	play(int soundID, float leftVolume, float rightVolume, int priority, int loop, float rate)
	
soundID就是load返回的id内容，第二三个参数为左右声道的音量，范围为0.0-1.0，第四个参数指定播放声音的优先级，数值越高，优先级越大，loop指定是否循环，-1表示无限循环，0表示不循环，其他值表示要重复播放的次数，rate是指定播放速率：1.0的播放率可以使声音按照其原始频率，而2.0的播放速率，可以使声音按照其 原始频率的两倍播放。如果为0.5的播放率，则播放速率是原始频率的一半。播放速率的取值范围是0.5至2.0。

        
但是这个老式的构造方法在api21以后已经被废弃了，官方建议使用SoundPool.Builder这个静态内部类的构造方法：

*use SoundPool.Builder instead to create and configure a SoundPool instance*

我根据官方的文档进行使用时，刚开始无论如何都无法播放声音，换成老式的构造方法则可以播放，于是去看源码：

	public SoundPool build() {
            if (mAudioAttributes == null) {
                mAudioAttributes = new AudioAttributes.Builder()
                        .setUsage(AudioAttributes.USAGE_MEDIA).build();
            }
            return new SoundPool(mMaxStreams, mAudioAttributes);
        }
之前一直都是调用的这个方法来进行调用load方法和play方法，可以看到，这个方法每次调用的时候都会建立一个新的对象，所以肯定没有办法播放，这时再用SoundPool去声明一个新的对象等于前面那个对象调用build()方法：

		mSoundPool = new SoundPool.Builder();
			//设置最大播放数目
        mSoundPool.setMaxStreams(4);
        sp=mSoundPool.build();
        
 这里使用sp去调用load和play方法就可以正常地播放了。
 
 
## 3、Android的三个录音方法

### 一、调用系统录音功能直接录音：

使用Intent调用系统本身自带的录音功能可以实现简单的录音功能：

	//系统录音功能（开始）
        btnStartRecord.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(MediaStore.Audio.Media.RECORD_SOUND_ACTION);
                startActivityForResult(intent,1);
            }
        });
        
但是注意要在manifest中写好permission：

	<uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    
然后在回调方法中通过Cursor获取文件路径，然后使用MediaPlayer进行播放：

	/**
     * 回调录音完毕内容
     * @param requestCode
     * @param resultCode
     * @param data
     */
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {

        switch (requestCode) {
            case 1:
                try {
                    Uri uri = data.getData();
                    Log.d("test2", String.valueOf(uri));

                    String filePath = getAudioFilePathFromUri(uri);
                    filePathOfMusic=filePath;
                    mMediaPlayer.setDataSource(filePathOfMusic);
                    mMediaPlayer.prepare();
                    Log.d("test", filePath);
                } catch (Exception e) {
                    throw new RuntimeException(e);
                }
                break;
            default:
                break;
        }
    }


调用系统录音功能较为简单，下面看调用官方类库的方法：

### 二、调用MediaRecorder类的方法来录音：


总的来说，使用MediaRecorder流程如下:

1、创建MediaRecorder对象。

2、调用MediaRecorder对象的setAudioSource()方法设置声音来源，一般传入MediaRecorder.AudioSource.MIC参数指定录制来自麦克风的声音。

3、调用MediaRecorder对象的setOutputFormat()设置所录制的音频文件的格式。

4、调用MediaRecorder对象的setAudioEncoder()、setAudioEncodingBitRate(int  bitRate)、setAudioSamplingRate(int  samplingRate)设置所录制的声音的编码格式、编码位率、采样率等，这些参数将可以控制所录制的声音的品质、文件的大小。一般来说，声音品质越好，声音文件越大。

5、调用MediaRecorder的setOutputFile(String  path)方法设置录制的音频文件的保存位置。

6、调用MediaRecorder的prepare()方法准备录制。

7、调用MediaRecorder对象的start()方法开始录制。

8、录制完成，调用MediaRecorder对象的stop()方法停止录制，并调用release()方法释放资源。

由于要使用麦克风和存储空间来进行操作，自然要喜闻乐见的权限请求操作：

		if (ContextCompat.
		checkSelfPermission(AudioRecorderActivity.this,
		Manifest.permission.RECORD_AUDIO) 
		!= PackageManager.PERMISSION_GRANTED ||
	       ContextCompat.
	   	checkSelfPermission(AudioRecorderActivity.this, 
		Manifest.permission.WRITE_EXTERNAL_STORAGE) 
		!= PackageManager.PERMISSION_GRANTED) {
	
           ActivityCompat.requestPermissions(
           AudioRecorderActivity.this, 
           new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE,
           				Manifest.permission.RECORD_AUDIO}, 
           				1);

	      } 
	      

要申请WRITE_EXTERNAL_STORAGE和RECORD_AUDIO这两个权限，然后根据流程，代码如下：


					try {
                        mRecorderFile = new File(Environment.
		                        getExternalStorageDirectory().
		                        getCanonicalFile() + 
		                        "/projectTest/sound.amr");
                        
                        if (!mRecorderFile.exists()) {
                            mRecorderFile.createNewFile();
                        }


                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                    mMediaRecorder.setAudioSource(MediaRecorder.AudioSource.MIC);
                    mMediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.THREE_GPP);
                    mMediaRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.AMR_NB);
                    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                        mMediaRecorder.setOutputFile(mRecorderFile.getAbsoluteFile());
                    }
                    try {
                        mMediaRecorder.prepare();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                    mMediaRecorder.start();


首先使用File类创建文件，然后调用MediaRecorder的初始化的方法，设置录音的格式，文件路径等等，然后调用prepare方法，最后调用start()方法。
有时候录音想要暂停一下，需要调用pause()方法，然后重新开始录制的时候需要调用resume方法，停止录音的时候和前面一样调用stop方法就好。


### 三、使用AudioRecord录音
AudioRecord和MediaRecorder相比更加底层一些，MediaRecorder类对于录音的封装相对要完备一些，AudioRecord同时也就更加灵活一些，可以直接读取到音频的数据，对音频进行一些处理等等。
使用AudioRecord类来进行录音时，可以使用AudioTrack进行流数据读取和播放，也可以把AudioRecord的数据保存成文件，添加header，然后使用MediaPlayer播放。
关于使用AudioTrack进行AudioRecord的播放较为复杂，而且与实现实时语音聊天有关，因此这里不做展开，以后再做语音聊天相关总结时会详细说，这里只总结关于AudioRecord进行录音的操作。
AudioRecord的构造方法有5个参数，根据官方解释，这5个参数的含义是这样的：

![](http://p34is8180.bkt.clouddn.com/18-4-12/14387315.jpg)

关于具体的参数含义，在[android中AudioRecord使用](http://www.360doc.com/content/18/0411/20/54414862_744829364.shtml)这篇中有比较详细的讲解，这里不在赘述，直接上代码：

	bufferSize = AudioRecord.getMinBufferSize(
                44100,
                AudioFormat.CHANNEL_IN_STEREO,
                AudioFormat.ENCODING_PCM_16BIT);
        //参数说明：
        // 音频获取源MIC，
        // 设置音频采样率，44100是目前的标准，但是某些设备仍然支持22050，16000，11025
        // 设置音频的录制的声道CHANNEL_IN_STEREO为双声道，CHANNEL_CONFIGURATION_MONO为单声道，
        // 音频数据格式:PCM 16位每个样本。保证设备支持。PCM 8位每个样本。不一定能得到设备支持，
        // 缓冲区字节大小
        mAudioRecord = new AudioRecord(
                MediaRecorder.AudioSource.MIC,
                44100,
                AudioFormat.CHANNEL_IN_STEREO,
                AudioFormat.ENCODING_PCM_16BIT,
                bufferSize);
                
调用构造方法以后直接调用startRecording()方法就开始录音了，但是，同时要开一个线程用来存储或者直接播放录制的音频数据，
在线程中读取音频文件需要调用read等方法，官方对于方法的解释如下：

![](http://p34is8180.bkt.clouddn.com/18-4-12/70865425.jpg)

一般来说是用一个while循环来进行数据的循环读取写入文件中:

	/**
     * 把录制的音频裸数据写入到文件中去
     * 这里将数据写入文件，但是并不能播放，因为AudioRecord获得的音频是原始的裸音频，
     * 如果需要播放就必须加入一些格式或者编码的头信息。但是这样的好处就是你可以对音频的 裸数据进行处理，比如你要做一个爱说话的TOM
     * 猫在这里就进行音频的处理，然后重新封装 所以说这样得到的音频比较容易做一些音频的处理。
     */
    private void writeDataToFile() {
        // new一个byte数组用来存一些字节数据，大小为缓冲区大小
        byte[] audioData = new byte[bufferSizeInBytes];
        int readSize = 0;
        FileOutputStream fos = null;
        File file = new File(AudioName);
        if (file.exists()) {
            file.delete();
        }
        try {
            fos = new FileOutputStream(file);//获取一个文件的输出流
        } catch (FileNotFoundException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }

        while (isRecord) {
            readSize = audioRecord.read(audioData, 0, bufferSizeInBytes);
            Log.d(TAG, "readSize =" + readSize);
            if (AudioRecord.ERROR_INVALID_OPERATION != readSize) {
                try {
                    fos.write(audioData);
                } catch (IOException e) {
                    // TODO Auto-generated catch block
                    e.printStackTrace();
                }
            }
        }

        try {
            fos.close();
        } catch (IOException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }

想要停止录音调用stop()方法就可以了。

以上就是关于Android的系统声音控制，播放声音，录制音频文件的一点粗浅的总结。

参考博客：
> 
> [https://blog.csdn.net/quincyjiang/article/details/53406271](https://blog.csdn.net/quincyjiang/article/details/53406271)
> 
> [https://blog.csdn.net/ddna/article/details/5178864](https://blog.csdn.net/ddna/article/details/5178864)
> 
>[ http://www.jb51.net/article/105465.htm]( http://www.jb51.net/article/105465.htm)
> 
> [https://blog.csdn.net/qq_19067845/article/details/51355021](https://blog.csdn.net/qq_19067845/article/details/51355021)
> 
> [https://www.2cto.com/kf/201405/304411.html](https://www.2cto.com/kf/201405/304411.html)
> 
> [https://blog.csdn.net/foruok/article/details/52578930](https://blog.csdn.net/foruok/article/details/52578930)
> 
> [https://developer.android.com/reference/android/media/SoundPool.html#play(int,%20float,%20float,%20int,%20int,%20float)](https://developer.android.com/reference/android/media/SoundPool.html#play(int,%20float,%20float,%20int,%20int,%20float))
> 
> [http://www.runoob.com/w3cnote/android-tutorial-soundpool.html](http://www.runoob.com/w3cnote/android-tutorial-soundpool.html)
> 
> [https://blog.csdn.net/fengyuzhengfan/article/details/38502865](https://blog.csdn.net/fengyuzhengfan/article/details/38502865)
> 
> [https://segmentfault.com/q/1010000003065983](https://segmentfault.com/q/1010000003065983)
> 
> [https://blog.csdn.net/true100/article/details/54962908](https://blog.csdn.net/true100/article/details/54962908)
> 
> [https://www.cnblogs.com/jiww/p/5619849.html](https://www.cnblogs.com/jiww/p/5619849.html)
> 
> [http://www.360doc.com/content/16/0723/18/9008018_577846265.shtml](http://www.360doc.com/content/16/0723/18/9008018_577846265.shtml)
> 
> [http://www.360doc.com/document/16/1108/17/16853537_604943676.shtml](http://www.360doc.com/document/16/1108/17/16853537_604943676.shtml)
> 
> [https://developer.android.com/reference/android/media/AudioRecord.html#AudioRecord(int,%20int,%20int,%20int,%20int)](https://developer.android.com/reference/android/media/AudioRecord.html#AudioRecord(int,%20int,%20int,%20int,%20int))
> 
> [https://blog.csdn.net/hedaogelaoshu/article/details/46236867](https://blog.csdn.net/hedaogelaoshu/article/details/46236867)





















	
	



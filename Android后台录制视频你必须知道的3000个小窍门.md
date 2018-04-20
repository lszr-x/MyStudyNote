##Android后台录制视频你必须知道的3000个小窍门

>参考目录：
>
>[android开发，静音录制视频，在一般清晰度的前提下保证文件大小越小越好](https://www.cnblogs.com/feijian/p/4794821.html)
>
>[官方文档](https://developer.android.com/reference/android/media/MediaRecorder.html#setVideoEncodingBitRate(int))
>
>


###普通的录制视频的方法

如果开发者想要在APP中加入录制视频的功能，在Android中一般来说，首先可以调用系统的录制视频的功能，也就是使用Intent去调用系统的视频功能，网上代码很全，这里就不多说了。这里我主要说一下关于使用MediaRecorder来进行视频的录制。
刚开始的时候，年少无知的我先去网上搜寻关于使用MediaRecorder录制视频的方法，然后找到一篇博客就开始仿照着使用，然后就出现了无穷无尽的bug，具体来说有Activity崩掉，start failed，stop failed，还有start failed之后不能打开手机上的照相机和手电筒，录制的文件不能播放等等问题，花式出错。然后，根据官网教程去重新配置MediaRecorder和SurfaceView还有Camera，连上手机，运行，录像，停止，查看文件，播放，一气呵成，流畅丝滑，然后感觉自己真的应该先看官方教程。。。。
这里就以官方文档为主讲解使用MediaRecorder录制视频的方法：

+ MediaRecorder的生命周期：


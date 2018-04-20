---
title : Android 笔记第二版
---
#Android 笔记第二版

###1、 关于相机在拍摄或者预览中手动对焦

+ 方法：调用Camera的一个函数，然后实现一个接口：

	首先，我们需要使用surfaceView去预览Camera的页面，因此，当点击surfaceView的时候可以去设置对焦：
	
	然后在点击事件里面对于Camera进行判空，如果不为空，则调用autofocus方法，然后在参数中实现Camera.AutoFocusCallback接口，即可以实现点击对焦：
	
		//设置点击调焦
        surfaceView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if(mCamera!=null){

                    mCamera.autoFocus(new Camera.AutoFocusCallback() {
                        @Override
                        public void onAutoFocus(boolean success, Camera camera) {

                        }
                    });
                }
            }
        });

至于自动对焦，参考[Android相机实时自动对焦的完美实现](https://blog.csdn.net/huweigoodboy/article/details/51378751)，尚未实现
        
        
###2、Camera.getParameters()调用位置：
+ 位置：Camera的引用调用open()后，setPreviewDisplay()之前，否则会报Parameters为空的错
+ 猜测：setPreviewDisplay()之后开始进行预览，Camera的引用会发生一些变化，调用getParameters()会报

###3、拍摄视频流程
+ 总流程：
	+ 初始化Camera控件
	+ Camera控件绑定surfaceViewHolder，设置previewDisplay
	+ 初始化MediaRecorder（设置Camera等）
	+ 开始录制
	+ 停止录制
+ 细化流程：
	+ 1、初始化Camera控件：
		+ 调用open()方法
		+ Camera.Parameters在这里获取，同时设置对应参数
		+ surfaceHolder调用surfaceView的getHolder
		+ 调用Camera的setPreviewDisplay()方法，把Holder作为参数传入
		+ 调用startPreView()方法，开始预览
		+ 设置竖屏录制，调用setDisplayOrientation(90);
		+ 解锁相机，调用unlock方法
	+ 2、初始化MediaRecorder
		+ 设置Camera，setCamera()
		+ 重置Recorder，reset()
		+ 设置Audio和Video的Source
		+ 设置profile
		+ 设置输出文件路径
		+ 设置播放文件时的竖屏，setOrientationHint(90);
		+ 和Camera一起设置预览，setPreviewDisplay(surfaceView.getHolder().getSurface());
		+ 调用prepare和start的方法去开始录制
	+ <font color="#A52A2A">hhh能改颜色<font>
	+ 
---
title: Android笔记
---

#python3笔记总结
##2017-11-30
+ find函数
		
		参数（要查找字符串，查找起始位置（从0开始））
		调用方法：被查找字符串.find()
		返回值：查找到的第一个符合的字符串
		例子：

		
		解释：查找a字符串中所有的b字符串，并打印其起始位置
+ count函数

		参数：（要查询的字符串）
		调用方法：被查找字符串.find()
		返回值：要查找字符串在被查找字符串中出现的次数
		
		
	
##2017-12-02
+ 替换一个字符串中的字符方法
		
		使用string和list之间的转换，把string先转换成list，然后进行替换或者改变，然后
		把list再转换成string
		如下：
	![](图片/Python3-2017-12-02.png)
		
		使用如上方法，最后的join方法即为重新转化的方法
+ 生成一个全排列字典的方法

		import itertools
		v = itertools.product('ACGT', repeat=x)
		C = {}
		for i in v:
    		C[''.join(i)] = 0
    	如上，即导入itertools包后使用一个字典来储存,x为全排列的位数，前面是内容，key值
    	为全排列的内容，val为0
    	
+ count函数使用注意：

		count函数在使用时只能返回不重复的子串数量，不能返回有重复的子串数量，需要自己写
		函数实现，如下    	
	![](图片/Python3-2017-12-02_count函数.png)
	
		如图，返回值为str1在str2中的数目（有重复）
		例如：aa在aaa中数量为2（有重复），数量为1（无重复）
		
+ 字符串切片方法总结

		str[::-1}
		
##2017-12-09

+ range函数总结：

	使用方法
	range（a，b，c）
	参数解释：a和b使起始和终止的区间位置，[a,b)这样的区间，a不填默认是0，c是增量的大小，不填默认是1
	
+ list排序函数：

	使用方法：
	list.sort()		#永久排序
	sorted（list)		#返回排序结果，不改变序列的值

		
		
		
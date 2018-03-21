---
title: java基础知识笔记
---

#java基础知识

+ 类，对象
	
	实例化：使用关键字new来创建一个对象。
	
	+ 实例变量

	 关于在类中声明的非静态变量，统称实例变量，根据其的公有私有性分为对子类是否可见，一般有对应的get和set方法
	 
	+ 类变量：

	类中声明的静态变量，静态变量除了被声明为常量外很少使用。常量是指声明为public/private，final和static类型的变量。常量初始化后不可改变。静态变量储存在静态存储区。经常被声明为常量，很少单独使用static声明变量。静态变量在程序开始时创建，在程序结束时销毁。
	
	+ final变量：
		 + 不能对已经初始化的变量修改,且作为类成员变量的时候必须被构造函数初始化或者有初值
		 + 对于final修饰的引用变量，如一个对象，对象的指向不能再改变，比如等于重新new一个，但是其指向可以被改变，即此对象的实例变量可以被修改
		 + 匿名内部类中使用的外部局部变量为什么只能是final变量
		 + 参考：[http://www.cnblogs.com/dolphin0520/p/3736238.html](http://www.cnblogs.com/dolphin0520/p/3736238.html)
	
	
	+ final类：不可以被任何类继承且内部类会被被隐式地指定为final方法。
	+ final方法:不可以被子类重写的方法
	+ java方法是值传递

##内部类仍存疑（参考中）
+ 内部类：参考[http://www.cnblogs.com/dolphin0520/p/3811445.html](http://www.cnblogs.com/dolphin0520/p/3811445.html)
	+ 成员内部类可以无条件访问外部类的所有成员属性和成员方法（包括private成员和静态成员）。
	+ 当成员内部类拥有和外部类同名的成员变量或者方法时，会发生隐藏现象，即默认情况下访问的是成员内部类的成员。如果要访问外部类的同名成员，需要以下面的形式进行访问：

		外部类.this.成员变量
		
		外部类.this.成员方法
		
	+ 在外部类中如果要访问成员内部类的成员，必须先创建一个成员内部类的对象，再通过指向这个对象的引用来访问
	+ 成员内部类是依附外部类而存在的，也就是说，如果要创建成员内部类的对象，前提是必须存在一个外部类的对象。创建成员内部类对象的一般方式如下：

			Outter outter = new Outter();
        	Outter.Inner inner = outter.new Inner();
	


+ 数据类型：
	+ 基本数据类型：
		+ int float Boolean double shot byte long char
	+ 对于基本数据类型，可以直接用==判断是否为相等，每个变量的对比即为其内部的值的对比
	+ 对于非基本类型的数据，即引用类型的数据，变量储存的是其关联的对象在内存中的地址，如String，不能直接使用==去判断是否为相等的字符串，其String声明的变量为其对象的地址，而不是值。参考[http://www.cnblogs.com/dolphin0520/p/3592500.html](http://www.cnblogs.com/dolphin0520/p/3592500.html)


+ equals()方法：
	+ 为Object类中的方法:
			
			/**
		     * Compares this string to the specified object.  The result is {@code*
		     * true} if and only if the argument is not {@code null} and is a {@code
		     * String} object that represents the same sequence of characters as this
		     * object.
		     *
		     * @param  anObject
		     *         The object to compare this {@code String} against
		     *
		     * @return  {@code true} if the given object represents a {@code String}
		     *          equivalent to this string, {@code false} otherwise
		     *
		     * @see  #compareTo(String)
		     * @see  #equalsIgnoreCase(String)
		     */
		    public boolean equals(Object anObject) {
		        if (this == anObject) {
		            return true;
		        }
		        if (anObject instanceof String) {
		            String anotherString = (String)anObject;
		            int n = value.length;
		            if (n == anotherString.value.length) {
		                char v1[] = value;
		                char v2[] = anotherString.value;
		                int i = 0;
		                while (n-- != 0) {
		                    if (v1[i] != v2[i])
		                        return false;
		                    i++;
		                }
		                return true;
		            }
		        }
		        return false;
		    }
		    
		可以看到，对于在String类中对于equals方法进行了重写，代码逻辑简单，很容易看懂
		
+ instanceof用法：
	+ instanceof通过返回一个布尔值来指出，这个对象是否是这个特定类或者是它的子类的一个实例。 
	+ result = object instanceof class 		
+  对象拷贝：
	+  分为引用拷贝，浅拷贝，深拷贝
	+  引用拷贝：直接使用=来给引用对象赋值，是相当于建立了一个相同的的引用，指向同一个对象
	+  浅拷贝：
		
		
		
		
+ 关于float和double的问题：
	+ 单双精度的区别	
![](http://p34is8180.bkt.clouddn.com/18-3-21/44127005.jpg)		
	+ 关于float精度丢失问题，参考[Java学习_ 基本数据类型_float](http://blog.csdn.net/bonjean/article/details/51475363)

	+ 整数是可以被保存为有限的二进制整数的，但是小数不行，有可能出现无限循环的二进制数，所以关于有的小数是可以准确表示的，有的是一个接近的值来存储的。


	
	
	
		    
		    
		    
		    
		    
		    
		    
		    
		    
		    
		    
		    
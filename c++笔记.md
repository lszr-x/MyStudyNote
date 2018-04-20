---
title: C++笔记
---


#C++笔记
+ 位运算

或：| 有一个是1就是1
且：& 两个都是1才是1
异或：^ 相同为0，不同为1

+ 加法

关于实现使用c++的语法的加法的模拟：

使用递归求解：要实现三步，

思路：
考虑一个普通的加法计算：5＋17＝22


_在十进制加法中可以分为如下3步进行：
    1. 忽略进位，只做对应各位数字相加，得到12（个位上5＋7＝12，忽略进位，结果2）；
    2. 记录进位，上一步计算中只有个位数字相加有进位1，进位值为10；
    3. 按照第1步中的方法将进位值与第1步结果相加，得到最终结果22。_

_下面考虑二进制数的情况（5＝101，17＝10001）：
仍然分3步：
    1. 忽略进位，对应各位数字相加，得到10100；
    2. 记录进位，本例中只有最后一位相加时产生进位1，进位值为10（二进制）；
    3. 按照第1步中的方法将进位值与第1步结果相加，得到最终结果10110，正好是十进制数22的二进制表示。_

_接下来把上述二进制加法3步计算法用位运算替换：
    第1步（忽略进位）：0＋0＝0，0＋1＝1，1＋0＝1，1＋1＝0，典型的异或运算。
    第2步：很明显，只有1＋1会向前产生进位1，相对于这一数位的进位值为10，而10＝(1&1)<<1。
    第3步：将第1步和第2步得到的结果相加，其实又是在重复这2步，直到不再产生进位为止。_
    
    int add(int a,int b){  
    if(b == 0) return a;//没有进位时，完成运算，a为最终和。  
    int sum,carry;  
    sum = a ^ b;//没有进位的加法运算  
    carry = (a & b) << 1;//进位，左移运算。  
    return add(sum , carry);//递归，相加。  
	}  
	
参考：[http://blog.csdn.net/u010274071/article/details/14614423]()
[https://baike.baidu.com/item/加法/1018876?fr=aladdin
]()

+ 阶乘求0个数：

即求对应的n中所有的5的个数，但是需要注意的是，5的次方中有多个5，因此，对于一个数n，需要求出它中5的个数+25的个数+。。。最后即可得所有5的个数，相当于25是在5的时候算了一遍，然后再在25的时候再算一遍，因此，需要把n一直除5求和，最后就是个数。

参考：[http://blog.csdn.net/surp2011/article/details/51168272]()




#STL之vector排序及使用
+ vector的基本使用

	+ 创建：vector<int> vec;
		
	+ 尾部插入数字：vec.push_back(a);
	+ 使用下标访问元素，cout<<vec[0]<<endl;记住下标是从0开始的。
	+ 使用迭代器访问元素：

		vector<int>::iterator it;

		for(it=vec.begin();it!=vec.end();it++)

    	cout<<*it<<endl;
    	
	+ 插入元素：    vec.insert(vec.begin()+i,a);在第i+1个元素前面插入a;
	+ 删除元素：    vec.erase(vec.begin()+2);删除第3个元素，vec.erase(vec.begin()+i,vec.end()+j);删除区间[i,j-1];区间从0开始
	+ 向量大小:vec.size();
	+ 清空:vec.clear();
	+ 可以定义结构体（全局），具体参见[http://blog.csdn.net/duan19920101/article/details/50617190/]()
	+ 算法：
		+ 使用reverse将元素翻转：
		 			#include<algorithm>
		 			reverse(vec.begin(),vec.end());//将元素翻转，即逆序排列！
		+ 使用sort排序：
				#include<algorithm>
				sort(vec.begin(),vec.end());//(默认是按升序排列,即从小到大)
				可以通过重写排序比较函数按照降序比较，如下：
				定义排序比较函数：
				bool Comp(const int &a,const int &b)
				{
				    return a>b;
				}
				调用时:sort(vec.begin(),vec.end(),Comp)，这样就降序排序。 
				
	
+ 0与浮点数的比较：设置一个较小的比较对象，使用<=和>=去比较
	
	const float EPSINON = 0.000001;
	
	
+ set使用：
	+ 使用结构体插入时，需要去重写运算符，把结构体的大小比较明确：
	
			bool operator<(const test & x,const test & y)
			{
			    return x.c>y.c;
			}




###int与string
![](http://p34is8180.bkt.clouddn.com/18-4-14/38899704.jpg)

![](http://p34is8180.bkt.clouddn.com/18-4-14/32382841.jpg)


























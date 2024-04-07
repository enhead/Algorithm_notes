

https://ac.nowcoder.com/acm/problem/19913

## 重载输入输出

[C++重载>>和<<（重载输入和输出运算符） (biancheng.net)](https://c.biancheng.net/view/9bwk5ij.html#:~:text=输入运算符的重载与输出运算符类似，语法格式如下： istream%26 operator>> (istream%26 in%2C 自定义类型 %26 obj),{ %2F%2F 自定义输入格式 %2F%2F in>>... return in%3B })



##  注意：`priority_queue`默认是大根堆，设置`greater<类型>`后才是小根堆



## 即使是bool数组一定一定也要赋初值，不然也是随机

`bool used[N]={0};`



## 比较一个数组的最大值:`max({a,b,c,d})`（这个是c++11的才有的）



## `string`方法：

重点看的部分，字符串查找，和截取

有结果展示：[C++中的String的常用函数用法总结_string函数-CSDN博客](https://blog.csdn.net/qq_37941471/article/details/82107077)

很详细：[C/C++ string字符串操作的全面总结 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/487259666)和

​				[C++ string的用法和例子_string 用法-CSDN博客](https://blog.csdn.net/tengfei461807914/article/details/52203202)

(到时候在看看笔记本)

```c++
//s.substr(pos1,n)返回字符串位置为pos1后面的n个字符组成的串
//s.substr(pos)//得到一个pos到结尾的串
```

> 第一个函数跟c++中是不一样的

## 不断查找字符串后替换

（**备注：两个字符串不一定相同**）

```c++
`	string a="12",b="ab";
	string t="123451234512345";
	int k=t.find(a);
	while(k!=-1) {
		string x=t.substr(0,k)+b+t.substr(k+a.size());//将字串为a的变换成b
        							//注意:这里时a的长度
  			   //第一个不太一样
		k=t.find(a,k+1);//一定要从下个位置开始找
		
        代码在这写
        
	}
	/**结果：
	ab3451234512345
	12345ab34512345
	1234512345ab345
	*/
```

## 起别名

`define 这个 实际表示那个`   

> （无分号）
>
> 定义这个表示那个

`typedef long long ll;`





## C++ STL标准库：序列容器 双向链表list（这个选看）

[C++ STL标准库：序列容器 双向链表list_c++标准库链表-CSDN博客](https://blog.csdn.net/u014779536/article/details/116376829)

```c++
可以简单记记
insert,push_back,push_front,pop_back/front,back,front
```







## STL:按值初始化`fill`函数

[c++ fill()函数使用_c++ fill如何赋值多维数组-CSDN博客](https://blog.csdn.net/qq_32095699/article/details/79303922)

```c++
	int N=9;
	int a[9];
	int aa[3][3];
	//一维数组：
	fill(a,a+N,666);
	for(int i=0; i<N; ++i) cout<<a[i]<<" ";
	cout<<endl;
	//多维数组
	fill(aa[0],aa[0]+N,666);
	for(int i=0; i<3; ++i) {
		for(int j=0; j<3; ++j) {
			cout<<aa[i][j]<<" ";
		}
		cout<<endl;
	}
```

> fill(首指针，尾指针，初始化的值);

## [《语法基础课》笔记 - AcWing](https://www.acwing.com/blog/content/4017/)

## [字符串](https://blog.csdn.net/Flag_ing/article/details/123361432)

(上面的链接，写得挺全的)

- `s.substr(int r)`:截取从下标r到结尾的子串

  `s.substr(int pos,int len)`:截取从下标`pos`开始，长度为len的子串

- `s.replace(int pos,int len,字符串a)`:将下标为`pos`开始算`len`个，替换成这个字符串a

- to_string(其他类型):将其他类型转换为字符串

- `s.insert(int pos,字符串)`

- **`s.find(字符串,pos)`**





## 逗号表达式是看最后一个值

## [C++中stoi()，atoi() ，to_string()使用技巧-CSDN博客](https://blog.csdn.net/YXXXYX/article/details/119955817)(==还没时间看==)


# 回文字符串

[139. 回文子串的最大长度 - AcWing题库](https://www.acwing.com/problem/content/description/141/)

如果一个字符串正着读和倒着读是一样的，则称它是回文的。

给定一个长度为 N 的字符串 S，求他的最长回文子串的长度是多少。

## manacher



> 这个老哥的题解相当好，这里为了省事，直接照搬下来了：
>
> ​	[Manacher 算法详解 - AcWing](https://www.acwing.com/file_system/file/content/whole/index/content/446985/) 和 [Manacher 算法详解 | Yuhi's Blog](https://yuhi.xyz/post/Manacher-算法详解/)
>
> （这里只是稍稍改进下写法而已，配和代码好好看看）
>
> 这里代码参照的是《算法竞赛》，但是它这里对算法的描述和图有点问题，会带来一些困惑



#### 字符串预处理

为了解决奇数长度的回文串，与偶数长度的回文串的不统一的问题，我们对给定字符串`S`预处理，即间隔插入分隔符`#`，**同时加入起始和结束符号，防止边界问题，自动结束**，如下所示：

    原串 abaa -> ^#a#b#a#a#$
    		    11213123211	
    最长回文子串 aba -> #a#b#a#
    
    原串 abaabc -> ^#a#b#a#a#b#c#$
    最长回文子串 baab -> #b#a#a#b#

那么所有回文子串都统一成了 _奇数长度_ 。

#### 引入 p 数组, rt, mid

预处理后的新串表示为`Str`，定义数组`p[]`，`p[i]`表示 `Str`中以下标`i`为回文中心的最大回文半径。以`abab`为例：

| i    | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    | 9    |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| Str  | #    | a    | #    | b    | #    | a    | #    | b    | #    |
| p[i] | 1    | 2    | 1    | 4    | 1    | 4    | 1    | 2    | 1    |

如果我们得到了 `p[i]`，那么 `p[i] - 1` 就是原串 $ m S$ 以 `i` 为回文中心的最大回文 _长度_ （可根据上表验证一下）。

现在我们来求解 `p[i]`，定义 `rt` 表示已经计算过的回文串能达到的最远右边界的 _下一个位置_[1]。即 $ rt =
\max(j+p[j]),j\in[1,i-1]$，`mid` 表示 `rt` 所对应的最左侧的回文中心，有 `mid + p[mid] == rt`
。如下图所示：

![](https://yuhi.xyz/images/manacher_1.png)

>  ◎ mid 与 rt 的定义

#### 分类讨论最小长度

如何计算 `p[i]` 呢，显然有 `i > mid`（因为 `p[mid]` 已经计算过·）下面再分两种情况讨论：  
 **情况一** ：`i < rt`

![](https://yuhi.xyz/images/manacher_2.png)

>  ◎ i < rt 时计算 p[i]

此时我们发现，我们可以用已知的 `p[j]` 的信息来辅助计算 `p[i]`。`p[j]` 又有两种情况，讨论如下：

>   1. 以 `j` 为中心的最大回文串 _被包含_ 在以 `mid` 为中心的最大回文串中
>
>      ![](https://yuhi.xyz/images/manacher_3.png)
>
> > ◎ 以 j为中心的最大回文串被包含在以 mid 为中心的最大回文串中
>
>   2. 以 `j` 为中心的最大回文串 _没有被包含_ 在以 `mid` 为中心的最大回文串中
>
>      ![](https://yuhi.xyz/images/manacher_4.png)
>
> >  ◎ 以 j为中心的最大回文串没有被包含在以 mid 为中心的最大回文串中

**情况二** ：`i >= rt` 此时，没有已知信息可以辅助计算了，令 `p[i] = 1` 然后暴力扩展。

![](https://yuhi.xyz/images/manacher_5.png)

>  ◎ i >= rt 时计算 p[i]

 **最后暴力（中心拓展法）求剩下的**



#### 时间复杂度分析

下次拓展发生在`rt`后面，因此时间复杂度$O(n)$



```java
//manacher算法
/* 本质上： 是动态规划的思想，尽量不重复计算
 * 		    利用的是回文串的镜像还是回文串
 * 重要技巧：需要分成多种情况，经过变换，将这些变换化为同一种
 * 			统一处理（使用了多次）
 * 
 * 
 * 		预处理变换：	本来回文串是分为两种：长度为奇数或者偶数
 * 					偶数的空隙为奇数，奇数的空隙为偶数
 * 					偶数+奇数=奇数
 * 					通过将缝隙用特殊全部补上，就都变为了奇数字符串
 * 					然后为了防止边界情况，左右边界加上特殊字符，左边界在加个开始字符，右边界加结束字符
 * 经过这样变换后个数，以s[i]为中心的回文串长度为p[i]-1
 * 
 * 具体过程（看题解，这里大概提一下）：【不断分类讨论】：
 * 		p数组用来存以s[i]为中心字符的最长回文串长度
 * 		当前最大的右端点rt（开区间），对应的中心点为c
 * 			rt=c+p[c]，回文串不包含这个
 * 		当前算p[i],p[1~i-1]已经算完了
 * 		情况1：能镜像：i<rt
 * 			i关于c的镜像是j=2*c-i【(i+j)/2=c】
 * 			① 以s[j]为中心的回文串被这个最大右端点的回文串包含:
 * 				那么以s[i]为中心的回文串最小为p[i]
 * 			② 以s[j]为中心的回文串被这个最大右端点的回文串不能包含:
 * 				那么最多只能确定到以c为中心回文串的右端点
 * 				长度为rt-i
 * 		情况2：不能镜像：i>=rt
 * 			那么p[i]至少为1
 * 		剩下的用暴力法（中心拓展法）确定就行
 * 		
 * 	时间复杂度为：O(n)【不会重复计算已经走过的右端点】			
 * */
import java.util.*;
import java.io.*;
public class Main{
	static final int N=(int)1e6+10;
	//这些参数配合上面的分析看
	static int len;//预处理后的长度
	static char[] s=new char[N<<1],tmp;
	static int[] p=new int[N<<1];
	static int c,rt;
	//预处理，将回文串都变为奇数
	static void init() {
		int k=1;//从1开始存储
		//构造左边界
		s[k++]='^';//开始字符
		s[k++]='#';//特殊字符加入缝隙中
		for(int i=0;i<tmp.length;++i) {
			s[k++]=tmp[i];
			s[k++]='#';
		}
		s[k++]='$';//结束字符
		len=k;
	}
	static void manacher() {
		for(int i=1;i<len;++i) {
			if(i<rt) 
				p[i]=Math.min(p[(c<<1)-i],rt-i);
			else
				p[i]=1;
			//再用暴力（中心拓展法，去算）
			while(s[i-p[i]]==s[i+p[i]])
				++p[i];
			if(i+p[i]>rt) {
				rt=i+p[i];
				c=i;
			}
		}
	}
	public static void main(String[] args) throws Exception{
		BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
		int T=1;
		while(true) {
			String ss=br.readLine();
			if("END".equals(ss)) break;
			tmp=ss.toCharArray();
			init();
			manacher();
			int res=0;
			for(int i=0;i<len;++i) 
				res=Math.max(res,p[i]-1);
			System.out.printf("Case %d: %d\n",T++,res);
		}
	}
}
```

## 二分+(前后缀)字符串哈希

> 时间复杂度没有比上一个好，这个本质上就是优化下暴力，这里引入上面那些参数
>
> 不过这种解法挺适合用来练习的

> 这里好好看看如何倒着处理哈希



```java
//暴力优化:二分+（前后缀）字符串哈希
	//引入下manacher那些东西
	//有明显的二段性，可以用二分找
import java.util.*;
import java.io.*;
public class Main{
	static final int N=(int)1e6+10;
	//引入manacher那些概念浅浅优化一下
	static int len;//预处理后的长度
	static char[] s=new char[N<<1],tmp;//预处理成s
	static int[] f=new int[N<<1];//以s[i]为中心的回文串半径
	
	//字符串哈希
		//注意下：java没有无符号的整型
	static final int P=131;//模数让他们自然溢出
	static long[] p=new long[N<<1];//预处理进制
	static long[] hl=new long[N<<1];//正向
	static long[] hr=new long[N<<1];//逆向，注意：最后一个字符表示下标1
			//注意：倒着存，hr[j]实际表示的是s[n~j]的哈希
	/**
	 * 					a b c d e f
	 * hl对应的下标：	1 2 3 4 5 6
	 * hr对应的下标：	6 5 4 3 2 1
	 * hr你反着来的,长度len=7,j对应len-i
	 */
	//预处理，将回文串都变为奇数+前后缀字符串哈希
	static void init() {
		//预处理s
		int k=1;//从1开始，因为后面会涉及前缀和
		s[k++]='^';
		s[k++]='#';
		for(int i=0;i<tmp.length;++i) {
			s[k++]=tmp[i];
			s[k++]='#';
		}
		s[k++]='$';
		len=k;
		
		//前后缀字符串哈希
		p[0]=1;//初始话
		for(int i=1,j=len-1;i<len;++i,--j) {
			hl[i]=hl[i-1]*P+s[i];
			hr[i]=hr[i-1]*P+s[j];//注意是倒着来的
			p[i]=p[i-1]*P;
		}
	}
	static long get(long[] h,int l,int r) {
		return h[r]-h[l-1]*p[r-l+1];//注意要配齐
	}
	static boolean check(int i,int l) {
		return get(hl,i-l,i-1)==get(hr,len-(i+l),len-(i+1));//这里没有啥好办法
			//最好就是自己举例子
	}
	
	public static void main(String[] args) throws Exception{
		BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
		int T=1;
		while(true) {
			String ss=br.readLine();
			if("END".equals(ss)) break;
			
			tmp=ss.toCharArray();
			init();
			for(int i=1;i<len;++i) {
				//二分找长度
					//找到长度的最大值
					//向右模板
				int l=0,r=Math.min(i-1,len-i-1);
				while(l<r) {
					int mid=l+r+1 >>1;
					if(check(i,mid)) 
						l=mid;
					else 
						r=mid-1;
				}
				f[i]=l+1;
			}
			
			int res=0;
			for(int i=0;i<len;++i) 
				res=Math.max(res,f[i]-1);
			System.out.printf("Case %d: %d\n",T++,res);
		}
		
	}
}
```

> **注意这里倒着的字符串哈希是逆向的**，并且注意这里的长度
>
> 有一堆边界问题
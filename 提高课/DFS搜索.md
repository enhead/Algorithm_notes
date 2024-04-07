# `DFS`搜索

(下面这些好好看看)

![dfs概述.jpg](https://cdn.acwing.com/media/article/image/2024/02/22/246003_814a89f8d1-dfs概述.jpg)

**有多组数据每次都不要忘记初始化**，尤其是那些标记数组



==需不需要恢复现场==

## `DFS`之连通性模型(内部搜索)

### [1112. 迷宫](https://www.acwing.com/problem/content/description/1114/)



迷宫可以看成是由 $n * n$的格点组成，每个格点只有2种状态，`.`和`#`，前者表示可以通行后者表示不能通行。

只能移动到东南西北(或者说上下左右)四个方向之一的相邻格点上，想要从点A走到点B，问在不走出迷宫的情况下能不能办到。

如果起点或者终点有一个不能通行(为#)，则看成无法办到。

**注意** ：A、B不一定是两个不同的点。

**数据范围:**$1 \le n \le 100$

----


```java
//dfs实现
/**
 * 思路：本题就是判断下是否联通
 * 		bfs代码会长一点，还需要自己实现一个队列
 * 		dfs的特判起点和终点都可以放在起点
 */
import java.util.*;
public class Main{
	static final int N=110;
	static int n;
	static int xa,ya,xb,yb;
	static char[][] g=new char[N][N];
	
	static boolean[][] st=new boolean[N][N];//特判是否搜过
	static int[] dx= {-1,0,1,0};
	static int[] dy= {0,1,0,-1};
	static boolean dfs(int x,int y) {
		if(g[x][y]=='#') return false;
		if(x==xb&&y==yb) return true;
		st[x][y]=true;
		for(int i=0;i<4;++i) {
			int a=x+dx[i],b=y+dy[i];
			if(a<0||a>=n||b<0||b>=n) continue;
			if(st[a][b]) continue;
			
			if(dfs(a,b)) return true;
		}
		return false;//都走不到终点
		
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		int k=sc.nextInt();
		while(k-- >0) {
			//初始化
			n=sc.nextInt();
			for(int i=0;i<n;++i) {
				char[] cs=sc.next().toCharArray();
				for(int j=0;j<n;++j) {
					g[i][j]=cs[j];
					st[i][j]=false;//别忘记了
				}
			}
			xa=sc.nextInt();
			ya=sc.nextInt();
			xb=sc.nextInt();
			yb=sc.nextInt();
//			Arrays.fill(st,false);//别忘了//注意不能针对二维数组
			if(dfs(xa,ya)) 
				System.out.println("YES");
			else 
				System.out.println("NO");	
		}		
	}
}
```



### `dfs`版洪水填充

[1113. 红与黑 - AcWing题库](https://www.acwing.com/problem/content/1115/)

有一间长方形的房子，地上铺了红色、黑色两种颜色的正方形瓷砖。
你站在其中一块黑色的瓷砖上，只能向相邻（上下左右四个方向）的黑色瓷砖移动。
请写一个程序，计算你总共能够到达多少块黑色的瓷砖。(先输入列数)
**数据范围**：1≤行和列≤20

---

```java
//dfs实现flood fill
import java.util.*;
public class Main{
	static final int N=30;
	static int n,m;
	static char[][] g=new char[N][N];
	
	static int sx,sy;
	static boolean[][] st=new boolean[N][N];//判断是否填充过
	static int[] dx= {-1,0,1,0};
	static int[] dy= {0,1,0,-1};
	static int dfs(int x,int y) {
		//题目说了第一个起点是黑的，现在就在拓展时判断就行
		st[x][y]=true;
		int ans=1;
		for(int i=0;i<4;++i) {
			int a=x+dx[i],b=y+dy[i];
			if(a<0||a>=n||b<0||b>=m) continue;
			if(st[a][b]||g[a][b]=='#') continue;
			ans+=dfs(a,b);
		}
		return ans;
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		//多组数据别忘记所有的初始化,尤其是st数组
		while(true) {
			//这题比较坑，先输入列的
			m=sc.nextInt();
			n=sc.nextInt();
			if(n==0&&m==0) break;
			for(int i=0;i<n;++i) {
				char[] cs=sc.next().toCharArray();
				for(int j=0;j<m;++j) {
					g[i][j]=cs[j];
					st[i][j]=false;//别忘了
					if(g[i][j]=='@') {
						sx=i;
						sy=j;
					}
				}
			}
			System.out.println(dfs(sx,sy));
		}
	}
}
```



## DFS之搜索顺序(外部枚举)

 

### 二维遍历（选当前个）

(这个是外部遍历，把整体当成一个状态，会记录下**当前顺序每个点的影响**每个点的影响)

**注意看这个模板的标记和回溯的位置，是标记当前个**：(跟之前写的模板都不太一样)

> 将标记和恢复当前个，当写在了拓展外面

- 能避免一些边界问题，不用特殊处理起点
-  不用初始化

注意事项：

- **`cnt`初始起点为1，要从1开始**【`cnt`表示选了几个点，**包括当前点**】



[1116. 马走日 - AcWing题库](https://www.acwing.com/problem/content/description/1118/)

马在中国象棋以日字形规则移动。
请编写一段程序，给定 $n*m$ 大小的棋盘，以及马的初始位置 $(x，y)$，要求不能重复经过棋盘上的同一个点，计算马可以有多少途径遍历棋盘上的所有点。

数据范围:$1 \le T \le 9$,$1 \le m,n \le 9$,$1 \le n \times m \le 28$,$0 \le x \le n-1$,$0 \le y \le m-1$

----

```java
//dfs遍历
	//注意看这个模板，只看当前位置选与不选，能避免边界问题
import java.util.*;
public class Main{
	static final int N=15;
	static int n,m;
	static int sx,sy;//开始位置
	static boolean[][] st=new boolean[N][N];
	static int ans;
	static int[] dx= {-1,-2,-2,-1,1,2,2,1};
	static int[] dy= {-2,-1,1,2,2,1,-1,-2};
	//二维版有多种拓展的模板
	static void dfs(int x,int y,int cnt) {
		if(cnt==n*m) {
			++ans;
			return;
		}
		st[x][y]=true;//标记
		for(int i=0;i<8;++i) {
			int a=x+dx[i],b=y+dy[i];
			if(a<0||a>=n||b<0||b>=m) continue;
			if(st[a][b]) continue;
			dfs(a,b,cnt+1);
		}
		st[x][y]=false;//恢复
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		int T=sc.nextInt();
		while(T-- >0) {
			n=sc.nextInt();
			m=sc.nextInt();
			sx=sc.nextInt();
			sy=sc.nextInt();
			ans=0;
			dfs(sx,sy,1);
			System.out.println(ans);
		}
		
	}
}
```



### 枚举拼接字符串

[1117. 单词接龙 - AcWing题库](https://www.acwing.com/problem/content/1119/)

现在我们已知一组单词，且给定一个开头的字母，要求出**以这个字母开头**的最长的“龙”，每个单词最多被使**用两次**。
在两个单词相连时，其重合部分合为一部分，例如`beast`和`astonish`，如果接成一条龙则变为`beastonish`。
我们可以任意选择重合部分的长度，但其长度必须大于等于1，且严格小于两个串的长度，例如 `at` 和 `atide` 间不能相连。

**输入格式**：输入的第一行为一个单独的整数 $n$（$n<=20$） 表示单词数，以下 $n$ 行每行有一个单词（只含有大写或小写字母，长度不超过20），输入的最后一行为一个单个字符，表示“龙”开头的字母。

----
也是外部遍历，每次**按这个遍历顺序**的影响会记录下来

```java
//dfs枚举
	//这里也是只改变时只作用在当前遍历的变量，避免在dfs外还需要特殊处理
import java.util.*;
public class Main{
	static final int N=30;
	static int n;
	static String[] word=new String[N];//对象类型小心了
	static int[][] g=new int[N][N];//表示i位置子符串与j位置子符串拼接重合的最小长度
							//这样连接才能最长
	static int[] cnt=new int[N];//表示每个字符用了几个
	static int ans;
	/**
	 * 注意：在当前枚举才选上当前
	 * @param dragon 当前拼接成的字符串
	 * @param last	 上一个拼接的单词
	 */
	static void dfs(String dragon,int last) {
		ans=Math.max(dragon.length(),ans);
		//在拓展外部标记和恢复
		++cnt[last];
		for(int i=0;i<n;++i) {
			if(g[last][i]!=0&&cnt[i]<2) {
				dfs(dragon+word[i].substring(g[last][i]) ,i); 
			}
		}
		--cnt[last];
		//没找到会自动结束
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		n=sc.nextInt();
		for(int i=0;i<n;++i) 
			word[i]=sc.next();
		char start=sc.next().charAt(0);//别忘记还有一个开头
		
		//预处理下：每个字符串之间的连接
		for(int i=0;i<n;++i) 
			for(int j=0;j<n;++j) {//包括自身，会用到两次以上
				//使拼接a和b最长
				String a=word[i],b=word[j];
				//重合部分的长度
				for(int len=1;len<Math.min(a.length(),b.length());++len) 
								//小细节：题目说了严格小于两字符串的长度
					if(a.substring(a.length()-len).equals(b.substring(0,len))) {
						g[i][j]=len;
						break;//第一次找到的就是最好的结果
					}
			}
		//找开头字符，才能遍历
		for(int i=0;i<n;++i) 
			if(start==word[i].charAt(0)) 
				dfs(word[i],i);//选当前个，这里就不需特殊处理起点
		System.out.println(ans);	
	}
}
```





### 分组枚举（重要，但还不熟）

[1118. 分成互质组 - AcWing题库](https://www.acwing.com/problem/content/1120/)

给定 $n$ 个正整数，将它们分组，使得每组中任意两个数互质。至少要分成多少个组？

----

这里有组同样的数据数据：`3 6 10 5`
用这种贪心分组法的话，会分成

第一组：`3，10`
第二组：`6，5`

但是这里换下顺序：`3 6 5 10`
会分成

第一组：`3,5`
第二组：`6`
第三组：`10`
答案是错的，而且显然贪心不能因为顺序改变就改变答案



#### 解1:枚举每一组能放那些元素（需要特别多剪枝时）

(下面那个画黄色的那题可以好好看看)

> 对每一组的要求特别严格时，每组的和都要是一个数

这里写的就是按y总的分组枚举思路：

> 对于每个数：①要么放最后一组②新开一组（不放其他组）
>
> ​			(每次都要从头到尾遍历一遍)
>
> 贪心优化：
>
> > 当一个数符合①时，不在进行②操作
> >
> > 解释：
> >
> > >  如果能放到当前最后一组i，但是选择新开一组i+1，此时不能在第i组放任何东西了，显然放在最后一组从分组的情况还是对后续的影响都会比新开好很多	
> >
> > 拓展下，就是最后一组还能再放下数，就在此基础下新开一组了

缺点：会有特别多的冗余情况，但是数据量很小也是小问题，后面会讲如何剪枝

(注意看看下面各种数组的含义，理解如何不漏的地去缩)

```java
//dfs分组遍历
import java.util.*;
public class Main{
	static final int N=10;
	static int n;
	static int[] arr=new int[N];
	
	static int[][] group=new int[N][N];//各个分组，c++用vector更简单,java太麻烦了
	static boolean[] st=new boolean[N];//是否被选过
			//原本想用set集合，重复是不会影响答案，但是数据量小差不了多少
	static int ans;//先初始化为最大值
	static int gcd(int x,int y) {//最大公约数
		return y==0?x:gcd(y,x%y);
	}
	/**
	 * 判断当前组能否放下第k个数
	 * @param g:当前组
	 * @param gc:当前组的个数
	 * @param k:第k个数
	 * @return
	 */
	static boolean check(int g,int gc,int k) {
		for(int i=0;i<gc;++i) {
			if(gcd(group[g][i],arr[k])!=1)
				return false;
		}
		return true;
	}
	/**
	 * 分组枚举
	 * @param g:当前组
	 * @param gc:当前分组有几个数
	 * @param cnt:已分组的个数
	 * @param start："当前组"从哪个数开始遍历（注意）
	 * 				注意下：这个数前面的情况
	 * 					1.已经在当前组2.选不了3.之前选过，现在不选
	 * 这些很多其实是可以放在全局变量里面，但是也没啥差
	 * @return
	 * 重点是尽量不重但是一定不漏的去搜
	 * 注意：如果是参数的话，所有的变化直接体现在参数上就行
	 */
	static void dfs(int g,int gc,int cnt,int start) {
		if(g>=ans) return;//优化：剪枝+结束条件(重要)
			//比当前最优解组数更多，不可能为最优解
		if(cnt==n){//（要配合上一句的）已经搜完所有点，这个解就是当前最优解
			 ans=g;
			 return;
		}
		boolean flag=true;//小小贪心优化：如果还有数能放进当前组，那么在此基础上没必要新建一组
		//当前组上加
		for(int i=start;i<n;++i) {
			if(!st[i]&&check(g,gc,i)) {
				st[i]=true;
				group[g][gc]=arr[i];
				dfs(g,gc+1,cnt+1,i+1);
				//恢复现场
				st[i]=false;
				flag=false;//还能选数
				if(gc==0) return;//这里是超级剪枝
					//没有这句也能过，但是能优化特别多
					//去重，当前组不在回头选，变成组合，避免一些顺序的干扰
			}
		}
		//新开一组
		if(flag) dfs(g+1,0,cnt,0);
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		n=sc.nextInt();
		ans=n;
		for(int i=0;i<n;++i) 
			arr[i]=sc.nextInt();
		dfs(1,0,0,0);
		System.out.println(ans);
	}
}
```

##### 剪枝优化:

1. **如果没有之前的情况好，直接剪掉**（==很常用==）

   ![剪枝优化1.png](https://cdn.acwing.com/media/article/image/2024/02/22/246003_68a4317ed1-剪枝优化1.png) 

2. **去掉一些同样的情况**:

   ![剪枝优化2.png](https://cdn.acwing.com/media/article/image/2024/02/22/246003_b4f5048bd1-剪枝优化2.png) 

   > 这题会存在一些同样的情况，就是一些顺序问题:
   >
   > 以样例`3,6,10,5`为例：
   >
   > ![重复的情况.jpg](https://cdn.acwing.com/media/article/image/2024/02/22/246003_7c97f948d1-重复的情况.jpg) 

#### 解2:枚举元素放哪一组(一般用这个)

（这个解法会更直接点，更符合组合，一般情况都更优）

```java
//dfs分组遍历
	//看每个元素需要分到那一组
import java.util.*;
public class Main{
	static final int N=15;//注意开多一点
	static int n;
	static int[] arr=new int[N];
	
	static int len=1;//表示当前有几组
	static int[][] group=new int[N][N];//各个分组，c++用vector更简单,java太麻烦了
	static int[] gc=new int[N];//每个分组各个的个数
	static int ans;//先初始化为最大值
	static int gcd(int x,int y) {//最大公约数
		return y==0?x:gcd(y,x%y);
	}
	/**
	 * 判断当前组能否放下第k个数
	 * @param g:当前组java
	 * @param k:第k个数
	 * @return
	 */
	static boolean check(int g,int k) {
		for(int i=0;i<gc[g];++i) {
			if(gcd(group[g][i],arr[k])!=1)
				return false;
		}
		return true;
	}
	/**
	 * 分组遍历:
	 * 		当前元素要选到哪一组
	 * @param x：当前遍历的元素编号
	 */
	static void dfs(int x) {
		if(len>=ans) return;//剪枝优化
		if(x==n) ans=len;//跟上面的解法一个道理
		//看已经存在的组
		for(int i=0;i<len;++i) {//枚举所有组
			if(check(i,x)) {
				group[i][gc[i]++]=arr[x];
				dfs(x+1);
				--gc[i];//假删就行
			}
		}
		//新开一组
		group[len][gc[len++]++]=arr[x];//注意：运算顺序
		dfs(x+1);
		--gc[--len];//运算顺序
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		n=sc.nextInt();
		ans=n;
		for(int i=0;i<n;++i) 
			arr[i]=sc.nextInt();
		dfs(0);
		System.out.println(ans);
	}
}
```

注意下运算顺序



参考：

[y总解法评论里的剪枝优化](https://www.acwing.com/activity/content/code/content/134221/)
[AcWing 1118. 分成互质组(y总代码版的保姆级注释) - AcWing](https://www.acwing.com/solution/content/56339/)
[两种解法](https://www.acwing.com/solution/content/10364/)





## DFS之剪枝与优化

1. 优化搜索顺序

   **分支数量少的先搜**，这样能少很多分支（**只要针对题目的主要限制条件**）

   > 比如：分组搜索中，先放入很重的，这时只能新开一组，如果先放的是轻的，分支会多很多

   一般就是先搜大的

2. 排除等效冗余

   把那些重复搜的情况给去掉

   > 例如：分组枚举时不同顺序导致了重复搜

3. 可行性剪枝：

   如果已经发现不合法，直接剪掉

4. 最优性剪枝：

   特别是那些求最优值的，**跟现在的最优值对比**，如果没比人家好，直接剪

5. 记忆化搜索（DP）

注意：**如果需要找到答案就立刻返回，又有多组数据的，一定要注意初始化**

### 分组枚举的剪枝优化

(这里多加了个优化搜索顺序)

[165. 小猫爬山 - AcWing题库](https://www.acwing.com/problem/content/167/)

 $N$ 只小猫要去下山，但是不想徒步只，好花钱让它们坐索道下山。
索道上的缆车最大承重量为 $W$，而 $N$ 只小猫的重量分别是 $C_1、C_2……C_N$。
当然，每辆缆车上的小猫的重量之和不能超过 $W$。
每租用一辆缆车，翰翰和达达就要付 $1$ 美元，所以他们想知道，最少需要付多少美元才能把这 $N$ 只小猫都运送下山？
**数据范围：**$1 \le N \le 18$,$1 \le C_i \le W \le 10^8$

---

```java
//分组dfs
	//具体看上面那一题
import java.util.*;
public class Main{
	static final int N=25;
	static int n,w;
	static Integer[] c=new Integer[N];//涉及到排序，还是等下面在初始化
			//不然不好逆序排序
	
	static int ans=N;//记录下答案，同时又来剪枝
	static int len=1;//当前有几组
	static int[] sum=new int[N];//每组对应质量
	static void dfs(int x) {//自带排除等效冗余剪枝
		if(len>=ans) return;//最优性剪枝
		if(x==n) {//需要跟上面的配合用
			ans=len;
			return;
		}
		//枚举之前的每一组
		for(int i=0;i<len;++i) {
			if(w-sum[i]>=c[x]) {//当前组能装下(其实也是可行性剪枝)
				sum[i]+=c[x];
				dfs(x+1);
				//恢复
				sum[i]-=c[x];
			}
		}
		//新开始一组
		sum[len++]=c[x];
		dfs(x+1);
		sum[--len]=c[x];//注意运算顺序
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		n=sc.nextInt();
		w=sc.nextInt();
		for(int i=0;i<n;++i)
			c[i]=sc.nextInt();
		
		//优化搜索顺序=>大的之前分支更少
		Arrays.sort(c,0,n,Comparator.reverseOrder());//用逆序排序[0,n)（降序）
				//注意不能是基础类型
		dfs(0);
		System.out.println(ans);
	}
}
```



### [166. 数独](https://www.acwing.com/problem/content/description/168/)(DFS剪枝优化，找到答案后立刻返回)

> 找到答案后**立刻返回**一般就是每个调用`dfs()`的地方都要有一个返回值，触发链式反应

[数独](https://baike.baidu.com/item/数独/74847?fr=aladdin) 是一种传统益智游戏，你需要把一个 $9×9$ 的数独补充完整，使得数独中每行、每列、每个 $3×3$ 的九宫格内数字 $1∼9$ 均恰好出现一次。

![](https://bkimg.cdn.bcebos.com/pic/9825bc315c6034a8cb048a23cb134954082376a9?x-bce-process=image/format,f_auto/resize,m_lfit,limit_1,h_284)

----

思路：每次枚举当前格子的拓展，直到所有格子选完

#### 用二进制状压(存能选的数)+预处理结果

1. 需要同时符合行、列和大方格，可以用二进制存能存的数

   然后再交集（与运算）就能得到这个位置能选什么

2. 可以将`lowbit(x)`对应哪一位上的数和每个数对应几个$1$预处理出来

#### **剪枝优化：**

1. 优化搜索顺序：每次遍历剩下的格子，看分支数最少的是哪一个

> 综合整个代码来看，直接遍历整个数组就行了
>
> > 如果你想用优先队列，反倒变麻烦，每次拓展，都要改变行，列和大方格的所有记录

2. 排除等效冗余：本身就没有冗余

> 每一层都只枚举一个格子的所有拓展情况，一般是不会有冗余的
>
> 当前格子选完后，看剩下的格子，不会在回头选了（没有顺序问题）

3. 可行性剪枝：知道不合法就不要进入了

> 用行，列和大方格的情况来特判

4. 本题找到答案后就可以立刻退出

```java
//dfs剪枝优化--枚举每个位置的可能
import java.util.*;
public class Main{
	static final int N=9,M=1<<N;//521
	static char[] s=new char[100];//数独：9*9
	
	static int lowbit(int x) {
		return x&-x;
	}
	//打个查询表(预处理)
		//ones[i]:i一共有几个1
		//map[i]:当前选哪一位（最高位的1在哪里）【有点浪费，可以用字典，但是值不大也无所谓】
	static int[] ones=new int[M],map=new int[M];
	
	//行、列、大方格中能填什么(位运算优化)
		//每个位置上能填1~9=>每一位对应一个数
	static int[] row=new int[N],col=new int[N];
	static int[][] cell=new int[3][3];
	//当前格子能填什么
	static int get(int x,int y) {//取个交集：要所有都符合才能填
		return row[x]&col[y]&cell[x/3][y/3];
	}
	
	
	/**
	 * 选或删这个位置：
	 * 如果is_set是真，则将(x,y)这个位置填上k
	 * 			   假，则将(x,y)原本值为'k+1'的填上'.'
	 * 并记录下对行、列和方格的影响
	 * @param x
	 * @param y
	 * @param k:二进制表示下对应哪一位
	 * @param is_set
	 * 
	 * 选了的话，这个位置就不能填了，变为0
	 */
	static void draw(int x,int y,int k,boolean is_set) {
		int v=1<<k;//表示选了第k位
		if(is_set) {
			s[x*N+y]=(char)('1'+k);//转为一维
		}else {
			s[x*N+y]='.';
			v=-v;//又不选了
		}
		//注意是减
		row[x]-=v;
		col[y]-=v;
		cell[x/3][y/3]-=v;
	}
	/**
	 * 初始化行、列和方格的情况
	 * @return 需要填多少个格子
	 */
	static int init() {
		Arrays.fill(row,M-1);
		Arrays.fill(col,M-1);
		for(int i=0;i<3;++i) Arrays.fill(cell[i],M-1);
		
		//一开始就当作一个没填，在一个个慢慢填
		int cnt=0;
		for(int i=0;i<N;++i) {
			for(int j=0;j<N;++j) {
				if(s[i*N+j]=='.') 
					++cnt;//需要填
				else 		
					draw(i,j,s[i*N+j]-'1',true);//为这个位置填上对应的数
			}
		}
		return cnt;
	}
	/**
	 * 枚举剩下的格子该填什么
	 * @param x 还剩几个没填(减着遍历)
	 * @return 
	 * 当找到答案后就可以结束了，没必要在继续填了,这就是返回值的作用
	 * 
	 * 必定是没有等效冗余的情况：
	 * 		每次只会搜可能最小且最前的一个格子（只有一个）
	 * 		这个格子选完以后，就不会在被选了(没有选择顺序问题)
	 */
	static boolean dfs(int cnt) {
		if(cnt==0) return true;
		//优化搜索顺序剪枝：找到分支数最少的
		int minv=10;//极大值
		int x=0,y=0;
		for(int i=0;i<N;++i) 
			for(int j=0;j<N;++j)
				if(s[i*N+j]=='.') {//没填过
					int state=get(i,j);//当前格子能填所有数
					if(ones[state]<minv) {//有几个数能填
						minv=ones[state];
						x=i;
						y=j;
					}
				}
		//给这个格子填数
		int state=get(x,y);
		for(int i=state;i!=0;i-=lowbit(i)) {
			int k=map[lowbit(i)];//当前是哪一位
			draw(x,y,k,true);
			if(dfs(cnt-1)) return true;//成功了，直接返回
			draw(x,y,k,false);//恢复
		}
		return false;//没有填成功
	}
	
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		//预处理
			//最高位的1在哪
		for(int i=0;i<N;++i) map[1<<i]=i;
			//当前数有几个1
		for(int i=0;i<M;++i) 
			for(int j=i;j!=0;j-=lowbit(j)) //不断减去末尾的1
				ones[i]+=1;
		
		while(true) {
			String st=sc.next();
			if("end".equals(st)) break;
			s=st.toCharArray();
			//初始化
			int cnt=init();
			dfs(cnt);
			System.out.println(new String(s));//char数组能初始化String
		}
	}
}
```

参考：[AcWing 166. 数独【详细注释版】 - AcWing](https://www.acwing.com/solution/content/31873/)





### 分组枚举且每组的总和一样(==分组的方式不一样==)

**这些剪枝方式直接记**，试了下其他的优化，都失败了(唉，我的时间呀)

> 优化搜索顺序
>
> 分组枚举：
>
> ​	头没有成功就剪，能变成成组合
>
> ​	特殊：尾没有成功，减（后面又凑出这个数没有意义了）
>
> ​	跳过相同元素

[167. 木棒 - AcWing题库](https://www.acwing.com/problem/content/description/169/)

乔治拿来一组等长的木棒，将它们随机地砍断，使得每一节木棍的长度都不超过 $50$ 个长度单位。(木棍总数不超过$64$)(每一节木棍的长度都用大于零的整数表示）
然后他又想把这些木棍恢复到为裁截前的状态，但忘记了初始时有多少木棒以及木棒的初始长度。
请你设计一个程序，帮助乔治计算木棒的可能最小长度。

（木棍长度最长也不会超过50）

----

（**本题写出了一堆bug出来,好好看看**）

剪枝再题解里面写得比较清楚，具体证明看代码的注解

这里讲下一些注意事项：

> 1. 本题枚举方式最好采用枚举组内元素的方式
>
>    本题限的很死，就是每组的总长都是固定的，**只要有一组不是恰好等于都不行**，这样可以少枚举很多情况
>
>    还有一些等长的等效冗余，很难剪掉
>
> 2. `for`循环中找下一个不同的，循环最优有`++i`，并且不一定能到`j`那边所以`i`应当要指向最后一个相等就是`i=j-1`
>
>    ```c++
>    for(int i=start;i<n;++i) {
>        /*中间一堆操作,可能中途退出*/
>        int j=i+1;
>        for(;j<n;++j) {
>            if(w[j]!=w[i]) break; 
>        }
>        i=j-1;
>    }
>    ```
> 3. **多组数据别忘记初始化了**
>  

```java
//分组dfs使每组的总长一样
	//木棒有多个木棍组成
/**
 * 思路：枚举木棒的长度=>枚举每个分组
 * 注意几点：
 * 	1.本题找到答案（true）会立刻返回且有多组数据，还需要注意初始化问题
 * 	2.最后还是按y总的，枚举组内有哪些元素会比较好
 * 		原本是按枚举元素该分到哪一组，发现部分剪枝优化不好整
 * 		比如像长度相同的木棍不需要放一个位置
 * 	3.这里不要集合把那些已经选过的剔除，因为会存在重复的元素
 * 	4. 有dfs返回值的地方都要return
 */
	
	
import java.io.*;
import java.util.*;
public class Main{
	static final int N=70;
	static int n;
	static Integer[] w=new Integer[N];
	
	static int sum,length;//总长和当前木棒的长度
	static boolean[] st=new boolean[N];//当前木棍是否被选过
	/**
	 * 枚举每个分组里的元素，使分组的总长为length
	 * @param g:当前分组
	 * @param cur:当前分组木棒的长度
	 * @param start:当前分组先要从哪个数开始
	 * @return 能否成功
	 * 注意：有dfs返回值的地方都要return dfs();
	 */
	static boolean dfs(int g,int cur,int start) {
		if((g+1)*length==sum) return true;//最后一组不用看，一定能拼成
		if(cur==length) return dfs(g+1,0,0);//满了就新开一组
		for(int i=start;i<n;++i) {
			if(st[i]||cur+w[i]>length) continue;//已经用过了，就要跳过
			st[i]=true;//选上
			if(dfs(g,cur+w[i],i+1))
				return true;
			//这样没有成功
			st[i]=false;//恢复
			
			//一堆剪枝
			if(cur==0) return false;
			/**
			 * 	剪枝：排除等效冗余
			 * 		1.变成组合，每组的顺序都是升序，避免一些顺序问题
			 * 		2.无法用把当前木棍作为开头
			 * 			第一次使用木棍如果无法放在第一根，当前方案已经不可能了
			 * 			后续的方案，交换一下也能将当前木棍放在第一根，
			 * 			就是只要存在这跟木棍就是不可能
			 */
			if(cur+w[i]==length) return false;
			/**
			 * 	剪枝：排除等效冗余
			 * 		无法将当前木棍作为结尾(本质上和上面使相同的)
			 * 		反证：
			 * 			如果用当前木棍最终凑出了length，但是最终还是失败了
			 * 			假定后续还有可能用多根木棍使当前组凑出length，并成功了
			 * 			那么用当前木棍交换到这个位置也能，矛盾了
			 */
			int j=i+1;
			for(;j<n;++j) {
				if(w[j]!=w[i]) break; 
			}
			i=j-1;//for循环中注意了，还会++，要么就别++了
			/**
			 * 剪枝：排除等效冗余	
			 * 		当前位置放相同的长度是没有意义的，前面已经搜过不行了
			 */
		}
		//这里无需新开一组，每一组都要凑齐才新开
		return false;
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		while(true) {
			//初始化:用到的都要初始化
			Arrays.fill(st,false);
			sum=0;
			length=1;
			n=sc.nextInt();
			if(n==0) break;
			for(int i=0;i<n;++i) {
				w[i]=sc.nextInt();
				sum+=w[i];
			}
			
			//剪枝：优化搜索顺序
			Arrays.sort(w,0,n,(a,b)->{//从大到小排，这样分支会少很多
				return b-a;
			});
			while(length<=sum) {
				if(sum%length==0&&dfs(0,0,0)) {
					System.out.println(length);
					break;
				}
				++length;
			}
		}
	}
}
```

> 这里排除等效冗余：只用了每组的开头和结尾
>
> > **不用看了**，试了下不行，还是会超时
> >
> >  (只能是同层的`dfs`)感觉还能再剪，就是前面某个长度已经不行了，然后又凑出了同长度，可以剪掉。（跟那个结尾是同理的，这个毕竟是组合么，但是不一样呀，结尾那个能更早排除）
> >
> > 如果你用这个剪枝：就不用再判断相等元素了的剪枝，自动跳过

资料：剪枝证明：[DFS之剪枝下面的评论](https://www.acwing.com/file_system/file/content/whole/index/content/137679/)





### 剪枝：用数学:求精确范围+预估答案提前剪枝

[168. 生日蛋糕 - AcWing题库](https://www.acwing.com/problem/content/description/170/)

一个体积为 $Nπ$ 的 $M$ 层生日蛋糕，每层都是一个圆柱体。

设从下往上数第 $i$ 层蛋糕是半径为 $R_i$，高度为 $H_i$ 的圆柱。
当 $i < M$ 时，要求 $R_i > R_{i+1}$ 且 $H_i > H_{i+1}$。

由于要在蛋糕上抹奶油，为尽可能节约经费，我们希望蛋糕外表面（最下一层的下底面除外）的面积 $Q$ 最小。
令 $Q = Sπ$ ，请编程对给出的 $N$ 和 $M$，找出蛋糕的制作方案（适当的 $R_i$ 和 $H_i$ 的值），使 $S$ 最小。
(除 $Q$ 外，以上所有数据皆为正整数。)

**数据范围：**$1 \le N \le 10000$,$1 \le M \le 20$

----

(下面这些很多参考了资料，具体看下面的资料)

(可以自动的约掉$π$了)

**思路**：先枚举每一层=>再枚举每一层的半径和

> 记最底层为m
> 表面积的公式为:$$S_总 = S_{m层上侧面积} + \sum_{i=1}^{m}2\pi R_iH_i$$
> 而体积为:$$V_总= \sum_{i=1}^{m}\pi R_i^2H_i$$

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210127003014554.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NTA3OTM3,size_16,color_FFFFFF,t_70#pic_center)

（注意下：上面是小的）

#### 剪枝优化：

重点是要放在题目需要放在满足的条件和答案中，如果还需要满足一些次要条件，可以适当忽略

设：记总体积为`n`，当前层位`u`, 第u层的高度为$H_u$, 半径为$R_u$，第$m$层到第u层体积的累计值$V_现$【其实就是第m层到u+1的体积和】,面积和是$S_现$

后面的$$V_剩=n-V_现$$【这个值其实就是$V_{1-n}$】

1. 优化搜索顺序：

   > **先从上到下枚举每一层：**
   >
   > > 层内：
   > >
   > > 先枚举半径再枚举高（半径相对于高来说对体积的影响较大）（看公式半径对体积是平方级的），半径由大到小，高度由大到小

2. 可行性剪枝：

   > 把一些一定不满足范围给剔除掉
   >
   > 对于第$u$层，求出每层的半径和高对应的范围:
   >
   > > - 半径
   > >
   > >   每层的半径需要比上层大，比下层小（高度同理）
   > >
   > >   根据那个体积公式，同时高度$H_{min}=u$，还可以求出另一个上限
   > >
   > >   范围：$$ u \leq R_u \leq min \lbrace R_{u+1}-1, \sqrt{\frac{V_剩}{u}} \rbrace$$
   > >
   > > - 高度
   > >
   > >   同上，并且根据枚举顺序，这时还知道了$R_u$
   > >
   > >   范围：$$ u \leq H_u \leq min \lbrace H_{u+1}-1, \frac {{V_剩}}{R_u^2} \rbrace$$
   > >

   (下面就是预测下各个的值范围，看看可不可能是答案)

2. 预处理出最小值，来预估下最小值

   > $minv(u)$:第u层最小体积和
   >
   > $mins(u)$:第u层最小侧面积和
   >
   > - 可行性剪枝：$V_现+minv(u)>n$时，已经不符合条件了
   > - 最优性剪枝：$S_现+mins(u)>=ans$
   
2. 放缩预估下总面积为多少

   > 第1层到第u层：
   >
   > 体积：$V_{1\sim u} = \sum_{i=1}^{u}R_i^2H_i=n - V$【就是剩下的体积】
   >
   > 表面积：$$S_{1-u} = 2\sum_{i=1}^{u}R_iH_i = \frac{2}{R_{u+1}} \sum_{i=1}^{u}R_{u+1}R_iH_i >=  \frac{2}{R_{u+1}}\sum_{i=1}^{u}R_i^2H_i$$
   >
   > 由此得到：$$S_{1-u} >=\frac{2(n-V)}{R_{u+1}}$$【可能取等最后剩下0时】
   >
   > 最优性剪枝:$S_总=S + S_{1-u}=S + \frac{2(n-V)}{R_{u+1}}>=S_{ans}$时已经可以剪掉了

   

**整型有除法最好放最后**

```java
//dfs剪枝优化
	//这里具体看分析那边
import java.io.*;
import java.util.*;
public class Main{
	static final int N=30,INF=(int)1e9;//层数和一个不可能的值
	static int n,m;
	
	//剪枝
	static int[] minv=new int[N],mins=new int[N];
		//minv[u]表示第1~u层的蛋糕的最小体积
		//mins[u]最小第1~u层的蛋糕的最小侧面积（不算上面的圆）
	static int[] R=new int[N],H=new int[N];//当前的所有层的半径和高
		//需要边界需要初始化，避免一些特判
	static int ans=INF;
	/**
	 * 搜索体积和为n，面积和最小的情况
	 * @param u:当前到了第几层
	 * @param v:当前总体积
	 * @param s:当前总面积
	 * 注意：从下往上层搜
	 */
	static void dfs(int u,int v,int s) {
		//一堆剪枝优化
		if(v+minv[u]>n) return;
		if(s+mins[u]>=ans) return;
		if(s+2*(n-v)/R[u+1]>=ans) return;//最优性剪枝
			//预估体积和
			//注意运算顺序，除法最好放最后
		
		if(u==0) {//结束条件（别忘了）
			if(v==n) ans=s;
			return;
		}
		
		//分支一看先看主要的限制的条件：总体积
		for(int r=Math.min(R[u+1]-1,(int)Math.sqrt((n-v)/u));r>=u;--r) {
		//从大到小枚举半径（对体积影响大）
			//还需要特判底层，需要加上上面的面积（别忘了）
			int t=0;
			if(u==m) t=r*r;
			for(int h=Math.min(H[u+1]-1,(n-v)/r/r);h>=u;--h) {//注意看清楚范围
			//从大到小枚举高度
				//不要忘记保存每层的这个了
				R[u]=r;
				H[u]=h;
				dfs(u-1,v+h*r*r,s+h*2*r+t);
			}	
		}
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		n=sc.nextInt();
		m=sc.nextInt();
		//预处理：最小的情况
		for(int i=1;i<=m;++i) {
			minv[i]=minv[i-1]+i*i*i;
			mins[i]=mins[i-1]+2*i*i;
		}
		//预处理下边界问题
		R[m+1]=H[m+1]=INF;
		
		dfs(m,0,0);
		if(ans==INF) ans=0;
		System.out.println(ans);
	}
}
```

参考资料：[AcWing 168. 生日蛋糕【图解+推导】 - AcWing](https://www.acwing.com/solution/content/31876/)



## 迭代加深

**思路**：每次`DFS`都规定一个深度上限，没找到答案深度就`+1`（类似`BFS`）

**场景**：答案深度比较浅或者限制了深度，如果直接`DFS`可能前面会走到比答案深得多的位置

> 迭代加深会一直搜前面的层：(一般只看指数的极端情况)
>
> 当搜索树的分支很多时，搜每一层复杂度是爆炸性增长的，搜之前所有层的和都不如搜当前层，因此可以忽略不记
>
> 就以二叉树为例：$2^0+2^1……+2^{n-1}+2^n=2^{n+1}-1$:前面所有层的和跟只搜最后$n+1$层差不多，不用咋当心
>
> 如果不是指数拓展更不用担心了，分支数少，一般都能搜完



> 为什么直接用`BFS`呢？
>
> 主要是因为`BFS`更容易爆空间，空间复杂度是指数级别，而`DFS`的时间复杂度只有$O(深度)$
>
> 当状态很多时容易爆

![](https://cdn.acwing.com/media/article/image/2021/03/04/2675_69ed07da7c-1.png)

![](https://cdn.acwing.com/media/article/image/2021/03/04/2675_6d4068c17c-2.png)

==如果是那种找到答案立刻放回，记得每次调用`dfs()`时都要有返回值==



[170. 加成序列 - AcWing题库](https://www.acwing.com/problem/content/description/172/)

满足如下条件的序列 $X$（序列中元素被标号为 $1、2、3…m$）被称为“加成序列”：

  1. $X[1]=1$
  2. $X[m]=n$
  3. $X[1]<X[2]<…<X[m-1]<X[m]$
  4. 对于每个 $k$（$2 \le k \le m$）都存在两个整数 $i$ 和 $j$ （$1 \le i,j \le k-1$，$i$ 和 $j$ 可相等），使得 $X[k]=X[i]+X[j]$。

你的任务是：给定一个整数 $n$，找出符合上述条件的长度 $m$ 最小的“加成序列”。

如果有多个满足要求的答案，只需要找出任意一个可行解。

**数据范围：** $1 \le n \le 100$

----

剪枝优化：

1. 优化搜索顺序：

   先枚举大的，显然后面到$n$，分支剩的少

2. 可行性剪枝：

   ①当前两个数的和大于$n$

   ②要比序列的最后一个数大【有这步这里无需标重前面的数了】

3. 排序等效冗余：

   同一个序列，如果和是一样的，不需重复搜

```java
//迭代加深
	/* 一个个深度去搜，适用于答案在比较浅层，如果一条路走到黑情况用特别多
	 * 这里不用bfs主要是空间的原因，这里存序列和每一层的元素容易爆,但是感觉有优化下还是行的
	 * 用dfs空间复杂度仅有O(深度)
	 * */
	//这里这个是原始做法,还能在加点优化（需要点数学证明）
import java.io.*;
import java.util.*;
public class Main{
	static final int N=110;

	static int n;
	static int[] path=new int[N];//记录下当前路径
	/**
	 * 判断深度为k时，能否找到答案
	 * @param u：当前整数序列的第u个
	 * @param k：深度
	 * @return 当深度为k，并且找到答案时，返回真
	 */
	static boolean dfs(int u,int k) {
		if(u==k) {
			return path[u-1]==n;
		}
		boolean[] st=new boolean[N];//主要是避免当前层不要重复搜
			//之前层不用管，只要比当前序列的最后一个数大，一定不会重复
			//注意：每层都是不同判重数组
		//剪枝：优化搜索顺序（从大到小枚举和）
        //这个循环还能优化：就是如果要大于最后一个数且要最短：一定要用到最后一个数
		/*//这个已经能过了
		for(int i=u-1;i>=0;--i) {
			for(int j=i;j>=0;--j) {
				int s=path[i]+path[j];
				//剪枝：可行性剪枝和排除等效冗余
				if(s>n||st[s]||s<=path[u-1]) continue;
					//不要忘记不会大于n
				path[u]=s;
				if(dfs(u+1,k)) return true;//每个dfs都要返回
				st[s]=true;
			}
		}*/
		//循环优化一下
		for(int i=u-1;i>=0;--i){
		    int s=path[i]+path[u-1];
		    if(s>n||st[s]) continue;
		    path[u]=s;
		    if(dfs(u+1,k)) return true;
		    st[s]=true;
		}
		return false;
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		
		//初始化
		path[0]=1;//不要忘记了第一个数
		while(true) {
			n=sc.nextInt();
			if(n==0) break;
			
			int k=1;
			while(!dfs(1,k)) ++k;//迭代加深的过程
			for(int i=0;i<k;++i) 
				System.out.printf("%d ",path[i]);
			System.out.println();
		}	
	}
}
```

通过性质来小小优化下：

> $a[n]$必定是由$a[n-1]$构造而来
>
> 证明：
>
> > （反证）不是由这个构造而来，那么这个序列不需要$a[n-1]$可以更短，长度可以更短，一定不是答案【我们也是一层一层搜的么】

**注意**：==如果是那种找到答案立刻放回，记得每次调用`dfs()`时都要有返回值==

参考资料：[别人的题解](https://www.acwing.com/solution/content/38248/)

[下面的评论有性质的来源](https://www.acwing.com/activity/content/code/content/136954/)











## 双向DFS（空间换时间：前半部打表，后半部查表）（思想重要）

思想：分成两半去搜

​			前一半搜完，打下结果表(需要去重排序，方便查找)；

​			后一半搜完，用后一半的结果，去结果表中查询最优结果，组合起来就是整个的集合

![](https://cdn.acwing.com/media/article/image/2021/03/10/2675_357470bf81-1.png)

---

[171. 送礼物 - AcWing题库](https://www.acwing.com/problem/content/description/173/)

达达帮翰翰给女生送礼物，翰翰一共准备了 $N$ 个礼物，其中第 $i$ 个礼物的重量是 $G[i]$。
达达的力气很大，他一次可以搬动重量之和不超过 $W$ 的任意多个物品。
达达希望一次搬掉尽量重的一些物品，请你告诉达达在他的力气范围内一次性能搬动的最大重量是多少。

**数据范围**:$1 \le N \le 46$,$1 \le W,G[i] \le 2^{31}-1$

---



时间复杂度：$2^k+2^{N-k}*log_2^{2^k}$=$2^k+k*2^{N-k}$，中间处不一定最优，但是一定差不了


```java
//双向dfs(打表：空间换时间)
	//这种思路还是挺重要的
	/* 本题用不了背包DP，因为重量太大了，肯定会爆复杂度O(N*M)
	 * 暴力思路：就是枚举每个礼物选或不选  O(2^N)
	 * 优化思路：
	 * 	1.枚举前一半的集合，将结果集合打表下来，供另一半查询
	 * 		答案表去重排序下，方便查找
	 *  2.枚举剩下的集合，当前重量为s，从表中找到不超过W的最大值(可以用二分)
	 * 	剪枝优化：
	 * 		1.优化搜索顺序：（针对的都是主要限制条件，这里是W）
	 * 			先枚举大的重量，剩下的质量少，分支就少
	 * 		2.可行性剪枝：
	 * 			质量超过W的不看
	 * 
	 * 说明：这里折半其实不一定是最优的，但是一定差不了
	 * 		没必要去找最优的那个点
	 * */
	//二分忘的有点多，这里先实现下二分查找的版本
import java.io.*;
import java.util.*;
public class Main{
	static final int N=55,M=1<<25;//M前半结果集合的数量
	static int W,n;
	static Integer[] g=new Integer[N];
	
	static int[] weight=new int[M];//前半部分的答案表
	static int cnt;//答案表有几个数
	static int ans;

	static int k;//中间点
	/**
	 * 枚举前半部分的集合
	 * 并创建前部分结果集合的查询表
	 * @param u:第u个礼物
	 * @param s:当前选择的总重（注意会爆）
	 * 
	 * 说明：这里到第k个礼物停，枚举的是0~k-1礼物的情况
	 */
	static void dfs1(int u,int s) {
		if(u==k) {
			weight[cnt++]=s;//保存下当前结果
			return;
		}
		//选上
		if((long)s+g[u]<=W)//加个可行性剪枝
			dfs1(u+1,s+g[u]);
		//不选
		dfs1(u+1,s);
	}
	/**
	 * 枚举后半部分，
	 * 并查找前半部分的结果表找到其中不超过W最大值
	 * @参数跟前面同
	 */
	static void dfs2(int u,int s) {
		if(u==n) {
			/*二分查找(有点忘了，复习下)
			 	找weight[i]<=W-s的最大值(可能会爆，稍微移动下)
				1.是找最小还是最大值，来确定是下或上取整
					找最大值(右找模板),向上取整
				2.画二分图
					找最大值，答案在左边
					左边<=W-s；右边>W-s
			 */
			int l=0,r=cnt-1;//不要管边界，因为后面在特判下
			while(l<r) {
				int mid=l+r+1 >>1;//上取整
				if(weight[mid]<=W-s) l=mid;//答案在这边
				else				 r=mid-1;//配齐
			}
			if(weight[l]+(long)s<=W) 
				ans=Math.max(ans,weight[l]+s);
			return;
		}
		//选上
		if((long)s+g[u]<=W)//加个可行性剪枝
			dfs2(u+1,s+g[u]);
		//不选
		dfs2(u+1,s);
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		W=sc.nextInt();
		n=sc.nextInt();
		for(int i=0;i<n;++i)
			g[i]=sc.nextInt();
		
		//优化搜索顺序:大的在前
		Arrays.sort(g,0,n,(a,b)->{
			return b-a;//b<a,后者小于前者
		});
		
		//获取前半部分
		k=n/2;
		dfs1(0,0);
		//对前半部分结果排序去重
			//排序
		Arrays.sort(weight,0,cnt);//一定要规定下边界
			//用流去重
		weight=Arrays.stream(weight).distinct().toArray();
			//注意哈，一定会出现0，后边会跟着去掉
		cnt=weight.length;
		
		//获取后半部分
		dfs2(k,0);
		System.out.println(ans);
	}
}
```

注意：**排序时，最好规定下首尾边界**，不然超过边界的那些$0$可能也会参与排序

> 测试了一下，用`TreeSet`是过不了最后一个数据，会超时。

### 手写去重

**会比用流去重快很多**

思路：(类似队列，但是是在原数组上进行的)特判下首个，如果当前与前者不同就放入

​			最后返回去重后的个数

```java
	static int unique(int[] arr,int len) {//前提：排序
		int t=0;//去重后数组的个数
		for(int i=0;i<len;++i) 
			if(i==0||arr[i-1]!=arr[i])//为首个或当前的不等于前面的
				arr[t++]=arr[i];
		return t;
	}
```

参考：[题解](https://www.acwing.com/solution/content/38250/)



## IDA* （迭代加深+估计深度的函数）

思路：（比`A*`算法更直接点）

> 每次迭代加深，会一个个增加深度，知道找到答案
>
> 但是有一些答案是能预估出当前深度（`max_depth`）一定不符合答案的
>
> 这时可以用估计函数估计出还需要的深度，如果已经不可能了，直接返回
>
> 前提：**估价函数<=真实值**

### [180. 排书 - AcWing题库](https://www.acwing.com/problem/content/182/)

给定 $n$ 本书，编号为 $1 \sim n$。
在初始状态下，书是任意排列的。
在每一次操作中，可以抽取其中连续的一段，再把这段插入到其他某个位置。
我们的目标状态是把书按照 $1 \sim n$ 的顺序依次排列。
求最少需要多少次操作。

如果最少操作次数大于或等于 $5$ 次，则输出 `5 or more`。

**数据范围：**$1 \le n \le 15$

```
输入：
3（样例数）
6
1 3 4 6 2 5
5
5 4 3 2 1
10
6 8 5 3 4 7 2 9 1 10
输出：
2
3
5 or more
```



---
![排书.jpg](https://cdn.acwing.com/media/article/image/2024/03/03/246003_02452a40d9-排书.jpg)

> 这里有个公式（但是我推不出来）（用暴力算也没事）
> $$
> n\times(n-1)+(n-1)\times(n-2)+....+2\times1=\frac{(n-1)\times n\times(n+1)}{3}
> $$
>

注意：**==像这种找到答案就立刻返回的，每次调用`dfs`都要返回==**，引发链式反映

```java
//IDA* 
	//其实就是：迭代加深+估价函数(估深度)
	//详细分析看笔记
import java.io.*;
import java.util.*;
public class Main{
	static final int N=20;//M前半结果集合的数量
	static int n;
	static int[] q=new int[N];
	
	static int[][] back=new int[5][];//每一层的备份:方便插入和复原
	//估价函数(小于等于实际就行)
		/* 最终:每一个都有对应的后继
		 * 每次抽一段插入:能改变的其实就三个数的直接后继
		 * 返回：最少需要插入几次 ceil(有多不是对应后继的/3)
		 * */
		//说明：为0时，表示已经排好序了
	static int f() {
		int cnt=0;//有多少个不是对应的后继的
		for(int i=0;i+1<n;++i)
			if(q[i+1]!=q[i]+1)
				++cnt;
		return (cnt+2)/3;//等价于向上取整
	}
	/**
	 * 在最大深度(次数)下，能否拍好序
	 * @param u:第u次插入
	 * @param max_depth:最大深度
	 * @return
	 */
	//说明：插入时其实会出现重复，这时只往一个方向能避免一些重复
	//注意：找到答案立刻返回，一定要每次调用`dfs`时都要，返回答案（引发链式反应）
	static boolean dfs(int u,int max_depth) {
		if(u+f()>max_depth) return false;
		if(f()==0) return true;
		
		//枚举各个插入的情况
		for(int len=1;len<n;++len) {//枚举当前段的长度
			for(int l=0;l+len-1<n;++l){//枚举当前段的左端点
				int r=l+len-1;//当前段的右端点
				for(int k=r+1;k<n;++k) {//枚举插入点
					back[u]=Arrays.copyOf(q,N);
						//注意N：主要是因为这个方法会返回一个新的数组（会覆盖）
					/*插入：
					 	将右端点后面到插入点这一段往前移
					 	空出来的那些放当前段*/
					int x,y;//x作用在插入后的数组;y作用在备份数组
					//将右端点后面到插入点这一段往前移
					for(x=l,y=r+1;y<=k;x++,y++) 
						q[x]=back[u][y];
					//空出来的放当前段
					for(y=l;y<=r;x++,y++)
						q[x]=back[u][y];
					if(dfs(u+1,max_depth)) return true;
					q=back[u];
				}
			}
		}
		return false;
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		int T=sc.nextInt();
		while(T-- >0) {
			n=sc.nextInt();
			for(int i=0;i<n;++i)
				q[i]=sc.nextInt();
			int depth=0;
			while(depth<5&&!dfs(0,depth)) {
				++depth;
			}
			if(depth>=5)
				System.out.println("5 or more");
			else 
				System.out.println(depth);
		}
	}
}
```

#### 数组的复制：

(像什么移动插入交换，最好都复制一遍，复制数组不动，改变原数组就行)

**特别注意：**

> **如果是数组直接等于另一个数组时`数组a=数组b;`，要特别注意，因为这时会共用同一个地址了，很危险**
>
> 这时你在什么改变都是同一个数组，最后都会相同

`Arrays.copyOf`:这个会生成**副本**，这样可以尽可能的避免用同地址问题【上面等于没有问题，因为又生成一个新的地址，并没有指向同一块地址】

`System.arrayscopy`:是**直接将值复制过去**，如果这时还直接`数组=备份数组`这时就会有问题了

例：(呜呜，又写了个小bug)

```java
				for(int k=r+1;k<n;++k) {//枚举插入点
					System.arraycopy(q, 0, back[u], 0, n);
					。。。。。
                      //q=back[u];
                      /*这样是错的，
                      		k到下一次循环时，q数组已经指向了back[u]数组
                      		这时q数组原来的地址已经丢失了，q这时就是back[u]数组
                      		也就是说后面无论q都是等于back[u]的
                      */
					System.arraycopy(back[u], 0, q, 0, n);
				}
```

注意：**如果是值复制，那么后面还原时也要复制**

**方法**：

- `int b[]=Arrays.copyOfRange(a,l,r);`:截取数组,注意: 左闭右开:$[l,r)$

  `Arrays.copyOf(a,len)`

  > 这个是新生成一个数组，不是单纯的复制值
  >
  > 也就是说**会覆盖**，因此最好每次复制时直接`Arrays.copyOf(a,N)`，这样可以尽可能避免因为覆盖而缩小的问题

- `public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)`

  > 第一个参数是要被复制的数组
  > 第二个参数是被复制的数字开始复制的下标
  > 第三个参数是目标数组，也就是要把数据放进来的数组
  > 第四个参数是从目标数据第几个下标开始放入数据
  > 第五个参数表示从被复制的数组中拿几个数值放到目标数组中
  >
  > [System.arraycopy的使用方法详解_arraycopy方法的作用-CSDN博客](https://blog.csdn.net/wenzhi20102321/article/details/78444158)



参考：[y总的题解](https://www.acwing.com/solution/content/4050/)



### [181. 回转游戏 - AcWing题库](https://www.acwing.com/problem/content/183/)【操作特殊+估价函数特殊】



有一个 `#` 形的棋盘，上面有 $1,2,3$ 三种数字各 $8$ 个。
给定 $8$ 种操作，分别为图中的 $A \sim H$。
这些操作会按照图中字母和箭头所指明的方向，把一条长为 $7$ 的序列循环移动 $1$ 个单位。

给定一个初始状态，请使用最少的操作次数，使 `#` 形棋盘最中间的 $8$ 个格子里的数字相同。

例如：下图最左边的 `#` 形棋盘执行操作 $A$ 后，会变为下图中间的 `#` 形棋盘，再执行操作 $C$ 后会变成下图最右边的 `#` 形棋盘。

![2286_1.jpg](https://cdn.acwing.com/media/article/image/2019/01/23/19_4ec33e321e-2286_1.jpg)

```
上图
输入：1 1 1 1 3 2 3 2 3 1 3 2 2 3 1 2 2 2 3 1 2 1 3 3
输出：
AC
2
```

**输入格式：**多组用例（输入0截止）。每个测试用例占一行，包含 $24$ 个数字，表示将初始棋盘中的每一个位置的数字，按整体从上到下，同行从左到右的顺序依次列出。

**输出格式**：有多个答案输出字典序最小的，第一行输出操作，第二行最终中间格的数字

---

时间复杂度：$O(7^k)$

> 假设答案最少需要 $k$ 步，每次需要枚举 $7$ 种不同操作（除了上一步的逆操作），因此最坏情况下需要枚举 $7^k$ 种方案。但加入启发函数后，实际枚举到的状态数很少。

**操作：**（再好好看看）

> 每个方向，按顺序存下对应编号，倒时候直接对应编号的数组就行了

估价函数：(挺不一样的)

- 统计中间8个方格中出现次数最多的数出现了多少次，记为 $k$ 次。
- 每次操作会从中间8个方格中移出一个数，再移入一个数，所以最多会减少一个不同的数。因此估价函数为$8-k$

剪枝：不要枚举上一次的逆操作，否则又还原了

![回转游戏.jpg](https://cdn.acwing.com/media/article/image/2024/03/03/246003_5ab31189d9-回转游戏.jpg) 

```c++
/*
      0     1
      2     3
4  5  6  7  8  9  10
      11    12
13 14 15 16 17 18 19
      20    21
      22    23
*/
```



```java
//IDA* 
	//其实就是：迭代加深+估价函数(估深度)
import java.io.*;
import java.util.*;
public class Main{
	static final int N=30;//M前半结果集合的数量
	static int[] g=new int[N];//用来存棋盘
	
	/*题目答案要求要字典序：
	 	所以每一层的操作直接按字典序来
	 	这样能够保证一个搜到的就是字典序最小的
	 	（因为就是按字典序的顺序搜的）*/
	//转为对应的一维编号
	static int[][] op= {//每一方向对应的编号
			{ 0, 2, 6,11,15,20,22},
			{ 1, 3, 8,12,17,21,23},
			{10, 9, 8, 7, 6, 5, 4},
			{19,18,17,16,15,14,13},
			{23,21,17,12, 8, 3, 1},
			{22,20,15,11, 6, 2, 0},
			{13,14,15,16,17,18,19},
			{ 4, 5, 6, 7, 8, 9,10}
	};
	static int[] center= {6,7,8,11,12,15,16,17};//最中间8格
	static int[] opposite= {5,4,7,6,1,0,3,2};//每个操作对应的逆操作
	
	static int[] path=new int[100];
	/**
	 * 估值函数：判断当前状态最少还需要几步
	 * 	【备注：每次只能移动一格】
	 * 设计:1.找到其中出现最多格数
	 * 		2.计算当前还差几个
	 * @return
	 * 说明：当估计函数等于0时，表示成功了
	 */
	static int[] sum=new int[4];//一共只有三个数字,每个数字出现几次
		//记得要还原
	static int f() {
		Arrays.fill(sum,0);
		for(int i=0;i<8;++i) {
			++sum[g[center[i]]];
		}
		int res=8;
		for(int i=1;i<=3;++i) {
			res=Math.min(res,8-sum[i]);
		}
		return res;
	}
	/**
	 * 对当前棋盘惊醒【'A'+x】这个操作
	 * @param x
	 */
	static void operation(int x) {
		int t=g[op[x][0]];//记录下首个
		//剩下的往前移
		for(int i=1;i<7;++i)
			g[op[x][i-1]]=g[op[x][i]];
		//剩下的位置就是给首个的
		g[op[x][6]]=t;
	}
	/**
	 * 判断在最大深度内能否得到目标
	 * @param u:当前深度
	 * @param max_depth：最大深度
	 * @param last：上一次操作
	 * @return
	 */
	//注意要链式反应
	static boolean dfs(int u,int max_depth,int last) {
		if(u+f()>max_depth) return false;//超过深度了
		if(f()==0) return true;
		//八种操作
		for(int i=0;i<8;++i) {
			if(opposite[i]==last) 
				continue;//排除下等效冗余
				//现在进行之前的逆操作又还原回去了
			operation(i);
			path[u]=i;
			if(dfs(u+1,max_depth,i))
				return true;//注意：链式反应！！！！！
			//还原
			operation(opposite[i]);//进行下逆操作还原回去
		}
		
		return false;
	}
		
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		while(!sc.hasNext("0")) {
			for(int i=0;i<24;++i)
				g[i]=sc.nextInt();
			
			//迭代加深算法
			int depth=0;//最大深度
			while(!dfs(0,depth,-1))//向下搜深度
				++depth;
				//-1表示初始状态
			
			if(depth==0)
				System.out.print("No moves needed");
			else
				for(int i=0;i<depth;++i)
					System.out.print((char)('A'+path[i]));
			System.out.println("\n"+g[6]);
		}
	}
}
```

参考：[y总的题解](https://www.acwing.com/solution/content/4056/)

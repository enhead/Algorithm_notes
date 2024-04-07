## 注意：`bw.readLine()`和`sc.next【xx】`一起用的时候的回车，很可能`bw.readLine()`会只读入一个回车

---------



## [[NOIP2010\]导弹拦截 ](https://ac.nowcoder.com/acm/problem/16601)(结构体排序+贪心枚举)

题目：

经过11年的韬光养晦，某国研发出了一种新的导弹拦截系统，凡是与它的距离不超过其工作半径的导弹都能够被它成功拦截。当工作半径为0时，则能够拦截与它位置恰好相同的导弹。但该导弹拦截系统也存在这样的缺陷：每套系统每天只能设定一次工作半径。而当天的使用代价，就是所有系统工作半径的平方和。

某天，雷达捕捉到敌国的导弹来袭。由于该系统尚处于试验阶段，所以只有两套系统投入工作。如果现在的要求是拦截所有的导弹，请计算这一天的最小使用代价。

-------

```java
/*
思路：
    先指定第一套系统拦截所有导弹，
    然后按照从大到小的距离，将导弹让给第二套系统拦截
*/

import java.util.*;
import java.io.*;
class P implements Comparable<P>{
    int x,y,d;//根据题目的范围并不会爆
//     @Override//有没有都没差
    public int compareTo(P o){
    //后者<前者，也就是从大到小排
        return o.d-d;
    }
    public P(int x1,int y1,int x2,int y2){
        x=x1;
        y=y1;
        d=Main.dist(x1,y1,x2,y2);
    }
    
}
public class Main{
    public static BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
    public static BufferedWriter bw=new BufferedWriter(new OutputStreamWriter(System.out));
    public static Scanner sc=new Scanner(System.in);
    public static int dist(int x1,int y1,int x2,int y2){
        return (x1-x2)*(x1-x2)+(y1-y2)*(y1-y2);
    }
    public static void main(String[] args)throws Exception{
        int x1=sc.nextInt(),y1=sc.nextInt(),x2=sc.nextInt(),y2=sc.nextInt();
        int n=sc.nextInt();
        P[] ps=new P[n+10];
        for(int i=0;i<n;++i)
            ps[i]=new P(sc.nextInt(),sc.nextInt(),x1,y1);
        Arrays.sort(ps,0,n);
        int res=ps[0].d;
        //从小到大，让系统二去拦截导弹
        int r2=0;
        for(int i=0;i<n;++i){
            //看系统2拦截这枚导弹需要多远
            r2=Math.max(r2,dist(ps[i].x,ps[i].y,x2,y2));
            int r1=((i!=n-1)?ps[i+1].d:0);//特判一下系统一个都不拦截的情况
            res=Math.min(res,r2+r1);
        }
        System.out.println(res);   
    }
}
```

## 输出`res+'\n'`：这样会自动转为整型，应该用字符串

## 爆`int`:如果运算过程中会出现`ll`，但是又通过取模回到整型（java中特别容易错）

注意观察这个式子：`(int)((long)t.sum*mul+(long)(t.r-t.l+1)*add)%p`

整数转换的优先级特别高，在取模前(int)(long)已经被转为整型了，才取模，这个时候就是错的

应该改成：

```java
(int)(((long)t.sum*mul+(long)(t.r-t.l+1)*add)%p)
```

注意：`int`转换==一定要保证是最后运算==





----



## java中不能直接输入字符，一般都是通过字符串转换



## `java`从编译器中复制过去，别忘记去掉那个包名



##  不断查找字符串来替换

注意：`c++的s.find(要找的字符串【,int pos】)`和``java的s.indexOf(要找的字符串【,int pos】)``

1. **每次找完`pos`一定要`+1`**，不然会死循环

2. 两个字符传不一定相同：

   ```java
   				int k = t.indexOf(a[i]);
   				while (k != -1) {
   					String x = t.substring(0, k) + b[i] + t.substring(k + a[i].length());// 字串变换
   										//这里我真的服啦，a[i]和b[i]的大小不一定相同
   					k=t.indexOf(a[i], k+1);//这一步千万别忘了，一定要往下走
   					
   					代码在这写
   				}
   ```

## 解决`eclipse`中 `while(Scanner.hasNext())`一直为死循环的问题



- 最好在编译器中，加一个结束符号0
- 样例末尾加一个0

**后面记得改回来**

```java
import java.util.Scanner;
 
public class ScannerKeyBoardTest1 {
 
	public static void main(String[] args) {
 
		// 例：当输入"0"时，循环结束
		Scanner sc = new Scanner(System.in);
		while (!sc.hasNext("0")) {
			System.out.println("键盘输入的内容是："+sc.next());
		}
	}
}
```



参考：[解决Java中 while(Scanner.hasNext())一直为死循环的问题_java scanner hasnext一直在读取-CSDN博客](https://blog.csdn.net/Rex_WUST/article/details/88424076)





## 优先队列默认小根堆



## 排序从1开始的时候，注意==左闭右开==

## java没有无符号的整型

像什么字符串哈希直接用就行

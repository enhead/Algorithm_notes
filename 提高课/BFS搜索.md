# `BFS`搜索

## 洪水填充Flood Fill

> 下面这个没有没有就差一点点效率：
>
> ​	可以在拓展的是否标重，**注意起点**

- `dfs`:栈不需自己写，实现起来容易一点点，但是无法求最短路，爆栈
- `bfs`：能求最短路

### 池塘计数（基础模板）

```
一块N*M的矩形土地，有一些土地被水淹了
每个单元格内，如果包含雨水，则用”W”表示，如果不含雨水，则用”.”表示。
每组相连的积水单元格集合可以看作是一片池塘。
每个单元格视为与其上、下、左、右、左上、右上、左下、右下八个邻近单元格相连。
约翰想知道形成多少片池塘？
```
![池塘计数.jpg](https://cdn.acwing.com/media/article/image/2023/08/23/246003_e00d871041-池塘计数.jpg) 
```c++
#include<bits/stdc++.h>
using namespace std;
//简写
typedef pair<int,int> pii;
#define x first//名字 常量
#define y second
const int N=1010;
int n,m;
char g[N][N];
bool st[N][N];//bfs中看是否走过
//stl版
void bfs(int sx,int sy){
    queue<pii> q;
    q.push({sx,sy});
    st[sx][sy]=1;
    while(!q.empty()){
        auto t=q.front();
        q.pop();
        //八联通的遍历方法
        for(int i=t.x-1;i<=t.x+1;i++){
            for(int j=t.y-1;j<=t.y+1;j++){
                //只需特判一下中间
                if(i==t.x&&j==t.y) continue;
                //首先要在矩形内
                if(i<1||i>n||j<1||j>m) continue;
                //其次要是水且没遍历过
                if(g[i][j]=='.'||st[i][j])continue;
                q.push({i,j});
                st[i][j]=1;
            }
        }
    }
}   

//数组模拟版
/*
pii q[N*N];
    //避免整个矩形都是的情况
void bfs(int sx,int sy){
    int hh=0,tt=0;//队头队尾,这里按模板都是表示实际的位置
        //不过这里默认有一个
    //把对头队尾放着，相当于新建了一个队列
    q[tt]={sx,sy};
    while(hh<=tt){
        auto t=q[hh++];
        for(int i=t.x-1;i<=t.x+1;i++){
            for(int j=t.y-1;j<=t.y+1;j++){
                if(i==t.x&&j==t.y) continue;
                if(i<1||i>n||j<1||j>m) continue;
                if(g[i][j]=='.'||st[i][j]) continue;
                q[++tt]={i,j};
                st[i][j]=1;
            }
        }
    }
}
*/
int main(){
    cin>>n>>m;
    for(int i=1;i<=n;i++) scanf("%s",g[i]+1);
        //一般输入字符串不会出错，但是如果是一个字符小心
        //从第一个开始输入
    int cnt=0;//记录共有多少个水潭
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            if(g[i][j]=='W'&&!st[i][j]){
            //标记完这个水塘
                bfs(i,j);
                cnt++;
            }
        }
    }
    cout<<cnt;
    return 0;
}
```
```java
import java.util.*;
class Pair{
    int x,y;
    Pair(int x,int y){
        this.x=x;
        this.y=y;
    }
}
public class Main{
    static final int N=1010;
    static int n,m;
    static char[][] g=new char[N][N];
    
    static boolean[][] st=new boolean[N][N];
    static Queue<Pair> q=new LinkedList<>();//避免多次创建
    static void bfs(int sx,int sy){
        //第一次拓展到就能标记了
        q.add(new Pair(sx,sy));
        st[sx][sy]=true;
        //八联通遍历小技巧
        while(!q.isEmpty()){
            Pair t=q.remove();
            // System.out.println(t.x+" "+t.y);
            for(int i=-1;i<=1;++i){
                for(int j=-1;j<=1;++j){
                    int x=t.x+i,y=t.y+j;
                    if(x==t.x&&y==t.y) continue;//中间那一块
                    if(x<1||x>n||y<1||y>m) continue;
                    if(g[x][y]=='.'||st[x][y]) continue;
                    st[x][y]=true;
                    q.add(new Pair(x,y));
                }
            }
        }
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        n=sc.nextInt();
        m=sc.nextInt();
        for(int i=1;i<=n;++i){
            char[] line=sc.next().toCharArray();
            for(int j=1;j<=m;++j){
                g[i][j]=line[j-1];
            }
        }
        
        //洪水填充算法
        int res=0;
        for(int i=1;i<=n;++i){
            for(int j=1;j<=m;++j){
                if(g[i][j]=='.'||st[i][j]) continue;//没水或者走过了
                bfs(i,j);
                // System.out.println();
                ++res;
            }
        }
        System.out.println(res);
    }
}
```



### 城堡问题(联通形态特殊)

（联通的特殊表示：引入二进制表示每个方格的状态，当相邻没有墙表示联通）
```
    1   2   3   4   5   6   7  
   ###########################################
 1 #       #       #           #
   #######   #######   #   #######   #
 2 #   #       #   #   #   #   #
   #   #######   #######   #######   #
 3 #           #   #   #   #   #
   #   #############   #######   #   #
 4 #   #                   #   #
   ###########################################
城堡被分割成 m ∗ n 个方格区域，

每个方格区域可以有0~4面墙（方向：上北下南左西右东）
（每个方块中墙的特征由数字 P  来描述，
我们用1表示西墙，2表示北墙，4表示东墙，8表示南墙，
P为该方块包含墙的数字之和。）
例如，如果一个方块的 P=3，
      则 3 = 1 + 2，该方块包含西墙和北墙。
城堡的内墙被计算两次，方块(1,1)的南墙同时也是方块(2,1)的北墙
 
注意：墙体厚度忽略不计。
计算有多少个房间?最大的房间有多大?

```
![城堡问题.jpg](https://cdn.acwing.com/media/article/image/2023/08/24/246003_7cf00f8e42-城堡问题.jpg) 

（**注意：是从当前格子的视角出发**，如果是从拓展格子出发是需要换顺序的）

![城堡问题2.jpg](https://cdn.acwing.com/media/article/image/2023/08/24/246003_8260645a42-城堡问题2.jpg) 

```c++
#include<bits/stdc++.h>
using namespace std;
const int N=60;
typedef pair<int,int> pii;
#define x first
#define y second
int n,m;
int g[N][N];
/*转为二进制的表示，1表示有墙
    低位到高位分别对应西北东南*/
int mx[4]={0,-1,0,1};
int my[4]={-1,0,1,0};
bool st[N][N];//判断是否走过
int bfs(int sx,int sy){//走完这个房间
                    //并返回面积
    queue<pii> q;
    q.push({sx,sy});
    st[sx][sy]=1;//标记走过只能在入队时（具体看笔记）
    int area=1;//能在被搜时统计，也能在入队时统计
    //为了避免一些没必要的麻烦，最好都只在入队时操作
    while(!q.empty()){
    //队头被搜时
        auto t=q.front();
        q.pop();
        for(int i=0;i<4;i++){//分别四个方向
            int x=t.x+mx[i],y=t.y+my[i];
            //是否能走
                //1.不在矩形内
            if(x<1||x>n||y<1||y>m) continue;
                //2.被走过
            if(st[x][y]) continue;
                //3.有墙
            if(g[t.x][t.y]>>i&1) continue;
                //这个方向是1，说明有墙
                //呜呜，注意是用当前格子来判断
            //入队时
            q.push({x,y});
            st[x][y]=1;
            area++;
        }
    }
    return area;
}
int main(){
    cin>>n>>m;
    for(int i=1;i<=n;i++)
        for(int j=1;j<=m;j++)
            cin>>g[i][j];
    //枚举所有位置，看是否有没走过的地方
    int cnt=0,area=0;//个数和最大面积
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            if(!st[i][j]){
                // cout<<i<<" "<<j<<endl;
                area=max(area,bfs(i,j));
                cnt++;
            }
        }
    }
    cout<<cnt<<endl<<area<<endl;
    return 0;
}
```

###  山峰和山谷(边界问题)

（判断拓展时候：引入对联通的边界进行特判）


----------

给定一个地图，地图被分为 $n \times n$ 的网格，
每个格子 $(i,j)$ 的高度 $w(i,j)$ 是给定的。
若两个格子有公共顶点，那么它们就是相邻的格子(八联通)
我们定义一个格子的集合 $S$ 为山峰（山谷）当且仅当：


1. $S$ 的所有格子都有相同的高度。
2. $S$ 的所有格子都连通。
3. 对于 $s$ 属于 $S$，与 $s$ 相邻的 $s’$ 不属于 $S$，都有 $w_s > w_{s’}$（山峰），或者 $w_s < w_{s’}$（山谷）。
4. 如果周围不存在相邻区域，则同时将其视为山峰和山谷。

如果所有格子都有相同的高度，那么整个地图即是山峰，又是山谷。
求出山峰和山谷的数量？

例子：3个山谷，3个山峰![](https://cdn.acwing.com/media/article/image/2019/10/16/19_08db5e60ef-2.png)


----------

```
本题需要判断与外围的关系：
    在求bfs的扩展情况时：
        把不合法去掉后
        特意判断一下联通块外围的情况
        （就是刚刚不符合联通块条件的那几个）
```
```c++
//只需额外多加一个判断边界周围的情况
#include<bits/stdc++.h>
using namespace std;
typedef pair<int,int> pii;
#define x first
#define y second
const int N=1010;
int n,g[N][N];
pii q[N*N];//可能整个矩形都在
bool st[N][N];
void bfs(int sx,int sy,bool &has_higher,bool &has_lower){
        //这里用了取值符，传的是实参
    int hh=0,tt=0;
    q[0]={sx,sy};
    st[sx][sy]=1;//注意是入队时标记
    while(hh<=tt){
        auto t=q[hh++];
        //八联通图遍历
        for(int i=t.x-1;i<=t.x+1;i++){
            for(int j=t.y-1;j<=t.y+1;j++){
                //将中间判掉
                if(i==t.x&&j==t.y) continue;
                //不在矩形中
                if(i<1||i>n||j<1||j>n) continue;
                //到了联通块的周围(没有相等)
                if(g[i][j]!=g[t.x][t.y]){
                    //存在更高的
                    if(g[i][j]>g[t.x][t.y]) has_higher=1;
                    //存在更低的
                    if(g[i][j]<g[t.x][t.y]) has_lower=1;
                }else if(!st[i][j]){//相等且没被遍历过
                    q[++tt]={i,j};
                    st[i][j]=1;
                }
            }
        }
    }
} 
int main(){
    cin>>n;
    for(int i=1;i<=n;i++)
        for(int j=1;j<=n;j++)
            scanf("%d",&g[i][j]);
    int peak=0,valley=0;
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            if(!st[i][j]){
                bool has_higher=0,has_lower=0;
                //是否有更高，更低的
                bfs(i,j,has_higher,has_lower);
                if(!has_higher) peak++;
                    //没有更高的说明是峰
                if(!has_lower) valley++;
                    //没有更低的说明是谷
            }
        }
    }
    printf("%d %d\n",peak,valley);
    return 0;
}
```

**注意：这个判断山峰和谷的办法**：没有更高为峰；没有更低为谷



## 最短路模型（条件：权值相等）

注意权值相等时才能用

### 迷宫问题（输出路径）

**输出路径**：需要能查询当前点对应的上一个点【key-value:能查询当前点就是将这个点当成键，上一个点和其它答案需要信息就是值】

> 如果只是坐标的话，用数组就能表示出来
>
> 但是如果字符串或者数字很大等等其他东西，数组不一定能表示，**这时需要使用字典数据结构**

从终点开始搜，避免翻转数组

```
给定一个n*n的二维数组：
它表示一个迷宫，其中的1表示墙壁，0表示可以走的路，只能横着走或竖着走，
不能斜着走，要求编程序找出从左上角到右下角的最短路线。
数据保证至少存在一条从左上角走到右下角的路径。
```
![迷宫问题.jpg](https://cdn.acwing.com/media/article/image/2023/08/26/246003_62239f2044-迷宫问题.jpg) 
```c++
//bfs涉及到记录路径
/*由于bfs是由上一步而来，只能记录上一步从何而来。
  所以这里是从终点倒推回去，
  这样实际记录的就是下一步的*/
  
const int N=1010;
int n;
bool g[N][N];
pii q[N*N];//队列
pii pre[N][N];//记录上一步是啥
int mx[4]={1,0,-1,0};
int my[4]={0,1,0,-1};
void bfs(int sx,int sy){//以这个为起点
//初始化
    int hh=0,tt=0;
    q[0]={sx,sy};
    
    memset(pre,-1,sizeof pre);
        //将所有值都赋为-1
    pre[sx][sy]={0,0};
    while(hh<=tt){
        auto t=q[hh++];//取出队首
        for(int i=0;i<4;i++){
            int x=t.x+mx[i],y=t.y+my[i];
            if(x<0||x>=n||y<0||y>=n) continue;
            if(g[x][y]) continue;
            if(pre[x][y].x!=-1) continue;
                //已经搜过了
            q[++tt]={x,y};
            pre[x][y]=t;
        }
    }
}
int main(){
    cin>>n;
    //这题比较特殊，从(0,0)开始
    for(int i=0;i<n;i++)
        for(int j=0;j<n;j++)
            scanf("%d",&g[i][j]);
    bfs(n-1,n-1);//从终点往回搜
    pii end(0,0);//现在的起点
        //等价于pii end={0,0};
    while(1){
        printf("%d %d\n",end.x,end.y);
        if(end.x==n-1&&end.y==n-1) break;//到达终点
        end=pre[end.x][end.y];//下一步
    }
    return 0;
}
```

### 武士风度的牛(记录步数)

需要记录步数

```
有一头牛叫Knight，能像象棋中马那样走，他的目标是吃一捆草。
给定一个n*m的农场地图，Knight的位置用 K 来标记，
障碍的位置用 * 来标记，草的位置用 H 来标记。
```
![武士风度的牛.jpg](https://cdn.acwing.com/media/article/image/2023/08/27/246003_aa0eecf044-武士风度的牛.jpg) 
```c++
int n,m;
char g[N][N];
pii q[N*N];//队列
int dist[N][N];
//记录所对应的层数（步数），顺便标重
//偏量法：记录马的走法
int mx[8]={-2,-2,-1,1,2,2,1,-1};
int my[8]={-1,1,2,2,1,-1,-2,-2};
int bfs(){
    //找起点
    int sx,sy;
    for(int i=1;i<=n;i++)
        for(int j=1;j<=m;j++)
            if(g[i][j]=='K'){
                sx=i,sy=j;
                break;
            }
    
    memset(dist,-1,sizeof dist);
        //-1表示没走过
    int hh=0,tt=0;
    q[0]={sx,sy};
    dist[sx][sy]=0;
    while(hh<=tt){
        auto t=q[hh++];
        for(int i=0;i<8;i++){
            int x=t.x+mx[i],y=t.y+my[i];
            //1.边界
            if(x<1||x>n||y<1||y>m) continue;
            //2.题目要求不能走
            if(g[x][y]=='*')    continue;
            //3.已经走过
            if(dist[x][y]!=-1) continue;
            //找到终点
            if(g[x][y]=='H') return dist[t.x][t.y]+1;
            q[++tt]={x,y};
            dist[x][y]=dist[t.x][t.y]+1;;
        }
    }
    return -1;//没找到
}
int main(){
    cin>>m>>n;
    //注意先输的是列数
    for(int i=1;i<=n;i++) cin>>(g[i]+1);
    cout<<bfs();
    return 0;
}
```



### 抓住那头牛（数轴搜索）

如果动态规划的方式，不能一个个来，要用到后面的数据，那么大概率不行了。
```
在一个数轴上，起点为n,终点为k
有两种移动方式：
    1.从X移动到 X−1或 X+1，每次移动花费一分钟
    2.从X移动到 2∗X，每次移动花费一分钟
最少花多少时间能到达?
```
![抓住那头牛.jpg](https://cdn.acwing.com/media/article/image/2023/08/27/246003_ac1fd67844-抓住那头牛.jpg) 

```c++
#include<bits/stdc++.h>
using namespace std;
const int N=1e5+10;//这里看个笔记吧
//只需对数轴bfs
    //不需要具体的范围，反正会返回第一个搜到的
    //但是那个乘2的情况，是要好好想一下的，否则会超
int n,k;//这里n是起点，k是终点
int q[N];//这里是推出来的
int dist[N];
int bfs(){
    memset(dist,-1,sizeof dist);
    int hh=0,tt=0;
    q[0]={n};
    dist[n]=0;
    while(hh<=tt){
        int t=q[hh++];
        // cout<<t<<endl;
        //找到了可以结束了
        if(t==k) return dist[k];
        //三种扩展形态
            //要在范围内且没走过
            //1.-1
        if(t-1>=0&&dist[t-1]==-1){
            dist[t-1]=dist[t]+1;
            q[++tt]=t-1;
        }
            //2.+1
        if(t+1<N&&dist[t+1]==-1){
            dist[t+1]=dist[t]+1;
            q[++tt]=t+1;
        }
            //2.*2
        if(t*2<N&&dist[t*2]==-1){
        //这里优化了
            dist[t*2]=dist[t]+1;
            q[++tt]=t*2;
        }
    }
    return -1;//找不到
}
int main(){
    cin>>n>>k;
    cout<<bfs();
    return 0;
}
```


# 多源BFS
## 矩阵距离
> 给定一个 $N$$N$ 行 $M$$M$ 列的 $01$$01$ 矩阵 $A$$A$，$A[i][j]$$A[i][j]$ 与 $A[k][l]$$A[k][l]$ 之间的曼哈顿距离定义为：$dist(A[i][j],A[k][l])=|i-k|+|j-l|$
>
> （就是这两个点只直走上下左右的距离）
>
> 输出一个 $N$$N$ 行 $M$$M$ 列的整数矩阵 $B$$B$，其中：$B[i][j]=min_{1≤x≤N,1≤y≤M,A[x][y]=1}⁡{dist(A[i][j],A[x][y])}$
>
> （其实就是求当前点到所有1的最小距离）



----------

![矩阵距离.jpg](https://cdn.acwing.com/media/article/image/2023/08/28/246003_a3defc4e45-矩阵距离.jpg) 
```c++
//多个起点的bfs

const int N=1010;
int n,m;
char g[N][N];
pii q[N*N];
int dist[N][N];//记录没有没有被搜过
                //以及距离起点的距离
int mx[4]={-1,0,1,0};
int my[4]={0,1,0,-1};
void bfs(){
    //初始化
    int hh=0,tt=-1;
    memset(dist,-1,sizeof dist);
    //找出所有起点
    for(int i=1;i<=n;i++)
        for(int j=1;j<=m;j++)
            if(g[i][j]=='1'){
                 q[++tt]={i,j};
                 dist[i][j]=0;
            }
            

    while(hh<=tt){
        auto t=q[hh++];
        for(int i=0;i<4;i++){
            int x=t.x+mx[i],y=t.y+my[i];    
            //边界
            if(x<1||x>n||y<1||y>m) continue;
            //不能走和走过
            if(dist[x][y]!=-1) continue;
            q[++tt]={x,y};
            dist[x][y]=dist[t.x][t.y]+1;
        }
    }
}
int main(){
    scanf("%d %d",&n,&m);
    for(int i=1;i<=n;i++) scanf("%s",g[i]+1);
    bfs();
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++) printf("%d ",dist[i][j]);
        puts("");
    }
    return 0;
}
```




# 最小步数模型
## 魔板
1. 将整个状态看成一个点（用字符串配和map）来搜
2. 需要到起点的距离
3. 需要输出路径（注意这里不能倒着搜了）
4. bfs延展形式特殊


这是一张有 $8$ 个大小相同的格子的魔板：

```
1 2 3 4
8 7 6 5
```

我们知道魔板的每一个方格都有一种颜色。
这 $8$ 种颜色用前 $8$ 个正整数来表示。

可以用颜色的序列来表示一种魔板状态，规定从魔板的左上角开始，沿顺时针方向依次取出整数，构成一个颜色序列。

对于上图的魔板状态，我们用序列 $(1,2,3,4,5,6,7,8)$ 来表示，这是基本状态。

这里提供三种基本操作，分别用大写字母 A，B，C 来表示（可以通过这些操作改变魔板的状态）：
A：交换上下两行；  
B：将最右边的一列插入到最左边；  
C：魔板中央对的4个数作顺时针旋转。

下面是对基本状态进行操作的示范：

A：

```
8 7 6 5
1 2 3 4
```

B：

```
4 1 2 3
5 8 7 6
```

C：

```
1 7 2 4
8 6 3 5
```
对于每种可能的状态，这三种基本操作都可以使用。
给你一个特殊状态，计算用最少的基本操作完成基本状态到特殊状态的转换，输出操作次数和基本操作序列。（都用字符串表示状态）
**注意**：数据保证一定有解。


----------

![最小步数模型.jpg](https://cdn.acwing.com/media/article/image/2023/08/31/246003_6d49e51e47-最小步数模型.jpg) 
```c++
/*由于可以不用换回原数组，就不用y总那样表示了
  直接用字符串来表示所有*/
//这里需要用字符串表示整个状态当作点来搜
#include<bits/stdc++.h>
using namespace std;
string op[3]={"87654321","41236785","17245368"};
    //分别对应三种操作，不过是直接对字符串进行
    //这里很巧妙的记录，操作后的与原数组对应的编号
    //但需要注意我们记入都从0开始，要-1
string move(string s,int k){
//对字符串s进行操作k,并返回操作结果
    string ans;
    for(int i=0;i<8;i++)
        ans+=s[op[k][i]-'1'];
        //注意，字符串记录是从0开始，所以还要-1
    return ans;
}

#define op first//操作,pair成员不用担心跟上面的重
#define ne second//下个状态是啥
/*具体需要几个未知（虽然能算），
  且为了简化代码，就不用数组模拟队列了*/
map<string,int> dist;//来记录每个状态距离起点的距离
map<string,pair<char,string> > pre;
        //记录上一步操作和从哪个变化过来
//1e4左右其实用哈希会更好
int bfs(string st,string ed){
//搜到终点就能结束了，返回走了几步
    if(st==ed) return 0;
    //千万别忘了，一开始就是起点的情况
    //初始化
    queue<string> q;
    q.push(st);
    dist[st]=0;
    while(!q.empty()){
        auto t=q.front();
        q.pop();
        //三种延展形态（操作）
        for(int i=0;i<3;i++){
            string s=move(t,i);//表示整个状态
            //是否重复走过
            if(dist.count(s)) continue;
            dist[s]=dist[t]+1;
            pre[s]={'A'+i,t};//操作和上一个状态
            q.push(s);
            if(s==ed) return dist[s];
        }
    }
    return -1;
}
int main(){
    string  st,ed;//初始为""
    //开始状态
    for(int i=0;i<8;i++) st+=i+'1';
    //结束状态
    for(int i=0;i<8;i++){
        char c;cin>>c;
        ed+=c;
    }
    int step=bfs(st,ed);
    cout<<step<<endl;
    string ans;
    /*这回从起点搜终点，由于每个点只能记录上一个点
      所以从终点往回搜，然后再倒回去就是正常了*/
    while(st!=ed){
        ans+=pre[ed].op;
        ed=pre[ed].ne;
    }
    reverse(ans.begin(),ans.end());
    if(ans[0]) cout<<ans<<endl;
    //别忘了起点就是终点的特殊情况
    return 0;
}
```






# 双端队列广搜
## 电路维修
![双端队列1.jpg](https://cdn.acwing.com/media/article/image/2023/10/22/246003_44e12a9470-双端队列1.jpg) 
![双端队列2.jpg](https://cdn.acwing.com/media/article/image/2023/10/22/246003_8ce1203070-双端队列2.jpg) 

```c++
//由于边权只有0/1所以可以用双端队列广搜
        //各种性质的解释看证明
#include<bits/stdc++.h>
using namespace std;
typedef pair<int,int> pii;
#define x first
#define y second
const int N=510,M=510;
int r,c;
char g[N][M];

int dist[N][M];//到这个点路径长，计算所需的最小旋转次数
bool st[N][M];//判断这条路是否走过（用这个点更新）

//下面的，按从左上开始按顺时针顺序一个一个对应
char cs[]="\\/\\/";//表示四个方向所对应的理想线路
            //这个需要'\'需要转义字符
int dx[4]={-1,-1,1,1};//要到哪个点
int dy[4]={-1,1,1,-1};
int ix[4]={-1,-1,0,0};//要到的那个点所对应的电线
int iy[4]={-1,0,0,-1};
int bfs(){
    //这里根据性质优化，没有其实也能过
        //和为奇数的点是走不到的
    if((r+c)&1) return 1e9;
    //初始化  (多组数据)
    memset(dist,100,sizeof dist);
    memset(st,0,sizeof st);
    deque<pii> q;//双端队列
    q.push_back({0,0});
    dist[0][0]=0;//起点为0，0
    //开始搜
    while(!q.empty()){
        auto t=q.front();
        q.pop_front();
        int x=t.x,y=t.y;//简化一下代码
        if(st[x][y]) continue;//前面走过的，肯定比后面小（区分更新）
        // printf("\n%d %d %d:\n",x,y,dist[x][y]);
        if(x==r&&y==c) return dist[r][c];//找到了，就能提早结束了
        st[x][y]=1;
        for(int i=0;i<4;i++){
            int a=x+dx[i],b=y+dy[i];
            if(a<0||a>r||b<0||b>c) continue;
            int ca=x+ix[i],cb=y+iy[i];
            int w=(g[ca][cb]!=cs[i]);//当跟理想的不符是就要移动
            // printf("%d %d %d %d\n",a,b,dist[a][b],dist[x][y]+w);
            if(dist[x][y]+w<dist[a][b]){
                //注意这里后面更新的不一定比前面大
                dist[a][b]=dist[x][y]+w;
                
                if(w) q.push_back({a,b}); //如果是1插在后面
                else  q.push_front({a,b});//如果是0插在前面
            }
        }
    }
    return dist[r][c];
}
int main(){
    int T;cin>>T;
    while(T--){
        scanf("%d %d",&r,&c);
        for(int i=0;i<r;i++) scanf("%s",g[i]);
        int t=bfs();
        if(t>1e7) printf("NO SOLUTION\n");
        else      printf("%d\n",t);
    }
    return 0;
}
```











## ==双向广搜==

(两个队列的版本)

[**190. 字串变换 - AcWing题库:**](https://www.acwing.com/problem/content/description/192/)

已知有两个字串 $A$, $B$(长度最多为20) 及一组字串变换的规则（至多 $6$ 个规则）:

$A_1 \to B_1$

$A_2 \to B_2$

…

规则的含义为：在 $A$ 中的子串 $A_1$ 可以变换为 $B_1$、$A_2$ 可以变换为 $B_2…$。

例如：$A$＝`abcd` $B$＝`xyz`

变换规则为：

`abc` $\to$ `xu`
`ud` $\to$ `y`
`y` $\to$ `yz`

则此时，$A$ 可以经过一系列的变换变为 $B$，其变换的过程为：

`abcd` $\to$ `xud` $\to$ `xy` $\to$ `xyz`

共进行了三次变换，使得 $A$ 变换为 $B$。

注意，一次变换只能变换一个子串，例如 $A$＝`aa` $B$＝`bb`

变换规则为：

`a` $\to$ `b`

此时，不能将两个 `a` 在一步中全部转换为 `b`，而应当分两步完成。



---

### 详细分析:

#### 思路：
双向搜索，就是在起点搜索的过程，终点也在往回搜，从而达到优化的效果。
普通搜索：（绿色点为终点）

![](https://cdn.acwing.com/media/article/image/2022/11/06/109870_e1dbe5f05d-%E6%97%A0%E6%A0%87%E9%A2%98.png)

双向搜索：

![](https://cdn.acwing.com/media/article/image/2022/11/06/109870_eb7ea4c45d-.png)

#### **原则：需要一层层搜**,

二段性会影响答案

> 当存在二段性时
>
> ![一层层 (1).jpg](https://cdn.acwing.com/media/article/image/2024/02/17/246003_3e756ea4cd-一层层-(1).jpg)
>
> ![](https://img-blog.csdnimg.cn/6edc7cbc5ed9444580b127855200508e.png) 
>
> 假设你搜A，如果你先搜到了$y+1$，这时返回结果就是错的

#### **时间复杂度:**$O(2m^{n/2})$

> 每次有m种拓展（假设每次当前字符串，每个规则都刚好只有一种拓展），一共进行n步

​	如果是单向时间复杂度为$O(m^n)$

#### 优化场景：

- 再什么拓展，总数也就那样的，一般都不需要

  像搜地图，还是什么最短路，拓展的总数也就那么点，用不用影响都不大

- 拓展总数可能非常大时，就需要了

  

#### 自己的猜想：

（不确定有没有问题）

**大前提：都是一层一层搜**

- 两个方向相遇的点，会在彼此的队列中

- 同时相遇的步数，刚好为step（就是进行了几次拓展）



```java
//双向广搜
//需要一层层搜，去取消二段性
//小优化：每次先搜拓展数量小的
//特判：起点就是终点

//这里会有点不一样，如果没找到返回的是-1，不是一个很大的数
/*猜测(主要是不会证)：
  一切的大前提：都是一层一层搜
  两个方向相遇的点，会在彼此的队列中
  同时相遇的步数，刚好为step（就是进行了几次拓展）*/
import java.util.*;

public class Main{
	static final int N = 10;
	static String A, B;
  //规则的正方向：a->b
	static int n = 0;// 记录规则数
	static String[] a = new String[N];
	static String[] b = new String[N];

	/**
	 * extend函数说明： 
	 * 功能：拓展当前队列的这一层 返回值：如果找到返回总步数，没找到返回-1或者返回一个不可能的数 
	 * extend(当前方向的队列,
	 * 		  当前方向拓展的集合及其搜过来的步数,另一个方向拓展的集合及其搜过来的步数 
	 * 		  操作a,操作b) 
	 * 		 【当前方向对应的变换规则为a->b】
	 */
	static int extend(Queue<String> q, Map<String, Integer> da, Map<String, Integer> db, String[] a, String[] b) {
		int d = da.get(q.peek());// 当前层对应的步数
		while (!q.isEmpty() && da.get(q.peek()) == d) {
			String t = q.remove();
			for (int i = 0; i < n; ++i) {
				// 先不断地找子串等于a[i]的来替换成b[i]
				int k = t.indexOf(a[i]);
				while (k != -1) {
					String x = t.substring(0, k) + b[i] + t.substring(k + a[i].length());// 字串变换
										//这里我真的服啦，a[i]和b[i]的大小不一定相同
					k=t.indexOf(a[i], k+1);//这一步千万别忘了，一定要往下走
					
					if (db.containsKey(x))
						return d + db.get(x) + 1;// 当前拓展找到了
					if (da.containsKey(x))
						continue;// 前面已经找到了
					// 没找到的情况
					da.put(x, d + 1);
					q.add(x);
				}
			}
		}
		return -1;
	}

	static int bfs() {// 返回步数，超过就返回-1
		if (A.equals(B))
			return 0;// 搜索一般都是特判下是否起点就是终点
		// 初始化
		Queue<String> qa = new LinkedList<>();// 正向当前层要搜的
		Queue<String> qb = new LinkedList<>();// 反向
		Map<String, Integer> da = new TreeMap<>();// 正向变过来这个字符串集合以及其步数
		Map<String, Integer> db = new TreeMap<>();// 反向
		qa.add(A);
		da.put(A, 0);
		qb.add(B);
		db.put(B, 0);
		// 双向搜
		// 当前循环具体搜哪个看的是数量小
		int step = 0;
		while (!qa.isEmpty() && !qb.isEmpty()) {
			int t;
			if (qa.size() < qb.size())
			    t = extend(qa, da, db, a, b);
			else
			    t = extend(qb, db, da, b, a);
			
			if (t != -1)
				return t;
			if (++step > 10)//运用猜测的性质，就是step几步，就是表示具体
				return t;
		}
		return -1;
	}

	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		A = sc.next();
		B = sc.next();
		while (sc.hasNext()) {
			a[n] = sc.next();
			b[n] = sc.next();
			++n;
		}
		int res = bfs();
		if (res != -1)
			System.out.println(res);
		else
			System.out.println("NO ANSWER!");

	}
}
```

**注意：**（唉，全是写出来的bug）

1. 就是**查找字符串时，`pos`一定要注意移动**
1. 然后需要注意题目中的变换规则对应的字符串是不一定相同的



参考：

[参考代码](https://www.acwing.com/activity/content/code/content/2847595/)

[更深理解双向广搜](https://blog.csdn.net/weixin_72060925/article/details/128994885?ops_request_misc=%7B%22request%5Fid%22%3A%22170787862816800213093343%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fblog.%22%7D&request_id=170787862816800213093343&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-1-128994885-null-null.nonecase&utm_term=双向&spm=1018.2226.3001.4450)

[图片来自的题解](https://www.acwing.com/solution/content/147506/)

###  不断查找字符串来替换

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



## A*算法

估值函数：一定是需要满足小于实际情况

> 设计：
>
> 1. 哈曼顿距离（场景：上下左右四个方向移动）:
>
>    ​	$$f(i)=abs(i.x-ed.x)+abs(i.y-ed.y)$$
>
> 2. 对角线距离（场景：8个方向移动）
>
>    ​	$$f(i)=max(abs(i.x-ed.x),abs(i.y-ed.y))$$
>
> 3. 实际距离（场景：任意方向）
>
>    ​	$$f(i)=\sqrt{(i.x-ed.x)^2+(i.y-ed.y)^2}$$
>
> 4. 最短路

![Astar算法.jpg](https://cdn.acwing.com/media/article/image/2024/02/19/246003_edaf5052ce-Astar算法.jpg) 
![注意.jpg](https://cdn.acwing.com/media/article/image/2024/02/19/246003_08f60656ce-注意.jpg) 







**输出路径**：需要能查询当前点对应的上一个点【key-value:能查询当前点就是将这个点当成键，上一个点和其它答案需要信息就是值】

> 如果只是坐标的话，用数组就能表示出来
>
> 但是如果字符串或者数字很大等等其他东西，数组不一定能表示，**这时需要使用字典数据结构**



资料：《算法竞赛》这本书对这个算法的描述写得挺好（可以在看看）

参考：题解和oi-wiki

----

### *结点表示特殊&输出路径&一二维的转换

[179. 八数码 - AcWing题库](https://www.acwing.com/problem/content/181/)

在一个 $3×3$ 的网格中，$1 \sim 8$ 这 $8$ 个数字和一个 `x`
恰好不重不漏地分布在这 $3×3$ 的网格中。

例如：

    1 2 3
    x 4 6
    7 5 8

在游戏过程中，可以把 `x`
与其上、下、左、右四个方向之一的数字交换（如果存在）。

我们的目的是通过交换，使得网格变为如下排列（称为正确排列）：

    1 2 3
    4 5 6
    7 8 x

例如，示例中图形就可以通过让 `x`
先后与右、下、右三个方向的数字交换成功得到正确排列。

交换过程如下：

    1 2 3   1 2 3   1 2 3   1 2 3
    x 4 6   4 x 6   4 5 6   4 5 6
    7 5 8   7 5 8   7 x 8   7 8 x

把 `x` 与上下左右方向数字交换的行动记录为 `u`、`d`、`l`、`r`。

现在，给你一个初始网格，请你通过最少的移动次数，得到正确排列。(结果不唯一)

----

(下面那一堆分析，**都要好好看看**)

![八数码无解.jpg](https://cdn.acwing.com/media/article/image/2024/02/19/246003_2f07ef96ce-八数码无解.jpg) 

```java
//A*算法
	//八数码本身需要：用字符串表示整个状态;一维二维相互转换
/**
 * 本质就是起点最优(dijkstra)+终点最优(贪心最优搜索)的结合
 * A*算法要保证有解，不然复杂度还更高
 * 	(传统的bfs就是肆无忌惮的拓展，
 * 	会有特别的无效点，主要问题就是如何尽可能的去掉无效点)
 * 【符号表示  s:起点;x:当前点;e:终点;
 * 			  f(x):当前点到终点的估值函数;
 * 			  d(i->j):i到j的实际距离】
 * 大大前提：f(x)<=d(x->e)
 * 估值结果=实际（s->x）+估计(x->e)=d(s->x)+f(x)
 * 估值函数f(x)的编写：（这里也想了好久）
 * 		八数码能移动的只有四个方向，所以肯定是用曼哈顿距离
 * 		接下来就是要保证小于实际距离：
 * 			每个数字最终的目标就是回到原位，手段是靠上下左右移动
 * 			而这个数字要想移动，前面就必须要有空格，只能用空格来铺路
 * 			空格最多就只能让数字更近一步，这时空格来到了数字后面，空格还需在来到当前数字的前面
 * 			每个数字要回去，最终消耗肯定大于曼哈顿距离
 * 			（直接这么理解：就是数字要想回去，就必须用空格来铺路，最少最少都要移动曼哈顿距离）
 * 		所以当前点好终点距离的f(x)=所有数字回到原位的曼哈顿距离
 * 			(空格这里不管，全部回到原位了，自然就回去了)
 */
import java.util.*;
class Node{//搜的优先队列
	int d;//起点到终点的估计距离
	String s;//当前状态
	Node(int d,String s){
		this.d=d;
		this.s=s;
	}
	/*法一：//这里用法2：用匿名函数来初始化堆
	@Override
	public int compareTo(Node o) {//快捷键alt+s
		return d-o.d;//-可以助记为小于，java优先队列默认位小根堆
	}*/
}
public class Main{
	//A*算法
	static int f(String x) {//当前点到终点的估值函数
		int res=0;
		char[] cs=x.toCharArray();  
		//直接一维算了
		for(int i=0;i<cs.length;++i) {
			if(cs[i]=='x') continue;
			int t=cs[i]-'1';//从1开始的
			res+=Math.abs(i/3-t/3)+Math.abs(i%3-t%3);//先算行，在列
		}
		return res;
	}
	static String swap(String x,int i,int j) {//交换字符串i和j字符的位置
		StringBuilder y=new StringBuilder(x);
		char t=y.charAt(i);
		y.setCharAt(i,y.charAt(j));
		y.setCharAt(j, t);
		return y.toString();
	}
	/**
	 * A*算法
	 * @param st
	 * @return 路径
	 */
	static String bfs(String st) {
		//偏量法及其对应的四个方向
		int[] dx= {-1,0,1,0};
		int[] dy= {0,1,0,-1};
		String[] op= {"u","r","d","l"};
		
		Map<String,Integer> d=new TreeMap<>();//用当前字符串查询与起点的实际距离
		Map<String,String[]> pre=new TreeMap<>();//用当前字符串查询上一个点，及其变化而来的方向
		
		Queue<Node> q=new PriorityQueue<>((a,b)->{
			return a.d-b.d;//默认就是小根堆，也就是:前d<后d
		});
		String end="12345678x";
		
		q.add(new Node(f(st),st));
		d.put(st,0);
		while(!q.isEmpty()) {
			String t=q.remove().s;//当前字符串
			if(end.equals(t)) break;//只有搜到终点的时候还是最小的
			int step=d.get(t);//肯定是存在的
			int k=t.indexOf('x');
			int tx=k/3,ty=k%3;
			for(int i=0;i<4;++i) {
				int x=tx+dx[i],y=ty+dy[i];
				if(x<0||x>=3||y<0||y>=3) continue;//越界了
				String cur=swap(t,x*3+y,k);
				//step+1就是走到当前的距离
				if(d.containsKey(cur)&&d.get(cur)<=step+1) continue;//当没有比之前走的小
				q.add(new Node(step+1+f(cur),cur));
				d.put(cur,step+1);
				pre.put(cur,new String[]{t,op[i]});//注意这个数据的初始话方式
			}
		}
		//从终点开始搜
		StringBuilder res=new StringBuilder();//由于需要翻转用这个
		while(!end.equals(st)) {
			String[] t=pre.get(end);
			res.append(t[1]);
			end=t[0];
		}
		res.reverse();
		return res.toString();
	}
	
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		String st="";
		for(int i=0;i<9;++i) 
			st+=sc.next();
		char[] cs=st.replace("x","").toCharArray();//去掉空格的st
		int cnt=0;//统计有多少逆序对
		for(int i=1;i<8;++i) 
			for(int j=0;j<i;++j) 
				if(cs[i]<cs[j]) ++cnt;
		if((cnt&1)==1) //如果为奇数就是无解
			System.out.println("unsolvable");
		else 
			System.out.println(bfs(st));
	}
}
```



### [第K短路](https://www.acwing.com/problem/content/description/180/)

给定一张 $N$ 个点（编号 $1,2……N$），$M$ 条边的有向图，求从起点 $S$ 到终点 $T$ 的第 $K$ 短路的长度，路径允许重复经过点或边。
**注意：** 每条最短路中至少要包含一条边。

---

优化：每个点走一次其实就是表示新生成了一条路径，第k短路，仅仅需要k条路径,后面的就不需要了(oi-wiki)。**所以每个点走了k次后就可以不入队了** 
```java
/**
 * A*算法：
 * 	本题的拓展特别多，如果你用像什么最短路算法，那样仅针对起点，肯定不行
 * 	因此使用A*算法针对起点和终点，来减少没用的拓展
 * 	
 * 	估计函数:从当前点到终点的估值
 * 		原则：小于等于实际的距离
 * 		这时可以用dijkstra,把终点作为搜起点，最后一定是小于等于实际值的
 * 
 * 	优化：每个点走一次其实就是表示新生成了一条路径，
 * 		  第k短路，仅仅需要k条路径,后面的就不需要了(oi-wiki)
 * 		  所以每个点走了k次后就可以不入队了 
 * 
 * 	只要是搜索，都要想想起点等于终点的情况
 */
import java.util.*;
public class Main{
		//拿最大值，算一下都不会爆int
	static int N=(int)1e3+10,M=(int)2e4+10;//还需要存反向的边
	static int n,m,S,T,K;
	//邻接表存边
	/*	h正向，rh逆向,存的是当前点的最后一条边,-1表示没有边
	 * 			rh用从终点往前搜
	 * 	ne表示同起点的上一条边
	 * 	e表示当前边的终点
	 * 	w表示边权
	 * 	idx下标分配器
	 * 
	 * 注意下：哪些存边，哪些存点
	 * */
	static int[] h=new int[N],rh=new int[N],e=new int[M],ne=new int[M],w=new int[M];
	static int idx;
	static void init() {
		//邻接表的
		Arrays.fill(h,-1);
		Arrays.fill(rh,-1);
		//最短路的
			//st默认为false
		Arrays.fill(dist,(int)1e9);
	}
	static void add(int[] h,int x,int y,int z) {//需要把表头信息传一下
		ne[idx]=h[x];w[idx]=z;e[idx]=y;h[x]=idx++;
	}
	//dijkstra初始化估值函数
	static int[] dist=new int[N];//由终点出发的最短路，同时也是估值函数
	static boolean[] st=new boolean[N];//判断这个点是否搜过
	static void dijkstra() {
		//int[]:存起点到这个点的距离,这个点
		Queue<int[]> q=new PriorityQueue<>((x,y)-> {
			return x[0]-y[0];//默认小根堆,让权值小的在前
		});
		dist[T]=0;
		q.add(new int[] {0,T});
		while(!q.isEmpty()) {
			int t=q.remove()[1];//只需要用到这个点
			if(st[t]) continue;//搜过了没必要搜了
			st[t]=true;
			for(int i=rh[t];i!=-1;i=ne[i]) {//搜以这个点为起点的所有边
				int d=dist[t]+w[i],y=e[i];
				if(dist[y]>d) {
					dist[y]=d;
					q.add(new int[] {d,y});
				}
			}
		}
	}
	//A*算法  从起点开始
	static int[] cnt=new int[N];//优化：用来记录当前点的遍历次数
	static int astar() {
		//int[]:预测的结果（实际(st->i)+估值(i->ed)）,实际(st->i),当前点i
		Queue<int[]> q=new PriorityQueue<>((x,y)->{
			return x[0]-y[0];
		});
		q.add(new int[] {dist[S],0,S});
		while(!q.isEmpty()) {
			int[] tmp=q.remove();
			int td=tmp[1],t=tmp[2];//t表示当前点，td表示起点到当前的实际距离
			++cnt[t];
			if(t==T&&cnt[t]==K) return td;
			//正向的
			for(int i=h[t];i!=-1;i=ne[i]) {
				int y=e[i];
				if(cnt[y]>K) continue;//这个点已经生成了k条路
				q.add(new int[] {td+w[i]+dist[y],td+w[i],y});
			}	
		}
		return -1;//没有找到的情况
	}
	public static void main(String[] args) {
		Scanner sc=new Scanner(System.in);
		init();
		n=sc.nextInt();m=sc.nextInt();
		for(int i=0;i<m;++i) {
			int x=sc.nextInt(),y=sc.nextInt(),z=sc.nextInt();
			//有向边
			add(h,x,y,z);
			add(rh,y,x,z);//反向存边
		}
		S=sc.nextInt();T=sc.nextInt();K=sc.nextInt();
		if(S==T) ++K;//题目说了，每条最短路至少包含一条边，所以需要特判
		dijkstra();//从终点开始搜，构造估值函数
		System.out.println(astar());
	}
}
```


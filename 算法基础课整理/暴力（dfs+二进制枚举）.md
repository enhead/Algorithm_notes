# 暴力（`dfs`+二进制枚举）

## 暴力模板：`dfs`模板和二进制

### AcWing 92. 递归实现指数型枚举（组合）

从 $1∼n$这$n$个整数中随机选取任意多个，输出所有可能的选择方案。

输出必须是升序的



----

`dfs`模板“选与不选”和“每个位置上有多个选择”的模板，本质上其实都是一样的

这里用来标记是否走过，其实可以不用数组，用一串二进制数字就行，0表示有，1表示没有
（但是这个其实没必要，不差这点空间）

变化只要体现在`dfs`的实参上就行

#### 1. 选与不选的模板

```c++
#include<bits/stdc++.h>
using namespace std;
int n;
bool op[20];
void dfs(int x){//选与不选模板
    if(x>n){
        for(int i=1;i<=n;i++){
            if(op[i]){
                cout<<i<<" ";
            }
        }
        cout<<endl;
        return;
    }
    //选
    op[x]=1;
    dfs(x+1);
    //不选
    op[x]=0;//用完一般都要记得恢复
    dfs(x+1);
    
}
int main(){
    cin>>n;
    dfs(1);
    
    return 0;
}
```



----------

#### 2. for循环模板(每个位置上有多种可能的模板)

```c++
#include<bits/stdc++.h>
using namespace std;
int n;
int f[20];
bool had[20];
void dfs(int x){
    //有问题的点在于输出
    //没一个位置上有多种可能
    for(int j=0;j<x;j++){
        cout<<f[j]<<" ";
    }
    cout<<endl;
    if(f[x-1]>n){
        return;
    }
    for(int j=f[x-1]+1;j<=n;j++){//优化，前面已经选过了
        // if(had[j]==0){
            f[x]=j;
            had[j]=1;
            dfs(x+1);
            had[j]=0;
        // }
        
    }
}
int main(){
    cin>>n;
    dfs(0);
    return 0;
}
```

#### 3. 二维的情况

一般是`dfs(x,y,k)`表示走到（x,y）这个位置选了k个（一般从`(1,1)`开始）

- **一般是一行一行走，列走完才换行**
- 同时不要忘记标记已经选过的
  - 看剩下几个就转为一维$n*m-(m*(x-1)+(y-1))$（模拟一下就能算出来的）



##### 例题：[托米航空公司](https://ac.nowcoder.com/acm/contest/111/C)

托米家的飞机每排有M个座位，有N排座位。因此座椅形成了M × N的网格（忽略过道）,公司为每次航班都出售K张票。
座位必须遵守以下规则：座位被占用时，座位正前方和座位后方的座位以及当前座位左边和右边必须是空的
有多少种可能,由于这个数字可能非常大，我们只求它对420047取模的结果。



----

```java
import java.util.*;
import java.io.*;
public class Main{
    static int n,m,k;
    static final int mod=420047;
    static int ans;
    static boolean[][] had=new boolean[90][90];
    //选和不选的模板    
        //这个一行一行走
    static void dfs(int x,int y,int cnt){
    //到达(x,y)这个位置，选了个
        if(cnt==k) {
            ans=(ans+1)%mod;
            /*for(int i=1;i<=n;++i){
                for(int j=1;j<=m;++j){
                    System.out.print((had[i][j]?"1":"0")+" ");
                }System.out.println();
            }
            System.out.println();*/
            return;
        }
        if(y>m){//换行
            ++x;
            y=1;
        }
        if(x>n||y>m) return;
        if(n*m-(m*(x-1)+y-1)<k-cnt) return;//k只有4感觉剪掉不够的情况不是很有必要
        
        //选上，但是要看能不能选
        if(!had[x][y-1]&&!had[x-1][y]){//因为是一行一行看么，所以这样就行
            had[x][y]=true;
            dfs(x,y+1,cnt+1);//只有列加
        }
        //恢复现场
        had[x][y]=false;
        //不选
        dfs(x,y+1,cnt);
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);
        int t=sc.nextInt();
        while(t-- >0){
            //重新初始化
            n=sc.nextInt();
            m=sc.nextInt();
            k=sc.nextInt();
            ans=0;
            dfs(1,1,0);
            System.out.println(ans);
        }
    }
}
```





#### 二进制枚举：

```c++
#include<bits/stdc++.h>
using namespace std;
int main(){
    int n;cin>>n;
    //二进制枚举
    for(int i=0;i<(1<<n);i++){
        for(int j=0;j<n&&i>=(1<<j);j++){//小小优化
            if(i>>j&1) printf("%d ",j+1);
        }puts("");
    }
    return 0;
}
```

### AcWing 94. 递归实现排列型枚举

把 $1 \sim n$ 这 $n$ 个整数排成一行后随机打乱顺序，输出所有可能的次序。

---



 **像这种乱序的，也只能用`for`循环的模板了** (就是还跟顺序有关的话)

![](https://cdn.acwing.com/media/article/image/2021/02/20/55289_0cd4222d73-%E6%B7%B1%E5%BA%A6%E4%BC%98%E5%85%88%E9%81%8D%E5%8E%86.png)

```c++
#include<bits/stdc++.h>
using namespace std;
bool had[15]={0};
int f[15];
int n;
void dfs(int x){//for循环模板
//每个位置上有
    
    if(x==n){
        for(int i=0;i<n;i++){
            cout<<f[i]<<" ";
        }
        cout<<endl;
        return;
    }
    for(int i=1;i<=n;i++){
        if(!had[i]){
            had[i]=1;
            f[x]=i;
            dfs(x+1);
            had[i]=0;
        }
    }
    
    
}
int main(){
    cin>>n;
    dfs(0);
    return 0;
}
```

参考：[画图详解](https://www.acwing.com/solution/content/44647/)

## AcWing 95. 费解的开关

题目：

你玩过“拉灯”游戏吗？

$25$ 盏灯排成一个 $5×5$ 的方形。

每一个灯都有一个开关，游戏者可以改变它的状态。

每一步，游戏者可以改变某一个灯的状态。

游戏者改变一个灯的状态会产生连锁反应：和这个灯上下左右相邻的灯也要相应地改变其状态。

我们用数字 1 表示一盏开着的灯，用数字 0 表示关着的灯。

下面这种状态

```
10111
01101
10111
10000
11011
```

在改变了最左上角的灯的状态后将变成：

```
01111
11101
10111
10000
11011
```

再改变它正中间的灯后状态将变成：

```
01111
11001
11001
10100
11011
```

给定一些游戏的初始状态，编写程序判断游戏者是否可能在 6 步以内使所有的灯都变亮。



----

#### 思路（二进制枚举）：一行一行处理，每一行的状态由上一行决定，因此只需枚举第一行即可解决

```c++
#include<bits/stdc++.h>
using namespace std;
char f[6][6];
#define for(i,a,b) for(int i=a;i<=b;i++)
//变量的位移用数组存储
int mx[5]={-1,0,0,0,1};
int my[5]={0,-1,0,1,0};
void turn(int x,int y){//变亮操作
    for(i,0,4){
        f[x+mx[i]][y+my[i]]^=1;
            //由于在ASCII码中'0'=48,'1'=49
            //刚好在二进制中就各位不同
            //所以只要当前位置上与1取异或可互相转换
    }
}
int main(){
    int n;
    cin>>n;
    while(n--){
        int ans=1e9;
        for(i,1,5){
            for(j,1,5){
                //为了避免边界问题，从1，1开始
                cin>>f[i][j];
            }
        }
        for(op,0,31){//枚举第一行点灯的所有可能
            int step=0;
            //二进制版枚举第一行
            char backup[6][6];//备份
            memcpy(backup,f,sizeof(backup));
            for(k,0,4){
                if(op>>k&1){//位运算左右移优先级更高
                    //若为该二进制上为1，就代表选
                    step++;
                    turn(1,k+1);//k记得加1，边界
                }
            }
            //由于后面的行的操作都是靠上一行决定的
            //接下来对剩下的四行进行操作
            for(i,2,5){
                for(j,1,5){
                    //位置正对上的那个位是按的才点
                    if(f[i-1][j]=='0'){
                        step++;
                        turn(i,j);
                    }
                }
            }
            //对操作过最后一行进行特判即可
            //因为我们，每一行都会上一行所有暗的点亮
            //只有最后一排不行，找暗0的就行
            bool dark=0;
            for(j,1,5){
                if(f[5][j]=='0'){
                    dark=1;
                    break;
                }
            }
            if(!dark){
                ans=min(ans,step);
            }
            memcpy(f,backup,sizeof(f));
        }           
        if(ans>6){
            ans=-1;
        }
        cout<<ans<<endl;        
    } 
    return 0;
}
```




# treap树堆（平衡树）

## 能完成的操作：

对应题目：[253. 普通平衡树 - AcWing题库](https://www.acwing.com/problem/content/description/255/)

1.  插入数值 x。
2.  删除数值 x(若有多个相同的数，应只删除一个)。
3.  查询数值 x 的排名(若有多个相同的数，应输出最小的排名)。
4.  查询排名为 x 的数值。
5.  求数值 x 的前驱(前驱定义为小于 x 的最大的数)。
6.  求数值 x 的后继(后继定义为大于 x 的最小的数)。

**注意：** 数据保证查询的结果一定存在。

----

## 树堆`treap`



![BST.jpg](https://cdn.acwing.com/media/article/image/2024/02/07/246003_5856bd9fc5-BST.jpg) 

![treap.jpg](https://cdn.acwing.com/media/article/image/2024/02/07/246003_d98461b0c5-treap.jpg)  ![唯一性.jpg](https://cdn.acwing.com/media/article/image/2024/02/07/246003_de1d99fbc5-唯一性.jpg) 

**易错点：**

- **小心左右旋然后根节点的更新**

  - `java`没有显性的引用，**那个根的更新只能用返回值 **

    **都要返回`p`,调用时让`当前根=函数(当前根)`**

    原本想用`AtomicInteger`这个对象来莽一下引用，但是**他是递归**，太麻烦了

    （唉，java写算法确实麻烦好多啊,本来就挺难的）

    ==**涉及到左旋右旋包括插入删除:**==

    1. 函数都要返回`p`
    2. 调用时让`当前根=函数(当前根,~);`
    3. 最后一定要`pushup`

- 树的结构发生改变最后都要`pushup`，不难点个个数会不准

- `java`的那个**随机数是要指定范围的**，不然是会爆空间的

- `java`建树：要建`tr[0]`这个点

- 每次都是没处理空结点

- 哎呀呀，**递归的时候名字不要写错了**

### 1. `java`版

（写得要吐了,能踩的坑大部分都踩了一遍）

```java
//树堆treap   =>(左旋右旋版)+(大根堆版+BST二叉搜索树)
    //treap树的形态是唯一的（看笔记《算法竞赛》那里的比较直观）
    /*（当时我不太明白为什么这个能称为平衡树，都是说概率期望能成）
        （这里也只是直观理解一下）
        treap的左右高度其实是说不准
        但是他能尽可能的避免退化成链表的情况：就是有序序列一个个插入情况
        就是你加一个随机的优先级，那他变成随机的堆
            这样退化的概率会特别小
        (哎呀，感觉这个数据结构，还挺看脸的)*/
        //（这里会写的比较详细，每种函数对应着笔记看吧）
import java.util.*;
import java.io.*;
class Node{//tr[0]表示空结点 =>0指向空
    int l,r;//左右孩子
    int key,val;//实际值 和 堆的优先级看这个随机数
    int cnt,size;//相同的值有几个点 和 这棵树总共的点树 (用来算排名)
    Node(int l,int r,int k,int v,int c,int s){
        this.l=l;
        this.r=r;
        key=k;
        val=v;
        cnt=c;
        size=s;
    }
    Node(){}
}
public class Main{
    static final int N=(int)1e5+10,INF=(int)1e8;//那个INF是最值
    static Node[] tr=new Node[N];
    static int root,idx;//现在的根结点 和 结点分配器  （用整数表示指针）
    static Random random=new Random();//试了下，java每次随机序列都不一样
    static int get_node(int key){//创建新的结点，并返回下标
        tr[++idx]=new Node(0,0,key,random.nextInt(N),1,1);//注意是++idx,下标为0表示空
                        //我真的受不了，这个N不指定是会爆空间的
        /*c++:
        随机序列如果你不加随机种子，就是伪随机
        srand(time(0));//加个这句话
        val=rand();//这个函数是用来生成正int之间的随机数的*/
        return idx;
    }
    
    static void pushup(int p){//两儿子的点合并
        //会改变树的状态的（除了查询）都要
        tr[p].size=tr[tr[p].l].size+tr[tr[p].r].size+tr[p].cnt;//两儿子点+自己的点
    }
    /*左右旋肯定能满足二叉搜索树的特性；
        这里还有一个作用：充当堆的下坠和上移操作，让这颗BST也满足堆*/
        
    
    //c++引用：需要更新值的或者当作返回值
        //根结点可能变的都用引用：左旋右旋插入删除
    //注意：哎呀，这里用的是java,所以只能用返回值了
    
        
    /*助记：先记录下旋上去成根的儿子，然后一个一个随后更新
            左儿子大，右旋上去(左儿子的val值比根的val大，不满足大根堆)
            右儿子大，左旋上去*/
    static int zip(int p){//右旋
    //同时会更新的根节点
        int q=tr[p].l;
        tr[p].l=tr[q].r;
        tr[q].r=p;
        pushup(p);pushup(q);
        return q;//q成为新的根
    }
    static int zap(int p){//左旋
        int q=tr[p].r;
        tr[p].r=tr[q].l;
        tr[q].l=p;
        pushup(p);pushup(q);
        return q;
    }
    
    static void build(){//建树
        tr[0]=new Node();//空结点
        //一开始就只有两个虚拟结点，最大值和最小值避免一些特判情况
        get_node(-INF);get_node(INF);
            //手动建一下
        root=1;tr[1].r=2;//先假设一下这颗treap是这样的
                    //不一定，满足了平衡树还要满足堆
        if(tr[2].val>tr[1].val)//如果不满足堆的性质：右儿子大，左旋
            root=zap(root);//注意：要传根，需要更新
    }
    
    
    static int insert(int p,int key){//插入
        //找对应的位置，如果不存在会出现在叶子结点，这时用左右旋上移
            //说明：插入操作结束后，插入的子树的根是会改变的
        if(p==0) return get_node(key);//没有先创建
        else if(tr[p].key==key) ++tr[p].cnt;//本来就有
        else if(tr[p].key>key){//在左儿子
            tr[p].l=insert(tr[p].l,key);//左儿子插入
            //检查一下是否符合堆
            if(tr[tr[p].l].val>tr[p].val) p=zip(p);//左儿子大了
        }else{//在右儿子那
            tr[p].r=insert(tr[p].r,key);
            if(tr[tr[p].r].val>tr[p].val) p=zap(p);
        }
        pushup(p);
        return p;
    }
    static int remove(int p,int key){//删除结点
        //将需要删除的点通过左右旋，下坠到叶子结点
        if(p==0) return 0;//不存在这个点,就不用动它了
        else if(tr[p].key==key){//找到当前结点
            if(tr[p].cnt>1) --tr[p].cnt;
            else if(tr[p].l!=0||tr[p].r!=0){//不是叶子结点，要转为叶子结点
                if(tr[p].r==0||tr[tr[p].l].val>tr[tr[p].r].val){//只有左结点或者左结点的权值比右大
                    p=zip(p);//让左结点成为根
                    //这时候待删的点变为了右儿子
                        //变成在右子树上删掉这个点，右子树的根可能变
                    tr[p].r=remove(tr[p].r,key);
                }else{//根结点变左儿子（对称一下就行）
                    p=zap(p);
                    tr[p].l=remove(tr[p].l,key);
                }
            }else return 0;//如果为叶子结点，指向空
        }else if(tr[p].key>key) //在左儿子那里
            tr[p].l=remove(tr[p].l,key);
        else//在右儿子那
            tr[p].r=remove(tr[p].r,key);
            
        //改完别忘记合并了
        pushup(p);
        return p;
        
    }
    
    static int get_rank_by_key(int p,int key){//根据数值获取排名
        if(p==0) return -1;//不存在这个排名（本题没有这种情况）
        else if(tr[p].key==key) return tr[tr[p].l].size+1;//就是当前结点，然后左子树都是小于这个结点的
        else if(tr[p].key>key)  return get_rank_by_key(tr[p].l,key);//在左儿子那边
        //最后一种在右儿子的情况
        return tr[tr[p].l].size+tr[p].cnt+get_rank_by_key(tr[p].r,key);
    }
    
    static int get_key_by_rank(int p,int rank){//根据排名找数值
        if(p==0) return INF;//排名太大了，没有这个值（本题不会出现）
        else if(tr[tr[p].l].size>=rank) //在左儿子那
            return get_key_by_rank(tr[p].l,rank);
        else if(tr[tr[p].l].size+tr[p].cnt>=rank)//就是当前结点
            return tr[p].key;
        //在右儿子那 
            //注意：别忘记减去前面的排名
        return get_key_by_rank(tr[p].r,rank-tr[tr[p].l].size-tr[p].cnt);
        
    }
    /*部分对称操作：
        	左右旋：左变右就行了,其他都不动
       全部对称操作：
       		找前后驱：全部都是反的
        */
        
    //找前驱
    static int get_prev(int p,int key){ // 找到严格小于key的最大数
        if(p==0) return -INF;//不存在
        else if(tr[p].key>=key) return get_prev(tr[p].l,key);//只能在左子树上
        return Math.max(tr[p].key,get_prev(tr[p].r,key));
            //当前结点已经小于但是还需要看看右结点
    } 
    //找后继
    static int get_next(int p,int key){  // 找到严格大于key的最小数
        if(p==0) return INF;//不存在
        else if(tr[p].key<=key) return get_next(tr[p].r,key);
        return Math.min(tr[p].key,get_next(tr[p].l,key));
    }
    public static void main(String[] args)throws Exception{
        BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
        int n=Integer.parseInt(br.readLine()),op,x;
        
        build();
        while(n-- >0){
            String[] ss=br.readLine().split(" ");
            op=Integer.parseInt(ss[0]);
            x=Integer.parseInt(ss[1]);
            //本题保证一定有结果，不会出现空结点的情况，这里的查询都不需要特判
                //注意：那两个虚拟结点
            if(op==1)       root=insert(root,x);
            else if(op==2)  root=remove(root,x);
            else if(op==3)  System.out.println(get_rank_by_key(root,x)-1);
                                //注意：有一个最小值的虚拟结点在前面
            else if(op==4)  System.out.println(get_key_by_rank(root,x+1));
                                //注意：有一个最小值的虚拟结点在前面
            else if(op==5)  System.out.println(get_prev(root,x));
            else            System.out.println(get_next(root,x));
        }
    }
}
//啊，真的很难写啊
    //哇靠，对照法找bug都找半天，呜呜，能踩的坑全部踩一遍了
        //真想不到连随机数那里都有坑
```

> 太恐怖了，真的不想写这个

参考做法：

写得比较接近y总：[题解2](https://www.acwing.com/activity/content/code/content/283177/)

`无旋树堆  java版`（`fhq treap`）:[题解2](https://www.acwing.com/activity/content/code/content/493760/)（java版没引用也特别难写）

别人家的论文：[1](https://byvoid.com/attachments/wp/2010/12/treap-analysis-and-application.pdf)

### 2. c++版：（by--yxc）

```c++
#include <cstdio>
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 100010, INF = 1e8;

int n;
struct Node
{
    int l, r;
    int key, val;
    int cnt, size;
}tr[N];

int root, idx;

void pushup(int p)
{
    tr[p].size = tr[tr[p].l].size + tr[tr[p].r].size + tr[p].cnt;
}

int get_node(int key)
{
    tr[ ++ idx].key = key;
    tr[idx].val = rand();
    tr[idx].cnt = tr[idx].size = 1;
    return idx;
}

void zig(int &p)    // 右旋
{
    int q = tr[p].l;
    tr[p].l = tr[q].r, tr[q].r = p, p = q;
    pushup(tr[p].r), pushup(p);
}

void zag(int &p)    // 左旋
{
    int q = tr[p].r;
    tr[p].r = tr[q].l, tr[q].l = p, p = q;
    pushup(tr[p].l), pushup(p);
}

void build()
{
    get_node(-INF), get_node(INF);
    root = 1, tr[1].r = 2;
    pushup(root);

    if (tr[1].val < tr[2].val) zag(root);
}


void insert(int &p, int key)
{
    if (!p) p = get_node(key);
    else if (tr[p].key == key) tr[p].cnt ++ ;
    else if (tr[p].key > key)
    {
        insert(tr[p].l, key);
        if (tr[tr[p].l].val > tr[p].val) zig(p);
    }
    else
    {
        insert(tr[p].r, key);
        if (tr[tr[p].r].val > tr[p].val) zag(p);
    }
    pushup(p);
}

void remove(int &p, int key)
{
    if (!p) return;
    if (tr[p].key == key)
    {
        if (tr[p].cnt > 1) tr[p].cnt -- ;
        else if (tr[p].l || tr[p].r)
        {
            if (!tr[p].r || tr[tr[p].l].val > tr[tr[p].r].val)
            {
                zig(p);
                remove(tr[p].r, key);
            }
            else
            {
                zag(p);
                remove(tr[p].l, key);
            }
        }
        else p = 0;
    }
    else if (tr[p].key > key) remove(tr[p].l, key);
    else remove(tr[p].r, key);

    pushup(p);
}

int get_rank_by_key(int p, int key)    // 通过数值找排名
{
    if (!p) return 0;   // 本题中不会发生此情况
    if (tr[p].key == key) return tr[tr[p].l].size + 1;
    if (tr[p].key > key) return get_rank_by_key(tr[p].l, key);
    return tr[tr[p].l].size + tr[p].cnt + get_rank_by_key(tr[p].r, key);
}

int get_key_by_rank(int p, int rank)   // 通过排名找数值
{
    if (!p) return INF;     // 本题中不会发生此情况
    if (tr[tr[p].l].size >= rank) return get_key_by_rank(tr[p].l, rank);
    if (tr[tr[p].l].size + tr[p].cnt >= rank) return tr[p].key;
    return get_key_by_rank(tr[p].r, rank - tr[tr[p].l].size - tr[p].cnt);
}

int get_prev(int p, int key)   // 找到严格小于key的最大数
{
    if (!p) return -INF;
    if (tr[p].key >= key) return get_prev(tr[p].l, key);
    return max(tr[p].key, get_prev(tr[p].r, key));
}

int get_next(int p, int key)    // 找到严格大于key的最小数
{
    if (!p) return INF;
    if (tr[p].key <= key) return get_next(tr[p].r, key);
    return min(tr[p].key, get_next(tr[p].l, key));
}

int main()
{
    srand(time(0));
    build();

    scanf("%d", &n);
    while (n -- )
    {
        int opt, x;
        scanf("%d%d", &opt, &x);
        if (opt == 1) insert(root, x);
        else if (opt == 2) remove(root, x);
        else if (opt == 3) printf("%d\n", get_rank_by_key(root, x) - 1);
        else if (opt == 4) printf("%d\n", get_key_by_rank(root, x + 1));
        else if (opt == 5) printf("%d\n", get_prev(root, x));
        else printf("%d\n", get_next(root, x));
    }

    return 0;
}
```



### 3. 能水过去得暴力版本（by评论）



```c++
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cstring>
#include<cmath>
#include<vector>
using namespace std;
vector<int> a;
int main()
{
    int n,opt,x;
    scanf("%d",&n);
    while(n--)
    {
        scanf("%d%d",&opt,&x);
        switch(opt)
        {
            case 1:a.insert(upper_bound(a.begin(),a.end(),x),x); break;
            case 2:a.erase(lower_bound(a.begin(),a.end(),x)); break;
            case 3:cout<<lower_bound(a.begin(),a.end(),x)-a.begin()+1<<endl; break;
            case 4:cout<<a[x-1]<<endl; break;
            case 5:cout<<(*--lower_bound(a.begin(),a.end(),x))<<endl; break;
            case 6:cout<<(*upper_bound(a.begin(),a.end(),x))<<endl; break;
        }
    }
    return 0;
}
```

#### 随机数：

- c++：

```c++
srand(time(0));//用当前时间来随机
int a=rand();//随机数 [0,unsigned int的最大值]
```

​	[C++ rand 与 srand 的用法 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/cpp-rand-srand.html)

- java:(哇敲，这里是有坑的，一不注意会超空间的)

  不知道它的底层是什么，反正**一定要指定范围**

```java
Random random=new Random();
int a=random.nextInt(N);//这里一定要指定范围[0,N)
```

[实例解析常用的java随机数生成办法_w3cschool](https://www.w3cschool.cn/java/java-random.html)



## 无旋`Treap`(FHQ Treap) （推荐）

（感觉没有cnt会更简单点，**下面有没有cnt的版本**）

(那几个`BST`的通用操作还是要会的，不涉及左右旋那几个)

(找前后缀，也能用二分图，分为两区间，答案放在`return`中)

![分裂](https://oi-wiki.org/ds/images/treap-none-rot-split-by-val.svg)

![分裂](https://pic3.zhimg.com/80/v2-2d4ea07aedb1dedd0324006930cf17ea_1440w.webp)

![分裂合并.jpg](https://cdn.acwing.com/media/article/image/2024/02/08/246003_d15c8a09c6-分裂合并.jpg) 



注意：(唉，自己写的bug)

- ==**递归名字不要写错**==（尤其是按排名分裂那里）
- 只要是改变点树最后都要`pushup`：
  - 分裂合并，插入，删除
- 多看几遍那个`get_key_by_rank`：**很关键很容易错**
  - **每个分支都要`return`**
- 最好还是写成`if ifelse`分支，这样比较不会错'
- 分裂完，记得复原

```java
//无旋Treap（FHQ Treap）
        //旋转操作太繁琐了，感觉真的写不来
    /*无旋Treap只需要分裂合并就能搞定几乎所有的操作，而且还有很多扩展操作
        时间复杂度可以说差不多
        说明：最后生成的树跟旋转treap其实是一样的，treap唯一性
        然后就是这里是用java写，引用只能返回值的形式*/
    /*这次的代码我就尽量去吸收好多种代码(看我裤裤抄)
        大部分写的模板都是不处理相同值的(cnt)，这里感觉可以处理下,还是会比旋转方便
        然后尽量去往y总的分格去靠*/
    /*题解风格：（直接一手究极缝合怪）
        1.返回值平替引用
        2.有cnt相同值计数器
            (没有cnt确实会简单点，但是有点浪费空间了)
        3.数组版treap
        4.模仿y总的码分
            (像建两个最值点，避免边界问题之类的)
        5.有按排名分裂
            （但是不会用单单这个找对应排名的数，因为有cnt，不好操作）*/
import java.util.*;
import java.io.*;
class Node{
    int l,r;//左右子树
    int key,val;//实际值 和 堆的随机权值（堆值）
    int cnt,size;//相同值的个数 和 子树的点数
        //这里为了可读性，就不写构造函数了
}
class Pair{//为了分裂充当返回值（平替下引用）
    int l,r;//分裂的左右树
    //规定：左子树承接<=key,右子树承接>key
    Pair(int l,int r){
        this.l=l;
        this.r=r;
    }
}
public class Main{
    static final int N=(int)1e5+10,INF=(int)1e8;//INF最值（不存在）
    //无旋treap
    static Node[] tr=new Node[N];
    static int root,idx;//根节点 和 下标分配器
    static void pushup(int p){//更新p这个点的size
        //注意：只要改变了树的结构就是要用的（分列和合并）
        tr[p].size=tr[tr[p].l].size+tr[tr[p].r].size+tr[p].cnt;
    }
    static Random random=new Random();//随机数分配器
    static int get_node(int key){//创建结点
        tr[++idx]=new Node();
        tr[idx].key=key;
        tr[idx].val=random.nextInt(N);//给个随机数，记得限定范围，不然会有问题
                    //不知道为什么这个没有N也能过
        tr[idx].cnt=tr[idx].size=1;
        return idx;
    }
    static Pair split(int p,int key){//默认按key值分裂（返回左右树:左树小于等于,右树大于）
                            //这里看笔记的图，会好理解点
                        //这里用c++引用写，会更简单
    //这个函数只需要看键值，不会用堆值，分裂完都是treap
        if(p==0) return new Pair(0,0);
        
        //其他的情况
        int l,r;
        //每次分裂都是针对p,只有pushup它就行
        if(tr[p].key<=key){//注意下这个等号
                //当根节点<=key时，这时候需要右割(想一下刚好等于的时候)
            l=p;
            //分裂右子树，可能存在的部分<=key（左树）,成为新的右子树
            Pair t=split(tr[p].r,key);
            tr[p].r=t.l;
            r=t.r;
        }else{//跟上面完全对称操作
            r=p;
            Pair t=split(tr[p].l,key);
            tr[p].l=t.r;
            l=t.l;
        }
        pushup(p);//对p就是，别忘记这步
        return new Pair(l,r);
    }

    static Pair split_by_rank(int p,int rank){//按排名分裂（返回左树小于等于）
                        //其实没有这个也能做出来了，毕竟模板题还是写全一点
        if(p==0) return new Pair(0,0);
        int l,r;//大部分同上
        int now_rank=tr[tr[p].l].size+tr[p].cnt;//这个其实是当前排名
        if(now_rank<=rank){//在右子树
                //根节点刚好等于rank时，右割
            l=p;
            //分裂右子树，可能存在的部分<=key（左树）,成为新的右子树
            Pair t=split_by_rank(tr[p].r,rank-now_rank);//注意：右边子树的排名是要减去根和左边的
            tr[p].r=t.l;
            r=t.r;
        }else{
            //跟上面完全对称操作
            r=p;
            Pair t=split_by_rank(tr[p].l,rank);//注意递归的名字别用错了
            tr[p].l=t.r;
            l=t.l;
        }
        pushup(p);//对p就是，别忘记这步
        return new Pair(l,r);
    }
    static int merge(int l,int r){//合并，返回新的根节点
            //l和r都是treap,合并时只看堆值就行
                //时刻要从BST的性质出发：l所有点都<r
        if(l==0||r==0) return l+r;//含有空结点
        
        //其他
            //其实就是两数争根
        int p;//新根
            //每次也是pushup根结点
        if(tr[l].val>tr[r].val){//左边的根成为新根
            p=l;
            //右和左的右子树合并
            tr[l].r=merge(tr[l].r,r);
        }else{//右的根成为新根
        //也是完全对称操作
            p=r;
            tr[r].l=merge(l,tr[r].l);//这里注意左右
        }
        pushup(p);//别忘了
        return p;
    }
    static void build(){//建树：包含两个不存在的点
        tr[0]=new Node();//java童鞋不要忘了，不然会报错的
        get_node(-INF);get_node(INF);
        root=merge(1,2);//合并子树
    }
    //只要点数还是形态改变都必须pushup
    static int insert(int p,int key){//插入值
        //cnt体现在这，没有会省略一点
        Pair t=split(p,key);
        int l,cur,r=t.r;//分裂称小于 等于 大于
        t=split(t.l,key-1);//左子树是小于等于
        l=t.l;cur=t.r;
        if(cur==0)
            cur=get_node(key);//没有这个点
        else//存在
            ++tr[cur].cnt;
        pushup(cur);
        return merge(merge(l,cur),r);//合并,会自动pushup
        
    }
    static int remove(int p,int key){//合并 （跟插入类似）
        Pair t=split(p,key);
        int l,cur,r=t.r;//分裂称小于 等于 大于
        t=split(t.l,key-1);//左子树是小于等于
        l=t.l;cur=t.r;
        if(tr[cur].cnt>1)
            --tr[cur].cnt;
        else
            cur=0;//标记为空结点
        pushup(cur);
        return merge(merge(l,cur),r);
    }
    
    
    //下面这些查找操作都能用传统方法(前面那些都是能用的)，也能用分裂合并的方法
        //传统BST应该会快一点，但是分裂合并简单
    static int get_rank_by_key(int p,int key){//根据值找排名
        if(p==0) return -1;//空节点不存在这个排名
        Pair t=split(p,key-1);//稍稍改动：变为左树<key
        int rank=tr[t.l].size+1;
        merge(t.l,t.r);//复原
        return rank;
    }
    static int get_key_by_rank(int p,int rank){//根据排名找值
        //这个就直接按前面的来了，比较快一点
            //可以按排名分裂出rank这个排名，然后再合并
        if(p==0) return INF;//不存在，这个一般是排名太大
        if(tr[tr[p].l].size>=rank) 
            return get_key_by_rank(tr[p].l,rank);//在左子树上
        //这种函数返回值，if并列还是if else都是一样的
        int t=tr[tr[p].l].size+tr[p].cnt;//当前节点的排名
        if(t>=rank) return tr[p].key;
        //剩下的情况就是在右子树的情况了
        return get_key_by_rank(tr[p].r,rank-t);
    }
    /*(初版，没写成功)
    //这里没想到什么好办法，如果有cnt的话
    static int get_key_by_rank(int p,int rank){
        if(p==0) return INF;
        int l,r,cur;
        Pair t=split_by_rank(p,rank);
        r=t.r;
        t=split_by_rank(t.l,rank-1);//这里不行，你不知到具体有多少个点
        l=t.l;cur=t.r;
        // System.out.println(tr[l].size+" "+tr[cur].size+" "+tr[r].size);
        int key=tr[cur].key;
        merge(merge(l,cur),r);
        return key;
    }
    */
    static int get_prev(int p,int key){// 找到严格小于key的最大数
        if(p==0) return -INF;//不存在
        //左树小于key
        Pair t=split(p,key-1);
        //找左数中最大的
        int res=get_key_by_rank(t.l,tr[t.l].size);
        merge(t.l,t.r);
        return res;
    }
    static int get_next(int p,int key){// 找到严格大于key的最小数
        if(p==0) return INF;
        //右树大于key
        Pair t=split(p,key);
        //找右树的最小值
        int res=get_key_by_rank(t.r,1);
        merge(t.l,t.r);
        return res;
    }
    
    public static void main(String[] args)throws Exception{
        BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
        int n=Integer.parseInt(br.readLine());
        
        build();//别忘了
        
        String[] ss;
        int opt,x;
        while(n-- >0){
            ss=br.readLine().split(" ");
            opt=Integer.parseInt(ss[0]);
            x=Integer.parseInt(ss[1]);
            if (opt == 1)      root=insert(root, x);
            else if (opt == 2) root=remove(root, x);
                    //排名都要注意两个虚拟结点
            else if (opt == 3) System.out.println(get_rank_by_key(root, x) - 1);
            else if (opt == 4) System.out.println(get_key_by_rank(root, x + 1));
            else if (opt == 5) System.out.println(get_prev(root, x));
            else               System.out.println(get_next(root, x));
        }
    }
}
//这个是会比较简单，但是还是把能错的都整上了
```

(`c++`版:无cnt版本看：《算法竞赛》)（往下看，实现了大部分，但是估计有部分是有问题的）

参考代码：

(无cnt版本   java)主要：[题解1](https://www.acwing.com/activity/content/code/content/493760/)

部分思路：[Treap - OI Wiki (oi-wiki.org)](https://oi-wiki.org/ds/treap/#插入_1)

参考图片：[Treap 和 fhq treap - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/657243646)



### 例题：

### [265. 营业额统计 - AcWing题库](https://www.acwing.com/problem/content/description/267/)

设第 $i$ 天的营业额为 $a_i$，则第 $i$ 天($i \ge 2$)的最小波动值 $f_i$ 被定义为：

​					$$f_i=min_{1 <= j <= i}|a_i-a_j|$$

当最小波动值越大时，就说明营业情况越不稳定。
而分析整个公司的从成立到现在营业情况是否稳定，只需要把每一天的最小波动值加起来就可以了。
你的任务就是编写一个程序帮助 Tiger 来计算这一个值.
第一天的最小波动值为第一天的营业额 $a_1$。

数据范围：<p>$1 \le n \le 32767, |a_i| \le 10^6$</p>（比较水）

---

#### 无旋`Treap（无cnt版）`

这个代码是验证过，完全没问题的：

靠，我忘记这题的前驱和后继不太一样，**一定要注意这的前驱后继**

```java
//无旋Treap
    //无cnt版本
import java.util.*;
import java.io.*;
class Node{
    int l,r;
    int key,val;
    int size;
}
class Pair{//承接分裂的值
    int l,r;
    Pair(int l,int r){
        this.l=l;
        this.r=r;
    }
}
public class Main{
    static final int N=(int)3.5e4,INF=(int)1e8;//最值
    static Node[] tr=new Node[N];
    static int root,idx;
    static Random rand=new Random();
    static int get_node(int key){
        tr[++idx]=new Node();
        tr[idx].key=key;
        tr[idx].val=rand.nextInt(N);
        tr[idx].size=1;
        return idx;
    }
    static void pushup(int p){
        tr[p].size=tr[tr[p].l].size+tr[tr[p].r].size+1;
    }
    static Pair split(int p,int key){//按值分裂
        if(p==0) return new Pair(0,0);
        int l,r;
        if(tr[p].key<=key){//当前点小于等于，分裂右子树
            l=p;
            Pair u=split(tr[p].r,key);
            tr[p].r=u.l;
            r=u.r;
        }else{
            r=p;
            Pair u=split(tr[p].l,key);
            tr[p].l=u.r;
            l=u.l;
        }
        pushup(p);
        return new Pair(l,r);
    }
    static Pair split_by_rank(int p,int rank){//按排名分裂（返回左树小于等于）
                        //其实没有这个也能做出来了，毕竟模板题还是写全一点
        if(p==0) return new Pair(0,0);
        int l,r;//大部分同上
        int now_rank=tr[tr[p].l].size+1;//这个其实是当前排名
        if(now_rank<=rank){//在右子树
                //根节点刚好等于rank时，右割
            l=p;
            //分裂右子树，可能存在的部分<=key（左树）,成为新的右子树
            Pair t=split_by_rank(tr[p].r,rank-now_rank);//注意：右边子树的排名是要减去根和左边的
            tr[p].r=t.l;
            r=t.r;
        }else{
            //跟上面完全对称操作
            r=p;
            Pair t=split_by_rank(tr[p].l,rank);//注意递归的名字别用错了
            tr[p].l=t.r;
            l=t.l;
        }
        pushup(p);//对p就是，别忘记这步
        return new Pair(l,r);
    }
    static int merge(int l,int r){//只看随机的堆值
        if(l==0||r==0) return l+r;
        //看彼此的根
        int p;
        if(tr[l].val>tr[r].val){
            p=l;
            tr[l].r=merge(tr[l].r,r);
        }else{
            p=r;
            tr[r].l=merge(l,tr[r].l);
        }
        pushup(p);
        return p;
    }
    static void build(){
        tr[0]=new Node();
        get_node(-INF);
        get_node(INF);
        root=merge(1,2);
    }
    static int insert(int p,int key){
        Pair u=split(p,key);
        return merge(merge(u.l,get_node(key)),u.r);
    }
    static int remove(int p,int key){
        int l,cur,r;
        Pair u=split(p,key);
        r=u.r;
        u=split(u.l,key-1);
        l=u.l;cur=u.r;
        cur=merge(tr[cur].l,tr[cur].r);//直接删当前点
        return merge(merge(l,cur),r);
    }
    /*法一：平衡树通用
    static int get_key_by_rank(int p,int rank){
        if(p==0) return INF;
        if(tr[tr[p].l].size>=rank) 
            return get_key_by_rank(tr[p].l,rank);
        int t=tr[tr[p].l].size+1;//当前排名
        if(t>=rank)
            return tr[p].key;
        return get_key_by_rank(tr[p].r,rank-t);
    }
    */
    static int get_rank_by_key(int p,int key){//根据值找排名
        if(p==0) return -1;//空节点不存在这个排名
        Pair t=split(p,key-1);//稍稍改动：变为左树<key
        int rank=tr[t.l].size+1;
        merge(t.l,t.r);//复原
        return rank;
    }
    static int get_key_by_rank(int p,int rank){
        if(p==0) return INF;
        int l,r,cur;
        Pair t=split_by_rank(p,rank);
        r=t.r;
        t=split_by_rank(t.l,rank-1);//这里不行，你不知到具体有多少个点
        l=t.l;cur=t.r;
        // System.out.println(tr[l].size+" "+tr[cur].size+" "+tr[r].size);
        int key=tr[cur].key;
        merge(merge(l,cur),r);
        return key;
    }
    static int get_prev(int p,int key){//找小于等于x的最大值
        if(p==0) return -INF;
        Pair u=split(p,key);//左区间是小于等于
        //找左区间的最大值
        int res=get_key_by_rank(u.l,tr[u.l].size);
        merge(u.l,u.r);
        return res;
    }
    static int get_next(int p,int key){//找大于等于的最小值
        if(p==0) return INF;
        Pair u=split(p,key-1);//这样右区间就是小于等于的
        int res=get_key_by_rank(u.r,1);
        merge(u.l,u.r);
        return res;
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);//低于1e5用这个
        int n=sc.nextInt();
        long res=0;
        build();
        for(int i=0;i<n;++i){
            int x=sc.nextInt();
            if(i==0) res+=x;
            else{
                // System.out.println(get_prev(root,x)+" "+get_next(root,x));
                res+=Math.min(x-get_prev(root,x),get_next(root,x)-x);
                // System.out.println(Math.min(x-get_prev(root,x),get_next(root,x)-x));
            }
            root=insert(root,x);
        }
        System.out.println(res);
    }
}

```



  

##### 传统平衡树方法

```java
//无旋Treap
import java.util.*;
import java.io.*;
class Node{//本题是简化版本,只需要查询前后缀
    int l,r;
    int key,val;
}
class Pair{//承接分裂的值
    int l,r;
    Pair(int l,int r){
        this.l=l;
        this.r=r;
    }
}
public class Main{
    static final int N=(int)3.5e4,INF=(int)1e8;//最值
    static Node[] tr=new Node[N];
    static int root,idx;
    static Random rand=new Random();
    static int get_node(int key){
        tr[++idx]=new Node();
        tr[idx].key=key;
        tr[idx].val=rand.nextInt(N);
        return idx;
    }
    static Pair split(int p,int key){//按值分裂
        if(p==0) return new Pair(0,0);
        int l,r;
        if(tr[p].key<=key){//当前点小于等于，分裂右子树
            l=p;
            Pair u=split(tr[p].r,key);
            tr[p].r=u.l;
            r=u.r;
        }else{
            r=p;
            Pair u=split(tr[p].l,key);
            tr[p].l=u.r;
            l=u.l;
        }
        //pushup
        return new Pair(l,r);
    }
    static int merge(int l,int r){//只看随机的堆值
        if(l==0||r==0) return l+r;
        //看彼此的根
        int p;
        if(tr[l].val>tr[r].val){
            p=l;
            tr[l].r=merge(tr[l].r,r);
        }else{
            p=r;
            tr[r].l=merge(l,tr[r].l);
        }
        //pushup
        return p;
    }
    static void build(){
        tr[0]=new Node();
        get_node(-INF);
        get_node(INF);
        root=merge(1,2);
    }
    static int insert(int p,int key){
        Pair u=split(p,key);
        return merge(merge(u.l,get_node(key)),u.r);
    }
    static int get_prev(int p,int key){//找小于等于x的最大值
        if(p==0) return -INF;
        //这里还是用二分那个图来解决，分为两个区间，答案放在return
        if(tr[p].key>key) return get_prev(tr[p].l,key);
        return Math.max(tr[p].key,get_prev(tr[p].r,key));//根节点符合<=,还需要向右找找
    }
    static int get_next(int p,int key){//找大于等于的最小值
        if(p==0) return INF;
        if(tr[p].key<key) return get_next(tr[p].r,key);
        return Math.min(tr[p].key,get_next(tr[p].l,key));
        
    }
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);//低于1e5用这个
        int n=sc.nextInt();
        long res=0;
        build();
        for(int i=0;i<n;++i){
            int x=sc.nextInt();
            if(i==0) res+=x;
            else{
                res+=Math.min(x-get_prev(root,x),get_next(root,x)-x);
            }
            root=insert(root,x);
        }
        System.out.println(res);
    }
}

```







#### `TreeSet`做法：

```java
//TreeSet容器：
import java.util.*;
import java.io.*;
public class Main{
    static final int INF=(int)1e8;//最值
    public static void main(String[] args){
        Scanner sc=new Scanner(System.in);//低于1e5用这个
        int n=sc.nextInt();
        long res=0;
        //建树，同时避免特判
        TreeSet<Integer> s=new TreeSet<>();
        s.add(-INF);
        s.add(INF);
        for(int i=0;i<n;++i){
            int x=sc.nextInt();
            if(i==0) res+=x;
            else{
                res+=Math.min(x-s.floor(x),s.ceiling(x)-x);
            }
            s.add(x);
        }
        System.out.println(res);
    }
}

```




# 数据结构第七周笔记——图(中)(慕课浙大版本--XiaoYu)

## 树之习题选讲-Tree Traversals Again

### 树习题-TTA.1 题意理解

 非递归中序遍历的过程

1. Push的顺序为先序遍历(pre)
2. Pop的顺序给出中序遍历(in)

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220728170236134.png" alt="image-20220728170236134" style="zoom:50%;" />





### 树习题-TTA.2 核心算法

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220728191451717.png" alt="image-20220728191451717" style="zoom:50%;" />

![image-20220728194407506](https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220728194407506.png)



```c
上图分别是先序、中序、后序遍历通过规律我们可以看到他们之间的位置分配
//伪代码
void solve(int preL,int inL,int n)
{
    if(n == 0) return;//n等于0的时候什么都不做(n真的会右等于0的时候吗？为什么写他?)调用完了之后右边没有元素，此时n等于0，进行判断正常结束进程
    if(n == 1){post[postL] == pre[preL];return;}//只有一个结点的时候，pre、in、post都应该等于同一个数字
    root = pre[preL];
    post[postL+n-1] = root;
    for(i = 0; i < n; i++)
        if(in[inL+i] == root) break; //判断in这个位置上的元素是不是等于根结点。结合上图看就是要找根结点在图中的哪里，找到了就跳出循环
    //在跳出循环之后我们就同时知道了左子树包含了多少元素，然后递归的去解决左边跟右边的问题
    L = i;R = n - L -1//得出左右两边的元素个数
    solve(preL+1,inL,postL,L);//左边的
    solve(preL+L+1,inL+L+1,postL+L,R)//右边的。第一个是蓝色的第一个位置，第二个也是蓝色一个的位置，第三个是蓝色的第一个位置(和前两个不同的是这个时候1在蓝色的后面，所以不用加上1了，第四个参数是右边子问题的总个数)
        
}
```



## 树之习题选讲-Complete Binary Search Tree(完全 二叉 搜索 树)

### 树习题-CBST.1 数据结构的选择

#### 题意理解

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220730184530957.png" alt="image-20220730184530957" style="zoom:50%;" />

```
题目要求：
输入一系列整数，要填入一个完全二叉树里面，同时这颗树还需要满足二叉搜索树的性质。也就是说左边的结点的键值都比根结点要小，右边的全部都比他要大，左右子树也都满足二叉搜索树递归的定义
```

经过修改后的图片如下：

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220730185418653.png" alt="image-20220730185418653" style="zoom:50%;" />

**树的表示法：链表 vs 数组**

```
在这道题目中我们需要的操作：
1.填写数字(某种遍历)，意思就是说对这棵树里面的每一个结点访问一次填写一个数字
2.层序遍历
```

**为什么在很多情况下我们宁愿用链表去表示一颗树而不用数组呢？**

```
用数组是可以的，好处是不涉及任何指针的操作。指针的操作通常是比较危险又比较耗时的
缺点：用数组去存，遇到左右不完全相等的树的时候，需要把中间那些不存在的结点的空间保留下来，要是树极端一点就非常耗费空间，在链表就没有这个问题
```

在这个问题中我们需要解决的问题：

1. **完全二叉树，不浪费空间**(从根结点到最后一个叶子结点一层层走下来没有一个是空的，没有一个元素是浪费的)
2. 层序遍历 == 直接顺序输出(按照下标顺序输出就结束了，用数组的话)所以我们决定用数组解决这个问题

### 树习题-CBST.2 核心算法

如果我们知道左子树一共包含了多少个结点，那就知道根结点R上放的一定是从小到大排第几位的那个数字

如何知道左子树有多少个结点呢？

1. 给定n个数之后，完全二叉树的结构是固定的，可以非常准确的算出左边一共多少个结点
2. 先给我们要输入的序列从小到大排一个序列。根据完全二叉树一共有多少个结点，通过导出的公式是可以精确计算他的左子树一共有多少个结点
3. <img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220730200103573.png" alt="image-20220730200103573" style="zoom:50%;" />典型先序遍历的应用

#### 核心算法

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220730215836941.png" alt="image-20220730215836941" style="zoom:50%;" />

TRoot是树T根结点所在的位置，那个元素的下标存在TRoot里面

```c
void solve(int ALeft,int ARight,int TRoot)
{
    //初始调用为solve(0,N-1,0)->A的起始点就是A的第0个元素，A的终止点就是A的最后一个元素(N-1是他的下标)，结果树T是完全二叉树，他的根结点一定存在T[0]这个位置，所以刚开始传入的是0
    //整个函数要完成的任务就是从A传进去的这一段里面选出一个正确的数字，填到我们结果的这颗树,TRoot的这颗树上
    n = ARight - ALeft + 1;//这一段里面元素总个数n(最右边下标减去最左边下标加上1)
    if(n == 0) return;
    L = GetLeftLenght(n);//计算出n个结点的树(完全二叉树)其左子树有多少个结点 
    T[TRoot] = A[ALeft + L];
    //左孩子的下标是多少？正常情况下根结点是TRoot的话那他的左孩子下标应该是TRoot×2，但是那是在堆里面(堆的第0个元素是存放哨兵的地方，不是用来存真实值的)，在本题中是从0开始算下标的
    LeftRoot = TRoot * 2 + 1;//(例子：左孩子第一个元素0*2+1为1，第二个3...)
    RightTRoot = LeftTRoot + 1;//(例子：右孩子第一个元素1+1为2，第二个4...)
    //准备递归左右边
    solve(ALeft,ALeft + L - 1,LeftTRoot);//左边，ALeft + L - 1是取掉根结点元素的前一个值
    solve(ALeft + L + 1,ARight,RightTRoot);//右边
    
}
```

#### 排序(目前凑合用的，后面详细讲解使用)

库函数：qsort

```c
#include<stdlib.h>//调用qsort
int main()
{
    ....
    qsort(A,N,sizeof(int),compare);//根据返回的两个数是正数还是负数还是0来决定两个元素谁排在前面或者后面或者不做交换
    //第一个参数：待排序序列的首元素的位置(就是把读进来的数存在一个叫A的数组里面)也就是说如果我们要将数组排序的话，这个就是数组首元素的地址
    //第二个参数：代排序列的总长度(一共要排N个元素)
    //第三个参数：要排元素的大小
    //第四个参数：不是变量，是一个函数的名字(可以不叫compare，这个随意)，他的作用是比较两个元素的大小。因为qsort就是比较一对元素的大小来决定哪个元素应该在前面的，哪个元素应该在后面的
    ....
}

    /*compare标准接口
    int compare(const void*a,const void*b)传入的是两个带比较元素的指针。需要返回的是三种整数之一(负数正数或者0，这里浙大的课程中字幕第一次出错负数显示为复数，但其实是负数，需要注意)
    {
    	return *(int*)a - *(int*)b;//后序也可以非常复杂
    }
    */
```

### 树习题-CBST.3 计算左子树的规模

h是层数

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220730232211145.png" alt="image-20220730232211145" style="zoom:50%;" />

真正计算H的时候X的出来的可能不是整数，但不要紧，X是多少都没有关系，求H的话可以忽略掉X然后的出来的答案向下取整即可。得到的H就是完美二叉树层数(H)，然后就可以反算出X了

完美二叉树的左子树：<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220730232601997.png" alt="image-20220730232601997" style="zoom:50%;" />

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220730232638342.png" alt="image-20220730232638342" style="zoom:33%;" />

如果X的个数蔓延到右子树那边去的话(就是最后一层的x跑到右边去，那上面左子树的式子就不能加上X)

所以我们需要知道最下面一层x的最大值和最小值可以取多少

1. 在这个式子中，最小值X要取到0(为什么不是1而是0呢？L是左子树哦)<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220730233019880.png" alt="image-20220730233019880" style="zoom:33%;" />

   1. 因为在上述式子中H至少为2啦，H为1是根结点的位置层数，左子树至少从第二层开始，最少只有他自己一个

2. 要使 ![img](https://img-ph-mirror.nosdn.127.net/gD61yyzBdJCC3bj5y02Eeg==/6631369031884693815.png) 得到正确结果，![img](https://img-ph-mirror.nosdn.127.net/lEiAQJQKvY14Qo2qj0E1Aw==/1072982611321422975.png)能取的最大值是 

   1. <img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220730233616555.png" alt="image-20220730233616555" style="zoom:33%;" />

3. 完整步骤：

   1. <img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220730233846536.png" alt="image-20220730233846536" style="zoom:50%;" />

   

## 树之习题选讲-Huffman Codes(哈夫曼)

### 树习题-HC.1 题意理解

Huffman编码不唯一

**注意：最优编码不一定通过Huffman算法得到,但是Huffman算法一定能得到最优编码**

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220731183340358.png" alt="image-20220731183340358" style="zoom:50%;" />

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220731184926731.png" alt="image-20220731184926731" style="zoom:50%;" />

#### Huffman Codes 的特点

1. 最优编码——总长度(WPL)最小
2. 无歧义解码——前缀码：数据仅存于叶子结点

```
前缀码对应到一颗二叉树里面意味着每一个字符都要放在这棵树的叶子结点上
如果有任何一个字符放在了中间的内部结点上，那就意味着一定存在另外一个字符(他的编码会经过这个字符的编码)，也就是说这个字符就会成为另外一个字符的前缀编码，这样解码的时候就会有歧义
```

3. Huffman树是没有度为1的结点——满足1、2则必然有3

```
什么是度为1的结点：
“度是一个计算机的单位，度为1的节点就说明该处的子节点个数为1，度为2就说明个数为2，而度为0的结点叫叶子结点，由二叉树的性质可以知道，二叉树中叶子结点总是比度为2的结点多一个。”

这个在程序中不需要判断的，因为满足第一跟第二的话，一定没有度为1的结点
为什么？
反证法：如果在这棵树里面存在一个度为1的结点，那么这个结点，假设他又这样一颗子树的话，他肯定不是叶子结点。那么在这个结点上就一定不会放真正的字符，那么他的这个一棵子树下面，一定是有叶子结点的。那些叶子结点对应着真正的字符的。如果我们把这个结点给他去掉的话,并不影响其他的编码仍然保持为前缀码这个特点，因为所有的字符还都在叶子结点上。
那么他原来子树上所有的叶子结点，所有的编码都会缩短一个点位，于是我们就得到一套更短的编码。但我们说他本身已经是最优的编码了，那已经本身是属于不在可以优化的了，已经没办法得到更短的编码了，所有反证得出一定没有度为1的结点
```

**注意：满足2、3可不一定有1!**

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220731195452124.png" alt="image-20220731195452124" style="zoom:50%;" />

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220731195626707.png" alt="image-20220731195626707" style="zoom:50%;" />

上图中两边都符合2、3，但是右边才是最优编码。由此得出符合2、3的不一定是最优编码

### 树习题-HC.2 计算最优编码长度

1. 计算最优编码长度

   1. ```C
      //声明H
      MinHeap H = CreateHeap(N);//创建一个空的、容量为N的最小堆(调用CreateHeap创建一个容量为N的最 小堆)
      H = ReadData(N);//将f[]读入H->Data[]中，为后面建立最小堆做好准备
      HuffmanTree T = Huffman(H);//建立Huffman树，提示：在调用哈夫曼算法的时候必须要先有一个最小堆(建立最小堆是在哈夫曼算法里面完成的)
      //哈夫曼算法里面先建立一个最小堆，每次从里面弹出两个最小的结点，然后生成一颗新的树再把它压回去
      计算最优编码长度还是需要我们自己去写一个函数的
      int CodeLen = WPL(T,0);//CodeLen返回的就应该是这颗哈夫曼树所对应的最优编码总长度
      //T是传入的树根，0是开始的地方
      //深度是传进去的每一棵子树的根结点他当前的深度是多少，此代码是从整棵树的根结点开始的(当前是0，从0开始)
      
      //进行的是一个递归的先序遍历过程
      int WPL(HuffmanTree T,int Depth)
      {
          if(!T->Left && !T->Right )
              return (Depth*T0->Weight);//当前深度乘以他的权重
          else//否则T一定有2个孩子
              return (WPL(T->Left,Depth+1) + WPL(T->Right,Depth+1));//递归的解决左边的问题和右边的问题，返回两边的和，递归返回的深度是要加1的
      }
      ```
      

### 树习题-HC.3 检查编码

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220801133232845.png" alt="image-20220801133232845" style="zoom:50%;" />

```
2与3冲突、100与1001，如果100的最后一个0是叶子结点，那他就不应该还能继续延伸下去
1与4冲突、1011与101，1011最后一个1是叶子结点，101的最后一个1在内部结点上(因为"1011"相对于"101后面还有一个1，就是说后面还有一个右子树)就冲突了
```

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220801132836279.png" alt="image-20220801132836279" style="zoom:50%;" />

## 7.1 最短路径问题

### 7.1.1 概述

```c
a点到b点之间路径可达最短的一条线路就是最短路径

或者说是从a点到b点最省钱的路线，这也是一种最短路的问题 ->这种情况下什么是长什么是短？
    //区别就在边上的权重(我们赋予了他们什么意义)，比如我们要找最便宜的路径，那么两点之间的那个边的权重就应该定义成票的价格
    //我们要找的就是票的价格的和是最小的那条路
假如我们要找的是a站点到b站点最快的那个路径，这时候什么叫快？
    //是 经停的站最少的快 还是说 途经时间最短的快。在这里我们选择前者
    //这时候边的权重就定义为1，经停站的个数就是每条边加起来的了
```

#### 最短路径问题的抽象

在网络中，求两个不同顶点之间的所有路径中，边的权值之和最小的那一条路径

1. 这条路径就是两点之间的最短路径(ShortestPath)
2. 第一个顶点为源点(Source)
3. 最后一个顶点为终点(Destination)

#### 问题分类

1. 单源最短路问题：
   1. 从某固定源点出发，求其到所有其他顶点的最短路径
   2. (有向)无权图
   3. (无向)有权图
2. 多源最短路径问题：
   1. 求任意两顶点间的最短路径

### 7.1.2 无权图的单源最短路

1. 按照递增(非递减)的顺序找出到各个顶点的最短路
2. <img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220801185718394.png" alt="image-20220801185718394" style="zoom:50%;" />
3. 这是一圈一圈向外扫描的(BFS广度优先搜索)，同时也就解决了James Bond从孤岛跳上岸，最少需要多少步

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220801190113983.png" alt="image-20220801190113983" style="zoom:50%;" />

上图中if中判断的条件里的是BFS的，在这里是不需要的，这里需要的是：

```c
dist[W] = S到W的最短距离//无穷大、无穷小、-1都可以。因为这些数可以一看到就知道还未被访问过，下面采用-1的方式
dist[S] = 0//初始值是0，表示他到他自己的距离
path[W] = S到W的路上经过的某顶点//记录路径，对每一个顶点W，把源点到w的路上一定要经过的某一个顶点存在这个path里面 
```

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220801190047751.png" alt="image-20220801190047751" style="zoom:50%;" />

单源最短函数算法

```c
void Unweighted ( Vertex S )//unweighted是无权的意思
{
	Enqueue(S,Q);
    while(!IsEmpty(Q)){
        V = Dequeue(Q);//每次弹出来一个顶点，就意味着这个顶点到s的最短路径已经被找到了
        for ( V 的每一个邻接点 W )
            if( dist[W] == -1 ){//正常的数都是正数，-1是属于不会被访问过的
                dist[W] == dist[V]+1;//这个时候W的最短距离就找到了
                path[W] = V//谁是S到W路上必经的顶点呢？那就是他的前一个顶点(V是顶点的编号)。记录下来这个的作用是：顺着path这个数组一个一个往前推，直到推到源点得到一个反向的路径(然后将其压到堆栈里面(堆栈起反序作用，后序先出)，从而得到正确的路径)
                Enqueue(W,Q);
            }
    }
}
```

如果有|V|个顶点和|E|条边的图用邻接表存储，则算法的时间复杂度是多少？

```
是 T = O(|V|+|E|)
```

### 7.2.2-s 无权图的单源最短路示例

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220801223448836.png" alt="image-20220801223448836" style="zoom:50%;" />

```c
void Unweighted ( Vertex S )//unweighted是无权的意思
{
	Enqueue(S,Q);
    while(!IsEmpty(Q)){//这是判断他是不是为空再决定要不要进行下去
        V = Dequeue(Q);
        for ( V 的每一个邻接点 W )
            if( dist[W] == -1 ){//正常的数都是正数，-1是属于不会被访问过的
                dist[W] == dist[V]+1;
                path[W] = V
                Enqueue(W,Q);
            }
    }
}

//dist是点到源点的最短距离
//path是指当前点在那条路径上
//所以所有的信息就都可以从表格中得到
```

### 7.1.3 有权图的单源最短路

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220801223914092.png" alt="image-20220801223914092" style="zoom:50%;" />由图得知从红色的位置到绿色的最短路径是哪条？

是这条啦：<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220801224456016.png" alt="image-20220801224456016" style="zoom:50%;" />

有权图跟无权图最短路的区别：

```
有权图的最短路不一定是经过顶点数最少的那条路，上图就很好的证实了这点
上图的最短路上的全重合时1加8等于9.但有权图的最短路是1+4+1=6(权重更低)
```

如果路径上的权重还有负数的话，是不是最短路又会发生改变，比如这样：<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220801225311696.png" alt="image-20220801225311696" style="zoom:50%;" />

这个图我如果不停的循环，每圈赚5块，那无限转圈不就反而倒赚正无穷(美好的愿望hh)<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220801225512626.png" alt="image-20220801225512626" style="zoom:50%;" />

这种情况叫做有一个**负值圈**叫做(negative-cost cycle)

1. 图里面要是有这样一种负值圈的话，基本上所有的算法都会挂掉，所有后面不考虑这种情况
2. 算法相通之处：按照递增(非递减)的顺序找出到各个顶点的最短路

#### Dijkstra算法

跟BFS相似的地方在于他都是把顶点一个个往那个集合里面收

1. 令S = {源点s + 已经确定了最短路径的顶点vi}

2. 定义一个距离的数组叫做dist：对于任何一个没有被收入的顶点v，我们把dist v定义为 什么呢？定义为源点到这个v的最短路径长度，但是这不是最终的最短路径，但是该路径仅经过s中的顶点。即路径{s->(vi属于S)->v}的最小长度(多半不是我们真正想要的最小长度，但随着一个个顶点不断被加到这个集合里面，这个dist v会慢慢的变小变小，直到最后被完善成那个最短路径)，当他成为最短路径后，这个v就被收到了这个集合里面

3. 算法能够这样执行很重要的前提：路径是按照递增(非递减)的顺序生成的，则

   1. 真正的最短路必须只经过S中的顶点(为什么？)

   2. 采用反证法：

      ```
      假如我们下一个要把v收进去的时候，从s到v的路径上还存在另外一个点w。这个w是在这个s以外的，那想要从s到v必然要先到达w再到v，这个时候就会有矛盾，s到w的距离显然小于s到v的距离，而v是下一个马上就要被收进去的顶点，我们的路径是按照递增顺序生成的，这就是意味着凡是距离比v要小的那些顶点都应该在他之前就已经被收进去了，w到s的距离显然比v到s的距离小，所有w肯定应该已经在s这个集合里面了，不可能在外面。所以就得出上述的结论
      ```

   3. 每次就从未收录的顶点中选一个dist最小的收录(贪心算法)

   4. 增加一个v进去S，可能影响另外一个w的dist值：得到的两个重要的事实在下面的图中选项B里

   5. <img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220801234849458.png" alt="image-20220801234849458" style="zoom:50%;" />

   6. ```
      1不仅在w的路径上，而且从v到w必定存在一条直接的边(意思就是收w一定是v的邻接点。况且v被收录进去能影响的也就他一圈的邻接点了，所以收录进去的时候看看这个值周围一圈邻接点看谁有没有比他更小的)
      为什么不可能是从s先到v，然后v再经过另外一个顶点再到w呢？这种情况是不可能的，因为路径是按照递增顺序生成的，如果v和w之间还有另外一个顶点的话，那么这个顶点到源点的距离一定比v到源点的距离要大，但是我们假设的是w的dist值应该是从s到w，那条这条路径的长度仅仅经过这个集合里面的顶点。如果另外还有一个节点在v后面的话，那这个顶点不可能在s里面，因为v是新增进去的，v应该是里面集合最长的
      ```

      1. 更小的dist值可能为：dist[w] = min{dist[w],dist[v] + <v,w>的权重}

#### Dijkstra算法框架

```c
void Dijkstra(Vertex s)
{
    while(1){
        V = 未收录顶点中dist最小者;
        
        collected[V] = true;//收到集合里面
        for( V的每个邻接点 W )
            if( collected[W] === false ){
                if( dist[V]+E<v,w> < dist[W] ){//this不能随便初始化，因为这个不等式在的原因，我们当描述一个顶点跟s没有直接的路可以通的时候，一定要把它定义成正无穷，这样当我们发现一个更短路径的时候，才可以把这个往更小的地方去更新(假如我们随便把它定义成-1的话，那这个不等式永远都不会成立了)
                dist[W] = dist[V] + E<v,w>;//<v,w>是下标
                path[W] = V;
                }
            }
        
    }
}//Dijkstra算法不能解决有负边的情况，因为这个算法的思路是按照距离从小到大的顺序去收集每一个顶点，如果有一条边是负的，那么对于某一个w来说就有可能说就有了一个dist v 然后他减掉了一个正值，我们就可以得到一个比v还要短的w。然后w之前是排在v的后面的，所以整个算法会乱掉

假如有边的话？要怎么做
    
    Dijkstra算法的时间复杂度：不科学，没有欸。因为在上面的代码中只是一个伪代码演示
    在 dist[W] = dist[V] + E<v,w>; 中说的是如果我有一个更短的距离，我要把这个值更新为这个最短的距离。这个更新不一定是一个简单的赋值
    方法1：直接扫描所有未收录顶点 -O(|V|)//这个的时间复杂度就是OV
    //使用方法1这种粗暴的方式的话那后面的赋值语句真的就只是一行了。得出来的整体复杂度就是 T = O(|V|²+|E|)，扫描一遍是V个，一共扫描V遍且扫描的时候涉及边的每个邻接点，也就是每条边又被访问了一遍
    //对于稠密图效果好，稠密图是指那些有很多边的(边的条数跟顶点的个数是OV平方数量级的)
    方法2：将dist存在最小堆中 -O(log|V|)
    //每次只要把堆的根节点弹出来，然后调整这个最小堆，每次获得最小距离的这一步操作是一个logv的时间复杂做的算法，比前面OV快多了
    //更新dist[W]的值 -O(log|V|)变成稍微复杂的事情，不仅要把值更新，还得把它插回那个最小堆里面去
    //总体复杂度：T = O(|V|log|V| + |E|log|V|)，这个堆稀疏图效果好，如果是稠密图的话可以将复杂度写成elogv
    稀疏图：e跟V是同一个数量级的，不是V平方数量级的，复杂度就是vlogv
```

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220802000630555.png" alt="image-20220802000630555" style="zoom:50%;" />



### 7.1.3-s 有权图的单源最短路示例

```c
void Dijkstra( Vertex s )
{
    while(1){
        V = 未收录顶点中dist最小者;
        if( 这样的v不存在 )
            break;
        collected[V] = true;
        for( V的每个邻接点W )
            if( collected[W] == false )
                if( dist[V] + E<v,w> < dist[W] ){//小于正无穷就执行，dist[W]还未经过的点是默认为正无穷
                    dist[W] = dist[V] + E<v,w>;
                    path[W] = V;
                }
    }
}

下图中的dist表示源点到目标点之间的权重，path表示当前点的上一个点的下标
```

刚开始：<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220802135358543.png" alt="image-20220802135358543" style="zoom:50%;" />正式进入Dijkstra算法：<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220802135529500.png" alt="image-20220802135529500" style="zoom:50%;" />进行邻接点排除前：<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220802140507807.png" alt="image-20220802140507807" style="zoom:50%;" />

排除完后：<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220802140800630.png" alt="image-20220802140800630" style="zoom:50%;" />

### 7.1.4 多源最短路算法

方法1：直接将单源最短路算法调用|V|遍

1. <img src="C:\Users\XiaoYu\AppData\Roaming\Typora\typora-user-images\image-20220802162534649.png" alt="image-20220802162534649" style="zoom:50%;" />对稀疏图效果好

方法2：Floyd算法

1. <img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220802162637428.png" alt="image-20220802162637428" style="zoom:50%;" />对稠密图效果好

#### Floyd算法

1. 只经过一部分顶点，只经过编号小于等于k的那些顶点
2. <img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220802163030611.png" alt="image-20220802163030611" style="zoom:50%;" />最初的D负一次方是定义为连接矩阵
3. <img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220802163007951.png" alt="image-20220802163007951" style="zoom:50%;" />
4. 如果i和j之间没有直接的边，D[i] [j]应该定义为：正无穷(不是0也不是负无穷)
5. <img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220802163903479.png" alt="image-20220802163903479" style="zoom:50%;" />
   1. 这个说人话就是加入k如果导致i到j的最短路径发生了变化(其实就是这个k的加入导致更短的啦)，那么i到k和k到j必定是两段最短的路径

**多源最短路算法**

```c
void Floyd()
{
    for( i = 0; i < N ; i++ )
        for( j = 0; j < N ; j++ ){
            D[i][j] = G[i][j];
            //path[i][j] = -1;这个二维数组是用来记录路径的，方便求最短路径，初始化为-1表示ij之间现在是没有路径的
        }
    for( k = 0; k < N ; k++ )
        for( i = 0; i < N ; i++ )
            for( j = 0; j < N ; j++ )
                if( D[i][k] + D[k][j] < D[i][j] ){
                    D[i][j] = D[i][k] + D[k][j];
                    //如果我们不仅要ij之间的最短距离，还要求输出他们两个之间的那个最短路径
                    //path[i][j] = k;当路径被更新之后，就意味着i到j就会经过k，我们就把k记在passi和j这个元素里
                    //这样记录的优势：很容易的把i到j之间的最短路径也打印出来，采用递归打印(i到k的路径，然后把k打出来，最后在递归的打印k到j的那段路径)出来
                    //i到j的最短路径：i到k的最短路径 + k + k到j的最短路径，三段组成
                }
}

//三重算法可以简单得到ford算法的时间 T = O(|V|³)
```



## 小白专场：哈利 波特的考试-C语言实现

### 小白-HP.1 题意理解

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220802215252987.png" alt="image-20220802215252987" style="zoom:50%;" />

```
样例第一列表示动物的个数(也就是顶点的个数)		第二列表示边的个数
D是最短距离的矩阵，记录从顶点i到顶点j之间的最短距离

Harry究竟应该带哪只动物去的问题的时候，首先检查每一行里面最大的那个数字，最大的那个数字代表着第一个动物变成最大数字的那个动物是最麻烦的，无穷符号指当前的那个动物

要带什么动物去会使我们最难变的动物变成最好变的呢？答案是动物4号，它最难变的咒语是70
```

### 小白-HP.2 程序框架搭建

```c
int main()
{
    读入图;
    分析图;
    return 0;
}
```

代码实际演示

```C
int main()
{
    //有两种图，一种邻接表的，一种邻接矩阵的。这里选择了邻接矩阵的方法来表示图
    Graph G = BuildGraph();
    FindAnimal( G );
    return 0;
}

//FindMaxDist(i)是最大距离，对第i个动物去求他所有最短距离里面的最大值，要求的是所有这些最大值里面的最小值
//FindMin是最小距离
```

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220802224222941.png" alt="image-20220802224222941" style="zoom:50%;" />

### 小白-HP.3 选择动物

```c
void FindAnimal(MGraph Graph)
{
    WeightType D[MaxVertexNum][MaxVertexNum],MaxDist,MinDist; 
    Vertex Animal, i;
    
    Floyd(Graph , D);
    
    //FindMin:从每个动物i的最短距离的最大值中，找到最小值MinDist,以及对应的动物编号Animal
    MinDist = INFINITY;
    for( i = 0;i<Graph->Mv;i++){
        MaxDist = FindMaxDist(D,i,Graph->Nv);//找到第i个动物它的最大的那个距离赋给MaxDist这个变量
    
    //还有一个特殊情况需要考虑：带一只动物去根本不可能，当图不连通的时候，图有不止一个连通集。该从哪里意识到图不连通了呢？
        if(MaxDist == INFINITY){//返回距离无穷大的话说明有i无法变出的动物
            printf("0\n");
            return;
        }
        
        
        if( MinDist > MaxDist ){//找到最长距离更小的动物
            MinDist = MaxDist; Animal = i + 1;//更新距离，记录编号。这里是i+1是因为动物编号从1开始，但是图的循环从0开始，所有需要加一
        }
    }      
    printf("%d %d\n",Animal,MinDist);
}
```

上述代码中涉及到封装起来的函数

```c
WeightType FindMaxDist(WeightType D[][MaxVertexNum],Vertex i,int N)
{
    WeightType MaxDist;
    Vertex j;
    
    MaxDist = O;//初始化为一个很小的数，比如0
    for(j = 0;j < N;j++ )//找出i到其他动物j的最长距离
        if( i != j && D[i][j]>MaxDist )//邻接矩阵所有的两个顶点之间的距离全部初始化为正无穷，所以对角元的值永远是正无穷。所以必须要把对角元排除掉跳过去，不排除的话这个无穷大必然会符合这个判断条件从而让MaxDist永远的返回正无穷
            MaxDist=[i][j];
    return MaxDist;
}
```



### 小白-HP.4 模块的引用与裁剪

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220803001249910.png" alt="image-20220803001249910" style="zoom:50%;" />

```c
#define MaxVertexNum 100//最大顶点数设置为100
#define INFINITY 65535//无穷大设为双字节无符号整数的最大值65535
typedef int Vertex//用顶点下标表示顶点，为整型
typedef int WeightType;//边的权值设为整型

//边的定义
typedef struct ENode *PtrToENode;
struct ENode{
    Vertex V1,V2;//有向边<V1,V2>
    WeightType Weight;//权重
};
typedef PtrToENode Edge;

//图结点的定义
typedef struct GNode *PtrToENode;
struct GNode{
    int Nv;//顶点数
    int Ne;//边数
    WeightType G[MaxVertexNum][MaxVertexNum];//邻接矩阵
    DataType Data[MaxVertexNum];//存顶点的数据
    //注意：很多情况下，顶点无数据，此时Data[]可以不出现
};
typedef PtrToENode MGraph;//以邻接矩阵存储的图类型	
```

#### CreateGraph原始代码

```c
MGraph CreateGraph(int VertexNum)
{/*初始化一个有VertexNum个顶点但没有边的图*/
	Vertex V,W;
	MGraph Graph;
    
	Graph=(MGraph)malloc(sizeof(struct GNode));/*建立图*/
	Graph->Nv VertexNum;
	Graph->Ne = 0;
/*初始化邻接矩阵*/
/*注意：这里默认顶点编号从0开始，到(Graph->Nv-1)*/
for (V=0; V<Graph->Nv; V++)
	for (W=0; W<Graph->Nv; W++)
		Graph->G[V][W] = INFINITY;

	return Graph;
}

void InsertEdge(MGraph Graph,Edge E)
{
/*插入边<V1,V2>*/
	Graph->G[E->V1][E->V2] = E->Weight;
/*若是无向图，还要插入边<V2,V1>   因为是无向图，所以我们需要把读进来的这个权重同时赋予矩阵里面两个元素*/
	Graph->G[E->V2][E->V1] = E->Weight;
```



```c
MGraph BuildGraph()
{
    MGraph Graph;
    Edge E;
    Vertex V;
    int Nv,i;
    
    scanf("%d",&Nv);//读入顶点个数
    Graph = CreateGraph(Nv);//初始化有Nv个顶点但没有边的图
    
    scanf("%d",&(Graph->Ne));//读入边数
    if(Graph->Ne != 0 ){//如果有边
        E = (Edge)malloc(sizeof(struct ENode));//建立边结点
        //读入边，格式为"起点 终点 权重"，插入邻接矩阵
    	for(i=0;i<Graph->Ne;i++){
            scanf("%d %d %d",&E->V1,&E->V2&E->Weight);//读进来的时候，编号是从1开始的
            //注意：；如果权重不是整型，Weight的读入格式要改
            E->V1--; E->V2--;//读进来的编号都-1，那就变成起始编号从0开始的了，原本1变成0，2变成1，依次变化
            InsertEdge(Graph,E);//插入边的时候，图默认顶点的编号是从0开始
        }
    }
    //如果顶点有数据的话，读入数据。没有的话这个for循环可以删掉
    for(V=0;V<Graph->Nv;V++)
        scanf(" %c",$(Graph->Data[V]));
     
    return Graph;
}
```

问：图的顶点从0开始编号，而本题目中动物从1开始编号。读输入时该如何处理使得图的相关模块可以顺利应用？

答案：E->V1--; E->V2--;



#### 标准的Floyd算法

```C
bool Floyd(MGraph Graph ,WeightType D[][MaxVertexNum],Vertex path[][MaxVertexNum])//返回布尔值(bool)
{
    Vertex i,j,k;
    //初始化
    for(i = 0;i<Graph->Nv;i++)
        for(j = 0;j<Graph->Nv;j++){
            D[i][j] = Graph->G[i][j];
            path[i][j] = -1;
        }
    
    for(k = 0;k <Graph->Nv;k++)
        for(i=0;i<Graph->Nv;i++)
            for(j=0;j<Graph->Nv;j++)
                if(D[i][k] + D[k][j] < D[i][j]){
                    D[i][j] = D[i][k] + D[k][j];
                    if( i == j && D[i][j]<0)//若发现负值圈，从i出发，走了一圈又回到i，这个最短距离是一个负的值，也对应了一个负值圈
                        return false;//不能正确解决，返回错误标记
                    path[i][j] = k;
                }
    return true;//算法执行完毕，返回正确标记
}
```

在本题中进行的改动部分：

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/925/image-20220803005320198.png" alt="image-20220803005320198" style="zoom:50%;" />
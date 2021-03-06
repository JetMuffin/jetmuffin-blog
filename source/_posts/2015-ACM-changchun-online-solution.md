title: 2015 ACM 长春赛区网赛题解 （部分）
tags: [数论,dfs,并查集,图论,lucas定理,中国剩余定理,优先队列,后缀数组]
categories: 算法题解
date: 2015-09-14 10:16:00
---

## 1001 Alisha’s Party (优先队列）[题目链接 HDOJ-5437](http://acm.hdu.edu.cn/showproblem.php?pid=5437)

### 题意

Alisha开party, 到了的好友在门外等候，Alisha会开m次门，每次开门按给定的条件`(t,p)->在第t个好友到达时候开门，并且放进p个人`，若门外等候的人少于p个，则会全部放进来；最后会开一次门把所有人放进来；每次放进的p个人，按礼品价值高低顺序进入；得到一个好友进入的序列，最后求序列中的某几个序号对应的人名。

<!-- more -->

### 思路

首先将所有的开门条件按时间排好序，然后用优先队列处理门外排队的人，循环模拟每个人到达的时间(i从1到k，第i时间时i-1号好友到达），将开门的计数器cnt置为0，然后当i等于某个开门条件时（即`i == open[k].t`)，将open[k].p个人出队并将他们的名字加入结果(vector)中。处理完所有的开门条件后，将队列中所有元素弹出，并加到结果中。

然后是这题的trick吧：

*   优先队列中，如果礼物价值相同，按时间顺序先进先出
*   m个开门条件(t,p)不一定是按顺序给的，要自行排序
*   所有m个开门条件结束以后，Alisha会开一次门把所有人放进去，也就是说当m=0(不开门)的时候，所有人按礼物价值降序进入

### 代码
```cpp
#include <iostream>
#include <cstdio>
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;
#define maxn 150005 
struct node{
    char name[205];  
    int w, t;  
    friend bool operator <(node a,node b)  
    {  
        if(a.w == b.w) return a.t > b.t;  
        return a.w < b.w;  
    }  
};
node fri[maxn];
struct node1{
    int t,p;
}open[maxn];
bool cmp(node1 a, node1 b){  
    return a.t < b.t;  
} 
int main(){
    int t, k, m, query;  
    //freopen("in.txt","r",stdin);
    scanf("%d", &t);  
    while(t--) {
        scanf("%d%d%d", &k, &m, &query);
        for(int i = 0 ; i < k; i++){
            scanf("%s %d",fri[i].name,&fri[i].w);
            fri[i].t = i;
        } 
        open[0].t = -1;
        for(int i = 0;i < m;i++)  
            scanf("%d%d", &open[i].t, &open[i].p);  
        priority_queue<node>q;  
        vector<int> ans;
        
        sort(open, open + m, cmp);
        int cnt = 0;
        for(int i = 1 ; i <= k; i++){
            q.push(fri[i-1]);
            if(i == open[cnt].t){
                //cout<<i<<endl;
                for(int j = 1; j <= open[cnt].p && (!q.empty()); j++){
                    node tmp = q.top();
                    //cout<<tmp.name<<endl;
                    q.pop();
                    ans.push_back(tmp.t);
                }
                cnt++;
            }
        }
        while(!q.empty()){
            node tmp = q.top();
            q.pop();
            ans.push_back(tmp.t);
        }
        int len = ans.size();
        for(int i = 0; i < query; i++){
            int x;
            scanf("%d",&x);
            printf("%s", fri[ans[x-1]].name);
            if(i == query-1)
               printf("\n");
            else
               printf(" ");  
        }
    }
}
```

## 1002 Ponds (DFS）[题目链接 HDOJ-5438](http://acm.hdu.edu.cn/showproblem.php?pid=5438)

### 题意

给定一个无向图，首先去掉度数小于2的顶点（**是不断删去，删去一个顶点可能会再次出现需要删除的顶点，要删到直到不再出现为止**)，然后对剩下的图计算其每个连通分量的顶点数，若顶点数为奇数，则将其权值加到最后的结果，输出到最后的结果。

*   trick:
需要不断删去不符合节点直到不存在不符合节点为止，如下图情况：
![1002](http://i1.tietuku.com/7ef99cd19149a4c2.png)
图1中所有顶点都会被去掉，图2中只剩下1,2,3

### 思路

首先用队列处理删点操作，先将所有符合删除条件的点入队，然后从队列中每弹出一个顶点，将其所有相邻顶点度数-1，若再次出现符合删除条件，则将新点也入队，直到队列为空。（注意用访问标记减少重复访问）；然后用DFS去遍历整个图，每访问完一个连通子图计算权值和并判断节点数。

*   复杂度O(|V|)

### 代码

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <queue>
using namespace std;
#define LL long long
const int maxn = 10010;
vector <int> g[maxn];
LL w[maxn];
int vis[maxn];
int deg[maxn];
LL ans = 0;
LL sum = 0;
int cnt = 0;
void dfs(int x){
    if(vis[x])
        return;
    vis[x] = 1;
    int len = g[x].size();
    // cout<<x<<" "<<sum<<" "<<cnt<<endl;
    sum += w[x];
    cnt++;
    for(int i = 0; i < len; i++){
        int v = g[x][i];
        if(!vis[v]){
            dfs(v);
        }
    }
}
void work(int n){
    queue<int> q;
    for(int i = 1; i <= n; i++){
        if(deg[i] < 2){
            vis[i] = 1;
            q.push(i);
        }   
    }
    while(!q.empty()){
        int u = q.front();
        q.pop();
        int len = g[u].size();
        for(int i = 0 ; i < len; i++){
            int v = g[u][i];
            if(!vis[v]){
                deg[v] --;
                if(deg[v] < 2){
                    vis[v] = 1;
                    q.push(v);
                }                
            }
        }
    }

}
int main(){
    int t;
    scanf("%d",&t);
    while(t--){
        int n,m;
        memset(vis, 0 , sizeof(vis));
        memset(deg,0, sizeof(deg));
        memset(g,0, sizeof(g));
        ans = 0;
        scanf("%d%d",&n,&m);
        for(int i = 1; i <= n; i++)
            scanf("%lld",w+i);
        for(int i = 1; i <= m; i++){
            int u,v;
            scanf("%d%d",&u,&v);
            g[u].push_back(v);
            g[v].push_back(u);
            deg[u]++;
            deg[v]++;
        }
        work(n);
        // for(int i = 1; i <= n; i++)
        //     cout<<vis[i]<<" ";
        // cout<<endl;
        for(int i = 1; i <= n; i++){
            if(!vis[i]){
                cnt = 0;
                sum = 0;
                dfs(i);
                if(cnt%2 == 1)
                    ans += sum;
            }
        }
        printf("%lld\n",ans);
    }
}
```
   

## 1005 Travel(并查集） [题目链接 HDOJ-5441](http://acm.hdu.edu.cn/showproblem.php?pid=5441)

之前没有做过并查集处理图的题目，该题之后上题解

## 1006 Favorite Donut（后缀数组）[题目链接 HDOJ-5442](http://acm.hdu.edu.cn/showproblem.php?pid=5442)

这题据说比较裸的后缀自动机，之后上题解

## 1007 The Water Problem (水题）[题目链接 HDOJ-5443](http://acm.hdu.edu.cn/showproblem.php?pid=5443)

签到题，没什么好说的，区间最大值，不需要RMQ，线段树什么的，直接O(nq）就能过，很好的手速题，5min看完题8min交题ORZ~

```cpp
#include <iostream>
#include <cstdio>
using namespace std;
const int maxn = 1005;
int a[maxn];
int main(){
    int t;
    scanf("%d",&t);
    while(t--){
        int n;
        scanf("%d",&n);
        for(int i = 1 ; i <= n; i++)
            scanf("%d",a+i);
        int q;
        scanf("%d",&q);
        for(int i = 1 ; i <= q; i++){
            int l,r;
            scanf("%d%d",&l,&r);
            int max = -1;
            for(int j = l; j<=r; j++){
                if(a[j] > max )
                    max = a[j];
            }
            printf("%d\n",max);
        }
    }
}
```
    

## 1008 Elven Postman (二叉排序树）[题目链接 HDOJ-5444](http://acm.hdu.edu.cn/showproblem.php?pid=5444)

### 题意

题目描述了一种房间的排序方式，又讲了给定的序列怎么来的，讲了一大堆，结果发现这树就是一颗倒着的二叉排序树（二叉搜索树），大的数放左节点，小的树放右节点。

### 思路

给定一个序列可以构造出一颗BST，然后根据查询条件去查找就行了，向左递归输出W，向右递归输出E。

复杂度：查询`O(log(q))`

*   trick:
按题意，给定的序列长度是1000，也就是说树的深度是1000，若用数组模拟，需要2^1000大小的数组，必然RE，因此要用指针做。

### 代码
```cpp
#include <iostream>
#include <cstdio>
#include <cstdlib>
 using namespace std;
 bool first;
  
 struct BST{
      int data;
      BST *leftchild;
      BST *rightchild;
 };
 
void Build(BST *&root,int x){
     if(root==NULL){
         root=(BST *)malloc(sizeof(BST));
         root->data=x;
         root->leftchild=root->rightchild=NULL;
     }else {
         if(x<root->data){
             Build(root->leftchild,x);
         }else 
             Build(root->rightchild,x);
     }
 }
 
 void Search(int v,BST *root){
    if(root == NULL){
        return;
    }else if(root->data > v){
        printf("E");
        Search(v, root->leftchild);
    }else if(root->data <v){
        printf("W");
        Search(v, root->rightchild);
    }else{
        return;
    }
 }
 
 int main(){
    int t;
    scanf("%d",&t);
    while(t--){
        BST *root=NULL;
        int n;
        scanf("%d",&n);
         for(int i=1;i<=n;i++){
             int x;
             scanf("%d",&x);
             Build(root,x);
         }
        int q;
        scanf("%d",&q);
        for(int i = 0; i < q; i++){
            int t;
            scanf("%d",&t);
            Search(t,root);
            printf("\n");
        }
    }
}
```

## 1010 Unknown Treasure(Lucas定理+中国剩余定理+按位乘法)[题目链接 HDOJ-5446](http://acm.hdu.edu.cn/showproblem.php?pid=5446)

### 题意

组合数取模，求C(n,m)mod(p1_p2_...pk)

### 思路

先使用Lucas定理求出对于每个pi，C(n, m) % pi的值。再使用中国剩余定理对模数和余数求解即可。

*   trick:
在用中国剩余定理的过程中，会出现long long * long long 结果爆掉了的情况，这个是导致WA的问题。。所以要手写一个按位乘法

### 代码
```cpp
#include <iostream>

using namespace std;

long long n,m,k;
long long lcm;
long long remainer[20];
long long divider[20];

long long extend_gcd(long long a,long long b,long long &x,long long &y)
{
    if (a==0 && b==0)
       return -1;
    if (b==0)
    {
       x=1;
       y=0;
       return a;
    }
    long long ans=extend_gcd(b,a%b,x,y);
    long long temp=x;
    x=y;
    y=temp-a/b*y;
    return ans;
}


long long mod_reverse(long long a,long long d_p)
{
    long long x,y;
    long long ans=extend_gcd(a,d_p,x,y);
    if (ans!=1)
       return -1;
    else
       return (x%d_p+d_p)%d_p;
}

long long mul(long long a, long long b, long long mod) {  
    a = (a % mod + mod) % mod;  
    b = (b % mod + mod) % mod;  
  
    long long ret = 0;  
    while(b){  
        if(b&1){  
            ret += a;  
            if(ret >= mod) ret -= mod;  
        }  
        b >>= 1;  
        a <<= 1;  
        if(a >= mod) a -= mod;  
    }  
    return ret;  
}  

long long crt(long long lcm)
{
    long long ans=0;
    for (long long i=0;i<k;i++)
    {
        long long others;
        others=lcm/divider[i];
        long long mr=mod_reverse(others,divider[i]);
        //ans = (ans+remainer[i]*others*mr)%lcm;
        ans = (ans + mul(mul(remainer[i],others,lcm),mr,lcm));
    }
    return (ans+lcm)%lcm;
}

long long mod_pow(long long a,long long n_,long long p)
{
    long long ret=1;
    long long A=a;
    while(n_)
    {
        if (n_ & 1)
            ret=(ret*A)%p;
        A=(A*A)%p;
        n_>>=1;
    }
    return ret;
}

long long factorial[100010];

void init(long long p)
{
    factorial[0] = 1;
    for(long long i = 1;i <= p;i++)
        factorial[i] = factorial[i-1]*i%p;
}

long long Lucas(long long a,long long k_,long long p)
{
    long long re = 1;
    while(a && k_)
    {
        long long aa = a%p;
        long long bb = k_%p;
        if(aa < bb) return 0;
        re = re*factorial[aa]*mod_pow(factorial[bb]*factorial[aa-bb]%p,p-2,p)%p;
        a /= p;
        k_ /= p;
    }
    return re;
}

int main()
{
    int T;
    //freopen("in.txt","r",stdin);
    long long p;
    cin>>T;
    while(T--)
    {
        lcm = 1;
        cin>>n>>m>>k;
        for (long long i=0;i<k;i++)
        {
            cin>>p;
            init(p);
            divider[i] = p;
            lcm = lcm*divider[i];
            remainer[i] = Lucas(n,m,p);
        }
        cout<<crt(lcm)<<endl;
    }
    return 0;
}

```
   
title: 编程之美初赛第三题 质数相关 小数据解法
tags: [编程之美,模拟,质数]
categories: 算法题解
date: 2015-04-25 23:30:10
---

<div class="limit"><div>时间限制:2000ms</div><div>单点时限:1000ms</div><div>内存限制:256MB</div></div><div>

### 描述

两个数a和 b (a<b)被称为质数相关，是指a × p = b，这里p是一个质数。一个集合S被称为质数相关，是指S中存在两个质数相关的数，否则称S为质数无关。如{2, 8, 17}质数无关，但{2, 8, 16}, {3, 6}质数相关。现在给定一个集合S，问S的所有质数无关子集中，最大的子集的大小。

### 输入

<!--more-->

第一行为一个数T，为数据组数。之后每组数据包含两行。

第一行为N，为集合S的大小。第二行为N个整数，表示集合内的数。

### 输出

对于每组数据输出一行，形如"Case #X: Y"。X为数据编号，从1开始，Y为最大的子集的大小。

### 数据范围

1 ≤ T ≤ 20

集合S内的数两两不同且范围在1到500000之间。

小数据

1 ≤ N ≤ 15

大数据

1 ≤ N ≤ 1000

</div><dl class="des"><dt>样例输入</dt><dd><pre>3
5
2 4 8 16 32
5
2 3 4 6 9
3
1 2 3

小数据解法：

模拟选择每个组合，判断其中每两个子元素做商是否为素数，小数据不会超时，大数据要通过图来做，之后提供。

```cpp
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#include <cmath>
using namespace std;
#define maxn 500001

int f[15];
vector<int> v;
vector<int>::iterator it;
vector<int>::iterator itj;

int ans = 0;
bool prime[maxn];
void IsPrime(){
    prime[0]=prime[1]=0;prime[2]=1;
    
    for(int i=3;i<maxn;i++)
        prime[i]=i%2==0?0:1;
        int t=(int)sqrt(maxn*1.0);

        for(int i=3;i<=t;i++)
            if(prime[i])
                for(int j=i*i;j<maxn;j+=2*i)//优化
                    prime[j]=0;
}

void output(){
    for(it = v.begin();it!=v.end();it++){
        cout<<*it<<" ";
    }
    cout<<endl;
}

int check(){
    if(v.size() == 1)
        return 1;

    for(it = v.begin();it!=v.end()-1;it++){
        for(itj = v.begin()+1;itj!=v.end();itj++){
            int x = *it;
            int y = *itj;
            //cout<<x<<" "<<y<<endl;

            if(y%x==0){
            //     cout<<"sub:"<<y/x<<endl;
                if(prime[y/x])
                    return 0;
            }
        }
    }
    return 1;
}

int solve(int count){
    for(int i = 1; i < (int)1<<count; i++){
        v.clear();
        for(int j = 0; j < count; j++){
            if(i & (1<<j)){
                //cout<<i<<" "<<j<<endl;
                v.push_back(f[j]);
            }
        }

        //output();
        if(check()){
            if(v.size() > ans)
                ans = v.size();
        }
    }
}

int main() {
    int T;
    cin>>T;
    for(int cnt = 0 ; cnt < T; cnt ++){
        ans = 0;
        int n;
        cin>>n;
        for(int i = 0 ; i < n ; i++)
            cin>>f[i];
        sort(f,f+n);
        IsPrime();
        solve(n);
        cout<<"Case #"<<cnt+1<<": "<<ans<<endl;
    }
    return 0;
}

```
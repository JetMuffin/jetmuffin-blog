title: hdu 2098 分拆素数和 水题
tags: [水题]
categories: 算法题解
date: 2014-10-12 19:02:27
---

偶尔一水，打个素数表，再正向打ans表。

<!--more-->

代码：

```cpp
#include<iostream>  

#include<math.h>  

#define N 1231  

using namespace std;  

int prime[N];   

int ans[N*N];  

void makeprime()  

{  

    int j,n=29,i=9,sqrtn;  

    prime[0]=2;  

    prime[1]=3;  

    prime[2]=5;  

    prime[3]=7;  

    prime[4]=11;  

    prime[5]=13;  

    prime[6]=17;  

    prime[7]=19;  

    prime[8]=23;  

    while (i<N)   

    {  

        j=0;   

        sqrtn=(double)sqrt((double)n);  

        while (prime[j]<=sqrtn)  

        {  

            if (n%prime[j]==0)break;   

            j++;  

        }  

        if (prime[j]>sqrtn)  

        {  

            prime[i]=n;   

            i++;  

        }  

        n+=2;  

    }  

}  

int main()  

{  

    makeprime();  

    for(int i=0;i<N;i++)  

        for(int j=i+1;j<N;j++)  

        {  

            if(prime[i] == prime[j]) continue;  

            else  

                ans[prime[i]+prime[j]]++;  

        }  

    int n;  

    while(cin>>n)  

    {  

        if(!n) break;  

        cout<<ans[n]<<endl;  

    }             

}
```
title: 大数乘法逆元求法总结
tags: [数论]
date: 2015-09-01 10:46:00
categories: 算法分析
---

## 大数的乘法逆元
<!-- more -->

1.  暴力法

```cpp
int i;  
   for (i=1;;i++) { if (((long long int)(n)*i-an)%M==0) break; }  
```

2.  欧拉函数
```cpp
long long inv( long long n )  
{  
    return pow( n, M - 2 )%M;  
}  
```

3.  拓展欧几里得算法
```cpp
//扩展欧几里德  
void exp_gcd( LL a ,LL b,LL &x,LL &y) {  
     if( b == 0 ) {  
         x = 1;  
         y = 0;  
     }  
     else {  
          exp_gcd( b,a%b,x,y );  
          LL t;  
          t = x;  
          x = y;  
          y = t - a/b*y;  
     }  
}  
//逆元  
inline LL getNN(LL x) {  
        LL now , t;  
        exp_gcd( x, M,now,t );  
        return (now%M+M)%M;  
}  
```

4.  拓展欧几里得（简洁版）
```cpp
int64 inv(int64 x) {    
    //简洁版求逆元    
    if(x == 1) return 1;    
    return  inv(MOD%x) * (MOD - MOD/x) % MOD;    
} 
```  
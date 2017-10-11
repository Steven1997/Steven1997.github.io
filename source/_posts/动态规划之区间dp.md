---
title: 动态规划之区间dp
date: 2017-10-11 16:11:11
tags: 算法
---
### Hello
### 简介  
区间dp，顾名思义就是在一段区间上进行动态规划。对于每段区间，他们的最优值都是由几段更小区间的最优值得到，是分治思想的一种应用，将一个区间问题不断划分为更小的区间直至一个元素组成的区间，枚举他们的组合 ，求合并后的最优值。  
 ### 算法结构  
设F[i,j]（1<=i<=j<=n）表示区间[i,j]内的数字相加的最小代价  
每次用变量k（i<=k<=j-1）将区间分为[i,k]和[k+1,j]两段  

For l:=2 to n do // 枚举区间长度   
for i:=1 to n do // 枚举区间的左端点  
begin  
j:=i+l-1; // 计算区间的右端点,因为区间长度和左端点循环嵌套枚举，保证了[i,j]内的所有子区间都被枚举到  
if j>n then break; // 保证了下标不越界
for k:= i to j-1 do // 状态转移，去推出 f[i,j]  
f[i , j]= max{f[ i,k]+ f[k+1,j]+ w[i,j] }   
end;   

这个结构必须记好，这是区间动态规划的代码结构。  

### 例题  

### 石子合并  
题目链接:<http://acm.nyist.net/JudgeOnline/problem.php?pid=737>

题意:有N堆石子排成一排，每堆石子有一定的数量。现要将N堆石子并成为一堆。合并的过程只能每次将相邻的两堆石子堆成一堆，每次合并花费的代价为这两堆石子的和，经过N-1次合并后成为一堆。求出总的代价最小值。 

分析:要求n个石子归并，我们根据dp的思想划分成子问题，先求出每两个合并的最小代价，然后每三个的最小代价，依次知道n个。
定义状态dp[i][j]为从第i个石子到第j个石子的合并最小代价。
那么dp[i][j] = min(dp[i][k] + dp[k+1][j])
那么我们就可以从小到大依次枚举让石子合并，直到所有的石子都合并。
这个问题可以用到平行四边形优化，用一个s[i][j]=k 表示区间 i---j 从k点分开才是最优的，这样的话我们就可以优化掉一层复杂度，变为O（n^2）  

代码1(无优化)  
 ```cpp
    #include <cstdio>
    #include <cstring>
    #include <algorithm>
    #define N 210
    int dp[N][N],sum[N];
    int main()
    {
        int n;
        while(~scanf("%d",&n))
        {
            int a[N];sum[0]=0;
            for(int i=1;i<=n;i++){
                scanf("%d",&a[i]);
                sum[i]=sum[i-1]+a[i];//因为要求解区间和，先维护前缀和
            }
            memset(dp,0,sizeof(dp));
            int i,j,l,k;
            for(l = 2; l <= n; ++l)//枚举区间长度
            {
                for(i = 1; i <= n - l + 1; ++i)//枚举区间左端点
                {
                    j = i + l - 1;//根据左端点和区间长度求区间右端点
                    if(j > n)
                    break;
                    dp[i][j] = 0x3f3f3f3f;
                    for(k = i; k < j; ++k)
                    {
                        dp[i][j] = min(dp[i][j],dp[i][k] + dp[k + 1][j] + sum[j] - sum[i-1]);
                    }
                }
            }
            printf("%d\n", dp[1][n]);
        }
        return 0;
    }
 ```   



代码2(平行四边形优化)    
 ```cpp
    #include <cstdio>
    #include <cstring>
    #include <algorithm>
    #define N 210
    int dp[N][N],sum[N],s[N][N];
    int main()
    {
        int n;
        while(~scanf("%d",&n))
        {
            int a[N];sum[0]=0;
            memset(s,0,sizeof(s));
            for(int i=1;i<=n;i++){
                scanf("%d",&a[i]);
                s[i][i]=i;
                sum[i]=sum[i-1]+a[i];//因为要求解区间和，先维护前缀和
            }
            memset(dp,0,sizeof(dp));
            int i,j,l,k;
            for(l = 2; l <= n; ++l)//枚举区间长度
            {
                for(i = 1; i <= n - l + 1; ++i) //枚举区间左端点
                {
                    j = i + l - 1;//根据左端点和区间长度求区间右端点
                    if(j > n)
                    break;
                    dp[i][j] = 0x3f3f3f3f;
                    for(k = s[i][j-1]; k <= s[i+1][j]; ++k)//四边形优化
                    {
                        if(dp[i][j]>dp[i][k] + dp[k + 1][j] + sum[j] - sum[i-1])
                             {
                                    dp[i][j]=dp[i][k] + dp[k + 1][j] + sum[j] - sum[i-1];
                                    s[i][j]=k;
                             }
                    }
                }
            }
            printf("%d\n", dp[1][n]);
        }
        return 0;
    }
 ```  


### 括号匹配  
题目链接：<http://poj.org/problem?id=2955>  

题意:给出一串的只有‘（’ ‘）’  '[‘  ']'四种括号组成的串，让你求解需要最少添加括号数让串中的所有括号完全匹配。  

分析：  
定义dp [ i ] [ j ] 为串中第 i 个到第 j 个括号的最大匹配数目   
1.如果第 i 个和第 j 个匹配,则dp [ i ] [ j ] = dp [ i+1 ] [ j-1 ] + 2 ;  
2.如果第 i 个和第 j 个不匹配，枚举中间分割点k(i <= k < j)
dp[ i ] [ j ] = max ( dp [ i ] [ j ] , dp [ i ] [ k ] + dp [ k+1 ] [ j ] )   

代码  
```cpp  
    #include <iostream>
    #include <cstring>
    #include <algorithm>
    #include <string>
    using namespace std;
    const int  N = 120;
    int dp[N][N];

    int main()
    {
        string s;
        while(cin>>s)
        {
            if(s=="end")
                break;
            memset(dp,0,sizeof(dp));
            int n = s.size();
            for(int len = 2;len <= n;len++)//枚举区间长度
            {
                for(int i = 0;i <= n - len; i++)//枚举区间左端点
                {
                    int j = i + len - 1;//确定区间右端点
                    if(j > n)
                    break;
                    if(s[i]=='('&&s[j]==')' || s[i]=='['&&s[j]==']')
                        dp[i][j]=dp[i+1][j-1]+2;
                    for(int k=i;k<j;k++)
                        dp[i][j]=max(dp[i][j],dp[i][k]+dp[k+1][j]);//枚举中间位置,注意j不取等号
                }
            }
            cout<<dp[0][n-1]<<endl;
        }
        return 0;
    }
```   


如果要求打印路径，即输出匹配后的括号  

题目链接: <http://poj.org/problem?id=1141>  

代码:  
```cpp
    #include <string>
    #include <iostream>
    #include <cstdio>
    #include <algorithm>
    #include <cstring>
    using namespace std;
    const int  N = 120;
    int dp[N][N],pos[N][N];   /*定义pos【i】【j】表示 i 到 j 从哪儿分开使得匹配添加括号最少，如果i和j匹配我们可以让pos【i】【j】=-1；*/
    string s;
    void show(int i,int j)
    {
        if(i>j)  return;
        if(i==j)
        {
            if(s[i]=='('||s[i]==')') cout<<"()";
            else      cout<<"[]";
        }
        else
        {
            if(pos[i][j]==-1)
            {
                cout<<s[i];
                show(i+1,j-1);
                cout<<s[j];
            }
            else
            {
                show(i,pos[i][j]);
                show(pos[i][j]+1,j);
            }
        }
    }
    int main()
    {

        while(cin>>s)
        {
            memset(dp,0,sizeof(dp));
            int len=s.size();
            for(int i=1; i<len; i++)
            {
                for(int j=0,k=i; k<len; j++,k++)
                {
                    if(s[j]=='('&&s[k]==')' || s[j]=='['&&s[k]==']')
                    {
                        dp[j][k]=dp[j+1][k-1]+2;
                        pos[j][k]=-1;
                    }
                    for(int f=j; f<k; f++)
                    {
                        if(dp[j][f]+dp[f+1][k]>=dp[j][k])
                        {
                            dp[j][k]=dp[j][f]+dp[f+1][k];
                            pos[j][k]=f;
                        }
                    }
                }
            }

            show(0,len-1);
            cout<<endl;
        }
        return 0;
    }
```

### 整数划分  
题目链接：<http://acm.nyist.net/JudgeOnline/problem.php?pid=746>  

题意:  给出两个整数 n , m ,要求在 n 中加入m - 1 个乘号，将n分成m段，求出这m段的最大乘积  

分析: 区间dp，设dp[i][j] 表示在区间[0, i]之中，插入j个乘号可以得到的最大数
设a[i][j]为区间[i,j]所形成的数
所以 dp[i][j] = max(dp[k][j-1] * a[k + 1][i])  

代码:  
```cpp
    #include <cmath>
    #include <cstdio>
    #include <cstring>
    #include <iostream>
    #include <algorithm>

    using namespace std;

    long long dp[25][25];
    long long a[25][25];
    char str[25];

    int main()
    {
        int len, t, m;
        scanf("%d", &t);
        while (t--)
        {
            scanf("%s%d", str, &m);
            len = strlen(str);
            m--;
            memset (a, 0, sizeof(a));
            memset (dp, 0, sizeof(dp));
            for (int i = 0; i < len; i++)          //先对a进行预处理，减少复杂度，a[i][j]表示第i段到第j段的数值
            {
                a[i][i] = str[i] - '0';
                for (int j = i + 1; j < len; j++)
                {
                    a[i][j] = a[i][j - 1] * 10 + str[j] - '0';
                }
            }
            for (int i = 0; i < len; i++)
            {
                dp[i][0] = a[0][i];
            }
            for (int j = 1; j <= m; j++)
            {
                for (int i = j; i < len; i++)
                {
                    for (int k = 0; k < i; k++)
                    {
                        dp[i][j] = max(dp[i][j], dp[k][j - 1] * a[k + 1][i]);
                    }
                }
            }
            printf("%lld\n", dp[len - 1][m]);
        }
        return 0;
    }
```  

### Halloween Costumes  
题目链接：<http://lightoj.com/login_main.php?url=volume_showproblem.php?problem=1422>  

题意：给你n天需要穿的衣服的样式，每次可以套着穿衣服，脱掉的衣服就不能再穿了，问至少要带多少条衣服才能参加所有宴会  

分析：首先我们使用dp[a][b]来表示区间 a~b 的答案，那么对于第 i 件衣服，我们有

①：如果在之后的区间内都不再重复利用这件衣服，那么明显  dp[i][j] = dp[i+1][j] + 1;

②：如果在之后的区间 i+1 ~ j 中存在一件衣服 k 是跟 i 一样的，那么我们便可以考虑是不是可以将i那件衣服在k这个地方重复利用，
那么转移方程为  dp[i][j] = min(dp[i][j] , dp[i][k-1]+dp[k+1][j])  

代码:
```cpp
    #include<cstdio>
    #include<algorithm>

    using namespace std;

    int n;
    int a[105];
    int dp[105][105];

    int main(void)
    {
        int t;
        int cas = 0;
        scanf("%d",&t);
        while(t--)
        {
            cas ++;
            scanf("%d",&n);
            for(int i = 1;i <= n;i++) scanf("%d",&a[i]);

            for(int i = 1;i <= n;i++)
            {
                for(int j = i;j <= n;j++)
                {
                    dp[i][j] = j-i+1;
                }
            }

            for(int i = n-1;i >= 1;i--)
            {
                for(int j = i+1;j <= n;j++)
                {
                    dp[i][j] = dp[i+1][j] + 1;
                    for(int k = i+1;k <= j;k++)
                    {
                        if(a[i] == a[k])
                        {
                            dp[i][j] = min(dp[i][j],dp[i][k-1] + dp[k+1][j]);
                        }
                    }
                }
            }

            printf("Case %d: %d\n",cas,dp[1][n]);
        }
        return 0;
    }
```  

### Cheapest Palindrome
题目链接:<http://poj.org/problem?id=3280>  

题意:给你m个字符，其中有n种字符，每种字符都有两个值，分别是增加一个这样的字符的代价，删除一个这样的字符的代价，让你求将原先给出的那串字符变成回文串的最小代价。  

分析:dp[i][j]代表区间i到区间j成为回文串的最小代价，那么对于dp[i][j]有三种情况：

1、dp[i+1][j]表示区间i到区间j已经是回文串了的最小代价，那么对于s[i]这个字母，我们有两种操作，删除与添加，对应有两种代价，dp[i+1][j]+add[s[i]],dp[i+1][j]+del[s[i]]，取这两种代价的最小值；

2、dp[i][j-1]表示区间i到区间j-1已经是回文串了的最小代价，那么对于s[j]这个字母，同样有两种操作，dp[i][j-1]+add[s[j]],dp[i][j-1]+del[s[j]]，取最小值

3、若是s[i]==s[j]，dp[i+1][j-1]表示区间i+1到区间j-1已经是回文串的最小代价，那么对于这种情况，我们考虑dp[i][j]与dp[i+1][j-1]的大小

然后dp[i][j]取上面这些情况的最小值  

代码  
```cpp
    #include<cstdio>
    #include<algorithm>
    #include<cstring>
    using namespace std;
    int dp[2005][2005],add[27],del[27];
    char s[2005];

    int main()
    {
        int n,m;
        while(scanf("%d%d",&n,&m)>0)
        {
            scanf("%s",s+1);
            for(int i=1;i<=n;i++)
            {
                char ch[10];
                int tmp1,tmp2;
                scanf("%s%d%d",ch,&tmp1,&tmp2);
                add[ch[0]-'a'+1]=tmp1;
                del[ch[0]-'a'+1]=tmp2;
            }
            memset(dp,0,sizeof(dp));
            for(int i=m-1;i>=1;i--)
            {
                for(int j=i+1;j<=m;j++)
                {
                    dp[i][j]=min(dp[i+1][j]+add[s[i]-'a'+1],dp[i+1][j]+del[s[i]-'a'+1]);
                    int tmp=min(dp[i][j-1]+add[s[j]-'a'+1],dp[i][j-1]+del[s[j]-'a'+1]);
                    dp[i][j]=min(dp[i][j],tmp);
                    if(s[i]==s[j])
                    dp[i][j]=min(dp[i][j],dp[i+1][j-1]);
                }
            }
            printf("%d\n",dp[1][m]);
        }
        return 0;
    }
```
### Treats for the Cows
题目链接:<http://poj.org/problem?id=3186>  

题意:只能从一个序列的左右两端取数字，且取出的第i个数乘i,求乘积相加的最大值  

分析:设dp[i][j]为取到剩余区间为[i,j]的最大值，可能由d[i+1][j]或者d[i][j-1]转移而来
转移方程：dp[i][j]=max(dp[i+1][j]+p[i]*(n+i-j),dp[i][j-1]+p[j]*(n+i-j));  其中n-(j-i)是第几次取  

代码
```cpp
    #include<cstdio>
    #include<algorithm>
    #include<cstring>
    using namespace std;
    int p[2010];
    int dp[2010][2010];
    int n;

    int main()
    {
        while(scanf("%d",&n)!=EOF)
        {
            memset(dp,0,sizeof(dp));
            for(int i=1;i<=n;i++)
            {
                scanf("%d",&p[i]);
                dp[i][i]= n * p[i];
            }
            int ans=0;
            for(int i=n;i>=1;i--)
                for(int j=i;j<=n;j++)
            {
               dp[i][j]=max(dp[i+1][j]+p[i]*(n+i-j),dp[i][j-1]+p[j]*(n+i-j));
            }
            printf("%d\n",dp[1][n]);
        }
    }
```

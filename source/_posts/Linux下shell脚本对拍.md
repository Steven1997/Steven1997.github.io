---
title: Linux下shell脚本对拍
date: 2017-12-02 00:14:43
tags: Linux
---
在算法竞赛中，我们常常会遇到一道题一直WA的情况，这时我们不得不自己造样例，这会耗费大量时间，而且还不一定能快速确定样例的答案。所以，如果时间充足，可以写一个对拍程序。  
对拍程序需要四个文件：  
1.我们已经写好但WA的待测试文件，记为code.cpp  
2.一个纯暴力程序(复杂度高但能保证答案一定正确)，记为std.cpp  
3.一个数据生成器(用srand函数和rand函数产生随机数即可)，记为data.cpp  
4.一个用于比较输出的shell脚本,记为duipai.sh  
对拍程序，顾名思义，就是随机生成数据给两个程序分别跑一遍，看看对不对的上。  

我们以一个计算1到n的和的程序为例，我们都知道计算1到n的暴力方法是用循环加和，简便方法则是使用等差数列求和公式。  

下面是待测试程序code.cpp(等比数列求和,当然这里没有WA)  
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
  int n;
  cin>>n;
  cout<<n * (n + 1) / 2<<endl;
  return 0;
}
```
下面是暴力求和程序std.cpp  
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
  int n;
  cin>>n;
  int ans = 0;
  for(int i = 1;i <= n;i++){
   ans += i;
  }
  cout<<ans<<endl;
  return 0;
}
```

然后是随机数据生成器data.cpp  
```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
  srand(time(0));
  int n = 100;
  while(n--){
  int k = rand() % 10 + 1; //生成100个[1,10]的随机数
  cout<<k<<endl;
  }
 return 0;
}
```
rand函数和srand函数的用法有点忘了，参见：[有关rand()，srand()产生随机数学习总结](https://www.cnblogs.com/guihailiuli/p/4154416.html) 

最后是duipai.sh  
```sh
#!/bin/bash
while true; do
    ./data > data.in
    ./std <data.in >std.out
    ./code <data.in >code.out
    if diff std.out code.out; then
        printf "AC\n"
    else
        printf "Wa\n"
        exit 0
    fi
done
```
这个程序的意思是运行三个可执行文件，比较std.out code.out是否相同，相同输出"AC"，不相同输出错误信息，并输出"WA"且退出。  

下面开始跑对拍，先用g++编译三个cpp文件，`g++ code.cpp -o code`,`g++ std.cpp -o std`,`g++ data.cpp -o data`。`g++ code.cpp -o code`表示将code.cpp编译并连接生成一个名为code的可执行文件，如果不加 -o 选项，所有cpp文件都默认生成一个名为a.out的可执行文件。通过`./a.out`可以运行这个可执行文件,`./`表示当前目录。如果加上 -c 选项，如`g++ -c code.cpp -o code`则只编译不连接，会产生一个obj文件而不是exe文件。  

编译完成后，输入`sh duipai.sh`即得结果。
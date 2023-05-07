---
title: "C++"
date: 2023-04-29T20:02:28+08:00
draft: false
tags: [cpp]
categories: [cpp]
---

## Chapter-8 函数探幽
### 内联函数
常规函数调用是使程序跳到另一个地址并在函数结束时返回,来回跳跃并记录跳跃位置和将函数参数复制到堆栈（内存块）意味着需要一定的开销。  
内联函数是`编译器将使用相应的函数代码替换函数调用`。对于内联代码，程序无需跳到另一个位置执行代码，再跳回来。内联函数的运行
速度比常规函数稍快，但是代价需要占用更多的内存。如果程序在10个不同地方调用同一个内联函数，则该程序将包含函数代码的10个副本。

使用方法： 在函数声明或者定义加上关键词`inline`
```cpp
#include <iostream>
using namespace std;
inline double square(double x) { return x * x; }
int main(){
  double a = 3.0, b = 4.0;
  cout << "a = " << a << ", b = " << b << endl;
  cout << "square(a) = " << square(a) << ", square(b) = " << square(b) << endl;
}

// a = 3, b = 4
// square(a) = 9, square(b) = 16
```

### 引用变量
引用变量是引用已定义的变量的别名，主要是用作函数的参数,通过将引用变量作为参数，函数将`使用原始数据而不是其副本`。  

使用方法： 在变量名前加`&`
```cpp
#include <iostream>
using namespace std;
int main() {
  int rats = 101;
  int &rodents = rats;
  cout << "rats = " << rats << ", rodents = " << rodents << endl;
  rodents++;
  cout << "rats = " << rats << ", rodents = " << rodents << endl;
  cout << "rats address = " << &rats << endl;
  cout << "rodents address = " << &rodents << endl;
}

// rats = 101, rodents = 101
// rats = 102, rodents = 102
// rats address = 0x7ffc2f7211ec
// rodents address = 0x7ffc2f7211ec
```

#### 临时变量、引用参数和const
如果实参与引用参数不匹配，C++将生成临时变量。  

如果引用参数是const,则编译器将在以下情况生成临时变量：
- 实参的类型正确，但不是左值
- 实参的类型不正确，但可以转成为正确的类型。

```cpp
double refcube(const double &ra) {
    return ra * ra * ra;
}

/*
double side = 3.0;                          
double *pd = &side;                         
double &rd = side;                          
long edge = 5L;                             
double len[4] = {2.0, 5.0, 10.0, 12.0};     
double c1 = refcube(side);                  // ra is side
double c2 = refcube(lens[2]);               // ra is len[2]
double c3 = refcube(rd);                    // ra is rd is side
double c4 = refcube(*pd);                   // ra is *pd is side
double c5 = refcube(edge);                  // ra is temporary variable
double c6 = refcube(7.0);                   // ra is temporary variable
double c7 = refcube(side + 10.0);           // ra is temporary variable
*/
```

现在来看refcube()函数目的只是传递值，而不是修改他们，因此临时变量不会造成任何不利的影响，反而会让函数可以接受更多的参数种类。
> tips: 如果函数调用的参数不是左值或相应的const引用参数的类型，则C++将创建类型正确的匿名变量，将函数调用的参数的值传递给该匿名变量，并让参数来引用该变量。

将引用参数声明为const的理由有：
1. 使用const可以避免无意中修改数据的编程错误
2. 使用const函数能够处理const和非const实参，否则只能接受非const参数
3. 使用const引用使函数能够正确生成并使用临时变量

C++11新增了另一种引用----右值引用（rvalue reference）。这种引用可以指向右值，使用`&&`声名。
```
double && rref = std::sqrt(36.0);   // not allowed for double &
double j = 15.0;                    
double && jref = 2.0*j + 18.5;      // not allowed for double &
std::cout << rref << std::endl;     // display 6.0
std::cout << jref << std::endl;     // display 48.5
```

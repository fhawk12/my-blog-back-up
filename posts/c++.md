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


### 函数模板
函数模板是通用的函数描述，它们使用泛型来定义函数，其中泛型可用具体的类型（如int或double）替换。模板本身并不创建任何函数，只是告诉编译器如何定义函数。
使用方法：
```cpp
template <typename AnyType>
void Swap(AnyType &a, AnyType &b){
    AnyType tmp;
    tmp = a;
    a = b;
    b = tmp;
}

template <class AnyType>
void Swap(AnyType &a, AnyType &b){
    AnyType tmp;
    tmp = a;
    a = b;
    b = tmp;
}
```
> 类型名可以任意选择(这里为AnyType)只要遵守C++命名规则就即可，许多程序员都使用简单的名称，如T。
> 在C++98添加关键字typename之前，C++使用关键字class来创建模板（将typename 替换 为 class 即可）

##### 模板的局限性
假设有以下模板：
```cpp
template<class T>
void f(T a, T b){
    ...
}
```
如果T为数组`a = b`将不成立，同理如果T为结构体，`if(a > b)`也不成立。编写的模板函数很可能无法处理某些类型。

##### 显示具体化
为了解决模板的局限性，可以提供一个具体化函数定义-----显示具体化（explicit specialization）,其中包含所需要的代码，当编译器找到与函数调用匹配的具体化定义
时，将使用该定义，不再寻找模板。

***第三代具体化(ISO/ANSI C++标准)***
- 定于给定的函数名，可以有非模板函数，模板函数和显示具体化模板函数以及他们的重载版本。
- 显示具体化的原型和定义应以template<>打头，并通过名称指出类型。
- 具体化优于常规模板，而非模板函数优于具体化和常规模板。

```cpp
// non template function prototype
void Swap(job &, job &);

// template prototype
template<typename T>
void Swap(T &, T &);

// explicit specialization for the job type
template<> void Swap<job>(job &, job &);

int main(){
    double u, v;
    ...
    Swap(u, v); // use template
    job a, b;
    ...
    Swap(a, b); // use void Swap<job>(job &, job &)
}
```

##### 实例化和具体化
在代码中包含函数模板本身并不会生成任何函数定义，它只是用于生函数定义的方案。编译器使用模板为特定类型生成函数时，得到的是模板实例。（调用模板函数时）
例如函数调用Swap（u, v）导致编译器生成Swap（）实例，该实例类型使用int类型。这种实例化方式被称为`隐式实例化`，但现在C++还允许显示实例化(explicit instantation)，
这意味着可以`直接命令编译器创建特定的实例`。
```cpp
template void Swap<int>(int, int);            // explicit instantation

template <> void Swap(int &, int &);          // explicit specialization
template <> void Swap<int> (int &, int &);    // explicit specialization


template <typename T>
T Add(T a, T b){
    return a + b;
}

...
int m = 6;
double x = 10.2;
cout << Add<double> (x, m) << endl;           // explicit instantation 
```
> 隐式实例化和显示实例化和显示具体化统称为具体化（specialization）。

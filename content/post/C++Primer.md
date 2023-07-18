---
title: C++ Primer
date: 2022-02-18 20:24:04
categories:
- 书籍笔记
tags:
- C/C++
---
# C++ Primer

---

## 1.带符号类型和无符号类型

> 除去布尔型和扩展字符集以外，其他整型可划分为带符号的(signed)和无符号整型(unsigned)两种。带符号类型可以表示正数，负数和零，无符号类型仅能表示大于零的值。

举例

- int 类型所能表示的值范围为

  $-2^{32-1}=-2,147,483,648$到$ 2^{32-1}=2,147,483,648$

- 无符号int类型所能表示的值为$0$到$4,294,967,296$

## 2.类型转换

```cpp
bool b=42;//b为true
int i=b;//i的值为1
i=3.14;//i为3
double pi=i;//pi为3.0
```

```cpp
int i=42;
if(i){//i值为true
    i=0;
}
```

### 1.负数转无符号数

对于无符号数只能为大于等于零的数，所以对于负数需要转换为无符号数。

> 把负数转换为无符号数类似于直接给无符号数赋一个负值，结果等于这个负数加上无符号数的模。
>                                                                                         ——C++ Primer

从数学角度解释为：

    取模运算时，对于负数，应该加上被除数的整数倍，使结果大于或等于0后，再运算，计算公式如下，$Unsigned$表示转换后的无符号数，$Normal$表示需要转换的负数值，$x$表示该类型位数

$$
Unsigned=(Normal+2^{x})\%2^{x}
$$

## 3.变量声明与定义的关系

> 如果想声明一个变量而非定义，再变量名前添加关键字extern关键字

```cpp
extern int i;//声明i而非定义
int j;//声明j并定义j
```

> 任何包含了显式初始化的声明即成为定义。
> 再函数体内部，如果试图初始化一个由extern标记的变量，将引发错误

## 4.名字作用域

> 作用域是程序的一部分，在其中名字有其特定的含义。C++语言中大多数作用域都以花括号分隔

```cpp
#include <iostream>
using namespace std; 
int main(){
    int i=100,sum=0;
    for (int i = 0; i!=10; ++i)
    {
        /* code */
        sum+=i;
    }
    cout<<i<<" "<<sum<<endl;
    return 0;
}
```

```shell
100 45
```

for(1;2;3){4}循环的执行步骤为1->2->4->3->2->4....
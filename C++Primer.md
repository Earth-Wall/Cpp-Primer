# 第Ⅱ部分 C++标准库

## 第9章 顺序容器

【容器】是【某种类型变量的集合】。
【顺序容器】可以控制【顺序容器的元素】的【存储顺序】和【访问顺序】。
【这种顺序】和【顺序容器添加元素的位置】有关，和【元素的值】无关。
在【第11章】中，介绍【有序关联容器】和【无序关联容器】，【关联容器】根据【关联容器的元素的关键字】存储【关联容器的元素】。

【标准库】支持三种【容器适配器】，【容器适配器】改变了【容器的接口】。
在本章末尾介绍【适配器】。

### 9.1 顺序容器概述

【顺序容器】可以快速顺序访问【顺序容器的元素】。
【不同类型的顺序容器】的【同类型操作的性能】不同：

【不同类型的顺序容器】的【添加或删除元素操作的性能】不同。
【不同类型的顺序容器】的【随机访问元素操作的性能】不同。

###### 表9.1 顺序容器类型 P292

|              | array    | string     | vector   | deque                  | list     | forward_list |
| ------------ | -------- | ---------- | -------- | ---------------------- | -------- | ------------ |
| 原理         | 固定数组 | 动态字符串 | 动态数组 | vector<固定数组的指针> | 双向链表 | 单向链表     |
| 动态大小     | ×        | √          | √        | √                      | √        | √            |
| 访问方式     | 随机     | 随机       | 随机     | 随机                   | 双向顺序 | 单向顺序     |
| 尾插删       | ×        | 快         | 快       | 快                     | 快       | 快           |
| 头插删       | ×        | 慢         | 慢       | 快                     | 快       | 快           |
| 任意位置插删 | ×        | 慢         | 慢       | 慢                     | 快       | 快           |

除了【固定大小的array】之外，【其他的顺序容器】都可以高效灵活的管理【内存】。
【其他的顺序容器】可以添加和删除【容器的元素】，扩大和缩小【容器的大小】。
【顺序容器保存元素的方式】影响【顺序容器操作的效率】。
【顺序容器保存元素的方式】影响【顺序容器是否支持某种操作】。

比如，【string】和【vector】在【连续的内存】中保存【元素】。
因为【string】和【vector】在【连续的内存】中保存【元素】，所以可以使用【string和vector的元素的下标】快速计算【string和vector的元素的地址】。
但是【向string和vector的中间位置插入或删除元素的速度】慢。
因为【string】和【vector】在【连续的内存】中保存【元素】，所以在【一次插入或删除操作】之后，必须移动【插入或删除位置之后的所有元素】。
【string】和【vector】添加【元素】可能需要申请【比现在更大的内存】。
如果【string】和【vector】申请了【新的内存】，那么必须移动【所有元素】到【新申请的内存】。

【list和forward_list的设计目标】是【向list和forward_list的任何位置插入或删除元素的速度都快】。
因为【list和forward_list的设计目标】，所以【list】和【forward_list】不支持【随机访问元素】。
【list】和【forward_list】访问【某个元素】，只能遍历【整个list或forward_list】。
【list和forward_list消耗的额外内存】大于【vector、deque、array】。

【deque】更复杂。
【deque】支持【快速随机访问】，和【string、vector】类似。
【向deque的中间位置插入或删除元素的速度】慢，和【string、vector】类似。
但是【向deque的首尾位置插入或删除元素的速度】快，【向deque的首尾位置插入或删除元素的速度】和【向list和forward_list的任何位置插入或删除元素的速度】相当。

【C++11的标准库】增加了【forward_list】和【array】。
【array】比【内置数组】更安全、更简单。
【array的大小】固定，和【内置数组】类似。
因为【array的大小】固定，所以【array】不能添加或删除【array的元素】，改变【array的大小】。
【forward_list的设计目标】是【forward_list的性能和最好的手写的单向链表相当】。
因为【forward_list】保存或计算【forward_list的大小】会产生【最好的手写的单向链表没有的额外开销】，所以【forward_list】不支持【.size()】。
【其他的容器.size()的时间复杂度】是【常量】。

【C++11的标准库的容器】比【C++11之前】快，原因在【13.6节 P470】。
【C++11的标准库的容器的性能】和【同类型数据结构】一样好（通常会更好）。
【现代C++程序】应该使用【标准库的容器】，而不是【更原始的数据结构】，比如【内置数组】。

##### 确定使用哪种顺序容器

选择【顺序容器】的基本原则：

默认使用【vector】。
如果【元素的数量】多，【元素的大小】小，想要【元素占用的内存】少，那么不使用【list】和【forward_list】。
如果需要随机访问【元素】，那么使用【vector】和【deque】。
如果需要向【顺序容器的中间位置】插入或删除【元素】，那么使用【list】和【forward_list】。
如果需要向【顺序容器的首尾位置】插入或删除【元素】，不会向【顺序容器的中间位置】插入或删除【元素】，那么使用【deque】。
如果在【读取并保存输入的数据】时需要向【顺序容器的中间位置】插入【元素】，之后需要随机访问【元素】。
那么确认是否必须向【顺序容器的中间位置】插入【元素】。
如果可以避免向【顺序容器的中间位置】插入【元素】，那么在【读取并保存输入的数据】时使用【vector】，之后使用【标准库的sort()】【10.2.3节 P343】排序【vector】。
如果必须向【顺序容器的中间位置】插入【元素】，那么在【读取并保存输入的数据】时使用【list】，之后拷贝【list的元素】到【vector】。

如果既需要随机访问【元素】，又需要向【顺序容器的中间位置】插入或删除【元素】。
那么比较【list或orward_list访问元素消耗的性能】和【vector或deque向中间位置插入或删除元素消耗的性能】。
默认情况，选择【执行使用最多的操作最快的顺序容器】。
需要分别测试【两种顺序容器的性能】。

如果不确定使用哪种【顺序容器】，那么只使用【vector和list都支持的通用的操作】，只使用【迭代器】，不使用【下标】，避免【随机访问】。
之后可以方便的更换另一种【顺序容器】。

### 9.2 容器库概览

【容器的操作】有层次：

【所有容器】都支持的【操作】【表9.2 容器操作 P295】。
【顺序容器】支持的【操作】【表9.3 容器定义和初始化 P299】。
【关联容器】支持的【操作】【表11.7 在一个关联容器中查找元素的操作 P388】。
【无序容器】支持的【操作】【表11.8 无序容器管理操作 P395】。

在本节介绍【所有容器都支持的操作】。
在本章介绍【顺序容器支持的操作】。
在【第11章】介绍【关联容器支持的操作】。

默认情况，【每个容器】都各自定义在【自己的头文件】中，【容器】的【头文件名】和【类型名】相同。
比如，【deque】定义在【头文件deque】，【list】定义在【头文件list】。
【容器】是【模板】【3.3节 P86】。
定义【vector变量】必须传入【vector变量的元素类型】。
定义【大多数类型的容器变量】必须传入【容器变量的元素类型】。

```c++
#include <array>
#include <string>
#include <vector>
#include <deque>
#include <list>
#include <forward_list>
```

```c++
容器类型<元素类型> 容器变量;
```

##### 对容器可以保存的元素类型的限制

【顺序容器】可以保存【几乎任意类型的元素】。
【容器的元素类型】可以是【另一个容器】。
定义【元素类型是容器的容器变量】和【其他元素类型的容器变量】相同。
向【尖括号】传入【元素类型】，【元素类型】是【另一个容器】。

```
容器类型A<容器类型B<元素类型B>> 容器变量;
```

【容器变量】是【容器类型A变量】，【容器变量的元素类型】是【<容器类型B<元素类型B>】。

在【旧版本编译器】，可能需要在【两个连续的右尖括号】之间插入【空格】。

```
容器类型A<容器类型B<元素类型B> > 容器变量;
```

虽然【容器】可以保存【几乎任意的元素类型】，但是使用【容器的某些操作】需要【容器的元素类型】支持【某些操作】。
如果【容器的元素类型】不支持【这些操作】，就不能使用【容器的这些操作】，只能使用【容器的其他操作】。

比如，可以向【顺序容器的有参构造函数】传入【容器的大小】【3.3.1节 P88】，【传入容器大小的顺序容器有参构造函数】使用了【顺序容器的元素的默认构造函数】。
如果【容器的元素类型】不支持【默认构造函数】，那么定义【容器变量】必须传入【元素的初始化器】。

```c++
容器类型<元素类型> 容器变量(元素的个数, 元素的初始化器);
```

在后面介绍【容器的操作】对【元素类型】的其他限制。

###### 表9.2 容器操作 P295

参考本章其他表格。

| 成员变量的意义 | 顺序容器 | array  | string | vector | deque | list | forward_list |
| -------------- | -------- | ------ | ------ | ------ | ----- | ---- | ------------ |
| 元素类型指针   |          | _Elems |        |        |       |      |              |
| 无效下标       |          |        | npos   |        |       |      |              |

#### 9.2.1 迭代器

【迭代器】使用【通用的接口】，和【容器】一样。
【所有支持某个相同操作的迭代器】使用【相同的方式】实现【这个操作】。
比如，【标准容器类型的所有迭代器】都可以访问【容器的元素】，【标准容器类型的所有迭代器】都使用【解引用运算符*】实现【访问容器元素的操作】。
比如，【标准容器类型的所有迭代器】都支持【递增运算符++】，递增【迭代器】，【迭代器】从【当前元素】移动到【下一个元素】。

【表3.6 标准容器迭代器的运算符 P96】列出了【容器的迭代器支持的所有操作】，除了【forward_list的迭代器】不支持【递减运算符--】。
【表3.7 vector和string迭代器支持的运算 P99】列出了【string、vector、deque、array的迭代器支持的算术运算】。
【其他的容器的迭代器】不支持【算术运算】。

##### 迭代器范围

【一对迭代器】构成【迭代器范围】，【这一对迭代器】分别指向【同一个容器】的【元素】或【尾后位置】。
【这一对迭代器】称作【begin】和【end】，或【first】和【last】，【这一对迭代器】指定了【容器的某个范围的元素】。

虽然【第二个迭代器】称作【last】，但是【第二个迭代器】不指向【迭代器范围的尾元素】，而是指向【迭代器范围的尾后位置】。
【迭代器范围】从【第一个迭代器指向的元素】开始，到【第二个迭代器指向的位置之前的元素】结束。

【迭代器范围】称作【左闭合区间】，下面是标准数学描述。

```
[begin, end)
```

【迭代器范围】从【begin】开始，到【end之前】结束。
【begin】和【end】必须指向【相同的容器】。
【begin】和【end】可以指向【相同的位置】，【end】不能指向【begin之前的位置】。

按照顺序排列【迭代器范围附近的迭代器】：
迭代器范围的首前迭代器（迭代器范围之前的迭代器），
迭代器范围的首迭代器（迭代器范围的首元素的迭代器），
迭代器范围的尾迭代器（迭代器范围的尾元素的迭代器），
迭代器范围的尾后迭代器（迭代器范围之后的迭代器）。

【[迭代器范围的首迭代器, 迭代器范围的尾后迭代器)】构成【迭代器范围】。

##### 使用左闭合范围蕴含的编程假定

因为【左闭合区间】的三个性质，所以使用【左闭合区间】。
假设【begin】和【end】构成【合法的迭代器范围】：

如果【begin】和【end】指向【相同的位置】，那么【迭代器范围】为空。
如果【begin】和【end】指向【不同的位置】，那么【迭代器范围】至少有【一个元素】，【begin】指向【迭代器范围的首元素】。
【begin】可以递增到等于【end】。

可以安全的使用【while循环】、【begin】、【end】处理【迭代器范围】。

```c++
while (begin != end)
{
	*begin = val;
	++begin;
}
```

传入【构成迭代器范围的迭代器begin和end】，如果【begin】==【end】，那么【迭代器范围】为空。
如果【迭代器范围】为空，那么直接退出【while循环】。
如果【迭代器范围】不为空，那么【begin】指向【迭代器范围的一个元素】。
因为【begin】一定指向【迭代器范围的一个元素】，所以在【while循环的循环体】中可以安全的解引用【begin】。
因为在【while循环的循环体】中每次循环递增【begin】，所以【while循环】可以结束。

#### 9.2.2 容器类型成员

【每个容器】都定义了【多个类型】，【表9.2 容器操作 P295】。
已经使用其中过三种类型：【size_type】【3.2.2节 P79】、【iterator】、【const_iterator】【3.4.1节 P97】。

【大多数的容器】支持【反向迭代器】。
【反向迭代器】反向遍历【容器】，【反向迭代器移动的方向】和【正向迭代器】相反。
比如，递增【反向迭代器】，从【当前元素】移动到【上一个元素】。
在【10.4.1节 P363】介绍【反向迭代器】。

在不了解【容器的元素类型】时，可以通过【容器的类型别名】使用【容器的元素类型】。
【容器的value_type】是【容器的元素类型】。
【容器的reference】是【容器的元素类型的引用】，【容器的const_reference】是【容器的元素类型的常量引用】。
在【第16章】介绍【在泛型编程中的类型别名】。

使用【容器的类型别名】。

```c++
容器类型<元素类型>::类型别名 变量;
```

声明语句使用了【作用域运算符::】【1.2节 P7】说明【容器类型<元素类型>】。

| 成员类型的意义     | 顺序容器               | array | string | vector | deque | list | forward_list |
| ------------------ | ---------------------- | ----- | ------ | ------ | ----- | ---- | ------------ |
| 迭代器类型         | iterator               |       |        |        |       |      |              |
| 常量迭代器类型     | const_iterator         |       |        |        |       |      |              |
| 反向迭代器类型     | reverse_iterator       |       |        |        |       |      | ×            |
| 反向常量迭代器类型 | const_reverse_iterator |       |        |        |       |      | ×            |
| 大小类型           | size_type              |       |        |        |       |      |              |
| 偏移类型           | difference_type        |       |        |        |       |      |              |
| 元素类型           | value_type             |       |        |        |       |      |              |
| 元素左值类型       | reference              |       |        |        |       |      |              |
| 元素常量左值类型   | const_reference        |       |        |        |       |      |              |
| 元素指针类型       | pointer                |       |        |        |       |      |              |
| 元素常量指针类型   | const_pointer          |       |        |        |       |      |              |

#### 9.2.3 begin和end成员

【.begin()】返回【容器的首元素的迭代器】，【.end()】返回【容器的尾后迭代器】【3.4.1节 P95】。
调用【.begin()】和【.end()】获取【包含容器所有元素的迭代器范围】。

【表9.2 容器操作 P295】，【.begin()】和【.end()】有多个版本：
【函数名有r的版本】返回【反向迭代器】【10.4.3节 P363】。
【函数名有c的版本】返回【常量迭代器】。

```c++
auto 迭代器变量               = 容器变量.begin();
auto 指向常量的迭代器变量     = 容器变量.cbegin();
auto 反向迭代器变量           = 容器变量.rbegin();
auto 指向常量的反向迭代器变量 = 容器变量.crbegin();
```

【函数名没有c的版本】是【重载函数】。
【容器】有【两个成员函数.begin()】。
【一个版本的.begin()】是【const成员函数】【7.1.2节 P231】，返回【容器的const_iterator】。
【另一个版本的.begin()】是【非const成员函数】，返回【容器的iterator】。
【.rbegin()、.end()、.rend()】和【.begin()】类似。
【非常量容器】调用【这些成员函数】返回【容器的iterator】。
【常量容器】调用【这些成员函数】返回【容器的const_iterator】。
【iterator】可以转化成【const_iterator】，【const_iterator】不能转化成【iterator】，和【指针、引用】类似。

【C++11的标准库】增加了【函数名有c的版本】，可以使用【auto】【2.5.2节 P61】接收【.begin()和.end()返回的迭代器变量】。
以前只能显式声明【迭代器的类型】。

```c++
// 显式声明【迭代器变量的类型】
容器类型<元素类型>::迭代器类型 迭代器变量 = 容器变量.begin();

// 使用【auto】声明【迭代器变量的类型】
// 【const容器变量】返回【const迭代器变量】
// 【非const容器变量】返回【非const迭代器变量】
auto 迭代器变量 = 容器变量.begin();

// 使用【auto】声明【迭代器变量的类型】
auto const迭代器变量 = 容器变量.cbegin();
```

使用【auto】声明【.begin()返回的迭代器变量的类型】，【迭代器变量的const】和【容器变量】相同。
【.cbegin()】和【.cend()】返回【const迭代器变量】。

在【不需要修改元素】时，应该调用【.cbegin()】和【.cend()】。

| 获取迭代器函数的返回值         | 顺序容器    | array | string | vector | deque | list | forward_list        |
| ------------------------------ | ----------- | ----- | ------ | ------ | ----- | ---- | ------------------- |
| 【首】迭代器                   | .begin();   |       |        |        |       |      |                     |
| 【尾后】迭代器                 | .end();     |       |        |        |       |      |                     |
| 【常量】【首】迭代器           | .cbegin();  |       |        |        |       |      |                     |
| 【常量】【尾后】迭代器         | .cend();    |       |        |        |       |      |                     |
| 【反向】【首】迭代器           | .rbegin();  |       |        |        |       |      |                     |
| 【反向】【尾后】迭代器         | .rend();    |       |        |        |       |      |                     |
| 【常量】【反向】【首】迭代器   | .crbegin(); |       |        |        |       |      |                     |
| 【常量】【反向】【尾后】迭代器 | .crend();   |       |        |        |       |      |                     |
| 【首前】迭代器                 |             |       |        |        |       |      | fl.before_begin();  |
| 【常量】【首前】迭代器         |             |       |        |        |       |      | fl.cbefore_begin(); |

#### 9.2.4 容器定义和初始化

【所有容器】都支持【默认构造函数】【7.1.4节 P236】。
【除了array之外其他的容器的默认构造函数】定义【空容器变量】，可以传入【容器的大小】和【元素的初始值】。

###### 表9.3 容器定义和初始化 P299

| 顺序容器的构造函数                                           | array | string                                              | vector | deque | list | forward_list |
| ------------------------------------------------------------ | ----- | --------------------------------------------------- | ------ | ----- | ---- | ------------ |
| 容器类型<元素类型> 容器变量;                                 |       |                                                     |        |       |      |              |
| 容器类型<元素类型> 容器变量{初始化列表};                     |       |                                                     |        |       |      |              |
| 容器类型<元素类型> 容器变量={初始化列表};                    |       |                                                     |        |       |      |              |
| 容器类型<元素类型> 容器变量A(容器变量B);                     |       |                                                     |        |       |      |              |
| 容器类型<元素类型> 容器变量A=容器变量B;                      |       |                                                     |        |       |      |              |
| 容器类型<元素类型> 容器变量A(容器变量B的迭代器范围的首迭代器,容器变量B的迭代器范围的尾后迭代器); | ×     |                                                     |        |       |      |              |
| 容器类型<元素类型> 容器变量A=(容器变量B的迭代器范围的首迭代器,容器变量B的迭代器范围的尾后迭代器); | ×     |                                                     |        |       |      |              |
| 容器类型<元素类型> 容器变量(元素的个数);                     | ×     | ×                                                   |        |       |      |              |
| 容器类型<元素类型> 容器变量(元素的个数,元素的值);            | ×     |                                                     |        |       |      |              |
|                                                              |       | string s(字符数组的指针);                           |        |       |      |              |
|                                                              |       | string s(字符数组的指针,字符的个数);                |        |       |      |              |
|                                                              |       | string s(字符数组的指针,字符数组的下标,字符的个数); |        |       |      |              |
|                                                              |       | string s1(s2);                                      |        |       |      |              |
|                                                              |       | string s1(s2,s2的下标);                             |        |       |      |              |
|                                                              |       | string s1(s2,s2的下标,字符的个数);                  |        |       |      |              |
|                                                              |       | string s1=s2.substr(s2的下标);                      |        |       |      |              |
|                                                              |       | string s1=s2.substr(s2的下标,字符的个数);           |        |       |      |              |

【string s(字符数组的指针,整型参数);的整型参数】是【字符的个数】，不是【开始下标】，和其他情况不同。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	string stringA("01234");

	string stringB("01234");	                           // 01234
	string stringC("01234", 2/*字符的个数*/);               // 01
	string stringD("01234", 2/*开始下标*/, 1/*字符的个数*/); // 2

	string stringE(stringA);	                           // 01234
	string stringF(stringA, 2/*开始下标*/);                 // 234
	string stringG(stringA, 2/*开始下标*/, 1/*字符的个数*/); // 2

	cout << stringB << endl;
	cout << stringC << endl;
	cout << stringD << endl;
	cout << stringE << endl;
	cout << stringF << endl;
	cout << stringG << endl;

	return 0;
}
```

##### 把一个容器初始化为另一个容器的拷贝

可以使用两种方法定义【作为另一个容器变量拷贝的容器变量】：
直接拷贝【整个容器变量】，或者拷贝【容器的迭代器范围的元素】（array除外）。

直接拷贝【整个容器变量】，【被拷贝的容器变量】和【新定义的容器变量】的【容器类型】必须相同，【元素类型】必须相同。
拷贝【迭代器范围的元素】，【两个容器变量】的【容器类型】可以不相同，【元素类型】可以不相同。
【被拷贝容器变量的元素类型】必须可以转换为【新容器变量的元素类型】【4.11节 P141】。

```c++
容器类型A<元素类型A> 容器变量A(容器类型A元素类型A的容器变量B);
容器类型A<元素类型A> 容器变量A(容器类型B元素类型B的容器变量B.begin(), 容器类型B元素类型B的变量B.end());
```

【传入构造函数的两个迭代器】表示【拷贝范围】。
【两个迭代器】分别指向【拷贝范围的首元素】和【拷贝范围的尾后位置】。
【新容器的大小】等于【拷贝范围的元素的个数】。
【新容器的元素】使用【拷贝范围的元素的值】初始化。

可以使用【传入两个迭代器的构造函数】定义【作为另一个容器变量的子序列的拷贝的容器变量】。

```c++
容器类型A<元素类型A> 容器变量A(容器类型B元素类型B的容器变量B的子序列的首迭代器, 容器类型B元素类型B的容器变量B的子序列的尾后迭代器);
```

##### 列表初始化

【C++11】可以使用【列表】初始化【容器】【3.3.1节 P88】。

```c++
容器类型<元素类型> 容器变量{ 初始化列表 };
容器类型<元素类型> 容器变量 = { 初始化列表 };
```

使用【列表】初始化【容器】，显式的传入了【容器的元素的初始值】。
隐含的传入了【容器的大小】（array除外）。
【容器的大小】等于【列表的初始值的个数】。

##### 与顺序容器大小相关的构造函数

除了【和关联容器相同的构造函数】之外，【除了array之外其他的顺序容器】还支持【其他的构造函数】，向【这种构造函数】传入【容器的大小】和【可选的元素的初始值】。

如果没有传入【元素的初始值】，那么【标准库】会创建【值初始化器】【3.3.1节 P88】。

```c++
容器类型<元素类型> 容器变量(元素的个数);
容器类型<元素类型> 容器变量(元素的个数, 元素的初始值);
```

如果【元素类型】是【内置类型】或【支持默认构造函数的类类型】，那么【传入容器的大小和元素的值的构造函数】可以只传入【容器的大小】。
如果【元素类型】不支持【默认构造函数】，那么【传入容器的大小和元素的值的构造函数】必须传入【容器的大小】和【元素的值】。

【关联容器】不支持【传入容器的大小和元素的值的构造函数】。

##### 标准库array具有固定大小

【array的大小】是【array类型的一部分】，和【内置数组】一样。
定义【array变量】必须传入【array的元素类型】和【array的大小】。

```c++
array<元素类型, 元素的个数> array变量;
```

使用【array类型】必须传入【array的元素类型】和【array的大小】。

```c++
array<元素类型, 元素的个数>::类型别名 变量;
```

因为【array的大小】是【array类型的一部分】，所以【array】不支持【普通的容器构造函数】。
【普通的容器构造函数】会显式或隐式的传入【容器的大小】。
向【array的构造函数】传入【array的大小】多余而且容易出错。

【array的大小固定】影响了【array的构造函数的行为】。
【默认构造的array变量】非空：
【默认构造的array变量的元素的个数】等于【array变量的大小】。
默认初始化【默认构造的array变量的元素】【2.2.1节 P40】，和【内置数组变量的元素】【3.5.1节 P102】一样。
【列表初始化的元素的初始值的个数】必须小于等于【array的大小】。
如果【列表初始化的元素的初始值的个数】小于【array的大小】，那么使用【列表初始化的元素的初始值】初始化【array前面的元素】，值初始化【array剩余的元素】【3.3.1节 P88】。
并且如果【array的元素类型】是【类类型】，那么【作为元素类型的类类型】必须支持【默认构造函数】，【值初始化】需要【默认构造函数】。

```c++
array<元素类型, 元素的个数> array变量 = { 初始化列表 }；
```

不能赋值或拷贝【内置数组】，可以赋值或拷贝【array】。

```
array<元素类型, 元素的个数> array变量A = array变量B;
```

因为【array的大小】是【array类型的一部分】，所以【被拷贝的array变量】和【新定义的array变量】的【元素类型】必须相同，【array的大小】必须相等。

#### 9.2.5 赋值和swap

【表9.4 容器赋值运算 P302】列出了【所有容器】都支持的【赋值运算】。
【赋值运算符=】拷贝【右边容器的元素】覆盖【左边容器】。

```c++
容器变量A = 容器变量B;
容器变量 = { 初始化列表 };
```

在第一个赋值运算之后，【左边容器】等于【右边容器】。
【左边容器的大小】等于【右边容器】。
在第二个赋值运算之后，【容器的大小】等于【初始化列表的元素的值的个数】。

【array】支持【赋值运算】，和【内置数组】不同。
【赋值运算符=左右两边的类型】必须相同。

```c++
array<元素类型A, 容器的大小A> array变量A = { 初始化列表 };
array<元素类型A, 容器的大小A> array变量B = { 0 }; // 初始化【array变量B的所有元素】为0
array变量A = array变量B;
array变量A = { 0 }; // VS支持
```

因为【赋值运算符=左边array的大小】可能不等于【赋值运算符=右边array的大小】，所以【array】不支持【.assign()】，不支持使用【初始化列表】赋值。

###### 表9.4 容器赋值运算 P302

| 顺序容器的赋值和swap(修改元素)                               | array  | string              | vector | deque | list | forward_list |
| ------------------------------------------------------------ | ------ | ------------------- | ------ | ----- | ---- | ------------ |
| 容器变量={初始化列表};                                       | VS支持 |                     |        |       |      |              |
| 容器变量A=容器变量B;                                         |        |                     |        |       |      |              |
| 容器变量A.swap(容器变量B);                                   |        |                     |        |       |      |              |
| swap(容器变量A,容器变量B);                                   |        |                     |        |       |      |              |
| 容器变量.assign({初始化列表});                               | ×      |                     |        |       |      |              |
| 容器变量A.assign(容器变量B的拷贝范围的首迭代器,容器变量B的拷贝范围的尾后迭代器); | ×      |                     |        |       |      |              |
| .assign(元素的个数,元素的值);                                | ×      |                     |        |       |      |              |
|                                                              |        | s1.assign(s2的参数) |        |       |      |              |

【s1.assign(s2的参数)的s2参数】和【s1.append(s2的参数)的s2参数】没有限制。

s1.assign(s2的参数)。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	char sz[]{ "01234" };	   // 字符数组
	char* pc = nullptr;		   // 字符数组的指针
	pc = sz;
	string stringA{ "01234" }; // string
	string stringB;

	stringB.assign(sz);			        // 01234
	cout << stringB << endl;
	stringB.assign(sz, 2/*字符的个数*/); // 01
	cout << stringB << endl;
	stringB.assign(sz, 2, 1);	        // 2
	cout << stringB << endl;
	stringB.assign(pc);			        // 01234
	cout << stringB << endl;
	stringB.assign(pc, 2/*字符的个数*/); // 01
	cout << stringB << endl;
	stringB.assign(pc, 2, 1);	        // 2
	cout << stringB << endl;
	stringB.assign(stringA);	           // 01234
	cout << stringB << endl;
	stringB.assign(stringA, 2/*开始下标*/); // 234
	cout << stringB << endl;
	stringB.assign(stringA, 2, 1);         // 2
	cout << stringB << endl;
	stringB.assign(stringA.begin(), stringA.end()); // 01234
	cout << stringB << endl;
	stringB.assign({ "01234" }); // 01234
	cout << stringB << endl;
	stringB.assign(5, '0'); // 00000
	cout << stringB << endl;

	return 0;
}
```

s1.append(s2的参数)。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	char sz[]{ "01234" };	   // 字符数组
	char* pc = nullptr;		   // 字符数组的指针
	pc = sz;
	string stringA{ "01234" }; // string
	string stringB;

	stringB.append(sz);			        // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.append(sz, 2/*字符的个数*/); // 01
	cout << stringB << endl;
	stringB.clear();
	stringB.append(sz, 2, 1);	        // 2
	cout << stringB << endl;
	stringB.clear();
	stringB.append(pc);			        // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.append(pc, 2/*字符的个数*/); // 01
	cout << stringB << endl;
	stringB.clear();
	stringB.append(pc, 2, 1);	        // 2
	cout << stringB << endl;
	stringB.clear();
	stringB.append(stringA);         	   // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.append(stringA, 2/*开始下标*/); // 234
	cout << stringB << endl;
	stringB.clear();
	stringB.append(stringA, 2, 1);         // 2
	cout << stringB << endl;
	stringB.clear();
	stringB.append(stringA.begin(), stringA.end()); // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.append({ "01234" }); // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.append(5, '0'); // 00000
	cout << stringB << endl;
	stringB.clear();

	return 0;
}
```

##### 使用assign（仅顺序容器）

【赋值运算符=左右两边的类型】必须相同。
【赋值运算符=】拷贝【右边容器的元素】覆盖【左边容器】。
【除了array之外其他的顺序容器】支持【.assign()】，【调用.assign()的容器变量】和【传入.assign()的容器变量的子序列】的【容器类型】可以不同，【元素类型】可以不同。
【.assign()】拷贝【传入.assign()的容器变量的子序列】覆盖【调用.assign()的容器变量】。
比如，可以调用【.assign()】拷贝【vector\<char*\>】覆盖【list\<string\>】。

```c++
容器变量A.assign(容器变量B的拷贝范围的首迭代器, 容器变量B的拷贝范围的尾后迭代器);
```

在调用【.assign()】之后，【调用.assign()的容器变量】等于【传入.assign()的容器变量的子序列】。
【传入.assign()的容器变量的子序列】决定【调用.assign()的容器变量】的【元素的个数】和【元素的值】。

【传入.assign()的容器变量的子序列】不能是【调用.assign()的容器变量的子序列】。

可以向【.assign()】传入【元素的个数】和【元素的值】。
【.assign()】拷贝【指定个数】个【元素的值】覆盖【调用.assign()的容器变量】。

```c++
容器变量.assign(元素的个数, 元素的值);
```

##### 使用swap

【swap()】交换【两个容器的元素】，【swap()交换的两个容器】的【容器类型】必须相同，【元素类型】必须相同。

```c++
swap(容器类型A元素类型A的容器变量A, 容器类型A元素类型A的容器变量B);
```

在调用【swap()】之后，【容器变量A】包含【之前容器变量B的元素】，【容器变量B】包含【之前容器变量A的元素】。
【swap()交换除了array之外其他的容器的速度】很快，【swap()】不交换【元素】，【swap()】交换【容器的内部数据结构】。

【swap()】不拷贝、删除、插入【除了array之外其他的容器的元素】，【swap()】交换【两个除了array之外其他的容器的元素】是【常量阶】。

因为【swap()】不会移动【除了array之外其他的容器的元素】，所以在调用【swap()】之后，【除了string之外其他的容器的迭代器、引用、指针】不会失效。
虽然【除了string之外其他的容器的迭代器、引用、指针】仍然指向【之前指向的元素】。
但是在调用【swap()】之后，【这些元素】属于【不同的容器】。
比如，在调用【swap()】之前，【迭代器】指向【容器变量A的第三个元素】，在调用【swap()】之后，【迭代器】指向【容器变量B的第三个元素】。
在调用【swap()】之后，【string的迭代器、引用、指针】会失效，和【其他的容器】不同。

【swap()】交换【两个array的元素】，和【其他的容器】不同。
【swap()】交换【两个array的元素】是【线性阶】。

在调用【swap()】之后，【array的迭代器、引用、指针】仍然指向【之前指向的位置】，【swap()】交换【两个array的相同位置的元素的值】。

【C++11的标准库】支持【成员函数版本的.swap()】和【通用版本的swap()】。
【C++11之前的标准库】只支持【成员函数版本的.swap()】。
在【泛型编程】中需要【通用版本的swap()】。
推荐使用【通用版本的swap()】。

#### 9.2.6 容器大小操作

【除了forward_list之外其他的容器】都支持【三个和容器大小相关的成员函数】。
【.size()】【3.2.2节 P78】返回【容器的元素的个数】。
【.empty()】在【.size()返回0】时返回【true】，在【.size()返回非0】时返回【false】。
【.max_size()】返回【大于等于调用.max_size()的容器变量最多能容纳的元素的个数】。
【forward_list】不支持【.size()】，原因在下一节。

参考本章其他的表格【表9.9 顺序容器大小操作 P314】。

#### 9.2.7 关系运算符

【所有容器】都支持【相等运算符== !=】。
【除了无序关联容器之外其他的容器】都支持【关系运算符< <= >= >】。
【关系运算符比较的两个容器】的【容器类型】必须相同，【元素类型】必须相同。

比较【两个容器】是逐对比较【两个容器的元素】。
【容器的关系运算符的行为】和【string的关系运算符】【3.2.2节 P79】类似：

【两个容器】从【首元素】开始逐对比较。
如果【一个容器的元素】小于【另一个容器的元素】，那么【这个容器】小于【另一个容器】。
如果【一个容器的元素】一直等于【另一个容器的元素】，【两个容器的大小】不等，那么【较小的容器】小于【较大的容器】。
如果【一个容器的元素】一直等于【另一个容器的元素】，【两个容器的大小】相等，那么【两个容器】相等。

| 顺序容器的关系运算符 | array | string          | vector | deque | list | forward_list |
| -------------------- | ----- | --------------- | ------ | ----- | ---- | ------------ |
| < <= == != >= >      |       |                 |        |       |      |              |
|                      |       | s.compare(参数) |        |       |      |              |

s.compare()的参数。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	char sz[]{ "01234" };	   // 字符数组
	char* pc = nullptr;		   // 字符数组的指针
	pc = sz;
	string stringA{ "01234" }; // string
	string stringB{ "01234" };

	cout << stringB.compare(sz) << endl;				          // 0
	cout << stringB.compare(0, 5, sz) << endl;			          // 0
	cout << stringB.compare(0, 4, sz, 4/*字符的个数*/) << endl;    // 0
	cout << stringB.compare(1, 4, sz, 1, 4) << endl;	          // 0
	cout << stringB.compare(pc) << endl;				          // 0
	cout << stringB.compare(0, 5, pc) << endl;			          // 0
	cout << stringB.compare(0, 4, pc, 4/*字符的个数*/) << endl;    // 0
	cout << stringB.compare(1, 4, pc, 1, 4) << endl;	          // 0
	cout << stringB.compare(stringA) << endl;			          // 0
	cout << stringB.compare(0, 5, stringA) << endl;		          // 0
	cout << stringB.compare(1, 4, stringA, 1/*开始下标*/) << endl; // 0
	cout << stringB.compare(1, 4, stringA, 1, 4) << endl;         // 0
	cout << stringB.compare({ "01234" }) << endl;		          // 0

	return 0;
}
```

##### 容器的关系运算符使用元素的关系运算符完成比较

使用【容器的比较运算符】需要【容器的元素类型】支持【比较运算符】。

【容器的相等运算符== !=】使用【元素的相等运算符== !=】，【容器的其他关系运算符】使用【元素的小于运算符<】。
使用【容器的运算符】需要【容器的元素类型】支持【这种运算符】。

### 9.3 顺序容器操作

【顺序容器组织元素的方式】和【关联容器】不同。
【组织元素的方式】影响【存储、访问、添加、删除元素的方式】。
上一节介绍了【所有容器都支持的操作】【表9.2 容器操作 P295】。
本章剩余部分介绍【顺序容器特有的操作】。

#### 9.3.1 向顺序容器添加元素

【除了array之外其他的顺序容器】都支持【灵活的内存管理】。
在运行时可以动态添加或删除【元素】，改变【容器的大小】。
【表9.5 向顺序容器添加元素的操作 P305】列出了【向除了array之外其他的顺序容器添加元素的操作】。

###### 表9.5 向顺序容器添加元素的操作 P305

| 添加元素函数的返回值                            | 顺序容器                                                     | array | string                        | vector | deque | list | forward_list                                                 |
| ----------------------------------------------- | ------------------------------------------------------------ | ----- | ----------------------------- | ------ | ----- | ---- | ------------------------------------------------------------ |
|                                                 | .push_back(元素的值);                                        | ×     |                               |        |       |      | ×                                                            |
|                                                 | .push_front(元素的值);                                       | ×     | ×                             | ×      |       |      |                                                              |
| 新元素的迭代器                                  | .insert(插入位置的迭代器,元素的值);                          | ×     |                               |        |       |      | ×                                                            |
| 第一个新元素的迭代器                            | .insert(插入位置的迭代器,{初始化列表});                      | ×     |                               |        |       |      | ×                                                            |
| 第一个新元素的迭代器                            | 容器变量A.insert(容器变量A的插入位置的迭代器,容器变量B的拷贝范围的首迭代器,容器变量B的拷贝范围的尾后迭代器); | ×     |                               |        |       |      | ×                                                            |
| 第一个新元素的迭代器                            | .insert(插入位置的迭代器,元素的个数,元素的值);               | ×     |                               |        |       |      | ×                                                            |
|                                                 | .emplace_back(元素类型的构造函数的参数);                     | ×     | ×                             |        |       |      | ×                                                            |
|                                                 | .emplace_front(元素类型的构造函数的参数);                    | ×     | ×                             | ×      |       |      |                                                              |
| 新元素的迭代器                                  | .emplace(插入位置的迭代器,元素类型的构造函数的参数);         | ×     |                               |        |       |      | ×                                                            |
| auto/auto&接收调用.insert()的string的拷贝/引用  |                                                              |       | s1.insert(s1的位置,s2的参数)  |        |       |      |                                                              |
| auto/auto&接收调用.append()的string的拷贝/引用  |                                                              |       | s1.append(s2的参数)           |        |       |      |                                                              |
| auto/auto&接收调用.replace()的string的拷贝/引用 |                                                              |       | s1.replace(s1的位置,s2的参数) |        |       |      |                                                              |
| 新元素的迭代器                                  |                                                              |       |                               |        |       |      | fl.insert_after(插入位置之前的迭代器,元素的值);              |
| 最后一个新元素的迭代器                          |                                                              |       |                               |        |       |      | fl.insert_after(插入位置之前的迭代器,{初始化列表});          |
| 最后一个新元素的迭代器                          |                                                              |       |                               |        |       |      | flA.insert_after(flA的插入位置之前的迭代器,flB的拷贝范围的首迭代器,flB的拷贝范围的尾后迭代器); |
| 最后一个新元素的迭代器                          |                                                              |       |                               |        |       |      | fl.insert_after(插入位置之前的迭代器,元素的个数,元素的值);   |
| 新元素的迭代器                                  |                                                              |       |                               |        |       |      | fl.emplace_after(插入位置之前的迭代器,元素类型的构造函数的参数; |

【s1.insert(s1的位置, s2的参数)的s2参数】和【s1.replace(s1的位置, s2的参数)】的s2参数】必须满足下表条件。

| insert(s1的插入位置的下标,s2的参数) | insert(s1的插入位置的迭代器,s2的参数) | replace(s1的删除开始位置的下标,s1删除字符的个数,s2的参数) | replace(s1的删除范围的首迭代器,s1的删除范围的尾后迭代器,s2的参数) | s2的参数可以是                           |
| ----------------------------------- | ------------------------------------- | --------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------------- |
| √                                   |                                       | √                                                         | √                                                            | 字符数组                                 |
|                                     |                                       | √                                                         | √                                                            | 字符数组,字符的个数                      |
|                                     |                                       | √                                                         |                                                              | 字符数组,字符数组的下标,字符的个数       |
|                                     |                                       | √                                                         | √                                                            | 字符数组的指针                           |
|                                     |                                       | √                                                         | √                                                            | 字符数组的指针,字符的个数                |
|                                     |                                       | √                                                         |                                                              | 字符数组的指针,字符数组的下标,字符的个数 |
|                                     |                                       | √                                                         | √                                                            | string                                   |
|                                     |                                       | √                                                         |                                                              | string,string的下标                      |
|                                     |                                       | √                                                         |                                                              | string,string的下标,字符的个数           |
|                                     | √                                     |                                                           | √                                                            | 拷贝范围的首迭代器,拷贝范围的尾后迭代器  |
|                                     |                                       | √                                                         | √                                                            | {初始化列表}                             |
|                                     | √                                     | √                                                         | √                                                            | 字符的个数,字符                          |

s1.insert(s1的插入位置的下标, s2的参数)。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	char sz[]{ "01234" };	   // 字符数组
	char* pc = nullptr;		   // 字符数组的指针
	pc = sz;
	string stringA{ "01234" }; // string
	string stringB{ "ABCDE" };

	stringB.insert(3, sz); // ABC01234DE
	cout << stringB << endl;
	stringB.clear();
	// stringB.insert(3, sz, 2);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(3, sz, 2, 1);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(3, pc);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(3, pc, 2);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(3, pc, 2, 1);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(3, stringA);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(3, stringA, 2);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(3, stringA, 2, 1);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(3, stringA.begin(), stringA.end());
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(3, { "01234" });
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(3, 5, '0');
	// cout << stringB << endl;
	// stringB.clear();

	return 0;
}
```

s1.insert(s1的插入位置的迭代器, s2的参数)。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	char sz[]{ "01234" };	   // 字符数组
	char* pc = nullptr;		   // 字符数组的指针
	pc = sz;
	string stringA{ "01234" }; // string
	string stringB;

	// stringB.insert(stringB.begin(), sz);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(stringB.begin(), sz, 2);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(stringB.begin(), sz, 2, 1);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(stringB.begin(), pc);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(stringB.begin(), pc, 2);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(stringB.begin(), pc, 2, 1);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(stringB.begin(), stringA);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(stringB.begin(), stringA, 2);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.insert(stringB.begin(), stringA, 2, 1);
	// cout << stringB << endl;
	// stringB.clear();
	stringB.insert(stringB.begin(), stringA.begin(), stringA.end()); // 01234
	cout << stringB << endl;
	stringB.clear();
	// stringB.insert(stringB.begin(), { "01234" });
	// cout << stringB << endl;
	// stringB.clear();
	stringB.insert(stringB.begin(), 5, '0'); // 00000
	cout << stringB << endl;
	stringB.clear();

	return 0;
}
```

s1.replace(s1的删除开始位置的下标, s1删除字符的个数, s2的参数)。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	char sz[]{ "01234" };	   // 字符数组
	char* pc = nullptr;		   // 字符数组的指针
	pc = sz;
	string stringA{ "01234" }; // string
	string stringB;

	stringB.replace(0, 0, sz);			       // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(0, 0, sz, 2/*字符的个数*/); // 01
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(0, 0, sz, 2, 1);           // 2
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(0, 0, pc);			       // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(0, 0, pc, 2/*字符的个数*/); // 01
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(0, 0, pc, 2, 1);           // 2
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(0, 0, stringA);		          // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(0, 0, stringA, 2/*开始下标*/); // 234
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(0, 0, stringA, 2, 1);         // 2
	cout << stringB << endl;
	stringB.clear();
	// stringB.replace(0, 0, stringA.begin(), stringA.end());
	// cout << stringB << endl;
	// stringB.clear();
	stringB.replace(0, 0, { "01234" }); // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(0, 0, 5, '0');      // 00000
	cout << stringB << endl;
	stringB.clear();

	return 0;
}
```

s1.replace(s1的删除范围的首迭代器, s1的删除范围的尾后迭代器, s2的参数)。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	char sz[]{ "01234" };	   // 字符数组
	char* pc = nullptr;		   // 字符数组的指针
	pc = sz;
	string stringA{ "01234" }; // string
	string stringB;

	stringB.replace(stringB.begin(), stringB.end(), sz);               // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(stringB.begin(), stringB.end(), sz/*字符个数*/, 2); // 01
	cout << stringB << endl;
	stringB.clear();
	// stringB.replace(stringB.begin(), stringB.end(), sz, 2, 1);
	// cout << stringB << endl;
	// stringB.clear();
	stringB.replace(stringB.begin(), stringB.end(), pc);               // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(stringB.begin(), stringB.end(), pc/*字符个数*/, 2); // 01
	cout << stringB << endl;
	stringB.clear();
	// stringB.replace(stringB.begin(), stringB.end(), pc, 2, 1);
	// cout << stringB << endl;
	// stringB.clear();
	stringB.replace(stringB.begin(), stringB.end(), stringA); // 01234
	cout << stringB << endl;
	stringB.clear();
	// stringB.replace(stringB.begin(), stringB.end(), stringA, 2);
	// cout << stringB << endl;
	// stringB.clear();
	// stringB.replace(stringB.begin(), stringB.end(), stringA, 2, 1);
	// cout << stringB << endl;
	// stringB.clear();
	stringB.replace(stringB.begin(), stringB.end(), stringA.begin(), stringA.end()); // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(stringB.begin(), stringB.end(), { "01234" });                    // 01234
	cout << stringB << endl;
	stringB.clear();
	stringB.replace(stringB.begin(), stringB.end(), 5, '0');                         // 00000
	cout << stringB << endl;
	stringB.clear();

	return 0;
}
```

向【vector】、【string】、【deque】添加【元素】之后，【指向vector、string、deque的迭代器、引用、指针】会失效。

【不同容器】需要使用【不同操作】添加【元素】，【容器添加元素的方式】影响【容器的性能】。
向【vector或string的尾部之外的位置】或【deque的首尾之外的位置】添加【元素】，需要移动【容器的元素】。
向【vector】或【string】添加【元素】，可能需要重新申请【内存】。
从【之前的内存】移动【容器的元素】到【新申请的内存】。

##### 使用push_back

【3.3.2节 P90】，调用【.push_back()】向【容器的尾部】插入【元素】。
【除了array和forward_list之外其他的顺序容器】支持【.push_back()】。

```c++
容器变量.push_back(元素的值);
```

使用【while循环】读取【string】，向【容器的尾部】添加【string】。

```c++
#include <string>
#include <vector>
#include <iostream>
using namespace std;

int main()
{
	string word;
	vector<string> container;

	while (cin >> word)
	{
		container.push_back(word);
	}

	for (auto a : container)
	{
		cout << a << endl;
	}

	return 0;
}
```

调用【.push_back()】向【容器的尾部】插入【元素】，【容器.size()】增加了【1】。
【新元素的值】是【word的拷贝】。
【容器的类型】可以是【list】、【vector】、【deque】。

因为【string】是【保存字符的容器】，所以可以调用【.push_back()】向【string的尾部】插入【字符】。

```c++
void pluralize(size_t cnt, string& word)
{
	if (cnt > 1)
	{
		word.push_back('s');
	}
}
```

传入【容器的拷贝构造函数】和【容器的添加元素的函数】的是【参数的值拷贝】。
【容器的元素】和【传入的参数】没有关联，修改【容器的元素】不会影响【传入的参数】，修改【传入的参数】不会影响【容器的元素】，和【变量】作为【非引用参数】【3.2.2节 P79】传入【函数】一样。

##### 使用push_front

【list】、【forward_list】、【deque】支持【.push_back()】。
调用【.push_front()】向【容器的头部】插入【元素】。

```c++
容器变量.push_front(元素的值);
```

```c++
#include <list>
#include <iostream>
using namespace std;

int main()
{
	list<int> int_list;

	for (size_t i = 0; i != 4; i++)
	{
		int_list.push_front(i);
	}

	for (auto a : int_list)
	{
		cout << a << endl;
	}

	return 0;
}

// 3
// 2
// 1
// 0
```

循环调用【.push_front()】向【容器的头部】插入【元素的序列】。
【元素的序列的每个元素】都插入到【容器的头部】。
【1】插入到【0】之前，【2】插入到【1】之前，【3】插入到【2】之前。
【元素序列的元素在容器的顺序】和【元素序列的元素在元素序列的顺序】相反。
在【for循环】执行结束之后，【容器的元素序列】是【3、2、1、0】。

##### 在容器的特定位置添加元素

调用【.push_back()】向【容器的尾部】插入【元素】，调用【.push_front()】向【容器的头部】插入【元素】。
调用【.insert()】向【容器的任意位置】插入【元素】。
【vector】、【deque】、【list】、【string】、【forward_list】支持【.insert()】。
【forward_list的.insert()的参数】和【其他的容器】不同，在【9.3.4节 P312】介绍。

【.insert()的第一个参数】是【迭代器】。
【迭代器】指向【容器的插入元素的位置】。
【迭代器】可以指向【容器的任何位置】，包括【容器的尾后位置】。
为了可以向【容器的头部】插入【元素】，【.insert()】向【迭代器指向的位置】插入【元素】。

```c++
容器变量.insert(插入位置的迭代器, 元素的值);
```

虽然【某些容器】不支持【.push_front()】，但是【这些容器】支持【.insert()】。
可以调用【.insert()】向【这些容器的头部】插入【元素】。

```c++
容器变量.insert(容器变量.begin(), 元素的值);
```

虽然可以向【vector、deque、string的任何位置】插入【元素】。
但是【向vector、deque、string的任何位置插入元素的速度】可能很慢。

##### 插入范围内元素

除了【第一个迭代器参数】之外，还可以向【.insert()】传入【更多的参数】，和【容器的构造函数】类似。
可以调用【.insert()】向【迭代器指向的位置】插入【指定个数】个【元素的值】。

```c++
容器变量.insert(插入位置的迭代器, 元素的个数, 元素的值);
```

可以调用【.insert()】向【迭代器指向的位置】插入【容器的迭代器范围的元素】。
【调用.insert()的容器】和【迭代器范围所在的容器】不能相同）。

```c++
容器变量A.insert(容器变量A的插入位置的迭代器, 容器变量B的拷贝范围的首迭代器, 容器变量B的拷贝范围的尾后迭代器);
```

可以调用【.insert()】向【迭代器指向的位置】插入【初始化列表】。

```c++
容器变量.insert(插入位置的迭代器, { 初始化列表 });
```

【C++11的标准库】的【.insert()】，如果【迭代器范围】不为空，那么返回【第一个新元素的迭代器】，如果【迭代器范围】为空，那么返回【插入位置的迭代器】。
【C++11之前的标准库】的【.insert()】返回【void】。

##### 使用insert的返回值

使用【.insert()的返回值】向【容器的固定位置】插入【元素】。

```c++
#include <string>
#include <list>
#include <iostream>
using namespace std;

int main()
{
	string word;
	list<string> lst;
	auto iter = lst.begin();

	while (cin >> word)
	{
		iter = lst.insert(iter, word);
	}

	for (auto a : lst)
	{
		cout << a << endl;
	}

	return 0;
}
```

初始化【迭代器】为【首元素的迭代器】。
如果可以读取单词，【容器】就调用【.insert()】，向【迭代器指向的位置】插入【cin读入的string】。
使用【.insert()返回的新元素的迭代器】赋值【迭代器】，【迭代】重新指向【容器的首元素】。
重复【while循环】，读取下一个单词。
【迭代器】一直指向【容器的首元素】，【while循环的功能】相当于【.push_front()】。

##### 使用emplace操作

【C++11】支持【.emplace_back()】、【.emplace_front()】、【.emplace()】，【三个版本的.emplace()】不拷贝【参数】构造【元素】，而是直接使用【参数】构造【元素】。
【三个版本的.emplace()】类似【三个版本的.insert()】，向【调用.emplace()的容器】的【头部】、【尾部】、【任意位置】插入【元素】。

调用【三个版本的.insert()】，向【三个版本的.insert()】传入【元素的值】，【三个版本的.insert()】拷贝【元素的值】向【迭代器指向的位置】插入【元素】。
调用【三个版本的.emplace()】，向【三个版本的.emplace()】传入【调用.emplace()的容器的元素类型的构造函数的参数】，【三个版本的.emplace()】使用【调用.emplace()的容器的元素类型的构造函数的参数】在【迭代器指向的位置】构造【元素】。

```c++
#include <vector>
#include <iostream>
using namespace std;

class CTest
{
public:
	CTest(int nA, int nB) :m_nA(nA), m_nB(nB) {};

private:
	int m_nA;
	int m_nB;
};

int main()
{
	vector<CTest> vectorA;

	vectorA.push_back(CTest{ 11111111,22222222 });
	vectorA.emplace_back(33333333, 44444444);

	return 0;
}
```

【.emplace_back()】和【.push_back()】都定义新变量，
【.emplace_back()】直接在【容器】定义【变量】。
【.push_back()】定义【临时变量】，向【容器】插入【临时变量的值拷贝】。

【.emplace_back()的参数】是【调用.emplace_back()的容器变量的元素类型的构造函数的参数】。

#### 9.3.2 访问元素

【表9.6 在顺序容器中访问元素的操作 P305】列出了【在顺序容器中访问元素的操作】。
访问【不存在的元素】，【访问操作的结果】未定义。

【所有顺序容器】都支持【.front()】，【除了forward_list之外其他的顺序容器】都支持【.back()】。
【.front()】返回【首元素引用】，【.back()】返回【尾元素引用】。
在【访问元素】之前检查【元素】是否存在，使用【auto】定义【变量的拷贝】，使用【auto&】定义【变量的引用】。

```c++
if (!容器变量.empty())
{
	auto 容器变量的首元素 = 容器变量.front();
	auto 容器变量的首元素 = *容器变量.begin();
	auto 容器变量的尾元素 = 容器变量.back();
	auto 容器变量的尾元素 = *(--容器变量.end());
}
```

可以使用两种方法获取【容器的首尾元素的引用】。
可以直接调用【.front()】和【.back()】返回【容器的首尾元素的引用】，也可以使用【\*.begin()】和【\*(--.end())】返回【容器的首尾元素的引用】。

【.end()返回的迭代器】指向【容器的尾后位置】，递减【.end()返回的迭代器】，【递减之后的迭代器】指向【容器的尾元素】。
如果【调用.front()、.back()、\*.begin(、\*(--.end())的容器】为空，那么行为未定义。

###### 表9.6 在顺序容器中访问元素的操作 P310

| 访问元素函数的返回值               | 顺序容器    | array | string                    | vector | deque | list | forward_list |
| ---------------------------------- | ----------- | ----- | ------------------------- | ------ | ----- | ---- | ------------ |
| 容器的尾元素的引用                 | .back()     |       |                           |        |       |      | ×            |
| 容器的首元素的引用                 | .front()    |       |                           |        |       |      |              |
| 下标n处元素的引用                  | 容器变量[n] |       |                           |        |       | ×    | ×            |
| 下标n处元素的引用，检查下标越界    | .at(n)      |       |                           |        |       | ×    | ×            |
| 参数第一次出现的下标               |             |       | s.find(参数)              |        |       |      |              |
| 参数最后一次出现的下标             |             |       | s.rfind(参数)             |        |       |      |              |
| 参数中字符第一次出现的下标         |             |       | s.find_first_of(参数)     |        |       |      |              |
| 参数中字符最后一次出现的下标       |             |       | s.find_last_of(参数)      |        |       |      |              |
| 参数中没有的字符第一次出现的下标   |             |       | s.find_first_not_of(参数) |        |       |      |              |
| 参数中没有的字符最后一次出现的下标 |             |       | s.find_last_not_of(参数)  |        |       |      |              |

s.find(参数)的参数。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	//              0123456789ABC
	string stringA("Hello worlld!");
	string stringB("ll");

	cout << stringA.find('o') << endl;		   // 4，找字符'o'
	cout << stringA.find('o', 5) << endl;	   // 7，从下标5开始找字符'o'
	cout << stringA.find("ll") << endl;		   // 2，找字符串"ll"
	cout << stringA.find("ll", 5) << endl;	   // 9，从下标5开始找字符串"ll"
	cout << stringA.find("llo", 5, 2) << endl; // 9，从下标5开始找【字符串"llo"的前2个字符组成的子串】
	cout << stringA.find(stringB) << endl;	   // 2，找string"ll"
	cout << stringA.find(stringB, 5) << endl;  // 9，从下标5开始找string"ll"

	return 0;
}
```

s.find_first_of(参数)的参数。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	//              0123456789ABC
	string stringA("Hello worlld!");
	string stringB("world");

	cout << stringA.find_first_of('l') << endl;			// 2，找字符'l'
	cout << stringA.find_first_of('l', 5) << endl;		// 9，从下标5开始找字符'l'
	cout << stringA.find_first_of("erl") << endl;		// 1，找字符串"erl"中字符
	cout << stringA.find_first_of("erl", 2) << endl;    // 2，从下标5开始找字符串"erl"中字符
	cout << stringA.find_first_of("erl", 2, 2) << endl; // 8，从下标5开始找字符串"erl"中前2个字符
	cout << stringA.find_first_of(stringB) << endl;		// 2，找string"world"中字符
	cout << stringA.find_first_of(stringB, 5) << endl;	// 6，从下标5开始找string"world"中字符

	return 0;
}
```

##### 访问成员函数返回的是引用

【访问容器的元素的成员函数】返回【容器的元素的引用】。
【容器的元素引用的const属性】和【容器变量的const属性】一样。
可以使用【元素的非const引用】修改【元素】。

```c++
if (!容器变量.empty())
{
	auto& 容器变量的首元素的引用 = 容器变量.front();
	auto& 容器变量的尾元素的引用 = *容器变量.begin();
}
```

使用【auto】定义【变量的拷贝】，使用【auto&】定义【变量的引用】。

##### 下标操作和安全的随机访问

【支持快速随机访问的容器string、vector、deque、array】也支持【下标运算符[]】【3.3.3节 P91】。
向【下标运算符[]】传入【下标】，返回【容器的下标位置的元素的引用】。
【传入下标运算符[]的下标】必须大于等于【0】，小于【容器的大小】。
【下标运算符[]】不检查【传入下标运算符[]的下标】是否大于等于【0】，小于【容器的大小】。
【越界的下标】产生错误。

可以调用【.at()】。
【.at()】检查【传入.at()的下标】，如果【传入.at()的下标】越界，那么【.at()】抛出【out_of_range异常】【5.6节 P173】。

```c++
支持快速随机访问的容器变量[下标];
支持快速随机访问的容器变量.at(下标);
```

------

重构分割线。

#### 9.3.3 删除元素

【除了array之外其他的顺序容器】支持【多个版本删除元素的操作】。
【表9.7 顺序容器的删除操作 P311】列出了【删除元素的成员函数】。

###### 表9.7 顺序容器的删除操作 P311

| 删除元素函数的返回值                          | 顺序容器                                         | array | string                                | vector | deque | list | forward_list                                              |
| --------------------------------------------- | ------------------------------------------------ | ----- | ------------------------------------- | ------ | ----- | ---- | --------------------------------------------------------- |
|                                               | .pop_back();                                     | ×     |                                       |        |       |      | ×                                                         |
|                                               | .pop_front();                                    | ×     | ×                                     | ×      |       |      |                                                           |
| 删除位置之后的迭代器                          | .erase(删除位置的迭代器);                        | ×     |                                       |        |       |      | ×                                                         |
| 删除范围的尾后迭代器                          | .erase(删除范围的首迭代器,删除范围的尾后迭代器); | ×     |                                       |        |       |      | ×                                                         |
|                                               | .clear();                                        | ×     |                                       |        |       |      |                                                           |
| 删除位置之后的迭代器                          |                                                  |       |                                       |        |       |      | fl.erase_after(删除位置之前的迭代器)                      |
| 删除范围的尾后迭代器                          |                                                  |       |                                       |        |       |      | fl.erase_after(删除范围之前的迭代器,删除范围的尾后迭代器) |
| auto/auto&接收调用.erase()的string的拷贝/引用 |                                                  |       | s.erase(删除开始位置的下标);          |        |       |      |                                                           |
| auto/auto&接收调用.erase()的string的拷贝/本身 |                                                  |       | s.erase(删除开始位置的下标,删除个数); |        |       |      |                                                           |

删除【deque除了首尾之外的元素】会使【指向deque的迭代器、引用、指针】失效。
【指向vector或string删除元素之后元素的迭代器、引用、指针】会失效。

【删除元素的成员函数】不检查参数。

##### pop_front和pop_back成员函数

调用【.pop_front()】和【.pop_back()】删除【首尾元素】。
【不支持.push_front()的容器vector和string】也不支持【.pop_front()】。
【不支持.push_back()的容器forward_list】也不支持【.pop_back()】
【空容器】不能【pop】。

【pop】不返回【pop删除的元素】。如果需要【pop删除的元素】，就自己先保存【pop删除的元素】。

##### 从容器内部删除一个元素

调用【.erase()】删除【容器指定位置的元素】。
可以向【.erase()】传入【一个迭代器】删除【迭代器指向的元素】，也可以传入【一对迭代器】删除【迭代器范围的元素】。
【两个版本.erase()】都返回【指向最后一个删除元素之后位置的迭代器】。

循环删除【list\<int\>的奇数元素】。

```c++
#include <list>
#include <iostream>
using namespace std;

int main()
{
	list<int> lst{ 0,1,2,3,4,5,6,7,8,9 };

	auto it = lst.begin();
	while (it != lst.end())
	{
		if (*it % 2)
		{
			it = lst.erase(it);
		}
		else
		{
			++it;
		}
	}

	for (auto a : lst)
	{
		cout << a << endl;
	}

	return 0;
}
```

在每次【while循环】中，检查【迭代器指向的元素】是否为【奇数】。
如果【迭代器指向的元素】是【奇数】，就删除【迭代器指向的元素】，【迭代器】指向【删除元素之后的元素】。
如果【迭代器指向的元素】是【偶数】，递增【迭代器】，【迭代器】指向【下一个要判断的元素】。

##### 删除多个元素

可以向【.erase()】传入【一对迭代器】删除【迭代器范围的元素】。
返回【指向最后一个删除元素之后位置的迭代器】，下面的例子在调用【.erase()】之后，【传入.erase()】的【删除范围的首迭代器】等于【删除范围的尾后迭代器】。

```c++
删除范围的首迭代器 = 容器变量.erase(删除范围的首迭代器, 删除范围的尾后迭代器);
```

可以调用【.clear()】删除【容器的所有元素】，也可以向【.erase()】传入【.begin()】和【.end()】删除【容器的所有元素】。

```c++
容器变量.clear();
容器变量.erase(容器变量.begin(), 容器变量.end());
```

#### 9.3.4 特殊的forward_list操作

【forward_list的添加和删除函数】和【其他的容器】不同的原因。

【容器】添加或删除【元素】，需要修改【删除或添加的元素的前驱的后继】。
需要获取【删除或添加的元素的前驱】。
【除了forward_list之外其他的容器】可以直接获取【删除或添加的元素的前驱】。
【forward_list】是【单向链表】。
【forward_list】不能直接获取【删除或添加的元素的前驱】。
所以【forward_list的添加和删除函数的参数】是【删除或添加的元素的前驱】。
【forward_list的添加和删除函数】通过【删除或添加的元素的前驱】获取【删除或添加的元素】和【删除或添加的元素的后继】。

因为【forward_list的添加和删除函数】和【其他的容器】不同，所以【forward_list的添加和删除函数名称】是【.insert_after()】、【.emplace_after()】、【.erase_after()】【表9.8 在forward_list中插入和删除元素的操作 P313】。
为了和【forward_list的添加和删除函数的参数】配合，【forward_list】额外支持【.before_begin()】，【.before_begin()】返回【首前迭代器】。
向【forward_list的添加和删除函数】传入【首前迭代器】，添加或删除【forward_list首元素】。

###### 表9.8 在forward_list中插入和删除元素的操作 P313

参考本章其他的表格【表9.5 向顺序容器添加元素的操作 P305】、【表9.7 顺序容器的删除操作 P311】、【9.2.3 begin和end成员的表格】。

在【forward_list添加或删除元素】时，需要注意【两个迭代器】，【一个迭代器】指向【添加或删除位置】，【另一个迭代器】指向【添加或删除位置的前驱】。
可以修改【循环删除list\<int\>的奇数元素 P312】。
实现循环删除【forward_list\<int\>的奇数元素】。

```c++
#include <forward_list>
#include <iostream>
using namespace std;

int main()
{
	forward_list<int> flst{ 0,1,2,3,4,5,6,7,8,9 };

	auto prev = flst.before_begin();
	auto curr = flst.begin(); 
	while (curr != flst.end())
	{
		if (*curr % 2)
		{
			curr = flst.erase_after(prev);
		}
		else
		{
			prev = curr;
			++curr;
		}
	}

	for (auto a : flst)
	{
		cout << a << endl;
	}

	return 0;
}
```

调用【.begin()返回的迭代器】赋值【curr】。
调用【.before_begin()返回的迭代器】赋值【prev】。
【curr】指向【当前要处理的元素】，【prev】指向【当前要处理的元素的前驱】。

在每次【while循环】中，检查【curr指向的元素】是否为【奇数】。
如果【curr指向的元素】是【奇数】，就向【.erase_after()】传入【prev】，删除【prev指向元素的后继】，调用【.erase_after()的返回值】赋值【curr】，【curr】指向【删除元素的后继】，【prev】指向【之前指向的元素】。
如果【curr指向的元素】是【偶数】，就递增【prev】和【curr】，【prev】和【curr】指向【它们之前指向元素的后继】。

#### 9.3.5 改变容器大小

调用【.resize()】扩大和缩小容器。
【array】不支持【.resize()】。
如果【容器的大小】大于【向.resize()传入的参数】，那么【.resize()】会删除【容器后面的元素】。
如果【容器的大小】小于【向.resize()传入的参数】，那么【.resize()】会向【容器后面】添加【新元素】。

```c++
容器变量.resize(有效元素的个数, 元素的值);
```

可以向【.resize()】传入【新元素的初始值】。
如果没有向【.resize()】传入【新元素的初始值】，那么【.resize()】值初始化【3.3.1节 P88】【新元素】。
如果【调用.resize()的容器变量的元素类型】没有【默认构造函数】，那么必须向【.resize()】传入【新元素的初始值】。

###### 表9.9 顺序容器大小操作 P314

| 意义                                                   | 大小函数的返回值                                    | 顺序容器                          | array | string | vector | deque | list | forward_list |
| ------------------------------------------------------ | --------------------------------------------------- | --------------------------------- | ----- | ------ | ------ | ----- | ---- | ------------ |
|                                                        | 容器的元素的个数                                    | .size()                           |       |        |        |       |      | ×            |
|                                                        | 大于等于调用.max_size()的容器最多能容纳的元素的个数 | .max_size()                       |       |        |        |       |      |              |
|                                                        | 容器是否为空                                        | .empty()                          |       |        |        |       |      |              |
| 调整有效元素的个数(不缩小缓冲区)                       |                                                     | .resize(有效元素的个数);          | ×     |        |        |       |      |              |
| 调整有效元素的个数(不缩小缓冲区)，新元素初始化为t      |                                                     | .resize(有效元素的个数,元素的值); | ×     |        |        |       |      |              |
| 缩小capacity至size(不保证缩小)                         |                                                     | .shrink_to_fit();                 | ×     |        |        |       | ×    | ×            |
|                                                        | 缓冲区目前能保存元素最大个数                        | .capacity();                      | ×     |        |        | ×     | ×    | ×            |
| 通知容器分配能保存这个元素的个数的缓冲区(只扩大缓冲区) |                                                     | .reserve(元素的个数);             | ×     |        |        | ×     | ×    | ×            |

【指向.resize()删除元素的迭代器、引用、指针】会失效。
【指向.resize()的vector、string、deque的迭代器、引用、指针】会失效。

#### 9.3.6 容器操作可能使迭代器失效

在【容器】添加或删除【元素】之后，【指向容器的迭代器、引用、指针】会失效。
【失效的迭代器、引用、指针】不表示【任何元素】。
使用【失效的迭代器、引用、指针】产生错误，和【使用未初始化指针】类似【2.3.2节 P49】。

| 容器类型           | 添加元素                                                     | 删除元素                                                     |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| list、forward_list |                                                              | 【指向删除元素的迭代器、引用、指针】失效。                   |
| vector、string     | 如果重新申请内存，那么【所有迭代器、引用、指针】都失效。<br />如果没有重新申请内存，那么【所有指向插入位置及之后的迭代器、引用、指针】都失效。 | 【所有指向删除位置及之后的迭代器、引用、指针】都失效。       |
| deque              | 如果向【首尾位置】插入元素，那么【所有迭代器】都失效。<br />如果向【首尾之外位置】插入元素，那么【所有迭代器、引用、指针】都失效。 | 如果删除【首元素】，那么【所有指向首元素的迭代器、引用、指针】都失效。<br />如果删除【尾元素】，那么【所有指向尾元素的迭代器、引用、指针】和【尾后迭代器】都失效。<br />如果删除【首尾之外元素】，那么【所有迭代器、引用、指针】都失效。 |

在添加和删除【元素】之后，必须重新赋值【迭代器、引用、指针】。
必须注意【vector、string、deque的迭代器、引用、指针】。

##### 编写改变容器的循环程序

【循环添加或删除vector、string、deque元素的程序】必须注意【迭代器、引用、指针】。
【程序】在【每次循环】中都更新【迭代器、引用、指针】。
如果【程序】在【循环】中调用【.insert()】或【.erase()】。
那么可以调用【.insert()和.erase()返回的迭代器】更新【迭代器】。

```c++
#include <vector>
#include <iostream>
using namespace std;

int main()
{
	vector<int> vi{ 0,1,2,3,4,5,6,7,8,9 };

	auto iter = vi.begin();
	while (iter != vi.end())
	{
		if (*iter % 2)
		{
			iter = vi.insert(iter, *iter);
			iter = iter + 2;
		}
		else
		{
			iter = vi.erase(iter);
		}
	}

	for (auto a : vi)
	{
		cout << a << endl;
	}

	return 0;
}
```

【程序】删除【vector\<int\>的奇数元素】，拷贝【vector\<int\>的偶数元素】。

【.erase()返回的迭代器】指向【下一个要判断的元素】。
【.insert()返回的迭代器+2之后】指向【下一个要判断的元素】。

##### 不要保存end返回的迭代器

因为在【vector、string、deque】添加或删除【元素】之后【之前的尾后迭代器】会失效。
所以【循环添加或删除vector、string、deque元素的程序】在【每次循环】中必须调用【.end()】，不能在【循环】之前保存【.end()返回的尾后迭代器】，不能使用【之前保存的.end()返回的尾后迭代器】。
因为需要经常调用【.end()】，所以【C++标准库的.end()的速度】快。

在【每次循环】中调用【.end()】的例子。

```c++
auto 迭代器 = 容器变量.begin();
while (迭代器 != 容器变量.end())
{

	++迭代器;
}
```

### 9.4 vector对象是如何增长的

【vector】和【string】为了支持【快速随机访问】，在【内存】中连续存储【元素】。
通常情况不需要关心【标准库类型的实现】，只需要关心【标准库类型的使用】。
但是【vector和string的接口】和【vector和string的实现】有关。

向【没有空闲内存的vector和string】添加【元素】，需要申请【新内存】。
因为【vector】和【string】在【内存】中连续存储【元素】，所以【vector】和【string】不能只在【新内存】存储【添加的新元素】，必须从【旧内存】拷贝【所有元素】到【新内存】，释放【旧内存】。
如果每次添加【元素】，【vector】和【string】就申请释放一次【内存】，那么【速度】慢。

【vector和string申请的新内存】大于【添加的新元素需要的内存】，【剩余的新内存】作为【缓冲区】，减少【申请释放内存的次数】。

使用【缓冲区】提高了【性能】。
虽然【vector】每次扩大【容器的大小】都需要拷贝【所有元素】，但是【使用了缓冲区的vector扩大容器大小的速度】比【list】和【deque】快。

##### 管理容量的成员函数

【表9.10 容器大小管理操作 P318】【vector和string的内存管理操作】。
【.capacity()】返回【缓冲区目前能保存元素最大个数】。
调用【.reserve()】通知【容器】分配【能保存这个元素的个数的缓冲区（只扩大缓冲区）】。

###### 表9.10 容器大小管理操作 P318

参考本章其他的表格【表9.9 顺序容器大小操作 P314】。

##### capacity和size

【.size()】返回【已保存元素的个数】。
【.capacity()】返回【缓冲区目前能保存元素最大个数】。

【空容器.size()】返回0，【空容器.capacity()】返回0。
向【容器】添加【元素】之后，【size】等于【容器的元素的个数】。
【capacity】大于等于【size】，【capacity的具体大小】和【标准库实现方式】有关。

【vector】每次申请【新内存】翻倍【capacity】。

调用【.shrink_to_fit()】缩小【capacity】至【size】（不保证缩小）。

【每个版本vector】可以自定义【内存管理策略】，【vector】必须只在【迫不得已】时重新申请【内存】。

在【.insert()】时【size】等于【capacity】，【vector】重新申请【内存】。
在【.resize()】或【.reserve()】时，【vector】重新申请【内存】。

【vector的实现原则】：【vector使用.push_back()添加元素的速度】快。
向【空vector】调用【n次.push_back()】是【线性阶】。

### 9.5 额外的string操作

除了【顺序容器都支持的操作】之外，【string】支持【额外的操作】。
【string】和【字符数组】可以互相转换，【string】支持使用【下标】替换【迭代器】。

【string】支持【大量的成员函数】。
【string的成员函数】使用【重复的模式】。

#### 9.5.1 构造string的其他方法

除了【3.2.1节 P76介绍的构造函数】和【其他的顺序容器的构造函数】【表9.3 容器定义和初始化 P299】之外，【string】支持【其他的三个版本的构造函数】【表9.11 构造string的其他方法 P321】。

###### 表9.11 构造string的其他方法 P321

参考本章其他的表格【表9.3 容器定义和初始化 P299】。

可以向【string的构造函数】传入【一个string】或【一个const char*】、【下标】、【字符个数】。

```c++
string string变量(字符数组, 字符个数);
string string变量(字符数组, 字符数组下标, 字符个数);
string string变量A(string变量B, string变量B下标, 字符个数);
```

向【string的构造函数】传入【字符数组】，要求【传入的字符数组】以【结束符】结尾，或者同时传入【字符数组】和【字符个数】。
如果【传入的字符数组】不以【结束符】结尾，并且没有传入【字符个数】，那么【构造函数的行为】未定义。

可以向【string的构造函数】传入【string】、【string下标】、【字符个数】。
【传入的string下标】必须小于【传入的string的大小】。
如果【传入的string下标】大于【传入的string的大小】，就抛出【out_of_range异常】【5.6节 P173】。
如果向【string的构造函数】传入【字符个数】，就从【传入的string下标】拷贝【传入的字符个数】个【字符】。
【string的构造函数】最多拷贝到【传入的string的结尾】，不管【传入的字符个数】。

##### substr操作

【.substr()】【表9.12 子字符串操作 P322】返回【调用.substr()的string的子串】。
可以向【.substr()】传入【调用.substr()的string的子串的开始下标】和【子串的字符个数】。

```c++
string变量A = string变量B.substr(string变量B的子串的开始下标);
string变量A = string变量B.substr(string变量B的子串的开始下标, 子串的字符个数);
```

如果【传入的调用.substr()的string的子串的开始下标】超过【调用.substr()的string的大小】，就会抛出【out_of_range异常】【5.6节 P173】。
【.substr()】最多拷贝到【调用.substr()的string的结尾】，不管【传入的子串的字符个数】。

###### 表9.12 子字符串操作 P322

参考本章其他的表格【表9.3 容器定义和初始化 P299】。

#### 9.5.2 改变string的其他方法

【string】支持【顺序容器】的【赋值运算符】、【.assign()】【9.2.5节 P302】、【.insert()】【9.3.1节 P306】、【.erase()】【9.3.3节 P311】。
【string】支持【其他的版本的.insert()】和【其他的版本的.erase()】。

可以向【string的.insert()】或【string的.erase()】传入【操作位置的迭代器】，也可以向【string的.insert()】或【string的.erase()】传入【操作位置的下标】。

```c++
string变量.insert(插入位置的下标, 插入的字符个数, 插入的字符);
string变量.erase(删除位置的下标, 删除的字符个数);
```

可以向【string的.insert()】或【string的.assign()】传入【字符数组】。

```c++
string变量.assign(字符数组, 字符个数);
string变量.insert(插入位置的下标, 字符数组);
```

向【.assign()】传入【字符数组】赋值【调用.assign()的string变量】。
使用从【传入的字符数组】开始的【传入的字符个数】个【字符】赋值【调用.assign()的string变量】。
【传入的字符个数】必须小于等于【传入的字符数组的大小】。

向【.insert()】传入【插入位置的下标】和【插入的string】，向【插入位置的下标】插入【传入的string】或【传入的string的子串】。

```c++
string变量A.insert(string变量A插入位置的下标, 插入的string变量B);
string变量A.insert(string变量A插入位置的下标, 插入的string变量B, string变量B开始插入位置的下标, string变量B插入的字符个数);
```

##### append和replace函数

【string】额外支持【.append()】和【.replace()】，调用【.append()】和【.replace()】修改【string】。
【表9.13 修改string的操作 P323】描述了【.append()和.replace()的功能】。
【.append()】向【调用.append()的string的末尾】插入【传入的string变量】。

```c++
string变量A.append(插入的string变量B);
```

【.replace()】删除【调用.append()的string的元素】，向【调用.append()的string】插入【传入的字符数组】。

```c++
string变量.replace(删除位置的下标, 删除的字符个数, 插入的字符数组);
```

【.replace()删除的字符个数】可以不等于【.replace()插入的字符个数】。

###### 表9.13 修改string的操作 P323

参考本章其他的表格【表9.5 向顺序容器添加元素的操作 P305】。

##### 改变string的多种重载函数

【.assign()】、【.append()】、【.insert()】、【.replace()】有【多个重载版本】。
【这些函数的插入参数】和【删除参数】有不同版本。
【这些函数】有【通用的接口】。

【.assign()】和【.append()】没有【删除参数】：
【.assign()】删除【调用.assign()的string的所有元素】，【.append()】不删除【元素】。

【.replace()的删除参数】有两种。
【删除开始位置】和【删除字符的个数】，或者【迭代器范围】。
【.insert()的插入参数】有两种：
【下标】或【迭代器】。
【.insert()】向下标或迭代器指向的元素之前的位置】插入【新元素】。

【插入参数】有很多种。
【新元素】可以是【string】、【字符数组】、【初始化列表】、【字符和字符个数】。
如果【新元素】是【string】或【字符数组】，那么可以传入【控制拷贝字符个数的参数】。

不是【每个函数】都支持【所有形式的参数】。

| insert(s1的插入位置的下标,s2的参数) | insert(s1的插入位置的迭代器,s2的参数) | replace(s1的删除开始位置的下标,s1删除字符的个数,s2的参数) | replace(s1的删除范围的首迭代器,s1的删除范围的尾后迭代器,s2的参数) | s2的参数可以是                           |
| ----------------------------------- | ------------------------------------- | --------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------------- |
| √                                   |                                       | √                                                         | √                                                            | 字符数组                                 |
|                                     |                                       | √                                                         | √                                                            | 字符数组,字符的个数                      |
|                                     |                                       | √                                                         |                                                              | 字符数组,字符数组的下标,字符的个数       |
|                                     |                                       | √                                                         | √                                                            | 字符数组的指针                           |
|                                     |                                       | √                                                         | √                                                            | 字符数组的指针,字符的个数                |
|                                     |                                       | √                                                         |                                                              | 字符数组的指针,字符数组的下标,字符的个数 |
|                                     |                                       | √                                                         | √                                                            | string                                   |
|                                     |                                       | √                                                         |                                                              | string,string的下标                      |
|                                     |                                       | √                                                         |                                                              | string,string的下标,字符的个数           |
|                                     | √                                     |                                                           | √                                                            | 拷贝范围的首迭代器,拷贝范围的尾后迭代器  |
|                                     |                                       | √                                                         | √                                                            | {初始化列表}                             |
|                                     | √                                     | √                                                         | √                                                            | 字符的个数,字符                          |

#### 9.5.3 string搜索操作

【string】支持【六种的搜索函数】，【每种搜索函数】有【四个重载版本】。
【表9.14 string搜索操作 P325】描述了【搜索成员函数】和【搜索成员函数的参数】。
【每种搜索函数】都返回【string::size_type类型的找到的下标】。
如果搜索失败，那么返回【static成员变量string::npos】【7.6节 P268】。
【标准库】定义【string::npos】为【const string::size_type类型】，赋值【string::npos】为【-1】。
因为【string::npos】是【无符号类型】，所以赋值【string::npos】为【-1】，【string::npos】是【string的最大大小】【2.1.2节 P32】。

因为【string::npos】是【无符号类型】，所以不推荐使用【有符号类型变量】接收【搜索函数的返回值】。

使用【auto】接收【搜索函数的返回值】。

###### 表9.14 string搜索操作 P325

参考本章其他的表格【表9.6 在顺序容器中访问元素的操作 P310】。

##### 指定在哪里开始搜索

可以向【搜索函数】传入【搜索开始的下标】，指向【搜素开始的位置】。
默认情况，【搜索开始的下标】是【0】。
使用【搜索开始的下标】在【调用搜索函数的string】中循环搜索【传入搜索函数的string】：

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	string name("123456789890QWERTYUIOPASDFGHJKLZXCVBNM");
	string numbers("0123456789ABCDEF");

	string::size_type pos = 0;
	while ((pos = name.find_first_of(numbers, pos)) != string::npos)
	{
		cout << "name[" << pos << "] = " << name[pos] << endl;
		++pos;
	}

	return 0;
}

// name[0] = 1
// name[1] = 2
// name[2] = 3
// name[3] = 4
// name[4] = 5
// name[5] = 6
// name[6] = 7
// name[7] = 8
// name[8] = 9
// name[9] = 8
// name[10] = 9
// name[11] = 0
// name[14] = E
// name[22] = A
// name[24] = D
// name[25] = F
// name[33] = C
// name[35] = B
```

在每次【while循环】中，赋值【pos】为【从pos开始在name中找到的numbers的字符的下标】。
如果【pos】是【合法下标】，就打印【name[pos]】，递增【pos】。

如果没有递增【pos】，那么【while循环】不会终止。
如果没有递增【pos】。
那么调用【.find_first_of()】会重复的返回【pos】。

##### 逆向搜索

【string】支持【反向搜索成员函数】。
【rfind.()】反向搜索【传入rfind.()的参数】。

【find_last的功能】和【find_first】类似，【find_last】反向搜索【传入find_first的参数】：

【.find_last_of()】在【调用.find_last_of()的string】中反向搜索【传入.find_last_of()的string的字符】。
【.find_last_not_of()】在【调用.find_last_not_of()的string】中反向搜索【不是传入.find_last_not_of()的string的字符】。

可以向【搜索函数】传入【搜索开始的下标】，指向【搜素开始的位置】。

#### 9.5.4 compare函数

除了【关系运算符】【3.2.2节 P79】之外，【string】支持【一组.compare()】，【.compare()的行为】和【strcmp()】【3.5.4节 P109】类似。
如果【调用.compare()的string】大于【传入.compare()的string】，那么【.compare()】返回【正数】，
如果【调用.compare()的string】等于【传入.compare()的string】，那么【.compare()】返回【0】，
如果【调用.compare()的string】小于【传入.compare()的string】，那么【.compare()】返回【负数】。

【表9.15 s.compare的几种参数形式 P327】，【string】支持【六个版本.compare()】。
【比较两个string】和【比较string和字符数组】，【向.compare()传入的参数】不同。
可以比较【整个】或【部分】。

###### 表9.15 s.compare的几种参数形式 P327

参考本章其他的表格【9.2.7 关系运算符的表格】。

#### 9.5.5 数值转换

转换string和数值的函数。

【转换数值的string的第一个非空白字符】必须是【数值转换目标类型可能出现的字符】。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	string stringA{ "-0x1.A" };
	auto d = stod(stringA.substr(stringA.find_first_of("+-xX.0123456789ABCDEF")));
	cout << d << endl;

	return 0;
}
```

使用【string.find_first_of()】【9.5.3节 0325】获取【+-xX.0123456789ABCDEF字符的下标】。
使用【string.substr()】获取【下标开始的子串】。
使用【stod()】把【子串】转换为【double】。

【转换数值的string的第一个非空白字符】必须是【+-xX.0123456789ABCDEF】。

如果【string】不能转换为数值，那么【转换string和数值的函数】抛出【invalid_argument异常】【5.6节 P173】。
如果使用【任何类型】都无法表示【转换结果】，那么抛出【out_of_range异常】。

###### 表9.16 string和数值之间的转换 P328

【基数】默认为【十进制】。
【size_t指针】默认为【0】，不传出【下标】。

```c++
string      string变量 = to_string(数值);
int                  n = stoi(string变量, 传出指向第一个非数值字符的下标的size_t指针, 基数);
long                 l = stol(string变量, 传出指向第一个非数值字符的下标的size_t指针, 基数);
unsigned long       ul = stoul(string变量, 传出指向第一个非数值字符的下标的size_t指针, 基数);
long long           ll = stoll(string变量, 传出指向第一个非数值字符的下标的size_t指针, 基数);
unsigned long long ull = stoull(string变量, 传出指向第一个非数值字符的下标的size_t指针, 基数);
float                f = stof(string变量, 传出指向第一个非数值字符的下标的size_t指针);
double               d = stod(string变量, 传出指向第一个非数值字符的下标的size_t指针);
long double         ld = stold(string变量, 传出指向第一个非数值字符的下标的size_t指针);
```

### 9.6 容器适配器

【标准库】有【三种容器适配器】：【stack】、【queue】、【priority_queue】。
【适配器】是【标准库的通用概念】。
【容器】、【迭代器】、【函数】都有【适配器】。
【适配器】和【硬件中的接口转换器】的功能类似。
【容器适配器】改变了【容器的接口】。
向【stack】传入【除了array和forward_list之外的顺序容器】，【stack的接口】像【栈】。
【表9.17 所有容器适配器都支持的操作和类型 P329】列出了【所有容器适配器都支持的操作和类型】。

###### 表9.17 所有容器适配器都支持的操作和类型 P329

| 容器适配器函数的返回值   | 容器适配器                                 | stack    | queue     | priority_queue |
| ------------------------ | ------------------------------------------ | -------- | --------- | -------------- |
| **构造函数**             |                                            |          |           |                |
|                          | 适配器类型<元素类型> 适配器变量;           |          |           |                |
|                          | 适配器类型<元素类型> 适配器变量(容器变量); |          |           |                |
|                          |                                            |          |           |                |
| **关系运算符**           |                                            |          |           |                |
|                          | < <= == != >= >                            |          |           |                |
|                          |                                            |          |           |                |
| **增加元素**             |                                            |          |           |                |
|                          | .push(t);                                  | 压栈     | 尾插      | 排序插入       |
|                          | .emplace(t);                               | 压栈     | 尾插      | 排序插入       |
|                          |                                            |          |           |                |
| **访问元素**             |                                            |          |           |                |
| 栈顶元素引用             |                                            | s.top()  |           |                |
| 首元素引用               |                                            |          | q.front() |                |
| 尾元素引用               |                                            |          | q.back()  |                |
| 优先级最高元素，不是引用 |                                            |          |           | pq.top()       |
|                          |                                            |          |           |                |
| **删除元素**             |                                            |          |           |                |
| 删除元素，无返回         | .pop();                                    | 栈顶元素 | 首元素    | 优先级最高元素 |
|                          |                                            |          |           |                |
| **swap**                 |                                            |          |           |                |
| 交换(相同类型)           | a.swap(b);                                 |          |           |                |
| 交换(相同类型)           | swap(a, b);                                |          |           |                |
|                          |                                            |          |           |                |
| **大小**                 |                                            |          |           |                |
| 获取元素的个数           | .size()                                    |          |           |                |
| 判空                     | .empty()                                   |          |           |                |
|                          |                                            |          |           |                |
| **类型**                 |                                            |          |           |                |
| 元素大小类型             | size_type                                  |          |           |                |
| 元素类型                 | value_type                                 |          |           |                |
| 底层容器类型             | container_type                             |          |           |                |

##### 定义一个适配器

【容器适配器】支持【两种构造函数】：
【适配器的默认构造函数】定义【空适配器变量】。
向【适配器的有参构造函数】传入【容器】定义【作为传入容器拷贝的适配器变量】。
比如，可以向【strack\<int\>的有参构造函数】传入【deque\<int\>】。

```c++
容器适配器类型<元素类型A> 容器适配器变量(元素类型A的容器变量);
```

默认情况，使用【deque】实现【stack】和【queue】，使用【vector】实现【priority_queue】。
可以向【适配器的构造函数】传入【容器类型】定义【使用容器类型实现的适配器变量】。

```c++
容器适配器类型<元素类型A, 实现容器适配器的容器类型<元素类型A>> 容器适配器变量;
容器适配器类型<元素类型A, 实现容器适配器的容器类型<元素类型A>> 容器适配器变量(元素类型A的容器变量);
```

【某些容器】可以实现【某些容器适配器】。
因为【所有容器适配器】都需要【实现容器适配器的容器】可以添加和删除【元素】。
所以【array】不能实现【容器适配器】。
因为【所有容器适配器】都需要【实现容器适配器的容器】可以访问【尾元素】。
所以【forward_list】不能实现【容器适配器】。
因为【stack】需要【.push_back()】、【.pop_back()】、【.back()】，所以可以使用【除了array和forward_list之外其他的顺序容器】实现【stack】。
因为【queue】需要【.push_back()】、【.back()】、【.push_front()】、【.front()】，所以可以使用【list】或【deque】实现【queue】，不能使用【vector】实现【queue】。
因为【priority_queue】需要【.push_back()】、【.pop_back()】、【.front()】、【快速随机访问元素】，所以可以使用【vector】或【deque】实现【priority_queue】，不能使用【list】实现【priority_queue】。

适配器的底层容器类型。

| stack  | queue  | priority_queue | 底层容器类型可以是 |
| ------ | ------ | -------------- | ------------------ |
| VS支持 | VS支持 | VS支持         | array              |
| ×      | ×      | ×              | string             |
| √      | VS支持 | 默认           | vector             |
| 默认   | 默认   | √              | deque              |
| √      | √      | VS支持         | list               |
| VS支持 | VS支持 | VS支持         | forward_list       |

##### 栈适配器

【stack的头文件】是【stack】。
【表9.18 表9.17未列出的栈操作 P330】列出了【stack支持的操作】。

```c++
#include <stack>
```

###### 表9.18 表9.17未列出的栈操作 P330

参考本章其他的表格【表9.17 所有容器适配器都支持的操作和类型 P329】。

只能使用【适配器操作】，不能使用【底层容器操作】。

```c++
#include <stack>
#include <iostream>
using namespace std;

int main()
{
	stack<int> stackA;

	stackA.push(0);
	stackA.push(1);
	stackA.push(2);
	stackA.emplace(3);
	stackA.top() = -1;
	cout << "stackA.size() = " << stackA.size() << endl;
	while (!stackA.empty())
	{
		cout << stackA.top() << endl;
		stackA.pop();
	}

	return 0;
}

// stackA.size() = 4
// -1
// 2
// 1
// 0
```

##### 队列适配器

【queue和priority_queue的头文件】是【queue】。
【表9.19 表9.17未列出的queue和priority_queue操作 P330】列出了【queue和priority_queue支持的操作】。

```c++
#include <queue> // queue和priority_queue
```

###### 表9.19 表9.17未列出的queue和priority_queue操作 P330

参考本章其他的表格【表9.17 所有容器适配器都支持的操作和类型 P329】。

【queue】使用【先进先出（first in first out，FIFO）存储和访问方式】。
【向queue添加的元素】存储在【queue的队尾】，删除【queue的元素】删除【queue的队首】。
【排队】是【queue的例子】。

【priority_queue】可以设置【元素优先级】。
【新添加的元素】存储在【比新添加元素优先级低的元素】之前。
【预约时间排队】是【priority_queue的例子】。
默认情况，【标准库】使用【元素类型的<运算符】比较【元素的优先级】。
在【11.2.2节 P378】学习【自定义元素比较操作】。

```c++
#include <queue>
#include <iostream>
using namespace std;

int main()
{
	queue<int> queueA;

	queueA.push(0);
	queueA.push(1);
	queueA.push(2);
	queueA.emplace(3);
	queueA.front() = -1;
	cout << "queueA.size() = " << queueA.size() << endl;
	while (!queueA.empty())
	{
		cout << queueA.front() << endl;
		queueA.pop();
	}

	return 0;
}

// queueA.size() = 4
// -1
// 1
// 2
// 3
```

```c++
#include <queue>
#include <iostream>
using namespace std;

int main()
{
	// 默认是大顶堆，less<int>设置大顶堆
	priority_queue<int, vector<int>, less<int>> priority_queueA;

	priority_queueA.push(0);
	priority_queueA.push(1);
	priority_queueA.push(2);
	priority_queueA.emplace(3);
	cout << "priority_queueA.size() = " << priority_queueA.size() << endl;
	while (!priority_queueA.empty())
	{
		cout << priority_queueA.top() << endl;
		priority_queueA.pop();
	}

	return 0;
}

// priority_queueA.size() = 4
// 3
// 2
// 1
// 0
```

```c++
#include <queue>
#include <iostream>
using namespace std;

int main()
{
    // greater<int>设置小顶堆
	priority_queue<int, vector<int>, greater<int>> priority_queueA;

	priority_queueA.push(0);
	priority_queueA.push(1);
	priority_queueA.push(2);
	priority_queueA.emplace(3);
	cout << "priority_queueA.size() = " << priority_queueA.size() << endl;
	while (!priority_queueA.empty())
	{
		cout << priority_queueA.top() << endl;
		priority_queueA.pop();
	}

	return 0;
}

// priority_queueA.size() = 4
// 0
// 1
// 2
// 3
```

## 第10章 泛型算法文档

```c++
#include <algorithm>
#include <numeric> // 数值泛型算法
#include <iterator>
#include <functional>
```

```c++
// 查找【迭代器范围的元素的值】
// 成功返回【第一个找到的元素的迭代器】
// 失败返回【查找范围的尾后迭代器】
迭代器 find(查找范围的首迭代器, 查找范围的尾后迭代器, 要查找的元素的值)

头指针/迭代器 begin(内置数组/容器)
尾后指针/迭代器 end(内置数组/容器)

要查找的元素的值在查找范围出现的次数 count(查找范围的首迭代器, 查找范围的尾后迭代器, 要查找的元素的值)

// 累加的初值 + 【迭代器范围的元素的值】
累加的结果 accumulate(累加范围的首迭代器, 累加范围的尾后迭代器, 累加的初值)

// 比较【第一个容器迭代器范围的元素】是否等于【第二个容器从迭代器位置开始的元素】
// 如果两个范围大小不相等就报错
是否相等 equal(第一个容器比较范围的首迭代器, 第一个容器比较范围的尾后迭代器, 第二个容器比较范围的首迭代器)

fill(赋值范围的首迭代器, 赋值范围的尾后迭代器, 要赋的元素的值);
fill_n(赋值范围的首迭代器, 元素的个数, 要赋的元素的值);

调用push_back的插入迭代器 back_inserter(容器)

// 拷贝【源容器迭代器范围的元素】到【目的容器从迭代器位置开始的元素】
目的容器粘贴范围的尾后迭代器 copy(源容器拷贝范围的首迭代器, 源容器拷贝范围的尾后迭代器, 目的容器粘贴范围的首迭代器)

// 搜索替换【迭代器范围的元素的值】
replace(搜索替换范围的首迭代器, 搜索替换范围的尾后迭代器, 要被替换的原元素的值, 要替换成的新元素的值);

// 搜索替换【源容器迭代器范围的元素的值】，结果保存在【目的容器从迭代器位置开始的元素】
目的容器粘贴范围的尾后迭代器 replace_copy(源容器拷贝范围的首迭代器, 源容器拷贝范围的尾后迭代器, 目的容器粘贴范围的首迭代器, 要被替换的原元素的值, 要替换成的新元素的值);

// 排序【迭代器范围的元素】
sort(排序范围的首迭代器, 排序范围的尾后迭代器);

// 向前提【迭代器范围的非重复元素】覆盖【重复元素】
// 不减小容器元素的个数，需要手动删除多余部分
最后一个非重复元素的尾后迭代器 unique(迭代器范围的首迭代器, 迭代器范围的尾后迭代器);

// 向【二元谓词】传入【迭代器范围的元素】，调用【二元谓词】排序【迭代器范围的元素】
sort(排序范围的首迭代器, 排序范围的尾后迭代器, 二元谓词);

// 向【二元谓词】传入【迭代器范围的元素】，调用【二元谓词】稳定排序【迭代器范围的元素】
stable_sort(排序范围的首迭代器, 排序范围的尾后迭代器, 二元谓词);

// 向【一元谓词】传入【迭代器范围的元素】，调用【一元谓词】排序【迭代器范围的元素】，【返回true的元素】排在前面
partition(排序范围的首迭代器, 排序范围的尾后迭代器, 一元谓词);

// 向【一元谓词】传入【迭代器范围的元素】，查找【迭代器范围的传入一元谓词返回true的元素】
// 成功返回【返回true的元素的迭代器】
// 失败返回【调用范围的尾后迭代器】
迭代器 find_if(查找范围的首迭代器, 查找范围的尾后迭代器, 一元谓词)

// 向【可调用对象】传入【迭代器范围的元素】，调用【可调用对象】
for_each(调用范围的首迭代器, 调用范围的尾后迭代器, 可调用对象);

// 向【可调用对象】传入【源容器迭代器范围的元素】，调用【可调用对象】，返回值保存在【目的容器从迭代器位置开始的元素】
// 目的容器可以是源容器
目的容器写入范围的尾后迭代器 transform(源容器调用范围的首迭代器, 源容器调用范围的尾后迭代器, 目的容器写入范围的首迭代器, 可调用对象);

// 向【一元谓词】传入【迭代器范围的元素】，调用【一元谓词】
返回true的次数 count_if(调用范围的首迭代器, 调用范围的尾后迭代器, 一元谓词)

// 【原函数的参数】可以是【固定值】或【新函数的参数_1、_2、_3、...】
新函数名 bind(原函数名, 原函数的第一个参数, 原函数的第二个参数);

对象的引用 ref(对象)
对象的常量引用 cref(对象)

调用push_front的插入迭代器 front_inserter(容器)
调用insert的插入迭代器 inserter(容器, 容器插入范围的首迭代器)

// 拷贝【源容器迭代器范围的非重复元素】到【目的容器从迭代器位置开始的元素】
目的容器粘贴范围的尾后迭代器 unique_copy(源容器拷贝范围的首迭代器, 源容器拷贝范围的尾后迭代器, 目的容器粘贴范围的首迭代器);

正向迭代器 反向迭代器.base()

reverser(反转范围的首迭代器, 反转范围的尾后迭代器)
reverser(源容器反转范围的首迭代器, 源容器反转范围的尾后迭代器, 目的容器粘贴范围的首迭代器)

// 向【一元谓词】传入【迭代器范围的元素】，删除【迭代器范围的传入一元谓词返回true的元素】
第一个返回false的元素的迭代器 remove_if(删除范围的首迭代器, 删除范围的尾后迭代器, 一元谓词);

// 向【一元谓词】传入【源容器迭代器范围的元素】，拷贝【源容器迭代器范围的传入一元谓词返回true的元素】覆盖【目的容器从迭代器位置开始的元素】
目的容器粘贴范围的尾后迭代器 remove_copy_if(源容器的迭代器范围的首迭代器, 源容器的迭代器范围的尾后迭代器, 目的容器的覆盖范围的首迭代器, 一元谓词);
```

## 第10章 泛型算法

使用【泛型算法】查找、删除、替换、排列【元素】。

【泛型算法】支持【经典算法的通用接口】。
【泛型算法】支持【不同容器类型】和【不同元素类型】。

### 10.1 概述

【大多数泛型算法的头文件】是【algorithm】。
【数值泛型算法的头文件】是【numeric】。

```c++
#include <algorithm>
#include <numeric> // 数值泛型算法
```

【泛型算法】遍历【迭代器范围】，处理【迭代器范围的元素的值】。
使用【find()】查找【vector\<int\>的元素的值】。

```c++
auto 迭代器变量 = find(查找范围的首迭代器, 查找范围的尾后迭代器, 要查找的元素的值);
if (迭代器变量 != 查找范围的尾后迭代器)
{
	// 找到了要查找的元素的值
}
```

find()的参数。
find()的返回值。
比较【find()的返回值】和【查找范围的尾后迭代器】判断搜索是否成功。

因为【find()】操作【迭代器】，所以【find()】兼容【任何容器】。
使用【find()】查找【string的字符】。

```c++
auto 迭代器变量 = find(string变量.begin(), string变量.end(), 要查找的字符);
if (迭代器变量 != string变量.end())
{
	// 找到了要查找的元素的值
}
```

使用【find()】查找【内置数组的元素的值】。

```c++
auto 指针变量 = find(begin(数组变量), end(数组变量), 要查找的元素的值);
if (指针变量 != end(数组变量))
{
	// 找到了要查找的元素的值
}
```

使用【begin()】和【end()】获得【整个数组范围的指针左闭合区间】，传入【find()】。

【find()】可以传入【容器查找范围的迭代器范围】。

```c++
auto 指针变量 = find(数组变量 + 1, 数组变量 + 4, 要查找的元素的值);
if (指针变量 != end(数组变量))
{
	// 找到了要查找的元素的值
}
```

##### 算法如何工作

【find()的操作】使用了【迭代器】，【find()的操作】不依赖【容器类型】和【元素类型】。

##### 迭代器令算法不依赖于容器

【find()除了比较元素的值之外的操作】使用了【迭代器】。

##### 但算法依赖于元素类型的操作

【算法】使用了【元素类型的比较操作】。
比如，【find()】使用【元素类型的等于运算符==】比较【元素的值】。
【其他的算法】可能使用【元素类型的小于运算符<】比较【元素的值】。
【大多数算法】可以自定义【元素比较操作】。

【算法本身】永远不会改变【底层容器的大小】。

赋值【插入迭代器】，【插入迭代器】向【底层容器】插入【元素】。
向【算法】传入【插入迭代器】，【插入迭代器】向【底层容器】插入【元素】，【算法本身】永远不会改变【底层容器的大小】。

### 10.2 初识泛型算法

【标准库算法】有【统一原则】。
【附录A】按照【操作方式】列出了【所有标准库算法】。

【大多数算法】操作【范围内的元素】。
【这个范围】称作【输入范围】。
【传入输入范围的算法的前两个参数】是【输入范围】，【一对迭代器】构成【迭代器范围】表示【输入范围】。

【大多数算法】【遍历输入范围的元素的方式】类似，【操作输入范围的元素的方式】不同。
了解【算法】是否读取、修改、排列【元素】。

#### 10.2.1 只读算法

【一些算法】只读取【输入范围的元素】，不修改【输入范围的元素】。
比如【find()】、【count()】。

【accumulate()】是【只读算法】。
【accumulate()的头文件】是【numeric】。
accumulate()的参数。

```c++
#include <numeric> // 数值泛型算法
```

accumulate()的功能。

【accumulate()的第三个参数】决定【accumulate()使用哪个加法运算符】以及【返回值的类型】。

##### 算法和元素类型

【accumulate()的累加范围的元素类型】和【第三个参数的类型】相同，或者能转换为【第三个参数的类型】。

因为【string】支持【加法运算符+】，所以可以使用【accumulate()】连接【vector\<string\>的所有string】。

```
string string变量 = accumulate(元素类型string的vector变量.cbegin(), 元素类型string的vector变量.cend(), string("");
```

使用【accumulate()】连接【vector\<string\>的所有string】到【传入accumulate()的第三个参数string】上，【这个string】初始为【空串】。
【传入accumulate()的第三个参数】必须使用【string("")】。

如果【传入accumulate()的第三个参数】直接使用【""】，那么【传入accumulate()的第三个参数】是【const char\*类型】，【const char\*类型】不支持【+运算符】，会报错。

如果向【只读取元素的算法】传入【迭代器】，并且不使用【算法返回的迭代器】改变【元素】，那么推荐向【算法】传入【.cbegin()】和【.cend()】。

##### 操作两个序列的算法

【equal()】是【只读算法】，equal()的功能。
equal()的返回值。
equal()的参数。

因为【equal()】使用【迭代器操作】，所以可以使用【equal()】比较【不同类型的容器】保存的【不同类型的元素】，只要可以使用【==运算符】比较【这两种元素类型】。

【传入equal()的两个范围的大小】必须相等。

#### 10.2.2 写容器元素的算法

【一些算法】赋值【输入范围的元素】。
【输出范围的大小】必须大于等于【算法赋值的元素的个数】。

【一些算法】赋值【输入范围的元素】，最多赋值【输入范围大小个元素】。

比如【fill()】，fill()的参数。
fill()的功能。

【一些算法】传入【两个范围】，【两个范围】的【容器类型】可以不相同，【元素类型】可以不相同，只要可以使用【==运算符】比较【两个范围的元素类型】。
比如【equal()】。

【一些算法】传入【三个迭代器参数】指向【两个范围】，【前两个迭代器参数】指向【第一个范围】，【第三个迭代器参数】指向【第二个范围的开始位置】。
【一些算法】传入【四个迭代器参数】指向【两个范围】，【前两个迭代器参数】指向【第一个范围】，【后两个迭代器参数】指向【第二个范围】。

【一些算法】传入【三个迭代器参数】指向【两个范围】，【第二个范围的大小】必须大于等于【第一个范围的大小】。
比如【equal()】。

##### 算法不检查拼写操作

【一些算法】传入【一个指向输出范围的开始位置的迭代器参数】。
比如【fill_n()】，fill_n()的参数。
fill_n()的功能。

【空容器】调用【fill_n()】是错误的。

【从输出范围的开始位置写入元素的算法】假定【输出范围的大小】大于等于【要写入的元素的个数】。

##### 介绍back_inserter

【算法】可以使用【插入迭代器】指向【输出范围的开始位置】，确保【输出范围的大小】大于等于【要写入的元素的个数】。
赋值【(\*迭代器)】，赋值【迭代器指向的元素】。
赋值【(\*插入迭代器)】，向【插入迭代器指向的容器的尾部】插入【和值相等的元素】。

【back_inserter()的头文件】是【iterator】。

```c++
#include <iterator>
```

back_inserter()的参数，back_inserter()的返回值。

使用【back_inserter()】获取【插入迭代器】，传入【算法】。

```c++
fill_n(back_inserter(容器变量), 元素的个数, 元素的值);
```

【fill_n()】通过【插入迭代器】赋值【输入序列的元素】。
赋值【(\*插入迭代器)】，向【插入迭代器指向的容器的尾部】插入【和值相等的元素】。

##### 拷贝算法

【拷贝算法】向【一个迭代器参数指向的输出范围的开始位置】写入【元素】。
copy()的参数。
【输出范围的大小】必须大于等于【输入范围的大小】。

使用【copy()】拷贝【内置数组】。

```c++
auto 目的数组粘贴范围的尾后迭代器 copy(源数组拷贝范围的首迭代器, 源数组拷贝范围的尾后迭代器, 目的数组粘贴范围的首迭代器)
```

copy()的返回值。

【一些算法】有【拷贝版本】，在【输出范围】保存【算法的结果】。

比如【replace()】，replace()的功能。
replace()的参数。

【replace_copy()】是【replace()的拷贝版本】。
replace_copy()的参数。

replace_copy()的功能。

#### 10.2.3 重排容器元素的算法

【一些算法】排列【输入范围的元素】，比如【sort()】。
sort()的功能，【sort()】使用【元素类型的<运算符】排列【元素】。

想要【一段文本出现的单词】按照【字典顺序】保存在【vector】，每个单词只出现一次。

##### 消除重复单词

想要消除【vector的重复单词】。
使用【sort()】按照【字典顺序】排列【单词】。
使用【unique()】向前提【没出现过的单词】覆盖【重复单词】。
调用【.erase()】删除【重复单词】。

```c++
sort(word.begin(), word.end());
word.erase(unique(word.begin(), word.end()), word.end());
```

sort()的参数。

##### 使用unique

unique()的功能，unique()的参数。

【unique()】向前提【没出现过的元素】覆盖【重复元素】，不删除【元素】。

##### 使用容器操作删除元素

调用【.erase()】删除【重复单词】。

【.erase()】可以删除【空范围】。

### 10.3 定制操作

【一些算法】比较【输入范围的元素】。
默认情况，使用【元素类型的小于运算符<或等于运算符==】比较【元素】。
可以自定义【元素比较操作】。

比如【sort()】。
如果想要自定义【元素比较操作】或者【元素类型】不支持【小于运算符<】。
就需要自定义【元素比较操作】。

#### 10.3.1 向算法传递函数

想要打印【按照字典顺序保存一段文本出现的单词的vector】。
想要【vector的单词】按照【长度大小】排列，【相同长度的单词】按照【字典顺序】排列。
使用【重载版本的三个参数的sort()】排列【保存单词的vector】，【sort()的第三个参数】是【谓词】。

##### 谓词

【谓词】是【可调用表达式】，返回【判断结果】。
【标准库算法】使用【两种谓词】。
【一元谓词】有【一个参数】，【二元谓词】有【两个参数】。
【有谓词参数的算法】对【输入范围的元素】调用【谓词】。
【输入范围的元素】必须可以转换为【谓词的参数类型】。

【传入二元谓词参数的sort()】调用【这个二元谓词】比较【元素】。
【传入sort()的谓词】必须满足【11.2.2节 P378】介绍的【条件】。
【二元谓词】必须可以使用【一个统一的规则】排列【输入范围的元素】。
在【6.2.2节 P189】中的【isShorter函数】满足【这个条件】，向【传入二元谓词参数的sort()】传入【isShorter函数】作为【二元谓词参数】。
按照【长度大小】排列【vector的单词】。

```c++
bool isShorter(const string& s1, const string& s2)
{
	return s1.size() < s2.size();
}
```

```c++
sort(word.begin(), word.end(), isShorter);
```

##### 排序算法

想要【vector的单词】按照【长度大小】排列，【相同长度的单词】按照【字典顺序】排列。
使用【stable_sort()】按照【长度大小】排列【vector的单词】，【相同长度的单词之间的相对顺序】不变，按照【字典顺序】排列。
【stable_sort()】是【稳定排序算法】，【稳定排序算法】不改变【相等元素之间的相对顺序】。

```c++
bool isShorter(const string& s1, const string& s2)
{
	return s1.size() < s2.size();
}
```

```c++
stable_sort(word.begin(), word.end(), isShorter);
```

partition()的参数，partition()的功能。
partition()的返回值。

#### 10.3.2 lambda

【某些算法的参数】只能是【一元谓词】，【某些算法的参数】只能是【二元谓词】。
【一元谓词】只有【一个参数】，【二元谓词】只有【两个参数】，【谓词】需要【更多的参数】。

想要获取【vector大于等于某个长度的单词数量】，想要打印【vector大于等于某个长度的单词】。

现在【vector的单词】按照【长度大小】排列，【相同长度的单词】按照【字典顺序】排列。

使用【find_if()】寻找【vector第一个大于某个长度的单词】。
find_if()的参数。
find_if()的功能。
find_if()的返回值。

【判断单词长度是否大于等于某个长度的函数】需要【单词和长度两个参数】，不是【一元谓词】。
【find_if()的参数】只能是【一元谓词】。
使用【lambda表达式】作为【一元谓词】，增加【一元谓词的参数数量】。

##### 介绍lambda

可以向【算法】传入【任何类型的可调用对象】。
【可调用对象或表达式】可以使用【调用运算符()】。
可调用对象或表达式(参数列表)。

【四种可调用对象】：
【函数】。
【函数指针】。
【重载了调用运算符()的类】。
【lambda表达式】。

【lambda表达式】有【参数列表】、【返回类型】、【函数体】，和【函数】类似。
【lambda表达式】有【捕获列表】，可以在【函数】内定义【lambda表达式】，和【函数】不同。
【lambda表达式】。

```c++
[捕获列表](参数列表)->返回类型 {函数体}
```

【lambda表达式的捕获列表】是【lambda表达式所在函数的局部变量的列表】。
【lambda表达式】使用【尾置返回】指定【返回类型】，和【函数】不同。

【lambda表达式】必须有【捕获列表】和【函数体】。

使用【lambda表达式】定义【可调用对象】。

```c++
auto 可调用对象 = [捕获列表](参数列表)->返回类型 {函数体};
```

使用【调用运算符()】调用【可调用对象】。

```c++
可调用对象(参数列表)
```

【lambda表达式】没写【参数列表】相当于使用【空参数列表】。

【lambda表达式】没写【返回类型】。

```
if (lambda表达式没写返回类型)
{
	if (函数体只有return)
	{
		根据返回的表达式的类型推断返回类型
	}
	else
	{
		返回void类型
	}
}
```

##### 向lambda传递参数

使用【传入lambda表达式的实参】初始化【lambda表达式的形参】。
【lambda表达式】的【实参类型】和【形参类型】必须匹配，和【函数】类似。
【lambda表达式】不支持【默认参数】，和【函数】不同。
【实参个数】和【形参个数】相等。
初始化【形参】之后，执行【函数体】。

可以使用【lambda表达式】完成【isShorter函数的功能】。

```c++
bool isShorter(const string& s1, const string& s2)
{
	return s1.size() < s2.size();
}
```

```c++
[](const string& s1, const string& s2) {return s1.size() < s2.size(); };
```

【lambda表达式的捕获列表】为空，【lambda表达式】不使用【lambda表达式所在函数的局部变量】。
【lambda表达式的参数】是【const string&】，和【isShorter函数】类似。
【lambda表达式的函数体】比较【传入lambda表达式的两个const string&的.size()】，返回【比较结果】，和【isShorter函数】类似。

调用【stable_sort()】，向【stable_sort()】传入【lambda表达式】。

```c++
stable_sort(word.begin(), word.end(), [](const string& s1, const string& s2) {return s1.size() < s2.size(); });
```

【stable_sort()】调用【lambda表达式】比较【stable_sort()输入范围的元素】。

##### 使用捕获列表

向【find_if()】传入【判断单词长度是否大于等于某个长度的lambda表达式】。
【find_if()】调用【lambda表达式】，向【lambda表达式】传入【单词】和【某个长度】，【lambda表达式】比较【单词长度】是否大于等于【某个长度】，返回【比较结果】。

【lambda表达式】可以使用【lambda表达式所在函数的局部变量】。
在【lambda表达式的捕获列表】写上【lambda表达式使用的所在函数的局部变量】，使用【逗号,】分隔【局部变量】。
【lambda表达式的捕获列表】指引【lambda表达式】保存【lambda表达式访问局部变量需要的信息】。

【判断单词长度是否大于等于某个长度的lambda表达式】捕获【某个长度】，传入【单词】。
【lambda表达式】比较【单词长度】是否大于等于【某个长度】。

```c++
[size](const string &s) {return s.size() >= size;};
```

##### 调用find_if

调用【find_if()】，向【find_if()】传入【lambda表达式】查找【第一个长度大于等于某个长度的单词】。

```c++
auto it = find_if(word.begin(), word.end(), [size](const string& s) {return s.size() >= size; });
```

【find_if()】成功返回【指向第一个长度大于等于某个长度单词的迭代器】。
失败返回【word.end()】。

使用【find_if()返回的指向第一个长度大于等于某个长度单词的迭代器】计算【vector大于等于某个长度的单词数量】。

```c++
auto it = find_if(word.begin(), word.end(), [size](const string& s) {return s.size() >= size; });
auto count = word.end() - it;
```

##### for_each算法

想要打印【vector大于等于某个长度的单词】。
使用【for_each()】打印【vector大于等于某个长度的单词】。
向【for_each()】传入【可调用对象】，【for_each()】调用【可调用对象】，向【可调用对象】传入【for_each()输入范围的元素】。

```c++
auto it = find_if(word.begin(), word.end(), [size](const string& s) {return s.size() >= size; });
for_each(it, word.end(), [](const string &s) {cout << s << endl; });
```

【捕获列表】只能捕获【局部非static变量】。
【lambda表达式】可以直接使用【局部static变量】和【全局变量】。

##### 完整的biggies

```c++
void biggies(vector<string>& word, vector<string>::size_type size)
{
	sort(word.begin(), word.end());
	word.erase(unique(word.begin(), word.end()), word.end());
	stable_sort(word.begin(), word.end(), [](const string& s1, const string& s2) {return s1.size() < s2.size(); });
	auto it = find_if(word.begin(), word.end(), [size](const string& s) {return s.size() >= size; });
	cout << word.end() - it << endl;
	for_each(it, word.end(), [](const string& s) {cout << s << endl; });
}
```

#### 10.3.3 lambda捕获和返回

定义【lambda表达式】，【编译器】生成【与lambda表达式对应的类类型】。
在【14.8.1节 P507】介绍【与lambda表达式对应的新的类类型】。
向【函数】传入【lambda表达式】，【编译器】定义了【与lambda表达式对应的类类型】和【类对象】：
【传入函数的参数】是【类对象】。
使用【auto】定义【lambda表达式初始化的变量】，定义了【类对象】。

默认情况，【与lambda表达式对应的类类型的成员变量】有【lambda表达式的捕获列表的局部变量】。
在定义【类对象】时初始化【成员变量】，和【普通类的成员变量】类似。

##### 值捕获

【捕获列表】可以【捕获值】、【捕获引用】，和【传递参数】类似。
【表10.1 P352】列出了【捕获列表的写法】。
【值捕获的局部变量】必须可以拷贝，和【值传递参数】类似。
因为在定义【lambda表达式】时拷贝【值捕获的局部变量】到【lambda表达式】，而不是在调用【lambda表达式】时。
所以在定义【lambda表达式】之后修改【值捕获的局部变量】不会影响【lambda表达式的值捕获】。

```c++
#include <iostream>
using namespace std;

int main()
{
	size_t 值捕获的局部变量 = 11111111;
	auto 可调用对象 = [值捕获的局部变量](int n)->int {
		// ++值捕获的局部变量; // 报错
		return 值捕获的局部变量 + n;
	};
	值捕获的局部变量 = 22222222;
	cout << 可调用对象(0) << endl;    // 11111111
	cout << 值捕获的局部变量 << endl; // 22222222

	return 0;
}
```

##### 引用捕获

【捕获列表】可以【捕获引用】。

```c++
#include <iostream>
using namespace std;

int main()
{
	size_t 引用捕获的局部变量 = 11111111;
	auto 可调用对象 = [&引用捕获的局部变量](int n)->int {
		++引用捕获的局部变量;
		return 引用捕获的局部变量 + n;
	};
	引用捕获的局部变量 = 22222222;
	cout << 可调用对象(0) << endl;     // 22222223
	cout << 引用捕获的局部变量 << endl; // 22222223

	return 0;
}
```

【&】表示【捕获引用】。
【捕获引用】和【返回引用】类似。
修改【lambda表达式的引用捕获】是修改【lambda表达式所在函数的局部变量】。
在上面的代码中，【lambda表达式】返回【lambda表达式所在函数的局部变量】。

【捕获引用】和【返回引用】类似。
在执行【lambda表达式】时【引用捕获的局部变量】必须存在。
在【lambda表达式所在函数】结束之后销毁【引用捕获的局部变量】。
如果在【lambda表达式所在函数】结束之后执行【lambda表达式】，那么【引用捕获的局部变量】已经销毁。

【不能拷贝的局部变量】不能【值捕获】，只能【引用捕获】。
比如【ostream类型】。
想要使用【ostream类型】打印数据。

```c++
void biggies(vector<string>& word, vector<string>::size_type size, ostream &os = cout, char c = ' ')
{
	sort(word.begin(), word.end());
	word.erase(unique(word.begin(), word.end()), word.end());
	stable_sort(word.begin(), word.end(), [](const string& s1, const string& s2) {return s1.size() < s2.size(); });
	auto it = find_if(word.begin(), word.end(), [size](const string& s) {return s.size() >= size; });
	os << word.end() - it << endl;
	for_each(it, word.end(), [&os, c](const string& s) {os << s << c; });
}
```

向【函数】传入【lambda表达式】，会立刻执行【lambda表达式】。
比如【for_each()】。
因为在执行【传入for_each()的lambda表达式】时【lambda表达式捕获的局部变量】存在。
所以【传入for_each()的lambda表达式】可以使用【引用捕获】。

从【函数】返回【lambda表达式】，【函数】直接返回【可调用对象】，或者返回【包含可调用对象的成员变量的类对象】。
【函数返回的lambda表达式】不能使用【引用捕获】。

可以值捕获【非迭代器、引用、指针局部变量】。
只需要保证【局部变量在捕获时的值】正确。

可以引用捕获【局部变量】，可以值捕获【迭代器、引用、指针局部变量】，必须保证在执行【lambda表达式】时【局部变量】存在。
【局部变量的值】正确。
从【定义lambda表达式时】到【执行lambda表达式时】，可能有代码改变【捕获的迭代器、引用、指针的值】。
在定义【lambda表达式】时【捕获的迭代器、引用、指针的值】正确，在执行【lambda表达式】时【捕获的迭代器、引用、指针的值】错误。

应该尽量减少捕获【局部变量】，应该避免捕获【迭代器、引用、指针局部变量】。

##### 隐式捕获

可以不直接写出【lambda表达式捕获的局部变量】，【编译器】可以根据【lambda表达式的函数体的代码】推测【lambda表达式需要捕获的局部变量】。
【[&]】表示【隐式声明引用捕获所有局部变量】，【[=]】表示【隐式声明值捕获所有局部变量】。

可以混合使用【隐式捕获】和【显式捕获】，值捕获【一部分局部变量】，引用捕获【另一部分局部变量】。

【[&, =显式声明值捕获的局部变量]】表示值捕获【=号右面的局部变量】，引用捕获【其他的局部变量】。
【[=, &显式声明引用捕获的局部变量]】表示引用捕获【&号右面的局部变量】，值捕获【其他的局部变量】。

在混合使用【隐式捕获】和【显式捕获】时，捕获列表的开头必须是【&】或【=】。
【&】和【=】指定了【默认捕获方式】。

在混合使用【隐式捕获】和【显式捕获】时，【显式声明的局部变量】和【隐式声明的局部变量】的【捕获方式】必须不同。

###### 表10.1 lambda捕获列表  P352

```c++
[]                                   // 空捕获列表
[值捕获的局部变量, &引用捕获的局部变量] // 显式声明，默认值捕获【局部变量】，引用捕获【&前缀的局部变量】
[&]				                     // 隐式声明，引用捕获【lambda表达式的函数体使用的所有局部变量】
[=]				                     // 隐式声明，值捕获【lambda表达式的函数体使用的所有局部变量】
[&, =显式声明值捕获的局部变量]	         // 隐式声明和显式声明，值捕获【=号右面的局部变量】，引用捕获【lambda表达式的函数体使用的其他的局部变量】
[=, &显式声明引用捕获的局部变量]       // 隐式声明和显式声明，引用捕获【=号右面的局部变量】，引用捕获【lambda表达式的函数体使用的其他的局部变量】
```

##### 可变lambda

默认情况，【lambda表达式】不能在【lambda表达式的函数体】修改【值捕获】。
【可变lambda表达式】可以修改【值捕获】，在【参数列表】之后写上【关键字mutable】。
【可变lambda表达式】可以省略【参数列表】。

```c++
#include <iostream>
using namespace std;

int main()
{
	size_t 值捕获的局部变量 = 11111111;
	auto 可调用对象 = [值捕获的局部变量](int n)mutable->int {
		++值捕获的局部变量;
		return 值捕获的局部变量 + n;
	};
	值捕获的局部变量 = 22222222;
	cout << 可调用对象(0) << endl;    // 11111112
	cout << 值捕获的局部变量 << endl; // 22222222

	return 0;
}
```

【lambda表达式】可以修改【非const局部变量的引用捕获】。

【值捕获】和【函数的const非引用形参】类似。
【可变lambda表达式的值捕获】和【函数的非引用形参】类似。
【引用捕获】和【函数的引用形参类似】类似。

在定义【lambda表达式】时，【捕获列表】传参。
在执行【lambda表达式】时，【参数列表】传参。
在执行【函数】时，【参数列表】传参。

##### 指定lambda返回类型

```
if (lambda表达式没写返回类型)
{
	if (函数体只有return)
	{
		根据返回的表达式的类型推断返回类型
	}
	else
	{
		返回void类型
	}
}
```

transform()的参数。
transform()的功能。
【transform()的输入范围】和【输出范围】可以相同，【输入范围的元素传入transform()的返回值】会覆盖【输入范围的元素】。

【lambda表达式】使用【尾置返回】。

count_if()的参数，count_if()的功能。
count_if()的返回值。

#### 10.3.4 参数绑定

【lambda表达式】适合【使用一两次的简单的操作】。
【函数】适合【使用多次的有很多语句的操作】。

【函数】可以替代【没有捕获变量的lambda表达式】。

【函数】很难替代【有捕获变量的lambda表达式】。

【只有一个参数，有很多捕获变量的lambda表达式】是【一元谓词】，【lambda表达式捕获的变量】不计入【参数个数】。
想要使用【函数】替代【一个参数一个捕获变量的lambda表达式】，【函数】使用【两个参数】替代【lambda表达式】的【一个参数】和【一个捕获变量】，【有两个参数的函数】不是【一元谓词】，不能向【传入一元谓词的算法】传入【这个函数】。

##### 标准库bind函数

【bind()】可以解决【函数的参数个数问题】，【bind()的头文件】是【functional】。
【bind()】是【函数适配器】。
bind()的参数，bind()的功能。

```c++
#include <functional>
```

```c++
auto 新可调用对象 = bind(原可调用对象, 原可调用对象的参数列表);
```

调用【新调用对象】，【新调用对象】会调用【原可调用对象】，向【原可调用对象】传入【原可调用对象的参数】。

【原可调用对象的参数】，可以是【常量】，也可以是【新可调用对象的参数】。
【新可调用对象的参数】在【原可调用对象的参数列表】写成【占位符】，【占位符的写法】是【\_n】，【n】是【正整数】。
使用【新可调用对象的第n个参数】替换【原可调用对象的\_n参数】。

```c++
// 声明原可调用对象，为了举例声明一下，实际使用不需要声明
原可调用对象(int nA, int nB, int nC);

// 定义新可调用对象
auto 新可调用对象 = bind(原可调用对象, 11111111, _2, _1);

// 调用新可调用对象
新可调用对象(22222222, 33333333)

// 相当于调用原可调用对象
原可调用对象(11111111, 33333333, 22222222)
```

```c++
#include <functional>
#include <iostream>
using namespace std;

// 原可调用对象FunA
void FunA(int nA, int nB, int nC)
{
	cout << nA << nB << nC << endl;
}

int main()
{
	// 定义新可调用对象FunA
	auto FunB = bind(FunA, 11111111, placeholders::_2, placeholders::_1);

	// 调用新可调用对象
	FunB(22222222, 33333333);

	// 相当于调用原可调用对象
	FunA(11111111, 33333333, 22222222);

	return 0;
}
```

##### 绑定check_size的sz参数

【bind()】可以解决【函数的参数个数问题】。

```c++
bool check_size(const string &s, string::size_type size)
{
	return s.size() >= size;
}

void biggies(vector<string>& word, vector<string>::size_type size, ostream& os = cout, char c = ' ')
{
	sort(word.begin(), word.end());
	word.erase(unique(word.begin(), word.end()), word.end());
	stable_sort(word.begin(), word.end(), [](const string& s1, const string& s2) {return s1.size() < s2.size(); });
	auto it = find_if(word.begin(), word.end(), bind(check_size, placeholders::_1, size));
	os << word.end() - it << endl;
	for_each(it, word.end(), [&os, c](const string& s) {os << s << c; });
}
```

##### 使用placeholders名字

【占位符_n】定义在【命名空间placeholders】，【命名空间placeholders】定义在【命名空间std】。

声明命名空间。

```c++
using namespace 命名空间名字;
```

```
using namespace 命名空间A名字::定义在命名空间A的命名空间B名字;
```

【命名空间placeholders】也定义在【头文件functional】。

##### bind的参数

【bind()】可以重排【可调用对象的参数顺序】。

##### 用bind重排函数参数顺序

【bind()】重排【sort()的参数顺序】。

```c++
bool isShorter(const string& s1, const string& s2)
{
	return s1.size() < s2.size();
}
```

```c++
// 从小到大排序
sort(word.begin(), word.end(), isShorter);

// 从大到小排序
sort(word.begin(), word.end(), bind(isShorter, placeholders::_2, placeholders::_1));
```

##### 绑定引用参数

默认情况，【bind()】拷贝【原可调用对象的参数列表的不是占位符的参数】到【新可调用对象】。
如果不能拷贝【原可调用对象的参数列表的不是占位符的参数】，或者想要使用【参数的引用】，可以使用【ref()】获得【对象的引用】。

```c++
ostream& print(ostream& os, const string& s, char c)
{
	return os << s << c;
}

void biggies(vector<string>& word, vector<string>::size_type size, ostream& os = cout, char c = ' ')
{
	sort(word.begin(), word.end());
	word.erase(unique(word.begin(), word.end()), word.end());
	stable_sort(word.begin(), word.end(), [](const string& s1, const string& s2) {return s1.size() < s2.size(); });
	auto it = find_if(word.begin(), word.end(), [size](const string& s) {return s.size() >= size; });
	os << word.end() - it << endl;
	for_each(it, word.end(), bind(print, ref(os), placeholders::_1, c));
}
```

ref()的返回值。
cref()的返回值。
【ref()和cref()的头文件】是【functional】。

```c++
#include <functional>
```

### 10.4 再探迭代器

标准库的其他几种迭代器。

【插入迭代器】：【插入迭代器】绑定【容器变量】，赋值【插入迭代器】，【插入迭代器】向【插入迭代器绑定的容器变量】插入【元素】。
【流迭代器】：【流迭代器】绑定【输入流】或【输出流】，使用【流迭代器】遍历【流迭代器绑定的输入流或输出流】。
【反向迭代器】：【反向迭代器】绑定【容器变量】，递增【反向迭代器】，【反向迭代器】向【容器头部】移动。【除了forward_list其他的容器】都支持【反向迭代器】。
【移动迭代器】：【移动迭代器】移动【元素】，不拷贝【元素】。

#### 10.4.1 插入迭代器

【插入器】是【迭代器适配器】，插入器的参数，插入器的返回值，插入器的功能。
赋值【插入迭代器】，【插入迭代器】使用【容器操作】向【插入迭代器指向的容器的位置】插入【元素】。

###### 表10.2 插入迭代器操作 P358

```c++
// 插入元素
iterator变量 = 元素的值

// 返回iterator变量本身的操作
iterator变量A *iterator变量A
iterator变量A ++iterator变量A
iterator变量A iterator变量A++
```

有【三种插入器】，【三种插入器】返回【三种插入迭代器】，【三种插入迭代器】向【容器的三种位置】插入【元素】：

back_inserter()的返回值。
front_inserter()的返回值。
inserter()的返回值，inserter()的参数。

【传入插入器的容器】必须支持【插入器使用的容器操作】。

赋值【inserter()返回的插入迭代器】，向【inserter()返回的迭代器指向的元素之前】插入【元素】，【inserter()返回的插入迭代器】总是指向【某个元素】。

赋值【front_inserter()返回的插入迭代器】，向【容器的首元素】之前插入【元素】，【front_inserter()返回的插入迭代器】总是指向【容器的首元素】。

使用【front_inserter()返回的插入迭代器】插入【元素序列】，【元素序列在容器中的顺序】和【插入顺序】相反。

#### 10.4.2 iostream迭代器

【iostream】有【iostream迭代器】。
【istream_iterator】可以读取【输入流】。
【ostream_iterator】可以写入【输出流】。
【iostream迭代器】把【iostream】当作【特定类型的元素序列】。
向【泛型算法】传入【iostream迭代器】读写【iostream】。

##### istream_iterator操作

定义【 iostream迭代器变量】必须指定【iostream迭代器变量读写的类型】。
因为【istream_iterator】使用【>>运算符】读取【istream】。
所以【istream_iterator读取的类型】必须支持【输入运算符>>】。
可以向【istream_iterator的有参构造】传入【istream】定义【istream_iterator变量】。
可以使用【istream_iterator的无参构造】定义【作为尾后迭代器的istream_iterator变量】。

```c++
istream_iterator<读取类型> istream_iterator变量(istream变量);
istream_iterator<读取类型> 作为尾后迭代器的istream_iterator变量;
```

定义【istream_iterator变量】和【尾后istream_iterator变量】，从【cin】读取【int】，插入【vector】。

```c++
#include <vector>
#include <iostream>
using namespace std;

int main()
{
	istream_iterator<int> in_it(cin);
	istream_iterator<int> in_it_eof;
	vector<int> vec;

	while (in_it != in_it_eof)
	{
		vec.push_back(*in_it++);
	}

	for (auto a : vec)
	{
		cout << a << endl;
	}

	return 0;
}
```

定义【istream_iterator变量】和【尾后istream_iterator变量】，从【cin】读取【int】，初始化容器【vector】。

```c++
#include <vector>
#include <iostream>
using namespace std;

int main()
{
	istream_iterator<int> in_it(cin);
	istream_iterator<int> in_it_eof;
	vector<int> vec(in_it, in_it_eof);

	for (auto a : vec)
	{
		cout << a << endl;
	}

	return 0;
}
```

###### 表10.3 istream_iterator操作 P360

```c++
istream_iterator<读取类型> istream_iterator变量(istream变量);
istream_iterator<读取类型> 作为尾后迭代器的istream_iterator变量;

// istream_iterator变量A和istream_iterator变量B的类型必须相同
istream_iterator变量是否指向相同istream istream_iterator变量A == istream_iterator变量B
istream_iterator变量是否指向相同istream istream_iterator变量A != istream_iterator变量B

istream_iterator变量读取的值 *istream_iterator变量
istream_iterator变量-> 相当于 *istream_iterator变量.
istream_iterator变量的引用 ++istream_iterator变量
istream_iterator变量的旧值 istream_iterator变量++
```

定义【istream_iterator变量】和++【istream_iterator变量】都会【等待输入】。

```c++
#include <iostream>
using namespace std;

int main()
{
	istream_iterator<int> istream_iteratorA(cin); // 输入 0
	++istream_iteratorA;						  // 输入 1
	++istream_iteratorA;						  // 输入 2
	++istream_iteratorA;						  // 输入 3

	return 0;
}
```

```c++
#include <iostream>
using namespace std;

int main()
{
	istream_iterator<int> istream_iteratorA(cin); // 输入 0
	istream_iteratorA++;						  // 输入 1
	istream_iteratorA++;						  // 输入 2
	istream_iteratorA++;						  // 输入 3

	return 0;
}
```

循环输入输出。

```c++
#include <iostream>
using namespace std;

int main()
{
	istream_iterator<int> istream_iteratorA(cin); // 0 输入
	cout << *istream_iteratorA << endl;		      // 0 输出
	istream_iteratorA++;						  // 1 输入
	cout << *istream_iteratorA << endl;		      // 1 输出
	istream_iteratorA++;						  // 2 输入
	cout << *istream_iteratorA << endl;		      // 2 输出

	return 0;
}
```

先输入一次，再循环输入输出。
【*istream_iteratorA++】返回【istream_iteratorA++之前的输入】，正确。

```c++
#include <iostream>
using namespace std;

int main()
{
	istream_iterator<int> istream_iteratorA(cin); // 0 输入
	cout << *istream_iteratorA++ << endl;		  // 1 输入 0 输出
	cout << *istream_iteratorA++ << endl;		  // 2 输入 1 输出
	cout << *istream_iteratorA++ << endl;		  // 3 输入 2 输出

	return 0;
}
```

先输入一次，再循环输入输出。
【*++istream_iteratorA】返回【++istream_iteratorA之前的输入】，错误。

```c++
#include <iostream>
using namespace std;

int main()
{
	istream_iterator<int> istream_iteratorA(cin); // 0 输入
	cout << *++istream_iteratorA << endl;		  // 1 输入 1 输出
	cout << *++istream_iteratorA << endl;		  // 2 输入 2 输出
	cout << *++istream_iteratorA << endl;		  // 3 输入 3 输出

	return 0;
}
```

##### 使用算法操作流迭代器

因为【算法】使用【迭代器操作】，【iostream迭代器】是【迭代器】，所以可以向【某些算法】传入【iostream迭代器】。
在【10.5.1节 P365】介绍【可以传入iostream迭代器的算法】。
比如【accumulate()】。

```c++
#include <numeric> // 数值泛型算法
#include <iostream>
using namespace std;

int main()
{
	istream_iterator<int> in_it(cin);
	istream_iterator<int> in_it_eof;

	cout << accumulate(in_it, in_it_eof, 0) << endl;

	return 0;
}
```

##### istream_iterator允许使用懒惰求值

定义【绑定istream的istream_iterator变量】，标准库不保证【istream_iterator】会立即读取【数据】。
可以推迟【istream_iterator从istream读取数据的时间】，直到使用【istream_iterator】时才会读取【数据】。
标准库保证在【第一次解引用istream_iterator】之前，【istream_iterator】已经读取了【数据】。
虽然【大多数程序】使用【立即读取方式】和【推迟读取方式】没有区别。
但是如果【程序】定义了【没有使用的绑定istream的istream_iterator变量】，或者【程序】使用了【两个istream_iterator变量】同步读取【同一个istream】，那么【程序】使用【立即读取方式】和【推迟读取方式】有区别。

##### ostream_iterator操作

【ostream_iterator写入的类型】必须支持【输出运算符<<】。
定义【ostream_iterator变量】必须向【ostream_iterator的有参构造】传入【ostream】，还可以传入【一个字符数组】，在每次输出之后也输出【这个字符数组】。
【ostream_iterator变量】必须绑定【ostream】，不支持【作为尾后迭代器的ostream_iterator变量】。
【这个字符数组】必须是【以'0'结尾的C风格字符数组】。

###### 表10.4 ostream_iterator操作 P361

```c++
ostream_iterator<读取类型> ostream_iterator变量(ostream变量);
ostream_iterator<读取类型> ostream_iterator变量(ostream变量, 字符数组);
ostream_iterator变量 = 写入ostream_iterator变量绑定的ostream变量的值

// 返回ostream_iterator变量本身的操作
ostream_iterator变量A *ostream_iterator变量A
ostream_iterator变量A ++ostream_iterator变量A
ostream_iterator变量A ostream_iterator变量A++
```

使用【ostream_iterator】输出【容器的元素】。

```c++
#include <vector>
#include <iostream>
using namespace std;

int main()
{
	ostream_iterator<int> out_it(cout, " ");
	vector<int> vec{ 0,1,2,3,4,5,6,7,8,9 };

	for (auto a : vec)
	{
		*out_it++ = a;
	}
	cout << endl;

	return 0;
}
```

【程序】向【cout】写入【vec的元素】，【每个元素】后加【空格】。
赋值【out_it】会提交【写操作】。

【ostream_iterator】忽略【解引用运算符\*】和【递增运算符++】。
不使用【解引用运算符\*】和【递增运算符++】，重写【使用ostream_iterator输出容器的元素的代码】。

```c++
#include <vector>
#include <iostream>
using namespace std;

int main()
{
	ostream_iterator<int> out_it(cout, " ");
	vector<int> vec{ 0,1,2,3,4,5,6,7,8,9 };

	for (auto a : vec)
	{
		out_it = a;
	}
	cout << endl;

	return 0;
}
```

推荐使用【ostream_iterator】时写上【解引用运算符*】和【递增运算符++】。
保持【ostream_iterator的使用方法】和【其他的迭代器】一致。
方便把【ostream_iterator】修改为【其他的迭代器类型】。
增加【程序可读性】。

使用【copy()】替代【循环】输出【vec的元素】。

```c++
#include <vector>
#include <iostream>
using namespace std;

int main()
{
	ostream_iterator<int> out_it(cout, " ");
	vector<int> vec{ 0,1,2,3,4,5,6,7,8,9 };

	copy(vec.begin(), vec.end(), out_it);
	cout << endl;

	return 0;
}
```

##### 使用流迭代器处理类类型

【istream_iterator】可以读取【支持输入运算符>>的类型】。
【ostream_iterator】可以写入【支持输出运算符<<的类型】。
使用【iostream迭代器】重写【1.6节 P21 书店程序】。

#### 10.4.3 反向迭代器

【反向迭代器】是【递增运算的移动方向和正向迭代器相反的迭代器】。
【反向迭代器】和【正向迭代器】的【递增和递减操作移动方向】相反。
递增【反向迭代器】，【反向迭代器】向【首元素】移动。
递减【反向迭代器】，【反向迭代器】向【尾元素】移动。

【除了forward_list之外其他的容器】都支持【反向迭代器】。
【.rbegin()、.rend()、.crbegin()、.crend()】返回【反向迭代器】。
【.rbegin()和.crbegin()】返回【反向尾迭代器】，【.rend()和.crend()】返回【反向首前迭代器】。
【.crbegin()和.crend()】返回【常量反向迭代器】。

迭代器顺序。

|                | 首元素   |      | 尾元素       |            |
| -------------- | -------- | ---- | ------------ | ---------- |
|                | 首迭代器 |      |              | 尾后迭代器 |
| 反向尾后迭代器 |          |      | 反向首迭代器 |            |

使用【反向迭代器】逆序打印【容器的元素】。

```c++
#include <vector>
#include <iostream>
using namespace std;

int main()
{
	vector<int> vec{ 0,1,2,3,4,5,6,7,8,9 };
	for (auto crit = vec.crbegin(); crit != vec.crend(); crit++)
	{
		cout << *crit << endl;
	}

	return 0;
}
```

向【sort()】传入【反向迭代器】递减排列【反向迭代器指向容器的元素】。

```c++
#include <vector>
#include <algorithm>
#include <iostream>
using namespace std;

int main()
{
	vector<int> vec{ 0,1,2,3,4,5,6,7,8,9 };
	sort(vec.rbegin(), vec.rend());

	for (auto a : vec)
	{
		cout << a << endl;
	}

	return 0;
}
```

##### 反向迭代器需要递减运算符

因为【反向迭代器】反向移动。
所以【反向迭代器对应的正向迭代器】必须支持【递减运算符--】。
【除了forward_list之外其他的容器】都支持【递增运算符++】和【递减运算符--】。
因为不能在【iostream】中反向移动，所以【iostream迭代器】不支持【递减运算符--】。
【forward_list】和【iostream迭代器】不支持【反向迭代器】。

##### 反向迭代器和其他迭代器的关系

使用【string】保存【逗号分隔的单词列表】，想要打印【string的第一个单词】。
使用【find()】和【正向迭代器】，正向搜索【逗号】，正向打印【string的第一个单词】。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	string line{ "Hello,World" };
	auto comma = find(line.cbegin(), line.cend(), ',');
	cout << string(line.cbegin(), comma) << endl; // Hello

	return 0;
}
```

如果【保存单词列表的string】有【逗号】，那么【comma】指向【string的第一个逗号】。
打印到【comma指向的string的第一个逗号之前】。
如果【保存单词列表的string】没有【逗号】，那么【comma】指向【string的尾后位置】。
打印到【整个string】。

使用【find()】和【反向迭代器】，反向搜索【逗号】，反向打印【string的最后一个单词】。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	string line{ "Hello,World" };
	auto comma = find(line.crbegin(), line.crend(), ',');
	cout << string(line.crbegin(), comma) << endl; // dlroW

	return 0;
}
```

因为向【find()】传入【反向迭代器】，所以【find()】从【string的最后一个字符】向前搜索【逗号】。
如果【保存单词列表的string】有【逗号】，那么【comma】指向【string的最后一个逗号】。
如果【保存单词列表的string】没有【逗号】，那么【comma】指向【string的首前位置】。

使用【反向迭代器】会反向打印【string的最后一个单词】。
想要正向打印【string的最后一个单词】。
需要把【反向迭代器】转换为【正向迭代器】。
【reverser_iterator.base()】把【反向迭代器】转化为【正向迭代器】。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	string line{ "Hello,World" };
	auto comma = find(line.crbegin(), line.crend(), ',');
	cout << string(comma.base(), line.cend()) << endl; // World

	return 0;
}
```

为了【一对反向迭代器】和【这对反向迭代器转化的正向迭代器】指向相同的范围。
【一个反向迭代器】和【这个反向迭代器转化的正向迭代器】指向相邻位置，【正向迭代器】指向右面，【反向迭代器】指向左面。

【正向迭代器】和【反向迭代器】互相赋值，不指向相同元素。

```c++
#include <string>
#include <iostream>
using namespace std;

int main()
{
	string line{ "Hello,World" };

	cout << *(line.cbegin() + 1) << endl;								// e
	cout << *string::const_reverse_iterator(line.cbegin() + 1) << endl;	// H

	return 0;
}
```

### 10.5 泛型算法结构

【算法要求传入算法的迭代器必须支持的操作】是【算法最基本的特性】。
【find()】要求【迭代器可以访问元素】，【可以递增迭代器】，【可以比较两个迭代器是否相等】。
【sort()】还要求【迭代器可以读元素】、【迭代器可以写元素】、【迭代器可以随机访问元素】。
【算法要求的迭代器类别】可以分为5种，【表10.5 迭代器类别 P365】。
【算法】说明【传入算法的迭代器类别】。

###### 表10.5 迭代器类别 P365

| 迭代器类型     | 读   | 写   | 扫描 | 递增 | 递减 |
| -------------- | ---- | ---- | ---- | ---- | ---- |
| 输入迭代器     | √    | ×    | 单遍 | √    | ×    |
| 输出迭代器     | √    | ×    | 单遍 | √    | ×    |
| 前向迭代器     | √    | √    | 多遍 | √    | ×    |
| 双向迭代器     | √    | √    | 多遍 | √    | √    |
| 随机访问迭代器 | √    | √    | 多遍 | √    | √    |

【第二种算法分类方式】是【算法是否读、写、重排元素】。

【不同算法】有【相同的参数传递规范和命名规范】。

#### 10.5.1 5类迭代器

【迭代器】有【通用操作】，和【容器】类似。
【一些操作】【所有迭代器】都支持，【一些操作】只有【某些迭代器支持】。
比如，【ostream_iterator】只支持【递增】、【解引用】、【赋值】。
【vector、string、deque迭代器】支持【递增】、【解引用】、【赋值】，还支持【递减】、【关系运算】、【算术运算】。

按照【迭代器支持的操作】分类【迭代器】。
除了【输出迭代器】之外，【高级迭代器】支持【低级迭代器的所有操作】。

【C++标准】要求了【泛型和数值算法的迭代器参数的最小级别】。
【find()】单次扫描【输入序列】，只读取【元素】，至少要求【输入迭代器】。
【replace()】至少要求【前向迭代器】。
【replace_copy()的前两个迭代器参数】至少要求【前向迭代器】，【replace_copy()的第三个参数】表示【输出位置】，至少要求【输出迭代器】。
至少向【算法】传入【和算法要求级别相等的迭代器】。
向【算法】传入【低级别迭代器】会产生错误。

向【算法】传入【低级别迭代器】不会警告。

##### 迭代器类别

| 迭代器类别                 | 输出迭代器           | 输入迭代器       | 前向迭代器           | 双向迭代器   | 随机访问迭代器                                     |
| -------------------------- | -------------------- | ---------------- | -------------------- | ------------ | -------------------------------------------------- |
| 扫描次数                   | 单遍                 | 单遍             | 多遍                 | 多遍         | 多遍                                               |
| 读(在赋值运算符右侧解引用) |                      | √                | √                    | √            | √                                                  |
| 写(在赋值运算符左侧解引用) | √                    |                  | √                    | √            | √                                                  |
| *                          | √                    | √                | √                    | √            | √                                                  |
| ++                         | √                    | √                | √                    | √            | √                                                  |
| ==                         |                      | √                | √                    | √            | √                                                  |
| ->                         |                      | √                | √                    | √            | √                                                  |
| --                         |                      |                  |                      | √            | √                                                  |
| +                          |                      |                  |                      |              | √                                                  |
| -                          |                      |                  |                      |              | √                                                  |
| <                          |                      |                  |                      |              | √                                                  |
| >                          |                      |                  |                      |              | √                                                  |
| []                         |                      |                  |                      |              | √                                                  |
| 要求这种迭代器的算法       | find()、accumulate() | copy()           | replace()            | reverse()    | sort()                                             |
| 这种迭代器的例子           | istream_iterator     | ostream_iterator | forward_list的迭代器 | list的迭代器 | array、string、vector、deque的迭代器、内置数组指针 |

```c++
#include <string>
#include <vector>
#include <list>
#include <forward_list>
#include <iostream>
using namespace std;

struct CTest
{
	int n;
};

int main()
{
	int n = 0;

	// 输出迭代器
	ostream_iterator<int> ostream_iteratorA(cout);
	ostream_iterator<string> ostream_iteratorB(cout);
	// n = *ostream_iteratorA;			       // 读
	*ostream_iteratorA = n;				       // 写
	*ostream_iteratorA;					       // *
	ostream_iteratorA++;				       // ++
	// ostream_iteratorA == ostream_iteratorA; // ==
	// ostream_iteratorB->size();			   // ->
	// ostream_iteratorA--;				       // --
	// ostream_iteratorA + 1;			       // +
	// ostream_iteratorA - 1;			       // -
	// ostream_iteratorA < ostream_iteratorA;  // <
	// ostream_iteratorA > ostream_iteratorA;  // >
	// ostream_iteratorA[0];			       // []

	// 输入迭代器
	istream_iterator<int> istream_iteratorA(cin);
	istream_iterator<string> istream_iteratorB(cin);
	n = *istream_iteratorA;					  // 读
	// *istream_iteratorA = n;				  // 写
	*istream_iteratorA;						  // *
	istream_iteratorA++;					  // ++
	istream_iteratorA == istream_iteratorA;	  // ==
	istream_iteratorB->size();				  // ->
	// istream_iteratorA--;					  // --
	// istream_iteratorA + 1;				  // +
	// istream_iteratorA - 1;				  // -
	// istream_iteratorA < istream_iteratorA; // <
	// istream_iteratorA > istream_iteratorA; // >
	// istream_iteratorA[0];				  // []

	// 前向迭代器
	forward_list<int> forward_listA;
	forward_list<string> forward_listB;
	auto forward_list_iteratorA = forward_listA.end();
	auto forward_list_iteratorB = forward_listB.end();
	n = *forward_list_iteratorA;			            // 读
	*forward_list_iteratorA = n;			            // 写
	*forward_list_iteratorA;					        // *
	forward_list_iteratorA++;				            // ++
	forward_list_iteratorA == forward_list_iteratorA;   // ==
	forward_list_iteratorB->size();			            // ->
	// forward_list_iteratorA--;			            // --
	// forward_list_iteratorA + 1;			            // +
	// forward_list_iteratorA - 1;			            // -
	// forward_list_iteratorA < forward_list_iteratorA; // <
	// forward_list_iteratorA > forward_list_iteratorA; // >
	// forward_list_iteratorA[0];			            // []

	// 双向迭代器
	list<int> listA;
	list<string> listB;
	auto list_iteratorA = listA.end();
	auto list_iteratorB = listB.end();
	n = *list_iteratorA;			    // 读
	*list_iteratorA = n;			    // 写
	*list_iteratorA;				    // *
	list_iteratorA++;				    // ++
	list_iteratorA == list_iteratorA;   // ==
	list_iteratorB->size();			    // ->
	list_iteratorA--;			        // --
	// list_iteratorA + 1;			    // +
	// list_iteratorA - 1;			    // -
	// list_iteratorA < list_iteratorA; // <
	// list_iteratorA > list_iteratorA; // >
	// list_iteratorA[0];			    // []

	// 随机访问迭代器
	vector<int> vectorA;
	vector<string> vectorB;
	auto vector_iteratorA = vectorA.end();
	auto vector_iteratorB = vectorB.end();
	n = *vector_iteratorA;			      // 读
	*vector_iteratorA = n;			      // 写
	*vector_iteratorA;					  // *
	vector_iteratorA++;				      // ++
	vector_iteratorA == vector_iteratorA; // ==
	vector_iteratorB->size();			  // ->
	vector_iteratorA--;			          // --
	vector_iteratorA + 1;			      // +
	vector_iteratorA - 1;			      // -
	vector_iteratorA < vector_iteratorA;  // <
	vector_iteratorA > vector_iteratorA;  // >
	vector_iteratorA[0];			      // []

	return 0;
}
```

#### 10.5.2 算法形参模式

【算法】有【参数规范】。

```c++
算法名(输入范围的首迭代器, 输入范围的尾后迭代器, 其他的参数);
算法名(输入范围的首迭代器, 输入范围的尾后迭代器, 输出位置的迭代器, 其他的参数);
算法名(输入范围的首迭代器, 输入范围的尾后迭代器, 输出范围的首迭代器, 其他的参数);
算法名(输入范围的首迭代器, 输入范围的尾后迭代器, 输出范围的首迭代器, 输出范围的尾后迭代器, 其他的参数);
```

##### 接受单个目标迭代器的算法

【输出位置的迭代器】指向【算法可以写入的输出位置】。
【算法】假设向【输出位置之后的空间】写入【不论多少个元素】都安全。

如果【输出位置的迭代器】是【普通迭代器】，那么【算法】向【容器中已存在的元素】写入【算法结果】。
如果【输出位置的迭代器】是【插入迭代器】，那么【算法】向【容器】插入【作为算法结果的新元素】。
如果【输出位置的迭代器】是【ostream_iterator】，那么【算法】向【输出流】写入【算法结果】。
使用【插入迭代器】和【ostream_iterator】不需要考虑【输出位置之后空间的大小】。

##### 接受第二个输入序列的算法

【primer】认为【函数的所有传入序列参数】都是【输入序列】？？？
【输入序列】指的是什么？？？

【有第二个输入范围的算法】使用【一个迭代器】或【一对迭代器】表示【第二个输入范围】。
【这些算法】结合使用【第一个输入范围的元素】和【第二个输入范围的元素】进行运算。

【使用一对迭代器表示第二个输入范围的算法】有【两个完整的输入范围】。

【使用一个迭代器表示第二个输入范围的算法】有【一个完整的输入范围】和【一个输入范围的开始位置】。
没有传入【第二个输入范围的结束位置】，【这些算法】假设【第二个输入范围】大于等于【第一个输入范围】。

#### 10.5.3 算法命名规范

【算法】有【命名和重载规范】。
【命名和重载规范】说明了【算法】是否自定义【比较操作】替代【默认的小于运算符<或等于运算符==】,【算法】的【输出范围】和【输入范围】是否重叠。

##### 一些算法使用重载形式传递一个谓词

【有谓词参数，使用谓词参数比较元素的算法版本】和【没有谓词参数，使用元素的小于运算符<比较元素的算法版本】是【重载函数】。

```c++
unique(迭代器范围的首迭代器, 迭代器范围的尾后迭代器);
unique(迭代器范围的首迭代器, 迭代器范围的尾后迭代器, 比较元素的二元谓词);
```

【两个版本的unique()】都向前提【迭代器范围的非重复元素】。
因为【两个版本的unique()的参数个数】不等，所以调用【unique()】不会出现【重载歧义】。

##### _if版本的算法

【传入元素的值参数的算法】有【另一个版本的传入一元谓词参数替代元素的值参数的算法】，【两个版本的算法的名称】不同。
【传入一元谓词参数替代元素的值参数的算法的名称】有【_if后缀】。

```c++
find(查找范围的首迭代器, 查找范围的尾后迭代器, 要查找的元素的值);
find_if(查找范围的首迭代器, 查找范围的尾后迭代器, 查找元素的二元谓词);
```

【两个版本的find()】都在【输入范围】查找【特定元素第一次出现的位置】。
【find()】返回【范围内第一个等于元素的值参数的元素的迭代器】。
【find_if()】返回【范围内第一个传入一元谓词返回true的元素的迭代器】。

因为【两个版本的find()的参数个数】相等，所以如果【两个版本的find()的名称】相同，那么调用【find()】可能出现【重载歧义】。
为了避免【重载歧义】，【两个版本的find()的名称】不同，不是【重载函数】。

##### 区别拷贝元素的版本和不拷贝的版本

默认情况，【重排元素的算法】的【输入范围】和【输出范围】相同。
【另一个版本的重排元素的算法】可以传入【输出范围】。
【可以传入输出范围的重排元素的算法的名称】有【_copy后缀】。

```c++
reverser(反转范围的首迭代器, 反转范围的尾后迭代器);
reverser(源容器反转范围的首迭代器, 源容器反转范围的尾后迭代器, 目的容器粘贴范围的首迭代器);
```

【一些算法】有【同时有\_copy和\_if的版本】，【同时有\_copy和\_if的算法版本】有【输出范围的首迭代器参数】和【一元谓词参数】。

```c++
remove_if(删除范围的首迭代器, 删除范围的尾后迭代器, 判断是否删除元素的一元谓词);
remove_copy_if(源容器的迭代器范围的首迭代器, 源容器的迭代器范围的尾后迭代器, 目的容器的覆盖范围的首迭代器, 判断是否拷贝元素的一元谓词);
```

【判断是否拷贝元素的一元谓词】可以是【lambda表达式】。

### 10.6 特定容器算法

【list】和【forward_list】支持【成员函数版本的算法】，和【其他的容器】不同。
比如【.sort()】、【.merge()】、【.remove()】、【.reverse()】、【.unique()】。
因为【通用版sort()】要求【随机访问迭代器】，【list】提供【双向迭代器】，【forward_list】提供【前向迭代器】，所以【list】和【forward_list】不能使用【通用版sort()】。

虽然【链表】可以使用【通用版算法】，但是【通用版算法的性能】不好。
【通用版算法】需要交换【输入范围的元素的值】。
【链表】可以通过改变【节点顺序】实现交换【元素的值】。
【链表的成员函数版本的算法的性能】更好。

###### 表10.6 list和forward_list成员函数版本的算法 P369

```c++
// 剪切【listB的元素】插入【listA】，排序【listA的元素】，【listB】变为空
listA.merge(listB)

// 剪切【listB的元素】插入【listA】，调用【二元谓词】排序【listA的元素】，【listB】变为空
listA.merge(listB, 二元谓词)

// 删除元素
listA.remove(元素的值);

// 删除【传入一元谓词返回true的元素】
listA.remove_if(一元谓词);

// 反转元素
listA.reverse();

// 排序元素
listA.sort();

// 按照二元谓词排序元素
listA.sort(二元谓词);

// 删除【相同元素】
listA.unique();

// 删除【二元谓词认为相同的元素】
listA.unique(二元谓词);
```

##### splice成员

只有【list】和【forward_list】支持【.splice()】。
【.splice()】没有【通用版】。

###### 表10.7 list和forward_list的splice成员函数版本的参数 P370

```c++
// 剪切【listB的所有元素】插入【listA的迭代器位置】，【listB】变为空
// 【listA】和【listB】不能相同
listA.splice(listA迭代器, listB);

// 剪切【forward_listB的所有元素】插入【forward_listA的迭代器位置之后】，【forward_listB】变为空
// 【forward_listA】和【forward_listB】不能相同
forward_listA.splice_after(forward_listA迭代器, forward_listB);

// 剪切【listB的迭代器指向的元素】插入【listA的迭代器位置】
// 【listA】和【listB】可以相同
listA.splice(listA迭代器, listB, listB迭代器);

// 剪切【forward_listB的迭代器指向的元素】插入【forward_listA的迭代器位置之后】
// 【forward_listA】和【forward_listB】可以相同
forward_listA.splice_after(forward_listA迭代器, forward_listB, forward_listB迭代器);

// 剪切【listB迭代器范围的元素】插入【listA的迭代器位置】
// 【listA】和【listB】可以相同，【listA迭代器】不能在【listB迭代器范围】
listA.splice(listA迭代器, listB, listB首迭代器, listB尾后迭代器);

// 剪切【forward_listB迭代器范围的元素】插入【forward_listA的迭代器位置之后】
// 【forward_listA】和【forward_listB】可以相同，【forward_listA迭代器】不能在【forward_listB迭代器范围】
forward_listA.splice_after(forward_listA迭代器, forward_listB, forward_listB首迭代器, forward_listB尾后迭代器);
```

##### 链表特有的操作会改变容器

【链表的成员函数版本的算法】和【通用版算法】类似，但是不完全相同。
【链表的成员函数版本的算法】会改变【底层容器】。
比如，【成员函数版本的.remove()】会删除【指定元素】。
【成员函数版本的.unique()】会删除【重复元素】。

【成员函数版本的.merge()】和【成员函数版本的.splice()】会销毁【输入范围的元素】。
【通用版merge()】拷贝【两个输入范围的元素】到【输出范围】。
在调用【通用版merge()】之后，【两个输入范围的元素】不变。
【成员函数版本的.merge()】剪切【源链表的输入范围的元素】插入【目的链表的输出范围】。
在调用【成员函数版本的merge()】之后，【源链表的两个输入范围的元素】仍然存在，但是【元素】不在【源链表的两个输入范围】，在【目的链表的输出范围】。











## 第11章 关联容器

【map】和【set】是【主要的关联容器】。
【map】保存【键值对】，【键】是【数组下标】，【值】是【数组元素】。
【set】只保存【键】。
使用【set】保存【文本中想要忽略的单词】。
使用【map】保存【字典】，【单词】作为【键】，【单词释义】作为【值】。

【标准库】有【8个关联容器】。
【map】保存【键值对】，【set】只保存【键】。
【multi】表示【键可重复】。
【unordered_】表示【无序容器】，使用【哈希函数】组织元素。

【map和multimap的头文件】是【map】。
【set和multiset的头文件】是【set】。
【unordered_map和unordered_multimap的头文件】是【unordered_map】。
【unordered_set和unordered_multiset的头文件】是【unordered_set】。

```c++
#include <map>           // map和multimap
#include <set>           // set和multiset
#include <unordered_map> // unordered_map和unordered_multimap
#include <unordered_set> // unordered_set和unordered_multiset
```

###### 表11.1 关联容器类型 P374

### 11.1 使用关联容器

【map】保存【键值对】。
【map】是【关联数组】，使用【键】作为【数组下标】获取【值】。

【set】只保存【键】。
使用【set】保存【只想知道是否存在的值】。

##### 使用map

使用【map】统计【每个单词在输入中出现的次数】。

【关联容器】是【模板】。
定义【map变量】必须指定【map的关键字类型】和【值类型】。
在统计单词程序中，【关键字类型】是【string】，【值类型】是【size_t】。
使用【string变量】作为【下标】访问【map】，获得【size_t变量】。

如果【map】没有【这个string变量的下标】，那么【下标运算符】会在【map】插入【一个新元素】，【键】等于【这个string变量】，【值】等于【0】。

使用【范围for】遍历【map】。
【map的元素类型】是【pair】。
【pair】是【模板】，【成员变量】有【first】和【second】。
【map的关键字】是【pair的first】，【值】是【pair的second】。

##### 使用set

使用【set】保存【在统计中想要忽略的单词】。

【set】是【模板】。
定义【set变量】必须指定【set的元素类型】。

使用【列表初始化】【关联容器变量】。

### 11.2 关联容器概述

【关联容器】支持【表9.2 容器操作 P295】。
【关联容器】不支持【顺序容器支持的位置有关的操作】，比如【.push_front()和.push_back()】。
【关联容器】根据【关键字】存储【元素】，【位置】对【关联容器】没有意义。
【关联容器】不支持【有【一个元素的值参数】和【一个元素的个数参数】的【构造函数】或【插入函数】】。

【关联容器】额外支持的【操作】和【类型别名】。
【无序容器】支持【调整哈希性能的操作】。

【关联容器的迭代器】是【双向迭代器】。

#### 11.2.1 定义关联容器

定义【map变量】必须指定【map的关键字类型】和【值类型】。
定义【set变量】必须指定【set的关键字类型】。
使用【容器的默认构造函数】定义【空容器变量】。
使用【容器的拷贝构造函数】定义【容器变量】，【定义的容器变量】是【传入的容器的拷贝】。
使用【容器的有参构造函数】传入【容器范围内元素】定义【容器变量】，【两个容器】的【容器类型】可以不相同，【元素类型】可以不相同。
使用【容器的有参构造函数】传入【初始化列表】定义【容器变量】。
【map的初始化列表】使用【花括号】包围【键值对】。

##### 初始化multimap或multiset

【map和set的关键字】唯一。
【multimap或multiset】可以有【相同的关键字】。
在【统计单词次数的map】中，【一个单词】对应【一个次数】。
在【一个词典】中，【一个单词】对应【多个词义】。

【一个vector\<int\>】有【重复元素】。
使用【这个vector】初始化【一个set】和【一个multiset】。

【set的元素的个数】小于【vector】。
【multiset的元素的个数】和【vector】相同。

#### 11.2.2 关键字类型的要求

【关联容器】对【关键字类型】有要求。
【有序容器的关键字类型】必须定义【比较的方式】。
默认情况，【标准库】使用【关键字类型的<运算符】比较【两个关键字】。

##### 有序容器的关键字类型

可以自定义【关键字比较操作】替代【关键字类型的<运算符】。
【这个比较方式】必须是【严格弱序】。

严格弱序的性质。
【两个关键字】不能互相小于等于【对方】。
【小于等于关系】可以传递。
【两个互相不小于等于的关键字】等价。
【等价关系】可以传递。

【容器】认为【等价关键字】相等。
【map的等价关键字】只能关联【一个值】，可以使用【任意一个等价关键字】访问【值】。

##### 使用关键字类型的比较函数

【自定义关键字比较操作的类型】是【容器类型】的一部分。
定义【使用自定义关键字比较操作的关联容器变量】必须指定【自定义关键字比较方式的类型】。
在尖括号中，元素类型之后，写【自定义关键字比较操作的类型】。

使用【有参构造函数】定义【容器变量】，传入【自定义关键字比较操作的类型】（和尖括号中类型相同）。

```c++
集合容器类型<键类型, decltype(自定义关键字操作的函数)*> 集合容器变量(自定义关键字比较操作的函数);
```

#### 11.2.3 pair类型

【pair的头文件】是【utility】。

```c++
#include <utility>
```

【pair】有【两个成员变量】。
【pair】是【模板】。
定义【pair变量】必须指定【两个成员变量类型】。
【pair的默认构造函数值】值初始化【成员变量】。

可以使用【初始化器】。

【pair的两个成员变量】是共有的。
调用【.成员访问符】访问【pair的两个成员变量】。

###### 表11.2 pair上的操作 P380

```c++
pair<first的类型, second的类型> pair变量;

pair<first的类型, second的类型> pair变量(first的初始值, second的初始值);

pair<first的类型, second的类型> pair变量 = { first的初始值, second的初始值 };

make_pair(first的初始值, second的初始值);

p.first

p.second

p1 < <= == != >= > p2
```

可以使用【列表初始化】【pair类型返回值】。

可以使用【make_pair()】获得【pair变量】。

```c++
return { first的值, second的值 };       // 列表初始化
return pair<first的类型, second的类型>; // 隐式构造返回值
return make_pair(first的值, second的值);
```

### 11.3 关联容器操作

###### 表11.3 关联容器额外的类型别名 P381

【set的key_type】和【value_type】相同。
【set】保存【关键字】。
【map】保存【关键字】和【值】，【元素】是【pair类型】。
【map的关键字】是const的。

【value_type】是【整体类型】。
【key_type】是【键类型】。
【mapped_type】是【值类型】。

使用关联容器的类型别名。

```c++
容器类型<元素类型>::类型别名
```

只有【map】有【mapped_type】。

#### 11.3.1 关联容器迭代器

解引用【关联容器迭代器】可以获取【容器的value_type类型的引用】。
【map的mapped_type】是【pair】，【pair的first】是【const的关键字】，【second】是【值】。

##### set的迭代器是const的

【set的iterator】和【const_iterator】都只能读取【元素】。
【set的关键字】是const的。

##### 遍历关联容器

【map】和【set】都支持【.begin()和.end()】。
调用【.begin()和.end()】获取【迭代器】，使用【迭代器】遍历容器。

使用【迭代器】遍历【关联容器】。

##### 关联容器和算法

不使用【泛型算法】操作【关联容器】。
因为【算法】需要向【容器】写入【元素】，【关联容器的关键字】是const，所以不使用。

虽然可以使用【只读取元素的泛型算法】操作【关联容器】，但是不推荐。
因为【这些算法】需要搜索【元素】，【关联容器】不能使用【关键字】快速查找【元素】，所以不推荐。
使用【关联容器的成员函数.find()】传入【关键字】直接获取【值】。
【泛型算法的find()】查找【元素】会进行顺序搜索。
【成员函数】比【泛型算法】快。

实际编程，【关联容器】可以是【算法的输入范围或输出范围】。
使用【copy()】从【一个关联容器】拷贝【元素】到【输出范围】。
使用【inserter()获取关联容器的插入迭代器】作为【算法的输出位置】。

#### 11.3.2 添加元素

使用【关联容器的成员函数.insert()】向【容器】添加【元素】。
【非multi类型关联容器的关键字】唯一，插入【一个已存在元素】不影响容器。

【insert】可以传入【一对迭代器】或者【一个列表初始化的参数列表】。
向【容器】插入【相同关键字】，只有第一次成功。

##### 向map添加元素

可以在【map的.insert()的参数列表】定义【pair变量】。

```c++
map变量.insert({ 关键字, 值 });
map变量.insert(make_pair(关键字, 值));
map变量.insert(pair<关键字类型, 值类型>(关键字, 值));
map变量.insert(map<关键字类型, 值类型>::value_type(关键字, 值));
```

在【map的.insert()的参数列表】定义【pair变量最简单的方法】是【列表初始化】。
也可以使用其他方法。

###### 表11.4 关联容器insert操作 P384

```c++
// iterator指向【这个关键字所在元素】的迭代器，【这个关键字所在元素】是否是本次插入
std::pair<std::关联容器类型<value_type>::iterator, bool> 关联容器变量.insert(value_type类型的值)

关联容器变量.insert(插入value_type类型的值的范围的首迭代器, 插入value_type类型的值的范围的尾后迭代器)

std::pair<std::关联容器类型<value_type>::iterator, bool> 关联容器变量.insert({ 初始化列表 })

std::关联容器类型<value_type>::iterator 关联容器变量.insert(开始搜索插入位置迭代器, value_type类型的值)

std::pair<std::关联容器类型<value_type>::iterator, bool> 关联容器变量.emplace(value_type类型的值)

// 关联容器变量.emplace(开始搜索插入位置迭代器, value_type类型的值) // 报错？？？
```

```c++
#include <map>
#include <vector>
#include <utility>
#include <iostream>
using namespace std;

int main()
{
	map<int, int> mapA;
	multimap<int, int> multimapA;
	pair<int, int> pairA{ 0,1 };
	vector<pair<int, int>> vectorA{ {2,3} };

	// iterator指向【这个关键字所在元素】的迭代器，【这个关键字所在元素】是否是本次插入
	std::pair<std::map<int, int>::iterator, bool> aA = mapA.insert(pairA);
	if (aA.second)
	{
		cout << "aA " << aA.first->first << " " << aA.first->second << endl;
	}

	mapA.insert(vectorA.begin(), vectorA.end());

	// iterator指向【这个关键字所在元素】的迭代器，【这个关键字所在元素】是否是本次插入
	std::pair<std::map<int, int>::iterator, bool> aB = mapA.insert({ 4, 5 });
	if (aB.second)
	{
		cout << "aB " << aB.first->first << " " << aB.first->second << endl;
	}

	std::map<int, int>::iterator aC = mapA.insert(mapA.begin(), { 6, 7 });
	cout << "aC " << aC->first << " " << aC->second << endl;

	// iterator指向【这个关键字所在元素】的迭代器，【这个关键字所在元素】是否是本次插入
	std::pair<std::map<int, int>::iterator, bool> aD = mapA.emplace(8, 9);
	if (aD.second)
	{
		cout << "aD " << aD.first->first << " " << aD.first->second << endl;
	}

	// mapA.emplace(mapA.begin(), { 10, 11 }); 报错？？？
	// mapA.emplace(mapA.begin(), 10, 11);	   报错？？？

	for (auto aE : mapA)
	{
		cout << aE.first << " " << aE.second << endl;
	}
	cout << "******************************" << endl;

	std::multimap<int, int>::iterator aF = multimapA.insert(pairA);
	cout << "aF " << aF->first << " " << aF->second << endl;

	multimapA.insert(vectorA.begin(), vectorA.end());

	std::multimap<int, int>::iterator aG = multimapA.insert({ 4, 5 });
	cout << "aG " << aG->first << " " << aG->second << endl;

	std::multimap<int, int>::iterator aH = multimapA.insert(multimapA.begin(), { 6, 7 });
	cout << "aH " << aH->first << " " << aH->second << endl;

	std::multimap<int, int>::iterator aI = multimapA.emplace(8, 9);
	cout << "aI " << aI->first << " " << aI->second << endl;

	// multimapA.emplace(multimapA.begin(), { 10, 11 }); // 报错？？？
	// multimapA.emplace(multimapA.begin(), 10, 11);     // 不报错？？？

	for (auto aK : multimapA)
	{
		cout << aK.first << " " << aK.second << endl;
	}

	return 0;
}
```

##### 检测insert的返回值

【【.insert()】和【.emplace()】的返回值】和【【容器类型】和【参数类型】】有关。
非multi类型关联容器的返回值。

调用【.insert()】实现【单词计数】。

##### 展开递增语句

解释【单词计数程序的递增语句】。

##### 向multiset和multimap添加元素

使用【multimap】保存【作者】和【作品】。

multi类型关联容器的返回值。】

#### 11.3.3 删除元素

【关联容器】有【三个版本的.erase()】。
.erase()的参数。
.erase()的功能。

【关联容器】有【额外的.erase()】。
额外.erase()的参数。
额外.erase()的功能，额外.erase()的返回值。

【额外.erase()】删除【非multi类型关联容器】，【返回值】是【0或1】，删除【multi类型关联容器】，【返回值】是【自然数】。

###### 表11.5 从关联容器删除元素 P387

```c++
size_type删除的元素的个数 关联容器变量.erase(关键字)

删除位置之后的迭代器 关联容器变量.erase(删除位置的迭代器);

删除位置之后的迭代器 关联容器变量.erase(删除范围的首迭代器, 删除范围的尾后迭代器);
```

```c++
#include <map>
#include <utility>
#include <iostream>
using namespace std;

int main()
{
	map<int, int> mapA;
	multimap<int, int> multimapA;

	for (int i = 0; i < 10; i = i + 2)
	{
		mapA.insert({ i, i + 1 });
	}

	size_t aA = mapA.erase(0);
	cout << "aA " << aA << endl;

	std::map<int, int>::iterator aB = mapA.erase(mapA.begin());
	cout << "aB " << aB->first << " " << aB->second << endl;

	std::map<int, int>::iterator aC = mapA.erase(mapA.begin(), --mapA.end());
	cout << "aC " << aC->first << " " << aC->second << endl;

	cout << "******************************" << endl;
	for (int i = 0; i < 10; i = i + 2)
	{
		multimapA.insert({ i, i + 1 });
		multimapA.insert({ i, i + 1 });
	}

	size_t aD = multimapA.erase(0);
	cout << "aD " << aD << endl;

	std::multimap<int, int>::iterator aE = multimapA.erase(multimapA.begin());
	cout << "aE " << aE->first << " " << aE->second << endl;
	
	std::multimap<int, int>::iterator aF = multimapA.erase(multimapA.begin(), --multimapA.end());
	cout << "aF " << aF->first << " " << aF->second << endl;

	return 0;
}
```

#### 11.3.4 map的下标操作

【map】和【unordered_map】有【[]运算符】和【成员函数.at()】。
【关键字】是【数组下标】，【值】是【数组元素】。
因为【set】没有【值】，所以【set】不支持【[]运算符】和【成员函数.at()】。
因为【【multimap】和【unordered_multimap】的一个关键字】对应【多个值】，所以【multimap】和【unordered_multimap】不支持【[]运算符】和【成员函数.at()】。

【map的[]运算符】传入【一个关键字】获取【一个值】。
如果【容器】中没有【这个关键字】，那么向【容器】插入【键值对】，先值初始化【值】，再赋值【值】。

[]运算符的操作流程。

因为【[]运算符】可能插入【元素】，所以只有【非const的map】可以使用【[]运算符】。

###### 表11.6 map和unordered_map的下标操作 P387

```c++
// 访问/插入 值
value_type类型值 map或unordered_map变量[关键字];

value_type类型引用值 map或unordered_map变量[关键字];

// 访问值
value_type类型值 map或unordered_map变量.at(关键字);

value_type类型引用值 map或unordered_map变量.at(关键字);
```

```c++
#include <map>
#include <iostream>
using namespace std;

int main()
{
	map<int, int> mapA;
	multimap<int, int> multimapA;

	for (int i = 0; i < 10; i = i + 2)
	{
		mapA.insert({ i, i + 1 });
	}

	int aA = mapA[0];
	aA = 11111111;
	cout << "mapA[0] " << mapA[0] << endl; // 1

	int& aB = mapA[0];
	aB = 22222222;
	cout << "mapA[0] " << mapA[0] << endl; // 22222222

	int aC = mapA.at(0);
	aC = 33333333;
	cout << "mapA[0] " << mapA[0] << endl; // 22222222

	int& aD = mapA.at(0);
	aD = 44444444;
	cout << "mapA[0] " << mapA[0] << endl; // 44444444

	return 0;
}
```

##### 使用下标操作的返回值

【map的[]运算符】返回【mapped_type类型的值】。
【map的迭代器的*运算符】返回【value_type类型的值】。

【map的[]运算符】返回【左值】。
【左值】可以读写。

如果只想要知道在【map】中是否有【某个元素】，不想要添加【元素】，那么不能使用【[]运算符】。

#### 11.3.5 访问元素

调用【.find()】和【.count()】查找【非multi类型关联容器的效果】相同。
调用【.count()】查找【multi类型关联容器】会统计【关键字出现次数】。

【.find()】返回【迭代器】。
【.count()】返回【关键字出现次数】。

###### 表11.7 在一个关联容器中查找元素的操作 P388

```c++
// 成功返回关键字所在元素迭代器，失败返回尾后迭代器
迭代器 关联容器变量.find(关键字)

关键字个数 关联容器变量.count(关键字)

指向第一个关键字所在元素的迭代器 有序关联容器变量.lower_bound(关键字)

指向最后一个关键字所在元素之后元素的迭代器 有序关联容器变量.upper_bound(关键字)

pair<指向第一个关键字所在元素的迭代器, 指向最后一个关键字所在元素之后元素的迭代器> 有序关联容器变量.equal_bound(关键字)
```

##### 对map使用find代替下标操作

【map】和【undered_map】的【[]运算符】会插入【元素】。
这种操作可能符合预期，可能产生错误。

只想要在【map】中查找【元素】，不想要插入【元素】，应该调用【.find()】。

##### 在multimap或multiset中查找元素

在【multimap】和【multiset】中【有相同关键字的元素】会相邻存储。

想要打印在【multimap】或【multiset】中存储的【一个作者的所有作品】。

调用【.find()】查找【这个作者的第一个作品】。
调用【.count()】获取【这个作者的作品个数】。

从【这个作者的第一个作品】开始循环打印。

##### 一种不同的，面向迭代器的解决办法

调用【.lower_bound()和.upper_bound()】获取【multimap】或【multiset】中【有相同关键字的迭代器范围的迭代器范围】。
.lower_bound()和.upper_bound()的参数，.lower_bound()和upper_bound()的返回值。

向【.upper_bound()】传入【容器中最大的关键字】，【.upper_bound()】返回【尾后迭代器】。
向【.upper_bound()】传入【比容器中最大的关键字更大的关键字】，【.upper_bound()】返回【尾后迭代器】。

【.lower_bound()】如果成功返回【第一个关键字的元素的迭代器】，如果失败返回【关键字的第一个安全插入点】。

调用【.lower_bound()和.upper_bound()】打印在【multimap】或【multiset】中存储的【一个作者的所有作品】。

##### euqal_range函数

调用【.equal_range()】打印在【multimap】或【multiset】中存储的【一个作者的所有作品】。
.equal_range()的参数，.equal_range()返回值。

#### 11.3.6 一个单词转换的map

一个文件保存宏，一个文件保存想要宏替换的文本。
写程序实现宏替换。

##### 单词转换程序

程序有三个函数。

```c++
// 管理整个过程，调用buildMap和transform
void word_transform(ifstream &宏文件, ifstream &想要宏替换的文本文件)
{
}

// 读取宏文件，创建map，保存宏替换规则
map<string, string>宏替换规则 buildMap(ifstream &宏文件)
{
}

// 如果需要替换就返回替换结果，如果不需要替换就返回原单词
string& transform(string& 单词, map<string, string>& 宏替换规则)
{
}
```

### 11.4 无序容器

【新标准的无序关联容器】使用【哈希函数】和【关键字类型的==运算符】组织元素。
如果【关键字】没有明显顺序或维护顺序的代价高，那么选择【无序容器】。

##### 使用无序容器

【无序容器】支持【有序容器的操作】，比如【.find()】、【.insert()】。
【unordered_map】和【unordered_set】兼容【【map】和【set】的操作】。
【无序容器】有【multi版】。

【有序容器】和【无序容器】可以互相替换。
【无序容器的元素】不按照顺序排列，遍历【无序容器】和【有序容器】的输出结果不同。

使用【unordered_map】实现【单词计数程序】。

##### 管理桶

【无序容器】使用【桶】存储【元素】，【每个桶】保存【零个或多个元素】。
【无序容器的元素】调用【哈希算法】获取【哈希值】，获取【存储自己的桶】。
【相同哈希值的元素】存储在【同一个桶】。
【multi类型无序容器的相同关键字的元素】存储在【同一个桶】。
【哈希函数的质量、桶个数、桶大小】影响【无序容器的质量】。

【相同值】调用【哈希函数】总是产生【相同结果】。
理想情况，【一个桶】存储【一个元素】。
【一个桶】也可以存储【多个关键字不同的元素】。

使用【顺序查找】保存【多个元素的桶】。
通常情况，【计算哈希值】和【在桶中顺序搜索】的速度很快。
但是如果【桶大小】很大，【在桶中顺序搜索速度】就慢。

###### 表11.8 无序容器管理操作 P395

```c++
size_t正在使用的桶个数 无序容器变量.bucket_count()
size_t最大扩容后能使用的桶最大个数 无序容器变量.max_bucket_count()
size_t桶的元素的个数 无序容器变量.bucket_size(0)
size_t关键字所在桶 无序容器变量.bucket(0)
float每个桶的平均元素的个数 无序容器变量.load_factor()
float容器想要的每个桶的平均元素的个数 无序容器变量.max_load_factor()

无序容器变量.rehash(桶个数);
无序容器变量.reserve(保存元素的个数);

local_iterator       // 无序容器迭代器类型
const_local_iterator // 无序容器常量迭代器类型

local_iterator首元素迭代器 无序容器变量.begin()
local_iterator尾后迭代器 无序容器变量.end()
const_local_iterator常量首元素迭代器 无序容器变量.cbegin()
const_local_iterator常量尾后迭代器 无序容器变量.cend()
```

```c++
#include <unordered_map>
#include <iostream>
using namespace std;

int main()
{
	unordered_map<int, int> unordered_mapA;

	for (size_t i = 0; i < 10; i = i + 2)
	{
		unordered_mapA.insert({ i, i + 1 });
	}

	size_t aA = unordered_mapA.bucket_count();	   // 8，正在使用的桶个数
	size_t aB = unordered_mapA.max_bucket_count(); // 536870911，最大扩容后能使用的桶最大个数
	size_t aC = unordered_mapA.bucket_size(0);	   // 0，桶的元素的个数
	size_t aD = unordered_mapA.bucket(0);		   // 5，关键字所在桶
	float aE = unordered_mapA.load_factor();	   // 0.625，每个桶的平均元素的个数
	float aF = unordered_mapA.max_load_factor();   // 1，容器想要的每个桶的平均元素的个数

	unordered_mapA.rehash(10);	// 设置桶个数
	unordered_mapA.reserve(10);	// 设置容器可以保存元素的个数，并且不需要rehash？？？

	cout << "aA " << aA << endl;
	cout << "aB " << aB << endl;
	cout << "aC " << aC << endl;
	cout << "aD " << aD << endl;
	cout << "aE " << aE << endl;
	cout << "aF " << aF << endl;

	return 0;
}
```

##### 无序容器对关键词类型的要求

默认情况，【无序容器】使用【关键字类型的==运算符】比较【元素】。
使用【hash\<key_type\>】生成【元素的哈希值】。
因为【内置类型、string、智能指针类型】有【hash模板】，所以可以直接定义【关键字是【内置类型、string、智能指针类型】的无序容器变量】。

不能直接定义【关键字】是【自定义类型的无序容器变量】。
必须提供【自定义的hash模板】。

【自定义hash模板】和【自定义关键字】的比较方式类似。
定义【函数】替代【==运算符】和【hash计算函数】。

定义【unordered_multiset】传入【自定义函数】。

```c++
unordered_multiset<关键字类型, decltype(哈希函数名)*, decltype(比较函数名)*> unordered_multiset变量(桶大小, 哈希函数名, 比较函数名);
```

如果【自定义的关键字类型】重载了【==运算符】，那么可以只自定义【哈希函数】。

## 第12章 动态内存

【局部static变量】、【类static成员变量】、【全局变量（定义在任何函数之外）】保存在【全局内存】。
【局部变量（定义在函数之内）】保存在【栈内存】。
【编译器】自动创建和销毁【全局内存】和【栈内存】的变量。
【栈内存的变量】在【定义栈内存变量的程序块】运行时存在。
【static变量】在使用之前分配，在程序结束时销毁。

【程序】有【内存池】，称作【堆】。
【程序】使用【堆】存储【动态分配的变量】。
【程序自己的代码】必须显式销毁【动态变量】。

### 12.1 动态内存与智能指针

在C++使用【【new】和【delete】运算符】。
使用【new】传入【变量类型】和【变量个数】，在【动态内存】中申请【动态变量】，返回【指向动态变量的指针】。
使用【delete】传入【指向动态变量的指针】，在【动态内存】中销毁【动态变量】，释放【动态变量的内存】。

正确释放内存很难。
没释放内存产生【内存泄漏】。
释放【有指针指向的内存】产生【指向非法内存的指针】。

使用【标准库提供的两种智能指针】管理【动态变量】更容易、更安全。
【智能指针】和【常规指针】的行为类似，区别是【智能指针】自动释放【指针指向的动态变量】。
使用【shared_ptr】，【一个对象】可以被【多个指针】指向。
使用【unique_ptr】，【一个对象】只能被【一个指针】指向。
【weak_ptr】是【弱引用】，【weak_ptr】只能指向【shared_ptr指向的动态变量】。
【智能指针的头文件】是【memory】。

```c++
#include <memory>
```

#### 12.1.1 shared_ptr类

【智能指针】是【模板】。
定义【智能指针变量】必须指定【指针指向的类型】。

```c++
shared_ptr<shared_ptr变量指向类型> shared_ptr变量;
```

使用【默认构造函数】定义【空智能指针变量】。
【智能指针】和【普通指针】的使用方式类似。
使用【*】解引用【智能指针】，返回【智能指针指向的变量】。
【空智能指针变量】==0，可以直接判断【空智能指针变量】是否为空。

###### 表12.1 shared_ptr和unique_ptr都支持的操作

```c++
shared_ptr<shared_ptr变量指向类型> shared_ptr变量;
unique_ptr<unique_ptr变量指向类型> unique_ptr变量;
p
智能指针变量指向的动态变量 *p
智能指针变量指向的动态变量的成员 p->智能指针变量指向的动态变量的成员
智能指针变量指向的动态变量的地址 p.get()

// 交换p1 p2
swap(p1, p2);

// 交换p1 p2
p1.swap(p2);
```

###### 表12.2 shared_ptr独有的操作

```c++
shared_ptr变量 make_shared<shared_ptr变量指向类型>(shared_ptr变量指向类型的构造函数的参数)

// sp2指向sp1指向的动态变量，sp1引用计数增加，【sp1指针】必须可以转化成【sp2指向类型的指针】
shared_ptr<shared_ptr变量指向类型>sp2(sp1)

// sp2指向sp1指向的动态变量，sp1引用计数增加，sp2引用计数减少，如果sp2之前指向的动态变量的引用计数减为0就释放
sp2 = sp1

是否use_count()==1 sp.unique()
sp指向的动态对象的引用计数 sp.use_count()
```

##### make_shared函数

通过【标准库函数make_shared()】，分配和使用【动态内存】，最安全。
使用【make_shared()】在【动态内存】中分配【变量空间】，初始化【变量】，返回【指向这个动态变量的shared_ptr】。
【make_shared()的头文件】是【memory】。

```c++
#include <memory>
```

使用【make_shared()】必须指定【shared_ptr指向类型】。
【【make_shared()】和【顺序容器的成员函数.emplace()】的行为】类似。
【make_shared()】使用【传入的参数】构造【shared_ptr指向的动态变量】。
【传入的参数】必须匹配【shared_ptr指向类型的构造函数】。
如果不传入参数，那么值初始化【shared_ptr指向的动态变量】。
使用【auto】定义【变量】接收【make_shared()的返回值】。

##### shared_ptr的拷贝和赋值

【每个shared_ptr】记录【shared_ptr指向的动态变量】被多少个【shared_ptr】指向。
可以认为【每个shared_ptr】有【计数器（引用计数）】。
拷贝【shared_ptr】，【shared_ptr的引用计数】会递增。
使用【shared_ptr】初始化【shared_ptr】，向【函数】传入【shared_ptr】，从【函数】返回【shared_ptr】，【shared_ptr的引用计数】会递增。
赋值【shared_ptr】，【shared_ptr】结束生命期，【shared_ptr的引用计数】会递减。

如果【shared_ptr的引用计数】减为0，那么【shared_ptr】会释放【shared_ptr指向的动态变量】。
【shared_ptr的引用计数的实现】没有强制规定。

##### shared_ptr自动销毁所管理的对象

销毁【指向的动态变量的最后一个shared_ptr】，【shared_ptr】会销毁【shared_ptr指向的动态变量】。
【shared_ptr】使用【析构函数】销毁【shared_ptr指向的动态变量】。
析构函数。

使用【析构函数】释放【变量的资源】。
比如string。
比如vector。

【shared_ptr的析构函数】减少【shared_ptr指向的动态变量的引用计数】。
如果【shared_ptr的引用计数】减为0，那么【shared_ptr的析构函数】会销毁【shared_ptr指向的动态变量】，释放【shared_ptr指向的动态变量占用的内存】。

##### shared_ptr还会自动释放相关联的内存

【shared_ptr】会自动释放【不使用的动态变量】。
【函数】返回【shared_ptr】，【shared_ptr】指向【动态变量】。

销毁【指向的动态变量的最后一个shared_ptr】，【shared_ptr】会释放【shared_ptr指向的动态变量占用的内存】。
需要销毁【不使用的shared_ptr】。
比如【在容器中不使用的shared_ptr】。

##### 使用了动态生存期的资源的类

因为程序不知道使用对象的数量（容器）、类型（在15章有例子），程序需要在对象之间共享资源（现在写例子），所以程序使用动态内存。

【目前出现的类对象】和【它的资源】的【生存期】相同。
比如【vector】。

【目前出现的类对象】和【它的资源】的【生存期】不同。
想要定义【Blob类】保存【元素】。
【一个Blob对象】和【这个Blob对象的拷贝】共享【元素】。

销毁【有共享资源的类对象】时不会直接释放【共享的资源】。

##### 定义StrBlob类

定义【StrBlob类】管理【string】。

使用【标准库的容器类型vector】管理【元素的内存】。

不能直接在【StrBlob类】中保存【想要共享的vector变量】。
在【动态内存】中保存【想要共享的vector变量】。

在【StrBlob类】中保存【shared_ptr成员变量】,【shared_ptr成员变量】指向【共享的vector变量】。
【shared_ptr成员变量】记录有多少个【shared_ptr成员变量】指向【共享的vector变量】，销毁【最后一个指向【共享的vector变量】的shared_ptr成员变量】时释放【共享的vector变量】。

【StrBlob类】的【成员函数】和【成员变量】。

```c++
class StrBlob
{
private:
	std::shared_ptr<std::vector<std::string>> data;
};
```

##### StrBlob构造函数

StrBlob类的默认构造函数和有参构造函数。

##### 元素访问成员函数

【访问vector元素的成员函数】在访问【vector元素】之前必须检查【将要访问的vector元素】是否存在。
定义【私有成员函数】检查【传入下标】是否存在。
【检查传入下标的私有成员函数】传入【描述错误的string】，在【传入下标】错误时抛出【异常】传入【描述错误的string】。

【访问vector元素的成员函数】先调用【检查传入下标的私有成员函数】，再调用【底层vector的成员函数】。

const版的成员函数。

##### StrBlob的拷贝、赋值和销毁

【StrBlob类】使用默认的拷贝、赋值、析构函数。
拷贝【StrBlob变量】会拷贝【StrBlob变量的shared_ptr成员变量】。

拷贝【shared_ptr】的特性。

#### 12.1.2 直接管理内存

【C++】使用【new和delete运算符】申请和释放【动态内存】。

【使用new和delete运算符直接管理动态内存的类】没有【默认的拷贝、构造、析构函数】，【使用智能指针的类】有。
推荐使用【智能指针】。

##### 使用new动态分配和初始化对象

【使用new申请的动态变量】没有名字，【new】直接返回【指向动态变量的指针】。

默认情况，【默认构造】【类类型动态变量】，【非类类型动态变量的值】未定义。

可以【直接初始化】【动态变量】。分为【直接初始化】和【拷贝初始化】，【直接初始化】没有等号，【拷贝初始化】有等号。
可以使用【传统的圆括号形式】、【列表初始化的花括号形式】、【值初始化的空圆括号形式】【直接初始化】【动态变量】。

【有默认构造函数的类】不会【值初始化】，只会【默认构造】。
值初始化【内置类型】，【内置类型的值】固定。
默认初始化【内置类型】，【内置类型的值】未定义。
默认构造【有内置类型成员变量的类对象】，如果【这个类的默认构造函数】没有初始化【这个类的内置类型成员变量】，那么【这个类的内置类型成员变量】未定义。

【new】可以使用【auto】。

```c++
auto 指向新类对象指针 = new auto{使用【传入的这个类对象】初始化【新类对象】};
```

##### 动态分配的const对象

【new】可以使用【const】。

必须初始化【const动态变量】。
可以隐式初始化【有默认构造函数的const动态类对象】，其他的类型必须显式初始化。
【const指针】指向【const动态变量】。

##### 内存耗尽

如果程序内存耗尽，那么【new】会失败。
默认情况，【new】失败会抛出【bad_alloc异常】。
可以使用【定位new】，【定位new】不抛出异常。

```c++
int *pn = new (nothrow) int;
```

【定位new】可以传入参数。
【定位new】传入【nothrow】，不抛出异常。
如果【传入nothrow的定位new】失败，返回空指针。
【【bad_alloc】和【nothrow】的头文件】是【new】。

##### 释放动态内存

使用【delete】传入【指向【想要释放的动态变量】的指针】，销毁【想要释放的动态变量】，释放【想要释放的动态变量所在的动态内存】。

##### 指针值和delete

【传入delete的指针】必须指向【动态内存】或者是【空指针】。
【传入delete的指针】没有指向【动态内存】或者多次传入【delete】释放【动态内存】，行为未定义。

如果【delete】传入【非指针类型】，那么编译器会报错。
如果【传入delete的指针】没有指向【动态内存】或者多次传入【delete】释放【动态内存】，那么编译器不会报错。

不能修改const变量，可以销毁const变量。

##### 动态对象的生存期直到被释放时为止

【shared_ptr】自动释放【shared_ptr指向的动态内存】。
【内置指针】不自动释放【内置指针指向的动态内存】。

一些函数返回【指向动态内存的内置指针】，必须手动释放【内置指针指向的动态内存】。

如果【指向动态内存的最后一个内置指针】销毁了，就无法释放【内置指针指向的动态内存】了。

一些函数使用【内置指针】申请【动态内存】，可以在函数结尾释放【内置指针指向的动态内存】。

一些函数使用【内置指针】申请【动态内存】，可以在函数结尾返回【指向动态内存的内置指针】。

##### 小心：动态内存的管理非常容易出错

使用【new和delete】管理【动态内存】产生的问题。

忘记delete【动态内存】，产生【内存泄漏】问题。
程序耗尽【动态内存】时，表现出问题。

使用【delete之后的动态内存】产生问题。

多次delete【动态内存】产生问题。

使用【智能指针】避免问题。

##### delete之后重置指针值

delete【动态内存】之后，【指向动态内存的内置指针】仍然指向【动态内存】，是【空悬指针】。

【未初始化指针】和【空悬指针】的缺点相同。
在【内置指针】离开作用域时释放【内置指针指向的动态内存】。
在delete【动态内存】之后，【指向动态内存的内置指针】=nullptr。

##### 这只是提供了有限的保护

可能有多个【内置指针】指向【相同的动态内存】，delete【这块动态内存】之后，【一个指向这块动态内存的内置指针】=nullptr，【其他的指向这块动态内存的内置指针】还指向【这块动态内存】。

找到【所有指向相同动态内存的内置指针】很困难。

#### 12.1.3 shared_ptr和new结合使用

智能指针默认初始化为空指针。
可以使用【new的返回值】初始化【智能指针】。

因为【传入内置指针参数的智能指针有参构造函数】是explicit（构造函数不能使用等号方式，只能使用括号方式）的。
所以【内置指针】不能隐式转换为【智能指针】（等号方式），只能使用【直接初始化】（括号方式）。

【返回智能指针的函数】不能直接return【new的返回值】。
【返回智能指针的函数】可以return【使用new的返回值初始化的shared_ptr】。

【初始化智能指针的内置指针】必须指向【动态内存】，默认情况【智能指针】使用delete释放【智能指针指向的动态变量】。
如果使用【自定义释放操作】替代【delete】，那么【智能指针】可以指向【其他的类型】。

###### 表 12.3 定义和改变shared_ptr的其他方法

```c++
shared_ptr<shared_ptr变量指向类型> shared_ptr变量(指向【动态内存】并且能转换为【shared_ptr变量指向类型指针】的内置指针);

shared_ptr<shared_ptr变量指向类型> shared_ptr变量(指向【动态内存】并且能转换为【shared_ptr变量指向类型指针】的内置指针, 替代delete的可调用对象);

shared_ptr<shared_ptr变量指向类型> shared_ptr变量(指向动态内存的shared_ptr参数, 替代delete的可调用对象);

// shared_ptr变量指向unique_ptr参数指向的动态内存，unique_ptr参数置空
shared_ptr<shared_ptr变量指向类型> shared_ptr变量(指向shared_ptr变量指向类型的unique_ptr);

// 如果【sp指向的动态变量】只被【sp】指向，那么会释放【这个动态变量】
// 如果传入【内置指针】，那么【sp】会指向【这个内置指针】
// 如果传入【可调用对象】，那么释放【内置指针】会使用【这个可调用对象】
sp.reset();
sp.reset(内置指针);
sp.reset(内置指针, 替换delete的可调用对象);
```

##### 不要混合使用普通指针和智能指针

【shared_ptr】可以自动调用【析构函数】。
推荐使用【make_shared】，不推荐使用【new】。
使用【make_shared】可以在构建【动态变量】时绑定【指向动态变量的shared_ptr】，【一个动态变量】不会绑定【多个shared_ptr】。

【传入智能指针的函数】不能传入【内置指针】。
【传入智能指针的函数】不能传入【使用内置指针初始化的智能指针】。
使用【内置指针】接收【new的返回值】，只有【这一个内置指针】指向【动态变量】，在【传入智能指针的函数的参数列表】使用【这个内置指针】初始化【智能指针】，进入【这个函数】后，【指向动态变量的智能指针的引用计数】是1，离开【这个函数的作用域】之后，【引用计数】减为0，【智能指针】自动释放【智能指针指向的动态变量】，【原来指向动态变量的内置指针】还指向【已经释放的动态变量】，使用【这个内置指针】产生错误。

使用【内置指针】指向【智能指针指向的动态变量】很危险，不知道【智能指针】什么时候销毁【智能指针指向的动态变量】。

##### 也不要使用get初始化另一个智能指针或为智能指针赋值

【智能指针】有【成员函数.get()】，【.get()】返回【指向【智能指针指向动态变量】的内置指针】。
调用【.get()】获取【内置指针】，【这个内置指针】传入【不能使用智能指针的函数】，【这个函数】不能释放【内置指针】。

不能调用【.get()获取的内置指针】初始化【智能指针】，否则会产生【两个独立的智能指针】指向【相同的动态变量】。
【一个智能指针】销毁【动态变量】时，【另一个智能指针】还会使用【这个动态变量】，产生错误。
销毁【第二个智能指针】时会第二次释放【这块动态内存】。

##### 其他shared_ptr操作

可以调用【.reset()】传入【内置指针】，【智能指针】会指向【内置指针指向的动态变量】。
【.reset()】会更新【引用计数】。
调用【.reset()】和【.unique()】管理【多个shared_ptr指向的动态变量】，【一个shared_ptr】修改【这个shared_ptr指向的动态变量】之前，先调用【.unique()】检查【这个shared_ptr指向的动态变量】是否只被【自己】指向。
如果只有【自己】指向【这个动态变量】，那么可以直接修改【这个动态变量】。
如果还有【其他的智能指针】指向【这个动态变量】，那么调用【.reset()】传入【【这个动态变量】初始化的【new】】，使得【自己】指向【一个新的动态变量】，修改【这个新的动态变量】。

#### 12.1.4 智能指针和异常

使用【异常处理的程序】发生异常之后，程序可以继续执行，程序必须正确释放资源。
使用【智能指针】可以正确释放资源。

函数有两种退出方式，【正常退出】和【异常退出】，这两种情况都会销毁【局部变量】。
销毁【局部智能指针变量】会检查【引用计数】，自动释放资源。

销毁【局部内置指针】不会自动释放资源。

如果在函数之外没有【指向这块动态内存的指针】，那么无法释放【这块动态内存】。

##### 智能指针和哑类

很多类有【析构函数】，在【析构函数】释放资源。
有一些类必须手动释放资源，比如【同时为C和C++设计的类】。

有一些类申请了资源，但是没有定义【析构函数】释放资源，容易忘记手动释放资源。
忘记手动释放资源，程序产生【资源泄露】。

使用【智能指针】自动释放【这些类的资源】。

##### 使用我们自己的释放操作

默认情况，【shared_ptr】指向【动态内存】。
销毁【shared_ptr】，【shared_ptr】使用【delete】释放【shared_ptr指向的动态内存】。
使用【自定义释放操作】替代【delete】可以自动释放资源。
定义【删除器函数】释放【shared_ptr管理类的资源】。

创建【管理类的shared_ptr】需要传入【删除器函数】。

销毁【管理类的shared_ptr】会自动调用【删除器函数】释放【shared_pt管理类的资源】。

##### 智能指针规范

不使用【一个内置指针】初始化【多个智能指针】。

不delete【.get()返回的内置指针】。

不调用【.get()返回的内置指针】初始化或.reset()【智能指针】。

在销毁【最后一个智能指针】之后，【之前智能指针.get()返回的内置指针】失效。

使用【智能指针】管理资源，定义【智能指针】需要传入【删除器】。

#### 12.1.5 unique_ptr

【unique_ptr】和【动态变量】一对一。
销毁【unique_ptr】会销毁【unique_ptr指向的动态变量】。
表12.1 shared_ptr和unique_ptr都支持的操作。

【shared_ptr】有【make_shared】，【unique_ptr】没有【make_unique】。
只能直接初始化【unique_ptr】。

【unique_ptr】不支持拷贝和赋值操作。

###### 表12.4 unique_ptr操作

```c++
// 使用delete释放资源的空unique_ptr变量
unique_ptr<unique_ptr指向类型> unique_ptr变量;

// 使用可调用对象释放资源的空unique_ptr变量
unique_ptr<unique_ptr指向类型, 可调用对象类型> unique_ptr变量;

// 使用可调用对象释放资源的空unique_ptr变量
unique_ptr<unique_ptr指向类型, 可调用对象类型> unique_ptr变量(可调用对象);

// 释放unique_ptr变量指向资源，unique_ptr变量置空
unique_ptr变量 = nullptr;

// up置空
指向【up指向资源】的内置指针 up.release();

// 释放up指向资源
up.reset();		    // up置空
up.reset(nullptr);  // up置空
up.reset(内置指针);	// up指向【内置指针指向的资源】
```

调用【.release()】或【.reset()】让【另一个指针】指向【有指针指向的资源】。

##### 传递unique_ptr参数和返回unique_ptr

例外，可以拷贝和赋值【马上要被销毁的unique_ptr】。
比如【函数返回的unique_ptr】。

##### auto_ptr

【以前的标准库】有【auto_ptr类】，【auto_ptr】和【unique_ptr】的特性类似。
【容器】不能保存【auto_ptr】，【函数】不能返回【auto_ptr】。

##### 向unique_ptr传递删除器

【unique_ptr】可以使用【自定义释放操作】替代【delete】。
【unique_ptr】和【shared_ptr】管理删除器方式有区别。

定义【自定义释放操作的unique_ptr变量】必须在尖括号指定【自定义释放操作的类型】，在参数传入【自定义释放操作的参数】。

使用【unique_ptr】替代【shared_ptr】实现【自动释放资源的结构体】。

先使用【decltype】传入【函数名】获取【函数名的函数类型】，再在【获取的函数名的函数类型】之后写上【星号】转为【指向函数名的函数指针类型】。

##### 12.1.6 weak_ptr

【智能指针weak_ptr】不控制【weak_ptr指向的动态变量的生存期】，【weak_ptr】指向【shared_ptr指向的动态变量】。
【weak_ptr】不修改【weak_ptr绑定的shared_ptr的引用计数】。
【shared_ptr】释放资源和【weak_ptr】无关。

###### 表12.5 weak_ptr

```c++
weak_ptr<weak_ptr指向的类型> 空weak_ptr变量;
weak_ptr<weak_ptr指向的类型> 和传入shared_ptr变量指向相同动态变量的weak_ptr_ptr变量(weak_ptr指向的类型能转换为shared_ptr指向的类型的shared_ptr);
// weak_ptr变量指向weak_ptr变量/shared_ptr变量指向的动态变量
weak_ptr变量 = weak_ptr变量/shared_ptr变量;
// weak_ptr变量置空
weak_ptr变量.reset();
指向weak_ptr变量指向的动态变量的shared_ptr的数量 weak_ptr变量.use_count();
指向weak_ptr变量指向的动态变量的shared_ptr的数量是否为零 weak_ptr变量.expired();

// 指向weak_ptr变量指向动态变量的shared_ptr的数量为零返回空shared_ptr，不为零返回指向weak_ptr变量指向的动态变量的shared_ptr
shared_ptr weak_ptr变量.lock();
```

定义【weak_ptr变量】传入【shared_ptr变量】初始化。

【weak_ptr变量】和【weak_ptr变量绑定的shared_ptr变量】指向【相同动态变量】。
【weak_ptr】不增加【weak_ptr绑定的shared_ptr变量的引用计数】。
【weak_ptr指向的动态变量】可能已经释放。

不能直接使用【weak_ptr】访问【weak_ptr指向的动态变量】，必须调用【.lock()】。
【.lock()】检查【weak_ptr指向的动态变量】是否存在。
如果存在，那么【.lock()】返回【和weak_ptr指向相同动态变量的shared_ptr】。
如果【shared_ptr】存在，那么【shared_ptr指向的动态变量】存在。

##### 核查指针类

为了讲解【weak_ptr】的作用，为【StrBlob类】定义一个【伴随指针类StrBlobPtr】。
【StrBlob类】有【成员变量data】，【StrBlobPtr类】有【weak_ptr成员变量】，使用【StrBlob变量】初始化【StrBlobPtr变量】，【StrBlobPtr变量的weak_ptr成员变量】指向【StrBlob类的shared_ptr成员变量data指向的vector动态变量】。
【weak_ptr】不影响【StrBlob类的成员变量data指向的vector动态变量的生命期】。
使用【weak_ptr】访问【不存在变量】会抛出异常。

【StrBlobPtr类】有两个成员变量【wptr】和【curr】，【wptr】指向【StrBlob类的成员变量data指向的vector动态变量】或为空，【curr】保存【vector的下标】。
【StrBlobPtr类】有【成员函数.check()】，【成员函数.check()】检查是否可以解引用【StrBlobPtr】。
使用【默认构造函数】定义【空StrBlobPtr变量】，【空StrBlobPtr变量】的【成员变量curr】为0，【成员变量wptr】为空。
使用【有参构造函数】传入【StrBlob变量引用】和【可选的下标】定义【StrBlobPtr变量】，【StrBlobPtr变量】的【成员变量wptr】指向【传入的StrBlob变量的成员变量data指向的vector动态变量】，【成员变量curr】为【传入的可选的下标】。
【可选的下标的默认参数】是【vector的首元素的下标】。
【StrBlob类的成员end】会使用【可选的下标参数】。

因为【StrBlobPtr类的有参构造函数的参数】是非const的。
所以【StrBlobPtr类的有参构造函数】不能传入【const StrBlobPtr】。

【StrBlobPtr类的成员函数.check()】会检查【StrBlobPtr类的weak_ptr成员变量指向的vector动态变量】是否存在。

因为【weak_ptr】不修改【指向weak_ptr指向的动态变量的shared_ptr的引用计数】，所以可能已经释放了【StrBlobPtr类的weak_ptr成员变量wptr指向的vector动态变量】。
如果已经释放【vector动态变量】，那么【.lock()】返回【空指针】。
访问【已经释放的vector动态变量】会抛出异常。
【.check()】检查【传入的下标】，如果【传入的下标】合法，那么【.check()】返回【.lock()返回的shared_ptr】。


##### 指针操作

定义【成员函数.deref()】和【成员函数.incr()】，【成员函数.deref()】解引用【StrBlobPtr类的weak_ptr成员变量】，【成员函数.incr()】递增【StrBlobPtr类的成员变量curr】。

【成员函数.deref()】调用【成员函数.check()】，【成员函数.check()】检查【vector动态变量】是否存在，【成员变量curr】是否在【vector动态变量的下标范围】内。

如果【成员函数.check()】成功，返回【指向【StrBlobPtr类的weak_ptr成员变量指向的vector动态变量】的shared_ptr】，【成员函数.deref()】解引用【成员函数.check()返回的shared_ptr】，返回【vector动态变量[成员变量curr]】。

【成员函数.incr()】调用【成员函数.check()】。

【StrBlobPtr类】是【StrBlob类】的友元类。
为【StrBlob类】定义【成员函数.begin()和.end()】，【成员函数.begin()和.end()】返回【StrBlobPtr变量】。

### 12.2 动态数组

【new和delete运算符】一次申请/释放一个【动态变量】，有时需要一次申请/释放多个【动态变量】。
比如【vector和string】在连续内存中保存元素，在【vector和string容器】重新申请内存时，需要一次申请多个【动态变量】。

【C++和标准库】提供了两种【一次申请/释放多个动态变量的方法】。
使用【C++new表达式的另一种语法】申请并初始化【动态数组变量】。
使用【标准库的allocator类】申请并初始化【动态数组变量】，【标准库的allocator类】的【申请操作】和【初始化操作】。
【allocator类】的性能更好，管理内存更灵活。

大多数应用不需要直接访问【动态数组变量】。
应用需要【可变数量的变量】时，应该使用更简单、更快速、更安全的【vector或其他的标准库的容器】，比如【StrBlob类】。

大多数应用应该使用【标准库的容器】，而不是【动态数组变量】。
使用【容器】更简单，更不容易出错，可能性能更好。

【使用容器的类】可以使用【默认版本的拷贝、赋值、析构函数】。
【使用动态数组变量的类】必须使用【自定义版本的拷贝、赋值、析构函数】。

#### 12.2.1 new和数组

```c++
// 【动态数组的元素的个数】必须是整型，可以是常量或非常量
动态数组的元素类型* 指向动态数组开头的指针变量 = new 动态数组的元素类型[动态数组的元素的个数];



// 定义【指定元素类型和元素的个数的数组类型】
typedef 数组的元素类型 数组类型[数组的元素的个数];

// 使用【上面定义的数组类型】传入【new】，申请【动态数组】
数组的元素类型* 指向动态数组开头的指针变量 = new 数组类型;
```

##### 分配一个数组会得到一个元素类型的指针

通常把【new T[]分配的内存】称作【动态数组】。
在使用【new】 申请【动态数组】时，没有获得【数组类型变量】，而是获得【数组的元素类型的指针】。
即使使用【typedef】定义【数组类型】传入【new】，【new】也不会返回【数组类型变量】。

因为【动态数组】不是【数组类型】，所以【动态数组】不能使用【begin()和end()】。
【begin()和end()】使用【数组维度】返回【首元素和尾后元素的指针】。
【动态数组】不能使用【范围for】。

##### 初始化动态分配对象的数组

默认情况，默认初始化【使用new申请的动态变量】。
可以在【new表达式】最后写上【一对空圆括号】，值初始化【使用new申请的动态变量】。
可以在【new表达式】最后写上【一对花括号包围的元素的值】，列表初始化【使用new申请的动态数组】。

【new表达式】和【内置数组】的【列表初始化】的行为类似。
如果【元素的值个数】大于【元素的个数】，那么【new表达式】失败，不申请内存。
会抛【出bad_array_new_length类型异常】。
【bad_array_new_length类型的头文件】是【new】。
使用【一对空圆括号】值初始化【使用new申请的动态数组】，不能在括号中写元素的值，不能在【new表达式】中使用【auto】申请【动态数组】。

##### 动态分配一个空数组是合法的

【new的元素的个数】可以是任意表达式。
【new的元素的个数】可以是0。

使用【new】申请【元素的个数0的动态数组】，【new】返回【合法非空指针】。
【这个指针】和【new返回的其他的指针】不同。
【这个指针】是【指针指向的零大小动态数组的尾后指针】。
【这个指针】可以比较，加减0，加减自身。
【这个指针】不能解引用。

【这个指针】可以使用【for循环】遍历【零大小动态数组】。

##### 释放动态数组

在【delete】之后写上【一对空方括号】，释放【动态数组】。

逆序【销毁动态数组的元素】，释放【动态内存】。

释放【动态数组】必须写上【一对空方括号】，告诉【编译器】，【传入delete的指针】指向【动态数组】。
释放【动态数组】没写【一对空方括号】，释放【单个动态变量】写了【一对空方括号】，行为未定义。

使用【自定义的数组类型】传入【new】，申请【动态数组】，必须写上【一对空方括号】释放【这个动态数组】。

##### 智能指针和动态数组

【unique_ptr】可以指向【new申请的动态数组】。
在【unique_ptr变量指向类型】之后写上【一对空方括号】，定义【指向动态数组的unique_ptr变量】。
【一对空方括号】表示【unique_ptr】指向数组。
销毁【指向动态数组的unique_ptr变量】时，自动使用delete[]释放【unique_ptr变量指向的动态数组】。

【指向动态数组的unique_ptr变量】和【指向单个动态变量的unique_ptr变量】的支持的操作不同。
【指向动态数组的unique_ptr变量】不能使用【点运算符】和【箭头运算符】。
【指向动态数组的unique_ptr变量】可以使用【下标运算符】。

###### 表12.6 指向数组的unique_ptr

```c++
unique_ptr<unique_ptr变量指向类型[]> 空unique_ptr变量;

// 【指向动态数组的内置指针的类型】必须可以转换为【unique_ptr变量指向类型】
unique_ptr<unique_ptr变量指向类型[]> 指向内置指针指向的动态数组的unique_ptr变量(指向动态数组的内置指针);

unique_ptr变量指向的动态数组下标处元素 非空unique_ptr变量[下标]
```

【shared_ptr】不支持直接指向【动态数组】。
【shared_ptr】指向【动态数组】必须自定义【删除器】。

可以使用【lambda表达式】作为【删除器】传入【shared_ptr的构造函数】，【lambda表达式】使用【delete[]】释放【动态数组】。

如果不提供删除器，那么行为未定义。
默认情况【shared_ptr】使用delete销毁【shared_ptr指向的动态变量】，如果不提供【删除器】，那么产生的错误和没写【一对空方括号】相同。
【shared_ptr】不支持【下标运算符】和【算术运算符】，可以调用【.get()】获取【指向shared_ptr指向的动态变量的内置指针】，使用【这个内置指针】访问【动态数组的元素】。

【VS2019】支持【shared_ptr<shared_ptr指向类型[]> shared_ptr变量】和【shared_ptr变量[下标]】。

#### 12.2.2 allocator类

【new】不灵活。
【new】申请并初始化【动态变量】。
【delete】销毁并释放【动态变量】。
在申请【单个动态变量】时，可以确定【单个动态变量的值】，【申请操作】和【初始化操作】组合在一起符合需求。

在申请【动态变量数组】时，想要根据需求在【动态内存】构造【动态变量】。
想要分离【申请操作】和【初始化操作】。

【申请操作】和【初始化操作】组合在一起浪费性能。

【new】申请并初始化【整个动态数组】，不需要所有【动态数组元素】。
在【new】之后立刻赋值【动态数组元素】。
赋值【每个动态数组元素】两次，第一次在new默认初始化时，第二次在赋值时。

不能new【没有默认构造函数的类对象】。

##### allocator类

【allocator类的头文件】是【memory】，【allocator】分离了【申请操作】和【初始化操作】。
【allocator】申请了【动态内存】，没有初始化【动态变量】。

【allocator】是【模板】。
定义【allocator变量】必须指定【allocator变量申请的动态变量的类型】。
【allocator变量】根据【allocator变量申请的动态变量的类型】申请【正确大小和对齐的动态内存】。

###### 12.7 标准库allocator类及算法

```c++
allocator<allocator变量申请的动态变量的类型> allocator变量;

// 申请动态内存
动态变量类型的指针 allocator变量.allocate(动态变量的个数);

// 构造动态变量
allocator变量.construct(动态变量类型的指针, 动态变量的构造函数的参数);

// 析构动态变量
allocator变量.destory(动态变量类型的指针);

// 释放动态内存
allocator变量.deallocate(allocate返回的指针, 传入allocate的动态变量个数);
```

##### allocator分配未构造的内存

【allocator】申请了【动态内存】，没有构造【动态变量】。
需要在【动态内存】构造【动态变量】。
【成员函数.construct()】传入【一个指针】和【动态变量的构造函数的参数】，在【指针地址】构造【动态变量】。
使用【传入的动态变量的构造函数的参数】构造【动态变量】。
【.construct()】和【make_shared()】的参数类似，【传入的动态变量的构造函数的参数】匹配【动态变量的构造函数】。

不能使用【没有构造动态变量的动态内存】。

必须调用【.destory()】销毁【用完的动态变量】。
【.destory()】传入【一个指针】，析构【指针指向的动态变量】。

使用【while循环】和【.destory()】从后往前销毁【.construct()构造的动态变量】。

可以重新使用【销毁的动态变量所在的动态内存】，也可以释放【销毁的动态变量所在的动态内存】。
调用【.deallocate()】释放【销毁了动态变量的动态内存】。

【.deallocate()的指针参数】不能为空，【指针参数】必须等于【.allocate()返回的动态变量类型的指针】。
【.deallocate()的大小参数】必须等于【.allocate()传入的动态变量的个数】。

##### 拷贝和填充未初始化内存的算法

【和allocator有关的标注库算法】可以在【未初始化内存】中构造【动态变量】。
【和allocator有关的标注库算法的头文件】是【memory】。

##### 表12.8 allocator算法

```c++
uninitialized_copy(拷贝范围的首迭代器, 拷贝范围之后的迭代器, 目的动态内存的迭代器);
uninitialized_copy_n(拷贝开始位置的迭代器, 拷贝个数, 目的动态内存的迭代器);
uninitialized_fill(动态内存范围的首迭代器, 动态内存范围之后的迭代器, 动态变量初始值);
uninitialized_fill_n(动态内存开始位置的迭代器, 创建动态变量个数, 动态变量初始值);
```

调用【.allocate()】申请【未初始化的动态内存】。

使用【uninitialized_copy()】传入【容器拷贝范围的迭代器】和【指向未初始化的动态内存的指针】拷贝构造【在动态内存上的动态变量】。

使用【uninitialized_fill_n】初始化【在剩余动态内存上的动态变量】。

【uninitialized_copy()】和【拷贝算法】的参数类似。
【前两个迭代器参数】构成【拷贝范围的迭代器范围】，【第三个迭代器参数】指向【目的动态内存】。
【uninitialized_copy()的第三个迭代器参数】必须【指向未初始化的动态内存】。
【uninitialized_copy()】在【目的动态内存】构造【动态变量】。

【uninitialized_copy()】返回【指向最后一个构造的动态变量之后的指针】。
使用【uninitialized_copy()返回的指针】和【uninitialized_fill_n()】初始化【在剩余动态内存上的动态变量】。

### 12.3 使用标准库：文本查询程序

实现【文本查询程序】，总结【学习的标准库相关内容】。
【程序】读取【文件】，在【文件】中查询【单词】。
显示【单词在文件中的总出现次数】和【单词每次出现所在整行的文本】。

【例子】使用【文本查询程序】查询【单词element】。

#### 12.3.1 文本查询程序设计

【列出程序的操作】是【开始设计程序的好方法】。
通过【需要的操作】分析【需要的数据结构】。
下面是【文本查询程序需要完成的任务】。

在【程序】读取【文件】时，逐行读取【文件】，【每行】分解为【独立的单词】。

在【程序】输出时，可以获取每个单词对应的行号，行号必须升序，行号不能重复，可以获取行号对应的整行文本。

下面是使用【标准库】实现【需要的操作】的方法。

使用【vector\<string\>】保存【整个输入文件】。
【输入文件的一行】是【vector\<string\>的一个元素】。
使用【行号】作为【下标】访问【vector\<string\>】，获取【指定行文本】。

使用【istringstream】把【每行】分解为【单词】。

使用【set】保存【每个单词出现的行号】。

使用【map】关联【单词】和【保存这个单词出现行号的set】。

使用【shared_ptr】。

##### 数据结构

虽然可以使用【vector、set、map】直接编写【文本查询程序】，但是推荐使用【抽象的解决方案】。
定义【保存输入文件的类】。
【保存输入文件类的类名】是【TextQuery】，【TextQuery】包含【一个vector成员变量】和【一个map成员变量】。
使用【vector】保存【输入文件的文本】，使用【map】关联【单词】和【保存这个单词出现行号的set】。
【TextQuery的构造函数】读取【指定的输入文件】，【TextQuery的一个成员函数】执行查询。

【TextQuery的查询成员函数】查找【map】检查【指定单词是否出现】。
【TextQuery的查询成员函数】返回【单词的总出现次数】，【单词每次出现的行号】，【单词每次出现所在整行的文本】。

定义【QueryResult类】，【QueryResult类】保存查询结果，【TextQuery的查询成员函数】返回【QueryResult类】。
【QueryResult的成员函数print】打印【查询结果】。

##### 在类之间共享数据

【QueryResult类】输出【查询的结果】。
【查询的结果】包含【保存单词每次出现行号的set】和【单词每次出现所在整行的文本】。
【QueryResult变量】保存【查询的结果】。

研究访问【QueryResult类】的方式。
直接拷贝浪费时间。

可以返回【TextQuery变量内部的迭代器】，避免拷贝。
但是如果【TextQuery变量】在【QueryResult变量】之前销毁，那么【QueryResult变量】引用了【不存在的TextQuery变量的数据】。
可以使用【shared_ptr】同步【TextQuery变量】和【QueryResult变量】的生存期。

##### 使用QueryResult类

先编写程序使用类，再实现类的成员，是设计一个类的非常有用的方法。
使用这种方法可以知道类是否支持需要的操作。
定义函数，传入【指向输入文件的ifstream的引用】，调用【TextQuery】和【QueryResult】的成员函数，实现打印【指定单词的查询结果】。

使用【传入的指向输入文件的ifstream的引用】初始化【TextQuery变量tq】。
【TextQuery的构造函数】读取【输入文件】，保存在【vector】中，定义【map】关联【单词】和【保存这个单词出现行号的set】。

使用【while(true)】循环输入【查询的单词】，打印查询结果。
使用【if】判断【输入内容】，break循环或者打印查询结果。

#### 12.3.2 文本查询程序类的定义

从定义【TextQuery】开始。
定义【TextQuery变量】，传入【istream变量】读取【输入文件】。
【TextQuery的成员函数.query()】，传入【string变量】，返回【QueryResult变量】，使用【QueryResult变量】显示【string出现的行】。

设计【TextQuery】需要考虑【TextQuery】和【QueryResult】的数据共享。
因为【QueryResult】需要共享【保存输入文件的vector】和【保存单词出现的行号的set】。
所以【QueryResult】有两个成员变量，【指向vector动态变量的shared_ptr成员变量】和【map\<string单词, shared_ptr\<保存单词出现行号的set动态变量\>\>动态变量】。

为了提高代码可读性，定义【一个成员类型】表示【vector\<string\>的下标】。

写作用域是定义类最困难的地方。
【可能存放在头文件的代码】使用【标准库】不直接声明整个命名空间，而是使用作用域访问符。
使用【作用域访问符】可能让【代码】难读。

##### TextQuery构造函数

【QueryResult】的构造函数，传入【一个istream变量】，逐行读取【输入文件】。

构造函数的初始化列表申请一个新的vector变量保存输入文件的文本。
使用getline()逐行读取输入文件，保存在vector中。
因为file是shared_ptr类型，所以使用->运算符解引用file获取file指向的vector变量的成员函数.push_back()。

使用istringstream处理一行的每个单词。
在处理一行的每个单词的while循环中，使用istringstream的输入运算符>>获取一行的每个单词，保存在word。
在while循环中，使用word作为下标访问map获取word关联的shared_ptr\<set\>，获取这个shared_ptr的引用lines。
改变引用lines也会改变lines对应的shared_ptr\<set\>。

如果单词不在map中，那么使用下标运算符会把单词添加到map中，值初始化【单词关联的保存单词出现次数的shared_ptr\<set\>】。
shared_ptr\<set\>为空，shared_ptr\<set\>的引用lines也为空。
如果引用lines为空，那么定义一个新的set变量，shared_ptr\<set\>调用【.reset()】，shared_ptr\<set\>指向新的set变量。

不管set变量是否是重新定义的，都调用.insert()把当前行号添加到set中。
如果一个单词在一行出现多次，那么多次调用.insert()，第一次调用之后的其他的调用什么也不做。

##### QueryResult类

【QueryResult】有三个成员变量。
一个保存查询单词的string。
一个指向保存输入文件的vector的shared_ptr。
一个指向保存单词出现行号的set的shared_ptr
【QueryResult唯一一个成员函数】是构造函数，构造函数初始化三个成员变量。

构造函数在初始化列表中把参数保存在成员变量中。

##### query函数

query函数传入一个要查询单词string，query使用【这个string类型的要查询的单词】作为下标访问map获取set。
如果找到了这个string，那么query函数定义一个【QueryResult变量】，【QueryResult变量】保存单词string，输入文件vector，map中获取的set。

如果没找到这个string，那么query函数定义一个【QueryResult静态局部变量】，【QueryResult静态局部变量】的map中获取的指向set的shared_ptr为空。

##### 打印结果

使用print函数在指定流中打印传入的【QueryResult变量】。

使用set的大小作为单词出现次数。
因为使用了shared_ptr指向set，lines是shared_ptr的引用，所以需要解引用lines。
使用make_plural()根据单词出现次数打印不同的单词。

使用for循环遍历保存单词出现行号的set，打印行号。

使用行号作为下标访问vector获取单词出现所在整行文本。

函数可以正确处理未找到单词的情况，范围for一个空容器，一次循环也不会执行。

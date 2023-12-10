<!--
 * @Author: zingerWang 1062659156@qq.com
 * @Date: 2023-11-24 21:21:34
 * @LastEditors: zingerWang 1062659156@qq.com
 * @LastEditTime: 2023-12-10 11:00:29
 * @FilePath: \ZingerNotesForCPlusPlus\c++的一些琐碎的知识点.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
# sizeof相关问题
* 在C或C++中，sizeof(数组名) 返回的是整个数组的大小，以字节为单位。这个大小是数组中所有元素的大小的总和。

* 例如，如果你有一个数组定义如下：

```cpp
int arr[10];
```
在这个例子中，sizeof(arr) 将返回整个数组 arr 的大小。由于 int 类型的大小通常是4字节（这取决于编译器和平台），arr 包含10个 int，所以 sizeof(arr) 将返回 10 * 4 = 40 字节。

* 重要的是要注意，sizeof 对于数组名返回的是整个数组的大小，而不是指向数组第一个元素的指针的大小。当数组名传递给函数时，它会退化为指向其第一个元素的指针，而 sizeof 在指针上的结果仅仅是指针本身的大小，而不是它所指向的数组的大小。指针的大小通常是与系统的位数（如32位或64位）相关的固定大小。
# union问题
* 在 C++ 中，union 是一种特殊的数据类型，可以用来在同一个内存区域存储不同的数据类型。union 类型定义了一种数据结构，其中所有成员都共享相同的内存空间。union 的语法如下：
```cpp 
union {  
  type1 member1;  
  type2 member2;  
  type3 member3;  
};
```
* union 的使用方式与结构体类似，可以通过访问成员变量的方式来读取和写入数据。但是需要注意的是，由于所有成员变量共享相同的内存空间，因此只有一个成员变量的值是有效的，其他成员变量的值会被覆盖。下面是一个使用 union 的例子：

```cpp
#include <iostream>
union ExampleUnion {  
  int intValue;  
  int intValueBak;  
  float floatValue;  
  char charValue;  
};  
  
int main() {  
  using namespace std;
  ExampleUnion myUnion;  
  myUnion.intValue = 10; // 设置 intValue 的值为 10  
  cout << myUnion.intValue << endl; // 输出 10  
  myUnion.intValueBak = 10;
  cout << myUnion.intValueBak << endl; // 输出 10  
  myUnion.floatValue = 3.14f; // 设置 floatValue 的值为 3.14，intValue 的值会被覆盖  
  cout << myUnion.intValue << endl; // 输出 0（intValue 的值被覆盖）  
  myUnion.charValue = 'A'; // 设置 charValue 的值为 'A'，floatValue 和 intValue 的值都会被覆盖  
  cout << myUnion.charValue << endl; // 输出 'A'（charValue 的值）  
  return 0;  
}
```
在上面的例子中，我们定义了一个名为 ExampleUnion 的 union，其中包含了三个不同类型的成员变量。在 main 函数中，我们创建了一个 ExampleUnion 类型的变量 myUnion，并分别设置了其中三个成员变量的值。由于所有成员变量共享相同的内存空间，因此当我们修改其中一个成员变量的值时，其他成员变量的值也会被覆盖。
# c++ template的特化和偏特化
在C++中，特化（specialization）和偏特化（partial specialization）都是模板的一部分，但它们在使用的上下文和目的上有所不同。
## 特化（Specialization）
模板特化是C++模板的一个重要特性，它允许我们为模板定义一个特殊的成员函数或类，这个特殊的成员函数或类专门用于处理模板参数的特殊情况。在模板特化中，我们必须明确指定我们正在特化的模板的名称。
```cpp
template <typename T>  
struct is_integral {  
    static const bool value = false;  
};  
  
template <>  
struct is_integral<int> {  
    static const bool value = true;  
};
```
在这个例子中，我们首先定义了一个名为is_integral的模板，该模板的默认行为是使value成员等于false。然后，我们特化了这个模板，为int类型创建了一个特殊的版本，在这个版本中，value成员被设置为true。
## 偏特化（Partial Specialization）
偏特化是模板的一种形式，它允许我们为模板定义一个适用于模板的部分参数类型的特殊情况。偏特化是通过在模板参数列表中减少一些参数类型来进行的。
```cpp
template <typename T, typename U>  
struct is_same {  
    static const bool value = false;  
};  
template <typename T>  
struct is_same<T, T> {  
    static const bool value = true;  
};
```
在这个例子中，我们首先定义了一个名为is_same的模板，该模板的默认行为是使value成员等于false。然后，我们偏特化了这个模板，为T和T相同的类型创建了一个特殊的版本，在这个版本中，value成员被设置为true。注意，偏特化的形式是在模板参数列表中减少了类型参数的数量。
## 总结
总的来说，特化和偏特化的主要区别在于：特化通常是为了处理模板参数的一个特定类型；而偏特化则是为了处理模板参数的部分特定类型组合。
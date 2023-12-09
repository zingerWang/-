# Traits编程技法
### traits编程技法和模板元编程的关系
* Traits编程技法和模板元编程都是C++中非常高级的编程技术，它们都涉及到模板和编译时编程，但是它们的目的和应用方式有所不同。

* 模板元编程（Template Metaprogramming）是一种在编译时进行计算的方法，它使用模板来生成代码或者控制代码的生成。模板元编程的主要目的是在编译时进行计算，以生成或优化运行时代码。模板元编程的例子包括模板特殊化、模板继承、元函数等。

* Traits编程技法是一种用于处理类型特性的方法，它可以在编译时获取类型的特性，并根据这些特性生成相应的代码。Traits的主要目的是提供一种编译时查询类型特性的方法，以便在编译时进行类型检查、类型转换、类型特性提取等操作。

* 因此，Traits编程技法和模板元编程的关系是，它们都是C++中的高级编程技术，都涉及到编译时计算，但是它们的重点和应用方式不同。模板元编程更注重在编译时进行计算和控制代码的生成，而Traits编程技法更注重在编译时查询和处理类型的特性。
## traits处理类型的特性的一个例子
```cpp
#include <iostream>  
#include <type_traits>  
  
// 定义一个traits类，用于检查类型是否具有某个成员函数  
template<typename T>  
struct has_member_function {  
  private:  
    typedef char one;  
    typedef struct { char a[2]; } two;  
  
    template<typename C> static one test(decltype(&C::my_member_function));  
    template<typename C> static two test(...);  
  
  public:  
    static const bool value = sizeof(test<T>(nullptr)) == sizeof(char);  
};  
  
// 定义一个类，该类具有一个名为my_member_function的成员函数  
class MyClass {  
public:  
    void my_member_function() {}  
};  
  
int main() {  
    // 检查MyClass类型是否具有my_member_function成员函数  
    if (has_member_function<MyClass>::value) {  
        std::cout << "MyClass has my_member_function" << std::endl;  
    } else {  
        std::cout << "MyClass does not have my_member_function" << std::endl;  
    }  
  
    // 检查int类型是否具有my_member_function成员函数  
    if (has_member_function<int>::value) {  
        std::cout << "int has my_member_function" << std::endl;  
    } else {  
        std::cout << "int does not have my_member_function" << std::endl;  
    }  
  
    return 0;  
}
```
`has_member_function<int>` 不会报错，原因在于模板特化和SFINAE（Substitution Failure Is Not An Error）原理。这个原理是C++模板元编程的一个关键概念，它允许在模板特化时对类型进行安全的检查，而不会导致编译错误。我们来逐步分析：

* **SFINAE 原理**: SFINAE原则允许在模板参数替换导致错误时，不视为硬性编译错误，而是简单地放弃当前模板特化，寻找其他合适的特化或重载。

* **模板 `has_member_function`**: 这个模板检查一个类型 `T` 是否有一个名为 `my_member_function` 的成员函数。它通过两个重载的 `test` 函数实现这一点。第一个重载试图将模板参数 `C` 的 `my_member_function` 成员函数地址作为参数传递，而第二个重载是一个可变参数函数，可以接受任何类型的参数。

* **`has_member_function<int>` 调用**: 当尝试实例化 `has_member_function<int>` 时，模板会尝试使用 `int` 类型作为 `C` 来调用 `test` 函数。由于 `int` 类型没有 `my_member_function` 成员函数，第一个 `test` 重载会导致一个替换失败。但根据SFINAE原则，这不是一个错误，而是导致编译器退而求其次，选择第二个 `test` 重载。

* **为什么不报错**: 尽管 `int` 类型没有 `my_member_function` 成员函数，但是由于第二个 `test` 函数可以接受任何参数，编译器就会选择这个重载，从而避免了编译错误。这就是为什么 `has_member_function<int>` 表达式不会导致编译错误的原因。

* **计算 `value` 的结果**: 在 `int` 的情况下，由于选择了第二个 `test` 函数重载，`sizeof(test<T>(nullptr))` 的结果会是 `sizeof(two)`，这与 `sizeof(char)` 不同，所以 `value` 会被设置为 `false`，正确地表明 `int` 没有 `my_member_function` 成员函数。

通过这种方式，`has_member_function` 模板可以安全地检查任何类型是否具有特定的成员函数，而不会在类型不满足条件时产生编译错误。

# allocator::rebind用法
### C++03用法
* 在C++中，std::allocator 类模板的 rebind 成员模板是用来将一个分配器从一种类型转换为另一种类型的工具。这在泛型编程中非常有用，特别是当你需要一个与容器元素类型不同的分配器类型时。

* 例如，你可能有一个用于分配 TypeA 对象的 std::allocator<TypeA>，但你需要一个用于分配 TypeB 对象的分配器。这时，你可以使用 rebind 来获得正确类型的分配器。

* 以下是一个 rebind 的示例：

```cpp
#include <memory>

template <typename T>
class MyContainer {
public:
    using allocator_type = std::allocator<T>;
    using other_allocator_type = typename allocator_type::template rebind<int>::other;

    // ...
};

int main() {
    MyContainer<double>::allocator_type allocator_for_doubles;
    MyContainer<double>::other_allocator_type allocator_for_ints;

    // ...
}
```

在这个例子中，MyContainer 是一个模板类，它使用 std::allocator。通过 rebind，它能够从用于 double 的分配器获得一个用于 int 的分配器。

从C++11开始，分配器的 rebind 用法已经大大简化，大多数时候你不需要显式地使用它。新的标准提供了更灵活的分配器特性，使得分配器可以自动适应不同类型的需求。

```cpp
using other_allocator_type = typename allocator_type::template rebind<int>::other;
```

这行代码是在一个模板类中定义类型别名 other_allocator_type，它使用了 allocator_type 的 rebind 模板。具体来说：

1. allocator_type: 这是一个别名，指的是 std::allocator<T>，其中 T 是 MyContainer 模板的模板参数。

2. typename 关键字: 在这里，typename 用于告诉编译器，allocator_type::rebind<int>::other 是一个类型。在模板编程中，如果你要引用一个依赖于模板参数的嵌套类型，你需要在前面加上 typename 来明确地指出这是一个类型。这是因为在模板实例化之前，编译器无法知道 allocator_type::rebind<int>::other 是一个类型还是一个成员。

3. template 关键字: 在这里，template 关键字是必须的，因为 rebind 是一个依赖于模板参数的成员模板。当你在一个模板类或模板函数内部访问另一个模板的成员时，你需要在该成员前加上 template 关键字，以告诉编译器它是一个模板。在这种情况下，没有 template 关键字，编译器可能无法正确解析代码。

4. rebind<int>::other: rebind 是 std::allocator 中的一个成员模板，它用于获取一个新的分配器类型，该分配器分配的对象类型与原始分配器不同。在这个例子中，rebind<int> 产生一个新的分配器类型，它用于分配 int 类型的对象。::other 是 rebind 模板的一个嵌套类型别名，指向了重新绑定后的分配器类型。 

# C++中的new

### 普通 new:
这是最常用的 new 形式，它为对象分配内存并调用构造函数。
例如：MyClass* obj = new MyClass();
* 不能重写
### Placement new:
这种 new 允许在已经分配的内存上构造对象。
例如：MyClass* obj = new (preAllocatedMemory) MyClass();
### operator new:
这是一个函数或一组重载函数，负责分配内存，但不调用构造函数。
它可以被重载以提供定制的内存分配行为。
例如：void* mem = operator new(sizeof(MyClass));

# trivial destructor
### 在C++中，一个类的析构函数被认为是非平凡的（non-trivial），如果满足以下任一条件：
1. 类显式定义了析构函数。
2. 类包含一个或多个成员，这些成员自身具有非平凡的析构函数。
3. 类继承自一个具有非平凡析构函数的基类。
### 在C++标准模板库（STL）中，术语“trivial destructor”（平凡析构函数）指的是一种特定类型的析构函数。一个类型的析构函数是平凡的（trivial），如果满足以下条件：

1. **默认生成**：析构函数没有被用户显式提供，而是由编译器默认生成。
2. **无操作**：析构函数不执行任何操作。也就是说，它没有显式的代码体，不做任何清理工作，例如释放内存、关闭文件句柄等。
3. **无需特别处理**：对象的销毁不需要任何特别的处理。对于这些对象，销毁过程仅涉及回收其占用的内存空间，而不需要执行任何额外的清理工作。

### 为什么重要：

- **性能优化**：了解一个类型是否有平凡的析构函数对于性能优化很重要。例如，在容器中存储具有平凡析构函数的类型时，容器可以选择更高效的内存管理策略，因为它知道不需要为每个元素单独调用析构函数。

- **类型特征**：在C++中，你可以使用类型特征（如 `std::is_trivially_destructible`）来检查一个类型是否具有平凡的析构函数。这对于模板元编程和编写只对特定类型特性的对象操作的通用代码非常有用。

### 示例：

对于简单的POD（Plain Old Data，纯旧式数据）类型，如内置类型（`int`、`char`）或只包含这些类型的结构体，它们的析构函数是平凡的：

```cpp
struct PODType {
    int a;
    char b;
};

static_assert(std::is_trivially_destructible<PODType>::value, "PODType should have a trivial destructor.");
```

在这个例子中，`PODType` 是一个POD类型，它有一个平凡的析构函数，因为它只包含基本数据类型，不需要特别的清理工作。

### 注意：

- 平凡的析构函数不应与无操作的析构函数混淆。一个用户定义的析构函数即使不执行任何操作，也不被认为是平凡的，因为它被用户显式定义了。
- C++11及更高版本提供了丰富的类型特征来查询类型属性，包括是否有平凡的构造函数、析构函数等。

在C语言中，`realloc` 函数用于调整之前通过 `malloc`, `calloc`, 或 `realloc` 分配的内存块的大小。它的作用是将内存块的大小变更为新的大小，这可以是增大或减小。

# C语言中的realloc
`realloc` 函数的原型定义在 `<stdlib.h>` 头文件中，如下所示：

```c
void* realloc(void* ptr, size_t new_size);
```

- `ptr`：指向需要重新分配大小的内存块的指针。如果 `ptr` 是一个空指针（即 `NULL`），那么 `realloc` 的行为就像 `malloc` 一样。

- `new_size`：内存块的新大小，以字节为单位。如果 `new_size` 为零，并且 `ptr` 不是 `NULL`，那么 `realloc` 会释放 `ptr` 指向的内存块，并返回一个空指针。

### `realloc` 的行为：

1. **增大内存块**：如果增大了内存块，新分配的部分不会被初始化，其内容是不确定的。

2. **减小内存块**：如果减小了内存块，超出新大小部分的数据将会丢失。

3. **内存移动**：如果无法在原位置调整大小，`realloc` 可能会分配一个新的内存块，并将原内存块的内容复制到新内存块中，然后释放原内存块。新分配的内存地址可能与原来的不同。

4. **返回值**：成功时返回指向新内存块的指针，失败时返回 `NULL`。如果 `realloc` 失败，原始内存块不会被释放，因此需要保持对原始指针的引用以避免内存泄露。

### 使用示例

```c
#include <stdlib.h>

int main() {
    int* arr = malloc(10 * sizeof(int)); // 分配初始内存
    if (arr == NULL) {
        // 处理内存分配失败
    }

    // ... 使用 arr ...

    int* temp = realloc(arr, 20 * sizeof(int)); // 调整内存大小
    if (temp == NULL) {
        // 处理内存重新分配失败
        free(arr); // 释放原始内存
    } else {
        arr = temp;
    }

    // ... 使用调整大小后的 arr ...

    free(arr); // 释放内存
    return 0;
}
```

在使用 `realloc` 时要特别小心，确保正确处理内存分配失败的情况，并避免内存泄露。如果 `realloc` 失败并返回 `NULL`，原始内存块仍然有效，需要使用 `free` 释放。
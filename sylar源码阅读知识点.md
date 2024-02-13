# 头文件相关知识
## `<stdint.h>` 
* <stdint.h>是C99标准中的头文件，它提供了标准的整数类型的定义。这个头文件定义了一些标准的整数类型，如int8_t,int16_t,int32_t,int64_t等，这些类型的大小是固定的，可以在不同的平台上提供一致的编程模型。

* 这些类型有助于编写可移植的代码，因为它们保证了在所有支持 C99 的平台上，这些类型的大小和范围都是一致的。

* 常见的类型：
    * intN_t、uintN_t（N可以为8、16、32、64）：表示长度为N位的整数类型和无符号整数类型。
    * int_leastN_t、uint_leastN_t（N可以为8、16、32、64）：表示能至少容纳N位整数的类型。
    * int_fastN_t、uint_fastN_t（N可以为8、16、32、64）：表示至少为N位的整数类型和无符号整数类型，但实现可能更快。

* stdint.h中也定义了以上各类型数的最大最小值，如INTN_MIN、UINTN_MIN、INTN_MAX、UINTN_MAX
    * int8_t、uint8_t：8位带符号整数和无符号整数，INT8_MIN为-128，INT8_MAX为127，UINT8_MAX为255。
    * int16_t、uint16_t：16位带符号整数和无符号整数，INT16_MIN为-32768，INT16_MAX为32767，UINT16_MAX为65535。
    * int32_t、uint32_t：32位带符号整数和无符号整数，INT32_MIN为-2147483648，INT32_MAX为2147483647，UINT32_MAX为4294967295。
    * int64_t、uint64_t：64位带符号整数和无符号整数，INT64_MIN为-9223372036854775808，INT64_MAX为9223372036854775807，UINT64_MAX为18446744073709551615。
## `<memory>`
是C++标准库中的头文件，它提供了内存操作函数的声明和定义。这个头文件定义了一些重要的C++类，比如std::allocator，以及一些用于内存管理的函数，比如std::free和std::malloc。此外，它还定义了一些模板类，如std::unique_ptr、std::shared_ptr和std::weak_ptr，这些类用于处理对象的生命周期和内存管理。
## `<sstream>` 和 `<fstream>`
是 C++ 标准库的头文件。
sstream 这个头文件，sstream 提供了 string stream 的功能，可以将字符串当作文件一样进行读写操作。
fstream 库提供了文件流的读写功能，可以以流(stream)的方式进行文件操作。
* std::stringstream 是 C++ 标准库中的一个类，它提供了类似于 std::cout 和 std::cin 的流式 I/O 功能，但允许我们使用字符串作为输入和输出的目标。

```cpp
/** 
@brief 在这个示例中，我们首先创建了一个 std::stringstream 对象 ss。然后，我们使用插入运算符 << 将字符串和换行符写入 ss。接着，我们使用提取运算符 >> 从 ss 中读取数据并将其存储在 message 变量中。最后，我们将结果输出到控制台。
*/
#include <iostream>  
#include <sstream>  
  
int main() {  
    // 创建一个 stringstream 对象  
    std::stringstream ss;  
  
    // 向 stringstream 写入数据  
    ss << "Hello, " << "world!" << std::endl;  
  
    // 从 stringstream 读取数据  
    std::string message;  
    ss >> message;  
  
    // 输出结果  
    std::cout << message << std::endl; // 输出 "Hello, world!"  
  
    return 0;  
}
```

* 下面是一个使用fstream的示例程序，用于从文件中读取数据并将其写入到另一个文件中：
```cpp
/**
@brief 在此示例中，我们首先使用std::ifstream打开一个名为"input.txt"的文件，并检查是否成功打开。然后，我们使用std::ofstream打开一个名为"output.txt"的文件，并检查是否成功打开。接下来，我们使用std::getline函数从输入文件逐行读取数据，并将每行数据写入输出文件。最后，我们关闭输入和输出文件。
*/
#include <iostream>  
#include <fstream>  
#include <string>  
  
int main() {  
    // 打开输入文件  
    std::ifstream inputFile("input.txt");  
    if (!inputFile.is_open()) {  
        std::cerr << "Failed to open input file." << std::endl;  
        return 1;  
    }  
  
    // 打开输出文件  
    std::ofstream outputFile("output.txt");  
    if (!outputFile.is_open()) {  
        std::cerr << "Failed to open output file." << std::endl;  
        return 1;  
    }  
  
    // 从输入文件读取数据并写入输出文件  
    std::string line;  
    while (std::getline(inputFile, line)) {  
        outputFile << line << std::endl;  
    }  
  
    // 关闭文件  
    inputFile.close();  
    outputFile.close();  
  
    return 0;  
}
```
## ```<stdarg.h>``` 
* 是 C 语言中的一个头文件，它包含了处理可变参数的宏和函数。这个头文件主要被用于实现函数重载和宏函数等功能。
stdarg.h 头文件包含了一些宏和函数，允许你在函数调用中使用可变数量的参数。例如，printf 函数就使用了这个头文件来实现其功能。
在大多数 C 语言编译器中，<stdarg.h> 头文件是默认包含的，所以你不需要手动包含它。但如果你使用的编译器不支持这个头文件，或者你想自己手动包含它，你可以在代码中使用 #include <stdarg.h> 来包含这个文件。

* 一个可变参数宏定义的例子
```cpp
#include <stdio.h>  
// __VA_ARGS__是一个预处理器宏，代表了可变数量的参数。
// 在C语言的宏定义中，##是一个特殊符号，被称为“预处理器连接符（Preprocessor Concatenator）”。它的作用是将两个宏参数连接起来。

在宏定义中，如果使用了##，那么它后面的宏参数将被连接起来，形成一个新的宏参数。这个新的宏参数可以在宏体中使用。
#define LOG(fmt, ...) \  
printf("[%s:%d] " fmt "\n", __FILE__, __LINE__, ##__VA_ARGS__)  
  
int main() {  
    LOG("Hello, World!");  
    LOG("Current date and time is: %s", "2023-03-15 10:30:00"); 
    LOG("The value of pi is approximately: %f", 3.1415926535);  
    return 0;  
}
```
## `<cstddef>`
ptrdiff_t是一个有符号整数类型，通常用于表示两个指针之间的差值。它是C和C++标准库中的一种类型，定义在头文件<cstddef>中。ptrdiff_t类型的具体大小取决于实现和平台，但通常是足够大的，以便能够存储任何两个指针的差值。这在进行指针运算和内存管理时非常有用。
```cpp
#include <iostream>  
#include <cstddef>  

int main() {  
    int arr[5] = {1, 2, 3, 4, 5};  
    int* ptr1 = &arr[0];  
    int* ptr2 = &arr[4];  
      
    ptrdiff_t diff = ptr2 - ptr1;  
    std::cout << "Difference between pointers: " << diff << std::endl;  
      
    return 0;  
}
```
在这个示例中，我们定义了一个包含5个整数的数组，并使用两个指针ptr1和ptr2分别指向数组的第一个元素和最后一个元素。我们使用ptrdiff_t类型的变量diff来存储这两个指针之间的差值，并将结果输出到控制台。由于ptr2指向数组的最后一个元素，而ptr1指向第一个元素，因此它们的差值是4（因为数组包含5个元素，所以范围是0到4）。
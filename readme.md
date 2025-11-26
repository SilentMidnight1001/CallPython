# PythonEvent - C++/Python 交互库

## 项目概述

`PythonEvent` 是一个 C++ 与 Python 交互的封装库，提供了简洁的 API 来在 C++ 程序中执行 Python 代码、调用 Python 函数，并在两种语言之间进行数据转换。

## 主要特性

- ✅ **Python 解释器管理** - 安全的初始化和关闭
- ✅ **代码执行** - 执行 Python 代码字符串和文件
- ✅ **函数调用** - 调用 Python 函数并获取返回值
- ✅ **类型转换** - 自动在 C++ 和 Python 类型间转换
- ✅ **多线程安全** - 内置 GIL 管理
- ✅ **模块导入** - 动态导入 Python 模块

## 支持的返回值类型

- `long` - 整型数值
- `double` - 浮点数值
- `string` - 字符串
- `bool` - 布尔值
- `std::vector<T>` - 列表/元组
- `std::map<Key, Value>` - 字典
- `wkp::Dict<Key, Value>` - 自定义字典类型

## 使用方法

### 1. 基本初始化

```cpp
#include "callPython.Hpp"

// 初始化 Python 解释器
PythonEvent::InitPython();

// 你的代码...

// 程序结束时关闭
PythonEvent::ClosePython();
```

### 2. 执行 Python 代码

```cpp
PythonEvent py;

// 执行 Python 代码字符串
string code = R"(
def hello(name):
    return f"Hello, {name}!"
)";
py.callPython(code);

// 执行 Python 文件
string fileContent = py.readPyFile("script.py");
py.callPython(fileContent);
```

### 3. 调用 Python 函数

```cpp
// 调用返回整型的函数
long result = py.callPythonFuncLong("function_name", arg1, arg2);

// 调用返回浮点数的函数  
double result = py.callPythonFuncDouble("function_name", arg1);

// 调用返回字符串的函数
string result = py.callPythonFuncString("function_name");

// 调用返回布尔值的函数
bool result = py.callPythonFuncBool("function_name", arg1, arg2);

// 调用返回列表的函数
std::vector<int> listResult = py.callPythonFuncList<int>("function_name");

// 调用返回字典的函数
std::map<string, int> dictResult = py.callPythonFuncDictToMap<string, int>("function_name");
```

### 4. 导入 Python 模块

```cpp
// 导入标准库或第三方模块
if (py.importPythonModel("numpy")) {
    cout << "成功导入 numpy" << endl;
}

if (py.importPythonModel("math")) {
    cout << "成功导入 math" << endl;
}
```

### 5. 完整示例

```cpp
int main() {
    wkp::useConsoleUtf8();
    PythonEvent py;
    
    // 初始化 Python
    PythonEvent::InitPython();

    try {
        // 定义 Python 函数
        string code = R"(
def calculate_stats(numbers):
    total = sum(numbers)
    average = total / len(numbers)
    return {
        'total': total,
        'average': average,
        'count': len(numbers)
    }
)";
        py.callPython(code);

        // 准备参数
        std::vector<double> numbers = {1.0, 2.0, 3.0, 4.0, 5.0};
        
        // 调用函数并获取字典结果
        auto stats = py.callPythonFuncDictToMap<string, double>("calculate_stats", numbers);
        
        // 输出结果
        for (const auto& [key, value] : stats) {
            println(format("{}: {}", key, value));
        }

    } catch (const std::exception& e) {
        cerr << "错误: " << e.what() << endl;
    }

    // 关闭 Python
    PythonEvent::ClosePython();
    return 0;
}
```

## 构建说明

### 环境要求

- CMake 3.27+
- Python 3.x (开发版)
- MinGW 或 Visual Studio
- WinKeyPress 库

### 构建步骤

1. **配置路径**
   ```cmake
   set(WinKeyPress_DIR "你的/WinKeyPress/路径")
   set(Python3_ROOT_DIR "你的/Python/安装路径")
   ```

2. **构建项目**
   ```bash
   mkdir build
   cd build
   cmake ..
   cmake --build .
   ```

## API 参考

### 静态方法

| 方法 | 说明 |
|------|------|
| `InitPython()` | 初始化 Python 解释器 |
| `ClosePython()` | 关闭 Python 解释器 |
| `getPythonInitFlag()` | 获取初始化状态 |

### 实例方法

| 方法 | 说明 |
|------|------|
| `callPython(code)` | 执行 Python 代码 |
| `readPyFile(filename)` | 读取 Python 文件 |
| `importPythonModel(name)` | 导入 Python 模块 |
| `check_python_error()` | 检查 Python 错误 |

### 模板方法

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `callPythonFuncLong` | `long` | 整型返回值 |
| `callPythonFuncDouble` | `double` | 浮点数返回值 |
| `callPythonFuncString` | `string` | 字符串返回值 |
| `callPythonFuncBool` | `bool` | 布尔返回值 |
| `callPythonFuncList<T>` | `std::vector<T>` | 列表返回值 |
| `callPythonFuncDictToMap<K,V>` | `std::map<K,V>` | 字典转 std::map |
| `callPythonFuncDict<K,V>` | `wkp::Dict<K,V>` | 字典转 wkp::Dict |

## 注意事项

1. **初始化顺序**：必须在调用任何 Python 相关功能前调用 `InitPython()`
2. **资源管理**：程序退出前必须调用 `ClosePython()`
3. **异常处理**：所有函数都可能抛出异常，建议使用 try-catch
4. **线程安全**：在多线程环境中使用时要确保正确的 GIL 管理
5. **类型匹配**：确保 C++ 类型与 Python 返回类型匹配

## 故障排除

### 常见问题

1. **Python 未初始化**
   ```cpp
   if (!PythonEvent::getPythonInitFlag()) {
       PythonEvent::InitPython();
   }
   ```

2. **模块导入失败**
    - 检查 Python 路径配置
    - 确认模块名称正确
    - 检查 Python 环境变量

3. **类型转换错误**
    - 确保 C++ 类型与 Python 返回值匹配
    - 使用合适的模板参数

### 调试技巧

```cpp
// 启用错误检查
py.check_python_error();

// 使用 try-catch 包装
try {
    auto result = py.callPythonFuncString("my_function");
} catch (const std::exception& e) {
    std::cerr << "调用失败: " << e.what() << std::endl;
}
```

这个库为 C++ 与 Python 的混合编程提供了强大而灵活的工具，使得在两个语言之间无缝传递数据和调用函数变得简单高效。
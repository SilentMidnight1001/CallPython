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
- ✅ **变量获取** - 直接获取 Python 全局变量
- ✅ **复杂类型支持** - 支持列表、字典等复杂数据结构的双向转换

## 支持的返回值类型

### 函数返回值
- `long` - 整型数值
- `double` - 浮点数值
- `string` - 字符串
- `bool` - 布尔值
- `std::vector<T>` - 列表/元组
- `std::map<Key, Value>` - 字典
- `wkp::Dict<Key, Value>` - 自定义字典类型

### 变量获取
- `getPythonLongVar()` - 获取整型变量
- `getPythonDoubleVar()` - 获取浮点数变量
- `getPythonStringVar()` - 获取字符串变量
- `getPythonListVar<T>()` - 获取列表变量
- `getPythonDictVar<Key, Value>()` - 获取字典变量（wkp::Dict）
- `getPythonDictVarToMap<Key, Value>()` - 获取字典变量（std::map）

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
    
# 设置全局变量
global_var = 42
name_list = ["Alice", "Bob", "Charlie"]
user_dict = {"id": 1, "name": "John", "active": True}
)";
py.callPython(code);

// 执行 Python 文件
string fileContent = py.readPyFile("script.py");
py.callPython(fileContent);
```

### 3. 获取 Python 变量

```cpp
// 获取基本类型变量
long intVar = py.getPythonLongVar("global_var");
double floatVar = py.getPythonDoubleVar("float_var");  
string strVar = py.getPythonStringVar("str_var");

// 获取列表变量
std::vector<string> nameList = py.getPythonListVar<string>("name_list");
std::vector<int> numberList = py.getPythonListVar<int>("number_list");

// 获取字典变量
auto userDict = py.getPythonDictVarToMap<string, string>("user_dict");
auto configDict = py.getPythonDictVar<string, int>("config_dict");
```

### 4. 调用 Python 函数

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
wkp::Dict<string, double> customDictResult = py.callPythonFuncDict<string, double>("function_name");

// 无返回值函数调用
py.callPythonFunctionNone("function_name", arg1, arg2);
```

### 5. 复杂参数传递

```cpp
// 传递 vector 参数
std::vector<int> numbers = {1, 2, 3, 4, 5};
auto result = py.callPythonFuncList<int>("process_numbers", numbers);

// 传递 map 参数
std::map<string, int> data = {{"a", 1}, {"b", 2}, {"c", 3}};
auto result = py.callPythonFuncDictToMap<string, int>("process_dict", data);

// 传递 wkp::Dict 参数
wkp::Dict<string, double> config;
config["threshold"] = 0.5;
config["max_value"] = 100.0;
auto result = py.callPythonFuncDouble("calculate", config);
```

### 6. 导入 Python 模块

```cpp
// 导入标准库或第三方模块
if (py.importPythonModel("numpy")) {
    cout << "成功导入 numpy" << endl;
}

if (py.importPythonModel("math")) {
    cout << "成功导入 math" << endl;
}

if (py.importPythonModel("pandas")) {
    cout << "成功导入 pandas" << endl;
}
```

### 7. 完整示例

```cpp
int main() {
    wkp::useConsoleUtf8();
    PythonEvent py;
    
    // 初始化 Python
    PythonEvent::InitPython();

    try {
        // 定义 Python 代码
        string code = R"(
# 全局变量
app_name = "Data Processor"
version = 1.2
users = ["Alice", "Bob", "Charlie"]
config = {"debug": True, "max_workers": 4, "timeout": 30.5}

def calculate_stats(numbers):
    total = sum(numbers)
    average = total / len(numbers)
    return {
        'total': total,
        'average': average,
        'count': len(numbers),
        'max': max(numbers),
        'min': min(numbers)
    }

def filter_users(users, min_length=3):
    return [user for user in users if len(user) >= min_length]
)";
        
        // 执行 Python 代码
        py.callPython(code);
        
        // 获取全局变量
        string appName = py.getPythonStringVar("app_name");
        double version = py.getPythonDoubleVar("version");
        auto users = py.getPythonListVar<string>("users");
        auto config = py.getPythonDictVarToMap<string, string>("config");
        
        println("应用名称: " + appName);
        println("版本: " + std::to_string(version));
        
        // 调用函数并传递 vector 参数
        std::vector<double> numbers = {1.5, 2.3, 3.7, 4.1, 5.9};
        auto stats = py.callPythonFuncDictToMap<string, double>("calculate_stats", numbers);
        
        // 输出统计结果
        for (const auto& [key, value] : stats) {
            println(format("{}: {:.2f}", key, value));
        }
        
        // 调用函数并传递字符串列表参数
        auto filteredUsers = py.callPythonFuncList<string>("filter_users", users, 4);
        println("过滤后的用户:");
        for (const auto& user : filteredUsers) {
            println("  - " + user);
        }

    } catch (const std::exception& e) {
        cerr << "错误: " << e.what() << endl;
    }

    // 关闭 Python
    PythonEvent::ClosePython();
    return 0;
}
```

### 8. 字典操作示例

```cpp
int main() {
    wkp::useConsoleUtf8();
    PythonEvent::InitPython();
    PythonEvent py;

    try {
        string code = R"(
# 创建字典示例
user_data = {
    1: "Alice",
    2: "Bob", 
    3: "Charlie"
}

scores = {
    "math": 95.5,
    "english": 88.0,
    "science": 92.3
}

mixed_dict = {
    "id": 1001,
    "name": "John Doe",
    "active": True,
    "score": 85.5
}
)";

        py.callPython(code);
        
        // 获取不同类型的字典
        auto users = py.getPythonDictVarToMap<int, string>("user_data");
        auto studentScores = py.getPythonDictVarToMap<string, double>("scores");
        auto userProfile = py.getPythonDictVar<string, string>("mixed_dict");
        
        // 输出字典内容
        println("用户数据:");
        for (const auto& [id, name] : users) {
            println(format("  {}: {}", id, name));
        }
        
        println("学生成绩:");
        for (const auto& [subject, score] : studentScores) {
            println(format("  {}: {:.1f}", subject, score));
        }

    } catch (const std::exception& e) {
        cerr << "错误: " << e.what() << endl;
    }

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

### 变量获取方法

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `getPythonLongVar(name)` | `long` | 获取整型变量 |
| `getPythonDoubleVar(name)` | `double` | 获取浮点数变量 |
| `getPythonStringVar(name)` | `string` | 获取字符串变量 |
| `getPythonListVar<T>(name)` | `std::vector<T>` | 获取列表变量 |
| `getPythonDictVar<K,V>(name)` | `wkp::Dict<K,V>` | 获取字典变量 |
| `getPythonDictVarToMap<K,V>(name)` | `std::map<K,V>` | 获取字典转 std::map |

### 函数调用模板方法

| 方法 | 返回类型 | 说明 |
|------|----------|------|
| `callPythonFuncLong` | `long` | 整型返回值 |
| `callPythonFuncDouble` | `double` | 浮点数返回值 |
| `callPythonFuncString` | `string` | 字符串返回值 |
| `callPythonFuncBool` | `bool` | 布尔返回值 |
| `callPythonFuncList<T>` | `std::vector<T>` | 列表返回值 |
| `callPythonFuncDictToMap<K,V>` | `std::map<K,V>` | 字典转 std::map |
| `callPythonFuncDict<K,V>` | `wkp::Dict<K,V>` | 字典转 wkp::Dict |
| `callPythonFunctionNone` | `void` | 无返回值调用 |

## 类型转换支持

### C++ → Python
- `int/long` → `PyLong`
- `double/float` → `PyFloat`
- `bool` → `PyBool`
- `std::string` → `PyUnicode`
- `std::vector<T>` → `PyList`
- `std::map<K,V>` → `PyDict`
- `wkp::Dict<K,V>` → `PyDict`

### Python → C++
- `PyLong` → `long/int`
- `PyFloat` → `double`
- `PyBool` → `bool`
- `PyUnicode/PyBytes` → `std::string`
- `PyList/PyTuple` → `std::vector<T>`
- `PyDict` → `std::map<K,V>` / `wkp::Dict<K,V>`

## 注意事项

1. **初始化顺序**：必须在调用任何 Python 相关功能前调用 `InitPython()`
2. **资源管理**：程序退出前必须调用 `ClosePython()`
3. **异常处理**：所有函数都可能抛出异常，建议使用 try-catch
4. **线程安全**：在多线程环境中使用时要确保正确的 GIL 管理
5. **类型匹配**：确保 C++ 类型与 Python 返回类型匹配
6. **内存管理**：Python 对象引用计数由库自动管理

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
   - 检查 Python 变量的实际类型

4. **变量不存在**
   ```cpp
   try {
       auto value = py.getPythonLongVar("non_existent_var");
   } catch (const std::exception& e) {
       cout << "变量不存在: " << e.what() << endl;
   }
   ```

### 调试技巧

```cpp
// 启用错误检查
py.check_python_error();

// 使用 try-catch 包装
try {
    auto result = py.callPythonFuncString("my_function");
    auto variable = py.getPythonListVar<int>("my_list");
} catch (const std::exception& e) {
    std::cerr << "操作失败: " << e.what() << std::endl;
}

// 检查变量是否存在
if (PythonEvent::getPythonInitFlag()) {
    PyGILState_STATE gstate = PyGILState_Ensure();
    PyObject* var = PyDict_GetItemString(PythonEvent::globals, "var_name");
    if (var != nullptr) {
        // 变量存在
    }
    PyGILState_Release(gstate);
}
```

这个库为 C++ 与 Python 的混合编程提供了强大而灵活的工具，使得在两个语言之间无缝传递数据和调用函数变得简单高效。支持从简单的变量获取到复杂的数据结构转换，满足各种交互场景的需求。
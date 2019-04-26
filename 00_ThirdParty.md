# Caffe中使用的几个第三方库
### gflags - 用于解析命令行参数的工具
首先给出一段使用gflags的示例程序:
```C++
#include <iostream>
#include <gflags/gflags.h>

using namespace std;

DEFINE_string(confPath, "../conf/setup.ini", "configure file.");
DEFINE_int32(port, 9090, "program listen port");

int main(int argc, char** argv)
{
    gflags::SetVersionString("1.0.0.0");
    gflags::SetUsageMessage("Usage : ./demo ");
    gflags::ParseCommandLineFlags(&argc, &argv, true);

    cout << "confPath = " << FLAGS_confPath << endl;
    cout << "port = " << FLAGS_port << endl;

    gflags::ShutDownCommandLineFlags();
    return 0;
}
```
而此时命令行中可以输入:
```
./main --confPath=./setup --port=6666
```
1. `DEFINE_DTYPE(arg_name, default_value, "description")`用于设置一个命令行可解析的参数`arg_name`, 命令行中可以使用`--arg_name=value`来为之赋值. 而之后代码中`FLAGS_arg_name`变量中保存的就是这个`value`. 如果未为之赋值,则启用默认值`default_value`, 而此宏函数最后一个参数则是对于这个变量或者说是命令行参数的描述.
2. gflags中额外添加了两个函数`SetVersionString()`, `SetUsageMessage()`分别用于设置代码版本信息和help信息.
3. `ParseCommandLineFlags()`用于解析参数, 而`ShutDownCommandLineFlags()`则表示结束, 一般出现在最后.
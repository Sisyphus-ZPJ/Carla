本文是参照Calar官方文档进行探索学习的记录。

# 关于Carla
Carla是一个开源的自动驾驶模拟器，关于Carla的详细信息可以查看[官方网站](https://carla.org/)，以及Carla的[中文社区](https://www.carla.org.cn/#/)。

Carla的[官方文档](https://carla.readthedocs.io/en/latest/)非常详细，值得深入学习。

# Carla的安装 (Windows build)

## 第一阶段：准备工作

### 系统需求

### 软件需求

#### Minor installations

- [CMake](https://cmake.org/download/) 从简单的 configuration files 生成标准的 build files 。
- [Git](https://git-scm.com/downloads) 是管理 carla 仓库的版本管理系统
- [Make](http://gnuwin32.sourceforge.net/packages/make.htm) 生成可执行文件
- 7Zip
- Python3 x64

## 第二阶段：构建Carla

### 构建Carla

这一章列出了构建Carla的命令

* 所有的命令需要在CARLA的根目录下运行
* 命令必须通过**x64 Native Tools Command Prompt for VS 2019**执行（在Windows搜索栏内搜索x64）

构建Carla有两步，编译 client 和编译 server 。

**1. 编译Python API client**

Python API client 授予对 simulation 的控制权。你需要在第一次构建Carla以及实施任何更新后编译 Python API client。Client 被编译后，你能够通过运行脚本与 simulation 进行交互。

以下命令完成对 Python API client 的编译：
```
make PythonAPI
```

The Carla client library 将以两种不同的、互斥的形式构建。这给予了用户选择何种方式运行 Carla client 代码的自由。两种方式包括 .egg 文件和 .whl 文件。 选择以下其中**一种**方式使用 client library ：

**A. .egg 文件**

.egg 文件不需要被安装。所有的 carla 示例脚本在导入 Carla 时都会自动查找次文件。

如果你之前安装了 .whl 文件，那么 .whl 将会优先于 .egg 被使用。

**B. .whl 文件**

.whl 文件需要使用 pip3 进行安装：
```
pip3 install <path/to/wheel>.whl
```

~~This .whl file cannot be distributed as it is built specifically for your OS.~~

> [!WARNING]
> Issues can arise through the use of different methods to install the CARLA client library and having different versions of CARLA on your system. It is recommended to use virtual environments when installing the .whl and to uninstall any previously installed client libraries before installing new ones.
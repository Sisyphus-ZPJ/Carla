本文是参照Calar官方文档进行探索学习的记录。

# 关于Carla
Carla是一个开源的自动驾驶模拟器，关于Carla的详细信息可以查看[官方网站](https://carla.org/)，以及Carla的[中文社区](https://www.carla.org.cn/#/)。

Carla的[官方文档](https://carla.readthedocs.io/en/latest/)非常详细，值得深入学习。

# Carla的安装 (Windows build)

## 第一阶段：准备工作

### 系统需求

- **x64 系统。**模拟器需要运行在64位Windows系统上。
- **165 GB 硬盘空间。**Carla需要约32GB空间，而相关的主要软件（包括虚幻引擎）需要约133GB空间。
- **足够的GPU。**Carla 旨在实现真实的模拟，所以 Server 需要至少有6GB显存的GPU，尽管推荐配置是8GB。
- **两个TCP端口和良好的网络连接。**默认端口是2000和20001，确保这两个端口没有被防火墙或其他应用阻塞。

### 软件需求

#### Minor installations

- [CMake](https://cmake.org/download/) 从简单的 configuration files 生成标准的 build files 。
- [Git](https://git-scm.com/downloads) 是管理 carla 仓库的版本管理系统
- [Make](http://gnuwin32.sourceforge.net/packages/make.htm) 生成可执行文件
- [7Zip](https://www.7-zip.org/)
- [Python3 x64](https://www.python.org/downloads/)

> [!IMPORTANT]
> 确保以上所有程序被添加到**环境变量**中

#### Python dependencies
```
pip3 -V
```

```
pip3 install --upgrade pip
```

```
pip3 install --user setuptools
pip3 install --user wheel
```

#### Minor installations

##### Visual Studio 2019

从[这里](https://developerinsider.co/download-visual-studio-2019-web-installer-iso-community-professional-enterprise/)获取 Visual Studio 的2019版本。选择 Community 的免费版本。在 Visual Studio Installer 中安装以下附件组件：

- Windows 8.1 SDK
- x64 Visual C++ Toolset
- .Net framework 4.6.2

##### Unreal Engine

从0.9.12版本开始，Carla 使用了 Unreal Engine 4.26的 modified fork。此 fork 包含 Carla 特有的补丁。

请注意，要下载这个虚幻引擎的分支，你需要有一个链接到虚幻引擎帐户的GitHub帐户。如果您没有此设置，请在继续操作之前遵循[本指南](https://www.unrealengine.com/en-US/ue4-on-github)。

请通过以下步骤构建虚幻引擎：

1. 在一个终端内，导航到要保存虚幻引擎的位置，然后克隆 carla 分支：

```
git clone --depth 1 -b carla https://github.com/CarlaUnreal/UnrealEngine.git .
```

2. 运行以下配置脚本：

```
Setup.bat
GenerateProjectFiles.bat
```

3. 编译 modified engine :

    1. 在 Visual Studio 2019 中打开 `UE4.sln` 。
    1. 在 build bar 中确保选中 'Development Editor'，'Win64'和'UnrealBuildTool'。若有疑问请查看[指南](https://docs.unrealengine.com/en-US/ProductionPipelines/DevelopmentSetup/BuildingUnrealEngine/index.html)。
    1. 在 solution explorer 中，右击 `UE4` 并选择 `Build` 。

4. 一旦 solution 被编译，你可以通过运行 `Engine\Binaries\Win64\UE4Editor.exe` 打开引擎，检查是否安装成功。

> [!NOTE]
> 你可以通过虚幻引擎的 version selector 检查安装是否成功，右键`.uproject`文件并选择`Switch Unreal Engine version`，你应该可以看见显示着`Source Build at PATH`的窗口，其中 PATH 是你选择的安装路径。如果上述操作出现偏差，说明虚幻引擎安装有误，你可能需要重新安装。

> [!IMPORTANT]
> 至此已经做了许多准备工作，推荐在继续完成后续步骤前，重启一下你的电脑。

## 第二阶段：构建Carla

### 构建Carla

这一章列出了构建Carla的命令

* 所有的命令需要在CARLA的根目录下运行
* 命令必须通过**x64 Native Tools Command Prompt for VS 2019**执行（在Windows搜索栏内搜索x64）

构建Carla有两步，编译 client 和编译 server 。

**1. 编译 Python API client**

Python API client 授予对 simulation 的控制权。你需要在第一次构建Carla以及实施任何更新后编译 Python API client。Client 被编译后，你能够通过运行脚本与 simulation 进行交互。

以下命令完成对 Python API client 的编译：
```
make PythonAPI
```

The Carla client library 将以两种不同的、互斥的形式构建。这给予了用户选择何种方式运行 Carla client 代码的自由。两种方式包括 .egg 文件和 .whl 文件。 选择以下其中**一种**方式使用 client library ：

**A. .egg 文件**

.egg 文件不需要被安装。所有的 carla 示例脚本在导入 Carla 时都会自动查找次文件。

如果你之前安装了 .whl 文件，那么 .whl 将会优于 .egg 被使用。

**B. .whl 文件**

.whl 文件需要使用 pip3 进行安装：
```
pip3 install <path/to/wheel>.whl
```

~~This .whl file cannot be distributed as it is built specifically for your OS.~~

> [!WARNING]
> Issues can arise through the use of different methods to install the CARLA client library and having different versions of CARLA on your system. It is recommended to use virtual environments when installing the .whl and to uninstall any previously installed client libraries before installing new ones.

**2. 编译 Server**

以下命令编译并启动虚幻引擎，当你需要启动 Server 或使用循环引擎编辑器时，运行这个命令。
```
make launch
```

第一次运行时项目可能会要求构建一些实例如 UE4Editor-Carla.dll ，同意这些要求。在第一次启动时，编辑器可能会显示一些关于 shader 和 mesh distance fields 的警告，这些需要一些时间才能加载，并且在此之前地图将无法正确显示。
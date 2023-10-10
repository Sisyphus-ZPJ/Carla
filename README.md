本文是参照Calar官方文档进行探索学习的记录。

# 关于Carla
Carla是一个开源的自动驾驶模拟器，关于Carla的详细信息可以查看[官方网站](https://carla.org/)，以及Carla的[中文社区](https://www.carla.org.cn/#/)。

Carla的[官方文档](https://carla.readthedocs.io/en/latest/)非常详细，值得深入学习。

# Carla的安装 (Windows build)

本指南详细介绍了如何在Windows系统上从源代码构建CARLA，指南包括两部分：第一部分说明了系统需求和所需安装的软件，第二部分介绍了如何构建和运行CARLA。

构建的过程很长（4小时或更长），涉及多种软件。强烈建议在开始构建前通读本指南。

如果你遇到错误或困难，请查看[F.A.Q.](#https://carla.readthedocs.io/en/latest/build_faq/)页面，该页面提供了部分常见问题的解决方案，或者你可以在 [Carla forum](#https://github.com/carla-simulator/carla/discussions) 上发布你可能存在的任何疑问。

- [第一阶段: 准备工作](#第一阶段准备工作)
    - [系统需求](#系统需求)
    - [软件需求](#软件需求)
        - [Minor installations](#minor-installations) 
        - [Python dependencies](#python-dependencies)
        - [Major installations](#major-installations)
            - [Visual Studio 2019](#visual-studio-2019)
            - [Unreal Engine](#unreal-engine)
- [第二阶段: 构建carla](#第二阶段构建carla)
    - [克隆 Carla 仓库](#克隆-carla-仓库)
    - [获取 assets](#获取-assets)
    - [设置虚幻引擎环境变量](#设置虚幻引擎环境变量)
    - [构建 Carla](#构建-carla)
    - [其他 make 命令](#其他-make-命令)

## 第一阶段：准备工作

在此阶段，你将会找到在你构建 Carla 前需要确认的必备要求，包括系统需求、Minor and major software installations 以及 Python 依赖。

### 系统需求

* **x64 系统**。模拟器需要运行在64位Windows系统上。
* **165 GB 硬盘空间**。Carla需要约32GB空间，而相关的主要软件（包括虚幻引擎）需要约133GB空间。
* **足够的GPU**。Carla 旨在实现真实的模拟，所以 Server 需要至少有6GB显存的GPU，尽管推荐配置是8GB。
* **两个TCP端口和良好的网络连接**。默认端口是2000和20001，确保这两个端口没有被防火墙或其他应用阻塞。

> [!WARNING]
> **如果你从 Carla 0.9.12 更新到 0.9.13**: 你必须更新 UE4 引擎的 Carla fork 到最新版本，关于更新的细节请查看 Unreal Engine 小节。

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
从 Carla 0.9.12 开始，用户可以选择使用 `pip3` 安装 Carla Python API ，需要 20.3 或者更高的版本，使用以下指令检查你是否拥有合适的版本：

```
pip3 -V
```

以下指令可以进行版本更新：

```
pip3 install --upgrade pip
```

你必须安装以下 Python 依赖：

```
pip3 install --user setuptools
pip3 install --user wheel
```

#### Major installations

##### Visual Studio 2019

从[这里](https://developerinsider.co/download-visual-studio-2019-web-installer-iso-community-professional-enterprise/)获取 Visual Studio 的2019版本。选择 Community 的免费版本。在 Visual Studio Installer 中安装以下附件组件：

- **Windows 8.1 SDK**。在 右侧 *Install details* 或者 在 *Indivdual Components* 中的 *SDKs,libraries,and frameworks* 选择该项。
- **x64 Visual C++ Toolset**。在 *Workloads* 中，选择 **Desktop development with C++** ，这将确保用于后续构建的 x64 command prompt 的安装。
- **.Net framework 4.6.2**。在 *Workloads* 中，选择 **.NET desktop development** ，并且在右侧的 *Installation details* 栏，选择 `.NET Framework 4.6.2 development tools` 。这是构建虚幻引擎所必须的。

> [!IMPORTANT]
> 根据作者的实践，发现 Visual Studio Installer 中 VS2019 删除了 winSDK 8.1，可以在[此链接](https://go.microsoft.com/fwlink/p/?LinkId=323507)下载 SDK 8.1 的安装包，下载后直接安装即可。该安装链接来自微软官方发布的[Windows SDK存档](https://developer.microsoft.com/zh-cn/windows/downloads/sdk-archive/)，解决方法参考自[某CSDN博客](https://blog.csdn.net/Septembre_/article/details/111320427)。

> [!IMPORTANT]
> 其他版本的 Visual Studio 可能会引起冲突。请确保将以前的 Visual Studio 完全删除，你可能需要到 `Program Files (x86)\Microsoft Visual Studio\Installer\resources\app\layout` 中，运行 `.\InstallCleanup.exe -full` 以完全清除过去版本留下的信息。

> [!Note]
> 使用 Visual Studio 2022 也是可行的，具体详见[官方文档](#https://carla.readthedocs.io/en/latest/build_windows/)。

##### Unreal Engine

从0.9.12版本开始，Carla 使用了 Unreal Engine 4.26的 modified fork。此 fork 包含 Carla 特有的补丁。

请注意，要下载这个虚幻引擎的分支，你需要有一个链接到虚幻引擎帐户的GitHub帐户。如果您没有此设置，请在继续操作之前遵循[本指南](https://www.unrealengine.com/en-US/ue4-on-github)。

（本人好像没用到这个账户，有无大佬讲解一下）

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
    2. 在 build bar 中确保选中 'Development Editor'，'Win64'和'UnrealBuildTool'。若有疑问请查看[指南](https://docs.unrealengine.com/en-US/ProductionPipelines/DevelopmentSetup/BuildingUnrealEngine/index.html)。
    3. 在 solution explorer 中，右击 `UE4` 并选择 `Build` 。

4. 一旦 solution 被编译，你可以通过运行 `Engine\Binaries\Win64\UE4Editor.exe` 打开引擎，检查是否安装成功。

> [!NOTE]
> 你可以通过虚幻引擎的 version selector 检查安装是否成功，右键`.uproject`文件并选择`Switch Unreal Engine version`，你应该可以看见显示着`Source Build at PATH`的窗口，其中 PATH 是你选择的安装路径。如果上述操作出现偏差，说明虚幻引擎安装有误，你可能需要重新安装。

> [!IMPORTANT]
> 至此已经做了许多准备工作，推荐在继续完成后续步骤前，重启一下你的电脑。

## 第二阶段：构建Carla

### 克隆 Carla 仓库

Carla 仓库地址：[Carla repository](#https://github.com/carla-simulator/carla)

你可以选择从上面地址下载仓库并将其解压至本地，或者运行以下命令：

```
git clone https://github.com/carla-simulator/carla
```

### 获取 assets

在 Carla 的根目录下，通过运行以下命令安装最新的 assets ：

```
Update.bat
```

如果7zip安装正确，Assets 将会被下载并被解压到指定位置，如果你没有安装此软件，你需要手动将文件解压至 `Unreal\CarlaUE4\Content\Carla` 。

下载指定版本 Carla 的 assets :

1. 从 Carla 目录的根路径出发，导航至 `\Util\ContentVersions.txt` ,该文件存储了所有 Carla 版本的 assets 下载地址。
2. 在 `Unreal\CarlaUE4\Content\Carla` 中提取 assets，如果路径不存在，则创建它。
3. 通过类似下列命令的方法提取文件：
```
tar -xvzf <assets_file_name>.tar.gz.tar -C C:\path\to\carla\Unreal\CarlaUE4\Content\Carla
```

### 设置虚幻引擎环境变量

有必要设置一个环境变量，使CARLA可以找到虚幻引擎的安装文件夹，这将允许用户选择要使用的虚幻引擎的版本。如果没有指定环境变量，CARLA将在windows注册表中搜索虚幻引擎，并使用它在那里找到的第一个版本。

设置环境变量的步骤参考：
1. 打开系统属性。
2. 点击 `高级` 并打开 `环境变量` 。
3. 点击 `新建...` 创建变量。
4. 命名变量为 `UE4_ROOT` 并且填入虚幻引擎的安装目录

### 构建 Carla

这一章列出了构建Carla的命令。

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
> 使用不同的方法安装 Carla client library 以及在系统上安装不同版本的 carla 可能会出现问题。建议在安装 .whl 时使用虚拟环境，并在安装新的 client library 之前卸载以前安装的任何 client library 。

**2. 编译 Server**

以下命令编译并启动虚幻引擎，当你需要启动 Server 或使用虚幻引擎编辑器时，运行这个命令。
```
make launch
```

第一次运行时项目可能会要求构建一些实例如 UE4Editor-Carla.dll ，同意这些要求。在第一次启动时，编辑器可能会显示一些关于 shader 和 mesh distance fields 的警告，这些需要一些时间才能加载，并且在此之前地图将无法正确显示。

**3. 开始模拟**

点击 **Play** 以启动模拟，可以通过 `WASDQE` 控制相机。

使用 `PythonAPI\examples` 中的示例脚本测试模拟器，在模拟器运行时，为每个脚本打开一个新窗口，并且运行以下命令生成一些物体，并创建一个 weather cycle :
```python
# Terminal A 
cd PythonAPI\examples
pip3 install -r requirements.txt
python3 generate_traffic.py  

# Terminal B
cd PythonAPI\examples
python3 dynamic_weather.py 
```

> [!NOTE]
> 如果模拟以较低的 FPS 运行，在虚幻引擎内找到 `Edit -> Editor preferences -> Performance` 并且关闭 `Use less CPU when in background` 。

### 其他 make 命令

下列是一些可能用到的 make 指令。


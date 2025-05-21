+++
date = '2025-05-21T15:43:38+08:00'
draft = false
title = 'JSBSim Quickstart'
+++

# JSBSim Quickstart

https://jsbsim-team.github.io/jsbsim-reference-manual/

![JSBSim Logo](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/logo_jsbsim_globe.svg)

JSBSim 是一个轻量级的数据驱动型非线性六自由度（6DoF）批处理仿真应用，旨在建模飞机的飞行动力学与控制。从最早的版本开始，JSBSim 就受益于其成长过程中的开源开发环境，以及众多用户对其持续改进所提出的各种想法。

本在线参考手册是一个社区合作项目，旨在让用户和开发者了解软件的所有功能。

**许可证：** JSBSim 根据 GNU 较宽通用公共许可证（LGPL）授权。



# Acknowledgements

这款软件是许多人多年来共同努力的成果。

Tony Peden 几乎从 JSBSim 的第一天起就开始为其发展做出贡献。他负责初始化和修剪代码。Tony 还将 David Megginson 的属性系统集成到了 JSBSim 中。Tony 来自俄亥俄州立大学，拥有航空与航天工程学位。

David Culp 为 JSBSim 开发了涡轮发动机模型，并设计了多个使用该模型的飞机，包括 T-38。David 拥有多种军用和民用飞机的飞行经验，包括 T-38、波音 707、727、737、757、767、SGS 2-32 和 OV-10。David 是一名航空航天工程师，毕业于美国空军学院。

David Megginson 曾长期参与 FlightGear 的核心开发工作。David 将我们的飞行动力学与他的一般航空飞行经验相结合，以帮助实现最大程度的真实感，除此之外，他还设计了 FlightGear 和 JSBSim 所使用的属性系统。他以对 XML 技术的贡献而闻名，并编写了 FlightGear 和 JSBSim 使用的 easyXML 解析器。

Erik Hofman 做了多方面的工作，包括寻找飞机数据、创建飞行模型（如 F-16），并进行一些编程工作。他还测试了 IRIX 兼容性。Erik 拥有计算机科学学位。

Mathias Frölich 增加了一个多功能的每个起落架的地面高度能力，并做了许多其他贡献。Mathias 是一位来自德国的数学家。

Agostino De Marco 为 JSBSim 创建了一个广泛适用的成本/惩罚修剪分析功能，并曾在那不勒斯大学单独使用 JSBSim 或与 FlightGear 一起使用 JSBSim。

来自英国的 David Luff 提供了最初的活塞发动机模型，Ron Jensen 对其进行了不断的改进。

拥有多年仿真经验的工程师 Lee Duke 和 Bill Galbraith 提出了许多建议和想法，帮助改进了 JSBSim。

来自 NASA 兰利研究中心的 Bruce Jackson，参与了多种仿真系统的开发与应用，长期以来一直给予支持和帮助，他许多年前用 C 语言编写的仿真代码（“LaRCSim”）对 JSBSim 的早期开发具有启发意义。

Curt Olson 协调 FlightGear 及其一些构成部分（SimGear）的开发，多年来在仿真、控制理论和其他许多话题的讨论中给予了极大的帮助。与 FlightGear 社区的合作使 JSBSim 成为一个更好的工具。

最后，用户和开发者社区的努力使 JSBSim 达到了今天的水平。感谢所有曾经花时间报告 bug 或请求新功能的人。



# 前言

JSBSim 于 1996 年构思，作为一个轻量级、数据驱动型、非线性的六自由度（6DoF）批处理仿真应用，旨在建模飞机的飞行动力学与控制。从最早的版本开始，JSBSim 就受益于其成长过程中的开源开发环境，以及众多用户对其持续改进所提出的各种想法。

## 本手册简介

本在线文档分为多个部分。这是因为 JSBSim 可以从不同的角度进行查看：作为飞行器模型开发者的视角，作为将 JSBSim 集成到完整飞行仿真架构中并配有视觉效果的集成者视角，或者作为希望通过添加额外功能来适配或增强 JSBSim 的软件开发者视角。

文档的 [快速入门](https://jsbsim-team.github.io/jsbsim-reference-manual/quickstart/) 部分（第零部分）解释了如何快速开始使用 JSBSim。

接下来的第一部分是 [用户手册](https://jsbsim-team.github.io/jsbsim-reference-manual/user/)，它解释了如何使用 JSBSim 进行仿真运行、创建飞机模型、编写脚本，并执行其他不涉及更改 JSBSim 程序代码的任务。

第二部分是 [程序员手册](https://jsbsim-team.github.io/jsbsim-reference-manual/programmer/)，解释了 JSBSim 的架构——代码是如何组织的，如何工作。

第三部分是 [公式手册](https://jsbsim-team.github.io/jsbsim-reference-manual/formulation/)，其中包含了 JSBSim 中存在的数学模型和算法的描述。

第四部分是一些示例和案例研究，展示了 JSBSim 的使用情况。

## 本文档不包含的内容

本文档不是关于推导运动方程和飞行动力学的详尽参考书。有关此类内容，请参阅 **(Stevens:Lewis:Johnson:2015)** 和 **(Zipfel:2003)**。然而，本文档旨在成为 JSBSim 的权威文档。



# 快速入门

要高效使用 JSBSim，您可能需要采用 *程序员的态度*。这意味着您需要自行下载源代码并在您的平台上进行编译。只要您的计算机上安装了正确的工具，这其实是一个简单的过程。

对于急于使用的人，提供了自动远程构建过程，能够交付最新的库二进制文件。您可以通过以下链接找到这些二进制文件：

## FlightGear 项目开发者提供的构建版本 ([Jenkins 服务器](https://jenkins.io/))

- [Linux 版 JSBSim 构建（Linux CentOS 7 虚拟机）](http://build.flightgear.org:8080/job/JSBsim)

前往工作区 [build.flightgear.org:8080/job/JSBSim/ws](http://build.flightgear.org:8080/job/JSBSim/ws/)，下载所有文件为 [Zip 压缩包](http://build.flightgear.org:8080/job/JSBSim/ws/*zip*/JSBsim.zip)。解压文件，进入 `/JSBSim/build/src/` 文件夹，您会找到：可执行文件 `JSBSim` 和静态库文件 `libJSBSim.a`。

- [Windows 版 JSBSim 构建](http://build.flightgear.org:8080/job/JSBsim-win)

前往工作区 [build.flightgear.org:8080/job/JSBSim-win/ws](http://build.flightgear.org:8080/job/JSBSim-win/ws/)，下载所有文件为 [Zip 压缩包](http://build.flightgear.org:8080/job/JSBSim-win/ws/*zip*/JSBSim-win.zip)。解压文件，进入 `/JSBSim-win/build/src/Debug/` 文件夹，您会找到：可执行文件 `JSBSim.exe` 和静态库文件 `JSBSim.lib`。

提供预编译 JSBSim 二进制文件的工作是 [FlightGear](https://www.flightgear.org/) 项目的 [持续集成与交付服务](http://build.flightgear.org:8080/) 的一部分。如需了解有关 Jenkins 持续集成的更多信息，您可以 [访问此链接](https://wiki.jenkins.io/display/JENKINS/Meet+Jenkins)。

## JSBSim 团队提供的构建版本 ([Travis 服务器](https://travis-ci.org/) 和 [AppVeyor 服务器](https://www.appveyor.com/))

JSBSim 团队提供了自己的持续集成服务，交付适用于 Ubuntu 14.04.5 LTS（Trusty Tahr）和 MS Windows 的 x64 二进制文件。发布版本标记为 `v2018a`（或更高版本），可以从 GitHub 仓库的 [发布区](https://github.com/JSBSim-Team/jsbsim/releases) 下载。

要查看最新构建的当前状态，可以访问以下链接：

- [Ubuntu 版 Travis 构建](https://travis-ci.org/JSBSim-Team/jsbsim)（包括 Python 2.7 和 3.6 的测试） [![Travis CI](https://travis-ci.org/JSBSim-Team/jsbsim.svg?branch=master)](https://travis-ci.org/JSBSim-Team/jsbsim)
- [Windows 版 AppVeyor 构建](https://ci.appveyor.com/project/agodemar/jsbsim/branch/master)（无测试） [![Build status](https://ci.appveyor.com/api/projects/status/89wkiqja63kc6h2v/branch/master?svg=true)](https://ci.appveyor.com/project/agodemar/jsbsim/branch/master)

![Placeholder image](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_sideview_climb_simplified.svg)

那么，您想要模拟这架飞机的飞行吗？

# 获取源代码

JSBSim 的 GitHub 仓库可以通过以下链接访问：[github.com/JSBSim-Team/jsbsim](https://github.com/JSBSim-Team/jsbsim)。该仓库镜像了 SourceForge 上的原始仓库：[sourceforge.net/projects/jsbsim](https://sourceforge.net/projects/jsbsim)。

## 下载源代码所需的工具

您需要安装 [Git 软件](https://git-scm.com/)。Git 是一个 *版本控制软件*，用于记录文件或文件集随时间的变化，以便您可以随时回溯到特定版本。JSBSim 的软件源代码文件是通过 Git 进行版本控制的。

要安装 Git，请 [访问下载页面](https://git-scm.com/downloads)，并选择适合您平台的版本。您可以通过两种方式在本地使用 Git：通过 [GUI 客户端](https://git-scm.com/downloads/guis)，或通过命令行（例如在 Linux 或 Windows 上使用 *Bash shell*）。

安装完 Git 后，假设您将通过命令行使用 Git，您可以从以下两个位置之一 *克隆* JSBSim 的公开源代码仓库。

## 从 [SourceForge](https://sourceforge.net/p/jsbsim/code/ci/master/tree/) 下载

在这种情况下，克隆仓库的 Git 命令是（HTTPS 模式）

```
> git clone https://git.code.sf.net/p/jsbsim/code jsbsim-code
```

或者（SSH 模式）

```
> git clone git://git.code.sf.net/p/jsbsim/code jsbsim-code
```

## 从 [GitHub](https://github.com/JSBSim-Team/jsbsim) 下载

在这种情况下，克隆仓库的 Git 命令是（HTTPS 模式）

```
> git clone https://github.com/JSBSim-Team/jsbsim.git jsbsim-code
```

或者（SSH 模式）

```
> git clone git@github.com:JSBSim-Team/jsbsim.git jsbsim-code
```

------

![Placeholder image](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_airspeeds_recap.svg)

那么，您想要模拟这架飞机的飞行吗？

# 构建程序和库

JSBSim 可以通过 [CMake](https://cmake.org/) 或 [Microsoft Visual Studio](https://www.visualstudio.com/free-developer-offers/) 进行构建。如果您使用的是 Mac OSX 或 Linux 平台，必须使用 CMake。如果您是 Windows 用户，可以选择任意一种工具。

JSBSim 使用标准 C++98/C99 编写，并且没有外部依赖，因此您只需要在您的平台上安装 C/C++ 编译器即可。

## 使用 CMake 构建

CMake 是一个跨平台的构建和测试软件的工具。它可以生成用于使用 GNU make 或 Microsoft Visual Studio 构建 JSBSim 的文件。为了将构建文件与源代码分开，最好在单独的目录中构建 JSBSim。

```
> cd jsbsim-code
> mkdir build
> cd build
```

CMake *不构建* 软件，它生成文件 *供* 多种构建工具使用。以下命令假定您使用 GNU make 来构建 JSBSim。

首先，您应该调用 CMake 然后执行 make

```
> cmake ..
> make
```

这将编译各种类并构建 JSBSim 应用程序，最终文件将位于 `build/src` 目录下。

### 传递给 CMake 的选项

CMake 可以使用多个参数来调整 JSBSim 的构建。以下是不同的选项，您可以根据需要独立使用它们或任意组合。

#### 传递参数给编译器

如果您想设置编译器选项，可以通过传递标志给 CMake 来构建 JSBSim 的 `Debug` 版本。JSBSim 也使用 C 语言编写了一些代码，您可以为 C++ 和 C 编译器设置选项。

```
> cmake -DCMAKE_CXX_FLAGS_DEBUG="-g -Wall" -DCMAKE_C_FLAGS_DEBUG="-g -Wall" -DCMAKE_BUILD_TYPE=Debug ..
> make
```

或者，您也可以构建 JSBSim 的发布版本，并请求 GNU Make 使用 4 核心来加速可执行文件的构建。

```
> cmake -DCMAKE_CXX_FLAGS_RELEASE="-O3 -march=native -mtune=native" -DCMAKE_C_FLAGS_RELEASE="-O3 -march=native -mtune=native" -DCMAKE_BUILD_TYPE=Release ..
> make -j4
```

#### 构建 Expat 或使用系统库

JSBSim 使用 [Expat 库](https://libexpat.github.io/) 来读取 XML 文件。Expat 源代码与 JSBSim 源代码一起提供，并在构建过程中与 JSBSim 一起编译。然而，如果 Expat 已经安装在您的平台上，您可能更倾向于使用系统的 Expat 库，以避免重复。在这种情况下，您应该将 `SYSTEM_EXPAT` 标志传递给 CMake：

```
> cmake -DSYSTEM_EXPAT=ON ..
> make
```

### 构建 JSBSim 的 [Python](https://www.python.org/) 模块

JSBSim 的 Python 模块也可以通过 CMake 来构建。为此，您需要在您的平台上安装 [Cython](http://cython.org/)。CMake 将自动检测到 Cython 并构建 Python 模块。

## 使用 Microsoft Visual Studio 构建

在 Visual Studio 中，您可以打开项目文件 `JSBSim.vcxproj` 来加载 JSBSim 项目。该项目文件将配置 Visual Studio 来构建 JSBSim 可执行文件。

**注意 1：** JSBSim 的官方构建工具是 CMake。Visual Studio 项目文件作为一种便利工具提供，并不保证始终与代码保持同步。

**注意 2：** 从 Visual Studio 2017 开始，Microsoft 已将 CMake 包含在内，因此您应该能够直接从 CMake 文件在 VS2017 中构建 JSBSim。

## 测试 JSBSim

JSBSim 附带了一个测试套件，用于自动检查构建是否正确。该测试套件位于 `tests` 目录中，并使用 Python 编写，因此您需要先构建 [JSBSim 的 Python 模块](https://jsbsim-team.github.io/jsbsim-reference-manual/quickstart/building-the-program/Building%20the%20Python%20module%20of%20JSBSim)。

测试套件可以在 `build` 目录中使用 `ctest` 运行。可以使用 `-j` 选项在多个核心上并行运行测试（例如，以下示例中使用 4 核心）。

```
> ctest -j4
```

## 安装 JSBSim

一旦 JSBSim 被构建和测试完成，您可以将 C++ 头文件和库安装到平台范围内。为此，您可以在 `build` 目录中调用 GNU make：

```
> make install
```

## 安装 Python 模块

如果您除了 C++ 头文件和库外，还计划安装 JSBSim 的 Python 模块，那么必须将 `INSTALL_PYTHON_MODULE` 标志传递给 CMake：

```
> cmake -DINSTALL_PYTHON_MODULE=ON ..
> make
> make install
```

另外，您也可以通过在 `build` 目录中执行以下命令手动安装 Python 模块：

```
> cd tests
> python setup.py install
```

------

![Placeholder image](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_airspeeds_recap.svg)

那么，您想模拟这架飞机的飞行吗？

# 使用 Visual Studio 构建

随着 Visual Studio 2017 开始支持 CMake，现在有两种方法可以使用 Visual Studio 2017 构建 JSBSim 及其各种组件。一种是使用标准的 Visual Studio 项目文件（`*.vcxproj`），用于构建 JSBSim 主程序、Aeromatic++ 等组件，另一种是通过 Visual Studio 使用 CMake 来构建 JSBSim 及其各种组件。

使用 git 检出 JSBSim 源代码。在这些示例中，源代码已检出到：

```
C:\source\JSBSim
```

## 使用 VS 2017 项目文件构建

选择 **文件 → 打开 → 项目/解决方案 …** 菜单选项。

![CMake](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/vs2017_open_project_solution.png)

浏览到 JSBSim 源代码所在的位置，选择根目录下的 `JSBSim.sln` 文件，在本例中是：

```
C:\source\JSBSim\JSBSim.sln
```

项目文件已配置为将编译器和链接器的中间文件以及最终输出文件存储在 JSBSim 源代码树之外的目录中，即存储在 `C:\source\JSBSim\src` 外部。

![CMake](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/vs2017_project_intermediate_output.png)

例如，JSBSim 和 Aeromatic 的中间文件将存储在以下目录中：

```
C:\source\JSBSim\Debug\x64\JSBSim
C:\source\JSBSim\Debug\x64\aeromatic
```

输出文件将位于：

```
C:\source\JSBSim\Debug
```

## 使用 VS 2017 CMake 支持构建

选择 **文件 → 打开 → CMake …** 菜单选项。

![CMake](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/vs2017_open_cmake.png)

浏览到 JSBSim 源代码所在的位置，选择根目录下的 `CMakeLists.txt` 文件，在本例中是：

```
C:\source\JSBSim\CMakeLists.txt
```

共有 4 种构建配置：x86、x64 以及每种版本的 Debug 和 Release。选择您想要构建的配置。

![CMake](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/vs2017_cmake_build_config.png)

然后使用 **CMake** 菜单选项选择您想要构建的组件。

![CMake](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/vs2017_cmake_build_targets.png)

默认情况下，Visual Studio 将配置 CMake 构建到源代码树之外，默认使用用户主目录中的一个构建目录，并且将 GUID（全球唯一标识符）作为目录路径的一部分。您将在 Visual Studio 的输出窗口中看到生成的路径，例如：

```
工作目录：C:\Users\Sean\CMakeBuilds\3f00c6d9-d323-5a32-8a90-665138817fd4\build\x64-Release
```

例如，如果您不想将 CMake 构建文件放在主目录中，可以通过 **CMake → 更改 CMake 设置** 菜单选项生成一个 *CMakeSettings.json* 文件，并编辑 **buildRoot** 和 **installRoot** 属性。

![CMake](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/vs2017_cmake_json_file.png)

最后，Visual Studio 还支持执行 JSBSim 测试。

![CMake](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/vs2017_cmake_tests1.png)

![CMake](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/vs2017_cmake_tests2.png)

# 运行程序

这里所指的 JSBSim 仓库所在的路径将被称为 *`<JSBSim-root-dir>`*。如果您是从源代码构建了 JSBSim，您将在 `<JSBSim-root-dir>/src/` 子目录下找到可执行文件（在 Linux 上为 `JSBSim`，在 Windows 上为 `JSBSim.exe`）。这是 JSBSim *独立应用程序*，您可能希望将其复制到根目录中：

```
<JSBSim-root-dir>$ cp src/JSBSim .
```

运行独立 JSBSim 应用程序时，可能会指定多个选项。

```
<JSBSim-root-dir>$ JSBSim

用法（方括号中的项是可选的）：
  JSBSim [脚本名称] [输出指令文件名称] <选项>
选项：
--help 返回使用信息
--version 返回版本号
--outputlogfile=<文件名> 设置/替换数据日志文件的名称
--logdirectivefile=<文件名> 设置数据日志指令文件的名称
--root=<路径> 设置 JSBSim 根目录（即 `src/` 所在目录）
--aircraft=<文件名> 设置要模拟的飞机名称
--script=<文件名> 指定要运行的脚本
--realtime 指定按实际世界时间运行
--nice 指示 JSBSim 以低 CPU 使用率运行
--suspend 指定在初始化后暂停仿真
--initfile=<文件名> 指定要使用的初始化文件
--catalog 指示 JSBSim 列出该模型的所有属性
  （--catalog 可以与 --aircraft 选项一起在命令行中指定，
   或单独指定，同时指定飞机名称，例如 --catalog=c172）
--end-time=<时间> 指定仿真结束时间（例如，time=20.5）
--property=<name=value> 设置属性的值。
  例如：--property=simulation/integrator/rate/rotational=1

注意：选项后跟文件名时，等号两边不能有空格
```

您可以通过提供脚本名称来运行 JSBSim：

```
<JSBSim-root-dir>$ JSBSim --script=scripts/c1723.xml
```

TODO

完善页面内容。

------

![Placeholder image](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_euler_gimbal.svg)

那么，您想模拟这架飞机的飞行吗？

# 获取支持

获取 JSBSim 支持的最佳方式是注册 GitHub 账户并 *关注* JSBSim 仓库：[github.com/JSBSim-Team/jsbsim](https://github.com/JSBSim-Team/jsbsim)。您可以订阅单独的对话，包括问题（issues）、拉取请求（pull requests）和团队讨论，即使您没有关注该仓库或不是讨论所在团队的成员。如果您不再对某个对话感兴趣，可以随时取消订阅未来的通知。

要了解更多信息，请阅读[此指南](https://help.github.com/articles/subscribing-to-and-unsubscribing-from-notifications/)。
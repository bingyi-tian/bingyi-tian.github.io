+++
date = '2025-11-16T15:01:36+08:00'
draft = false
title = 'Cgal配置教程'
tags = ["",""]
categories = ""
author = "bingyi-tian"
description = ""

+++



先安装ceres:

基于官方文档，我为你推荐**最简单实用的安装方案**：

## 方案1：使用vcpkg一键安装（强烈推荐）

这是最省心的方法，适合99%的用户：

### 步骤1：安装vcpkg

```
# 在合适的位置（如D盘根目录）打开PowerShell
git clone https://github.com/Microsoft/vcpkg.git
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

### 步骤2：安装Ceres及所有依赖

```
# 安装基础版本（推荐）
.\vcpkg.exe install ceres:x64-windows

# 或者安装包含SuiteSparse的完整版本
.\vcpkg.exe install ceres[suitesparse]:x64-windows
```

### 步骤3：配置你的CGAL项目

```
cd 你的项目目录
cmake -DCMAKE_BUILD_TYPE=Release ^
      -DCGAL_DIR="D:\VisualStudio\Source\cgal\cgal\cgal" ^
      -DCMAKE_TOOLCHAIN_FILE="D:\vcpkg\scripts\buildsystems\vcpkg.cmake" ^
      ../..
```

## 方案2：手动编译（仅限特殊情况）

只有在需要特定版本或自定义配置时才用此方法：

### 步骤1：安装依赖

```
# 先用vcpkg安装依赖（这样最简单）
.\vcpkg.exe install eigen3 glog gflags:x64-windows
```

### 步骤2：编译Ceres

```
git clone https://github.com/ceres-solver/ceres-solver.git
cd ceres-solver
mkdir build
cd build

# 配置（替换为你的实际路径）
cmake .. ^
    -DCMAKE_INSTALL_PREFIX="D:\Libraries\ceres" ^
    -DEigen3_DIR="D:\vcpkg\installed\x64-windows\share\eigen3" ^
    -Dglog_DIR="D:\vcpkg\installed\x64-windows\share\glog" ^
    -Dgflags_DIR="D:\vcpkg\installed\x64-windows\share\gflags" ^
    -G "Visual Studio 17 2022" -A x64

cmake --build . --config Release
cmake --install . --config Release
```

## 验证安装

安装成功后，重新配置项目应该看到：

```
-- Found Ceres: ... (found version 2.1.0)
-- Examples that use the Ceres library will be compiled.
```

## 重要建议

1. 1.

   **首选vcpkg**：避免手动处理依赖关系的噩梦

2. 2.

   **使用Release模式**：Debug模式性能差，且CGAL会警告

3. 3.

   **集成到VS**：运行 `vcpkg integrate install`让Visual Studio自动找到库

4. 4.

   **64位系统**：始终使用 `x64-windows`架构

## 如果遇到问题

```
# 清理重装
vcpkg remove ceres
vcpkg install ceres:x64-windows

# 更新vcpkg
git pull
.\bootstrap-vcpkg.bat
```

**总结**：直接使用方案1的vcpkg方法，这是最可靠且官方推荐的方式。手动编译只有在极端特殊需求时才需要考虑。



进入到example中，cmake命令：

```
cmake -DCMAKE_BUILD_TYPE=Debug       -DCGAL_DIR="D:\VisualStudio\Source\cgal\cgal\cgal"       -DCMAKE_TOOLCHAIN_FILE="D:\VisualStudio\Source\cgal\Third_Party\vcpkg\vcpkg\scripts\buildsystems\vcpkg.cmake"       -DEigen3_DIR="D:\VisualStudio\Source\cgal\Third_Party\eigen3\eigen3"       ../..
```



如果想要只生成一个example中的一个解决方案的解决方案，就把他设置成启动项，然后右键生成



然后报错找不到eigen3里面的头文件：

![image-20251117102726361](C:\Users\Administrator\Desktop\blog\bingyi-tian.github.io\content\post\image\cgal配置教程\image-20251117102726361.png)



## 使用vcpkg安装Eigen

由于您使用了vcpkg，确保Eigen已安装：

```
cd "D:\VisualStudio\Source\cgal\Third_Party\vcpkg\vcpkg"
.\vcpkg install eigen3
```







然后重新编译cmake

```
cmake -DCMAKE_BUILD_TYPE=Debug       -DCGAL_DIR="D:\VisualStudio\Source\cgal\cgal\cgal"       -DCMAKE_TOOLCHAIN_FILE="D:\VisualStudio\Source\cgal\Third_Party\vcpkg\vcpkg\scripts\buildsystems\vcpkg.cmake"         ../..
```



编译成功，但是没有解决这个问题，这个问题是路径问题：

修正方法：

### 修改CMakeLists.txt（推荐）

在你的CMakeLists.txt中添加Eigen3的头文件路径：

```
# 在find_package(Eigen3)之后添加
if(Eigen3_FOUND)
    # 添加Eigen3的头文件路径
    include_directories(${EIGEN3_INCLUDE_DIR})
    
    # 或者如果你使用target的方式
    # target_include_directories(your_target_name PRIVATE ${EIGEN3_INCLUDE_DIR})
endif()
```

实际cmake代码：

```
find_package(Eigen3 3.2.0 QUIET) #(requires 3.2.0 or greater)
message(STATUS "Eigen3_FOUND: ${Eigen3_FOUND}")
include_directories(${EIGEN3_INCLUDE_DIR})
message(STATUS "EIGEN3_INCLUDE_DIR: ${EIGEN3_INCLUDE_DIR}")
include(CGAL_Eigen3_support)
if(TARGET CGAL::Eigen3_support)
```

成功！

以后编译的话，多用vcpkg下载包，下载之后，cmake的时候指定工具链

切记最后改一下cmakelists，包含一下文件的头文件





# 日志部分

ceres真实安装日志：

```
Windows PowerShell
版权所有（C） Microsoft Corporation。保留所有权利。

安装最新的 PowerShell，了解新功能和改进！https://aka.ms/PSWindows

加载个人及系统配置文件用了 510 毫秒。
(base) PS D:\VisualStudio\Source\cgal\Third_Party\vcpkg> git clone https://github.com/Microsoft/vcpkg.git
Cloning into 'vcpkg'...
remote: Enumerating objects: 293281, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 293281 (delta 0), reused 0 (delta 0), pack-reused 293277 (from 2)
Receiving objects: 100% (293281/293281), 91.67 MiB | 4.86 MiB/s, done.
Resolving deltas: 100% (196576/196576), done.
Updating files: 100% (13090/13090), done.
(base) PS D:\VisualStudio\Source\cgal\Third_Party\vcpkg> cd vcpkg
(base) PS D:\VisualStudio\Source\cgal\Third_Party\vcpkg\vcpkg> .\bootstrap-vcpkg.bat
Downloading https://github.com/microsoft/vcpkg-tool/releases/download/2025-10-16/vcpkg.exe -> D:\VisualStudio\Source\cgal\Third_Party\vcpkg\vcpkg\vcpkg.exe (using IE proxy: 127.0.0.1:7897)... done.
Validating signature... done.

vcpkg package management program version 2025-10-16-71538f2694db93da4668782d094768ba74c45991

See LICENSE.txt for license information.
Telemetry
---------
vcpkg collects usage data in order to help us improve your experience.
The data collected by Microsoft is anonymous.
You can opt-out of telemetry by re-running the bootstrap-vcpkg script with -disableMetrics,
passing --disable-metrics to vcpkg on the command line,
or by setting the VCPKG_DISABLE_METRICS environment variable.

Read more about vcpkg telemetry at docs/about/privacy.md
(base) PS D:\VisualStudio\Source\cgal\Third_Party\vcpkg\vcpkg> .\vcpkg integrate install
Applied user-wide integration for this vcpkg root.
CMake projects should use: "-DCMAKE_TOOLCHAIN_FILE=D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/scripts/buildsystems/vcpkg.cmake"

All MSBuild C++ projects can now #include any installed libraries. Linking will be handled automatically. Installing new libraries will make them instantly available.
(base) PS D:\VisualStudio\Source\cgal\Third_Party\vcpkg\vcpkg> .\vcpkg.exe install ceres:x64-windows
Computing installation plan...
The following packages will be built and installed:
    ceres:x64-windows@2.2.0#5
  * eigen3:x64-windows@3.4.1#1
  * gflags:x64-windows@2.2.2#9
  * glog:x64-windows@0.7.1
  * vcpkg-cmake:x64-windows@2024-04-23
  * vcpkg-cmake-config:x64-windows@2024-05-23
Additional packages (*) will be modified to complete this operation.
Detecting compiler hash for triplet x64-windows...
-- Automatically setting %HTTP(S)_PROXY% environment variables to "127.0.0.1:7897".
Compiler found: D:/VisualStudio/VisualStudio/2022/community/VC/Tools/MSVC/14.44.35207/bin/Hostx64/x64/cl.exe
A suitable version of 7zip was not found (required v25.1.0).
Downloading https://github.com/ip7z/7zip/releases/download/25.01/7z2501.exe -> 7z2501.7z.exe
Successfully downloaded 7z2501.7z.exe
Extracting 7zip...
A suitable version of 7zr was not found (required v25.1.0).
Downloading https://github.com/ip7z/7zip/releases/download/25.01/7zr.exe -> 7d84fcad-7zr.exe
Successfully downloaded 7d84fcad-7zr.exe
Restored 0 package(s) from C:\Users\Administrator\AppData\Local\vcpkg\archives in 87.3 us. Use --debug to see more details.
Installing 1/6 vcpkg-cmake:x64-windows@2024-04-23...
Building vcpkg-cmake:x64-windows@2024-04-23...
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/vcpkg-cmake_x64-windows/share/vcpkg-cmake/vcpkg_cmake_configure.cmake
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/vcpkg-cmake_x64-windows/share/vcpkg-cmake/vcpkg_cmake_build.cmake
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/vcpkg-cmake_x64-windows/share/vcpkg-cmake/vcpkg_cmake_install.cmake
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/vcpkg-cmake_x64-windows/share/vcpkg-cmake/vcpkg-port-config.cmake
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/vcpkg-cmake_x64-windows/share/vcpkg-cmake/copyright
-- Performing post-build validation
Starting submission of vcpkg-cmake:x64-windows@2024-04-23 to 1 binary cache(s) in the background
Elapsed time to handle vcpkg-cmake:x64-windows: 41 ms
vcpkg-cmake:x64-windows package ABI: ade78fca9f99770eea7e6b6190d64a9740445ad0ee86eb0e1d78d40ce11af42c
Installing 2/6 vcpkg-cmake-config:x64-windows@2024-05-23...
Building vcpkg-cmake-config:x64-windows@2024-05-23...
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/vcpkg-cmake-config_x64-windows/share/vcpkg-cmake-config/vcpkg_cmake_config_fixup.cmake
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/vcpkg-cmake-config_x64-windows/share/vcpkg-cmake-config/vcpkg-port-config.cmake
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/vcpkg-cmake-config_x64-windows/share/vcpkg-cmake-config/copyright
-- Skipping post-build validation due to VCPKG_POLICY_EMPTY_PACKAGE
Starting submission of vcpkg-cmake-config:x64-windows@2024-05-23 to 1 binary cache(s) in the background
Elapsed time to handle vcpkg-cmake-config:x64-windows: 34.8 ms
vcpkg-cmake-config:x64-windows package ABI: d0d523f423055780d14dd2f12dab989b51d5294c3bb7e07f5c220a6266a95b6e
Installing 3/6 eigen3:x64-windows@3.4.1#1...
Building eigen3:x64-windows@3.4.1#1...
CMake Warning at scripts/cmake/vcpkg_buildpath_length_warning.cmake:4 (message):
  eigen3's buildsystem uses very long paths and may fail on your system.

  We recommend moving vcpkg to a short path such as 'C:\src\vcpkg' or using
  the subst command.
Call Stack (most recent call first):
  ports/eigen3/portfile.cmake:1 (vcpkg_buildpath_length_warning)
  scripts/ports.cmake:206 (include)


Downloading https://gitlab.com/libeigen/eigen/-/archive/3.4.1/eigen-3.4.1.tar.gz -> libeigen-eigen-3.4.1.tar.gz
Successfully downloaded libeigen-eigen-3.4.1.tar.gz
-- Extracting source D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/downloads/libeigen-eigen-3.4.1.tar.gz
-- Using source at D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/buildtrees/eigen3/src/3.4.1-0ff0cf177d.clean
-- Configuring x64-windows
-- Building x64-windows-dbg
-- Building x64-windows-rel
-- Fixing pkgconfig file: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/eigen3_x64-windows/lib/pkgconfig/eigen3.pc
Downloading msys2-mingw-w64-x86_64-pkgconf-1~2.4.3-1-any.pkg.tar.zst, trying https://mirror.msys2.org/mingw/mingw64/mingw-w64-x86_64-pkgconf-1~2.4.3-1-any.pkg.tar.zst
Successfully downloaded msys2-mingw-w64-x86_64-pkgconf-1~2.4.3-1-any.pkg.tar.zst
Downloading msys2-msys2-runtime-3.6.2-2-x86_64.pkg.tar.zst, trying https://mirror.msys2.org/msys/x86_64/msys2-runtime-3.6.2-2-x86_64.pkg.tar.zst
Successfully downloaded msys2-msys2-runtime-3.6.2-2-x86_64.pkg.tar.zst
-- Using msys root at D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/downloads/tools/msys2/9272adbcaf19caef
-- Fixing pkgconfig file: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/eigen3_x64-windows/debug/lib/pkgconfig/eigen3.pc
-- Performing post-build validation
Starting submission of eigen3:x64-windows@3.4.1#1 to 1 binary cache(s) in the background
Elapsed time to handle eigen3:x64-windows: 17 s
eigen3:x64-windows package ABI: 919fec9c262ae7a61748b5a452ea279dd4ee673d6bc3da3ed85229e316e0992d
Completed submission of vcpkg-cmake:x64-windows@2024-04-23 to 1 binary cache(s) in 79.7 ms
Completed submission of vcpkg-cmake-config:x64-windows@2024-05-23 to 1 binary cache(s) in 24.1 ms
Installing 4/6 gflags:x64-windows@2.2.2#9...
Building gflags:x64-windows@2.2.2#9...
Downloading https://github.com/gflags/gflags/archive/v2.2.2.tar.gz -> gflags-gflags-v2.2.2.tar.gz
Successfully downloaded gflags-gflags-v2.2.2.tar.gz
-- Extracting source D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/downloads/gflags-gflags-v2.2.2.tar.gz
-- Applying patch 0001-patch-dir.patch
-- Applying patch fix_cmake_config.patch
-- Using source at D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/buildtrees/gflags/src/v2.2.2-d9283b9285.clean
-- Configuring x64-windows
-- Building x64-windows-dbg
-- Building x64-windows-rel
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/gflags_x64-windows/share/gflags/vcpkg-cmake-wrapper.cmake
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/gflags_x64-windows/share/gflags/usage
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/gflags_x64-windows/share/gflags/copyright
-- Performing post-build validation
Starting submission of gflags:x64-windows@2.2.2#9 to 1 binary cache(s) in the background
Elapsed time to handle gflags:x64-windows: 10 s
gflags:x64-windows package ABI: 8459c63a57095ebb7714e4151adb13fc5488963ed2d42c1767234642fd3cab2f
Completed submission of eigen3:x64-windows@3.4.1#1 to 1 binary cache(s) in 220 ms
Installing 5/6 glog:x64-windows@0.7.1...
Building glog:x64-windows@0.7.1...
Downloading https://github.com/google/glog/archive/v0.7.1.tar.gz -> google-glog-v0.7.1.tar.gz
Successfully downloaded google-glog-v0.7.1.tar.gz
-- Extracting source D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/downloads/google-glog-v0.7.1.tar.gz
-- Applying patch fix_glog_CMAKE_MODULE_PATH.patch
-- Applying patch glog_disable_debug_postfix.patch
-- Applying patch fix_crosscompile_symbolize.patch
-- Applying patch fix_cplusplus_macro.patch
-- Using source at D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/buildtrees/glog/src/v0.7.1-795557b621.clean
-- Configuring x64-windows
CMake Warning at installed/x64-windows/share/vcpkg-cmake/vcpkg_cmake_configure.cmake:344 (message):
  The following variables are not used in CMakeLists.txt:

      WITH_CUSTOM_PREFIX

  Please recheck them and remove the unnecessary options from the
  `vcpkg_cmake_configure` call.

  If these options should still be passed for whatever reason, please use the
  `MAYBE_UNUSED_VARIABLES` argument.
Call Stack (most recent call first):
  ports/glog/portfile.cmake:24 (vcpkg_cmake_configure)
  scripts/ports.cmake:206 (include)


-- Building x64-windows-dbg
-- Building x64-windows-rel
-- Using cached msys2-mingw-w64-x86_64-pkgconf-1~2.4.3-1-any.pkg.tar.zst
-- Using cached msys2-msys2-runtime-3.6.2-2-x86_64.pkg.tar.zst
-- Using msys root at D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/downloads/tools/msys2/9272adbcaf19caef
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/glog_x64-windows/share/glog/copyright
-- Performing post-build validation
Starting submission of glog:x64-windows@0.7.1 to 1 binary cache(s) in the background
Elapsed time to handle glog:x64-windows: 14 s
glog:x64-windows package ABI: 0434af1267a67b3d1a2be1aee13958681a408f9e72ea76955b5936b313c6cade
Completed submission of gflags:x64-windows@2.2.2#9 to 1 binary cache(s) in 423 ms
Installing 6/6 ceres:x64-windows@2.2.0#5...
Building ceres:x64-windows@2.2.0#5...
Downloading https://github.com/ceres-solver/ceres-solver/archive/85331393dc0dff09f6fb9903ab0c4bfa3e134b01.tar.gz -> ceres-solver-ceres-solver-85331393dc0dff09f6fb9903ab0c4bfa3e134b01.tar.gz
Successfully downloaded ceres-solver-ceres-solver-85331393dc0dff09f6fb9903ab0c4bfa3e134b01.tar.gz
-- Extracting source D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/downloads/ceres-solver-ceres-solver-85331393dc0dff09f6fb9903ab0c4bfa3e134b01.tar.gz
-- Applying patch 0001_cmakelists_fixes.patch
-- Applying patch 0004_remove_broken_fake_ba_jac.patch
-- Applying patch 0005_link_cuda_static.patch
-- Applying patch 0006_fix_cuda_architectures.patch
-- Applying patch 0007_support_cuda_13.patch
-- Using source at D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/buildtrees/ceres/src/fa3e134b01-7e5d31decb.clean
-- Configuring x64-windows
-- Building x64-windows-dbg
-- Building x64-windows-rel
-- Installing: D:/VisualStudio/Source/cgal/Third_Party/vcpkg/vcpkg/packages/ceres_x64-windows/share/ceres/copyright
-- Performing post-build validation
Starting submission of ceres:x64-windows@2.2.0#5 to 1 binary cache(s) in the background
Elapsed time to handle ceres:x64-windows: 1.7 min
ceres:x64-windows package ABI: 50716300293dde0c49effe9da3d02796ffa0a7e23609e30caaac36da43f75986
Total install time: 2.4 min
Installed contents are licensed to you by owners. Microsoft is not responsible for, nor does it grant any licenses to, third-party packages.
Packages installed in this vcpkg installation declare the following licenses:
Apache-2.0
BSD-3-Clause
MIT
MPL-2.0
ceres provides CMake targets:

  # this is heuristically generated, and may not be correct
  find_package(Ceres CONFIG REQUIRED)
  target_link_libraries(main PRIVATE Ceres::ceres)

Completed submission of glog:x64-windows@0.7.1 to 1 binary cache(s) in 493 ms
Waiting for 1 remaining binary cache submissions...
Completed submission of ceres:x64-windows@2.2.0#5 to 1 binary cache(s) in 17 s (1/1)
All requested installations completed successfully in: 2.4 min
(base) PS D:\VisualStudio\Source\cgal\Third_Party\vcpkg\vcpkg>
```


# Chromium Compile Guide

***Ver 2.0***

***Author: Hank***

***请参考官方文档：[windows_build_instructions](https://chromium.googlesource.com/chromium/src/+/main/docs/windows_build_instructions.md)***

## Change Log

v1.0 add some description details

*v2.0 源码构建方式发生较大变化，请以官方文档为主！*

## i. Compile Environment

1. IDE

    Visual Studio 2022

    1. ***使用 C++ 桌面开发***

        Vusual Studio C++ 必须模块

    2. ***Windows 11 SDK***

        在其他组件中找到对应工具集勾选安装

    3. ***Debugging Tools for Windows***

        This version of the Debugging tools is needed in order to support reading the large-page PDBs that Chrome uses to allow greater-than 4 GiB PDBs.

        If you don't know how to install, check [this](https://stackoverflow.com/a/66755402/11345052)

2. VPN

    从仓库fetch & update第三方库源码，这些墙外三方库需要梯子，这里自备不做详述，直接跳到代理设置。

    ``` bash
    git config --global http.proxy http://localhost:port
    git config --global https.proxy http://localhost:port

    # 如果将来要取消代理
    git config --global --unset http.proxy
    git config --global --unset https.proxy
    ```

3. INSTALL depot_tools

    Please focus on this [tutorial](https://www.chromium.org/developers/how-tos/install-depot-tools/)

    [Remind to check that your environment variables are **properly** settled](https://chromium.googlesource.com/chromium/src/+/main/docs/windows_build_instructions.md#install)

    and then execute:

    ```bash
    gclient
    ```

4. Enviroment Variable

    设置临时或者永久的环境变量，反正之后每次操作都会用到对应的变量

    ``` bash
    set DEPOT_TOOLS_WIN_TOOLCHAIN=0
    set http_proxy="http://127.0.0.1:7890"
    set https_proxy="https://127.0.0.1:7890"
    ```

    ***注意***：这里可以使用powershell，但是后面有些命令不符合powershell语法执行会出错，笔者不会改，所以在这里和之后默认统一使用cmd作为cli终端

## ii. Source Code Synchronization

新建文件夹

```c++
mkdir chromium && cd chromium
```

***在 chromium 目录下运行命令行终端***

``` bash
fetch chromium --no-history --depth=1
gclient sync
```

***注意***： 这中途如果终端关闭，重新启动后之前的环境变量会失效，需要再次遵循以上步骤设置必要的环境变量，下文同理，不再解释

## iii. Project Generation

***在 chromium/src 目录下运行命令行终端***

``` bash
# windows platform
# dir: out/Debug_x64
gn gen out/Debug_x64 --ide=vs2019 --args="target_os=\"win\" target_cpu=\"x64\" is_component_build=true is_debug=true is_official_build=false proprietary_codecs=true media_use_ffmpeg=true ffmpeg_branding=\"Chrome\" enable_nacl=false enable_widevine=true"

# dir: out/Debug_x64_mini
# 生成精简调试版本，如果带--no-dep参数只会包含chrome工程，否则all.sln项目里只有chrome工程以及依赖工程
gn gen out/Debug_x64_mini --ide=vs2019 --filters=//chrome --no-dep --args="target_os=\"win\" target_cpu=\"x64\" is_component_build=true is_debug=true is_official_build=false proprietary_codecs=true media_use_ffmpeg=true ffmpeg_branding=\"Chrome\" enable_nacl=false enable_widevine=true"

# dir: out/Release_x64
gn gen out/Release_x64 --ide=vs2019 --args="target_os=\"win\" target_cpu=\"x64\" is_component_build=false is_debug=false is_official_build=true proprietary_codecs=true media_use_ffmpeg=true ffmpeg_branding=\"Chrome\" enable_nacl=false enable_widevine=true"

# macOS platform
# dir: out/Debug_x64
gn gen out/Debug_x64 --ide=xcode --args="is_component_build=true is_debug=true is_official_build=false proprietary_codecs=true media_use_ffmpeg=true ffmpeg_branding=\"Chrome\" target_cpu =\"arm64\" enable_nacl=false "

# dir: out/arm_x64
gn gen out/arm_x64 --ide=xcode --args="is_component_build=false is_debug=false is_official_build=true proprietary_codecs=true media_use_ffmpeg=true ffmpeg_branding=\"Chrome\" target_cpu =\"arm64\" enable_nacl=false "

# dir: out/Release_x64
gn gen out/Release_x64 --ide=xcode --args="is_component_build=false target_cpu=\"x64\"  is_debug=false is_official_build=true proprietary_codecs=true media_use_ffmpeg=true ffmpeg_branding=\"Chrome\" enable_nacl=false "
```

## iv. Compile

打开 out/xxxx（你的解决方案生成路径）下的 `args.gn`，追加如下：

``` bash
google_api_key = "AIzaSyDVg1wMsDLUbO07bLbSJ-FZOu26VcZ0x8w"
google_default_client_id = "1062540499206-grf7pu0s12rdtrridioirvt73mrooenj.apps.googleusercontent.com"
google_default_client_secret = "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"
```

***在 chromium/src 目录下运行 `vs` 命令行终端(Develop Command prompt for VS 2022)***

``` bash
ninja -C out\Debug_x64_mini chrome # your project dir
ninja -C out\Release_x64 chrome # your project dir
```

## v. Pack and Release

***在 chromium/src 目录下运行命令行终端***

使用以下命令编译生成安装包，生成的安装包名称为`mini_installer.exe`, 存储在`out`目录下。

``` bash
ninja -C out\Debug_x64_mini mini_installer
ninja -C out\Release_x64 mini_installer
```

## vi. Resigned

解包重签名

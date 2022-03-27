# 阿里云OSS C++工具套件

阿里云对象存储（Object Storage Service，简称OSS），是阿里云对外提供的海量、安全、低成本、高可靠的云存储服务。用户可以通过调用API，在任何应用、任何时间、任何地点上传和下载数据，也可以通过用户Web控制台对数据进行简单的管理。OSS适合存放任意文件类型，适合各种网站、开发企业及开发者使用。

适用于阿里云OSS的 C++ SDK提供了一组现代化的 C++（C++ 11）接口,让您不用复杂编程即可访问阿里云OSS服务。

如果您在使用SDK的过程中遇到任何问题，欢迎前往阿里云SDK问答社区提问，提问前请阅读提问引导。亦可在当前GitHub提交Issues。

完成本文档中的操作开始使用 C++ SDK。


## 前提条件

在使用 C++ SDK 前，确保您已经：

* 注册了阿里云账号并获取了访问密钥（AccessKey）。

> **说明：** 为了保证您的账号安全，建议您使用RAM账号来访问阿里云服务。阿里云账号对拥有的资源有全部权限。RAM账号由阿里云账号授权创建，仅有对特定资源限定的操作权限。详情[参见RAM](https://help.aliyun.com/document_detail/28647.html)。


* 安装支持 C++ 11 或更高版本的编译器：
	* Visual Studio 2013 或以上版本
	* 或 GCC 4.8 或以上版本
	* 或 Clang 3.3 或以上版本

## 从源代码构建 SDK

1. 从 GitHub 下载或 Git 克隆 [aliyun-oss-cpp-sdk](https://github.com/aliyun/aliyun-oss-cpp-sdk)

* 直接下载 https://github.com/aliyun/aliyun-oss-cpp-sdk/archive/master.zip
* 使用 Git 命令获取

```
git clone https://github.com/aliyun/aliyun-oss-cpp-sdk.git
```

2. 安装 cmake 3.1 或以上版本，进入 SDK 创建生成必要的构建文件

```
cd <path/to/aliyun-oss-cpp-sdk>
mkdir build
cd build
cmake ..
```

### Windows

进入 build 目录使用 Visual Studio 打开 alibabacloud-oss-cpp-sdk.sln 生成解决方案。

或者您也可以使用 VS 的开发人员命令提示符，执行以下命令编译并安装：

```
msbuild ALL_BUILD.vcxproj
msbuild INSTALL.vcxproj
```

### Linux

要在 Linux 平台进行编译, 您必须安装依赖的外部库文件 libcurl、libopenssl, 通常情况下，系统的包管理器中的会有提供。

例如：在基于 Redhat / Fedora 的系统上安装这些软件包

```
sudo dnf install libcurl-devel openssl-devel
```
例如：在基于 Debian / Ubuntu 的系统上安装这些软件包
```
sudo apt-get install libcurl4-openssl-dev libssl-dev
```

在安装依赖库后执行以下命令编译并安装：

```
make
sudo make install
```

### Mac
在Mac平台编译时，需要指定openssl 库的路径。例如 openssl安装在 /usr/local/Cellar/openssl/1.0.2p, 请使用如下命令
```
cmake -DOPENSSL_ROOT_DIR=/usr/local/Cellar/openssl/1.0.2p  \
      -DOPENSSL_LIBRARIES=/usr/local/Cellar/openssl/1.0.2p/lib  \
      -DOPENSSL_INCLUDE_DIRS=/usr/local/Cellar/openssl/1.0.2p/include/ ..
make
```

### Android
例如，在linux 环境下，基于android-ndk-r16 工具链构建工程。可以先把第三方库 `libcurl` 和 `libopenssl` 编译并安装到 `$ANDROID_NDK/sysroot` 下，然后使用如下命令
```
cmake -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK/build/cmake/android.toolchain.cmake  \
      -DANDROID_NDK=$ANDROID_NDK    \
      -DANDROID_ABI=armeabi-v7a     \
      -DANDROID_TOOLCHAIN=clang     \
      -DANDROID_PLATFORM=android-21 \
      -DANDROID_STL=c++_shared ..
make
```

### CMake 选项

#### BUILD_SHARED_LIBS
(默认为关，即OFF) 如果打开，会同时构建静态库和动态库， 静态库名字增加-static后缀。同时，sample工程会链接到sdk的动态库上。
```
cmake .. -DBUILD_SHARED_LIBS=ON
```

#### BUILD_TESTS
(默认为关，即OFF) 如果打开，会构建出test 及 ptest两个测试工程。
```
cmake .. -DBUILD_TESTS=ON
```

#### ENABLE_RTTI
(默认为开，即ON) 如果关闭, 构建的库不会添加运行时类型信息.
```
cmake .. -DENABLE_RTTI=OFF
```

#### OSS_DISABLE_BUCKET
(默认为关，即OFF) 如果打开, 构建的库不会包含和 bucket 有关的接口.
```
cmake .. -DOSS_DISABLE_BUCKET=ON
```
#### OSS_DISABLE_LIVECHANNEL
(默认为关，即OFF) 如果打开, 构建的库不会包含和 livechannel 有关的接口.
```
cmake .. -DOSS_DISABLE_LIVECHANNEL=ON
```

#### OSS_DISABLE_RESUAMABLE
(默认为关，即OFF) 如果打开, 构建的库不会包含断点续传功能.
```
cmake .. -DOSS_DISABLE_RESUAMABLE=ON
```

#### OSS_DISABLE_ENCRYPTION
(默认为关，即OFF) 如果打开, 构建的库不会包含客户端加密功能.
```
cmake .. -DOSS_DISABLE_ENCRYPTION=ON
```

## 如何使用 C++ SDK

以下代码展示了如何获取请求者拥有的Bucket。

> **说明：** 您需要替换示例中的 your-region-id、your-access-key-id 和 your-access-key-secret 的值。

```
#include <alibabacloud/oss/OssClient.h>
using namespace AlibabaCloud::OSS;

int main(int argc, char** argv)
{
    // 初始化SDK
    InitializeSdk();

    // 配置实例
    ClientConfiguration conf;
    OssClient client("your-region-id", "your-access-key-id", "your-access-key-secret", conf);

    // 创建API请求
    ListBucketsRequest request;
    auto outcome = client.ListBuckets(request);
    if (!outcome.isSuccess()) {
        // 异常处理
        std::cout << "ListBuckets fail" <<
            ",code:" << outcome.error().Code() <<
            ",message:" << outcome.error().Message() <<
            ",requestId:" << outcome.error().RequestId() << std::endl;
        ShutdownSdk();
        return -1;
    }

    // 关闭SDK
    ShutdownSdk();
    return 0;
}
```

## 许可协议
请参阅 LICENSE 文件（Apache 2.0 许可证）。

## Windows下基于MINGW编译aliyun-oss-cpp-sdk
修改代码：
src/utils/FileSystemUtils.cc
增加如下代码：
```
std::string ws2s(const std::wstring &ws)
{
    size_t i;
    std::string curLocale = setlocale(LC_ALL, NULL);
    setlocale(LC_ALL, "chs");
    const wchar_t* _source = ws.c_str();
    size_t _dsize = 2 * ws.size() + 1;
    char* _dest = new char[_dsize];
    memset(_dest, 0x0, _dsize);
    wcstombs_s(&i, _dest, _dsize, _source, _dsize);
    std::string result = _dest;
    delete[] _dest;
    setlocale(LC_ALL, curLocale.c_str());
    return result;
}
```
修改如下代码：
```
std::shared_ptr<std::fstream> AlibabaCloud::OSS::GetFstreamByPath(
    const std::string& path, const std::wstring& pathw,
    std::ios_base::openmode mode)
{
#ifdef _WIN32
    if (!pathw.empty()) {
        return std::make_shared<std::fstream>(ws2s(pathw), mode);
    }
#else
    ((void)(pathw));
#endif
    return std::make_shared<std::fstream>(path, mode);
}
```
修改文件src/utils/ResumableBaseWorker.cc
```
#include <string>
#include <algorithm>
#ifdef _WIN32
#include <codecvt>
#include <locale>
#endif
#include <alibabacloud/oss/Const.h>
#include "ResumableBaseWorker.h"
#include "../utils/FileSystemUtils.h"
#include "../utils/Utils.h"
```
修改文件sdk/src/utils/Utils.cc
```
std::time_t mkgmtime64(struct tm* timeptr)
{
   std::time_t tt = mktime(timeptr);

   return tt;
}

std::time_t AlibabaCloud::OSS::UtcToUnixTime(const std::string &t)
{
    const char* date = t.c_str();
    std::tm tm;
    std::time_t tt = -1;
    int ms;
    auto result = sscanf(date, "%4d-%2d-%2dT%2d:%2d:%2d.%dZ",
        &tm.tm_year, &tm.tm_mon, &tm.tm_mday, &tm.tm_hour, &tm.tm_min, &tm.tm_sec, &ms);

    if (result == 7) {
        tm.tm_year = tm.tm_year - 1900;
        tm.tm_mon = tm.tm_mon - 1;
#ifdef _WIN32
        tt = mkgmtime64(&tm);
#else
        tt = timegm(&tm);
#endif // _WIN32
    }
    return tt < 0 ? -1 : tt;
}
```		 
修改文件CMakeLists.txt
36行增加代码:
set(HAVE__MKGMTIME64 1)

编译：
```
mkdir build
cd build
# mingw64位编译,如果mingw安装32位版本，则把-DCMAKE_CL_64=1去掉
cmake -DCMAKE_CL_64=1 -G "MinGW Makefiles" ..
mingw32-make.exe
```

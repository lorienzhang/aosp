# aosp
aosp compile，debug

# 一、安装repo
```bash
mkdir ~/bin
PATH=~/bin:$PATH
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

# 二、下载源码
```bash
mkdir aosp
cd aosp
```

### 初始化仓库
```bash
# nexus5x 最后支持的版本
repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-8.1.0_r52
```
#### aosp代号、标记、build号
[https://source.android.com/docs/setup/about/build-numbers](https://source.android.com/docs/setup/about/build-numbers)

#### 同步源码
```bash
repo sync
```

删除git缓存目录
```bash
find . -name .git | xargs rm -rf
```

# 三、ubuntu编译aosp
安装依赖
```bash
sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev ccache libgl1-mesa-dev libxml2-utils xsltproc unzip
```

```bash
source build/envsetup.sh 
lunch aosp_x86-eng
export LC_ALL=C
make -j4
```
如果要生成CLion工程，需要在编译前设置环境变量
```bash
export SOONG_GEN_CMAKEFILES=1
export SOONG_GEN_CMAKEFILES_DEBUG=1
```

# 四、导入AS
参考链接：
1. [https://blog.csdn.net/itachi85/article/details/89038386?spm=1001.2014.3001.5501](https://blog.csdn.net/itachi85/article/details/89038386?spm=1001.2014.3001.5501)
2. [https://www.jianshu.com/p/2ba5d6bd461e](https://www.jianshu.com/p/2ba5d6bd461e)

```bash
source build/envsetup.sh 
lunch aosp_x86-eng
mmm development/tools/idegen/

sudo development/tools/idegen/idegen.sh
```

为提升as第一次构建速度，增加exclude目录:
```bash
<excludeFolder url="file://$MODULE_DIR$/bionic" />
<excludeFolder url="file://$MODULE_DIR$/bootable" />
<excludeFolder url="file://$MODULE_DIR$/build" />
<excludeFolder url="file://$MODULE_DIR$/cts" />
<excludeFolder url="file://$MODULE_DIR$/dalvik" />
<excludeFolder url="file://$MODULE_DIR$/developers" />
<excludeFolder url="file://$MODULE_DIR$/development" />
<excludeFolder url="file://$MODULE_DIR$/device" />
<excludeFolder url="file://$MODULE_DIR$/docs" />
<excludeFolder url="file://$MODULE_DIR$/external" />
<excludeFolder url="file://$MODULE_DIR$/hardware" />
<excludeFolder url="file://$MODULE_DIR$/kernel" />
<excludeFolder url="file://$MODULE_DIR$/out" />
<excludeFolder url="file://$MODULE_DIR$/pdk" />
<excludeFolder url="file://$MODULE_DIR$/platform_testing" />
<excludeFolder url="file://$MODULE_DIR$/prebuilts" />
<excludeFolder url="file://$MODULE_DIR$/sdk" />
<excludeFolder url="file://$MODULE_DIR$/system" />
<excludeFolder url="file://$MODULE_DIR$/test" />
<excludeFolder url="file://$MODULE_DIR$/toolchain" />
<excludeFolder url="file://$MODULE_DIR$/tools" />
<excludeFolder url="file://$MODULE_DIR$/.repo" />
```
as构建完成后，需要配置SDK和JDK，参考下图：

![](./res/as-1.png)

![](./res/as-2.png)

![](./res/as-3.png)

# 五、CLion导入多工程

官方参考：[https://android.googlesource.com/platform/build/soong/+/refs/heads/master/docs/clion.md](https://android.googlesource.com/platform/build/soong/+/refs/heads/master/docs/clion.md)

#### 安装CLion
ubuntu安装CLion破解版：[https://www.quanxiaoha.com/idea-pojie/idea-reset-30-day.html](https://www.quanxiaoha.com/idea-pojie/idea-reset-30-day.html)

【备注】：破解包，移步res/目录下载

#### out/development/ide/clion目录下创建CMakeLists.txt
```bash
cmake_minimum_required(VERSION 3.6)
project(native)
add_subdirectory(frameworks/native)

add_subdirectory(frameworks/base/core/jni/libandroid_runtime-x86-android)
add_subdirectory(frameworks/base/libs/hwui/libhwui-x86-android)
add_subdirectory(frameworks/base/media/jni/libmedia_jni-x86-android)

# mediacodec
add_subdirectory(frameworks/av/media/libstagefright/libstagefright-x86-android)
add_subdirectory(frameworks/av/media/libstagefright/foundation/libstagefright_foundation-x86-android)

# system
add_subdirectory(system/core/libutils/libutils-x86-linux)
```

使用CLion以project形式打开CMakeLists.txt

#### CLion index工程时可能会报错：
```bash
no such file or directory: 'external/compiler-rt/lib/cfi/cfi_blacklist.txt'
```

#### 解决：
在CMakeLists.txt中找到这个文件，添加${ANDROID_ROOT}，改成绝对路径:
```bash
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fsanitize-blacklist=${ANDROID_ROOT}/external/compiler-rt/lib/cfi/cfi_blacklist.txt")
```

# 六、CLion 调试native代码
参考：[https://mp.weixin.qq.com/s/ZLnqsIGu7zvrW8s5kHVpdA](https://mp.weixin.qq.com/s/ZLnqsIGu7zvrW8s5kHVpdA)
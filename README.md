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

# 三、编译aosp
```bash
source build/envsetup.sh 
lunch aosp_x86-eng
export LC_ALL=C
make -j4
```

# 四、生成CLion工程
官方参考：[https://android.googlesource.com/platform/build/soong/+/refs/heads/master/docs/clion.md](https://android.googlesource.com/platform/build/soong/+/refs/heads/master/docs/clion.md)
```bash
export SOONG_GEN_CMAKEFILES=1
export SOONG_GEN_CMAKEFILES_DEBUG=1
```
重新编译

```bash
source build/envsetup.sh 
lunch aosp_x86-eng
export LC_ALL=C
make -j4
```
### 导入CLion多工程

ubuntu安装CLion破解版：[https://www.quanxiaoha.com/idea-pojie/idea-reset-30-day.html](https://www.quanxiaoha.com/idea-pojie/idea-reset-30-day.html)

破解包在res/目录下

out/development/ide/clion 目录下创建CMakeLists.txt
```bash
cmake_minimum_required(VERSION 3.6)
project(native)
add_subdirectory(services/surfaceflinger)
add_subdirectory(libs/ui/libui-arm64-android)
add_subdirectory(libs/gui/libgui-arm64-android)
```
使用CLion打开CMakeLists.txt
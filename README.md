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
如果要生成CLion工程，需要在编译前设置环境变量
```bash
export SOONG_GEN_CMAKEFILES=1
export SOONG_GEN_CMAKEFILES_DEBUG=1
```

# 四、导入AS
参考链接：
1. [https://blog.csdn.net/itachi85/article/details/89038386?spm=1001.2014.3001.5501](https://blog.csdn.net/itachi85/article/details/89038386?spm=1001.2014.3001.5501)
2. [https://www.jianshu.com/p/2ba5d6bd461e](https://www.jianshu.com/p/2ba5d6bd461e)

# 五、导入CLion工程
官方参考：[https://android.googlesource.com/platform/build/soong/+/refs/heads/master/docs/clion.md](https://android.googlesource.com/platform/build/soong/+/refs/heads/master/docs/clion.md)

### 导入CLion多工程

#### 安装CLion
ubuntu安装CLion破解版：[https://www.quanxiaoha.com/idea-pojie/idea-reset-30-day.html](https://www.quanxiaoha.com/idea-pojie/idea-reset-30-day.html)

【备注】：破解包，移步res/目录下载

#### out/development/ide/clion目录下创建CMakeLists.txt
```bash
cmake_minimum_required(VERSION 3.6)
project(native)
add_subdirectory(services/surfaceflinger)
add_subdirectory(libs/ui/libui-arm64-android)
add_subdirectory(libs/gui/libgui-arm64-android)
```
使用CLion以project形式打开CMakeLists.txt

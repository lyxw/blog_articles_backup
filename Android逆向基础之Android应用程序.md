# Android逆向基础之Android应用程序

### 0x01 APK简介

**APK**是Android Package的缩写，即Android安装包(apk)。Google为程序员提供了Android SDK来开发Android平台的软件，每个软件在最终发布时会打包成一个APK文件，将APK文件传送到Android设备中运行即可完成安装。实际上APK文件就是一个zip压缩包。

### 0x02 打包APK

Android工程的打包方式有两种：

* 使用Eclipse等**集成开发环境**直接导出生成APK；

* 使用**Ant**工具在命令行方式下打包生成APK。

整个APK打包过程可分为七个步骤：

* **1、打包资源文件，生成`R.java`文件**。用到的打包资源工具为**aapt**。<br />
>* 首先检查`AndroidManifest.xml`的合法性，然后对`res`目录下的资源子目录进行处理，处理内容包括资源文件名合法性检查、向资源表table添加条目等
>* 处理完后编译`res`与`asserts`目录下的资源并生成`resources.arsc`文件
>* 完成资源编译后，对`res`目录的子目录下的`xml`文件分别进行编译，这样处理过的xml文件就简单的被“加密”了
>* 最后将所有的资源与编译生成的`resources.arsc`文件以及“加密”过的`AndroidManifest.xml`文件打包压缩成`resources.ap_`文件（使用Ant工具命令行编译则会生成与`build.xml`中“project name”指定的属性同名的`ap_`文件）。

* **2、处理aidl文件，生成相应的Java文件**。用到的工具为**aidl**。<br />
>* aidl文件，即android interface definition language，Android接口描述语言文件。对于没有使用到aidl的Android工程，这步跳过。

* **3、编译工程源代码，生成相应的class文件**。<br />
>* 调用**javac**编译工程`src`目录下所有的java源文件，生成的class文件位于工程的`bin/classes`目录下。
>* 实际开发中，也可能会使用Android NDK来编译native代码，此时，这一步还需要使用Android NDK编译C/C++代码，当然，编译C/C++代码的步骤也可以提前到第一步或第二步。

* **4、转换所有的class文件，生成classes.dex文件**。用到的工具为**dx**。

* **5、打包生成APK文件**。打包的工具为**apkbuilder**。<br />
>* 代码构建了一个ApkBuilder类，
>* 然后包含`resources.arsc`的文件为基础生成apk文件，这个文件一般为`ap_`结尾的文件
>* 添加工程的资源，处理内容包括`res`目录与`asserts`目录中的文件
>* 添加完资源后往apk文件中写入依赖库
>* 添加工程`libs`目录下的Native库（通过Android NDK编译生成的so或bin文件）
>* 最后关闭apk文件

* **6、对APK文件进行签名**。<br />
>* Android应用程序**需要签名**才能在Android设备上安装。
>* 签名apk文件有两种情况：在调试程序时Eclipse会使用一个`debug.keystore`对apk进行签名；打包发布时需要提供一个符合Android开发文档要求的签名文件进行签名。
>* 签名方法有两种：使用JDK中提供的**jarsigner**工具签名；使用Android源码中提供的**signapk**工具签名。

* **7、对签名后的APK文件进行对齐处理**。用到的工具为**zipalign**。<br />
>* 将apk包进行对齐处理，使apk包中的所有资源文件距离文件起始偏移为4字节整数倍，这样通过内存映射访问apk文件时的速度会更快。

![](https://lyxw.github.io/images/android/APK_build.jpg)

### 0x03 APK安装

Android应用程序安装方式主要有以下4种：

* **系统程序安装**。<br />
>* 由开机启动时的**PackageManagerService**服务完成，该服务在启动时会扫描系统程序目录`/system/app`并重新安装所有程序，此安装没有安装界面。

* **ADB工具安装**。<br />
>* 使用Android SDK提供的调试桥**adb**来安装，在命令行下输入“`adb install xxx.apk`”即可完成安装，此安装没有安装界面。

* **手机自带安装**。<br />
>* 通过点击手机中文件浏览器中的apk文件，直接调用Android系统的软件包**packageinstaller.apk**来完成安装，此安装有安装界面。

* **通过Android应用市场安装**。<br />
>* 直接通过Android市场下载APK文件进行安装，集成Google Play商店的安装没有安装界面。
>* 国内手机大多使用其他Android市场来安装apk程序，此类安装跟手机自带安装类似。

### 0x04 APK安装流程

Android系统接到请求需要安装apk程序时，会启动**PackageInstaller**的*PackageInstallerActivity*，并接收**Intent**传递过来的apk文件信息，*PackageInstallerActivity*启动时会首先初始化一个*PackageManager*与*PackageParser.Package*对象，接着调用***PackageUtil***类的静态方法`getPackageInfo()`解析程序包的信息，如果这一步解析出错，程序就会失败返回，如果成功就调用`setContentView()`方法设置*PackageInstallerActivity*的显示视图，接着调用***PackageUtil***类的静态方法`getAppSnippet()`与`initSnippetForNewApp()`来设置*PackageInstallerActivity*的控件显示程序的名称与图标，最后调用`initiateInstall()`方法进行一些其他的初始化工作。

*PackageInstallerActivity*的`onCreate()`方法中有两个重要函数：***PackageUtil***的`getPackageInfo()`方法以及`initiateInstall()`。`getPackageInfo()`方法首先通过`packageURI`获取到apk文件的路径，然后构造一个*PackageParser*对象，最后调用*PackageParser*对象的`parsePackage()`方法解析apk程序包。

紧接着`parsePackage()`调用了另一个版本的`parsePackage()`方法从`AndroidManifest.xml`文件中获取程序包名，接着构建了一个*Package*对象，接下来挨个处理`AndroidManifest.xml`文件中的标签，处理application标签使用了`parseApplication()`方法，后者解析activity、receiver、service、provider并将它们添加到传递进来的*Package*对象的*ArrayList*中。

`onCreate()`方法中的`getPackageInfo()`返回后调用了`initiateInstall()`，`initiateInstall()`检测该程序是否已经安装，然后分别调用`startInstallConfirm()`显示安装界面或调用`showDialogInner(DLG_REPLACE_APP)`弹出替换程序对话框。

安装按钮使用`startActivity()`方法启动一个Activity类*InstallAppProgress.class*，*InstallAppProgress*类在初始化`onCreate()`方法中调用了`initView()`，后者最终调用了***PackageManager***的`installPaceage()`方法来安装apk程序。

`installPaceage()`调用了`installPackageWithVerification()`方法，该方法首先验证调用者是否具有程序安装的权限，最后通过消息处理的方式调用`processPendingInstall()`进行安装，`processPendingInstall()`又调用方法经过一阵验证折腾，最终调用`replacePackageLl()`或`installNewPackageLl()`来替换或安装程序。

安装和替换操作最终都会调用`scanPackageLl()`方法，`scanPackageLl()`会实例化一个*PackageParser*对象，然后调用其`parsePackage()`方法来解析apk程序包，代码的最后又调用了`scanPackageLl()`的另一个版本来完成apk的依赖库检测、签名的验证、sharedUser的签名检测、更新Native库目录文件、组件名称的检测等工作，最后调用***mInstaller***的`install()`方法来安装程序。

`install()`方法构造字符串“`install name uid gid`”后调用`transaction()`方法，通过**socket**向`/system/bin/installd`程序发送***install***指令，**installd**程序是开机后常驻于内存中的。

`install()`执行完毕后，会通过**socket**回传结果，最终***PackageInstaller***根据返回结果做出相应处理，至此，一个apk程序就安装完成了。

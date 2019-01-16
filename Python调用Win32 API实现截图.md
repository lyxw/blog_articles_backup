# Python调用Win32 API实现截图

### 0x01 Win32 API简介

Win32 API即为Microsoft 32位平台（包括：Windows 9x, Windows NT3.1/4.0/5.0, WindowsCE等）的应用程序编程接口（Application Programming Interface），是构筑所有32位Windows平台的基石，所有在Win32平台上运行的应用程序都可以调用这些函数。

使用Win32 API，应用程序可以充分挖掘Windows的32位操作系统的潜力。 Mircrosoft的所有32位平台都支持统一的API，包括函数、结构、消息、宏及接口。使用 Win32 API不但可以开发出在各种平台上都能成功运行的应用程序，而且也可以充分利用每个平台特有的功能和属性。

在具体编程时，程序实现方式的差异依赖于相应平台的底层功能的不同。最显著的差异是某些函数只能在更强大的平台上实现其功能。例如，安全函数只能在Windows NT操作系统下使用。另外一些主要差别就是系统限制，比如值的范围约束，或函数可管理的项目个数等等。

标准Win32 API函数可以分为以下几类：

* 窗口管理
* 窗口通用控制
* Shell特性
* 图形设备接口
* 系统服务
* 国际特性
* 网络服务

win32api可参考[https://msdn.microsoft.com/en-us/library/windows/desktop/ff818516(v=vs.85).aspx](https://msdn.microsoft.com/en-us/library/windows/desktop/ff818516(v=vs.85).aspx)

### 0x02 Pywin32

#### 1、安装Pywin32

由于本次操作是使用python来操作Win32api，需要先安装Pywin32，可以从这里下载对应的Python和Windows版本文件，下载完后直接运行安装就可以了。

安装文件：[https://sourceforge.net/projects/pywin32/files/](https://sourceforge.net/projects/pywin32/files/)

#### 2、截图思路

截图操作的大致思路如下：

* 模拟按下键盘上的“win + PrtSc”组合键
* 从剪贴板读取缓存的截图
* 将截图文件保存在本地

#### 3、模拟按键

使用的是win32api中的`keydb_event`函数，相关详细信息可以参考[https://msdn.microsoft.com/en-us/library/windows/desktop/ms646304(v=vs.85).aspx](https://msdn.microsoft.com/en-us/library/windows/desktop/ms646304(v=vs.85).aspx)

键位码可以参考[https://msdn.microsoft.com/en-us/library/windows/desktop/dd375731(v=vs.85).aspx](https://msdn.microsoft.com/en-us/library/windows/desktop/dd375731(v=vs.85).aspx)

```python
win32api.keybd_event(0x91, 0, 0, 0)     # 0x91 --> win key
win32api.keybd_event(0x2C, 0, 0, 0)     # 0x2C --> PRINT SCREEN key
win32api.keybd_event(0x91, 0, win32con.KEYEVENTF_KEYUP, 0)
win32api.keybd_event(0x2C, 0, win32con.KEYEVENTF_KEYUP, 0)
```

#### 4、PrtSc相关知识

当按下“win + PrtSc”组合键后，截图被保存在剪贴板中，可以直接在word或者是附件中的画图程序里粘贴后使用，使用剪贴板查看器可以查看剪贴板的内容。

剪贴板在Windows 7/8/10 中是没有的，可以在网上搜索下载一个，一般为clipbrd.exe。

![](https://lyxw.github.io/images/python/clipbrd.png)

自己操作下就会发现，剪贴板只能存储最近复制的内容，新内容会替换旧内容（不区分图片或文字），QQ截图使用的也是系统剪贴板。

从剪贴板查看器的查看选项可以看到，这里有两种图片格式：位图和DIB位图。

![](https://lyxw.github.io/images/python/clipbrd_screen.png)

#### 5、从剪贴板中读取截图

win32api中有一个模块`win32clipboard`是负责剪贴板相关的操作。相关详细信息可以参考[https://msdn.microsoft.com/zh-cn/library/windows/desktop/ff468802(v=vs.85).aspx](https://msdn.microsoft.com/zh-cn/library/windows/desktop/ff468802(v=vs.85).aspx)

`win32clipboard.IsClipboardFormatAvailable(formats)`确定剪贴板是否包含指定格式的数据。

`win32clipboard.GetClipboardData(formats)`可以从剪贴板里读取数据。

需要指定数据的格式`formats`，有关标准剪贴板格式的说明，请参阅标准剪贴板格式[https://msdn.microsoft.com/zh-cn/library/windows/desktop/ms649013(v=vs.85).aspx#_win32_Standard_Clipboard_Formats](https://msdn.microsoft.com/zh-cn/library/windows/desktop/ms649013(v=vs.85).aspx#_win32_Standard_Clipboard_Formats)

此处选用`CF_DIB`，`CF_DIB`返回一个内存对象，包含BIT格式图片的信息。相关详细信息可以参考[https://msdn.microsoft.com/zh-cn/library/windows/desktop/ff729168(v=vs.85).aspx](https://msdn.microsoft.com/zh-cn/library/windows/desktop/ff729168(v=vs.85).aspx)

```python
win32clipboard.OpenClipboard()
if win32clipboard.IsClipboardFormatAvailable(win32clipboard.CF_DIB):
	data = win32clipboard.GetClipboardData(win32clipboard.CF_DIB)
```

#### 6、BMP简介

BMP（全称Bitmap）是Windows操作系统中的标准图像文件格式，它采用位映射存储格式，除了图像深度可选以外，不采用其他任何压缩，因此，BMP文件所占用的空间很大。BMP文件的图像深度可选lbit、4bit、8bit及24bit。BMP文件存储数据时，图像的扫描方式是按从左到右、从下到上的顺序。由于BMP文件格式是Windows环境中交换与图有关的数据的一种标准，因此在Windows环境中运行的图形图像软件都支持BMP图像格式。

典型的BMP图像文件由四部分组成：

* 位图头文件数据结构，它包含BMP图像文件的类型、显示内容等信息；
* 位图信息数据结构，它包含有BMP图像的宽、高、压缩方法，以及定义颜色等信息；
* 调色板，这个部分是可选的，有些位图需要调色板，有些位图，比如真彩色图（24位的BMP）就不需要调色板；
* 位图数据，这部分的内容根据BMP位图使用的位数不同而不同，在24位图中直接使用RGB，而其他的小于24位的使用调色板中颜色索引值。

详细内容可查看[https://baike.baidu.com/item/BMP/35116?fr=aladdin](https://baike.baidu.com/item/BMP/35116?fr=aladdin)

#### 7、构建结构体

##### BMP文件头结构体

详细内容可参考[https://msdn.microsoft.com/en-us/library/windows/desktop/dd183374(v=vs.85).aspx](https://msdn.microsoft.com/en-us/library/windows/desktop/dd183374(v=vs.85).aspx)

```python
class BITMAPFILEHEADER(Structure):
    _pack_   = 1
    _fields_ = [
        ('bfType', WORD),
        ('bfSize', DWORD),
        ('bfReserved1', WORD),
        ('bfReserved2', WORD),
        ('bfOffBits', DWORD),
        ]
```

##### 位图信息头结构体

详细内容可参考[https://msdn.microsoft.com/en-us/library/windows/desktop/dd183376(v=vs.85).aspx](https://msdn.microsoft.com/en-us/library/windows/desktop/dd183376(v=vs.85).aspx)

```python
class BITMAPINFOHEADER(Structure):
    _pack_   = 1
    _fields_ = [
        ('biSize', DWORD),
        ('biWidth', LONG),
        ('biHeight', LONG),
        ('biPLanes', WORD),
        ('biBitCount', WORD),
        ('biCompression', DWORD),
        ('biSizeImage', DWORD),
        ('biXpelsPerMeter', LONG),
        ('biYpelsPerMeter', LONG),
        ('biClrUsed', DWORD),
        ('biClrImportant', DWORD),
        ]
```

用sizeof获取二者的大小并赋值给`SIZEOF_BITMAPFILEHEADER`和`SIZEOF_BITMAPINFOHEADER`。

#### 8、拷贝截图数据

从内存中拷贝出BMP数据用到的是ctypes中的`memmove`函数。详细内容可查看[https://docs.python.org/2/library/ctypes.html?highlight=memmove#ctypes.memmove](https://docs.python.org/2/library/ctypes.html?highlight=memmove#ctypes.memmove)

同时，新建一块区域，全部用0来填充，用到的是ctypes中的`memset`函数。详细内容可查看[https://docs.python.org/2/library/ctypes.html?highlight=memmove#ctypes.memset](https://docs.python.org/2/library/ctypes.html?highlight=memmove#ctypes.memset)

```python
BitMapInfoHeaderHandle = BITMAPINFOHEADER()
memmove(pointer(BitMapInfoHeaderHandle), data, SIZEOF_BITMAPINFOHEADER)
        
BitMapFileHeaderHandle = BITMAPFILEHEADER()
memset(pointer(BitMapFileHeaderHandle), 0, SIZEOF_BITMAPFILEHEADER)
```

#### 9、写文件头

构建文件头的详细内容可参考[https://baike.baidu.com/item/BMP/35116?fr=aladdin](https://baike.baidu.com/item/BMP/35116?fr=aladdin)

```python
BitMapFileHeaderHandle.bfType = ord('B') | (ord('M') << 8)
BitMapFileHeaderHandle.bfSize = SIZEOF_BITMAPFILEHEADER + len(data)
SIZEOF_COLORTABLE = 0
BitMapFileHeaderHandle.bfOffBits = SIZEOF_BITMAPFILEHEADER + SIZEOF_BITMAPINFOHEADER + SIZEOF_COLORTABLE
```

#### 10、生成图片

以二进制方式，先写文件头，再写从剪贴板获取到的字符串到本地的.bmp文件中，完成图片生成。

```python
with open(filename, 'wb') as bmp_file:
    bmp_file.write(BitMapFileHeaderHandle)
    bmp_file.write(data)
print 'file "{}" created from clipboard image'.format(filename)
```

### 0x03 完整代码

[PrintScreen.py](https://github.com/lyxw/codes/blob/master/tools/PrintScreen.py)

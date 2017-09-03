# 跨站脚本攻击（XSS）漏洞自动化验证

### XSS简介

XSS是一个非常常见的Web漏洞，各大互联网公司对XSS的重视程度差别很大，有的作为高危，有的最多当中危甚至当低危，原因无非是这两方面：一方面是XSS虽然数量过多但是利用时需要很多前置条件，不容易成功利用；另一方面一但被成功利用危害又较大，会造成盗取帐号、控制数据、盗窃资料、网站挂马等危害。

### XSS漏洞检测

绝大多数Web漏洞扫描器都可以检测出某一网站是否存在XSS漏洞，网上也能找到很多专用的XSS检测工具，具体的原理及检测过程不再赘述。有兴趣可查看极客学院的课程[《XSS检测与防御》](http://www.jikexueyuan.com/course/1388.html)

### XSS自动化验证

验证的前提是已经确认某页面存在XSS漏洞，并有成功触发的POC（Web漏洞扫描器都会返回吧？）。

验证的思路如下：

* 判断该POC的发送方式：GET和POST
* 若为GET，直接发送即可触发
* 若为POST，拆分POC，本地构造输入框，提交POST请求
* 触发后自动截图

#### 判断发送方式

此处使用的是某厂商的Web漏扫，很人性化的提示当前使用的POC是用那种方式提交，不用自己进行判断，其他情况后续再做补充。

GET：
![xss_get.png](https://lyxw.github.io/images/xss_get.png)

POST：
![xss_post.png](https://lyxw.github.io/images/xss_post.png)

#### 浏览器操作

既然是漏洞验证，得提供充足的证据来证明该漏洞确实是存在的。对于XSS来讲，截取浏览器界面，证明POC触发成功是一个强有力的证据，所谓“无图无真相”嘛。此处就得操作浏览器了。

浏览器操作选用selenium + Firefox。

##### 安装selenium

代码是用python写的，所以基础环境为 Windows 10 + python 2.7。
安装selenium只需要一条命令：

```
pip install selenium
```

##### 下载Firefox driver

selenium需要驱动程序与你所选的浏览器进行连接。Firefox需要安装geckodriver才能正常运行，同时，确保它在你的PATH环境中。

下载地址：[geckodriver](https://github.com/mozilla/geckodriver/releases)

其他受支持的浏览器也有自己的驱动程序可用。一些更受欢迎的浏览器的驱动程序下载链接如下：

Chrome:	[https://sites.google.com/a/chromium.org/chromedriver/downloads](https://sites.google.com/a/chromium.org/chromedriver/downloads)

Edge:	[https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/](https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/)

Safari:	[https://webkit.org/blog/6900/webdriver-support-in-safari-10/](https://webkit.org/blog/6900/webdriver-support-in-safari-10/)

##### selenium简单操作

简单说下selenium的操作，编写代码，让浏览器依次执行以下指令：

* 打开一个新的Firefox浏览器
* 加载百度主页
* 等待一段时间
* 关闭浏览器

代码如下：

```python
from selenium import webdriver

driver = webdriver.Firefox()

driver.get('https://www.baidu.com')
driver.implicitly_wait(10)

driver.quit()
```

#### 漏洞验证

如果漏洞POC触发方式为`GET`时，在上面代码的基础上稍作修改，增加窗口最大化以及截图功能即可实现。

关键代码如下：

```python
......

maxWidth = driver.execute_script("return window.screen.availWidth;")
maxHeight = driver.execute_script("return window.screen.availHeight;")
driver.set_window_size(maxWidth, maxHeight)

......

if driver.switch_to_alert():
    printScreen.printScreen('crackme2.bmp')
	
......
```

有关自动截图部分的内容请看[Python调用Win32 API实现截图](https://lyxw.github.io/archivers/Python%E8%B0%83%E7%94%A8Win32%20API%E5%AE%9E%E7%8E%B0%E6%88%AA%E5%9B%BE)

因为selenium操作浏览器时，不支持直接发送POST请求，如果触发方式为`POST`时，则需要先构造表单，用浏览器模拟点击操作来发送POST请求。

##### 表单构造

从Web漏扫获取到的风险点中，POST触发方式的链接多为如下形式：

```
http://***.***.com:80/register/register.php?FirstName=135791&LastName=135791&Password=admin&DOB=135791&Address=135791&txtCity=135791&drpState=Alabama&TelephoneNo=13412312345&Email=admin@admin.com&UserId=admin>'><ScRiPt>window.a==1?1:prompt(a=1)</ScRiPt>
```

构造表单的目的只是用来向服务器发送一个POST请求，所以，越简单越好。

* 拆分获取到的url

从Web漏扫获取存在XSS漏洞的风险点，首先拆分出使用的网络协议、域名、路径以及传递的参数。

```python
import urlparse

url = "http://crackme.cenzic.com:80/kelev/register/register.php?FirstName=135791&LastName=135791&Password=admin&DOB=135791&Address=135791&txtCity=135791&drpState=Alabama&TelephoneNo=13412312345&Email=admin@admin.com&UserId=admin>'><ScRiPt>window.a==1?1:prompt(a=1)</ScRiPt>?qqdrsign=042c6"

res = urlparse.urlparse(url)

scheme = res[0]
host = res[1]
path = res[2]
data_string = res[4]
```

* 构造表单内容

将传递的参数进一步拆分，还原成表单，生成html代码。

```python
form = ''
s = data_string.split('&')
        
for i in range(0, len(s)):
    p = s[i].split('=', 1)
    inputName = p[0]
    inputValue = p[1]
    tr = '<tr><td align=right><b>' + inputName + ':</b></td><td><input type=text name="' + inputName + '" value="' + inputValue + '" size=80></td></tr>'
    form = form + tr
```

* 构造页面

添加html代码，生成完整的`.html`文件，方便浏览器使用。

```html
<!DOCTYPE html>
<html>
	<head>
		<meta http-equiv="content-type" content="text/html; charset=utf-8">
		<title>WebApi</title>
		
		<style>
			body{ font-size:14px;  font-family:"Microsoft Yahei";background-color:#fff;}
			td{table-layout: fixed; word-wrap:break-word;word-break:break-all;}
		</style>
	</head>
	<body>
		<table border=0 width='90%'>
			<tr>
				<td align=right style=width:120px;>
					<b>url(Action):</b>
				</td>
				<td>
					<a id=id_action target=_blank href="{{ url_base }}">{{ url_base }}</a>
				</td>
			</tr>
		
			<form name=theForm action="{{ url }}" method=post>
			{% csrf_token %}
			
				{% autoescape off %}

					{{ form }}

				{% endautoescape %}
			
			<tr>
				<td></td>
				<td>
					<input onclick="theForm.action=document.getElementById('id_action').href;" id=id_input_action type=submit value=' 使用Action提交POST数据 '>
				</td>
			</tr>
			</form>
		</table>
	</body>
</html>
```

效果图如下：

![xss_form.png](https://lyxw.github.io/images/xss_form.png)

##### 发送POST请求

XSS验证表单构造完毕后，下一步即为发送POST请求。在前文“selenium简单操作”部分的代码基础上，增加点击过程即可。

首先，定位元素名为“id_input_action”的按钮，然后执行点击操作，紧接着判断是否有弹窗，最后截图保存到当前目录。代码如下：

```python
driver.find_element_by_id("id_input_action").click()
time.sleep(3)
if driver.switch_to_alert():
    printScreen.printScreen('crackme2.bmp')
```

### 生成报告

此处为了简便，直接生成html报告，后期需要，也可转换成pdf等格式。

#### 构造报告模板

此处为了简便，可以直接生成html报告，后期需要，也可转换成pdf等其他格式。

构造方式可参考上文表单构造部分内容，只需要传入存在漏洞的链接以及存放图片的路径即可，也可按自己想法或需求，增加漏洞产生的原因、解决方案等内容。

### 效果展示

脚本运行后，先从扫描器结果中获取XSS漏洞的url（涉及到api请求与处理），分析当前url触发方式，选用不同的提交方式去触发，最后截图保存（也可以直接写入word文件中）。

![crackme2.png](https://lyxw.github.io/images/crackme2.png)

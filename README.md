# Python-web

1.1、基础知识 
---
基本数据结构  
数据类型  
控制流  
函数的使用  
模块的使用  

1.2、第三方依赖库
---
Request ：一个方便、简洁、高效且人性化的HTTP请求库  
BeautifulSoup ： HTML解析库  
Pymongo ： MongDB的 Python 封装模块  
Selenium ： 一个web自动化测试框架，用于模拟登陆和获取JS动态数据  
Pytesseract： 一个ORC识别模块，用于验证码识别  
Pillow ： Python图像处理模块  

1.3、第三方库安装：
---
```pip install requests
   pip install bs4
   pip install pymongo
   pip install selenium
   pip install Pillow
   pip install pytesseract
```

2.从一个简单的HTTP请求开始
---
2.1为什么从HTTP开始
---
  因为无论任何与网站的交互，还是通过脚本对URL网站进行访问，本质上都是对HTTP服务器发起请求。  
  打开开发者工具，选择_"network"_可以查看，我们发出的请求"header"和响应内容"response"。

2.2基本HTTP概念
---
  通常HTTP消息*包括*客户机向服务器的*请求消息*和服务器向客户机的*响应消息*。
  这两类的消息由一个起始行，一个或多个头域，一个指示头域结束的空行和可选的消息体组成。


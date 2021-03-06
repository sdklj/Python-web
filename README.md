# Python-web (重点第七章selenium)

## 1.1、基础知识 

基本数据结构  
数据类型  
控制流  
函数的使用  
模块的使用  

## 1.2、第三方依赖库  

Request ：一个方便、简洁、高效且人性化的HTTP请求库  
BeautifulSoup ： HTML解析库  
Pymongo ： MongDB的 Python 封装模块  
Selenium ： 一个web自动化测试框架，用于模拟登陆和获取JS动态数据  
Pytesseract： 一个ORC识别模块，用于验证码识别  
Pillow ： Python图像处理模块  

## 1.3、第三方库安装：

```pip install requests
   pip install bs4
   pip install pymongo
   pip install selenium
   pip install Pillow
   pip install pytesseract
```

2.从一个简单的HTTP请求开始
---
## 2.1为什么从HTTP开始

  因为无论任何与网站的交互，还是通过脚本对URL网站进行访问，本质上都是对HTTP服务器发起请求。  
  打开开发者工具，选择_"network"_可以查看，我们发出的请求"header"和响应内容"response"。

## 2.2基本HTTP概念
  通常HTTP消息*包括*客户机向服务器的*请求消息*和服务器向客户机的*响应消息*。  
  这两类的消息由一个起始行，一个或多个头域，一个指示头域结束的空行和可选的消息体组成。  

### 2.2.1 HTTP 概念  
   Ruquest URL:表示请求的URL  
   Request Method: 表示请求的方法，此处为GET。除此之外，HTTP的请求方法还有OPTION、HEAD、POST、DELETE、PUT等，而最常用的就是GET和POST方法：  

   #### - POST：  
   向指定资源提交数据，请求服务器进行处理(例如提交表单或者上传文件)。数据被包含在请求文本中。这个请求可能会创建新的资源或修改现有资源，或二者皆有。  
   
   #### - GET：
   
   向指定的资源发出“显示”请求。  
   
   #### - Status Code：  
   显示HTTP请求和状态码，表示HTTP请求的状态，此处为200，表示请求一杯服务器接收、理解和处理：  
   状态代码的第一个数字代表当前响应的类型，HTTP协议中有以下集中类型响应：  
   
   - 1xx 消息--请求已被服务器接收，继续处理。
   - 2xx 成功--请求已成功被服务器接收，继续处理、并接收。
   - 3xx 重定向--需要后续操作才能完成这一请求。
   - 4xx 请求错误--请求含有语法错误或无法被执行。
   - 5xx 服务器错误--服务器在处理某个正确请求时发生错误。
   
   ### 2.2.2 HTTP请求头
   **Accept** ：表示请求的资源类型
   **Cookie** ：为了辨识用户身份，进行Session跟踪而存储在用户本地终端上的数据
   **User-Agent** ： 表示浏览器标识
   **Accept-Language** ：表示浏览器所支持的语言类型
   **Accept-Charset** ：告诉Web服务器，浏览器可以接受哪些字符编码
   **Accept** ：表示浏览器支持的MIME类型
   **Accept-Encoding** ：表示浏览器有能力解码的编码类型
   **Connection** ：表示客户端与服务端链接类型
   
   基本介绍如上，如需进一步学习可以学习《图解HTTP》
   
   ## 2.3用Python进行HTTP请求
   
   这里以本文原作者个人网站为例：http：//zmmister.com。打开代码编辑器，输入以下代码
   ```
   import requests
   url = "http：//zmmister.com"
   data = requests.get(url)
   ```
   这样就完成了一次HTTP请求。
   
   **查看状态码：**
   ```data.status_code```
   结果返回200，再**查看响应主体消息。**
   ```data.content```
   结果返回了一大串编码了的HTML源码，这些HTML源码未经解码和解析，看上去是很凌乱。
   
   接下来尝试使用BeautifulSoup对HTML源码进行整理以下。
   
   # 第三章：简单的HTML解析--爬取腾讯新闻
   ## 3.1爬取腾讯新闻
   ### 3.1.1寻找特征数据
   
   ```
   <ul calss="list">
   <li id="20210127A06JNU_3" class="item cf itme-ls" data-bosszone="new_list" data-pos="3" data-bossirs="&amp;articleid=20210127A06JNU&amp;category=36&amp;category2=3600&amp;category_ori=law&amp;s_order=3&amp;articletype=0&amp;pool_type=24hours_pool&amp;source_id=undefined&amp;channel=main&amp;pagetype=ch">
   <a class="picture" href="https://new.qq.com/omn/20210127/20210127A06JNU00.html" target="_blank">
   <img class="Monograph" alt="包工头“偷拍张家慧打麻将”敲诈案再开庭，两名家属获准旁听" src="http://inews.gtimg.com/newsapp_ls/0/13092597955_640330/0">
   ```
   ![avatar](http://inews.gtimg.com/newsapp_ls/0/13092597955_640330/0)
   
   确认完想要获取内容在HTML的文件中的位置。
   
  接下来我们对各种标题进行爬取。
  
  ### 3.1.2 编写爬取代码
  ```
  # import：utf-8
  # 引入相关模块
  import requests
  from bs4 import BeautifulSoup
  
  url = "http://www.dilidili1.com/"
  #请求腾讯新闻的URL，获取其text文本
  webRaw = requests.get(url)
  webRaw.encoding = 'utf8' 
  wbdata = webRaw.text
  #对获取到的文本进行解析
  soup = BeautifulSoup(wbdata,'lxml')
  # 从解析文件中通过select 选择器定位指定的元素，返回一个列表
  new_titles = soup.select(".am-list-item-hd")
  
  #对返回的列表进行遍历
  for n in new_titles:
   #提取出标题和链接信息
   title = n.get_text()
   link = n.get("herf")
   data = {
         '标题'：tiltle，
         '链接'：link
   }
   print(data)
  ```
   # 第四章：使用Cookie模拟登录--获取电子书下载链接
   我们可以使用Cookie模拟用户登录，进行模拟登录后才能爬取的内容
   查找cookie方法：
   1.浏览器的开发者工具-network-Doc-查找cookie
   2.console中搜索document.cookie
   ## 4.1使用Cookie爬取看看电子书下载链接-将cookie加在header里
     ```
     # coding：utf-8
      import requests
      from bs4 import BeautifulSoup

      #以下内容需要登陆后从浏览器开发者软件中手动复制（一种十分不方便的方法）

      cookie ='''douban-fav-remind=1; gr_user_id=fedb1f1a-e7c9-4caa-9d8b-75f19fbfa676; __utmv=30149280.20895; bid=QARc0X_kTRs; ll="108288"; __utma=30149280.1902666466.1563262675.1611124663.1615882371.26; __utmz=30149280.1615882371.26.16.utmcsr=cn.bing.com|utmccn=(referral)|utmcmd=referral|utmcct=/; __utmc=30149280; __utmt=1; dbcl2="208954144:5lfsFmAL+hA"; ck=IqgM; ap_v=0,6.0; push_noty_num=0; push_doumail_num=0; __utmb=30149280.7.10.1615882371; _ga=GA1.3.1902666466.1563262675; _gid=GA1.3.22791438.1615882526; _pk_ref.100001.a7dd=%5B%22%22%2C%22%22%2C1615882526%2C%22https%3A%2F%2Fcn.bing.com%2F%22%5D; _pk_ses.100001.a7dd=*; __gads=ID=8aa2fbb33a579cea-229dec506cc6004e:T=1615882583:RT=1615882583:S=ALNI_MYtc9ecydcIENuDpnVjWoXRh0519g; _pk_id.100001.a7dd=447a2e5e60155dc8.1615882526.1.1615882744.1615882526.'''
      header = {
          'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.90 Safari/537.36',
          'Connection':'keep-alive',
          'Accept':'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9',
          'Cookie':cookie
      }
      url = "https://read.douban.com/"
      #请求豆瓣的URL，获取其text文本
      webRaw = requests.get(url,headers=header)
      webRaw.encoding = 'utf8' 
      wbdata = webRaw.text
      print(wbdata)
      #对获取到的文本进行解析
      soup = BeautifulSoup(wbdata,'lxml')
      print(soup)
     ```
   ## 4.2使用Cookie爬取看看电子书下载链接-将cookie加在request里
     ```
     # coding：utf-8
      import requests
      from bs4 import BeautifulSoup

      #以下内容需要登陆后从浏览器开发者软件中手动复制（一种十分不方便的方法）

      cookie ={douban-fav-remind=1; gr_user_id=fedb1f1a-e7c9-4caa-9d8b-75f19fbfa676; __utmv=30149280.20895; bid=QARc0X_kTRs; ll="108288"; __utma=30149280.1902666466.1563262675.1611124663.1615882371.26; __utmz=30149280.1615882371.26.16.utmcsr=cn.bing.com|utmccn=(referral)|utmcmd=referral|utmcct=/; __utmc=30149280; __utmt=1; dbcl2="208954144:5lfsFmAL+hA"; ck=IqgM; ap_v=0,6.0; push_noty_num=0; push_doumail_num=0; __utmb=30149280.7.10.1615882371; _ga=GA1.3.1902666466.1563262675; _gid=GA1.3.22791438.1615882526; _pk_ref.100001.a7dd=%5B%22%22%2C%22%22%2C1615882526%2C%22https%3A%2F%2Fcn.bing.com%2F%22%5D; _pk_ses.100001.a7dd=*; __gads=ID=8aa2fbb33a579cea-229dec506cc6004e:T=1615882583:RT=1615882583:S=ALNI_MYtc9ecydcIENuDpnVjWoXRh0519g; _pk_id.100001.a7dd=447a2e5e60155dc8.1615882526.1.1615882744.1615882526.}
     
      url = "https://read.douban.com/"
      #请求豆瓣的URL，获取其text文本
      webRaw = requests.get(url,cookies=cookie)
      webRaw.encoding = 'utf8' 
      wbdata = webRaw.text
      print(wbdata)
      #对获取到的文本进行解析
      soup = BeautifulSoup(wbdata,'lxml')
      print(soup)
     ```
     
   上述方法都需要登录后，手动复制cookie等，如何通过代码获取cookie可以通过使用selenium来实现，之后介绍。
   # 第五章：获取JavaScript动态生成的内容-爬取今日头条
   ## 1.如何处理JS生成的网页内容
   在网页源码中我们看不到内容，我们能通过一下两种方法爬取所需内容：
   
   - 使用网页相依中找到JS脚本放回的JSON数据；
   - 使用selenium对网页进行模拟访问
   
   ## 2.爬取今日头条
   网页是由JS动态生成的，JS也需要对某个 *接口* 进行调用，并根据接口返回的JSON数据再进行加载和渲染。所以我们可以找到JS调用的数据接口，从数据接口中找到网页最后呈现的数据。
   
   ### 2.1 找到JS请求的数据接口
   在浏览器-开发者工具中：
   先择“网络”选线卡，发现很多响应，选择XHR（Ajax中的概念，表示XMTLHTTPrequest）
然后我们发现少了很多链接，随便点开一个预览（preview）中有一串JSON数据。
   
   ## 2.请求和解析数据接口数据
      ```
      #coding: utf-8
      import requests
      import json

      word = 'Mobile'
      url = 'http://dict.qq.com/dict?q='+word
      wbdata = requests.get(url).text

      data = json.loads(wbdata)

      print(data)
      ```
   地址不太行，找不到端口。待学习。
   
# 第六章：多线程爬取智联招聘
   
# 第七章：使用selenium，模拟用户
## 7.1 selenium 
Selenium是一个Web的自动化测试工具，最初是为网站自动化测试而开发的，类型像我们玩游戏用的按键精灵，可以按指定的命令自动操作，不同是Selenium 可以直接运行在浏览器上，它支持所有主流的浏览器。

Selenium 可以根据我们的指令，让浏览器自动加载页面，获取需要的数据，甚至页面截屏，或者判断网站上某些动作是否发生。

Selenium 自己不带浏览器，不支持浏览器的功能，它需要与第三方浏览器结合在一起才能使用。但是我们有时候需要让它内嵌在代码中运行，所以我们可以用一个叫 Chrome 的工具代替真实的浏览器。
## 7.2 快速入门
Selenium 库里有个叫 WebDriver 的 API。WebDriver 有点儿像可以加载网站的浏览器，但是它也可以像 BeautifulSoup 或者其他 Selector 对象一样用来查找页面元素，与页面上的元素进行交互 (发送文本、点击等)，以及执行其他动作来运行网络爬虫。
```
# IPython2 测试代码

# 导入 webdriver
from selenium import webdriver

# 要想调用键盘按键操作需要引入keys包
from selenium.webdriver.common.keys import Keys

# 调用环境变量指定的PhantomJS浏览器创建浏览器对象
driver = webdriver.PhantomJS()

# 如果没有在环境变量指定PhantomJS位置
# driver = webdriver.PhantomJS(executable_path="./phantomjs"))

# get方法会一直等到页面被完全加载，然后才会继续程序，通常测试会在这里选择 time.sleep(2)
driver.get("http://www.baidu.com/")

# 获取页面名为 wrapper的id标签的文本内容
data = driver.find_element_by_id("wrapper").text

# 打印数据内容
print data

# 打印页面标题 "百度一下，你就知道"
print driver.title

# 生成当前页面快照并保存
driver.save_screenshot("baidu.png")

# id="kw"是百度搜索输入框，输入字符串"长城"
driver.find_element_by_id("kw").send_keys(u"马云")

# id="su"是百度搜索按钮，click() 是模拟点击
driver.find_element_by_id("su").click()

# 获取新的页面快照
driver.save_screenshot("马云.png")

# 打印网页渲染后的源代码
print driver.page_source

# 获取当前页面Cookie
print driver.get_cookies()

# ctrl+a 全选输入框内容
driver.find_element_by_id("kw").send_keys(Keys.CONTROL,'a')

# ctrl+x 剪切输入框内容
driver.find_element_by_id("kw").send_keys(Keys.CONTROL,'x')

# 输入框重新输入内容
driver.find_element_by_id("kw").send_keys(u"王健林")

# 模拟Enter回车键
driver.find_element_by_id("su").send_keys(Keys.RETURN)

# 清除输入框内容
driver.find_element_by_id("kw").clear()

# 生成新的页面快照
driver.save_screenshot("王健林.png")

# 获取当前url
print driver.current_url

# 关闭当前页面，如果只有一个页面，会关闭浏览器
# driver.close()

# 关闭浏览器
driver.quit()
```
## 7.3 页面操作
Selenium 的 WebDriver提供了各种方法来寻找元素，假设下面有一个表单输入框：
```
<input type="text" name="user-name" id="passwd-id" />
   
# 获取id标签值
element = driver.find_element_by_id("passwd-id")
# 获取name标签值
element = driver.find_element_by_name("user-name")
# 获取标签名值
element = driver.find_elements_by_tag_name("input")
# 也可以通过XPath来匹配
element = driver.find_element_by_xpath("//input[@id='passwd-id']")
```
### 7.3.1 By ID
```
<div id="coolestWidgetEvah">...</div>
```
实现
```
element = driver.find_element_by_id("coolestWidgetEvah")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
element = driver.find_element(by=By.ID, value="coolestWidgetEvah")
```
### 7.3.2 By Class Name
```
<div class="cheese"><span>Cheddar</span></div><div class="cheese"><span>Gouda</span></div>
```
实现
```
cheeses = driver.find_elements_by_class_name("cheese")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
cheeses = driver.find_elements(By.CLASS_NAME, "cheese")
```
### 7.3.3 By Tag Name
```
<iframe src="..."></iframe>
```
实现
```
frame = driver.find_element_by_tag_name("iframe")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
frame = driver.find_element(By.TAG_NAME, "iframe")
```
### 7.3.4 By Name
```
<input name="cheese" type="text"/>
```
实现
```
cheese = driver.find_element_by_name("cheese")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
cheese = driver.find_element(By.NAME, "cheese")
```
### 7.3.5 By Link Text
```
<a href="http://www.google.com/search?q=cheese">cheese</a>
```
实现
```
cheese = driver.find_element_by_link_text("cheese")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
cheese = driver.find_element(By.LINK_TEXT, "cheese")
```
### 7.3.6 By Partial Link Text
```
<a href="http://www.google.com/search?q=cheese">search for cheese</a>>
```
实现
```
cheese = driver.find_element_by_partial_link_text("cheese")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
cheese = driver.find_element(By.PARTIAL_LINK_TEXT, "cheese")
```
### 7.3.7 By CSS
```
<div id="food"><span class="dairy">milk</span><span class="dairy aged">cheese</span></div>
```
实现
```
cheese = driver.find_element_by_css_selector("#food span.dairy.aged")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
cheese = driver.find_element(By.CSS_SELECTOR, "#food span.dairy.aged")
```
### 7.3.8 By XPath
```
<input type="text" name="example" />
<INPUT type="text" name="other" />
```
实现
```
inputs = driver.find_elements_by_xpath("//input")
------------------------ or -------------------------
from selenium.webdriver.common.by import By
inputs = driver.find_elements(By.XPATH, "//input")
```
## 7.4 鼠标动作链
有些时候，我们需要再页面上模拟一些鼠标操作，比如双击、右击、拖拽甚至按住不动等，我们可以通过导入 ActionChains 类来做到：
```
#导入 ActionChains 类
from selenium.webdriver import ActionChains

# 鼠标移动到 ac 位置
ac = driver.find_element_by_xpath('element')
ActionChains(driver).move_to_element(ac).perform()


# 在 ac 位置单击
ac = driver.find_element_by_xpath("elementA")
ActionChains(driver).move_to_element(ac).click(ac).perform()

# 在 ac 位置双击
ac = driver.find_element_by_xpath("elementB")
ActionChains(driver).move_to_element(ac).double_click(ac).perform()

# 在 ac 位置右击
ac = driver.find_element_by_xpath("elementC")
ActionChains(driver).move_to_element(ac).context_click(ac).perform()

# 在 ac 位置左键单击hold住
ac = driver.find_element_by_xpath('elementF')
ActionChains(driver).move_to_element(ac).click_and_hold(ac).perform()

# 将 ac1 拖拽到 ac2 位置
ac1 = driver.find_element_by_xpath('elementD')
ac2 = driver.find_element_by_xpath('elementE')
ActionChains(driver).drag_and_drop(ac1, ac2).perform()
```

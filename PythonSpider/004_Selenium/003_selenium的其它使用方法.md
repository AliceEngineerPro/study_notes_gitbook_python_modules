# selenium的其它使用方法

## 一. selenium标签页的切换

> 当selenium控制浏览器打开多个标签页时，控制浏览器在不同的标签页中进行切换
>
> 需要以下两步：
>
> 1. 获取所有标签页的窗口句柄
>
>    窗口句柄: 向标签页对象的标识
> 2. 利用窗口句柄字切换到句柄指向的标签页

参考代码示例:

```python
# coding: utf8
""" 
@File: part_006.py
@Author: Alice(From Chengdu.China)
@HomePage: https://github.com/AliceEngineerPro
@CreatedTime: 2022/12/9 9:24
"""

from selenium import webdriver
import time

webdriver.FirefoxOptions().binary_location = r'C:\Program Files\Mozilla Firefox\firefox.exe'
executable_path = r'./driver/firefox_107_0_1_x86_64/win/geckodriver.exe'
driver = webdriver.Firefox(executable_path=executable_path)
driver.get(url='https://baidu.com')
js_opentable = 'window.open("https://taobao.com")'
driver.execute_script(js_opentable)
time.sleep(2)
# 浏览器标签句柄
browser_windows = driver.window_handles
driver.switch_to.window(browser_windows[0])
time.sleep(5)
driver.switch_to.window(browser_windows[1])
time.sleep(5)
driver.quit()
```

## 二. switch_to切换frame标签

> iframe是html中常用的一种技术，即一个页面中嵌套了另一个网页，selenium默认是访问不了frame中的内容的，对应的解决思路是`driver.switch_to.frame(frame_element)`。接下来我们通过qq邮箱模拟登陆来学习这个知识点

参考代码示例:

```python
# coding: utf8
""" 
@File: part_007.py
@Author: Alice(From Chengdu.China)
@HomePage: https://github.com/AliceEngineerPro
@CreatedTime: 2022/12/9 10:30
"""
import time
from selenium import webdriver
from selenium.webdriver.common.by import By


driver = webdriver.Chrome()

url = 'https://mail.qq.com/cgi-bin/loginpage'
driver.get(url)
time.sleep(2)

login_frame = driver.find_element(by=By.ID, value='login_frame') # 根据id定位 frame元素
driver.switch_to.frame(login_frame) # 转向到该frame中
driver.find_element(by=By.XPATH, value='//*[@id="u"]').send_keys('1596930226@qq.com')
time.sleep(2)
driver.find_element(by=By.XPATH, value='//*[@id="p"]').send_keys('hahamimashicuode')
time.sleep(2)
driver.find_element(by=By.XPATH, value='//*[@id="login_button"]').click()
time.sleep(2)
# 操作frame外边的元素需要切换出去
windows = driver.window_handles
driver.switch_to.window(windows[0])
content = driver.find_element(by=By.CLASS_NAME, value='login_pictures_title').text
print(content)
driver.quit()
```

## 三. selenium对cookie的处理

> selenium能够帮助我们处理页面中的cookie，比如获取、删除等

### 1. 获取cookie

> `driver.get_cookies()`返回列表，其中包含的是完整的cookie信息！不光有name、value，还有domain等cookie其他维度的信息。所以如果想要把获取的cookie信息和requests模块配合使用的话，需要转换为name、value作为键值对的cookie字典

```python
# coding: utf8
""" 
@File: part_008.py
@Author: Alice(From Chengdu.China)
@HomePage: https://github.com/AliceEngineerPro
@CreatedTime: 2022/12/9 20:20
"""

from selenium import webdriver

webdriver.FirefoxOptions().binary_location = r'C:\Program Files\Mozilla Firefox\firefox.exe'
executable_path = r'./driver/firefox_107_0_1_x86_64/win/geckodriver.exe'
driver = webdriver.Firefox(executable_path=executable_path)
driver.get(url='https://baidu.com')
baidu_cookies = {data.get('name'): data.get('value') for data in driver.get_cookies()}
print(baidu_cookies)
driver.quit()
```

### 2. 删除cookie

```python
#删除一条cookie
driver.delete_cookie("CookieName")

# 删除所有的cookie
driver.delete_all_cookies()
```

## 四. selenium控制浏览器执行js代码

> selenium可以让浏览器执行我们规定的js代码，运行下列代码查看运行效果

```python
# coding: utf8
""" 
@File: part_009.py
@Author: Alice(From Chengdu.China)
@HomePage: https://github.com/AliceEngineerPro
@CreatedTime: 2022/12/11 9:15
"""

from selenium import webdriver
import time

webdriver.FirefoxOptions().binary_location = r'C:\Program Files\Mozilla Firefox\firefox.exe'
executable_path = r'./driver/firefox_107_0_1_x86_64/win/geckodriver.exe'
driver = webdriver.Firefox(executable_path=executable_path)
driver.get(url='https://qq.com')
js = '''window.scrollTo(0,document.body.scrollHeight)'''
driver.execute_script(script=js)
time.sleep(5)
driver.quit()
```

- 执行js的方法：`driver.execute_script(js)`

## 五. 页面等待

> 页面在加载的过程中需要花费时间等待网站服务器的响应，在这个过程中标签元素有可能还没有加载出来，是不可见的

1. 页面等待分类
2. 强制等待介绍
3. 显式等待介绍
4. 隐式等待介绍
5. 手动实现页面等待

### 1. 页面等待的分类

> 了解以下selenium页面等待的分类

1. 强制等待
2. 隐式等待
3. 显式等待

### 2. 强制等待（了解）

- 其实就是`time.sleep()`
- 缺点时不智能，设置的时间太短，元素还没有加载出来；设置的时间太长，则会浪费时间

### 3. 隐式等待

隐式等待针对的是元素定位，隐式等待设置了一个时间，在一段时间内判断元素是否定位成功，如果完成了，就进行下一步

在设置的时间内没有定位成功，则会报超时加载

参考代码示例:

```python
from selenium import webdriver

driver = webdriver.Chrome()  

driver.implicitly_wait(10) # 隐式等待，最长等20秒  

driver.get('https://www.baidu.com')

driver.find_element_by_xpath()
```

### 4. 显式等待（了解）

每经过多少秒就查看一次等待条件是否达成，如果达成就停止等待，继续执行后续代码

如果没有达成就继续等待直到超过规定的时间后，报超时异常

参考代码示例:

```python
from selenium import webdriver  
from selenium.webdriver.support.wait import WebDriverWait  
from selenium.webdriver.support import expected_conditions as EC  
from selenium.webdriver.common.by import By 

driver = webdriver.Chrome()

driver.get('https://www.baidu.com')

# 显式等待
WebDriverWait(driver, 20, 0.5).until(
    EC.presence_of_element_located((By.LINK_TEXT, '好123')))  
# 参数20表示最长等待20秒
# 参数0.5表示0.5秒检查一次规定的标签是否存在
# EC.presence_of_element_located((By.LINK_TEXT, '好123')) 表示通过链接文本内容定位标签
# 每0.5秒一次检查，通过链接文本内容定位标签是否存在，如果存在就向下继续执行；如果不存在，直到20秒上限就抛出异常

print(driver.find_element_by_link_text('好123').get_attribute('href'))
driver.quit()
```

### 5. 手动实现页面等待

> 在了解了隐式等待和显式等待以及强制等待后，我们发现并没有一种通用的方法来解决页面等待的问题，比如“页面需要滑动才能触发ajax异步加载”的场景，那么接下来我们就以淘宝网首页为例，手动实现页面等待

原理:

- 利用强制等待和显式等待的思路来手动实现
- 不停的判断或有次数限制的判断某一个标签对象是否加载完毕（是否存在）

参考代码示例:

```python
import time
from selenium import webdriver
driver = webdriver.Chrome('/home/worker/Desktop/driver/chromedriver')

driver.get('https://www.taobao.com/')
time.sleep(1)

# i = 0
# while True:
for i in range(10):
    i += 1
    try:
        time.sleep(3)
        element = driver.find_element_by_xpath('//div[@class="shop-inner"]/h3[1]/a')
        print(element.get_attribute('href'))
        break
    except:
        js = 'window.scrollTo(0, {})'.format(i*500) # js语句
        driver.execute_script(js) # 执行js的方法
driver.quit()
```

## 六. selenium开启无界面模式

> 绝大多数服务器是没有界面的，selenium控制谷歌浏览器也是存在无界面模式的

开启无界面模式的方法:

1. 实例化配置对象
   - `options = webdriver.ChromeOptions()`
2. 配置对象添加开启无界面模式的命令
   - `options.add_argument("--headless")`
3. 配置对象添加禁用gpu的命令
   - `options.add_argument("--disable-gpu")`
4. 实例化带有配置对象的driver对象
   - `driver = webdriver.Chrome(options=options)`

参考代码示例:

```python
from selenium import webdriver

options = webdriver.ChromeOptions() # 创建一个配置对象
options.add_argument("--headless") # 开启无界面模式
options.add_argument("--disable-gpu") # 禁用gpu

# options.set_headles() # 无界面模式的另外一种开启方式
driver = webdriver.Chrome(chrome_options=options) # 实例化带有配置的driver对象

driver.get('http://www.itcast.cn')
print(driver.title)
driver.quit()
```

## 七. selenium使用代理ip

> selenium控制浏览器也是可以使用代理ip的！

使用代理ip的方法:

1. 实例化配置对象
   - `options = webdriver.ChromeOptions()`
2. 配置对象添加使用代理ip的命令
   - `options.add_argument('--proxy-server=http://202.20.16.82:9527')`
3. 实例化带有配置对象的driver对象
   - `driver = webdriver.Chrome('./chromedriver', chrome_options=options)`

参考代码示例:

```python
from selenium import webdriver

options = webdriver.ChromeOptions() # 创建一个配置对象
options.add_argument('--proxy-server=http://202.20.16.82:9527') # 使用代理ip

driver = webdriver.Chrome(chrome_options=options) # 实例化带有配置的driver对象

driver.get('http://www.itcast.cn')
print(driver.title)
driver.quit()
```

## 八. selenium替换user-agent

> selenium控制谷歌浏览器时，User-Agent默认是谷歌浏览器的

替换user-agent的方法:

1. 实例化配置对象
   - `options = webdriver.ChromeOptions()`
2. 配置对象添加替换UA的命令
   - `options.add_argument('--user-agent=Mozilla/5.0 HAHA')`
3. 实例化带有配置对象的driver对象
   - `driver = webdriver.Chrome('./chromedriver', chrome_options=options)`

参考代码示例: 

```python
from selenium import webdriver

options = webdriver.ChromeOptions() # 创建一个配置对象
options.add_argument('--user-agent=Mozilla/5.0 HAHA') # 替换User-Agent

driver = webdriver.Chrome('./chromedriver', chrome_options=options)

driver.get('http://www.itcast.cn')
print(driver.title)
driver.quit()
```


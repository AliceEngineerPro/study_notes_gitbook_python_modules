# 数据提取: jsonpath模块

## 一. jsonpath模块的使用场景

>   如果有一个多层嵌套的复杂字典,想要根据key和下标来批量提取value,这是比较困难的.jsonpath模块就能解决这个痛点,接下来我们就来学习jsonpath模块

jsonpath可以按照key对python字典进行批量数据提取

## 二. jsonpath模块的使用方法

### 1. jsonpath模块的安装

> jsonpath是第三方模块，需要额外安装

```python
pip install jsonpath
```

### 2. jsonpath模块提取数据的方法

```python
from jsonpath import jsonpath
ret = jsonpath(a, 'jsonpath语法规则字符串')
```

### 3. jsonpath语法规则

![jsonpath的方法](./static/images/jsonpath%E7%9A%84%E6%96%B9%E6%B3%95.png) 

### 4. jsonpath使用示例

```python
book_dict = { 
  "store": {
    "book": [ 
      { "category": "reference",
        "author": "Nigel Rees",
        "title": "Sayings of the Century",
        "price": 8.95
      },
      { "category": "fiction",
        "author": "Evelyn Waugh",
        "title": "Sword of Honour",
        "price": 12.99
      },
      { "category": "fiction",
        "author": "Herman Melville",
        "title": "Moby Dick",
        "isbn": "0-553-21311-3",
        "price": 8.99
      },
      { "category": "fiction",
        "author": "J. R. R. Tolkien",
        "title": "The Lord of the Rings",
        "isbn": "0-395-19395-8",
        "price": 22.99
      }
    ],
    "bicycle": {
      "color": "red",
      "price": 19.95
    }
  }
}

from jsonpath import jsonpath

print(jsonpath(book_dict, '$..author')) # 如果取不到将返回False # 返回列表，如果取不到将返回False
```

![jsonpath使用示例](./static/images/jsonpath%E4%BD%BF%E7%94%A8%E7%A4%BA%E4%BE%8B.png) 

## 三. jsonpath练习

> 我们以拉勾网城市JSON文件 http://www.lagou.com/lbs/getAllCitySearchLabels.json 为例，获取所有城市的名字的列表，并写入文件。

参考代码:

```python
import requests
import jsonpath
import json

# 获取拉勾网城市json字符串
url = 'http://www.lagou.com/lbs/getAllCitySearchLabels.json'
headers = {"User-Agent": "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)"}
response =requests.get(url, headers=headers)
html_str = response.content.decode()

# 把json格式字符串转换成python对象
jsonobj = json.loads(html_str)

# 从根节点开始，获取所有key为name的值
citylist = jsonpath.jsonpath(jsonobj,'$..name')

# 写入文件
with open('city_name.txt','w') as f:
    content = json.dumps(citylist, ensure_ascii=False)
    f.write(content)
```


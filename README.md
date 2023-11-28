# 爬取 PTT CAR 版 NISSAN 相關討論文章,並進行輿情分析 
在學習網頁爬蟲相關資源時，發現到大多數的文章僅著重在網頁爬蟲技術分享。輿情分析的部分，免費的中文學習資源較少，大多數以收費課程之形式存在。

因此希望透過此專案可以將自己的實作過程記錄下來，並將學習的結果分享給大家。
這邊僅針對語法概念及目的進行說明，完整之語法執行結果大家可藉由此專案資料夾內之.ipynb檔做更進一步的了解。

本專案旨在透過網頁爬蟲技巧收集 PTT Car 版中有關 Nissan 的討論文章，並透過輿情分析探索社會大眾對於 Nissan 相關主題的意見與情感傾向。
以下為本專案之大綱，後續將於每段步驟進行介紹並提供示範語法

**大綱**
1. 網頁爬蟲
   * 環境安裝
   * 語法 
2. 輿情分析
   * 環境安裝
   * 語法 

## 網頁爬蟲
### 環境安裝
本專案使用 Python3 並且會使用 pip 來安裝所需的套件。以下是需要安裝的套件：

* beautifulsoup4：主要功能就是可以全面解析 HTML 或 XML 的架構。

* requests：用於發送與接收 HTTP 請求及回應。
  
* pandas：進行資料處理和資料分析的工具

使用以下指令來安裝這些套件：

```

pip install beautifulsoup4 requests pandas

```

### 語法
#### 套件匯入
首先，開發過程有時會忘記到底先前有沒有匯入過想使用的套件，導致重複在不同地方 import 一樣的套件進來，因此如果要使用之套件數量不多時，我個人習慣先將會使用到的套件一次匯入，
避免讓 import 語法分散在不同段落中。

使用以下指令來匯入稍後將使用到的套件：

```

import requests
from bs4 import BeautifulSoup
import csv
from datetime import datetime
import os
import pandas as pd

```
## 輿情分析
### 環境安裝
### 語法

















# 一级标题  
## 二级标题  
### 三级标题  
#### 四级标题  
##### 五级标题  
###### 六级标题 
二、编辑基本语法  
1、字体格式强调
 我们可以使用下面的方式给我们的文本添加强调的效果
*强调*  (示例：斜体)  
 _强调_  (示例：斜体)  
**加重强调**  (示例：粗体)  
 __加重强调__ (示例：粗体)  
***特别强调*** (示例：粗斜体)  
___特别强调___  (示例：粗斜体)  
2、代码  
`<hello world>`  
3、代码块高亮  
```
@Override
protected void onDestroy() {
    EventBus.getDefault().unregister(this);
    super.onDestroy();
}
```  
4、表格 （建议在表格前空一行，否则可能影响表格无法显示）
  
 表头  | 表头  | 表头
 ---- | ----- | ------  
 单元格内容  | 单元格内容 | 单元格内容 
 单元格内容  | 单元格内容 | 单元格内容  
  
5、其他引用
图片  
![图片名称](https://www.baidu.com/img/bd_logo1.png)  
链接  
[链接名称](https://www.baidu.com/)    
6、列表 
1. 项目1  
2. 项目2  
3. 项目3  
   * 项目1 （一个*号会显示为一个黑点，注意⚠️有空格，否则直接显示为*项目1） 
   * 项目2   
### Place 1

使用markdown语法：[点击跳转](#top)
直接回车不能换行，  
可以在上一行文本后面补两个空格，  
这样下一行的文本就换行了。
或者就是在两行文本直接加一个空行。
也能实现换行效果，不过这个行间距有点大。  
 
8、引用
> 第一行引用文字  
> 第二行引用文字

## Specification
Example text blah. Example text blah. Example text blah. Example text blah. 
Example text blah. Example text blah. Example text blah. Example text blah. 
Example text blah. Example text blah. Example text blah. Example text blah. 
Example text blah. Example text blah. 

## Dependencies Title
Example text blah. Example text blah. Example text blah. Example text blah. 
Example text blah. Example text blah. Example text blah. Example text blah. 
Example text blah. Example text blah. Example text blah. Example text blah. 
Example text blah. Example text blah. 

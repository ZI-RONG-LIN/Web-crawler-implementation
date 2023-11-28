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

```python
pip install beautifulsoup4 requests pandas
```

### 語法
#### 套件匯入
首先，開發過程有時會忘記到底先前有沒有匯入過想使用的套件，導致重複在不同地方 import 一樣的套件進來，因此如果要使用之套件數量不多時，可以先將會使用到的套件一次匯入，避免讓 import 語法分散在不同段落中。

使用以下指令來匯入稍後將使用到的套件：

```python
import requests
from bs4 import BeautifulSoup
import csv
from datetime import datetime
import os
import pandas as pd
```
#### 讀取網站資料
在進行網頁爬蟲時，我們會透過模擬瀏覽器發送 HTTP 請求獲取 PTT CAR 版中包含指定關鍵字的文章列表的 HTML 內容。這個函式會返回一個 response 物件，裡頭包含了網頁的回應內容。接著我們使用 BeautifulSoup 解析這個 HTML 內容，方便後續的資料提取。

使用以下指令來讀取網站資料：

```python
#定義了要搜索的關鍵字，這裡以'Nissan'為例，可以根據需求調整為其他品牌或是使用迴圈撈取多品牌的結果。
search_keyword = 'Nissan'

#建立了目標網頁的URL，其中因爲PTT每頁有文章上限，因此透過頁面編號（page）這個參數，後續以迴圈方式獲取多頁目標網頁的內容。
src='https://www.ptt.cc/bbs/car/search?page=' + str(page) + '&q='+ search_keyword

#有些網站會檢查 User-Agent 以確保請求是由瀏覽器發出的，因此這裡模擬了 Chrome 瀏覽器的 User-Agent。
requestUA=request.Request(src, headers={
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.182 Safari/537.36"
})

#打開 URL 並發送 HTTP 請求。使用 with 關鍵字可以確保在程式碼區塊結束時關閉資源，這邊也可以使用 requests.get 直接發送 GET 請求。
with request.urlopen(requestUA) as response:
    data=response.read().decode("utf-8")

#讓BeautifulSoup協助解析HTML格式文件
root=bs4.BeautifulSoup(data, "html.parser") 
```

#### 撈取特定網站內容
接著為了確認是否可以順利取得網頁的資訊，透過解析網頁的標籤位置，印出看板標題及文章標題
```python
#印出看板標題
print(root.title.string)#尋找"title"標籤字串

#印出文章標題
titles=root.find_all("div", class_="title") #尋找所有class="title"的div標籤
for title in titles:
    if title.a != None: #如果標題包含a標籤 (沒有被刪除), 印出來
        print(title.a.string)
```
<p align="center">
<img src="https://github.com/p56071078/Web-crawler-implementation/blob/main/img/print%20title.png" width="550" height="500">
</p>




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

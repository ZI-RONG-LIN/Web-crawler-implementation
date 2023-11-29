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
import urllib.request as request 
import bs4
import csv
from datetime import datetime
import os
import pandas as pd
from collections import defaultdict
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
接著為了確認是否可以順利取得網頁的資訊，透過解析網頁的標籤位置，印出看板標題及文章標題。
```python
#尋找"title"標籤字串，印出看板標題
print(root.title.string)

#尋找所有class="title"的div標籤，印出文章標題
titles=root.find_all("div", class_="title") 
for title in titles:
    if title.a != None: #只印出未被刪除的文章
        print(title.a.string)
```
<p align="center">
<img src="https://github.com/p56071078/Web-crawler-implementation/blob/main/img/print%20title.png" width="550" height="500">
</p>

#### 取得文章連結
接著為了後續可以透過迴圈取得每篇文章的內容、作者、推文等資訊，因此可以先把文章連結事先撈取出來並存在一個空的陣列中。

```python
#尋找所有class="r-ent"的div標籤，印出文章連結
rent = root.find_all('div',class_='r-ent')

#建立一個空的陣列，把抓到的文章連結一個一個添加進去
link=[]
for title in rent:
    #由於有些被刪除的文章會抓不到連結，所以把抓不到被刪除的文章濾掉
    if title.a != None:
        link.append("https://www.ptt.cc"+title.a.get("href"))
print(link)
```
#### 依序爬取每篇文章之標題、作者、日期、內文、推文
接著透過迴圈取得多頁文章的內容、作者、推文等資訊，由於後續希望將所有爬取的結果一筆一筆加進 DataFrame 並匯出成 csv 檔，因此我會把爬取到的資訊分別存在不同的陣列中，另外由於PTT留言會有字數限制，因此會再針對留言進行加工，將相同帳號的留言合併後，再 append 到 DataFrame 裡頭存放。

結合前面的步驟，最後完成的程式語法如下：
```python
#建立一個空的 DataFrame，後續會一筆一筆將爬取到的資料加進 DataFrame 
dataset = pd.DataFrame()

#建立迴圈，可爬取多頁內容
for page in range(1,4): #可依需求調整要爬取的頁數
    search_keyword = 'Nissan' #可依需求調整關鍵字
    #模擬 Chrome 瀏覽器的 User-Agent
    src='https://www.ptt.cc/bbs/car/search?page=' + str(page) + '&q='+ search_keyword 
    requestUA=request.Request(src, headers={
        "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.182 Safari/537.36"
    })
    with request.urlopen(requestUA) as response:
        data=response.read().decode("utf-8")
    root=bs4.BeautifulSoup(data, "html.parser") #讓BeautifulSoup協助解析HTML格式文件
    #尋找"title"標籤字串，印出看板標題
    print(root.title.string)

    #尋找所有class="title"的div標籤，印出文章標題
    titles=root.find_all("div", class_="title") 
    for title in titles:
        if title.a != None: #只印出未被刪除的文章
            print(title.a.string)

    #抓取看板文章的連結
    rent = root.find_all('div',class_='r-ent')
    #建立一個空的陣列，把抓到的文章連結一個一個添加進去
    link=[]
    for title in rent:
        #由於有些被刪除的文章會抓不到連結，所以把抓不到被刪除的文章濾掉
        if title.a != None:
            link.append("https://www.ptt.cc"+title.a.get("href"))
    #print(link)

    #透過文章連結，抓取文章內容
    for websites in link:
        requestUA=request.Request(websites, headers={
        "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/88.0.4324.182 Safari/537.36"
        })
        with request.urlopen(requestUA) as response:
            data=response.read().decode("utf-8")
        root=bs4.BeautifulSoup(data, "html.parser") #讓BeautifulSoup協助解析HTML格式文件

        header = root.find_all('span','article-meta-value')
        if header != []:

        # 作者
            author = header[0].text
        #可用此語法印出作者:print("Author : "+ author+'\n')

        # 日期
            date = header[3].text
        #可用此語法印出日期:print("Date : "+ date+'\n')

        #文章主題
        titles=root.find_all("div", id_="main-content") 
        for title in titles:
            if title.a != None: #如果標題包含a標籤 (沒有被刪除), 印出來
                print(title.a.string)

        #文章內容
        main_container = root.find(id='main-container')
        # 把所有文字都抓出來
        if main_container != " ":
            all_text = main_container.text
            # 把整個內容切割透過 "-- " 切割成2個陣列
            pre_text = all_text.split('--')[0]

            # 把每段文字 根據 '\n' 切開
            texts = pre_text.split('\n')
            # 如果你爬多篇你會發現 
            contents = texts[2:]
            # 內容
            content = '\n'.join(contents)

        #可用此語法印出文章內容:print(content)

        #推文
        pushs=root.find_all(class_="f3 push-content") 
        #可用此語法印出推文:print(pushs)

        #推文ID
        push_userid=root.find_all(class_="f3 hl push-userid")
        #可用此語法印出推文ID:print(push_userid)

        #將推文ID存入空的陣列，排除掉已被刪除的推文
        id=[]
        for userid in push_userid:
            if userid != None: 
                id.append(userid.string)
        #可用此語法印出結果:print(userid.string) 

        #將推文內容存入空的陣列，排除掉已被刪除的推文
        comment=[]
        for push in pushs:
            if push != None: 
                comment.append(push.string)
        #可用此語法印出結果:print(push.string) 

        #為提升可讀性，將推文ID跟內容用迴圈整合在一起，存入空的陣列，排除掉已被刪除的推文
        id_comment=[]
        for i in range(len(id)):
            if id[i] and comment[i] != None:
                id_comment.append(id[i]+comment[i])
            #print(id_comment)

        # 由於PTT留言會有字數限制,因此這邊會再針對留言進行加工,將相同帳號的留言合併
        comments = id_comment

        # 將留言合併
        merged_comments = defaultdict(list)

        for comment in comments:
            # 使用冒號分割帳號和留言內容
            parts = comment.split(':')
            if len(parts) == 2:
                username = parts[0].strip()
                message = parts[1].strip()

                # 將留言加入字典中的相應帳號
                merged_comments[username].append(message)
                
        # 將合併的留言以迴圈加進陣列中
        formatted_comments = []
        for username, messages in merged_comments.items():
            formatted_comments.append(f"{username} : {''.join(messages)}")

        #可用此語法印出合併後的留言
        #for comment in formatted_comments:
            #print(comment)
            
        format_comments = '\n'.join(formatted_comments)

        dataset = dataset.append(pd.DataFrame(data={'i': page,
                                                    'date':date,
                                                    'title':title.a.string,
                                                    'link':websites,
                                                    'author':author,
                                                    'content':content,
                                                    'comment':format_comments
                                                    }, index = [0]), ignore_index = True)

#將 DataFrame 匯出成csv，可自行調整路徑跟名稱
path='希望存放的路徑'
dataset.to_csv(path + '/nissan_web_crawler.csv', index=False, encoding='utf-8')
```
## 未來規劃
後續將會陸續把輿情分析部分及API的練習補上~

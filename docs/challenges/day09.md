# 第9天：RSS摘要閱讀器

## 專案目標

開發一個命令列RSS閱讀器，用於獲取、解析和顯示來自不同RSS源的新聞和文章摘要。

## 專案需求

1. 支援添加、刪除和管理RSS源URL
2. 從指定的RSS源獲取最新的文章列表
3. 顯示文章的標題、發布日期、作者和摘要
4. 提供基本的過濾和搜索功能
5. 將RSS源保存到設定檔中，以便下次啟動時使用
6. 提供友好的終端用戶界面
7. 處理網絡連接和解析錯誤

## 實現提示

### 基本結構
```nim
# rss_reader.nim
import httpclient, xmlparser, xmltree, os, times, json, strutils, uri, strformat

type
  RSSItem = object
    title: string
    link: string
    description: string
    pubDate: string
    author: string

  RSSFeed = object
    title: string
    url: string
    items: seq[RSSItem]

  RSSReader = object
    feeds: seq[RSSFeed]

proc loadFeeds(filename: string): seq[RSSFeed] =
  # 從設定檔加載RSS源

proc saveFeeds(feeds: seq[RSSFeed], filename: string) =
  # 保存RSS源到設定檔

proc fetchFeed(url: string): RSSFeed =
  # 從URL獲取RSS內容並解析

proc displayFeedItems(feed: RSSFeed, count: int = 10) =
  # 顯示指定RSS源的文章列表

proc searchItems(feeds: seq[RSSFeed], keyword: string): seq[RSSItem] =
  # 在所有RSS源中搜索包含關鍵字的文章

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### RSS解析
- 使用`httpclient`模組獲取RSS源內容
- 使用`xmlparser`和`xmltree`解析XML格式
- 提取標題、鏈接、描述等相關信息

### 網絡處理
- 處理HTTP請求和錯誤
- 實現超時和重試機制
- 考慮錯誤的RSS格式情況

### 用戶界面
- 創建清晰的菜單系統
- 格式化顯示文章列表和摘要
- 提供分頁瀏覽功能

## 進階挑戰

1. 添加文章更新通知功能
2. 實現文章內容的完整獲取和顯示
3. 支援將文章保存為離線閱讀
4. 添加標籤和分類功能
5. 實現RSS源自動發現（從網站URL自動找出RSS源）
6. 創建更豐富的終端UI，包括顏色和格式

## 專案結構

```
day09/
├── rss_reader.nim    # 主程式檔案
├── feeds.json        # RSS源設定檔
└── README.md         # 專案說明
```

## 學習資源

- [Nim標準庫：httpclient](https://nim-lang.org/docs/httpclient.html)
- [Nim標準庫：xmlparser](https://nim-lang.org/docs/xmlparser.html)
- [Nim標準庫：xmltree](https://nim-lang.org/docs/xmltree.html)
- [Nim標準庫：uri](https://nim-lang.org/docs/uri.html)

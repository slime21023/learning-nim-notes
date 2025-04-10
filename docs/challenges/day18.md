# 第18天：簡易網頁爬蟲

## 專案目標

開發一個基本的網頁爬蟲，能夠訪問網頁、提取內容和鏈接，並根據特定規則進行資料收集。

## 專案需求

1. 實現以下基本功能：
   - 訪問指定URL並獲取頁面內容
   - 解析HTML並提取信息
   - 提取頁面中的鏈接並支援後續訪問
   - 保存爬取的內容到檔案

2. 支援以下爬蟲功能：
   - 深度爬取（通過設定最大深度）
   - 鏈接過濾（只訪問特定網域或路徑）
   - 內容過濾（使用正則表達式或選擇器）
   - 延遲或間隔訪問（避免過度請求）

3. 提供友好的命令列界面，包括以下選項：
   - 起始URL
   - 爬取深度限制
   - 鏈接篩選模式
   - 內容提取規則
   - 輸出格式和存儲位置

4. 實現合理的錯誤處理和日誌記錄

## 實現提示

### 基本結構
```nim
# web_crawler.nim
import httpclient, htmlparser, xmltree, os, strutils, re, uri, strformat, parseopt, json, logging, times

type
  CrawlerConfig = object
    startUrl: string
    maxDepth: int
    delay: int  # 毫秒
    urlFilter: string  # 正則表達式
    contentSelector: string
    outputDir: string
    outputFormat: string  # text, json, html
    userAgent: string
    maxPages: int
    logLevel: Level

  PageResult = object
    url: string
    title: string
    content: seq[string]
    links: seq[string]
    depth: int
    timestamp: Time

proc initCrawler(config: CrawlerConfig): Crawler =
  # 初始化爬蟲

proc crawlPage(crawler: Crawler, url: string, depth: int): Future[PageResult] {.async.} =
  # 爬取單個頁面並返回結果

proc extractLinks(html: XmlNode, baseUrl: string, urlFilter: Regex): seq[string] =
  # 從HTML中提取鏈接

proc extractContent(html: XmlNode, selector: string): seq[string] =
  # 使用選擇器從HTML中提取內容

proc normalizeUrl(url, baseUrl: string): string =
  # 標準化URL，處理相對路徑等

proc savePage(result: PageResult, config: CrawlerConfig) =
  # 保存爬取結果到檔案

proc crawl(crawler: Crawler, startUrl: string) {.async.} =
  # 主爬蟲邏輯，遞迴爬取頁面

proc parseCommandLine(): CrawlerConfig =
  # 解析命令列參數

proc main() {.async.} =
  # 主程式邏輯

when isMainModule:
  waitFor main()
```

### HTML解析
- 使用`htmlparser`和`xmltree`解析HTML內容
- 實現簡單的CSS選擇器或XPath功能
- 處理HTML實體和編碼問題

### URL處理
- 使用`uri`模組處理URL解析和合併
- 實現URL規範化和去重
- 使用正則表達式過濾URL

### 網絡請求
- 使用`httpclient`模組發送HTTP請求
- 設置請求頭和用戶代理
- 處理重定向和錯誤響應

## 進階挑戰

1. 實現分佈式爬蟲（多機或多進程協作）
2. 添加網頁截圖功能
3. 實現URL優先級隊列
4. 添加表單提交和登入功能
5. 整合資料庫儲存爬取結果
6. 實現復原功能（中斷後可以繼續爬取）
7. 添加機器學習或啟發式方法來識別重要內容

## 專案結構

```
day18/
├── web_crawler.nim    # 主程式檔案
├── output/            # 爬取結果儲存目錄
└── README.md          # 專案說明
```

## 學習資源

- [Nim標準庫：httpclient](https://nim-lang.org/docs/httpclient.html)
- [Nim標準庫：htmlparser](https://nim-lang.org/docs/htmlparser.html)
- [Nim標準庫：xmltree](https://nim-lang.org/docs/xmltree.html)
- [網頁爬蟲的法律和道德問題](https://www.robotstxt.org/robotstxt.html)

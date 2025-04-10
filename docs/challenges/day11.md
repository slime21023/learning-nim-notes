# 第11天：HTTP檔案伺服器

## 專案目標

開發一個簡單的HTTP檔案伺服器，允許使用者通過網頁瀏覽和下載本地目錄中的檔案。

## 專案需求

1. 提供指定目錄中檔案的HTTP訪問
2. 實現基本的目錄瀏覽功能，顯示檔案和子目錄
3. 支援各種檔案類型的下載
4. 提供簡單的HTML界面，包括：
   - 檔案列表，顯示名稱、大小和修改日期
   - 返回上級目錄的鏈接
   - 基本的檔案搜索功能
5. 支援自定義的埠號和根目錄路徑
6. 實現基本的並發連接處理

## 實現提示

### 基本結構
```nim
# http_file_server.nim
import asynchttpserver, asyncdispatch, os, strutils, strformat, times, mimetypes

type
  ServerConfig = object
    port: int
    rootDir: string
    allowDirectoryListing: bool
    maxConnections: int

proc initServerConfig(): ServerConfig =
  # 初始化伺服器配置，設置預設值

proc serveDirectory(req: Request, path: string, config: ServerConfig): Future[void] {.async.} =
  # 為目錄請求提供HTML列表頁面

proc serveFile(req: Request, path: string): Future[void] {.async.} =
  # 提供檔案下載

proc handleRequest(req: Request, config: ServerConfig): Future[void] {.async.} =
  # 處理HTTP請求，判斷是檔案請求還是目錄請求

proc startServer(config: ServerConfig) {.async.} =
  # 啟動HTTP伺服器，監聽指定埠

proc parseCommandLine(): ServerConfig =
  # 解析命令列參數，更新伺服器配置

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### HTTP伺服器
- 使用`asynchttpserver`建立HTTP伺服器
- 使用`asyncdispatch`處理非同步請求
- 處理不同的HTTP方法（GET、HEAD等）

### 檔案系統操作
- 使用`os`模組處理目錄和檔案操作
- 檢查路徑安全性，防止目錄遍歷攻擊
- 使用`mimetypes`模組檢測檔案MIME類型

### HTML生成
- 創建簡單的HTML頁面用於目錄瀏覽
- 格式化檔案大小和時間戳
- 添加基本的CSS樣式

## 進階挑戰

1. 添加基本的身份驗證功能
2. 實現檔案上傳功能
3. 添加最近訪問和熱門下載統計
4. 實現簡單的快取機制，提高性能
5. 添加圖像預覽和文本檔案在線查看功能
6. 實現ZIP檔案的即時壓縮下載（選擇多個檔案打包下載）

## 專案結構

```
day11/
├── http_file_server.nim    # 主程式檔案
├── public/                 # 示例檔案目錄
│   ├── images/
│   ├── documents/
│   └── index.html
└── README.md               # 專案說明
```

## 學習資源

- [Nim標準庫：asynchttpserver](https://nim-lang.org/docs/asynchttpserver.html)
- [Nim標準庫：asyncdispatch](https://nim-lang.org/docs/asyncdispatch.html)
- [Nim標準庫：mimetypes](https://nim-lang.org/docs/mimetypes.html)
- [HTTP協議基礎](https://developer.mozilla.org/zh-TW/docs/Web/HTTP)

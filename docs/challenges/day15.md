# 第15天：並發下載器

## 專案目標

開發一個支援多線程或非同步IO的檔案下載器，能夠同時下載多個檔案並顯示下載進度。

## 專案需求

1. 支援從HTTP/HTTPS URL下載檔案
2. 實現並發下載功能：
   - 同時下載多個檔案
   - 單個檔案的分段並發下載（可選）
3. 顯示下載進度和速度
4. 支援暫停、恢復和取消下載
5. 實現命令列界面並支援以下功能：
   - 直接下載單個URL
   - 從文本檔案批量載入URL
   - 指定下載目錄和檔案名稱
6. 處理網絡錯誤和重試機制

## 實現提示

### 基本結構
```nim
# concurrent_downloader.nim
import asyncdispatch, httpclient, os, strutils, strformat, parseopt, times, streams

type
  DownloadTask = object
    url: string
    targetPath: string
    size: int64
    downloaded: int64
    status: DownloadStatus
    startTime: Time
    speed: float
  
  DownloadStatus = enum
    dsQueued, dsDownloading, dsPaused, dsCompleted, dsFailed
  
  DownloaderConfig = object
    maxConcurrent: int
    retryCount: int
    retryDelay: int  # 毫秒
    connectionTimeout: int  # 秒
    downloadDir: string

proc downloadFile(url, targetPath: string, config: DownloaderConfig): Future[void] {.async.} =
  # 非同步下載單個檔案

proc updateProgress(task: var DownloadTask) =
  # 更新並顯示下載進度

proc downloadManager(urls: seq[string], config: DownloaderConfig) {.async.} =
  # 管理多個下載任務並控制並發數

proc downloadFromList(filepath: string, config: DownloaderConfig) {.async.} =
  # 從檔案載入URL並下載

proc parseCommandLine(): tuple[urls: seq[string], listFile: string, config: DownloaderConfig] =
  # 解析命令列參數

proc main() {.async.} =
  # 主程式邏輯

when isMainModule:
  waitFor main()
```

### 並發處理
- 使用`asyncdispatch`和`httpclient`實現非同步下載
- 控制並發下載數量，避免過載
- 實現任務隊列和優先級管理

### 進度顯示
- 計算下載速度和剩餘時間
- 使用進度條或百分比顯示下載進度
- 實現實時更新的終端輸出

### 錯誤處理
- 處理網絡連接問題
- 實現智能重試機制
- 支援下載恢復（如果伺服器支援）

## 進階挑戰

1. 實現單檔案的分段並發下載
2. 添加下載限速功能
3. 支援FTP和其他協議
4. 實現下載隊列的持久化（程式關閉後可恢復）
5. 添加代理伺服器支援
6. 創建簡單的API或界面，允許其他程式使用下載功能

## 專案結構

```
day15/
├── concurrent_downloader.nim    # 主程式檔案
├── urls.txt                    # 示例URL列表
└── README.md                   # 專案說明
```

## 學習資源

- [Nim標準庫：asyncdispatch](https://nim-lang.org/docs/asyncdispatch.html)
- [Nim標準庫：httpclient](https://nim-lang.org/docs/httpclient.html)
- [Nim標準庫：streams](https://nim-lang.org/docs/streams.html)
- [HTTP範圍請求](https://developer.mozilla.org/en-US/docs/Web/HTTP/Range_requests)

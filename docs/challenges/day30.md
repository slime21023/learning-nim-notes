# 第30天：跨平台桌面應用

## 專案目標

開發一個功能完整的跨平台桌面應用程式，整合前面所學的各種技術，並實現良好的用戶體驗和實用功能。

## 專案需求

1. 創建一個適合日常使用的桌面應用，如：
   - 筆記和知識管理工具
   - 專案管理和任務追蹤系統
   - 媒體管理或編輯工具
   - 系統工具和實用程式
   - 資料分析和視覺化應用

2. 實現跨平台支援：
   - Windows
   - Linux
   - macOS（可選）

3. 提供現代化用戶界面：
   - 直觀的操作邏輯
   - 美觀的視覺設計
   - 響應式布局
   - 主題和個性化選項

4. 整合以下至少三種技術：
   - 資料持久化（檔案、資料庫）
   - 網絡功能（API或雲同步）
   - 多線程或非同步處理
   - 外部系統集成
   - 資料分析或處理

5. 實現完整的應用生命週期管理：
   - 安裝和卸載
   - 設置和配置
   - 更新機制
   - 錯誤報告和日誌

## 實現提示

### 架構選擇
可以選擇以下其中一種架構：

1. **原生GUI + Nim核心**
   ```nim
   # 使用nimx或nigui等原生GUI庫
   import nigui, os, json, asyncdispatch
   
   app.init()
   
   # 創建主視窗和界面組件
   var window = newWindow("我的跨平台應用")
   window.width = 800
   window.height = 600
   
   # 設置應用邏輯和事件處理
   # ...
   
   # 啟動應用
   window.show()
   app.run()
   ```

2. **Web技術 + Nim後端**
   ```nim
   # 使用webview嵌入HTML/CSS/JS界面
   import webview, jester, asyncdispatch, os
   
   # 啟動API伺服器
   proc startServer() {.async.} =
     var router: Router
     router.get "/api/data":
       resp %*{"status": "success", "data": getData()}
     
     router.post "/api/save":
       let data = parseJson(request.body)
       saveData(data)
       resp %*{"status": "success"}
     
     let settings = newSettings(port=8000)
     var server = initJester(router, settings=settings)
     server.serve()
   
   # 創建WebView
   proc main() =
     asyncCheck startServer()
     var webview = newWebView("我的跨平台應用", "http://localhost:8000/index.html", 800, 600)
     webview.run()
   
   when isMainModule:
     main()
   ```

3. **跨平台框架 + Nim邏輯**
   ```nim
   # 使用如Qt或Electron的跨平台框架並與Nim集成
   # ...
   ```

### 數據管理
```nim
# 資料持久化模組
# data_manager.nim
import json, os, db_sqlite, strutils, times

type
  DataManager = ref object
    dbPath: string
    conn: DbConn

proc newDataManager(dbPath: string): DataManager =
  result = DataManager(dbPath: dbPath)
  result.conn = open(dbPath, "", "", "")
  
  # 初始化資料庫結構
  result.conn.exec(sql"""
    CREATE TABLE IF NOT EXISTS items (
      id INTEGER PRIMARY KEY,
      title TEXT NOT NULL,
      content TEXT,
      tags TEXT,
      created_at TEXT,
      updated_at TEXT
    )
  """)

proc saveItem(dm: DataManager, title, content, tags: string): int64 =
  # 儲存新項目
  let now = $now()
  result = dm.conn.insertID(sql"""
    INSERT INTO items (title, content, tags, created_at, updated_at)
    VALUES (?, ?, ?, ?, ?)
  """, title, content, tags, now, now)

proc updateItem(dm: DataManager, id: int64, title, content, tags: string): bool =
  # 更新現有項目
  let now = $now()
  result = dm.conn.tryExec(sql"""
    UPDATE items
    SET title = ?, content = ?, tags = ?, updated_at = ?
    WHERE id = ?
  """, title, content, tags, now, id)

proc getItem(dm: DataManager, id: int64): JsonNode =
  # 獲取單個項目
  # ...

proc getAllItems(dm: DataManager): JsonNode =
  # 獲取所有項目
  # ...

proc searchItems(dm: DataManager, query: string): JsonNode =
  # 搜索項目
  # ...

proc close(dm: DataManager) =
  # 關閉資料庫連接
  dm.conn.close()
```

### 應用打包與分發
```nim
# nimble檔案
# my_app.nimble
version       = "0.1.0"
author        = "Your Name"
description   = "Cross-platform desktop application"
license       = "MIT"
srcDir        = "src"
bin           = @["my_app"]

requires "nim >= 1.4.0"
requires "nigui >= 0.2.6"
requires "db_sqlite >= 0.1.0"
```

使用`nimble`管理依賴並構建應用：
```
nimble build -d:release
```

### 現代化UI設計
- 使用響應式布局適應不同屏幕尺寸
- 實現淺色/深色主題模式
- 添加適當的動畫和過渡效果
- 遵循平台相關的設計準則

## 進階挑戰

1. 實現雲同步功能，允許跨裝置使用
2. 添加插件系統，支援第三方擴展
3. 實現離線工作模式和數據同步
4. 添加高級搜索和過濾功能
5. 實現多語言支援和國際化
6. 添加輔助功能支援（無障礙設計）
7. 實現自動更新機制

## 專案結構

```
day30/
├── src/                   # 源代碼目錄
│   ├── my_app.nim         # 主程式入口
│   ├── ui/                # 用戶界面模組
│   │   ├── main_window.nim
│   │   └── components.nim
│   ├── data/              # 資料管理模組
│   │   ├── data_manager.nim
│   │   └── models.nim
│   ├── utils/             # 工具函數
│   │   ├── config.nim
│   │   └── logger.nim
│   └── net/               # 網絡相關模組
│       └── sync.nim
├── assets/                # 靜態資源
│   ├── images/
│   └── styles/
├── dist/                  # 發佈目錄
├── my_app.nimble          # 專案描述和構建腳本
└── README.md              # 專案說明
```

## 學習資源

- [NiGui文檔](https://github.com/simonkrauter/NiGui)
- [跨平台開發最佳實踐](https://www.qt.io/cross-platform-application-development)
- [現代UI設計原則](https://material.io/design)
- [桌面應用可用性指南](https://www.nngroup.com/articles/ten-usability-heuristics/)
- [應用打包和分發](https://nim-lang.org/docs/packaging.html)

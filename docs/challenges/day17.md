# 第17天：JSON設定管理器

## 專案目標

開發一個JSON設定檔案管理工具，用於讀取、修改和寫入配置文件，提供易用的命令列和程式庫API。

## 專案需求

1. 提供以下基本功能：
   - 讀取和解析JSON設定檔案
   - 獲取和設置配置項的值
   - 添加和刪除配置項
   - 格式化並保存更改到檔案
   
2. 支援以下配置訪問方式：
   - 點分隔路徑（如 "server.host"）
   - 數組索引（如 "users[0].name"）
   - 多層嵌套結構
   
3. 提供命令列界面，支援以下操作：
   - 讀取整個設定檔
   - 獲取特定配置項的值
   - 設置配置項的值
   - 從標準輸入讀取JSON更新配置
   
4. 設計一個可重用的模組，以便在其他專案中使用

## 實現提示

### 基本結構
```nim
# json_config_manager.nim
import json, os, strutils, parseopt, strformat

type
  ConfigManager = ref object
    data: JsonNode
    filename: string
    isDirty: bool  # 標記是否有未保存的更改

proc newConfigManager(filename: string): ConfigManager =
  # 創建一個新的配置管理器，從檔案載入配置

proc load(cm: ConfigManager, filename: string = ""): bool =
  # 從檔案載入JSON設定

proc save(cm: ConfigManager, filename: string = "", pretty: bool = true): bool =
  # 將當前配置保存到檔案

proc getPath(cm: ConfigManager, path: string): JsonNode =
  # 使用路徑獲取配置項的值

proc setPath(cm: ConfigManager, path: string, value: JsonNode): bool =
  # 使用路徑設置配置項的值

proc deletePath(cm: ConfigManager, path: string): bool =
  # 刪除指定路徑的配置項

proc mergePatch(cm: ConfigManager, patch: JsonNode): bool =
  # 合併JSON補丁到當前配置

proc parseCommandLine() =
  # 解析命令列參數並執行相應的操作

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### JSON處理
- 使用`json`模組解析和生成JSON數據
- 實現深層路徑解析和訪問
- 正確處理不同JSON數據類型

### 路徑解析
- 解析點分隔路徑（如 "server.host"）
- 處理數組索引（如 "users[0].name"）
- 處理不存在的路徑和類型錯誤

### 命令列界面
- 設計清晰的命令語法和説明信息
- 支援從檔案或標準輸入讀取JSON數據
- 提供格式化輸出選項

## 進階挑戰

1. 添加配置合併功能（合併多個配置檔案）
2. 實現配置驗證功能（基於JSON模式）
3. 支援配置版本控制和歷史記錄
4. 添加環境變數替換功能
5. 實現配置加密和保護敏感信息
6. 創建基於此工具的更高級的配置系統

## 專案結構

```
day17/
├── json_config_manager.nim    # 主程式檔案
├── examples/                  # 示例配置檔案
│   ├── config.json
│   └── patch.json
└── README.md                  # 專案說明
```

## 學習資源

- [Nim標準庫：json](https://nim-lang.org/docs/json.html)
- [JSON Patch規範](http://jsonpatch.com/)
- [JSON Path語法](https://goessner.net/articles/JsonPath/)

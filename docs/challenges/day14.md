# 第14天：SQLite資料管理器

## 專案目標

開發一個簡單的SQLite資料庫管理工具，提供基本的資料庫操作功能和直觀的命令列界面。

## 專案需求

1. 提供以下資料庫操作功能：
   - 連接/創建SQLite資料庫
   - 執行SQL查詢和命令
   - 顯示資料庫架構（表、索引等）
   - 導入/導出資料
2. 實現基本的表格管理功能：
   - 創建和刪除表格
   - 添加、編輯和刪除記錄
   - 查詢和過濾數據
3. 提供友好的命令列界面：
   - 交互式SQL提示符
   - 格式化的查詢結果顯示
   - 命令歷史記錄
4. 支援從檔案載入和執行SQL腳本
5. 實現基本的錯誤處理和錯誤報告

## 實現提示

### 基本結構
```nim
# sqlite_manager.nim
import db_sqlite, os, strutils, strformat, parseopt, tables

type
  ManagerConfig = object
    dbPath: string
    outputFormat: string  # 例如：table, csv, json
    historyFile: string
    maxResults: int

  QueryResult = object
    columns: seq[string]
    rows: seq[seq[string]]
    rowsAffected: int
    lastInsertRowid: int64

proc connectDatabase(dbPath: string): DbConn =
  # 連接到SQLite資料庫

proc executeQuery(db: DbConn, query: string): QueryResult =
  # 執行SQL查詢並返回結果

proc displayResult(result: QueryResult, format: string) =
  # 以指定格式顯示查詢結果

proc getTableSchema(db: DbConn, tableName: string): string =
  # 獲取表格結構信息

proc listTables(db: DbConn): seq[string] =
  # 列出資料庫中所有表格

proc importData(db: DbConn, filePath, tableName: string, delimiter: char = ',') =
  # 從CSV檔案導入數據到表格

proc exportData(db: DbConn, tableName, filePath: string, delimiter: char = ',') =
  # 將表格數據導出為CSV檔案

proc interactiveMode(db: DbConn, config: ManagerConfig) =
  # 進入交互式命令模式

proc parseCommandLine(): ManagerConfig =
  # 解析命令列參數

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### 資料庫操作
- 使用`db_sqlite`模組連接和操作SQLite資料庫
- 設計安全的查詢執行流程，防止SQL注入
- 處理事務和錯誤回滾

### 用戶界面
- 設計清晰的命令列界面和幫助訊息
- 實現命令歷史記錄和自動完成功能
- 格式化顯示結果，使用表格或其他格式

### 檔案處理
- 實現CSV、JSON等格式的導入導出
- 處理大型數據集的分批處理
- 支援不同的字符編碼和分隔符

## 進階挑戰

1. 實現數據備份和恢復功能
2. 添加資料庫性能監控和優化建議
3. 實現資料庫結構的可視化
4. 添加用戶權限管理功能
5. 支援與其他資料庫系統的連接和遷移
6. 實現簡單的ORM（物件關聯映射）功能

## 專案結構

```
day14/
├── sqlite_manager.nim    # 主程式檔案
├── example/              # 示例資料庫和腳本
│   ├── example.db
│   └── sample_queries.sql
└── README.md             # 專案說明
```

## 學習資源

- [Nim標準庫：db_sqlite](https://nim-lang.org/docs/db_sqlite.html)
- [SQLite官方文檔](https://www.sqlite.org/docs.html)
- [SQL基礎教程](https://www.w3schools.com/sql/)

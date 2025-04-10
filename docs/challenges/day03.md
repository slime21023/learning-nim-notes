# 第3天：簡單待辦事項列表

## 專案目標

創建一個命令列待辦事項管理器，允許使用者添加、刪除和查看任務，並將數據保存到檔案中。

## 專案需求

1. 允許使用者添加新任務，每個任務包含標題和可選的描述
2. 支援任務狀態管理（待完成/已完成）
3. 提供列出所有任務的功能，並顯示其狀態
4. 支援按編號刪除任務
5. 將任務數據保存到檔案中，程式重啟後可以讀取
6. 提供清晰的命令列界面和使用指南

## 實現提示

### 基本結構
```nim
# todo_list.nim
import os, json, strutils, strformat

type
  Task = object
    title: string
    description: string
    completed: bool

  TodoList = object
    tasks: seq[Task]

proc saveToFile(list: TodoList, filename: string) =
  # 將任務列表保存到檔案

proc loadFromFile(filename: string): TodoList =
  # 從檔案加載任務列表

proc addTask(list: var TodoList, title, description: string) =
  # 添加新任務

proc markAsCompleted(list: var TodoList, taskIndex: int) =
  # 將任務標記為已完成

proc deleteTask(list: var TodoList, taskIndex: int) =
  # 刪除任務

proc displayTasks(list: TodoList) =
  # 顯示所有任務

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### 文件操作
- 使用`json`模組處理數據序列化和反序列化
- 使用`readFile`和`writeFile`進行檔案讀寫
- 處理檔案不存在的情況

### 用戶交互
- 使用`readLine(stdin)`獲取使用者輸入
- 使用`parseCmdLine`解析命令列參數
- 提供清晰的操作菜單

### 數據結構
- 使用`seq[Task]`存儲任務列表
- 在`Task`物件中存儲任務詳細信息
- 使用`json`模組將物件轉換為JSON格式

## 進階挑戰

1. 添加任務優先級（高/中/低）
2. 實現任務截止日期和提醒功能
3. 支援任務標籤和基於標籤的過濾
4. 添加任務搜索功能
5. 實現撤銷/重做功能
6. 提供更豐富的終端用戶界面（TUI）

## 專案結構

```
day03/
├── todo_list.nim    # 主程式檔案
├── tasks.json       # 任務數據儲存檔案
└── README.md        # 專案說明
```

## 學習資源

- [Nim標準庫：json](https://nim-lang.org/docs/json.html)
- [Nim標準庫：os](https://nim-lang.org/docs/os.html)
- [Nim中的檔案操作](https://nim-lang.org/docs/io.html)

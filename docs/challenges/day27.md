# 第27天：分散式計算實驗

## 專案目標

開發一個簡單的分散式計算框架，實現主從架構的任務分配系統，並用它解決一個適合並行處理的問題。

## 專案需求

1. 實現分散式計算框架：
   - 主節點（Master）負責任務分配和結果收集
   - 工作節點（Worker）負責執行具體計算任務
   - 支援任務分割和結果合併
   - 處理節點故障和任務重分配
   
2. 實現網絡通信：
   - 使用TCP/IP或HTTP進行節點間通信
   - 設計簡單的通信協議
   - 支援節點註冊和發現
   
3. 設計並實現一個測試問題，如：
   - 大數據集的並行處理
   - 密碼破解或加密運算
   - 圖像或視頻處理
   - 蒙特卡羅模擬
   
4. 提供性能監控和報告功能

## 實現提示

### 基本結構
```nim
# 主節點程式
# master.nim
import asyncdispatch, asynchttpserver, httpclient, json, tables, strutils, times, os, strformat

type
  Task = object
    id: string
    data: JsonNode
    status: TaskStatus
    createdAt: Time
    assignedTo: string
    attempts: int
  
  TaskStatus = enum
    tsPending, tsAssigned, tsCompleted, tsFailed
  
  Worker = object
    id: string
    url: string
    lastSeen: Time
    tasksAssigned: int
    tasksCompleted: int
  
  Master = object
    tasks: TableRef[string, Task]
    pendingTasks: seq[string]
    workers: TableRef[string, Worker]
    results: JsonNode
    server: AsyncHttpServer

proc newMaster(): Master =
  # 初始化主節點

proc generateTasks(master: var Master, problem: JsonNode) =
  # 將問題分割為多個任務

proc assignTasks(master: var Master) {.async.} =
  # 將任務分配給可用的工作節點

proc collectResults(master: var Master): JsonNode =
  # 收集和合併所有計算結果

proc checkWorkers(master: var Master) {.async.} =
  # 檢查工作節點健康狀態並重新分配失敗任務

proc handleRequest(master: var Master, req: Request) {.async.} =
  # 處理HTTP請求（工作節點註冊、結果提交等）

proc startServer(master: var Master, port: int) {.async.} =
  # 啟動HTTP伺服器監聽請求

proc main() {.async.} =
  # 主程式邏輯

when isMainModule:
  waitFor main()
```

```nim
# 工作節點程式
# worker.nim
import asyncdispatch, httpclient, json, os, strutils, times, strformat, random

type
  Worker = object
    id: string
    masterUrl: string
    client: AsyncHttpClient
    busy: bool

proc newWorker(masterUrl: string): Worker =
  # 初始化工作節點

proc register(worker: Worker) {.async.} =
  # 向主節點註冊

proc requestTask(worker: var Worker) {.async.} =
  # 從主節點請求任務

proc processTask(worker: Worker, task: JsonNode): JsonNode =
  # 處理分配的任務

proc submitResult(worker: var Worker, taskId: string, result: JsonNode) {.async.} =
  # 提交計算結果到主節點

proc heartbeat(worker: Worker) {.async.} =
  # 發送心跳信號維持與主節點的連接

proc workLoop(worker: var Worker) {.async.} =
  # 工作節點主循環

proc main() {.async.} =
  # 主程式邏輯

when isMainModule:
  randomize()
  waitFor main()
```

### 任務分割與管理
- 將大型問題分解為獨立的子任務
- 實現任務隊列和優先級管理
- 設計合理的任務分配策略
- 處理任務超時和失敗情況

### 通信協議
- 使用JSON作為數據交換格式
- 設計簡單的RESTful API或自定義協議
- 處理網絡連接問題和錯誤重試
- 實現節點發現和健康檢查

### 測試問題
- 實現一個有意義的計算問題
- 設計適合並行處理的數據結構
- 確保結果的正確性和一致性
- 比較串行和並行處理的性能差異

## 進階挑戰

1. 實現動態任務調度和負載平衡
2. 添加任務依賴關係處理
3. 實現工作節點的自動擴展和縮減
4. 添加結果校驗和容錯機制
5. 支援不同類型的計算任務
6. 實現任務進度追踪和可視化

## 專案結構

```
day27/
├── master.nim           # 主節點程式
├── worker.nim           # 工作節點程式
├── tasks/               # 任務處理模組
│   ├── common.nim
│   └── monte_carlo.nim  # 示例任務實現
├── results/             # 結果輸出目錄
└── README.md            # 專案說明
```

## 學習資源

- [分散式系統基礎](https://www.distributed-systems.net/index.php/books/distributed-systems-3rd-edition-2017/)
- [Nim標準庫：asyncdispatch](https://nim-lang.org/docs/asyncdispatch.html)
- [Nim標準庫：asynchttpserver](https://nim-lang.org/docs/asynchttpserver.html)
- [MapReduce程式設計模型](https://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf)

# 第28天：嵌入式資料庫

## 專案目標

開發一個輕量級的嵌入式資料庫，提供基本的資料儲存、查詢和管理功能，可以嵌入到其他應用程式中使用。

## 專案需求

1. 實現資料庫核心功能：
   - 資料儲存和檢索
   - 基本的CRUD操作（建立、讀取、更新、刪除）
   - 簡單的查詢語言或API
   - 索引支援
   
2. 設計資料儲存格式：
   - 自定義的檔案格式
   - 或使用JSON/BSON等已有格式
   - 支援不同的資料類型
   
3. 提供以下特性：
   - 交易支援（原子操作、一致性）
   - 並發控制（讀寫鎖或MVCC）
   - 持久化和恢復
   - 記憶體快取
   
4. 設計程式庫API以便於其他應用程式使用

## 實現提示

### 基本結構
```nim
# embedded_db.nim
import tables, hashes, os, streams, strutils, sequtils, times, json, locks, endians

type
  DataType = enum
    dtNull, dtInteger, dtFloat, dtString, dtBoolean, dtArray, dtObject, dtBinary
  
  Value = object
    case kind: DataType
    of dtNull: discard
    of dtInteger: intVal: int64
    of dtFloat: floatVal: float64
    of dtString: strVal: string
    of dtBoolean: boolVal: bool
    of dtArray: arrayVal: seq[Value]
    of dtObject: objVal: Table[string, Value]
    of dtBinary: binVal: seq[byte]
  
  IndexType = enum
    itHash, itBTree
  
  Index = object
    name: string
    field: string
    case kind: IndexType
    of itHash: hashIndex: Table[Hash, seq[uint64]]
    of itBTree: discard  # B-樹索引實現
  
  Collection = object
    name: string
    docs: Table[uint64, Value]
    indices: seq[Index]
    nextId: uint64
    lock: Lock
  
  Transaction = object
    id: uint64
    startTime: Time
    collections: seq[string]
    operations: seq[Operation]
    committed: bool
  
  OperationType = enum
    otInsert, otUpdate, otDelete, otQuery
  
  Operation = object
    case kind: OperationType
    of otInsert, otUpdate:
      collection: string
      docId: uint64
      value: Value
    of otDelete:
      delCollection: string
      delId: uint64
    of otQuery: discard  # 查詢操作不需要記錄
  
  Database = object
    path: string
    collections: Table[string, Collection]
    transactions: Table[uint64, Transaction]
    nextTxId: uint64
    journalEnabled: bool
    globalLock: Lock

proc newDatabase(path: string): Database =
  # 初始化資料庫

proc openDatabase(path: string): Database =
  # 打開現有資料庫

proc createCollection(db: var Database, name: string) =
  # 創建新的集合

proc dropCollection(db: var Database, name: string) =
  # 刪除集合

proc beginTransaction(db: var Database): uint64 =
  # 開始新交易

proc commitTransaction(db: var Database, txId: uint64): bool =
  # 提交交易

proc rollbackTransaction(db: var Database, txId: uint64) =
  # 回滾交易

proc insert(db: var Database, collection: string, value: Value, txId: uint64 = 0): uint64 =
  # 插入文檔

proc update(db: var Database, collection: string, id: uint64, value: Value, txId: uint64 = 0): bool =
  # 更新文檔

proc delete(db: var Database, collection: string, id: uint64, txId: uint64 = 0): bool =
  # 刪除文檔

proc findById(db: Database, collection: string, id: uint64): Option[Value] =
  # 按ID查找文檔

proc createIndex(db: var Database, collection, field: string, indexType: IndexType = itHash): bool =
  # 創建索引

proc query(db: Database, collection: string, filter: proc(v: Value): bool): seq[Value] =
  # 根據過濾條件查詢文檔

proc queryByField(db: Database, collection, field: string, value: Value): seq[Value] =
  # 根據字段值查詢文檔

proc saveDatabase(db: Database) =
  # 將資料庫保存到磁碟

proc loadDatabase(path: string): Database =
  # 從磁碟載入資料庫

proc main() =
  # 示例用法

when isMainModule:
  main()
```

### 資料儲存
- 設計高效的磁碟儲存格式
- 實現分頁或塊管理
- 考慮壓縮和加密選項
- 處理記憶體和磁碟間的數據交換

### 索引和查詢
- 實現哈希索引或B樹索引
- 設計簡單但強大的查詢API
- 支援基本的比較運算符和邏輯運算符
- 優化查詢執行計劃

### 事務和並發
- 實現ACID特性（原子性、一致性、隔離性、持久性）
- 使用讀寫鎖或MVCC（多版本並發控制）
- 處理死鎖檢測和預防
- 實現日誌和檢查點機制

## 進階挑戰

1. 實現SQL子集作為查詢語言
2. 添加全文搜索功能
3. 支援資料庫遷移和版本控制
4. 實現複雜索引（複合索引、地理空間索引等）
5. 添加主從複製功能
6. 實現分佈式資料庫和分片

## 專案結構

```
day28/
├── embedded_db.nim     # 資料庫核心實現
├── storage.nim         # 儲存引擎
├── index.nim           # 索引實現
├── query.nim           # 查詢處理器
├── transaction.nim     # 交易管理
├── examples/           # 使用範例
│   └── simple_app.nim
└── README.md           # 專案說明
```

## 學習資源

- [資料庫系統概念](https://www.db-book.com/)
- [儲存引擎設計](https://www.cs.umd.edu/~abadi/papers/abadi-cacm2012.pdf)
- [B樹和B+樹](https://en.wikipedia.org/wiki/B-tree)
- [MVCC並發控制](https://en.wikipedia.org/wiki/Multiversion_concurrency_control)

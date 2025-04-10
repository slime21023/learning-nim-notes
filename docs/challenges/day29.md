# 第29天：編譯時代碼產生器

## 專案目標

利用Nim強大的編譯時元編程功能，開發一個代碼生成器，用於自動產生重複性代碼或基於模式的代碼，如ORM、序列化器等。

## 專案需求

1. 設計一個代碼產生系統，可用於以下場景之一：
   - 對象關係映射（ORM）
   - 序列化/反序列化
   - RPC（遠程過程調用）存根
   - GUI控件綁定
   - 狀態機生成
   
2. 實現以下編譯時功能：
   - 類型檢查與檢驗
   - 代碼生成與轉換
   - 編譯時錯誤報告
   - 自定義註解處理
   
3. 提供直觀的API：
   - 清晰的註解或標記語法
   - 低侵入性的設計
   - 可擴展的框架
   
4. 創建示例和測試用例，展示代碼生成器的使用方法

## 實現提示

### 基本結構（以ORM代碼生成器為例）
```nim
# orm_generator.nim
import macros, strutils, strformat, tables, typetraits, options

# 模型註解巨集
macro model*(typeDecl: untyped): untyped =
  # 檢查類型定義的正確性
  expectKind(typeDecl, nnkTypeDef)
  
  # 複製原始類型定義
  result = typeDecl.copy
  
  # 提取類型名稱和欄位
  let
    typeName = typeDecl[0]
    typeImpl = typeDecl[2]
  
  # 檢查是否為對象類型
  if typeImpl.kind != nnkObjectTy:
    error("Model macro can only be applied to object types", typeImpl)
    return
  
  # 提取欄位定義
  var fields: seq[tuple[name: string, typ: NimNode]] = @[]
  if typeImpl[2].kind == nnkRecList:
    for identDef in typeImpl[2]:
      for i in 0 ..< identDef.len - 2:
        let
          fieldName = identDef[i]
          fieldType = identDef[^2]
        fields.add((name: $fieldName, typ: fieldType))
  
  # 生成ORM函數
  var
    createTableProc = genCreateTableProc(typeName, fields)
    insertProc = genInsertProc(typeName, fields)
    updateProc = genUpdateProc(typeName, fields)
    deleteProc = genDeleteProc(typeName, fields)
    findByIdProc = genFindByIdProc(typeName, fields)
    findAllProc = genFindAllProc(typeName, fields)
  
  # 將生成的函數添加到結果
  result = newStmtList(
    result,
    createTableProc,
    insertProc,
    updateProc,
    deleteProc,
    findByIdProc,
    findAllProc
  )

# 欄位註解巨集
macro column*(fieldDecl: untyped, options: varargs[untyped]): untyped =
  # 處理欄位註解，如主鍵、索引等
  result = fieldDecl.copy
  
  # 解析選項
  # ...

# 數據庫連接配置巨集
macro setupDatabase*(connectionString: string): untyped =
  # 生成資料庫連接和初始化代碼
  # ...

# 代碼生成輔助函數
proc genCreateTableProc(typeName: NimNode, fields: seq[tuple[name: string, typ: NimNode]]): NimNode =
  # 生成創建表函數
  # ...

proc genInsertProc(typeName: NimNode, fields: seq[tuple[name: string, typ: NimNode]]): NimNode =
  # 生成插入函數
  # ...

proc genUpdateProc(typeName: NimNode, fields: seq[tuple[name: string, typ: NimNode]]): NimNode =
  # 生成更新函數
  # ...

proc genDeleteProc(typeName: NimNode, fields: seq[tuple[name: string, typ: NimNode]]): NimNode =
  # 生成刪除函數
  # ...

proc genFindByIdProc(typeName: NimNode, fields: seq[tuple[name: string, typ: NimNode]]): NimNode =
  # 生成按ID查詢函數
  # ...

proc genFindAllProc(typeName: NimNode, fields: seq[tuple[name: string, typ: NimNode]]): NimNode =
  # 生成查詢所有記錄函數
  # ...

# 使用範例
when isMainModule:
  import db_sqlite
  
  # 設置數據庫
  setupDatabase("test.db")
  
  # 定義模型
  model:
    type User = object
      id {.column(primaryKey).}: int
      name {.column(unique).}: string
      email {.column(unique).}: string
      age {.column.}: int
  
  # 創建表
  createTable(User)
  
  # 使用生成的函數
  var user = User(name: "Test User", email: "test@example.com", age: 30)
  let id = insert(user)
  echo "Inserted user with ID: ", id
  
  user.age = 31
  update(user)
  
  let foundUser = findById(User, id)
  echo "Found user: ", foundUser
  
  delete(user)
```

### 編譯時編程
- 使用`macros`模組操作抽象語法樹（AST）
- 理解Nim的語法節點類型和結構
- 生成類型安全和高效的代碼
- 提供清晰的編譯時錯誤信息

### 代碼生成設計
- 識別重複模式並設計適當的抽象
- 平衡自動生成與手動定制之間的權衡
- 考慮生成代碼的可讀性和調試性
- 提供詳細的文檔和示例

### 性能優化
- 利用編譯時執行減少運行時開銷
- 生成針對特定使用場景優化的代碼
- 考慮記憶體使用和緩存友好性
- 測量生成代碼的性能

## 進階挑戰

1. 實現跨數據庫支援（SQLite, MySQL, PostgreSQL等）
2. 添加更複雜的關聯關係（一對多、多對多）
3. 支援模式遷移和版本控制
4. 實現延遲加載和預加載策略
5. 添加查詢構建器DSL
6. 支援自定義驗證和轉換

## 專案結構

```
day29/
├── code_generator.nim    # 代碼生成器核心實現
├── examples/             # 使用示例
│   ├── orm_example.nim
│   └── serializer_example.nim
├── tests/                # 測試用例
│   └── test_generator.nim
└── README.md             # 專案說明
```

## 學習資源

- [Nim巨集文檔](https://nim-lang.org/docs/manual.html#macros)
- [Nim AST](https://nim-lang.org/docs/macros.html#ast-helpers)
- [編譯時函數執行](https://nim-lang.org/docs/manual.html#compile-time-function-execution)
- [代碼生成模式與實踐](https://pragprog.com/titles/tpdsl/language-implementation-patterns/)

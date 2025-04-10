# 模組與程式設計實踐  

## 學習目標
- 理解模組化設計的原則和優勢
- 掌握 Nim 模組系統的使用方法
- 學會組織和管理大型專案
- 實踐軟體工程最佳實踐

## 先決條件
- 已完成基本語法學習
- 理解函式和型別系統
- 熟悉基本的檔案操作

## 關鍵概念
1. 模組系統基礎
   - 模組定義和導入
   - 可見性控制
   - 命名空間管理
   - 循環依賴處理
   
2. 專案組織
   - 目錄結構
   - 套件管理
   - 建置系統
   - 測試框架
   
3. 設計模式
   - 依賴注入
   - 工廠模式
   - 單例模式
   - 觀察者模式

## 模組化設計

### 模組的定義與組織

模組是程式碼組織的基本單位，每個 `.nim` 檔案都是一個模組。良好的模組設計應該：
- 職責單一
- 高內聚低耦合
- 介面清晰
- 易於測試

#### 基本模組結構
```nim
# mathutils.nim
type
  Vector* = object  # 公開型別
    x*, y*: float  # 公開欄位
    
proc add*(a, b: Vector): Vector =  # 公開函式
  result.x = a.x + b.x
  result.y = a.y + b.y

proc length(v: Vector): float =  # 私有函式
  sqrt(v.x * v.x + v.y * v.y)
```

### 模組的導入與使用

Nim 提供了多種導入模組的方式，每種方式都有其特定用途：

```nim
# 基本導入
import mathutils

# 選擇性導入
from mathutils import add

# 重命名導入
import mathutils as math

# 限定符號導入
from mathutils import nil
```

## 實戰示例

### 1. 實現一個模組化的日誌系統
```nim
# logger.nim
type
  LogLevel* = enum
    Debug, Info, Warning, Error

  Logger* = object
    minLevel*: LogLevel
    useTimestamp*: bool

proc newLogger*(minLevel = Info, useTimestamp = true): Logger =
  result.minLevel = minLevel
  result.useTimestamp = useTimestamp

proc log*(logger: Logger, level: LogLevel, msg: string) =
  if level >= logger.minLevel:
    let timestamp = if logger.useTimestamp: $now() & " " else: ""
    echo timestamp & $level & ": " & msg

# main.nim
import logger

let log = newLogger()
log.log(Info, "應用程式啟動")
log.log(Debug, "這條訊息不會顯示")
log.log(Error, "發生錯誤！")
```

### 2. 實現一個簡單的依賴注入容器
```nim
# container.nim
type
  Container* = ref object
    services: Table[string, proc()]

proc newContainer*(): Container =
  new(result)
  result.services = initTable[string, proc()]()

proc register*[T](container: Container, factory: proc(): T) =
  container.services[$T] = proc() = factory()

proc resolve*[T](container: Container): T =
  let factory = container.services[$T]
  factory().T

# service.nim
type
  Database* = ref object
    connectionString: string

  UserService* = ref object
    db: Database

proc newDatabase*(): Database =
  Database(connectionString: "localhost:5432")

proc newUserService*(db: Database): UserService =
  UserService(db: db)

# main.nim
let container = newContainer()
container.register(newDatabase)
container.register(proc(): UserService = newUserService(resolve[Database]()))
```

### 3. 實現一個模組化的命令列應用程式
```nim
# commands.nim
type
  Command* = object
    name*: string
    description*: string
    execute*: proc(args: seq[string])

# command_registry.nim
var commands = initTable[string, Command]()

proc registerCommand*(cmd: Command) =
  commands[cmd.name] = cmd

proc executeCommand*(name: string, args: seq[string]) =
  if commands.hasKey(name):
    commands[name].execute(args)
  else:
    echo "未知命令：", name

# specific_commands.nim
proc initCommands*() =
  registerCommand(Command(
    name: "greet",
    description: "向用戶問好",
    execute: proc(args: seq[string]) =
      if args.len > 0:
        echo "你好，", args[0]
      else:
        echo "你好，訪客"
  ))
```

## 最佳實踐

1. 模組組織建議：
   - 按功能劃分模組
   - 保持模組大小適中
   - 避免循環依賴
   - 明確定義公開介面

2. 命名規範：
   - 使用有意義的名稱
   - 遵循一致的命名風格
   - 避免名稱衝突
   - 適當使用命名空間

3. 錯誤處理：
   - 統一錯誤處理方式
   - 提供有意義的錯誤訊息
   - 適當使用異常機制
   - 考慮錯誤恢復策略

## 小測驗
1. Nim 中如何控制符號的可見性？
2. 什麼情況下應該使用 `from ... import nil`？
3. 如何避免模組間的循環依賴？
4. 為什麼要使用依賴注入？

## 進一步閱讀
- [Nim 官方文檔：Modules](https://nim-lang.org/docs/manual.html#modules)
- [Nim 套件管理：Nimble](https://github.com/nim-lang/nimble)
- [Nim 測試框架：unittest](https://nim-lang.org/docs/unittest.html)

## 模組化設計

模組化設計是一種將程式碼拆分為多個模組的方式，旨在提升程式的可讀性、可維護性以及重用性。Nim 提供了強大的模組管理功能，讓開發者能夠輕鬆地組織和導入模組。

### 模組的拆分與導入  

在 Nim 中，每個檔案都可以視為一個模組。模組可以包含函式、型別、常數等，並能被其他模組導入使用。

假設我們有一個程式需要處理數學運算和字串操作，可以將其拆分為兩個模組：`mathutils.nim` 和 `stringutils.nim`。

**`mathutils.nim`**  
```nim
proc add(a, b: int): int =
  return a + b

proc multiply(a, b: int): int =
  return a * b
```

**`stringutils.nim`**  
```nim
proc toUpperCase(s: string): string =
  return s.toUpperAscii()

proc containsSubstring(s, sub: string): bool =
  return s.contains(sub)
```

**主程式 (`main.nim`)**  
```nim
import mathutils, stringutils  # 導入模組

echo add(5, 3)                # 輸出: 8
echo multiply(4, 7)           # 輸出: 28
echo toUpperCase("hello")     # 輸出: HELLO
echo containsSubstring("hello", "ell")  # 輸出: true
```

透過模組拆分，我們可以讓程式碼更具結構性，並且方便重用。

### 使用 `from` 與 `include` 語句  

#### `from` 語句  
`from` 語句允許開發者從模組中選擇性地導入特定的函式或型別，避免導入不必要的內容。

**範例：使用 `from` 語句**
```nim
from mathutils import add  # 只導入 `add` 函式

echo add(10, 20)           # 輸出: 30
# echo multiply(5, 5)      # 編譯錯誤，因為未導入 `multiply`
```

#### `include` 語句  
`include` 語句會將指定檔案的內容直接插入到使用它的檔案中，適合用於一些小型工具或輔助程式碼。

**範例：使用 `include` 語句**
```nim
# utils.nim
proc greet(name: string) =
  echo "Hello, ", name

# main.nim
include utils  # 將 `utils.nim` 的內容插入此檔案

greet("Alice")  # 輸出: Hello, Alice
```

注意，`include` 不適合用於大型模組，因為它會直接插入內容，可能導致命名衝突。

---

## 程式設計最佳實踐


### 型別安全與效能優化  

型別安全是 Nim 的核心特性之一。通過明確的型別定義，可以避免許多潛在的程式錯誤。同時，Nim 提供了多種效能優化手段，讓程式執行更高效。

#### 型別安全範例
```nim
proc add(a: int, b: int): int =
  return a + b

# add("hello", 5)  # 編譯錯誤，型別不匹配
```

#### 效能優化技巧
- **避免不必要的動態分配**：使用固定大小的資料結構，如陣列或子範圍型別。
- **使用 `const` 和 `let`**：對不會改變的值使用 `const` 或 `let`，避免不必要的記憶體操作。
- **啟用編譯器優化**：使用 `--opt:speed` 編譯選項以提升執行效能。

#### 模組化與可維護性  

模組化設計可以讓程式碼更易於維護和擴展。以下是幾個模組化設計的最佳實踐：

- **單一責任原則**：每個模組應專注於一個特定的功能。例如，數學運算模組只處理數學相關的函式。
- **避免過度耦合**：模組間應保持低耦合性，避免直接依賴具體實現。
- **清晰的命名**：模組和函式的命名應具有描述性，方便使用者理解。

#### 模組化設計範例
```nim
# mathutils.nim
proc add(a, b: int): int =
  return a + b

# stringutils.nim
proc toUpperCase(s: string): string =
  return s.toUpperAscii()

# main.nim
import mathutils, stringutils

echo add(2, 3)                # 輸出: 5
echo toUpperCase("nim lang")  # 輸出: NIM LANG
```

透過模組化設計，我們可以輕鬆擴展功能。例如，新增一個新的模組 `fileutils.nim` 處理檔案操作，而不影響現有模組。

---

總結來說，模組化設計與程式設計最佳實踐是撰寫高效、可維護程式的關鍵。透過合理的模組拆分、型別安全的使用以及效能優化，開發者可以設計出結構清晰且運行高效的程式。
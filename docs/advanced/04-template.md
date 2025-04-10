# 模板與進階語法

## 學習目標
- 掌握 Nim 中模板的定義和使用方法
- 理解模板與函數、宏的區別
- 學習如何使用模板進行代碼生成和優化
- 掌握模板參數的各種使用方式
- 理解模板衛生性（hygiene）的概念
- 學習提升函數（lifted functions）的應用

## 先決條件
- 熟悉 Nim 的基本語法和函數定義
- 理解編譯期和運行期的概念
- 具備基本的代碼重用和抽象能力
- 了解 Nim 的類型系統

## 關鍵概念
1. **模板基礎**
   - 模板定義和語法
   - 編譯期代碼替換
   - 參數處理和表達式注入

2. **模板進階特性**
   - 模板衛生性（Hygiene）
   - 注入（Injection）和綁定（Binding）
   - 靜態和動態綁定
   - 符號操作

3. **常見模板模式**
   - 控制結構模板
   - 資源管理模板
   - 日誌和測量模板
   - DSL 構建

4. **提升函數**
   - 標量函數提升
   - 循環避免和代碼簡化
   - 高階函數組合

## 模板基礎

模板是 Nim 中一種強大的語法樹替換機制，用於在編譯時進行代碼生成。它允許我們定義靈活的代碼片段，並在使用時以特定的方式展開。

### 模板語法

模板的基本語法如下：

```nim
template 模板名稱(參數列表): 返回類型 =
    模板內容
```

與函數不同，模板在編譯時會直接替換為其內容，而不是在運行時調用。這使得模板非常適合於創建零開銷抽象。

### 簡單模板示例

讓我們從一些簡單的模板開始：

```nim
# 定義一個比較模板
template `!=`(a, b): bool =
  not (a == b)

# 定義一個日誌模板
import std/times

template log(msg: string): void =
  echo "[" & $now() & "] " & msg

# 使用這些模板
let x = 5
let y = 10
echo x != y  # 輸出: true（被展開為 not (x == y)）

log("程序開始")  # 輸出: [2023-04-10T12:34:56+08:00] 程序開始
log("程序結束")  # 輸出: [2023-04-10T12:35:01+08:00] 程序結束
```

在這個例子中，`!=` 模板定義了一個不等比較運算符，而 `log` 模板則在消息前添加了時間戳。

### 模板與函數的區別

模板和函數之間有幾個關鍵區別：

```nim
# 函數版本 - 運行時調用
proc add(a, b: int): int =
  a + b

# 模板版本 - 編譯時替換
template addT(a, b: int): int =
  a + b

# 使用函數和模板
let result1 = add(2, 3)  # 編譯為函數調用
let result2 = addT(2, 3)  # 編譯為 2 + 3

# 使用帶有副作用的表達式
var i = 0
echo add(i, i += 1)  # 結果可預測：0 + 1 = 1
echo addT(i, i += 1)  # 結果取決於展開方式和編譯器
```

函數會在執行時被調用，而模板會在編譯時替換為其內容。這使得模板在處理帶有副作用的表達式時可能會產生出乎意料的結果。

### 模板參數處理

模板參數可以有不同的處理方式：

```nim
# 立即計算參數
template evalNow(x: int): int =
  x + 1

# 延遲計算參數（使用 untyped 或 typed）
template evalLater(x: untyped): untyped =
  x + 1

# 調用示例
var n = 0
echo evalNow(n += 1)  # n 增加後，結果為 1 + 1 = 2
echo evalLater(n += 1)  # 展開為 (n += 1) + 1，n 增加 1，結果為 2
```

使用 `untyped` 或 `typed` 參數類型可以延遲表達式的計算，使其在模板展開後再計算。

## 模板進階特性

### 模板衛生性

模板衛生性是指模板內部定義的標識符不會與外部標識符衝突的特性：

```nim
# 默認情況下，模板是衛生的
template hygienic() =
  var x = 10
  echo x

var x = 5
hygienic()  # 輸出: 10（模板內的 x 與外部的 x 不衝突）
echo x      # 輸出: 5（外部的 x 不受影響）

# 非衛生模板（使用 {.dirty.} 編譯指示）
template dirty() {.dirty.} =
  var x = 20
  echo x

dirty()    # 輸出: 20（但會重新定義外部的 x）
echo x     # 輸出: 20（外部的 x 被修改了）
```

默認情況下，Nim 的模板是衛生的，這意味著模板內部定義的變量不會與外部變量發生衝突。使用 `{.dirty.}` 編譯指示可以創建非衛生模板。

### 注入和綁定

模板可以通過參數注入和綁定來控制代碼展開的方式：

```nim
# 使用注入綁定參數
template withFile(f: untyped, filename: string, mode: FileMode, body: untyped) =
  var f: File
  if open(f, filename, mode):
    try:
      body
    finally:
      close(f)
  else:
    raise newException(IOError, "無法打開文件: " & filename)

# 使用模板
withFile(myFile, "example.txt", fmRead):
  let content = myFile.readAll()
  echo content
```

在這個例子中，`f` 被注入到 `body` 中，使得在 `body` 內部可以直接使用 `myFile` 變量。

### 符號操作

模板也可以用於符號操作，例如創建新的標識符：

```nim
import std/macros

template declareInt(name: untyped) =
  var name: int = 0

# 使用模板創建新變量
declareInt(myCounter)
echo myCounter  # 輸出: 0
myCounter = 42
echo myCounter  # 輸出: 42
```

這個模板可以動態地創建新的整數變量，這在元編程中非常有用。

## 常見模板模式

### 控制結構模板

模板可以用來創建自定義控制結構：

```nim
# 重試操作直到成功
template retry(attempts: int, body: untyped) =
  var success = false
  var remainingAttempts = attempts
  
  while not success and remainingAttempts > 0:
    try:
      body
      success = true
    except:
      remainingAttempts -= 1
      if remainingAttempts <= 0:
        raise
      sleep(1000)  # 等待 1 秒再重試
  
  if not success:
    raise newException(Exception, "重試 " & $attempts & " 次後仍然失敗")

# 使用重試模板
retry(3):
  # 嘗試執行可能失敗的操作
  let response = httpGet("https://example.com/api")
  echo response
```

這個模板允許重試可能失敗的操作多次，為錯誤處理提供了一種簡潔的方式。

### 資源管理模板

模板非常適合用於資源管理，如文件、鎖和連接：

```nim
# 鎖管理模板
template withLock(lock: Lock, body: untyped) =
  acquire(lock)
  try:
    body
  finally:
    release(lock)

# 數據庫連接模板
template withConnection(db: string, user: string, password: string, body: untyped) =
  let conn = openConnection(db, user, password)
  try:
    body
  finally:
    closeConnection(conn)

# 使用示例
var mutex: Lock
initLock(mutex)

withLock(mutex):
  # 這裡的代碼在鎖保護下執行
  modifySharedResource()

withConnection("mydb", "user", "password"):
  # 這裡的代碼可以使用數據庫連接
  executeQuery("SELECT * FROM users")
```

這些模板確保資源總是被正確釋放，即使在執行期間出現異常。

### 日誌和測量模板

模板可以用於創建強大的日誌和性能測量工具：

```nim
import std/[times, strformat]

# 測量代碼執行時間的模板
template benchmark(name: string, body: untyped) =
  let startTime = epochTime()
  body
  let endTime = epochTime()
  let elapsed = endTime - startTime
  echo fmt"{name} 耗時: {elapsed:.6f} 秒"

# 帶有上下文的日誌模板
template logContext(context: string, body: untyped) =
  let oldContext = getCurrentContext()
  setCurrentContext(context)
  try:
    body
  finally:
    setCurrentContext(oldContext)

# 使用示例
benchmark("排序算法"):
  var data = @[5, 9, 3, 2, 8, 1, 6]
  sort(data)
  echo data

logContext("用戶認證"):
  log("開始驗證用戶憑證")
  authenticate(username, password)
  log("驗證完成")
```

這些模板簡化了常見任務，如性能測量和上下文化日誌記錄。

### DSL 構建

模板的強大功能之一是能夠創建領域特定語言 (DSL)：

```nim
# HTML DSL 示例
import std/strformat

type Element = object
  tag: string
  content: string
  attributes: seq[(string, string)]

template element(tag: string, attributes: varargs[(string, string)], body: untyped): untyped =
  var element = Element(tag: tag, attributes: @attributes)
  body
  renderElement(element)

template content(e: var Element, text: string) =
  e.content = text

proc renderElement(e: Element): string =
  var attrs = ""
  for (key, val) in e.attributes:
    attrs.add fmt" {key}=\"{val}\""
  
  result = fmt"<{e.tag}{attrs}>{e.content}</{e.tag}>"

# 使用 HTML DSL
let html = element("div", ("class", "container"), ("id", "main")):
  content("Hello, World!")

echo html  # 輸出: <div class="container" id="main">Hello, World!</div>
```

這個 DSL 使 HTML 生成變得更加直觀和類型安全。

## 提升函數

提升函數是一種方便的工具，用於將原本只能處理標量的函數擴展為可以處理集合（如數組或序列）的函數。

### 標量函數提升

Nim 標準庫提供了 `liftScalarProc` 過程來提升標量函數：

```nim
import std/[math, sequtils]

# 提升 sqrt 函數
let sqrtArray = liftScalarProc(sqrt)

# 使用提升後的函數處理數組
let numbers = @[1.0, 4.0, 9.0, 16.0]
let results = sqrtArray(numbers)

echo results  # 輸出: @[1.0, 2.0, 3.0, 4.0]
```

在這個例子中，`sqrt` 函數被提升為可以處理整個序列的函數。

### 自定義提升模板

我們可以創建自己的提升模板來提升各種函數：

```nim
# 通用函數提升模板
template lift(f: untyped): untyped =
  proc(s: openArray[auto]): auto =
    var result = newSeq[type(f(s[0]))](s.len)
    for i in 0..<s.len:
      result[i] = f(s[i])
    result

# 二元函數提升模板
template lift2(f: untyped): untyped =
  proc(a, b: openArray[auto]): auto =
    assert a.len == b.len, "序列長度必須相同"
    var result = newSeq[type(f(a[0], b[0]))](a.len)
    for i in 0..<a.len:
      result[i] = f(a[i], b[i])
    result

# 定義一些標量函數
proc square(x: float): float = x * x
proc add(x, y: float): float = x + y

# 提升單參數函數
let squareArray = lift(square)
echo squareArray(@[1.0, 2.0, 3.0, 4.0])  # 輸出: @[1.0, 4.0, 9.0, 16.0]

# 提升雙參數函數
let addArrays = lift2(add)
echo addArrays(@[1.0, 2.0], @[3.0, 4.0])  # 輸出: @[4.0, 6.0]
```

這些提升模板可以簡化處理集合數據的代碼，消除手動編寫循環的需要。

### 組合提升函數

提升函數可以組合使用，創建更複雜的數據處理管道：

```nim
# 提升多個函數並組合它們
let pipeline = lift(sqrt) ∘ lift(square) ∘ lift(x => x + 1.0)

# 使用處理管道（執行步驟：加 1，平方，開方）
let input = @[0.0, 1.0, 2.0, 3.0]
let output = pipeline(input)

echo output  # 輸出: @[1.0, 1.414..., 1.732..., 2.0]
```

這種方法支持函數式編程風格，使代碼更加聲明式和可組合。

## 實用案例：配置 DSL

下面是一個結合模板和提升函數的實際案例，用於創建配置 DSL：

```nim
import std/[tables, strutils, json]

# 配置類型
type
  ConfigValue = object
    case kind: ConfigKind
    of ckInt: intVal: int
    of ckFloat: floatVal: float
    of ckString: strVal: string
    of ckBool: boolVal: bool
    of ckArray: arrayVal: seq[ConfigValue]
    of ckObject: objectVal: Table[string, ConfigValue]

  ConfigKind = enum
    ckInt, ckFloat, ckString, ckBool, ckArray, ckObject

  Config = ref object
    root: Table[string, ConfigValue]
    currentPath: seq[string]

# 創建新配置
proc newConfig(): Config =
  result = Config(
    root: initTable[string, ConfigValue](),
    currentPath: @[]
  )

# 設置值模板
template set(config: Config, key: string, value: untyped) =
  var path = config.currentPath
  path.add(key)
  var current = addr config.root
  
  for i in 0..<path.high:
    if not current.hasKey(path[i]):
      current[path[i]] = ConfigValue(kind: ckObject, objectVal: initTable[string, ConfigValue]())
    if current[path[i]].kind != ckObject:
      raise newException(ValueError, "路徑衝突")
    current = addr current[path[i]].objectVal
  
  when typeof(value) is int:
    current[path[^1]] = ConfigValue(kind: ckInt, intVal: value)
  elif typeof(value) is float:
    current[path[^1]] = ConfigValue(kind: ckFloat, floatVal: value)
  elif typeof(value) is string:
    current[path[^1]] = ConfigValue(kind: ckString, strVal: value)
  elif typeof(value) is bool:
    current[path[^1]] = ConfigValue(kind: ckBool, boolVal: value)
  else:
    static: error("不支持的類型")

# 嵌套配置模板
template section(config: Config, name: string, body: untyped) =
  config.currentPath.add(name)
  body
  discard config.currentPath.pop()

# 導出為 JSON
proc toJson(value: ConfigValue): JsonNode =
  case value.kind
  of ckInt: result = newJInt(value.intVal)
  of ckFloat: result = newJFloat(value.floatVal)
  of ckString: result = newJString(value.strVal)
  of ckBool: result = newJBool(value.boolVal)
  of ckArray:
    result = newJArray()
    for item in value.arrayVal:
      result.add(toJson(item))
  of ckObject:
    result = newJObject()
    for key, val in value.objectVal:
      result[key] = toJson(val)

proc toJson(config: Config): JsonNode =
  result = newJObject()
  for key, val in config.root:
    result[key] = toJson(val)

# 使用配置 DSL
var config = newConfig()

# 設置基本配置
config.set("appName", "我的應用")
config.set("version", 1.2)
config.set("debug", true)

# 設置數據庫配置
config.section("database"):
  config.set("host", "localhost")
  config.set("port", 5432)
  config.set("username", "admin")
  config.set("password", "secret")

# 設置日誌配置
config.section("logging"):
  config.set("level", "info")
  config.set("file", "app.log")
  
  config.section("rotation"):
    config.set("maxSize", 10_000_000)
    config.set("backups", 5)

# 輸出為 JSON
echo pretty(toJson(config))
```

這個示例展示了如何使用模板創建一個直觀的配置 DSL，可以生成嵌套配置結構並導出為 JSON。

## 練習與挑戰

1. **基本模板實現**
   - 創建一個 `times` 模板，用於重複執行代碼塊指定的次數
   - 實現一個 `assert` 模板，當條件不滿足時輸出自定義錯誤消息
   - 設計一個 `withResource` 模板，確保資源總是被釋放

2. **高級模板設計**
   - 開發一個用於實現單元測試的模板系統，包括測試組、前置條件和後置條件
   - 實現一個事件系統模板，支持自定義事件的發布和訂閱
   - 創建一個簡單的狀態機 DSL，使用模板定義狀態和轉換

3. **提升函數應用**
   - 創建一個函數組合模板，能夠將多個函數組合成一個管道
   - 實現提升函數的版本，能夠處理可選值（Option 類型）
   - 開發一個批處理系統，使用提升函數在數據集上應用轉換

4. **實際項目**
   - 實現一個簡單的 HTML 模板引擎，使用模板生成 HTML
   - 創建一個輕量級數據驗證 DSL，使用模板定義驗證規則
   - 開發一個基於模板的命令行參數解析器

## 自我評估問題

1. 解釋模板和函數之間的主要區別。什麼情況下應該使用模板而不是函數？
2. 什麼是模板衛生性？為什麼它對於防止意外變量衝突很重要？
3. 解釋 `untyped` 和 `typed` 參數之間的區別。何時應該使用每種類型？
4. 如何使用模板創建領域特定語言 (DSL)？提供一個實際的例子。
5. 解釋提升函數的概念，以及它們如何簡化集合處理。
6. 在模板中，`{.dirty.}` 編譯指示的作用是什麼？何時應該使用它？
7. 如何在模板中操作符號和標識符？提供一個實例。
8. 討論模板在資源管理中的應用。為什麼模板特別適合這種用例？

## 進一步閱讀

- [Nim 官方文檔：模板](https://nim-lang.org/docs/manual.html#templates)
- [Nim 官方文檔：元編程](https://nim-lang.org/docs/manual.html#meta-programming)
- [Nim 標準庫：sequtils（用於提升函數）](https://nim-lang.org/docs/sequtils.html)
- [Nim 標準庫：macros（高級元編程）](https://nim-lang.org/docs/macros.html)
- [Nim by Example：模板](https://nim-by-example.github.io/templates/)
- [Nim 元編程深入探討](https://nim-lang.org/docs/tut3.html)

## 小結

本章探討了 Nim 中的模板系統，這是一種強大的編譯時代碼生成機制。模板允許開發者創建零開銷抽象，簡化代碼並提高可讀性，同時保持高性能。

與函數不同，模板在編譯時直接替換為其內容，而不是在運行時調用。這使得模板特別適合於資源管理、錯誤處理和創建領域特定語言等任務。模板的衛生性機制確保內部定義的標識符不會與外部標識符發生衝突，除非明確指定。

提升函數是 Nim 中另一個強大的特性，它允許將處理單個值的函數自動提升為處理集合的函數。這大大簡化了數據處理代碼，減少了重複並提高了可讀性。

通過結合模板和提升函數，開發者可以創建直觀、類型安全且高效的代碼。無論是開發 Web 框架、配置系統還是數據處理管道，這些工具都提供了強大的元編程能力。

掌握模板和提升函數的使用，將使您能夠寫出更簡潔、更可讀且更可維護的 Nim 代碼，同時保持極高的運行時性能。
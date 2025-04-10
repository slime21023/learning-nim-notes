# 模組系統與套件管理

## 學習目標
- 全面掌握 Nim 模組系統的使用方法與設計原則
- 深入理解 Nimble 套件管理器及其工作流程
- 學會組織和管理大型 Nim 專案的架構與依賴
- 運用模組化設計實踐軟體工程最佳實踐
- 掌握 Nim 生態系統中的資源獲取與貢獻方法

## 先決條件
- 已完成基本語法和型別系統學習
- 理解函式、物件和基本 I/O 操作
- 熟悉命令列工具的基本使用
- 對軟體工程概念有初步認識

## 關鍵概念
1. 模組系統基礎
   - 模組定義和導入機制
   - 符號可見性控制與命名空間
   - 循環依賴的處理
   - 編譯順序與連結模型
   
2. Nimble 套件管理
   - Nimble 安裝與配置
   - 建立與發布套件
   - 依賴管理與版本控制
   - 套件文檔與測試
   
3. 專案組織與架構
   - 標準目錄結構
   - 主檔案和模組規劃
   - 組件間的通訊機制
   - 程式碼風格與一致性
   
4. 設計模式與最佳實踐
   - Nim 風格的設計模式
   - 錯誤處理與日誌記錄
   - 測試與持續整合
   - 效能優化與監控

## 模組系統深入解析

### 模組基礎概念

Nim 的模組系統是構建大型應用程式的基礎。在 Nim 中，每個 `.nim` 檔案都是一個獨立的模組，具有自己的作用域和命名空間：

```nim
# 這是 mathutils.nim 模組
# 公開可被其他模組訪問的符號使用 * 標記

type
  Vector2D* = object  # 公開型別
    x*, y*: float     # 公開欄位

proc add*(a, b: Vector2D): Vector2D =  # 公開函式
  result.x = a.x + b.x
  result.y = a.y + b.y
  
proc length(v: Vector2D): float =  # 私有函式，只在模組內可用
  sqrt(v.x * v.x + v.y * v.y)

const PI* = 3.14159   # 公開常數
var count = 0         # 私有變數
```

#### 模組的導入與使用

Nim 提供多種導入模組的方式，每種方式有不同的命名空間效果：

```nim
# main.nim

# 基本導入: 需要使用模組名稱前綴訪問符號
import mathutils
let v1 = mathutils.Vector2D(x: 3, y: 4)
let v2 = mathutils.Vector2D(x: 1, y: 2)
let v3 = mathutils.add(v1, v2)
echo "結果: ", v3, ", PI = ", mathutils.PI

# 選擇性導入: 僅導入特定符號
from mathutils import Vector2D, add
let v4 = Vector2D(x: 5, y: 6)
let v5 = add(v3, v4)
# echo mathutils.PI  # 錯誤: PI 未被導入

# 全局導入: 將所有公開符號導入當前作用域
from mathutils import nil  # 只聲明模組，不導入任何符號
let v6 = mathutils.Vector2D(x: 7, y: 8)

# 使用 except 排除特定符號
import mathutils except PI
# echo PI  # 錯誤: PI 被排除了
```

#### 模組搜索路徑

Nim 編譯器按照以下順序查找模組：

1. 當前目錄
2. 編譯命令中指定的路徑 (使用 `-p:PATH` 選項)
3. Nim 標準庫路徑
4. Nimble 套件路徑

可以使用 `--listSearchPaths` 選項查看完整搜索路徑：

```bash
nim c --listSearchPaths main.nim
```

### 模組組織與可見性

#### 符號可見性控制

Nim 使用特殊標記控制符號可見性：

```nim
# visibility.nim

# 公開符號 - 可被其他模組訪問
proc publicProc*(x: int): int = x * 2

# 私有符號 - 僅在當前模組內可見
proc privateProc(x: int): int = x * 3

# 部分公開的物件
type
  Person* = object  # 類型是公開的
    name*: string   # 該欄位是公開的
    age: int        # 該欄位是私有的

# 私有輔助函式
proc helperFunc(p: Person): string =
  p.name & " is " & $p.age & " years old"

# 公開 API 使用私有輔助函式
proc describe*(p: Person): string =
  "Person: " & helperFunc(p)
```

#### 嵌套模組

Nim 支持嵌套模組，用於進一步組織代碼：

```nim
# graphics.nim
type Point* = object
  x*, y*: float

# graphics/shapes.nim
import ../graphics

type
  Circle* = object
    center*: Point
    radius*: float

  Rectangle* = object
    topLeft*: Point
    width*, height*: float

# graphics/colors.nim
type
  Color* = object
    r*, g*, b*: int
    alpha*: float

# 使用嵌套模組
# main.nim
import graphics
import graphics/shapes
import graphics/colors

let p = Point(x: 10, y: 20)
let c = Circle(center: p, radius: 5)
let red = Color(r: 255, g: 0, b: 0, alpha: 1.0)
```

#### 符號重新導出

一個模組可以重新導出另一個模組的符號：

```nim
# 在 api.nim 中整合多個模組的 API
import mathutils
import stringutils

# 重新導出選擇的符號
export mathutils.Vector2D, mathutils.add
export stringutils.transform
```

### 處理模組依賴

#### 循環依賴問題

循環依賴是指模組 A 導入模組 B，而模組 B 也導入模組 A 的情況。Nim 提供多種解決方案：

```nim
# 方法1: 使用 {.hint[XDeclaredButNotUsed]:off.} 編譯指示
# a.nim
import b
proc procA*() = echo "A"

# b.nim
import a
proc procB*() = echo "B"
```

```nim
# 方法2: 前向聲明
# a.nim
proc procB(): string  # 前向聲明，不提供實現

proc procA*(): string =
  result = "A calls " & procB()

# 在模組底部導入其他模組
import b

# b.nim
proc procB*(): string =
  "B"
```

```nim
# 方法3: 使用 types.nim 共享型別
# types.nim
type
  NodeA* = ref object
    value*: int
    next*: NodeB

  NodeB* = ref object
    value*: int
    next*: NodeA

# a.nim
import types
proc processA*(node: NodeA) = discard

# b.nim
import types
proc processB*(node: NodeB) = discard
```

#### 模組初始化順序

Nim 模組的初始化順序是確定的：

```nim
# module1.nim
echo "Initializing Module 1"
var x* = 10

# module2.nim
import module1
echo "Initializing Module 2"
var y* = x * 2

# main.nim
import module2
echo "In Main: ", y
```

執行 `main.nim` 會輸出：
```
Initializing Module 1
Initializing Module 2
In Main: 20
```

## Nimble 套件管理系統

### Nimble 基礎

Nimble 是 Nim 的官方套件管理器，用於創建、發布和管理套件：

```bash
# 安裝 Nimble
nimble install nimble

# 查看幫助
nimble help

# 搜索套件
nimble search http

# 安裝套件
nimble install jester
```

### 創建 Nimble 套件

#### 建立新套件

```bash
# 創建新套件
nimble init mypackage
```

這將生成以下文件結構：

```
mypackage/
├── src/
│   └── mypackage.nim
├── tests/
│   └── test1.nim
├── mypackage.nimble
└── README.md
```

#### 定義套件信息

`.nimble` 文件定義了套件的元信息和依賴：

```nim
# mypackage.nimble
# Package
version       = "0.1.0"
author        = "Your Name"
description   = "A new awesome Nim package"
license       = "MIT"
srcDir        = "src"
bin           = @["mypackage"]

# Dependencies
requires "nim >= 1.4.0"
requires "jester >= 0.5.0"
```

#### 套件任務

Nimble 支持自定義任務：

```nim
# 添加到 .nimble 文件
task hello, "Prints hello world":
  echo "Hello, World!"

task test, "Runs the test suite":
  exec "nim c -r tests/test1.nim"

task docs, "Generate documentation":
  exec "nim doc --project src/mypackage.nim"
```

```bash
# 運行任務
nimble hello
nimble test
nimble docs
```

### 發布和使用套件

#### 發布套件

```bash
# 測試套件
nimble test

# 發布套件
nimble publish
```

#### 使用發布的套件

```nim
# 在其他項目中使用
import mypackage

# 或使用特定模組
import mypackage/submodule
```

#### 版本控制和依賴解析

Nimble 使用語義化版本控制：

```nim
# 支持各種版本需求格式
requires "jester >= 0.5.0"
requires "docopt >= 0.6.0 & < 0.7.0"
requires "nre#head"  # 使用 Git 分支
```

## 專案組織與架構

### 標準目錄結構

大型 Nim 專案通常使用以下目錄結構：

```
project/
├── src/               # 源代碼
│   ├── projectname.nim  # 主模組
│   ├── config.nim       # 配置
│   ├── types.nim        # 共享型別
│   └── utils/           # 工具模組
├── tests/             # 測試
├── docs/              # 文檔
├── examples/          # 使用示例
├── scripts/           # 工具腳本
├── LICENSE            # 授權協議
├── README.md          # 說明文檔
└── projectname.nimble # 套件文件
```

### 實例：Web 應用程式架構

```
webapp/
├── src/
│   ├── webapp.nim        # 應用入口
│   ├── config.nim        # 配置管理
│   ├── models/           # 數據模型
│   │   ├── user.nim
│   │   └── post.nim
│   ├── views/            # 視圖模板
│   │   ├── layout.nim
│   │   └── user.nim
│   ├── controllers/      # 控制器
│   │   ├── auth.nim
│   │   └── posts.nim
│   ├── services/         # 業務邏輯
│   │   └── userservice.nim
│   ├── db/               # 數據庫訪問
│   │   └── database.nim
│   └── utils/            # 工具函數
│       ├── crypto.nim
│       └── validation.nim
├── public/               # 靜態資源
│   ├── css/
│   ├── js/
│   └── images/
├── tests/                # 測試
├── webapp.nimble         # 套件文件
└── config.json           # 配置文件
```

### 大型專案的模組劃分策略

1. 按功能劃分
2. 按層次劃分 (MVC, 三層架構等)
3. 按領域劃分 (DDD)

示例 - 電子商務系統的模組劃分：

```
ecommerce/
├── src/
│   ├── ecommerce.nim          # 主模組
│   ├── common/                # 共用工具和型別
│   ├── catalog/               # 產品目錄
│   │   ├── product.nim
│   │   └── category.nim
│   ├── customer/              # 客戶管理
│   │   ├── customer.nim
│   │   └── account.nim
│   ├── order/                 # 訂單處理
│   │   ├── order.nim
│   │   ├── cart.nim
│   │   └── payment.nim
│   ├── warehouse/             # 倉庫管理
│   │   ├── inventory.nim
│   │   └── shipping.nim
│   └── admin/                 # 管理後台
│       ├── dashboard.nim
│       └── reports.nim
└── ecommerce.nimble
```

## 設計模式與最佳實踐

### Nim 常用設計模式

#### 工廠模式

```nim
# factory.nim
type
  ShapeKind = enum
    skCircle, skRectangle, skTriangle

  Shape = ref object
    case kind: ShapeKind
    of skCircle:
      radius: float
    of skRectangle:
      width, height: float
    of skTriangle:
      a, b, c: float

proc newCircle*(radius: float): Shape =
  Shape(kind: skCircle, radius: radius)

proc newRectangle*(width, height: float): Shape =
  Shape(kind: skRectangle, width: width, height: height)

proc newTriangle*(a, b, c: float): Shape =
  Shape(kind: skTriangle, a: a, b: b, c: c)

proc area*(shape: Shape): float =
  case shape.kind
  of skCircle: 
    result = 3.14159 * shape.radius * shape.radius
  of skRectangle: 
    result = shape.width * shape.height
  of skTriangle:
    let s = (shape.a + shape.b + shape.c) / 2
    result = sqrt(s * (s - shape.a) * (s - shape.b) * (s - shape.c))
```

#### 依賴注入

```nim
# dependency_injection.nim
type
  Logger = ref object of RootObj
  ConsoleLogger = ref object of Logger
  FileLogger = ref object of Logger
  
  UserService = ref object
    logger: Logger

# Logger 實現
method log*(l: Logger, msg: string) {.base.} =
  discard

method log*(l: ConsoleLogger, msg: string) =
  echo "[Console] ", msg

method log*(l: FileLogger, msg: string) =
  let f = open("app.log", fmAppend)
  f.writeLine("[File] " & msg)
  f.close()

# 注入依賴
proc newUserService*(logger: Logger): UserService =
  UserService(logger: logger)

proc createUser*(service: UserService, name: string) =
  service.logger.log("Creating user: " & name)
  # 實際邏輯...

# 使用
let consoleLogger = ConsoleLogger()
let userService = newUserService(consoleLogger)
userService.createUser("Alice")

# 可輕易切換實現
let fileLogger = FileLogger()
let userService2 = newUserService(fileLogger)
userService2.createUser("Bob")
```

#### 觀察者模式

```nim
# observer.nim
type
  Observer = ref object of RootObj
  Subject = ref object
    observers: seq[Observer]

method update*(o: Observer, msg: string) {.base.} =
  discard

proc register*(s: Subject, o: Observer) =
  s.observers.add(o)

proc notify*(s: Subject, msg: string) =
  for observer in s.observers:
    observer.update(msg)

# 實現具體觀察者
type
  ConsoleObserver = ref object of Observer
  LogObserver = ref object of Observer

method update*(o: ConsoleObserver, msg: string) =
  echo "[Console] Notification: ", msg

method update*(o: LogObserver, msg: string) =
  let f = open("notifications.log", fmAppend)
  f.writeLine("[Log] " & msg)
  f.close()

# 使用
var subject = Subject(observers: @[])
subject.register(ConsoleObserver())
subject.register(LogObserver())
subject.notify("Something important happened!")
```

### 最佳實踐

#### 適當的錯誤處理

```nim
# 定義自定義錯誤
type
  DatabaseError* = object of CatchableError
  ValidationError* = object of CatchableError
  
# 引發特定類型的錯誤
proc connectToDb*(connectionString: string): DbConn =
  if connectionString == "":
    raise newException(DatabaseError, "空連接字符串")
  # ...實際連接邏輯

# 使用 try-except 捕獲特定類型的錯誤
try:
  let conn = connectToDb("")
except DatabaseError as e:
  echo "數據庫錯誤: ", e.msg
except:
  echo "未知錯誤: ", getCurrentExceptionMsg()
```

#### 單元測試策略

```nim
# 在 tests/test_math.nim
import unittest
import ../src/mathutils

suite "Vector Operations":
  test "Vector addition":
    let v1 = Vector2D(x: 3, y: 4)
    let v2 = Vector2D(x: 1, y: 2)
    let result = add(v1, v2)
    check(result.x == 4)
    check(result.y == 6)
  
  test "Vector scalar multiplication":
    let v = Vector2D(x: 3, y: 4)
    let result = multiply(v, 2)
    check(result.x == 6)
    check(result.y == 8)
```

#### 代碼文檔標準

```nim
## 向量模組提供二維和三維向量操作
##
## 示例:
## ```nim
## let v1 = Vector2D(x: 3, y: 4)
## let v2 = Vector2D(x: 1, y: 2)
## let v3 = add(v1, v2)
## echo v3  # 輸出 (x: 4, y: 6)
## ```

type
  Vector2D* = object
    ## 表示二維平面上的向量
    x*, y*: float  ## x和y分量

proc add*(a, b: Vector2D): Vector2D {.raises: [].} =
  ## 向量加法
  ##
  ## 參數:
  ## - a: 第一個向量
  ## - b: 第二個向量
  ##
  ## 返回:
  ## - 兩個向量的和
  result.x = a.x + b.x
  result.y = a.y + b.y
```

#### 日誌記錄

```nim
import logging

var L = newConsoleLogger()
addHandler(L)

# 設置日誌級別
setLogFilter(lvlInfo)

# 使用日誌
debug "這條消息在默認級別不會顯示"
info "應用啟動成功"
warn "配置文件未找到，使用默認配置"
error "無法連接到數據庫"
fatal "嚴重錯誤，應用即將退出"
```

## 實戰案例：構建 CLI 工具

### 設計命令行解析器

```nim
# cli.nim
import parseopt

type
  CliConfig = object
    verbose: bool
    inputFile: string
    outputFile: string
    count: int

proc parseCommandLine(): CliConfig =
  result = CliConfig(count: 1)  # 默認值
  
  for kind, key, val in getopt():
    case kind
    of cmdLongOption, cmdShortOption:
      case key
      of "verbose", "v": result.verbose = true
      of "input", "i": result.inputFile = val
      of "output", "o": result.outputFile = val
      of "count", "c": result.count = parseInt(val)
      else: discard
    of cmdArgument:
      if result.inputFile == "":
        result.inputFile = key
    of cmdEnd: break

proc main() =
  let config = parseCommandLine()
  echo "配置: ", config

when isMainModule:
  main()
```

### 處理配置文件

```nim
# config.nim
import json, os

type
  AppConfig* = object
    serverPort*: int
    dbConnection*: string
    logLevel*: string
    maxConnections*: int

proc loadConfig*(path: string): AppConfig =
  result = AppConfig(
    serverPort: 8080,      # 默認值
    logLevel: "info",
    maxConnections: 100
  )
  
  if not fileExists(path):
    echo "配置文件不存在，使用默認值"
    return
  
  let configJson = parseFile(path)
  
  if configJson.hasKey("serverPort"):
    result.serverPort = configJson["serverPort"].getInt()
  
  if configJson.hasKey("dbConnection"):
    result.dbConnection = configJson["dbConnection"].getStr()
  
  if configJson.hasKey("logLevel"):
    result.logLevel = configJson["logLevel"].getStr()
  
  if configJson.hasKey("maxConnections"):
    result.maxConnections = configJson["maxConnections"].getInt()

proc saveConfig*(config: AppConfig, path: string) =
  var configJson = newJObject()
  configJson["serverPort"] = newJInt(config.serverPort)
  configJson["dbConnection"] = newJString(config.dbConnection)
  configJson["logLevel"] = newJString(config.logLevel)
  configJson["maxConnections"] = newJInt(config.maxConnections)
  
  writeFile(path, pretty(configJson))
```

## 練習與挑戰

1. **模組拆分**
   - 將單一大文件應用拆分為多個模組
   - 實踐可見性控制和接口設計

2. **套件開發**
   - 創建一個簡單的 Nimble 套件
   - 編寫測試和文檔

3. **CLI 工具**
   - 實現一個簡單的命令行工具，如文件轉換器
   - 使用參數解析和配置管理

4. **設計模式應用**
   - 選擇一個設計模式並在實際項目中應用
   - 分析模式帶來的優勢和可能的缺點

## 自我評估問題

1. 解釋 Nim 中公開和私有符號的區別，以及它們如何影響模組設計。
2. 描述 Nimble 套件管理器的主要功能和套件發布流程。
3. 如何解決模組間的循環依賴問題？
4. 大型 Nim 專案應該如何組織其目錄結構和模組劃分？
5. 你會在什麼情況下選擇依賴注入模式？

## 進一步閱讀

- [Nim 官方文檔: 模組](https://nim-lang.org/docs/modules.html)
- [Nimble 使用指南](https://github.com/nim-lang/nimble#readme)
- [Nim Basics: Package Management](https://narimiran.github.io/nim-basics/#_package_management)
- [Nim in Action: Chapter on Modules and Packages](https://book.picheta.me/)
- [Awesome Nim: 社區資源集合](https://github.com/xflywind/awesome-nim)

## 小結

本章全面介紹了 Nim 的模組系統和套件管理機制，我們學習了如何組織大型專案、管理依賴關係、應用設計模式和實踐軟體工程最佳實踐。這些知識將幫助你構建可維護、可擴展的 Nim 應用程式，並有效利用 Nim 生態系統中的各種資源。模組化設計不僅可以使代碼更加清晰和易於理解，還能促進團隊協作和代碼重用，是專業 Nim 開發的基礎技能。
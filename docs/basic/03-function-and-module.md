# 函式與模組

Nim 提供簡潔且強大的函式與模組化設計語法，幫助開發者撰寫具結構性和可重用的程式碼。

## 學習目標
- 掌握 Nim 函式的定義和使用
- 理解函式參數和返回值的類型系統
- 學會使用模組組織代碼
- 掌握命名空間和導入語法

## 先決條件
- 已完成基本控制流程學習
- 了解基本數據類型
- 熟悉變數作用域概念

## 關鍵概念
1. 函式基礎
   - proc 關鍵字
   - 參數定義
   - 返回值類型
   - result 變數
   
2. 函式進階特性
   - 命名參數
   - 默認參數值
   - 函式重載
   - 泛型函式

3. 模組系統
   - 模組定義
   - import 語句
   - export 標記
   - 命名空間管理

## 函式定義與使用

函式是程式設計中最基本的組成單位之一，用於封裝特定功能的程式碼片段。Nim 中使用 `proc` 關鍵字定義函式，提供了強大且靈活的函式系統。

### 基本函式定義

最簡單的函式定義包含函式名、參數列表和返回類型：

```nim
proc functionName(param1: Type1, param2: Type2): ReturnType =
  # 函式主體
  return value  # 返回值
```

範例：定義一個計算兩數之和的函式

```nim
proc add(a: int, b: int): int =
  return a + b

# 呼叫函式
let sum = add(3, 5)
echo sum  # 輸出: 8
```

### 使用 result 變數

Nim 提供了隱式的 `result` 變數，可以替代 `return` 語句：

```nim
proc multiply(a: int, b: int): int =
  result = a * b  # 等同於 return a * b

echo multiply(4, 5)  # 輸出: 20
```

`result` 變數會自動初始化為返回類型的零值（對整數為 0，對字串為 ""，等等）。

### 函式參數

#### 基本參數

```nim
proc greet(name: string) =
  echo "Hello, ", name

greet("Alice")  # 輸出: Hello, Alice
```

#### 命名參數

Nim 支持在呼叫函式時使用參數名稱，提高代碼可讀性：

```nim
proc createUser(name: string, age: int, active: bool) =
  echo "Name: ", name
  echo "Age: ", age
  echo "Active: ", active

# 使用命名參數
createUser(name = "Bob", age = 30, active = true)

# 可以改變參數順序
createUser(active = false, name = "Charlie", age = 25)
```

#### 默認參數值

可以為參數指定默認值，使呼叫更加靈活：

```nim
proc connect(host: string, port: int = 80, secure: bool = false) =
  echo "Connecting to ", host, " on port ", port
  if secure:
    echo "Using secure connection"

# 使用所有默認參數
connect("example.com")  # 端口 80，非安全連接

# 覆蓋部分默認參數
connect("example.com", 8080)  # 端口 8080，非安全連接
connect("example.com", secure = true)  # 端口 80，安全連接
```

### 函式重載

Nim 支持函式重載，允許多個同名但參數不同的函式：

```nim
proc process(x: int) =
  echo "Processing integer: ", x

proc process(x: string) =
  echo "Processing string: ", x

process(42)     # 呼叫第一個函式
process("nim")  # 呼叫第二個函式
```

### 泛型函式

泛型函式可以處理不同類型的數據，提高代碼重用性：

```nim
proc max[T](a, b: T): T =
  if a > b: return a
  else: return b

echo max(5, 10)      # 整數比較，輸出: 10
echo max(3.14, 2.71) # 浮點數比較，輸出: 3.14
echo max("abc", "xyz") # 字串比較，輸出: "xyz"
```

## 模組系統

模組是 Nim 中組織和重用代碼的基本單位。每個 `.nim` 文件都是一個模組，可以被其他模組導入。

### 創建模組

假設我們創建一個 `mathutils.nim` 模組：

```nim
# mathutils.nim

# 公開的函式（可被其他模組訪問）
proc add*(a, b: int): int =
  return a + b

proc subtract*(a, b: int): int =
  return a - b

# 私有函式（僅在本模組內可用）
proc internalHelper(x: int): int =
  return x * 2
```

注意 `*` 符號標記了公開的成員，未標記的成員為私有。

### 導入模組

Nim 提供多種導入模組的方式：

#### 基本導入

```nim
# 導入整個模組
import mathutils

echo add(5, 3)       # 輸出: 8
echo subtract(10, 4) # 輸出: 6
```

#### 選擇性導入

```nim
# 僅導入指定的符號
from mathutils import add

echo add(5, 3)       # 輸出: 8
# echo subtract(10, 4) # 錯誤! subtract 未導入
```

#### 重命名導入

```nim
# 導入並重命名模組
import mathutils as math

echo math.add(5, 3)  # 輸出: 8
```

#### 使用限定符

```nim
# 導入但需使用模組名稱訪問
from mathutils import nil

echo mathutils.add(5, 3)  # 輸出: 8
```

### 命名空間管理

當需要導入多個模組時，可能會遇到命名衝突。使用限定符是避免衝突的好方法：

```nim
from mathutils import nil
from strutils import nil

echo mathutils.add(5, 3)     # 來自 mathutils 的 add
echo strutils.parseInt("42") # 來自 strutils 的 parseInt
```

## 實戰示例

### 示例 1：創建一個簡單的計算器模組

```nim
# calculator.nim
proc add*(a, b: float): float =
  result = a + b

proc subtract*(a, b: float): float =
  result = a - b

proc multiply*(a, b: float): float =
  result = a * b

proc divide*(a, b: float): float =
  if b == 0:
    raise newException(DivByZeroDefect, "除數不能為零")
  result = a / b
```

```nim
# main.nim
import calculator

proc main() =
  try:
    echo "5 + 3 = ", add(5, 3)
    echo "5 - 3 = ", subtract(5, 3)
    echo "5 * 3 = ", multiply(5, 3)
    echo "5 / 3 = ", divide(5, 3)
    echo "5 / 0 = ", divide(5, 0)  # 會拋出異常
  except DivByZeroDefect:
    echo "錯誤：除數不能為零"

main()
```

### 示例 2：使用泛型創建一個堆疊（Stack）數據結構

```nim
# stack.nim
type
  Stack*[T] = ref object
    data: seq[T]

proc newStack*[T](): Stack[T] =
  result = Stack[T](data: @[])

proc push*[T](s: Stack[T], item: T) =
  s.data.add(item)

proc pop*[T](s: Stack[T]): T =
  if s.data.len == 0:
    raise newException(IndexDefect, "堆疊為空")
  result = s.data[^1]
  s.data.setLen(s.data.len - 1)

proc isEmpty*[T](s: Stack[T]): bool =
  result = s.data.len == 0

proc size*[T](s: Stack[T]): int =
  result = s.data.len
```

```nim
# main.nim
import stack

proc main() =
  # 創建整數堆疊
  var intStack = newStack[int]()
  
  # 推入元素
  intStack.push(10)
  intStack.push(20)
  intStack.push(30)
  
  echo "堆疊大小: ", intStack.size()  # 輸出: 3
  
  # 彈出元素
  echo intStack.pop()  # 輸出: 30
  echo intStack.pop()  # 輸出: 20
  
  echo "堆疊是否為空: ", intStack.isEmpty()  # 輸出: false
  
  # 測試字串堆疊
  var strStack = newStack[string]()
  strStack.push("Nim")
  strStack.push("程式設計")
  
  echo strStack.pop()  # 輸出: 程式設計
  echo strStack.pop()  # 輸出: Nim
  
  echo "堆疊是否為空: ", strStack.isEmpty()  # 輸出: true

main()
```

## 最佳實踐

1. **函式命名**：
   - 使用動詞或動詞短語來命名函式
   - 使用駝峰命名法（例如 `calculateTotal`）
   - 名稱應反映函式的功能

2. **模組組織**：
   - 每個模組應有單一職責
   - 相關功能應組織在同一模組中
   - 大型模組應考慮拆分為多個小模組

3. **參數設計**：
   - 盡量減少函式參數數量
   - 將常用參數放在前面
   - 為可選參數提供合理的默認值

4. **錯誤處理**：
   - 使用異常處理潛在錯誤
   - 清晰記錄函式的前置條件和後置條件
   - 考慮使用 `Option[T]` 處理可能不存在的值

## 小測驗
1. 在 Nim 中，如何定義一個返回整數的函式？
2. `result` 變數在函式中有什麼作用？
3. 如何在模組中標記公開和私有的函式？
4. 導入模組時，`from moduleName import nil` 語句的作用是什麼？
5. 下面的代碼會輸出什麼？
   ```nim
   proc test(a = 10, b = 20): int =
     result = a + b
   
   echo test()
   echo test(5)
   echo test(b = 5)
   ```

## 進一步閱讀
- [Nim 官方文檔：Procedures](https://nim-lang.org/docs/manual.html#procedures)
- [Nim 官方文檔：Modules](https://nim-lang.org/docs/manual.html#modules)
- [Nim 標準庫概覽](https://nim-lang.org/docs/lib.html)
- [Nimble 套件管理器](https://github.com/nim-lang/nimble)

---

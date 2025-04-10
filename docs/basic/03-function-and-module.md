# 函式與模組

Nim 提供簡潔且強大的函式與模組化設計語法，幫助開發者撰寫具結構性和可重用的程式碼

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

## 函式定義

在 Nim 中，函式使用 `proc` 關鍵字定義。函式可以包含參數、回傳值，並支援隱式 `result` 變數與命名參數。

### 使用 `proc` 定義函式

基本函式定義：

```nim
proc add(a: int, b: int): int =
  return a + b
```

定義了一個名為 `add` 的函式，接受兩個整數參數 `a` 和 `b`，並回傳它們的加總。

使用範例：

```nim
echo add(3, 5)  # 輸出：8
```

### 隱式 `result` 變數

在 Nim 中，可以使用隱式的 `result` 變數來存放回傳值，而不需要使用 `return`：

```nim
proc multiply(a: int, b: int): int =
  result = a * b
```

`result` 自動作為函式的回傳值。

使用範例：

```nim
echo multiply(4, 6)  # 輸出：24
```

### 命名參數

Nim 支援命名參數，讓函式呼叫更具可讀性：

```nim
proc greet(name: string, age: int): string =
  result = "Hello, " & name & "! You are " & $age & " years old."

echo greet(name = "Alice", age = 30)
```

程式會輸出：

```
Hello, Alice! You are 30 years old.
```

命名參數的順序不必與函式定義一致，方便開發者在呼叫函式時清楚地指定參數。

---

## 模組化設計

Nim 支援模組化程式設計，透過模組可以將程式碼分割成多個檔案，提升程式的可維護性與重用性。模組可以使用 `import` 導入。

### 使用 `import` 導入模組

#### 導入標準模組

Nim 提供豐富的標準模組。例如，導入 `math` 模組以使用數學函式：

```nim
import math

echo sqrt(16)  # 輸出：4.0
```

以上程式使用了 `math` 模組中的 `sqrt` 函式來計算平方根。

#### 自定義模組

開發者可以建立自己的模組並導入使用。以下是範例：

**模組檔案 (`myutils.nim`)**：

```nim
proc square(x: int): int =
  result = x * x
```

**主程式檔案 (`main.nim`)**：

```nim
import myutils

echo square(5)  # 輸出：25
```

在主程式中，透過 `import myutils` 導入自定義模組 `myutils`，即可使用其中定義的函式。

#### 選擇性導入

如果只需要模組中的部分函式，可以選擇性導入：

```nim
from math import sin, cos

echo sin(3.14)  # 輸出：0.00159265291648683
echo cos(3.14)  # 輸出：-0.9999987317275395
```

僅導入了 `math` 模組中的 `sin` 和 `cos` 函式，避免導入不必要的內容。

#### 命名空間管理

為避免名稱衝突，可以使用模組名稱作為前綴：

```nim
import math

echo math.sqrt(25)  # 輸出：5.0
```

透過 `math.sqrt` 明確地調用 `math` 模組中的 `sqrt` 函式。

## 練習題

### 1. 基礎函式練習
```nim
# 1. 實現一個計算階乘的函式
proc factorial(n: int): int =
  # TODO: 實現函式內容
  
# 2. 實現一個交換兩個變數值的函式
proc swap[T](a, b: var T) =
  # TODO: 實現函式內容
```

### 2. 進階函式練習
```nim
# 1. 實現一個函式，接受可變數量的參數並返回它們的平均值
proc average(numbers: varargs[float]): float =
  # TODO: 實現函式內容

# 2. 實現一個高階函式，接受一個函式作為參數
proc applyTwice[T](x: T, f: proc(x: T): T): T =
  # TODO: 實現函式內容
```

### 3. 模組練習
```nim
# 創建一個計算器模組 (calculator.nim)
# 實現基本的數學運算函式
# 並在主程式中導入使用
```

## 小測驗
1. proc 和 func 關鍵字的區別是什麼？
2. 什麼是命名參數？何時使用它們？
3. 如何在一個模組中選擇性導出符號？
4. result 變數和 return 語句有什麼區別？

## 進一步閱讀
- [Nim 官方文檔：Procedures](https://nim-lang.org/docs/manual.html#procedures)
- [Nim 官方文檔：Modules](https://nim-lang.org/docs/manual.html#modules)
- [Nim by Example：Procedures](https://nim-by-example.github.io/procedures/)

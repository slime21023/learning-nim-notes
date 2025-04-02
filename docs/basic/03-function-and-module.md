# 函式與模組

Nim 提供簡潔且強大的函式與模組化設計語法，幫助開發者撰寫具結構性和可重用的程式碼

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

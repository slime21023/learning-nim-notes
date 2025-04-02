# 型別詳解  

## 基本型別操作

### 布林、字元、字串的操作與應用  

Nim 提供了常見的基本型別如布林 (`bool`)、字元 (`char`) 和字串 (`string`)。這些型別在程式中廣泛應用，並且擁有豐富的操作方法。

#### 布林型別 (`bool`)  
布林型別只有兩個值：`true` 和 `false`，通常用於邏輯判斷和條件控制。

**範例：布林型別操作**
```nim
let a = true
let b = false

# 布林運算
echo a and b  # 輸出: false
echo a or b   # 輸出: true
echo not a    # 輸出: false
```

#### 字元型別 (`char`)  
字元型別表示單一字符，使用單引號定義。字元可以進行比較、轉換和處理。

**範例：字元型別操作**
```nim
let c: char = 'A'

# 字元比較
echo c > 'B'  # 輸出: false

# 字元轉換
echo ord(c)   # 輸出: 65 (ASCII碼)
echo chr(66)  # 輸出: B
```

#### 字串型別 (`string`)  
字串型別表示文字序列，使用雙引號定義。字串支援拼接、分割、搜尋等操作。

**範例：字串型別操作**
```nim
let s = "Hello, Nim!"

# 字串拼接
let s2 = s & " How are you?"
echo s2  # 輸出: Hello, Nim! How are you?

# 字串搜尋
echo s.contains("Nim")  # 輸出: true

# 字串分割
for part in s.split(","):
  echo part  # 輸出: Hello 和 Nim!
```


### 整數與浮點數的運算與轉換  

整數 (`int`) 和浮點數 (`float`) 是 Nim 中常用的數值型別，支援基本運算、類型轉換和進階數學操作。

#### 整數型別 (`int`)
整數型別表示不含小數的數值，支援加減乘除等基本運算。

**範例：整數運算**
```nim
let x = 10
let y = 3

# 基本運算
echo x + y  # 輸出: 13
echo x - y  # 輸出: 7
echo x * y  # 輸出: 30
echo x div y  # 輸出: 3 (整數除法)
echo x mod y  # 輸出: 1 (取餘數)
```

#### 浮點數型別 (`float`)
浮點數型別表示含小數的數值，適合用於精確度要求較高的計算。

**範例：浮點數運算**
```nim
let a: float = 10.5
let b: float = 3.2

# 基本運算
echo a + b  # 輸出: 13.7
echo a / b  # 輸出: 3.28125

# 浮點數轉換
let c = int(a)  # 浮點數轉整數
echo c          # 輸出: 10
```

---

## 進階型別應用

### 子範圍型別：限制值的範圍  
子範圍型別允許開發者定義變數的取值範圍，從而提升程式的安全性和可讀性。

**範例：子範圍型別**
```nim
type Age = range[0..120]  # 定義年齡範圍

var myAge: Age = 25       # 合法值
# myAge = 130             # 編譯錯誤，超出範圍

echo myAge  # 輸出: 25
```

子範圍型別可以有效防止程式中出現非法值，避免潛在的錯誤。

### 元組型別：輕量級資料結構  
元組型別是一種輕量級的資料結構，用於存儲多個相關的值。元組的元素可以是不同型別，並且可以透過名稱或索引存取。

**範例：元組型別**
```nim
# 定義元組
let person = (name: "Alice", age: 30, isStudent: false)

# 存取元組元素
echo person.name       # 輸出: Alice
echo person.age        # 輸出: 30
echo person.isStudent  # 輸出: false

# 元組解構
let (n, a, s) = person
echo n  # 輸出: Alice
echo a  # 輸出: 30
echo s  # 輸出: false
```

元組型別適合用於簡單的資料封裝，尤其是在函式返回多個值時非常有用。

---

透過學習基本型別操作與進階型別應用，開發者可以更靈活地處理資料並設計高效、安全的程式邏輯。
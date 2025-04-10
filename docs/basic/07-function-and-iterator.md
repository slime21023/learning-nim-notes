# 進階函式與迭代器

## 學習目標
- 掌握函式重載的概念和應用
- 學會創建和使用自定義運算子
- 理解迭代器的工作原理
- 掌握閉包和匿名函式的使用

## 先決條件
- 已完成基本函式學習
- 理解泛型和型別系統
- 熟悉基本的迭代概念

## 關鍵概念
1. 函式進階特性
   - 函式重載
   - 運算子重載
   - 閉包
   - 匿名函式
   
2. 迭代器基礎
   - 迭代器定義
   - yield 語句
   - 閉包迭代器
   
3. 高階函式
   - 函式作為參數
   - 返回函式
   - 函式組合

## 基礎概念

### 函式重載
```nim
# 基於參數類型的重載
proc process(x: int) = echo "處理整數：", x
proc process(x: string) = echo "處理字串：", x

# 基於參數數量的重載
proc sum(a, b: int): int = a + b
proc sum(a, b, c: int): int = a + b + c
```

### 自定義運算子
```nim
# 定義新的運算子
proc `++`(a, b: int): int = a * 2 + b
proc `=~`(a, b: float): bool = abs(a - b) < 0.001

echo 5 ++ 3  # 輸出：13
echo 1.0 =~ 1.0001  # 輸出：true
```

## 實戰示例

### 1. 實現一個通用的比較器
```nim
type
  Comparator[T] = proc(a, b: T): int

proc defaultCompare[T](a, b: T): int =
  if a < b: -1
  elif a > b: 1
  else: 0

proc sortBy[T](arr: var seq[T], cmp: Comparator[T] = defaultCompare[T]) =
  # 使用自定義比較器的排序實現
  for i in 0..arr.high:
    for j in i+1..arr.high:
      if cmp(arr[i], arr[j]) > 0:
        swap(arr[i], arr[j])
```

### 2. 實現一個懶惰求值的數列生成器
```nim
iterator fibonacci(): int {.closure.} =
  var a = 0
  var b = 1
  while true:
    yield a
    let next = a + b
    a = b
    b = next

# 使用示例
proc printFirstN(n: int) =
  var count = 0
  for num in fibonacci():
    if count >= n: break
    echo num
    inc count
```

### 3. 實現函式組合器
```nim
proc compose[A, B, C](f: proc(x: B): C, g: proc(x: A): B): proc(x: A): C =
  result = proc(x: A): C = f(g(x))

# 使用示例
proc double(x: int): int = x * 2
proc addOne(x: int): int = x + 1

let doubleAndAddOne = compose(addOne, double)
echo doubleAndAddOne(5)  # 輸出：11
```

## 最佳實踐

1. 函式重載建議：
   - 保持參數類型明顯區別
   - 避免過度重載造成混淆
   - 提供清晰的文檔說明

2. 運算子重載建議：
   - 保持運算子語義直觀
   - 避免違反常規使用習慣
   - 適度使用，不要過度

3. 迭代器使用建議：
   - 適當使用懶惰求值
   - 注意資源管理
   - 考慮性能影響

## 小測驗
1. 函式重載和預設參數有什麼區別？
2. 什麼情況下應該使用閉包迭代器？
3. 為什麼要使用懶惰求值？
4. 如何避免運算子重載導致的混淆？

## 進一步閱讀
- [Nim 官方文檔：Procedures](https://nim-lang.org/docs/manual.html#procedures)
- [Nim 官方文檔：Iterators](https://nim-lang.org/docs/manual.html#iterators)
- [Nim 官方文檔：First Class Functions](https://nim-lang.org/docs/manual.html#procedures-first-class-functions)

## 函式重載與運算子

### 函式重載：多版本函式  
Nim 支援函式重載，允許開發者定義多個名稱相同但參數類型或數量不同的函式。這樣可以根據不同的輸入自動選擇適合的函式版本，提升程式的靈活性。

**範例：函式重載**
```nim
proc greet(name: string) =
  echo "Hello, ", name

proc greet(age: int) =
  echo "You are ", age, " years old"

greet("Alice")  # 輸出: Hello, Alice
greet(25)       # 輸出: You are 25 years old
```

`greet` 函式有兩個版本，分別接受 `string` 和 `int` 類型的參數，Nim 編譯器會根據傳入的參數類型自動選擇適合的版本。

---

### 自訂運算子：擴展語言功能  
Nim 允許開發者自訂運算子，讓程式碼更具可讀性和表達力。自訂運算子可以用於數學運算、邏輯操作或其他特殊用途。

**範例：自訂運算子**
```nim
proc `++`(x: int, y: int): int =
  return x * y  # 自訂 "++" 為乘法運算

let result = 3 ++ 4  # 使用自訂運算子
echo result  # 輸出: 12
```

`++` 運算子被重新定義為乘法運算，並應用於整數操作。自訂運算子需用反引號包裹 ``，以區分它與內建運算子。

---

## 迭代器

### 定義與使用迭代器  
迭代器是一種特殊的程式結構，可以用來生成序列或集合中的元素，並支援逐步迭代。Nim 的迭代器使用 `iterator` 關鍵字定義，並且可以直接用於 `for` 循環。

**範例：定義迭代器**
```nim
iterator myRange(start, stop: int): int =
  for i in start..stop:
    yield i  # 使用 `yield` 產生元素

for x in myRange(1, 5):
  echo x  # 輸出: 1, 2, 3, 4, 5
```

迭代器 `myRange` 定義了一個範圍，並透過 `yield` 關鍵字逐個返回範圍內的元素。

---

## 迭代器與函式的區別  
迭代器與普通函式的主要區別在於以下幾點：  

- **返回方式**：  
    - 函式使用 `return` 返回單一值或結構。  
    - 迭代器使用 `yield` 返回多個值，並且可以逐步生成。  


- **執行過程**：  
    - 函式執行完畢後結束。  
    - 迭代器執行時會暫停在 `yield` 處，等待下一次調用時繼續執行。

**範例：函式與迭代器的區別**
```nim
# 普通函式
proc sumRange(start, stop: int): int =
  var sum = 0
  for i in start..stop:
    sum += i
  return sum

echo sumRange(1, 5)  # 輸出: 15

# 迭代器
iterator rangeElements(start, stop: int): int =
  for i in start..stop:
    yield i

for x in rangeElements(1, 5):
  echo x  # 輸出: 1, 2, 3, 4, 5
```

普通函式 `sumRange` 計算範圍總和並返回單一值，而迭代器 `rangeElements` 則逐步返回範圍內的每個元素。

---

透過學習函式重載、自訂運算子和迭代器，開發者能夠更靈活地擴展 Nim 程式的功能，並有效處理複雜的資料結構與流程。
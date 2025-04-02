# 進階函式與迭代器  

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
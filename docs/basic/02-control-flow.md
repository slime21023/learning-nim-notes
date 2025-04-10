# 控制流程

## 學習目標
- 掌握 Nim 中的條件判斷語句
- 理解並運用各種循環結構
- 學會使用 break 和 continue 控制流程
- 熟悉 case 語句的使用

## 先決條件
- 已完成環境設置
- 了解基本變數宣告和賦值
- 熟悉基本運算符

## 關鍵概念
1. 條件判斷
   - if/elif/else 結構
   - 比較運算符
   - 邏輯運算符
   
2. 循環結構
   - for 循環
   - while 循環
   - 循環控制語句

3. 分支選擇
   - case 語句
   - 模式匹配

### 條件判斷

#### `if` 語句

`if` 語句用於執行條件判斷，支援單一條件與多重條件。

**單一條件判斷**：

```nim
var x = 10

if x > 0:
  echo "x 是正數"
```

**多重條件判斷**：

```nim
var x = -5

if x > 0:
  echo "x 是正數"
elif x < 0:
  echo "x 是負數"
else:
  echo "x 是零"
```

---

#### `case` 語句

`case` 語句用於多分支選擇，類似於其他語言的 `switch` 語句。

**基本用法**：

```nim
var day = 3

case day:
  of 1:
    echo "今天是星期一"
  of 2:
    echo "今天是星期二"
  of 3:
    echo "今天是星期三"
  else:
    echo "未知的星期"
```

**支援範圍匹配**：

```nim
var score = 85

case score:
  of 90..100:
    echo "成績優秀"
  of 75..89:
    echo "成績良好"
  of 60..74:
    echo "成績及格"
  else:
    echo "成績不及格"
```


---

### 迴圈與迭代

#### `while` 語句

`while` 語句用於基於條件的迴圈執行，當條件為 `true` 時，重複執行程式碼。

**基本用法**：

```nim
var i = 0

while i < 5:
  echo i
  i += 1
```


**使用複合條件**：

```nim
var x = 10

while x > 0 and x % 2 == 0:
  echo x
  x -= 2
```



---

#### `for` 語句

`for` 語句用於迭代集合或範圍。

**迭代範圍**：

```nim
for i in 1..5:
  echo i  # 輸出 1 到 5
```

**迭代字串**：

```nim
for c in "Nim":
  echo c  # 輸出每個字元：N, i, m
```

**迭代序列**：

```nim
var numbers = @[1, 2, 3, 4, 5]

for num in numbers:
  echo num  # 輸出序列中的每個元素
```

**使用索引迭代**：

```nim
var names = @["Alice", "Bob", "Charlie"]

for i, name in names:
  echo "索引 ", i, ": ", name
```

以上程式會輸出每個元素及其索引，例如：

```
索引 0: Alice
索引 1: Bob
索引 2: Charlie
```

## 練習題

### 1. 基礎練習
```nim
# 1. 實現一個函數，判斷一個數是否為偶數
proc isEven(n: int): bool =
  # TODO: 實現函數內容
  
# 2. 使用 while 循環計算 1 到 n 的和
proc sumToN(n: int): int =
  # TODO: 實現函數內容
```

### 2. 進階練習
```nim
# 1. 實現 FizzBuzz
# 規則：3的倍數輸出"Fizz"，5的倍數輸出"Buzz"，
# 同時是3和5的倍數輸出"FizzBuzz"
proc fizzBuzz(n: int) =
  # TODO: 實現函數內容

# 2. 使用 case 語句實現簡單計算器
proc calculator(a, b: int, op: char): int =
  # TODO: 實現函數內容
```

### 3. 挑戰題
```nim
# 實現一個函數，使用嵌套循環輸出以下圖案：
# *
# **
# ***
# ****
# *****
proc printStars(n: int) =
  # TODO: 實現函數內容
```

## 小測驗
1. if 語句可以沒有 else 分支嗎？
2. while 和 for 循環的主要區別是什麼？
3. case 語句和多個 if-elif 語句的區別是什麼？
4. 什麼情況下應該使用 break 語句？

## 進一步閱讀
- [Nim 官方文檔：Control Flow](https://nim-lang.org/docs/manual.html#statements-and-expressions)
- [Nim by Example：Control Flow](https://nim-by-example.github.io/control_flow/)

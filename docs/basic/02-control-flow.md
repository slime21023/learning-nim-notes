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

## 條件判斷

控制流程是程式中的重要組成部分，讓程式能根據不同條件執行不同的程式碼片段。Nim 提供多種控制流程結構，幫助開發者實現複雜的業務邏輯。

### `if` 語句

`if` 語句用於執行條件判斷，支援單一條件與多重條件。

#### 單一條件判斷

```nim
var x = 10

if x > 0:
  echo "x 是正數"
```

#### 多重條件判斷

```nim
var x = -5

if x > 0:
  echo "x 是正數"
elif x < 0:
  echo "x 是負數"
else:
  echo "x 是零"
```

### 比較運算符

Nim 提供以下比較運算符用於條件判斷：

| 運算符 | 描述 | 例子 |
|-------|------|------|
| `==` | 等於 | `x == y` |
| `!=` | 不等於 | `x != y` |
| `>` | 大於 | `x > y` |
| `<` | 小於 | `x < y` |
| `>=` | 大於或等於 | `x >= y` |
| `<=` | 小於或等於 | `x <= y` |

### 邏輯運算符

Nim 提供以下邏輯運算符用於組合多個條件：

| 運算符 | 描述 | 例子 |
|-------|------|------|
| `and` | 邏輯與 | `x > 0 and x < 10` |
| `or` | 邏輯或 | `x < 0 or x > 10` |
| `not` | 邏輯非 | `not (x == y)` |

### 短路求值

邏輯運算符使用短路求值（short-circuit evaluation）：

```nim
var x = 5

# 因為第一個條件為 false，所以不會執行第二個條件
if x > 10 and someExpensiveFunction():
  echo "x 大於 10 且 someExpensiveFunction 返回 true"

# 因為第一個條件為 true，所以不會執行第二個條件
if x < 10 or someExpensiveFunction():
  echo "x 小於 10 或 someExpensiveFunction 返回 true"
```

## 循環結構

循環允許重複執行一段程式碼，直到滿足特定條件為止。

### `for` 循環

`for` 循環用於遍歷序列、範圍或集合中的元素。

#### 遍歷範圍

```nim
# 遍歷 1 到 5
for i in 1..5:
  echo i  # 輸出: 1, 2, 3, 4, 5

# 遍歷 5 到 1（遞減）
for i in countdown(5, 1):
  echo i  # 輸出: 5, 4, 3, 2, 1
  
# 設定步長
for i in countup(0, 10, 2):
  echo i  # 輸出: 0, 2, 4, 6, 8, 10
```

#### 遍歷序列

```nim
var fruits = @["蘋果", "香蕉", "橙子", "葡萄"]

for fruit in fruits:
  echo "我喜歡吃", fruit

# 同時獲取索引
for i, fruit in fruits:
  echo "索引：", i, "，水果：", fruit
```

#### 遍歷字串

```nim
var name = "Nim"

for c in name:
  echo c  # 輸出: N, i, m
```

### `while` 循環

`while` 循環重複執行一段程式碼，直到條件不再滿足。

```nim
var count = 1

while count <= 5:
  echo count
  inc count  # 等同於 count += 1
```

### 循環控制語句

#### `break` 語句

`break` 語句用於立即退出循環：

```nim
for i in 1..10:
  if i > 5:
    break  # 當 i > 5 時，退出循環
  echo i  # 輸出: 1, 2, 3, 4, 5
```

#### `continue` 語句

`continue` 語句用於跳過當前迭代，直接開始下一次迭代：

```nim
for i in 1..10:
  if i mod 2 == 0:
    continue  # 跳過偶數
  echo i  # 輸出: 1, 3, 5, 7, 9
```

#### 無限循環

使用 `while true` 創建無限循環，必須使用 `break` 來退出：

```nim
var count = 1

while true:
  echo count
  inc count
  if count > 5:
    break  # 當 count > 5 時，退出循環
```

## 分支選擇

### `case` 語句

`case` 語句基於一個值在多個可能的分支之間選擇，比多個 `if-elif` 語句更清晰高效。

```nim
var dayOfWeek = 3

case dayOfWeek
of 1:
  echo "星期一"
of 2:
  echo "星期二"
of 3:
  echo "星期三"
of 4:
  echo "星期四"
of 5:
  echo "星期五"
of 6, 7:
  echo "週末"
else:
  echo "無效日期"
```

#### 範圍匹配

`case` 語句也支援範圍匹配：

```nim
var temperature = 25

case temperature
of 0..9:
  echo "很冷"
of 10..19:
  echo "涼爽"
of 20..29:
  echo "溫暖"
of 30..39:
  echo "熱"
else:
  echo "極端溫度"
```

## 實戰示例：猜數字遊戲

結合條件判斷和循環結構，我們可以創建一個簡單的猜數字遊戲：

```nim
import random, strutils

proc main() =
  # 設置隨機種子
  randomize()
  
  # 生成 1-100 之間的隨機數
  let secretNumber = rand(1..100)
  var attempts = 0
  var guess: int
  
  echo "歡迎來到猜數字遊戲！"
  echo "我已經想好了一個 1 到 100 之間的數字。"
  
  while true:
    inc attempts
    echo "請猜一個數字："
    
    try:
      guess = parseInt(readLine(stdin))
    except ValueError:
      echo "請輸入有效的數字！"
      continue
    
    if guess < secretNumber:
      echo "太小了！請猜大一點。"
    elif guess > secretNumber:
      echo "太大了！請猜小一點。"
    else:
      echo "恭喜你猜對了！"
      echo "你用了 ", attempts, " 次嘗試。"
      break

# 運行遊戲
main()
```

## 實戰示例：FizzBuzz

FizzBuzz 是一個經典的程式設計練習，規則如下：
- 如果數字能被 3 整除，輸出 "Fizz"
- 如果數字能被 5 整除，輸出 "Buzz"
- 如果數字同時能被 3 和 5 整除，輸出 "FizzBuzz"
- 否則，輸出該數字

```nim
for i in 1..100:
  if i mod 15 == 0:
    echo "FizzBuzz"
  elif i mod 3 == 0:
    echo "Fizz"
  elif i mod 5 == 0:
    echo "Buzz"
  else:
    echo i
```

另一種使用 `case` 語句的實現：

```nim
for i in 1..100:
  case i mod 15
  of 0:
    echo "FizzBuzz"
  of 3, 6, 9, 12:
    echo "Fizz"
  of 5, 10:
    echo "Buzz"
  else:
    echo i
```

## 最佳實踐

1. **避免複雜的條件判斷**：如果有多個條件需要檢查，考慮使用 `case` 語句或拆分為多個函式。

2. **謹慎使用無限循環**：確保有明確的退出條件，避免程式陷入無限迴圈。

3. **選擇正確的循環類型**：
   - 當知道迭代次數時，使用 `for` 循環
   - 當基於條件決定循環時，使用 `while` 循環

4. **使用 `countup`/`countdown`**：對於較複雜的迭代需求，使用這些函式可以更精確地控制步長和迭代方向。

## 小測驗
1. Nim 中，如何同時獲取序列的索引和值？
2. 如何在循環中跳過當前迭代而繼續下一次迭代？
3. `case` 語句相比多個 `if-elif` 語句有什麼優勢？
4. 如何創建一個從 10 到 1 的倒數循環？
5. 以下程式碼會輸出什麼結果？
   ```nim
   for i in 1..5:
     if i == 3:
       continue
     echo i
   ```

## 進一步閱讀
- [Nim 官方文檔：控制流程](https://nim-lang.org/docs/manual.html#statements-and-expressions)
- [Nim 標準庫：隨機數生成](https://nim-lang.org/docs/random.html)
- [Nim By Example：控制結構](https://nim-by-example.github.io/control_flow/)

---

# 控制流程

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

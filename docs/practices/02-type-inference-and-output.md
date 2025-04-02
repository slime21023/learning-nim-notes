# 練習 2: 型別推導與基本輸出

```nim
# 目標：掌握 Nim 的型別推導機制
# 實作要點：
# - 利用型別推導宣告變數
# - 實作各種型別的輸出格式化
# - 使用 `typeof` 驗證型別推導結果

import strformat  # 引入字串格式化模組

# 利用型別推導宣告變數
var
  count = 42                # 整數
  pi = 3.14159              # 浮點數
  name = "張小明"            # 字串
  isActive = true           # 布林值
  initial = 'T'             # 字元
  scores = [85, 92, 78, 96] # 整數陣列
  coordinates = (10.5, 20.3)# 元組 (tuple)

# 使用 echo 輸出變數值及其型別
echo "=== 基本變數及其型別 ==="
echo "count = ", count, ", 型別: ", typeof(count)
echo "pi = ", pi, ", 型別: ", typeof(pi)
echo "name = ", name, ", 型別: ", typeof(name)
echo "isActive = ", isActive, ", 型別: ", typeof(isActive)
echo "initial = ", initial, ", 型別: ", typeof(initial)
echo "scores = ", scores, ", 型別: ", typeof(scores)
echo "coordinates = ", coordinates, ", 型別: ", typeof(coordinates)

# 格式化輸出
echo "\n=== 格式化輸出 ==="
echo fmt"整數格式化: {count:5d}"  # 5位整數對齊
echo fmt"浮點數格式化 (2位小數): {pi:.2f}"
echo fmt"浮點數格式化 (科學記號): {pi:e}"
echo fmt"布林值: {isActive}"
echo fmt"字串與變數組合: 你好，{name}！"

# 型別轉換
var
  countAsFloat = float(count)
  piAsInt = int(pi)
  activeAsString = $isActive  # $ 運算子將值轉換為字串

echo "\n=== 型別轉換 ==="
echo "整數轉浮點數: ", countAsFloat, ", 型別: ", typeof(countAsFloat)
echo "浮點數轉整數: ", piAsInt, ", 型別: ", typeof(piAsInt)
echo "布林值轉字串: ", activeAsString, ", 型別: ", typeof(activeAsString)

# 自動型別推導與明確宣告的比較
var explicitCount: int = 42
echo "\n=== 明確型別宣告與型別推導比較 ==="
echo "explicitCount 型別: ", typeof(explicitCount)
echo "count 型別: ", typeof(count)
echo "兩者型別相同嗎? ", typeof(explicitCount) is typeof(count)

# 不同數值型別的自動推導
var
  smallNumber = 10         # int
  largeNumber = 1_000_000_000_000  # 大數字，可能是 int64
  fraction = 1/3           # float
  scientific = 6.022e23    # 科學記號表示法

echo "\n=== 數值型別推導 ==="
echo "smallNumber = ", smallNumber, ", 型別: ", typeof(smallNumber)
echo "largeNumber = ", largeNumber, ", 型別: ", typeof(largeNumber)
echo "fraction = ", fraction, ", 型別: ", typeof(fraction)
echo "scientific = ", scientific, ", 型別: ", typeof(scientific)

# 混合型別的序列與陣列
var
  mixedSeq = @[1, 2, 3]    # 序列
  emptySeq = newSeq[int]() # 空序列需要明確指定型別

echo "\n=== 序列與陣列 ==="
echo "mixedSeq = ", mixedSeq, ", 型別: ", typeof(mixedSeq)
echo "emptySeq = ", emptySeq, ", 型別: ", typeof(emptySeq)

# 自定義型別與型別推導
type
  Person = object
    name: string
    age: int

var student = Person(name: "李小明", age: 20)

echo "\n=== 自定義型別 ==="
echo "student = ", student, ", 型別: ", typeof(student)
echo "student.name = ", student.name, ", 型別: ", typeof(student.name)
echo "student.age = ", student.age, ", 型別: ", typeof(student.age)

# 使用字串插值
let 
  x = 10
  y = 20
  sum = x + y

echo "\n=== 字串插值 ==="
echo &"計算結果: {x} + {y} = {sum}"
echo &"計算結果 (格式化): {x:3d} + {y:3d} = {sum:3d}"

# 使用多行字串
let multiline = """
這是一個
多行字串
可以包含多行內容
"""

echo "\n=== 多行字串 ==="
echo multiline
echo "多行字串型別: ", typeof(multiline)
```

這個練習將幫助你理解 Nim 的型別推導機制，學習如何使用 `typeof` 檢查變數的型別，以及掌握不同資料類型的輸出格式化方法。你可以嘗試修改變數值或增加其他型別的變數來進一步探索 Nim 的型別系統。
# 練習 1: 註釋與變數宣告

```nim
# 目標：熟悉 Nim 註釋語法與變數宣告
# 實作要點：
# - 實作單行與多行註釋
# - 宣告並初始化各種基本型態變數（整數、浮點數、字串、布林值）
# - 展示顯式與隱式型態宣告

# 這是單行註釋

#[
  這是多行註釋
  可以跨越多行
  非常適合較長的說明文字
]#

# 顯式型態宣告 (明確指定型態)
var
  age: int = 25           # 整數型態
  height: float = 175.5   # 浮點數型態
  name: string = "小明"    # 字串型態
  isStudent: bool = true  # 布林值型態

# 使用 echo 輸出變數值
echo "顯式宣告的變數:"
echo "年齡: ", age
echo "身高: ", height
echo "姓名: ", name
echo "是學生嗎? ", isStudent

# 隱式型態宣告 (讓編譯器自動推斷型態)
var
  count = 100             # 自動推斷為 int
  price = 99.9            # 自動推斷為 float
  greeting = "你好"        # 自動推斷為 string
  hasPermission = false   # 自動推斷為 bool

echo "\n隱式宣告的變數:"
echo "數量: ", count
echo "價格: ", price
echo "問候語: ", greeting
echo "有權限嗎? ", hasPermission

# 使用 const 宣告常數
const
  PI = 3.14159
  MAX_USERS = 1000

echo "\n常數值:"
echo "PI: ", PI
echo "最大使用者數: ", MAX_USERS

# 使用 let 宣告不可變變數
let
  birthYear = 2025
  month = "4月"

echo "\n不可變變數:"
echo "出生年: ", birthYear
echo "月份: ", month
```
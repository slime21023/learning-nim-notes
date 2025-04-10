# 練習 6: 模組與實用實例

## Q1: 實作自訂模組
```nim
# 目標：實作和使用自訂模組
# 要求：
# 1. 建立一個 mathutils 模組，含有實用的數學工具函數
# 2. 實作平均值、標準差、階乘等函數
# 3. 從主程式中使用這些函數

# mathutils.nim 模組內容
# ----------------------------
# 計算數列平均值
proc mean*(values: openArray[float]): float =
  # TODO: 實作計算平均值的函數
  
# 計算數列標準差
proc standardDeviation*(values: openArray[float]): float =
  # TODO: 實作計算標準差的函數
  
# 計算階乘
proc factorial*(n: int): int =
  # TODO: 實作計算階乘的函數（支援 0! = 1）
  
# 檢查數字是否為質數
proc isPrime*(n: int): bool =
  # TODO: 實作檢查質數的函數
  
# 計算最大公因數 (GCD)
proc gcd*(a, b: int): int =
  # TODO: 實作計算最大公因數的函數
  
# 計算最小公倍數 (LCM)
proc lcm*(a, b: int): int =
  # TODO: 實作計算最小公倍數的函數
# ----------------------------

# 主程式內容 (main.nim)
# ----------------------------
import mathutils
import strformat

# 測試數學工具模組
proc testMathUtils() =
  # 測試平均值和標準差
  let data = [12.5, 10.0, 14.5, 16.0, 9.5]
  echo fmt"數據: {data}"
  echo fmt"平均值: {mean(data)}"
  echo fmt"標準差: {standardDeviation(data)}"
  
  # 測試階乘
  for i in 0..5:
    echo fmt"{i}! = {factorial(i)}"
  
  # 測試質數檢查
  for i in 1..20:
    if isPrime(i):
      echo fmt"{i} 是質數"
  
  # 測試 GCD 和 LCM
  let 
    a = 24
    b = 36
  echo fmt"GCD({a}, {b}) = {gcd(a, b)}"
  echo fmt"LCM({a}, {b}) = {lcm(a, b)}"

# 執行測試
testMathUtils()
```

Q2: 使用標準庫模組

```nim
# 目標：熟悉 Nim 標準庫的常用模組
# 要求：
# 1. 使用 strutils 處理字串
# 2. 使用 sequtils 處理序列
# 3. 使用 times 處理時間日期
# 4. 使用 random 生成隨機數

import strutils, sequtils, times, random

# 使用 strutils 示範
proc demonstrateStrUtils() =
  # TODO: 展示字串處理函數（分割、取代、轉換大小寫等）

# 使用 sequtils 示範
proc demonstrateSeqUtils() =
  # TODO: 展示序列函數（映射、過濾、轉換等）

# 使用 times 示範
proc demonstrateTimes() =
  # TODO: 展示時間日期處理函數（格式化、計算時間差等）

# 使用 random 示範
proc demonstrateRandom() =
  # TODO: 展示隨機數生成函數

# 執行所有示範
proc runDemonstrations() =
  echo "=== StrUtils 示範 ==="
  demonstrateStrUtils()
  
  echo "\n=== SeqUtils 示範 ==="
  demonstrateSeqUtils()
  
  echo "\n=== Times 示範 ==="
  demonstrateTimes()
  
  echo "\n=== Random 示範 ==="
  demonstrateRandom()

# 進行示範
runDemonstrations()
```

## Q3: 檔案操作與 JSON 處理
```nim
# 目標：實作檔案操作與 JSON 資料處理
# 要求：
# 1. 實作讀寫文字檔案功能
# 2. 實作讀寫 JSON 檔案功能
# 3. 將資料結構轉換為 JSON 並儲存
# 4. 從 JSON 檔案讀取資料並解析

import json, os

# 定義使用者資料型別
type
  User = object
    id: int
    name: string
    email: string
    active: bool

# 讀取文字檔案
proc readTextFile(filename: string): string =
  # TODO: 實作讀取文字檔案內容

# 寫入文字檔案
proc writeTextFile(filename, content: string) =
  # TODO: 實作寫入內容到文字檔案

# 將使用者資料序列化為 JSON 字串
proc userToJson(user: User): string =
  # TODO: 將使用者資料轉換為 JSON 格式

# 從 JSON 字串反序列化使用者資料
proc jsonToUser(jsonStr: string): User =
  # TODO: 將 JSON 格式轉換回使用者資料

# 儲存使用者列表到 JSON 檔案
proc saveUsers(users: seq[User], filename: string) =
  # TODO: 將多個使用者資料儲存到 JSON 檔案

# 從 JSON 檔案載入使用者列表
proc loadUsers(filename: string): seq[User] =
  # TODO: 從 JSON 檔案讀取多個使用者資料

# 測試檔案操作與 JSON 處理
proc testFileAndJsonOperations() =
  # 準備測試資料
  let users = @[
    User(id: 1, name: "小明", email: "ming@example.com", active: true),
    User(id: 2, name: "小華", email: "hua@example.com", active: false),
    User(id: 3, name: "小美", email: "mei@example.com", active: true)
  ]
  
  # 測試文字檔案操作
  let textFilename = "test.txt"
  writeTextFile(textFilename, "這是一個測試檔案\n第二行\n第三行")
  let content = readTextFile(textFilename)
  echo "讀取的檔案內容:\n", content
  
  # 測試單一使用者 JSON 轉換
  let jsonStr = userToJson(users[0])
  echo "\n使用者轉換為 JSON:\n", jsonStr
  
  let user = jsonToUser(jsonStr)
  echo "\n從 JSON 轉換回使用者:\nID: ", user.id, "\n名稱: ", user.name
  
  # 測試使用者列表 JSON 檔案操作
  let jsonFilename = "users.json"
  saveUsers(users, jsonFilename)
  echo "\n使用者資料已儲存至 ", jsonFilename
  
  let loadedUsers = loadUsers(jsonFilename)
  echo "\n從檔案載入的使用者:"
  for u in loadedUsers:
    echo "  - ", u.name, " (", u.email, ")"
  
  # 清理測試檔案
  removeFile(textFilename)
  removeFile(jsonFilename)

# 執行測試
testFileAndJsonOperations()
```


# 練習 8: 範本技術

## Q1: 基本範本使用
```nim
# 目標：熟悉 Nim 範本基本使用方式
# 要求：
# 1. 實作簡單的範本函數
# 2. 理解延遲求值與替換時機
# 3. 比較範本與一般函數的差異

import strformat

# 使用範本實作最大值
template max(a, b): untyped =
  # TODO: 實作取最大值的範本
  
# 使用函數實作最大值
proc maxProc(a, b: int): int =
  # TODO: 實作取最大值的函數
  
# 使用範本實作重複執行
template repeat(count: int, body: untyped) =
  # TODO: 實作重複執行程式碼區塊的範本
  
# 使用範本實作安全除法
template safeDivide(a, b): untyped =
  # TODO: 實作檢查除數為零的安全除法範本
  
# 使用範本實作測量執行時間
template benchmark(name: string, body: untyped) =
  # TODO: 實作測量程式碼區塊執行時間的範本
  
# 測試範本與函數差異
proc testTemplateBasics() =
  # 測試最大值範本和函數
  var 
    x = 10
    y = 20
  
  echo "使用範本, max(10, 20) = ", max(x, y)
  echo "使用函數, maxProc(10, 20) = ", maxProc(x, y)
  
  # 示範範本的副作用
  var a = 5
  echo "\n範本副作用示範:"
  echo "  執行前 a = ", a
  echo "  max(a++, 10) = ", max(a++, 10)
  echo "  執行後 a = ", a
  
  # 測試重複執行範本
  echo "\n使用 repeat 範本:"
  var sum = 0
  repeat(5):
    sum += 10
    echo "  目前總和: ", sum
  
  # 測試安全除法範本
  echo "\n使用安全除法範本:"
  echo "  10 / 2 = ", safeDivide(10, 2)
  echo "  10 / 0 = ", safeDivide(10, 0)
  
  # 測試效能測量範本
  echo "\n使用效能測量範本:"
  benchmark "計算 1 到 1000000 的總和":
    var total = 0
    for i in 1..1000000:
      total += i
    echo "  總和結果: ", total

# 執行測試
testTemplateBasics()
```

## Q2: 範本應用場景
```nim
# 目標：掌握範本的實際應用場景
# 要求：
# 1. 實作延遲語句執行的範本
# 2. 實作資源安全使用的範本
# 3. 實作調試輔助的範本

import os, strutils, times

# 實作 defer 範本
template defer(body: untyped) =
  # TODO: 實作類似 Go 語言的 defer 功能，確保程式區塊在當前範圍結束時執行
  
# 實作 withFile 範本處理文件操作
template withFile(f: untyped, filename: string, mode: FileMode, body: untyped) =
  # TODO: 實作檔案安全操作範本，確保檔案正確開啟與關閉
  
# 實作條件編譯的調試日誌範本
template debug(msg: string, body: untyped) =
  # TODO: 實作調試日誌範本，只在調試模式開啟時輸出
  
# 實作斷言範本
template myAssert(condition: bool, message: string = "") =
  # TODO: 實作自定義斷言範本，提供更多錯誤資訊
  
# 測試範本應用
proc testTemplateApplications() =
  # 測試 defer 範本
  echo "測試 defer 範本:"
  proc testDefer() =
    echo "  函數開始"
    defer:
      echo "  清理資源 (最後執行)"
    
    echo "  函數主要邏輯"
    # 如果有提前返回，defer 仍會執行
    if true:
      echo "  提前返回"
      return
    
    echo "  這一行不會被執行"
  
  testDefer()
  
  # 測試 withFile 範本
  echo "\n測試 withFile 範本:"
  let filename = "test_template.txt"
  
  # 寫入檔案
  withFile(file, filename, fmWrite):
    file.writeLine("這是測試檔案的第一行")
    file.writeLine("這是測試檔案的第二行")
    echo "  已寫入資料到檔案"
  
  # 讀取檔案
  withFile(file, filename, fmRead):
    let content = file.readAll()
    echo "  讀取的檔案內容:\n    ", content.replace("\n", "\n    ")
  
  # 清理測試檔案
  removeFile(filename)
  
  # 測試調試範本
  echo "\n測試調試範本:"
  let x = 42
  debug "檢查變數 x":
    echo "  x = ", x
    if x > 10:
      echo "  x 大於 10"
  
  # 測試斷言範本
  echo "\n測試斷言範本:"
  proc testAssert(x: int) =
    try:
      myAssert(x > 0, "x 必須為正數")
      echo "  斷言通過: x = ", x
    except:
      let e = getCurrentException()
      echo "  斷言失敗: ", e.msg
  
  testAssert(10)  # 應該通過
  testAssert(-5)  # 應該失敗

# 執行測試
testTemplateApplications()
```

## Q3: 元程式設計與DSL 
```nim
# 目標：使用範本實作領域特定語言 (DSL)
# 要求：
# 1. 使用範本實作一個簡單的 HTML 生成 DSL
# 2. 實作一個簡單的測試框架 DSL
# 3. 實作一個簡單的狀態機 DSL

import strutils

# HTML 生成 DSL
template html(body: untyped): string =
  # TODO: 實作用於生成 HTML 的頂層範本
  
template tag(name: string, body: untyped): string =
  # TODO: 實作生成 HTML 標籤的範本
  
template attr(name, value: string): string =
  # TODO: 實作生成 HTML 屬性的範本
  
# 測試框架 DSL
template suite(name: string, body: untyped) =
  # TODO: 實作測試套件範本
  
template test(name: string, body: untyped) =
  # TODO: 實作測試案例範本
  
template check(condition: bool, message: string = "") =
  # TODO: 實作檢查條件的範本
  
# 狀態機 DSL
template stateMachine(name: string, body: untyped) =
  # TODO: 實作狀態機定義範本
  
template state(name: string, body: untyped) =
  # TODO: 實作狀態定義範本
  
template transition(target: string, condition: bool = true) =
  # TODO: 實作狀態轉換範本
  
# 測試 DSL 功能
proc testDSLs() =
  # 測試 HTML 生成 DSL
  echo "=== HTML 生成 DSL 示範 ==="
  let htmlResult = html:
    tag("html"):
      tag("head"):
        tag("title"):
          "My Page"
      tag("body"):
        tag("h1"):
          "Welcome"
        tag("p"):
          "This is a " & tag("strong", "DSL") & " example."
        tag("div") & attr("class", "container"):
          tag("ul"):
            tag("li"):
              "Item 1"
            tag("li"):
              "Item 2"
  
  echo htmlResult
  
  # 測試測試框架 DSL
  echo "\n=== 測試框架 DSL 示範 ==="
  suite "數學函數測試":
    test "加法測試":
      check 2 + 2 == 4, "2+2 應等於 4"
      check 0 + 0 == 0, "0+0 應等於 0"
      check 5 + (-5) == 0, "5+(-5) 應等於 0"
    
    test "乘法測試":
      check 2 * 3 == 6, "2*3 應等於 6"
      check 0 * 5 == 0, "0*5 應等於 0"
  
  # 測試狀態機 DSL
  echo "\n=== 狀態機 DSL 示範 ==="
  var
    currentTemperature = 22
    heaterOn = false
    coolerOn = false
  
  proc prettyPrint() =
    echo "  溫度: ", currentTemperature, "°C, 暖氣: ", 
         if heaterOn: "開啟" else: "關閉", 
         ", 冷氣: ", 
         if coolerOn: "開啟" else: "關閉"
  
  stateMachine "溫控系統":
    state "正常":
      prettyPrint()
      transition "太冷", currentTemperature < 20
      transition "太熱", currentTemperature > 25
    
    state "太冷":
      heaterOn = true
      coolerOn = false
      currentTemperature += 2
      prettyPrint()
      transition "正常", currentTemperature >= 20
    
    state "太熱":
      heaterOn = false
      coolerOn = true
      currentTemperature -= 2
      prettyPrint()
      transition "正常", currentTemperature <= 25

# 執行測試
testDSLs()
```
# 練習 10: 編譯指示

## Q1: 基本編譯指示
```nim
# 目標：熟悉 Nim 常用編譯指示
# 要求：
# 1. 使用並理解 {.inline.} 編譯指示
# 2. 使用並理解 {.noSideEffect.} 編譯指示
# 3. 使用並理解 {.deprecated.} 編譯指示
# 4. 使用並理解 {.compileTime.} 編譯指示

# 使用 inline 編譯指示
proc addInline(a, b: int): int {.inline.} =
  # TODO: 實作一個簡單的加法函數並使其內聯
  result = a + b

# 未使用 inline 的對比函數
proc addNormal(a, b: int): int =
  result = a + b

# 使用 noSideEffect 編譯指示
proc pureFunction(a, b: int): int {.noSideEffect.} =
  # TODO: 實作一個純函數，不產生副作用
  result = a * b + a

# 使用 deprecated 編譯指示
proc oldFunction(x: int): int {.deprecated: "請使用 newFunction 代替".} =
  # TODO: 實作一個已棄用的函數
  result = x * 2

# 新版本的函數
proc newFunction(x: int): int =
  # TODO: 實作替代舊函數的新函數
  result = x * 2 + 1

# 使用 compileTime 編譯指示
proc compileTimeCalculation(n: int): int {.compileTime.} =
  # TODO: 實作一個編譯期計算函數
  result = 1
  for i in 1..n:
    result *= i

# 使用編譯期常數
const factorial5 = compileTimeCalculation(5)

# 測試各種編譯指示
proc testPragmas() =
  echo "測試 inline 編譯指示:"
  echo "  10 + 20 = ", addInline(10, 20)
  echo "  未使用 inline: ", addNormal(10, 20)
  
  echo "\n測試 noSideEffect 編譯指示:"
  echo "  純函數結果: ", pureFunction(5, 3)
  
  echo "\n測試 deprecated 編譯指示:"
  echo "  舊函數結果: ", oldFunction(10)
  echo "  新函數結果: ", newFunction(10)
  
  echo "\n測試 compileTime 編譯指示:"
  echo "  編譯期計算 5! = ", factorial5
  # 以下是運行時的計算，用於對比
  echo "  運行時計算 5! = ", compileTimeCalculation(5)

# 執行測試
testPragmas()
```

## Q2: 條件編譯與平台特定代碼
```nim
# 目標：掌握條件編譯與平台特定代碼的編寫
# 要求：
# 1. 使用系統檢測編譯指示實作平台特定代碼
# 2. 使用 when 語句進行條件編譯
# 3. 實作適用於不同操作系統的功能

import os

# 使用條件編譯提供平台特定的路徑分隔符
proc getPathSeparator(): string =
  # TODO: 根據不同系統返回不同的路徑分隔符
  when defined(windows):
    return "\\"
  else:
    return "/"

# 使用條件編譯提供平台特定的終端清屏命令
proc clearScreen() =
  # TODO: 根據不同系統執行不同的清屏命令
  when defined(windows):
    discard execShellCmd("cls")
  else:
    discard execShellCmd("clear")

# 使用條件編譯實作平台特定的系統資訊函數
proc getSystemInfo(): string =
  # TODO: 根據不同系統返回不同的系統信息
  var info = "系統資訊:\n"
  
  when defined(windows):
    info.add("  作業系統: Windows\n")
    when defined(amd64):
      info.add("  架構: x86_64\n")
    elif defined(i386):
      info.add("  架構: x86\n")
    else:
      info.add("  架構: 其他\n")
  elif defined(linux):
    info.add("  作業系統: Linux\n")
    when defined(amd64):
      info.add("  架構: x86_64\n")
    elif defined(arm):
      info.add("  架構: ARM\n")
    else:
      info.add("  架構: 其他\n")
  elif defined(macosx):
    info.add("  作業系統: macOS\n")
  else:
    info.add("  作業系統: 其他\n")
    
  return info

# 使用條件編譯實作調試輸出函數
proc debugPrint(msg: string) =
  # TODO: 只在調試模式下輸出訊息
  when defined(debug):
    echo "[調試] ", msg

# 使用條件編譯定義常數
const
  when defined(release):
    MaxItems = 1000000
    EnableLogging = false
  else:
    MaxItems = 1000
    EnableLogging = true

# 測試條件編譯與平台特定代碼
proc testConditionalCompilation() =
  echo "目前系統的路徑分隔符: '", getPathSeparator(), "'"
  
  # 如果需要，可以取消註釋以下行來測試清屏功能
  # clearScreen()
  
  echo getSystemInfo()
  
  debugPrint("這是一條調試消息")
  
  echo "根據編譯模式設定的常數:"
  echo "  最大項目數: ", MaxItems
  echo "  啟用日誌: ", EnableLogging

# 執行測試
testConditionalCompilation()
```

## Q3: 自定義編譯指示

```nim
# 目標：掌握自定義和使用編譯指示
# 要求：
# 1. 定義並使用自定義編譯指示
# 2. 實作帶有編譯指示的 API 文檔
# 3. 理解編譯指示與宏的結合使用

import macros

# 定義一個傳遞函數名稱的自定義編譯指示
macro funcName(procName: static[string]): untyped =
  # 建立一個編譯指示
  let pragma = newNimNode(nnkPragma)
  pragma.add(newIdentNode("inject"))
  pragma.add(
    newColonExpr(
      newIdentNode("name"),
      newLit(procName)
    )
  )
  result = pragma

# 使用自定義編譯指示
proc myFunction(a, b: int): int {.funcName: "my_function".} =
  # TODO: 實作一個使用自定義編譯指示的函數
  result = a + b

# 定義用於 API 文檔的自定義編譯指示
macro apiDoc(doc: static[string]): untyped =
  # 建立一個編譯指示
  let pragma = newNimNode(nnkPragma)
  pragma.add(newIdentNode("inject"))
  pragma.add(
    newColonExpr(
      newIdentNode("doc"),
      newLit(doc)
    )
  )
  result = pragma

# 使用 API 文檔編譯指示
proc calculateArea(width, height: float): float {.
  apiDoc: """
    計算矩形面積
    
    參數:
      - width: 寬度
      - height: 高度
      
    返回:
      矩形面積
  """
.} =
  # TODO: 實作計算面積的函數
  result = width * height

# 定義運行時性能測量的編譯指示
template measure(name: string, body: untyped) {.pragma.} =
  # TODO: 實作測量函數執行時間的編譯指示
  echo "開始測量 ", name
  let startTime = epochTime()
  body
  let endTime = epochTime()
  echo "結束測量 ", name, ", 耗時: ", (endTime - startTime) * 1000, " 毫秒"

# 使用性能測量編譯指示
proc heavyCalculation(n: int): int {.measure: "耗時計算".} =
  # TODO: 實作一個耗時計算
  result = 0
  for i in 1..n:
    for j in 1..1000:
      result += i * j

# 測試自定義編譯指示
proc testCustomPragmas() =
  echo "使用自定義函數名稱編譯指示:"
  echo "  myFunction(10, 20) = ", myFunction(10, 20)
  
  echo "\nAPI 文檔編譯指示:"
  when defined(nimdoc):
    echo "  (文檔模式下可見)"
  else:
    echo "  (在一般構建中，文檔編譯指示主要用於文檔生成)"
  
  echo "  calculateArea(5.0, 3.0) = ", calculateArea(5.0, 3.0)
  
  echo "\n性能測量編譯指示:"
  discard heavyCalculation(1000)

# 執行測試
testCustomPragmas()
```
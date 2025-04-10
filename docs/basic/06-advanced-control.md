# 進階控制與語句

Nim 提供靈活的控制語句，幫助開發者在不同情境下精確地控制程式邏輯。以下介紹 **Scopes 與 Block** 的應用，以及條件編譯相關的 **When 語句**。

---

## 學習目標
- 掌握 Nim 的進階控制結構
- 理解作用域和區塊的概念
- 學會使用條件編譯
- 掌握異常處理機制

## 先決條件
- 已完成基本控制流程學習
- 理解基本的變數作用域
- 熟悉函式和模組概念

## 關鍵概念
1. 作用域控制
   - 區塊語句
   - 變數生命週期
   - 作用域規則
   
2. 進階流程控制
   - break 和 continue
   - block 標籤
   - when 語句
   
3. 條件編譯
   - 編譯時條件判斷
   - 平台特定代碼
   - 調試代碼控制

## 基礎概念

### Scopes 與 Block

作用域 (Scope) 是變數的可見範圍，區塊 (Block) 用於創建局部作用域：

```nim
var x = 10
block myBlock:
  var x = 20  # 區塊內的新變數
  echo x      # 輸出：20
echo x        # 輸出：10
```

### 帶標籤的 Break

可以使用標籤來跳出特定的區塊：

```nim
block outer:
  for i in 1..5:
    for j in 1..5:
      if i * j > 10:
        break outer  # 跳出外層區塊
      echo i * j
```

## 實戰示例

### 1. 實現一個簡單的狀態機
```nim
type State = enum
  Ready, Running, Paused, Stopped

proc runStateMachine() =
  var state = Ready
  block stateLoop:
    while true:
      case state:
      of Ready:
        echo "準備就緒"
        state = Running
      of Running:
        echo "正在運行"
        state = Paused
      of Paused:
        echo "已暫停"
        state = Stopped
      of Stopped:
        echo "已停止"
        break stateLoop
```

### 2. 條件編譯示例
```nim
when defined(windows):
  proc getConfigPath(): string =
    r"C:\Program Files\MyApp\config.ini"
elif defined(linux):
  proc getConfigPath(): string =
    "/etc/myapp/config.ini"
else:
  proc getConfigPath(): string =
    "config.ini"

# 調試模式下的額外檢查
when defined(debug):
  proc validateConfig(path: string) =
    if not fileExists(path):
      raise newException(IOError, "配置文件不存在")
```

### 3. 異常處理模式
```nim
proc processFile(filename: string) =
  block fileProcessing:
    try:
      let file = open(filename)
      defer: file.close()  # 確保文件最終被關閉
      
      while not endOfFile(file):
        let line = file.readLine()
        try:
          processLine(line)
        except ValueError:
          echo "跳過無效行"
          continue
    except IOError:
      echo "文件處理失敗"
      break fileProcessing
```

## 最佳實踐

1. 區塊使用建議：
   - 為複雜的區塊添加有意義的標籤
   - 使用區塊來限制變數作用域
   - 避免過深的嵌套

2. 條件編譯使用建議：
   - 為不同平台提供專門的實現
   - 在調試模式下添加額外檢查
   - 避免過多的條件編譯導致代碼難以維護

3. 異常處理建議：
   - 使用 defer 確保資源釋放
   - 適當處理預期的異常
   - 提供有意義的錯誤信息

## 小測驗
1. block 語句的主要用途是什麼？
2. 什麼情況下應該使用帶標籤的 break？
3. when 語句和 if 語句的區別是什麼？
4. defer 語句的作用是什麼？

## 進一步閱讀
- [Nim 官方文檔：Control Flow](https://nim-lang.org/docs/manual.html#statements-and-expressions)
- [Nim 官方文檔：Exception Handling](https://nim-lang.org/docs/manual.html#exception-handling)
- [Nim 官方文檔：Conditional Compilation](https://nim-lang.org/docs/manual.html#conditional-compilation)

---

## Scopes 與 Block

### 作用域與區塊語句的應用

在 Nim 中，作用域 (Scope) 是指變數的存活範圍。區塊語句 (Block) 是一個邏輯單元，通常用於封裝程式碼以便控制作用域或提升可讀性。

範例：使用 `block` 明確定義作用域

```nim
var x = 10
block myBlock:
  var x = 20  # 在區塊內重新定義 x
  echo x      # 輸出：20
echo x        # 區塊外的 x，輸出：10
```

`block` 創建了局部作用域，所有在區塊內定義的變數僅在區塊內有效。

---

## `break` 與 `continue`：迴圈控制

Nim 支援 `break` 和 `continue` 語句，用於控制迴圈的執行流程。

- **`break`**: `break` 用於立即跳出迴圈。

```nim
for i in 1..10:
  if i == 5:
    break
  echo i  # 輸出：1, 2, 3, 4
```

- **`continue`**: `continue` 用於跳過本次迴圈的剩餘程式碼，並進入下一次迴圈。

```nim
for i in 1..5:
  if i == 3:
    continue
  echo i  # 輸出：1, 2, 4, 5
```

使用 `break` 和 `continue` 可以更靈活地控制迴圈的執行邏輯。

---

## **When 語句**

### **條件編譯與平台特定程式碼**

`when` 是 Nim 的條件編譯語句，用於根據編譯時的條件選擇執行的程式碼。這在寫跨平台程式或需支援特定功能時非常有用。

- **基本用法**：`when` 可用於檢查條件並執行特定程式碼。

```nim
when true:
  echo "This will always run"
```

- **平台特定程式碼**：使用 `when` 判斷運行平台，執行平台相關的程式碼。

```nim
when defined(windows):
  echo "Running on Windows"
elif defined(linux):
  echo "Running on Linux"
else:
  echo "Other platforms"
```

`defined(<platform>)` 用於檢查是否在特定平台上編譯程式。

### **條件編譯的應用場景**

- **跨平台程式設計**：根據運行環境執行不同程式碼，確保程式兼容多個平台。
- **功能切換**：根據編譯條件啟用或禁用某些功能。
- **優化編譯**：根據硬體特性或編譯器選項，生成最佳化程式碼。

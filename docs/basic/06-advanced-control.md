# 進階控制與流程管理

## 學習目標
- 掌握 Nim 的進階控制結構和區塊管理
- 理解作用域和區塊標籤的實際應用
- 學會使用條件編譯實現跨平台代碼
- 掌握完整的異常處理機制
- 學習 defer 語句的資源管理方法

## 先決條件
- 已完成基本控制流程學習
- 理解基本的變數作用域
- 熟悉函式和模組概念
- 了解錯誤處理的基本思想

## 關鍵概念
1. 作用域控制
   - 區塊語句與標籤
   - 變數生命週期管理
   - 嵌套作用域規則
   
2. 進階流程控制
   - 標籤化 break 和 continue
   - 嵌套迴圈控制
   - 狀態機實現
   
3. 條件編譯
   - 編譯時條件判斷
   - 平台特定代碼組織
   - 調試與發布版本控制
   
4. 異常處理
   - try-except-finally 結構
   - 異常類型層次
   - 自定義異常
   - defer 語句資源管理

## 區塊與作用域控制

### 基本區塊語句

區塊 (block) 用於創建局部作用域，限制變數的可見性，並提供流程控制：

```nim
# 基本區塊示例
var x = 10
block:
  var x = 20  # 區塊內的新變數，與外部的 x 不同
  echo x      # 輸出：20
echo x        # 輸出：10
```

### 帶標籤的區塊

可以為區塊添加標籤，配合 break 或 continue 使用：

```nim
block outer:
  for i in 1..5:
    for j in 1..5:
      if i * j > 10:
        break outer  # 跳出外層區塊
      echo i * j
```

### 變數作用域與遮蔽

Nim 中變數作用域遵循以下規則：

```nim
var x = "全局"

proc demo() =
  # 函式作用域開始
  echo x  # 全局
  
  block:
    # 區塊作用域開始
    var x = "區塊"  # 遮蔽外部的 x
    echo x  # 區塊
    # 區塊作用域結束
  
  echo x  # 全局，外部的 x 又可見了
  # 函式作用域結束

demo()
```

### 流程控制應用

區塊和標籤可以實現複雜的流程控制：

```nim
proc findValue(data: seq[seq[int]], target: int): (int, int) =
  block found:
    for i, row in data:
      for j, value in row:
        if value == target:
          # 找到目標值，返回位置並跳出所有循環
          result = (i, j)
          break found
    
    # 如果執行到這裡，表示未找到
    result = (-1, -1)

let matrix = @[
  @[1, 2, 3],
  @[4, 5, 6],
  @[7, 8, 9]
]

echo findValue(matrix, 5)  # 輸出: (1, 1)
echo findValue(matrix, 10) # 輸出: (-1, -1)
```

## 條件編譯

### when 語句基礎

`when` 是 Nim 的條件編譯語句，在編譯時期評估條件，用於根據編譯時的條件選擇執行的程式碼：

```nim
when sizeof(int) == 8:
  echo "64位整數系統"
else:
  echo "32位整數系統"
```

與 `if` 不同，`when` 在編譯時解析，不包含在最終的執行檔中：

```nim
# when 使用示例 - 只有符合條件的代碼會被編譯
when defined(release):
  const debugMode = false
else:
  const debugMode = true
```

### 平台特定代碼

使用條件編譯為不同平台提供特定實現：

```nim
when defined(windows):
  proc openFile(filename: string): File =
    # Windows 特定的文件開啟實現
    echo "使用 Windows API 開啟文件"
    open(filename, fmRead)
elif defined(linux):
  proc openFile(filename: string): File =
    # Linux 特定的文件開啟實現
    echo "使用 Linux 系統調用開啟文件"
    open(filename, fmRead)
else:
  proc openFile(filename: string): File =
    # 通用實現
    echo "使用通用方法開啟文件"
    open(filename, fmRead)
```

### 編譯標誌與自定義條件

可以使用 `-d:名稱` 或 `--define:名稱` 定義編譯標誌：

```nim
# 在命令行中： nim c -d:enableLogging myprog.nim

when defined(enableLogging):
  proc log(msg: string) =
    let timestamp = now()
    echo timestamp, ": ", msg
else:
  proc log(msg: string) = discard

log("這個信息在啟用日誌時才會輸出")
```

### 條件編譯組合

可以組合多個條件進行複雜的條件編譯：

```nim
when (defined(windows) or defined(macosx)) and not defined(noGui):
  import gui
  proc showMessage(msg: string) =
    gui.showMessageBox(msg)
else:
  proc showMessage(msg: string) =
    echo msg

showMessage("這條消息根據平台選擇不同的顯示方式")
```

## 異常處理

### 基本異常處理結構

Nim 使用 `try`-`except`-`finally` 結構進行異常處理：

```nim
try:
  let value = parseInt("abc")  # 這會拋出 ValueError
  echo "值是: ", value  # 不會執行到這裡
except ValueError:
  echo "無法將字符串轉換為整數"
finally:
  echo "無論是否發生異常，這裡都會執行"
```

### 多異常處理

可以捕獲並處理多種不同的異常：

```nim
try:
  case commandType:
    of "file": loadFile(path)
    of "net": fetchUrl(url)
    else: raise newException(ValueError, "未知命令類型")
except IOError as e:
  echo "IO錯誤: ", e.msg
except HttpRequestError:
  echo "網絡請求失敗"
except ValueError as e:
  echo "值錯誤: ", e.msg
except:
  echo "發生其他異常: ", getCurrentExceptionMsg()
```

### 自定義異常類型

可以創建自定義異常類型來表示特定的錯誤情況：

```nim
type
  ConfigError = object of CatchableError
  DatabaseError = object of CatchableError
  ConnectionError = object of DatabaseError

proc readConfig(path: string) =
  if not fileExists(path):
    raise newException(ConfigError, "配置文件不存在: " & path)

proc connectDatabase(connStr: string) =
  if connStr.len == 0:
    raise newException(ConnectionError, "連接字符串為空")

try:
  readConfig("config.ini")
  connectDatabase("")
except ConfigError:
  echo "配置錯誤"
except ConnectionError:
  echo "資料庫連接錯誤"
except DatabaseError:
  echo "其他資料庫錯誤"
```

### defer 語句用於資源管理

`defer` 語句用於確保資源被正確釋放，無論函式如何退出：

```nim
proc processFile(path: string) =
  var file: File
  try:
    file = open(path, fmRead)
  except IOError:
    echo "無法開啟文件"
    return
  
  defer: file.close()  # 確保文件最終會被關閉
  
  # 處理文件內容...
  for line in file.lines:
    echo line
  
  # 即使這裡發生異常，defer 仍然確保文件會被關閉
  let lastLine = file.readLine()
  echo "最後一行: ", lastLine
```

可以使用多個 `defer` 語句，它們將按照與定義相反的順序執行（後進先出）：

```nim
proc resourceManagement() =
  echo "開始"
  
  defer: echo "清理 1"
  defer: echo "清理 2"
  
  echo "處理中"
  # 輸出順序: "開始", "處理中", "清理 2", "清理 1"
```

## 實戰示例

### 示例 1：實現狀態機

使用區塊和標籤實現簡單的狀態機：

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

runStateMachine()
# 輸出:
# 準備就緒
# 正在運行
# 已暫停
# 已停止
```

### 示例 2：跨平台文件操作

使用條件編譯實現跨平台的文件操作：

```nim
# 定義跨平台的文件路徑處理
when defined(windows):
  const PathSep = '\\'
  proc getConfigPath(): string =
    r"C:\Program Files\MyApp\config.ini"
elif defined(linux) or defined(macosx):
  const PathSep = '/'
  proc getConfigPath(): string =
    when defined(linux):
      "/etc/myapp/config.ini"
    else:  # macOS
      "/Users/Shared/MyApp/config.ini"
else:
  const PathSep = '/'
  proc getConfigPath(): string =
    "config.ini"

# 通用代碼使用平台特定實現
echo "配置文件路徑: ", getConfigPath()
echo "路徑分隔符: ", PathSep
```

### 示例 3：安全的資源管理模式

結合 `try`、`except` 和 `defer` 實現安全的資源管理：

```nim
import std/[os, streams]

type
  LogLevel = enum
    Info, Warning, Error
  
  Logger = ref object
    logFile: FileStream
    isOpen: bool

proc newLogger(path: string): Logger =
  result = Logger(isOpen: false)
  try:
    result.logFile = newFileStream(path, fmWrite)
    if result.logFile == nil:
      raise newException(IOError, "無法開啟日誌文件")
    result.isOpen = true
  except:
    echo "創建日誌器失敗: ", getCurrentExceptionMsg()

proc log(logger: Logger, level: LogLevel, message: string) =
  if not logger.isOpen:
    return
  
  let levelStr = case level:
    of Info: "INFO"
    of Warning: "WARNING"
    of Error: "ERROR"
  
  logger.logFile.writeLine($now() & " [" & levelStr & "] " & message)
  logger.logFile.flush()

proc close(logger: Logger) =
  if logger.isOpen:
    logger.logFile.close()
    logger.isOpen = false

proc processWithLogging(filePath: string) =
  let logger = newLogger("application.log")
  if logger == nil or not logger.isOpen:
    echo "無法啟動日誌系統，終止處理"
    return
  
  defer: logger.close()
  
  logger.log(Info, "開始處理文件: " & filePath)
  
  try:
    if not fileExists(filePath):
      raise newException(IOError, "文件不存在")
    
    let fileContent = readFile(filePath)
    logger.log(Info, "文件大小: " & $fileContent.len & " 字節")
    
    # 進行文件處理...
    
    logger.log(Info, "文件處理完成")
  except:
    let errorMsg = getCurrentExceptionMsg()
    logger.log(Error, "處理失敗: " & errorMsg)
    echo "錯誤: ", errorMsg
```

## 最佳實踐

### 區塊使用建議

1. **標籤命名**：
   - 使用描述性的標籤名稱
   - 對於嵌套區塊，使用層次清晰的名稱如 `outerBlock`、`innerBlock`

2. **區塊範圍**：
   - 使用區塊限制變數作用域
   - 避免過長或過於複雜的區塊
   - 當不需要標籤時，使用匿名區塊 `block:`

3. **流程控制**：
   - 使用帶標籤的 break/continue 避免過多的標誌變數
   - 在複雜的嵌套迴圈中使用標籤提高代碼清晰度

### 條件編譯最佳實踐

1. **組織清晰**：
   - 將相關的條件編譯規則組織在一起
   - 使用註釋清晰說明條件編譯的目的

2. **平台差異**：
   - 盡量減少平台特定代碼的數量
   - 提取共享邏輯到通用函式中

3. **測試覆蓋**：
   - 測試所有條件分支的代碼
   - 使用模擬條件進行測試

4. **版本兼容性**：
   - 使用 `when (NimMajor, NimMinor) > (1, 0):` 處理不同版本的 Nim

### 異常處理最佳實踐

1. **捕獲特定異常**：
   - 盡量捕獲特定類型的異常，避免使用通用 `except:`
   - 針對不同異常提供有意義的處理

2. **資源管理**：
   - 在資源獲取後立即設置 `defer` 進行資源釋放
   - 處理多個資源時注意 `defer` 執行順序

3. **異常信息**：
   - 提供詳細且有用的異常信息
   - 包含上下文信息，便於排查問題

4. **錯誤恢復**：
   - 設計適當的錯誤恢復策略
   - 考慮使用重試機制處理臨時性錯誤

## 小測驗

1. 以下代碼的輸出結果是什麼？
   ```nim
   block outer:
     for i in 1..3:
       if i == 2:
         break outer
       echo i
   echo "完成"
   ```

2. `when` 語句和 `if` 語句的主要區別是什麼？

3. 以下代碼中，`defer` 語句的執行順序是怎樣的？
   ```nim
   proc test() =
     echo "開始"
     defer: echo "A"
     defer: echo "B"
     echo "結束"
   ```

4. 如何在 Nim 中定義和使用自定義異常？

5. 在以下代碼中，如果 `readConfig` 函式拋出異常，`file` 變數會被正確關閉嗎？為什麼？
   ```nim
   proc process() =
     var file = open("data.txt")
     defer: file.close()
     readConfig("config.ini")
     # 處理文件內容...
   ```

## 進一步閱讀
- [Nim 官方文檔：Control Flow](https://nim-lang.org/docs/manual.html#statements-and-expressions)
- [Nim 官方文檔：Exception Handling](https://nim-lang.org/docs/manual.html#exception-handling)
- [Nim 官方文檔：Conditions Compilation](https://nim-lang.org/docs/manual.html#implementation-specific-pragma-compiletime-reflection-pragmas)
- [Nim 異常處理最佳實踐](https://nim-lang.org/docs/tutorial.html#exceptions)

---

下一章將介紹 Nim 中的高級函式特性和迭代器，讓我們能夠創建更靈活、更強大的代碼結構。

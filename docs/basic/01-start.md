# 快速入門

## 學習目標
- 了解 Nim 語言的特點和優勢
- 安裝並設置 Nim 開發環境
- 編寫並運行第一個 Nim 程式
- 掌握基本變數宣告和型別系統

## 關鍵概念
1. Nim 語言簡介
   - 靜態型別編譯語言
   - Python 風格的語法
   - 高效能與易用性的結合
   
2. 開發環境設置
   - 安裝 Nim 編譯器
   - 編輯器/IDE 選擇
   - 編譯與執行命令
   
3. 變數與型別
   - 變數宣告
   - 型別推斷
   - 基本資料型別
   - 型別安全

## Nim 語言簡介

Nim 是一種高效且易於學習的靜態型別程式語言。它結合了 Python 的易讀性和 C 的效能，同時提供強大的型別系統和記憶體安全特性。

### 為什麼選擇 Nim？

- **高效能**：編譯為 C/C++/JavaScript，性能接近系統語言
- **易讀易寫**：語法類似 Python，簡潔且直觀
- **安全性**：強型別系統，幫助在編譯時捕捉錯誤
- **跨平台**：支援多種作業系統和硬體平台
- **多範式**：支援過程式、物件導向和函數式程式設計

## 安裝與設置

### Windows 安裝
1. 訪問 [Nim 下載頁面](https://nim-lang.org/install.html)
2. 下載 Windows 安裝包
3. 執行安裝程式，按照提示完成安裝
4. 打開命令提示字元，輸入 `nim -v` 確認安裝成功

### Linux/macOS 安裝
```bash
# 使用 choosenim（推薦）
curl https://nim-lang.org/choosenim/init.sh -sSf | sh

# 或使用包管理器
# Ubuntu/Debian
apt-get install nim

# macOS
brew install nim
```

### IDE/編輯器設置

推薦以下編輯器和擴展：
- Visual Studio Code + Nim 插件
- JetBrains IDEs + Nim 插件
- Sublime Text + NimLime
- Vim/Neovim + nim.vim

## 第一個程式

讓我們從編寫一個簡單的 "Hello World" 程式開始。

### 創建並編寫程式

建立一個名為 `hello.nim` 的檔案，並在其中輸入以下程式碼：

```nim
# hello.nim - 我的第一個 Nim 程式

# 直接輸出字串
echo "Hello World!"

# 使用函式封裝邏輯
proc greet(name: string) =
  echo "Hello, ", name, "!"

# 呼叫函式
greet("Nim")
```

### 編譯與執行

在終端機中使用以下命令編譯並執行程式：

```bash
# 完整命令
nim compile --run hello.nim

# 簡寫形式
nim c -r hello.nim
```

執行後，你應該會在終端機中看到：

```
Hello World!
Hello, Nim!
```

### 常用編譯選項

```bash
# 優化編譯（釋出模式）
nim c -d:release hello.nim

# 生成偵錯資訊
nim c -d:debug hello.nim

# 檢查程式碼但不編譯
nim check hello.nim
```

## 變數與型別

Nim 使用 `var`、`let` 和 `const` 關鍵字來宣告變數：

- `var`：可變變數，可以重新賦值
- `let`：不可變變數，一旦賦值後不能更改
- `const`：編譯時常數，必須在編譯時確定值

### 變數宣告與型別推斷

Nim 支援型別推斷，這意味著當你賦值給變數時，Nim 會自動判斷其型別：

```nim
var
  age = 25            # 型別自動推斷為 int
  name = "Alice"      # 型別自動推斷為 string
  pi = 3.14           # 型別自動推斷為 float
  isStudent = true    # 型別自動推斷為 bool

# 不可變變數
let
  maxUsers = 100
  appName = "My Nim App"

# 編譯時常數
const
  VERSION = "1.0.0"
  MAX_ATTEMPTS = 3
```

### 顯式型別宣告

你也可以明確指定變數型別：

```nim
var
  height: float = 1.75
  score: int = 100
  isActive: bool = false
  greeting: string = "Hello, Nim!"
```

### 基本型別

Nim 提供以下常見的基本型別：

| 型別 | 描述 | 範例 |
|------|------|------|
| `int` | 整數 | `42`, `-5`, `0` |
| `float` | 浮點數 | `3.14`, `-0.01` |
| `bool` | 布林型別 | `true`, `false` |
| `char` | 字元 | `'a'`, `'+'` |
| `string` | 字串 | `"Hello"` |

### 型別安全

Nim 是強型別語言，因此變數型別一旦確定，就不能更改：

```nim
var x = 10      # x 的型別為 int
# x = "hello"   # 錯誤：不能將 string 賦值給 int 型別

# 正確：型別轉換
var y = 20
var z = $y      # 將 int 轉換為 string，z = "20"
```

## 實戰示例：簡易計算器

讓我們創建一個簡單的計算器程式，展示變數和基本運算：

```nim
# calculator.nim

proc add(a, b: float): float =
  return a + b

proc subtract(a, b: float): float =
  return a - b

proc multiply(a, b: float): float =
  return a * b

proc divide(a, b: float): float =
  if b == 0:
    echo "錯誤：除數不能為零"
    return 0
  return a / b

# 主程式
echo "簡易計算器"
echo "第一個數字："
let a = parseFloat(readLine(stdin))

echo "第二個數字："
let b = parseFloat(readLine(stdin))

echo "加法結果：", add(a, b)
echo "減法結果：", subtract(a, b)
echo "乘法結果：", multiply(a, b)
echo "除法結果：", divide(a, b)
```

使用以下命令執行：
```bash
nim c -r calculator.nim
```

## 小測驗
1. Nim 的程式碼檔案通常使用什麼副檔名？
2. 在 Nim 中，哪個關鍵字用於宣告可變變數？
3. 如何編譯並立即運行一個 Nim 程式？
4. 以下哪個不是 Nim 的基本型別：(a) int, (b) decimal, (c) string, (d) bool
5. Nim 中如何輸出文字到終端機？

## 進一步閱讀
- [Nim 官方文檔](https://nim-lang.org/docs/manual.html)
- [Nim 語言教程](https://nim-lang.org/docs/tut1.html)
- [Nim by Example](https://nim-by-example.github.io/)
- [Nim 標準庫文檔](https://nim-lang.org/docs/lib.html)

---

下一章將介紹 Nim 的控制流程，包括條件語句、循環和分支結構。

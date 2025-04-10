# 快速入門

## 學習目標
- 安裝並設置 Nim 開發環境
- 了解基本的編譯和運行命令
- 編寫第一個 Nim 程式

## 環境設置

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

## IDE/編輯器設置

推薦以下編輯器和擴展：

1. Visual Studio Code
   - 安裝 "Nim" 擴展
   - 安裝 "Code Runner" 擴展（可選）

2. Sublime Text
   - 安裝 "NimLime" 套件

3. Vim/Neovim
   - 安裝 "nim.vim" 插件

## 學習路徑

1. 基礎階段（1-2週）
   - 變數和基本型別
   - 控制流程
   - 函數基礎
   
2. 進階基礎（2-3週）
   - 複雜型別
   - 模組系統
   - 錯誤處理
   
3. 中級概念（3-4週）
   - 物件導向程式設計
   - 泛型程式設計
   - 並行程式設計
   
4. 高級特性（4週以後）
   - 範本
   - 編譯指示
   - 巨集系統

### 第一個程式

Nim 是一種高效且易於學習的靜態型別程式語言。讓我們從編寫一個簡單的 "Hello World" 程式開始。

建立一個名為 `hello.nim` 的檔案，並在其中輸入以下程式碼：

```nim
# Nim 的第一個程式
echo "Hello World!"
```

在終端機中使用以下命令編譯並執行程式：

```bash
nim compile --run hello.nim
```

或使用簡短形式：

```bash
nim c -r hello.nim
```

執行後，你應該會在終端機中看到：

```
Hello World!
```

恭喜！你已成功完成你的第一個 Nim 程式。

---

### 變數與型別

Nim 使用 `var` 關鍵字來宣告變數，並支援型別推斷，這意味著當你賦值給變數時，Nim 會自動判斷其型別。

#### 宣告變數

以下是一些範例：

```nim
var
  age = 25            # 型別自動推斷為 int
  name = "Alice"       # 型別自動推斷為 string
  pi = 3.14            # 型別自動推斷為 float
  isStudent = true     # 型別自動推斷為 bool
```

你也可以明確指定變數型別：

```nim
var
  height: float = 1.75
  score: int = 100
  isActive: bool = false
  greeting: string = "Hello, Nim!"
```

#### 基本型別

Nim 提供以下常見的基本型別：

- **int**: 整數型別，例如 `10`, `-5`, `0`
- **float**: 浮點數型別，例如 `3.14`, `-0.01`, `0.0`
- **bool**: 布林型別，值為 `true` 或 `false`
- **string**: 字串型別，例如 `"Hello"`, `"Nim"`

#### 範例

```nim
var
  count: int = 42
  price: float = 19.99
  isAvailable: bool = true
  productName: string = "Nim Language"

# 使用型別推斷
var
  quantity = 10      # int
  discount = 0.15    # float
  isInStock = false  # bool
  description = "Learn Nim!"  # string

# 輸出變數值
echo count          # 輸出: 42
echo price          # 輸出: 19.99
echo isAvailable    # 輸出: true
echo productName    # 輸出: Nim Language
```

#### 型別限制

Nim 是強型別語言，因此變數型別一旦確定，就不能更改。例如：

```nim
var x = 10      # x 的型別為 int
x = "hello"     # 錯誤：不能將 string 賦值給 int 型別
```

這樣的型別系統可以幫助開發者在編譯階段捕捉潛在的錯誤，提升程式的可靠性。

## 常見問題解決

1. 找不到 nim 命令
   - 確認環境變數設置
   - 重新開啟終端機
   
2. 編譯錯誤
   - 檢查語法錯誤
   - 確認所有必要的 import
   - 使用 `nim check file.nim` 進行語法檢查

3. 執行時錯誤
   - 使用 `-d:debug` 參數編譯
   - 添加 echo 語句進行除錯

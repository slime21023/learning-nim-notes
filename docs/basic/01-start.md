# 快速入門

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

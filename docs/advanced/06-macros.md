# Nim 宏系統綱要

## 宏系統介紹
- **引言**：宏是編譯時執行的函數，用於將 Nim 語法樹轉換為不同的樹。宏系統是 Nim 語言中最強大的特性之一，允許程式員在編譯時期進行代碼轉換和生成。
- **基本概念**：與函數不同，宏在編譯時運行，接收抽象語法樹 (AST) 作為輸入，並輸出新的語法樹。
- **範例**：
    - 斷言宏：`myAssert(a == b)` 轉換為 `if a != b: quit($a " != " $b)`。
    - 調試宏：`myDebugEcho(a)` 轉換為 `echo "a: ", a`。
    - 符號微分：`diff(a*pow(x,3) + b*pow(x,2) + c*x + d, x)` 轉換為 `3*a*pow(x,2) + 2*b*x + c`。
- **宏的聲明**：使用 `macro` 關鍵字，類似於過程定義，但返回值必須是 `NimNode` 類型。

```nim
macro myMacro(arg: untyped): untyped =
  # 宏體：處理語法樹並返回新的語法樹
  result = quote do:
    echo "處理參數: ", `arg`
```

---

## 宏的參數
### 無類型參數 (Untyped Arguments)
- **定義**：在語義檢查前傳遞給宏，語法樹不需要符合 Nim 語義。
- **優點**：語法樹簡單且可預測，更容易處理。
- **缺點**：與 Nim 的重載解析不兼容，無法獲取類型信息。
- **使用場景**：當需要處理尚未經過語義檢查的代碼時，例如創建新的語法結構。

```nim
macro myUntyped(arg: untyped): untyped =
  # 可以處理任何語法結構，即使它在 Nim 中不合法
  echo arg.treeRepr
  result = arg
```

### 有類型參數 (Typed Arguments)
- **定義**：在語義檢查後傳遞給宏，語法樹已解析並包含類型信息。
- **優點**：支持重載解析，可以訪問類型信息，語法樹更複雜但功能更強大。
- **缺點**：語法樹結構更複雜，不如無類型參數直觀。
- **使用場景**：當需要獲取表達式的類型信息時。

```nim
macro myTyped(arg: typed): untyped =
  # 可以訪問參數的類型信息
  echo "參數類型: ", arg.getType.repr
  result = arg
```

### 靜態參數 (Static Arguments)
- **定義**：將值作為值而非語法樹節點傳遞給宏。
- **範例**：`macro myMacro(arg: static[int])`。
- **使用場景**：當需要在編譯時知道確切的值而不是處理語法樹時。

```nim
macro generateArray(size: static[int]): untyped =
  result = newStmtList()
  for i in 0..<size:
    result.add quote do:
      echo "元素 #", `i`
```

---

## 語法樹與代碼生成
### 語法樹表示
- **工具**：使用 `macros.treeRepr` 和 `dumpTree` 查看語法樹。
- **節點類型**：Nim 的語法樹由不同類型的節點組成，如 `nnkIdent`（標識符）、`nnkInfix`（中綴運算符）等。


```nim
import macros

dumpTree:
  var mt: MyType = MyType(a:123.456, b:"abcdef")

# 輸出類似於：
# StmtList
#   VarSection
#     IdentDefs
#       Ident "mt"
#       Ident "MyType"
#       ObjConstr
#         Ident "MyType"
#         ExprColonExpr
#           Ident "a"
#           FloatLit 123.456
#         ExprColonExpr
#           Ident "b"
#           StrLit "abcdef"
```

### 自定義語義檢查
- **工具**：使用 `macros.expectKind` 和 `macros.expectLen` 檢查參數。

```nim
macro checkInfix(arg: untyped): untyped =
# 確保參數是中綴表達式
arg.expectKind(nnkInfix)
# 確保中綴表達式有兩個操作數
arg.expectLen(3)

let operator = arg[0]
let leftOperand = arg[1]
let rightOperand = arg[2]

result = quote do:
    echo "運算符: ", `operator`
    echo "左操作數: ", `leftOperand`
    echo "右操作數: ", `rightOperand`
```

### 代碼生成
- **方法**：  
    - 使用 `newTree` 和 `newLit` 手動生成語法樹。
    - 使用 `quote do:` 語法生成代碼（更簡潔直觀）。

```nim
# 手動構建語法樹
macro manualTree(varName: untyped, value: untyped): untyped =
  result = newTree(nnkStmtList,
    newTree(nnkVarSection,
      newTree(nnkIdentDefs,
        varName,
        newTree(nnkEmpty),
        value)))

# 使用 quote do 語法
macro quoteTree(varName: untyped, value: untyped): untyped =
  result = quote do:
    var `varName` = `value`
```

- **字符串插值**：使用反引號 `` ` `` 在 `quote do:` 中插入變量。

```nim
macro interpolate(i: untyped, content: untyped): untyped =
  result = quote do:
    let `i` = `content`
    echo `i`
```

---

## 宏的實際應用
### 實現斷言宏 (myAssert)
**步驟**：

- 檢查參數是否為中綴運算符。
- 提取運算符和操作數。
- 生成斷言失敗時的錯誤信息。

```nim
import macros

macro myAssert(condition: untyped): untyped =
  # 檢查條件是否為中綴表達式
  if condition.kind == nnkInfix:
    let operator = condition[0]
    let lhs = condition[1]
    let rhs = condition[2]
    
    result = quote do:
      if not (`lhs` `operator` `rhs`):
        raise newException(AssertionDefect, $(`lhs`) & " " & 
                          astToStr(`operator`) & " " & $(`rhs`) & 
                          " 斷言失敗")
  else:
    # 處理非中綴表達式的情況
    result = quote do:
      if not `condition`:
        raise newException(AssertionDefect, 
                          "條件 " & astToStr(`condition`) & " 斷言失敗")
```

### 創建多個函數
- **範例**：使用 `newStmtList` 和 `ident` 動態生成函數。

```nim
import macros

macro generateFunctions(names: varargs[untyped]): untyped =
  result = newStmtList()
  
  for i in 0..<names.len:
    let name = names[i]
    let content = newLit("這是函數 " & $name)
    
    result.add quote do:
      proc `name`() = 
        echo `content`

# 使用宏生成多個函數
generateFunctions(foo, bar, baz)

# 現在可以調用這些函數
foo()  # 輸出: 這是函數 foo
bar()  # 輸出: 這是函數 bar
baz()  # 輸出: 這是函數 baz
```

### 自定義控制結構
- **範例**：實現一個 `repeat...until` 循環結構。

```nim
import macros

macro repeatUntil(body: untyped, condition: untyped): untyped =
  result = quote do:
    block:
      while true:
        `body`
        if `condition`:
          break

# 使用自定義控制結構
var i = 0
repeatUntil:
  echo i
  i += 1
do:
  i >= 5
```

---

## 宏的注意事項與限制
### 使用建議
- **原則**：盡量少用宏，但必要時使用。宏應該是最後的選擇，而不是第一選擇。
- **文檔**：宏的實現不足以作為文檔，需額外說明宏的行為和用法。
- **調試**：宏的調試比普通代碼更困難，可以使用 `echo treeRepr(arg)` 來檢查語法樹。
- **命名約定**：宏名稱通常使用駝峰命名法，以區別於普通函數。

### 限制
- **NimVM 限制**：宏必須用純 Nim 代碼實現，無法調用外部 C 函數。
- **副作用**：宏在編譯時執行，因此不應依賴運行時狀態。
- **遞歸限制**：宏可以遞歸，但需注意避免無限遞歸。
- **作用域問題**：宏生成的代碼在調用宏的作用域中執行，需注意變量命名衝突。

### 高級技巧
- **條件編譯**：結合 `when` 語句實現條件編譯。
- **代碼注入**：在特定位置注入代碼，例如在函數開始或結束時添加日誌。
- **AST 轉換**：遍歷和轉換現有的 AST，例如自動添加錯誤處理。

```nim
macro instrument(procDef: untyped): untyped =
  # 假設 procDef 是一個過程定義
  procDef.expectKind(nnkProcDef)
  
  # 獲取過程名稱
  let procName = procDef[0]
  # 獲取過程體
  let procBody = procDef[6]
  
  # 創建新的過程體，添加日誌
  let newBody = quote do:
    echo "進入函數 ", astToStr(`procName`)
    try:
      `procBody`
    finally:
      echo "離開函數 ", astToStr(`procName`)
  
  # 替換原過程體
  procDef[6] = newBody
  result = procDef
```

---

## 實例研究：領域特定語言 (DSL)
### HTML 生成 DSL
- **目標**：創建一個用於生成 HTML 的簡潔語法。

```nim
import macros

macro html(body: untyped): string =
  # 實現 HTML DSL
  # ...

let page = html:
  html:
    head:
      title: "我的網頁"
      meta(charset="utf-8")
    body:
      h1: "歡迎使用 Nim HTML DSL"
      p: "這是一個示例段落"
      ul:
        li: "項目 1"
        li: "項目 2"

echo page
# 輸出 HTML 字符串
```

### SQL 查詢 DSL
- **目標**：創建一個類型安全的 SQL 查詢構建器。

```nim
import macros

macro sql(query: untyped): untyped =
  # 實現 SQL DSL
  # ...

let users = sql:
  select:
    name, age, email
  from:
    users
  where:
    age > 18
  orderBy:
    name asc

# 生成類型安全的 SQL 查詢
```

---

通過這些擴展內容，開發者可以更全面地了解 Nim 宏系統的功能和應用，從基本概念到實際應用案例，幫助他們有效地利用這一強大工具進行代碼生成和轉換。
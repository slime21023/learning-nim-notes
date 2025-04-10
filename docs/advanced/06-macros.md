# Nim 宏系統

## 學習目標
- 理解 Nim 宏的基本概念和運作原理
- 掌握宏的定義和使用方法
- 學習語法樹的結構和操作方式
- 能夠使用宏進行代碼生成和轉換
- 掌握宏的實際應用場景和最佳實踐
- 了解宏的優勢與局限性

## 先決條件
- 熟悉 Nim 的基本語法和類型系統
- 了解 Nim 的模板系統
- 具備基本的編譯原理知識
- 理解抽象語法樹(AST)的基本概念
- 熟悉編譯時和運行時的概念區別

## 關鍵概念
1. **宏系統基礎**
   - 宏的定義和語法
   - 編譯時代碼轉換
   - 宏與模板的區別
   
2. **語法樹處理**
   - AST 節點類型和結構
   - 語法樹的遍歷和修改
   - 代碼生成技術
   
3. **宏參數類型**
   - 無類型參數 (untyped)
   - 有類型參數 (typed)
   - 靜態參數 (static)
   
4. **宏的實際應用**
   - 代碼自動生成
   - 自定義語法結構
   - 領域特定語言(DSL)

## 宏系統基礎

宏是編譯時執行的函數，用於將 Nim 語法樹轉換為不同的樹。宏系統是 Nim 語言中最強大的特性之一，允許程式員在編譯時期進行代碼轉換和生成。

### 宏的定義和語法

宏使用 `macro` 關鍵字定義，類似於過程定義，但返回值必須是 `NimNode` 類型：

```nim
import macros

macro myMacro(arg: untyped): untyped =
  # 宏體：處理語法樹並返回新的語法樹
  result = quote do:
    echo "處理參數: ", `arg`

# 使用宏
myMacro(42)  # 編譯為 echo "處理參數: ", 42
```

與函數不同，宏在編譯時運行，接收抽象語法樹 (AST) 作為輸入，並輸出新的語法樹。這使得宏可以進行任意的代碼轉換和生成。

### 宏與模板的區別

雖然宏和模板都是在編譯時運行的，但它們有以下關鍵區別：

```nim
# 模板示例
template logValue(value: untyped) =
  echo "值: ", value

# 宏示例
macro logValueMacro(value: untyped): untyped =
  result = quote do:
    echo "值: ", `value`

# 使用
var x = 10
logValue(x)  # 展開為: echo "值: ", x
logValueMacro(x)  # 同樣展開為: echo "值: ", x

# 但宏可以做更複雜的轉換
macro dumpVar(varName: untyped): untyped =
  # 獲取變量名作為字符串
  let varNameStr = $varName
  
  result = quote do:
    echo `varNameStr`, " = ", `varName`

dumpVar(x)  # 展開為: echo "x", " = ", x
```

主要區別：
- 模板是簡單的代碼替換，不能操作語法樹
- 宏可以檢查、分析和轉換語法樹
- 宏更強大但也更複雜
- 模板通常更簡單，足夠處理許多常見情況

### 查看和操作語法樹

使用 `macros` 模塊中的工具可以查看和操作語法樹：

```nim
import macros

# 顯示語法樹結構
dumpTree:
  var x = 10
  echo x + 20

# 輸出結構如:
# StmtList
#   VarSection
#     IdentDefs
#       Ident "x"
#       Empty
#       IntLit 10
#   Command
#     Ident "echo"
#     Infix
#       Ident "+"
#       Ident "x"
#       IntLit 20

# 操作語法樹示例
macro inspect(body: untyped): untyped =
  echo body.treeRepr  # 打印語法樹
  result = body       # 返回原始代碼不變

inspect:
  let a = 42
  echo a
```

## 宏參數類型

宏的參數可以有三種主要類型：無類型參數 (untyped)、有類型參數 (typed) 和靜態參數 (static)。

### 無類型參數 (Untyped Arguments)

無類型參數在語義檢查前傳遞給宏，語法樹不需要符合 Nim 語義：

```nim
macro printExpr(expr: untyped): untyped =
  # expr 是未經語義檢查的原始語法樹
  echo expr.treeRepr
  
  result = quote do:
    echo "表達式: ", astToStr(`expr`), " = ", `expr`

# 可以傳遞任何語法結構
printExpr(2 + 3 * 4)  # 生成: echo "表達式: ", "2 + 3 * 4", " = ", 2 + 3 * 4
```

優點：
- 語法樹簡單且可預測，更容易處理
- 可以處理尚未語義檢查的代碼，例如自定義語法

缺點：
- 不能獲取類型信息
- 與 Nim 的重載解析不兼容

### 有類型參數 (Typed Arguments)

有類型參數在語義檢查後傳遞給宏，語法樹已解析並包含類型信息：

```nim
macro showType(expr: typed): untyped =
  # 可以獲取表達式的類型
  let typeInfo = expr.getType
  
  result = quote do:
    echo "表達式 ", astToStr(`expr`), " 的類型是: ", `typeInfo`.repr

# 使用有類型參數
var str = "Hello"
showType(str)  # 輸出類型信息
showType(str.len)  # 可以顯示 int 類型
```

優點：
- 可以訪問類型信息
- 支持重載解析

缺點：
- 語法樹結構更複雜，不如無類型參數直觀
- 必須符合 Nim 的類型系統

### 靜態參數 (Static Arguments)

靜態參數將值作為值而非語法樹節點傳遞給宏：

```nim
macro generateArray(size: static[int]): untyped =
  # size 是一個實際的整數值，而不是語法樹
  var arrayNodes = newStmtList()
  
  for i in 0..<size:
    arrayNodes.add quote do:
      result[`i`] = `i` * 10
  
  result = quote do:
    var result = newSeq[int](`size`)
    `arrayNodes`
    result

# 使用靜態參數
let arr = generateArray(5)
echo arr  # 輸出: @[0, 10, 20, 30, 40]
```

靜態參數適用於需要在編譯時知道確切值的情況，特別是用於生成重複代碼或優化。

## 語法樹與代碼生成

### 語法樹節點類型

Nim 的語法樹由不同類型的節點組成，主要節點類型包括：

- `nnkIdent`：標識符（變量名、函數名等）
- `nnkIntLit`、`nnkFloatLit`、`nnkStrLit`：字面值
- `nnkInfix`：中綴運算符（如 `a + b`）
- `nnkPrefix`：前綴運算符（如 `-a`）
- `nnkPostfix`：後綴運算符（如 `a*`）
- `nnkCall`：函數調用
- `nnkCommand`：命令式調用（如 `echo "hello"`）
- `nnkDotExpr`：點運算符（如 `a.b`）
- `nnkStmtList`：語句列表
- `nnkProcDef`：過程定義
- `nnkIfStmt`：if 語句
- `nnkWhileStmt`：while 循環語句

可以使用 `macros.treeRepr` 查看語法樹的詳細結構：

```nim
import macros

macro showTree(body: untyped): untyped =
  echo body.treeRepr
  result = body

showTree:
  if x > 0:
    echo "Positive"
  else:
    echo "Non-positive"
```

### 手動構建語法樹

可以使用 `newTree`、`newLit` 等函數手動構建語法樹：

```nim
import macros

macro generateAddition(a, b: untyped): untyped =
  # 手動構建 a + b 的語法樹
  result = newTree(nnkInfix,
    newIdentNode("+"),
    a,
    b)

# 使用宏
let sum = generateAddition(5, 10)  # 展開為: 5 + 10
echo sum  # 輸出: 15
```

### 使用 Quote Do 語法

更簡潔的方法是使用 `quote do:` 語法，它允許直接編寫 Nim 代碼並將其轉換為語法樹：

```nim
import macros

macro generateIfElse(cond, thenBranch, elseBranch: untyped): untyped =
  result = quote do:
    if `cond`:
      `thenBranch`
    else:
      `elseBranch`

# 使用宏
generateIfElse(x > 10, echo "Greater than 10", echo "Not greater than 10")
```

反引號 `` ` `` 用於在 `quote do:` 中插入變量，這稱為「插值」。

### 節點迭代和遍歷

語法樹節點可以像序列一樣進行迭代：

```nim
import macros

macro inspect(body: untyped): untyped =
  for i in 0..<body.len:
    echo "節點 ", i, ": ", body[i].treeRepr
  
  result = body

# 使用宏
inspect:
  x = 10
  y = 20
```

也可以遞歸訪問節點，例如實現一個簡單的變量替換：

```nim
import macros

macro replaceVars(body: untyped): untyped =
  # 遞歸處理語法樹
  proc process(node: NimNode): NimNode =
    if node.kind == nnkIdent:
      # 替換特定標識符
      if $node == "x":
        return newLit(42)
      elif $node == "y":
        return newLit(99)
    
    # 遞歸處理其他節點
    result = copyNimNode(node)
    for i in 0..<node.len:
      result.add process(node[i])
  
  result = process(body)

# 使用宏
replaceVars:
  echo x + y  # 展開為: echo 42 + 99
```

## 實用案例

### 實現自定義斷言宏

下面是一個自定義斷言宏的實現：

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
        raise newException(AssertionDefect, 
          $(`lhs`) & " " & astToStr(`operator`) & " " & $(`rhs`) & 
          " 斷言失敗")
  else:
    # 處理非中綴表達式的情況
    result = quote do:
      if not `condition`:
        raise newException(AssertionDefect, 
          "條件 " & astToStr(`condition`) & " 斷言失敗")

# 使用自定義斷言
proc testFunction(a, b: int) =
  myAssert(a == b)
  echo "a 等於 b"

try:
  testFunction(10, 20)
except AssertionDefect as e:
  echo "捕獲到異常: ", e.msg
```

### 創建自定義控制結構

宏可以用來創建自定義控制結構，如 `repeat...until` 循環：

```nim
import macros

macro repeatUntil(body: untyped, condition: untyped): untyped =
  result = quote do:
    while true:
      `body`
      if `condition`:
        break

# 使用自定義控制結構
var i = 0
repeatUntil:
  echo "i = ", i
  i += 1
do:
  i >= 5
```

### 領域特定語言 (DSL)

宏可以用於創建領域特定語言，簡化特定領域的代碼編寫：

```nim
import macros

# 創建一個簡單的 HTML DSL
macro html(body: untyped): untyped =
  # 處理語法樹，將節點轉換為 HTML 元素
  proc processNode(node: NimNode): NimNode =
    case node.kind
    of nnkCall:
      # 處理如 div(), p() 等元素
      let tagName = $node[0]
      var attrs = ""
      var content = newStmtList()
      
      for i in 1..<node.len:
        if node[i].kind == nnkExprEqExpr:
          # 處理屬性：id="myId"
          let attrName = $node[i][0]
          let attrValue = node[i][1]
          if attrValue.kind == nnkStrLit:
            attrs.add " " & attrName & "=\"" & attrValue.strVal & "\""
        else:
          # 處理內容
          content.add processNode(node[i])
      
      # 生成 HTML 標籤
      var contentStr = ""
      for i in 0..<content.len:
        contentStr.add $content[i]
      
      result = newLit("<" & tagName & attrs & ">" & contentStr & "</" & tagName & ">")
    
    of nnkStrLit:
      # 處理純文本
      result = node
    
    of nnkStmtList:
      # 處理語句列表
      result = newStmtList()
      for i in 0..<node.len:
        result.add processNode(node[i])
    
    else:
      error("不支持的節點類型: " & $node.kind, node)
      result = node
  
  # 生成 HTML 代碼
  result = processNode(body)

# 使用 HTML DSL
let page = html:
  html:
    head:
      title: "我的網頁"
    body(id="main"):
      h1: "歡迎"
      p: "這是一個使用宏生成的網頁"
      ul:
        li: "項目 1"
        li: "項目 2"

echo page
```

### 自動生成 ORM 代碼

下面是一個使用宏自動生成對象關係映射(ORM)代碼的實用案例：

```nim
import macros, strutils

macro defineModel(modelDef: untyped): untyped =
  # 確保modelDef是類型定義
  expectKind(modelDef, nnkTypeDef)
  
  # 獲取類型名稱
  let typeName = modelDef[0]
  let typeNameStr = $typeName
  
  # 獲取對象定義部分
  var objDef = modelDef[2]
  if objDef.kind == nnkObjectTy:
    objDef = objDef[2]  # 獲取對象字段列表
  
  # 生成表名
  let tableName = newLit(toLowerAscii(typeNameStr))
  
  # 生成字段信息
  var fieldDecls = newSeq[NimNode]()
  for field in objDef:
    if field.kind == nnkIdentDefs:
      let fieldName = field[0]
      let fieldType = field[1]
      let fieldNameStr = newLit($fieldName)
      
      fieldDecls.add quote do:
        Field(name: `fieldNameStr`, typ: $typeof(`typeName`.`fieldName`))
  
  # 創建字段列表
  var fieldsArrayNode = newTree(nnkBracket)
  for field in fieldDecls:
    fieldsArrayNode.add field
  
  # 生成模型元數據
  result = quote do:
    type
      `modelDef`
      
      # 字段信息類型
      Field = object
        name: string
        typ: string
    
    # 模型元數據
    proc tableName(_: type[`typeName`]): string = `tableName`
    proc fields(_: type[`typeName`]): seq[Field] = @`fieldsArrayNode`
    
    # 生成 SQL 創建表語句
    proc createTableSql(_: type[`typeName`]): string =
      result = "CREATE TABLE " & `tableName` & " ("
      var fieldDefs = newSeq[string]()
      
      for field in `typeName`.fields():
        var sqlType = ""
        
        # 映射 Nim 類型到 SQL 類型
        case field.typ
        of "string": sqlType = "TEXT"
        of "int": sqlType = "INTEGER"
        of "float": sqlType = "REAL"
        of "bool": sqlType = "BOOLEAN"
        else: sqlType = "TEXT"
        
        fieldDefs.add(field.name & " " & sqlType)
      
      result.add(fieldDefs.join(", "))
      result.add(");")

# 使用宏定義模型
defineModel:
  User = object
    id: int
    username: string
    email: string
    active: bool

# 使用生成的函數
echo User.tableName()  # 輸出: user
echo User.fields()     # 輸出: @[(name: "id", typ: "int"), ...]
echo User.createTableSql()  # 輸出: CREATE TABLE user (id INTEGER, username TEXT, ...)
```

## 練習與挑戰

1. **基本宏實現**
   - 實現一個 `debug` 宏，自動輸出變量名和值
   - 創建一個 `times` 宏，重複執行代碼塊指定次數
   - 設計一個 `benchmark` 宏，測量代碼塊執行時間

2. **語法樹操作**
   - 實現一個語法樹遍歷器，計數代碼中的函數調用數量
   - 創建一個代碼轉換宏，將所有浮點字面值轉換為整數
   - 開發一個簡單的代碼求值器，計算常量表達式的值

3. **高級宏應用**
   - 實現一個簡單的測試框架，自動收集和運行測試函數
   - 開發一個事件系統，支持事件定義、訂閱和發布
   - 設計一個自動生成 builder 模式代碼的宏

4. **DSL 設計**
   - 創建一個狀態機 DSL，用宏定義狀態和轉換
   - 實現一個簡單的查詢語言 DSL，轉換為 SQL 查詢
   - 開發一個圖形描述 DSL，生成可視化代碼

## 自我評估問題

1. 宏與模板的主要區別是什麼？各自的優勢和適用場景是什麼？
2. 解釋 `untyped` 和 `typed` 參數的區別。什麼情況下應該使用哪種類型的參數？
3. 什麼是抽象語法樹 (AST)？如何在 Nim 中查看和操作語法樹？
4. 解釋 `quote do:` 語法的作用和使用方法。如何在其中使用插值？
5. 宏如何幫助創建領域特定語言 (DSL)？請舉一個例子。
6. 在宏中，如何處理錯誤情況並向用戶提供有用的錯誤消息？
7. 解釋 "衛生宏" 的概念。Nim 如何確保宏生成的代碼不會與現有代碼發生衝突？
8. 宏的潛在缺點和風險是什麼？如何確保宏代碼的可維護性？

## 進一步閱讀

- [Nim 官方文檔：宏](https://nim-lang.org/docs/manual.html#macros)
- [Nim 標準庫：macros 模塊](https://nim-lang.org/docs/macros.html)
- [Nim by Example：宏](https://nim-by-example.github.io/macros/)
- [Nim 元編程指南](https://nim-lang.org/docs/tut3.html)
- [實用 Nim 宏教程](https://hookrace.net/blog/introduction-to-metaprogramming-in-nim/)

## 小結

本章深入探討了 Nim 的宏系統，這是一項強大的元編程工具，允許開發者在編譯時期進行代碼轉換和生成。宏使得 Nim 具有了卓越的表達能力，可以創建無法用普通代碼實現的抽象和優化。

宏在編譯時執行，接收語法樹作為輸入，並輸出轉換後的語法樹。這種設計使得宏可以深入理解和操作代碼的結構，從而實現功能強大的代碼生成和轉換。

通過宏，我們可以實現自定義的控制結構、領域特定語言、自動化的模板代碼生成等高級功能。這些功能在減少重複代碼、提高代碼可讀性和減少錯誤方面發揮了重要作用。

然而，宏的強大功能伴隨著複雜性。不當使用宏可能導致難以理解和維護的代碼。因此，在使用宏時，應遵循以下原則：

- 只在確實需要元編程能力時使用宏
- 為宏提供清晰的文檔和示例
- 確保宏生成的代碼易於理解和調試
- 考慮使用更簡單的替代方案，如模板或泛型

掌握宏系統需要時間和實踐，但它是 Nim 編程中最強大的工具之一，能夠極大地擴展語言的能力，使開發者能夠創建簡潔、高效且表達力強的代碼。
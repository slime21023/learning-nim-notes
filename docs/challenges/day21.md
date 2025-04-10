# 第21天：簡易編譯器/直譯器

## 專案目標

開發一個簡單的程式語言直譯器或編譯器，能夠解析和執行基本的程式碼。

## 專案需求

1. 設計一個簡單的程式語言，支援以下特性：
   - 變數宣告和賦值
   - 基本運算（加、減、乘、除）
   - 條件語句（if-else）
   - 循環結構（while或for）
   - 函數定義和呼叫
   - 輸入和輸出功能

2. 實現語言解析器，包括：
   - 詞法分析（將原始碼轉換為標記）
   - 語法分析（將標記轉換為抽象語法樹）
   - 語義分析（類型檢查和變數作用域）

3. 實現執行環境：
   - 直譯器模式（即時執行代碼）
   - 或編譯器模式（將代碼轉換為中間表示或目標代碼）

4. 提供命令列界面，支援以下功能：
   - 執行代碼檔案
   - 交互式REPL環境（讀取-求值-列印循環）
   - 調試和錯誤報告選項

## 實現提示

### 基本結構
```nim
# simple_interpreter.nim
import strutils, streams, tables, strformat, parseopt, os

type
  TokenType = enum
    # 定義語言的標記類型，如
    ttNumber, ttIdentifier, ttPlus, ttMinus, ttMultiply, ttDivide,
    ttAssign, ttEqual, ttLess, ttGreater, ttLParen, ttRParen, ttSemicolon,
    ttIf, ttElse, ttWhile, ttFor, ttFunction, ttReturn,
    ttEOF, ttError

  Token = object
    tokenType: TokenType
    lexeme: string
    line: int
    column: int

  NodeType = enum
    # 定義抽象語法樹的節點類型
    ntBinary, ntUnary, ntLiteral, ntVariable, ntAssign,
    ntIf, ntWhile, ntBlock, ntFunction, ntCall, ntReturn

  Node = ref object
    line: int
    column: int
    case nodeType: NodeType
    of ntBinary:
      operator: TokenType
      left, right: Node
    of ntUnary:
      unaryOp: TokenType
      operand: Node
    of ntLiteral:
      value: string
    of ntVariable:
      name: string
    of ntAssign:
      target: string
      expression: Node
    of ntIf:
      condition: Node
      thenBranch: Node
      elseBranch: Node
    of ntWhile:
      whileCondition: Node
      body: Node
    of ntBlock:
      statements: seq[Node]
    of ntFunction:
      funcName: string
      params: seq[string]
      funcBody: Node
    of ntCall:
      callee: string
      arguments: seq[Node]
    of ntReturn:
      returnValue: Node

  Lexer = object
    source: string
    tokens: seq[Token]
    position: int
    line: int
    column: int

  Parser = object
    tokens: seq[Token]
    current: int

  Interpreter = object
    globals: TableRef[string, string]
    environment: TableRef[string, string]
    functions: TableRef[string, Node]

proc initLexer(source: string): Lexer =
  # 初始化詞法分析器

proc scanTokens(lexer: var Lexer): seq[Token] =
  # 將源代碼轉換為標記序列

proc initParser(tokens: seq[Token]): Parser =
  # 初始化語法分析器

proc parse(parser: var Parser): seq[Node] =
  # 解析標記序列為抽象語法樹

proc initInterpreter(): Interpreter =
  # 初始化直譯器

proc evaluate(interpreter: var Interpreter, node: Node): string =
  # 求值表達式節點

proc execute(interpreter: var Interpreter, node: Node) =
  # 執行語句節點

proc executeBlock(interpreter: var Interpreter, nodes: seq[Node]) =
  # 執行代碼塊

proc interpret(interpreter: var Interpreter, nodes: seq[Node]) =
  # 執行整個程式

proc repl() =
  # 實現交互式命令列

proc runFile(filename: string) =
  # 執行程式檔案

proc parseCommandLine() =
  # 解析命令列參數

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### 詞法分析
- 使用有限狀態機識別標記
- 處理數字、標識符、運算符和關鍵字
- 跟蹤行號和列號以便錯誤報告

### 語法分析
- 使用遞歸下降解析器構建抽象語法樹
- 實現運算符優先級和結合性
- 處理錯誤並提供有用的錯誤訊息

### 執行環境
- 使用符號表管理變數和函數
- 實現作用域和變數生命週期
- 處理運行時錯誤和異常情況

## 進階挑戰

1. 添加更多數據類型（如字串、數組、布林值）
2. 實現更複雜的控制結構（如switch-case、try-catch）
3. 添加模組化和導入功能
4. 實現一個簡單的標準庫
5. 將代碼編譯為中間表示（如位元組碼）
6. 添加靜態類型系統和類型檢查

## 專案結構

```
day21/
├── simple_interpreter.nim   # 主程式檔案
├── examples/                # 範例程式碼
│   ├── hello.mylang
│   └── fibonacci.mylang
└── README.md                # 專案說明
```

## 學習資源

- [編譯器設計基礎](https://en.wikipedia.org/wiki/Compiler)
- [遞歸下降解析器](https://en.wikipedia.org/wiki/Recursive_descent_parser)
- [抽象語法樹](https://en.wikipedia.org/wiki/Abstract_syntax_tree)
- [符號表和作用域](https://en.wikipedia.org/wiki/Symbol_table)

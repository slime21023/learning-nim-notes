# 第26天：DSL（領域特定語言）

## 專案目標

使用Nim的巨集功能，設計並實現一個領域特定語言（DSL），針對特定問題領域提供簡潔、直觀的表達方式。

## 專案需求

1. 選擇一個適合DSL的問題領域，例如：
   - 數據驗證
   - 網頁模板
   - 配置管理
   - 自動化測試
   - 遊戲腳本
   - 狀態機定義

2. 設計DSL語法：
   - 應該簡潔、直觀且符合領域特性
   - 提供比一般Nim程式碼更高層次的抽象
   - 保持對Nim類型系統和語義的兼容

3. 使用Nim巨集實現DSL：
   - 定義巨集來轉換DSL語法為標準Nim程式碼
   - 提供適當的錯誤檢查和報告
   - 實現必要的運行時支援庫

4. 創建示例和文檔：
   - 提供DSL的完整使用示例
   - 編寫清晰的文檔說明語法和用法
   - 說明DSL相比標準代碼的優勢

## 實現提示

### 基本結構（以數據驗證DSL為例）
```nim
# validation_dsl.nim
import macros, strutils, strformat, sequtils, tables

# 運行時支援類型和函數
type
  ValidationError = object
    field: string
    message: string
  
  ValidationResult = object
    valid: bool
    errors: seq[ValidationError]

proc newValidationResult(): ValidationResult =
  result.valid = true
  result.errors = @[]

proc addError(r: var ValidationResult, field, message: string) =
  r.valid = false
  r.errors.add(ValidationError(field: field, message: message))

# DSL巨集定義
macro validate*(body: untyped): untyped =
  # 展開 validate 區塊，生成驗證函數
  result = newStmtList()
  
  # 函數定義
  let procName = newIdentNode("validateData")
  let dataParam = newIdentDefs(newIdentNode("data"), newIdentNode("object"), newEmptyNode())
  
  var procBody = newStmtList()
  procBody.add(parseStmt("var result = newValidationResult()"))
  
  # 處理驗證規則
  for node in body:
    # 解析 DSL 語法並生成驗證代碼
    # ...
  
  procBody.add(parseStmt("return result"))
  
  # 構建最終函數
  result.add(
    newProc(
      name = procName,
      params = @[
        newIdentNode("ValidationResult"),
        dataParam
      ],
      body = procBody
    )
  )

# DSL使用範例
when isMainModule:
  type
    User = object
      name: string
      email: string
      age: int
  
  # 使用我們的DSL定義驗證規則
  validate:
    field "name":
      required "Name is required"
      minLength 3, "Name must be at least 3 characters"
    
    field "email":
      required "Email is required"
      match r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$", "Invalid email format"
    
    field "age":
      required "Age is required"
      min 18, "User must be 18 or older"
      max 120, "Invalid age"
  
  # 測試驗證
  let user = User(name: "Jo", email: "invalid", age: 16)
  let result = validateData(user)
  
  if not result.valid:
    echo "Validation failed:"
    for error in result.errors:
      echo &"  {error.field}: {error.message}"
  else:
    echo "Validation passed!"
```

### 巨集開發
- 理解并使用Nim的`macro`系統
- 使用抽象語法樹（AST）操作來轉換DSL代碼
- 生成類型安全且優化的Nim代碼
- 提供有意義的編譯時錯誤消息

### DSL設計原則
- 保持語法簡潔且一致
- 使DSL盡可能接近自然語言
- 避免冗長和重複
- 考慮可擴展性

### 示例開發
- 為DSL創建真實世界的使用場景
- 展示DSL如何簡化複雜問題的解決方案
- 比較使用和不使用DSL的代碼差異

## 進階挑戰

1. 實現巢狀DSL結構
2. 添加自定義錯誤處理和調試功能
3. 支援外部庫或配置的整合
4. 實現編譯時驗證和優化
5. 創建可視化工具來展示DSL結構
6. 支援從外部檔案載入DSL代碼

## 專案結構

```
day26/
├── dsl.nim             # DSL實現檔案
├── runtime.nim         # 運行時支援庫
├── examples/           # 示例檔案
│   ├── simple.nim
│   └── advanced.nim
└── README.md           # 專案說明
```

## 學習資源

- [Nim巨集文檔](https://nim-lang.org/docs/manual.html#macros)
- [Nim AST](https://nim-lang.org/docs/macros.html#ast-helpers)
- [DSL設計原則](https://tomassetti.me/domain-specific-languages/)
- [內部DSL與外部DSL](https://martinfowler.com/bliki/DomainSpecificLanguage.html)

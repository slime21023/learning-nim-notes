# 練習 9: 巨集技術

## Q1: 基本巨集使用
```nim
# 目標：了解 Nim 巨集基礎及其應用
# 要求：
# 1. 實作簡單的函數封裝巨集
# 2. 實作根據類型生成代碼的巨集
# 3. 實作自動產生樣板程式碼的巨集

import macros

# 實作簡單的 debug 巨集，用於輸出表達式及其結果
macro debug(arg: untyped): untyped =
  # TODO: 實作輸出表達式及其結果的巨集
  let argStr = arg.toStrLit
  result = quote do:
    let res = `arg`
    echo `argStr`, " = ", res
    res

# 實作自動產生 getter 和 setter 的巨集
macro genAccessors(objType: typed): untyped =
  # TODO: 實作自動為物件類型產生 getter 和 setter 函數的巨集
  result = newStmtList()
  let objTypeImpl = objType.getType[1].getImpl
  
  for identDef in objTypeImpl[2][2]:
    if identDef.kind == nnkIdentDefs:
      let fieldName = identDef[0]
      let fieldType = identDef[1]
      let getterName = ident("get" & $fieldName)
      let setterName = ident("set" & $fieldName)
      
      # 生成 getter
      let getter = quote do:
        proc `getterName`(obj: `objType`): `fieldType` =
          obj.`fieldName`
      
      # 生成 setter
      let setter = quote do:
        proc `setterName`(obj: var `objType`, value: `fieldType`) =
          obj.`fieldName` = value
      
      result.add(getter)
      result.add(setter)

# 實作自動產生 toString 方法的巨集
macro genToString(objType: typed): untyped =
  # TODO: 實作自動為物件類型產生 toString 方法的巨集
  let objTypeImpl = objType.getType[1].getImpl
  var fieldList = newSeq[NimNode]()
  
  for identDef in objTypeImpl[2][2]:
    if identDef.kind == nnkIdentDefs:
      let fieldName = identDef[0]
      fieldList.add(fieldName)
  
  # 生成 toString 方法
  result = quote do:
    proc toString(obj: `objType`): string =
      result = $(`objType`) & "("
      var first = true
  
  let procBody = result[0][6]
  
  for fieldName in fieldList:
    let fieldStr = newLit($fieldName & ": ")
    let comma = newLit(", ")
    
    procBody.add quote do:
      if first:
        first = false
      else:
        result.add(`comma`)
      result.add(`fieldStr`)
      result.add($obj.`fieldName`)
  
  procBody.add quote do:
    result.add(")")

# 定義測試用的物件類型
type
  Person = object
    name: string
    age: int
    height: float

# 為 Person 類型生成訪問器
genAccessors(Person)

# 為 Person 類型生成 toString 方法
genToString(Person)

# 測試巨集功能
proc testMacroBasics() =
  # 測試 debug 巨集
  echo "測試 debug 巨集:"
  debug(5 + 10)
  debug("Hello" & " World")
  
  var x = 100
  debug(x * 2)
  
  # 測試自動生成的 getter 和 setter
  echo "\n測試自動生成的訪問器:"
  var p = Person(name: "小明", age: 25, height: 175.5)
  
  echo "  初始值:"
  echo "    名稱: ", getName(p)
  echo "    年齡: ", getAge(p)
  echo "    身高: ", getHeight(p)
  
  setName(p, "小華")
  setAge(p, 30)
  setHeight(p, 180.0)
  
  echo "  修改後:"
  echo "    名稱: ", getName(p)
  echo "    年齡: ", getAge(p)
  echo "    身高: ", getHeight(p)
  
  # 測試自動生成的 toString 方法
  echo "\n測試自動生成的 toString 方法:"
  echo "  ", toString(p)

# 執行測試
testMacroBasics()
```

## Q2: 抽象語法樹操作

```nim
# 目標：深入理解 Nim 抽象語法樹 (AST) 操作
# 要求：
# 1. 實作能夠檢查 AST 結構的巨集
# 2. 實作能夠修改 AST 的巨集
# 3. 實作能夠動態建立 AST 的巨集

import macros

# 實作列印 AST 結構的巨集
macro dumpAst(arg: untyped): untyped =
  # TODO: 實作一個能夠列印出參數 AST 結構的巨集
  echo arg.treeRepr
  result = arg

# 實作收集變數名稱的巨集
macro collectVarNames(code: untyped): untyped =
  # TODO: 實作一個能夠收集程式區塊中所有變數宣告的巨集
  var names = newSeq[string]()
  
  # 遞迴搜尋變數宣告
  proc searchVarSection(node: NimNode) =
    case node.kind
    of nnkVarSection:
      # 找到變數宣告區段
      for child in node.children:
        if child.kind == nnkIdentDefs:
          # 依次處理每個標識符
          for i in 0..<child.len-2:
            names.add($child[i])
    else:
      # 繼續搜尋子節點
      for child in node:
        searchVarSection(child)
  
  searchVarSection(code)
  
  result = code
  # 在代碼後面添加列印變數名稱的語句
  let namesListStr = newLit("找到的變數: " & $names)
  result.add quote do:
    echo `namesListStr`

# 實作將變數初始化為零的巨集
macro zeroInit(code: untyped): untyped =
  # TODO: 實作一個能夠自動將數值變數初始化為 0 的巨集
  result = code.copyNimTree()
  
  # 遞迴處理變數宣告
  proc processVarSection(node: NimNode) =
    case node.kind
    of nnkVarSection:
      # 處理變數宣告區段
      for i, child in node.pairs:
        if child.kind == nnkIdentDefs:
          # 檢查是否為數值類型且沒有初始化值
          let varType = child[^2]
          let initValue = child[^1]
          
          # 如果沒有初始化值 (empty) 且是數值類型，設為 0
          if initValue.kind == nnkEmpty and
            (varType.kind == nnkIdent and ($varType == "int" or 
                                         $varType == "float" or 
                                         $varType == "int64" or 
                                         $varType == "uint" or 
                                         $varType == "uint64")):
            # 替換為 0 值
            node[i][^1] = newLit(0)
    else:
      # 繼續處理子節點
      for i in 0..<node.len:
        if node[i].kind != nnkNilLit:
          processVarSection(node[i])
  
  processVarSection(result)

# 實作自動產生 case 語句的巨集
macro genCaseStatement(value: typed, handlers: untyped): untyped =
  # TODO: 實作一個能夠動態生成 case 語句的巨集
  # 檢查 handlers 的結構
  expectKind(handlers, nnkStmtList)
  
  # 建立 case 語句
  result = newNimNode(nnkCaseStmt)
  result.add(value)
  
  # 處理每一個 handler
  for handler in handlers:
    expectKind(handler, nnkCall)
    
    let caseValue = handler[0]
    var caseBody: NimNode
    
    if handler.len > 1:
      if handler[1].kind == nnkStmtList:
        caseBody = handler[1]
      else:
        caseBody = newStmtList(handler[1])
    else:
      caseBody = newStmtList()
    
    # 建立 case 分支
    let branch = newNimNode(nnkOfBranch)
    branch.add(caseValue)
    branch.add(caseBody)
    
    result.add(branch)
  
  # 添加默認分支
  let defaultBranch = newNimNode(nnkElse)
  defaultBranch.add(newStmtList(quote do:
    echo "沒有處理程序"
  ))
  result.add(defaultBranch)

# 測試 AST 操作巨集
proc testAstManipulation() =
  # 測試列印 AST 結構
  echo "測試列印 AST 結構:"
  dumpAst:
    var x = 10
    if x > 5:
      echo "x 大於 5"
  
  # 測試收集變數名稱
  echo "\n測試收集變數名稱:"
  collectVarNames:
    var
      a = 10
      b = "Hello"
      c: float
  
  # 測試零初始化
  echo "\n測試零初始化:"
  zeroInit:
    var
      x: int
      y: float
      z = 42
      name: string
    
    echo "x = ", x
    echo "y = ", y
    echo "z = ", z
    echo "name = ", name
  
  # 測試動態生成 case 語句
  echo "\n測試動態生成 case 語句:"
  var status = 200
  
  genCaseStatement(status):
    200:
      echo "OK"
    404:
      echo "Not Found"
    500:
      echo "Server Error"

# 執行測試
testAstManipulation()
```

## Q3: 領域特定語言 (DSL)

```nim
# 目標：使用巨集實作領域特定語言
# 要求：
# 1. 實作簡單的路由 DSL
# 2. 實作資料庫操作 DSL
# 3. 實作配置檔案 DSL

import macros, strutils, tables

# 實作簡單的路由 DSL
macro router(body: untyped): untyped =
  # TODO: 實作一個用於定義 HTTP 路由的 DSL
  expectKind(body, nnkStmtList)
  
  # 建立儲存路由的資料結構
  let setup = quote do:
    var routes = initTable[string, proc(params: string): string]()
  
  result = newStmtList(setup)
  
  # 處理每個路由定義
  for route in body:
    if route.kind == nnkCommand and route.len >= 2:
      let httpMethod = route[0]
      let path = route[1]
      var handler: NimNode
      
      if route.len > 2 and route[2].kind == nnkStmtList:
        handler = route[2]
      else:
        handler = newStmtList()
      
      # 生成路由處理函數
      let key = newLit($httpMethod & " " & $path)
      let routeHandler = quote do:
        routes[`key`] = proc (params: string): string =
          `handler`
      
      result.add(routeHandler)
  
  # 生成路由處理函數
  let handleRequest = quote do:
    proc handleRequest(httpMethod, path, params: string): string =
      let key = httpMethod & " " & path
      if routes.hasKey(key):
        return routes[key](params)
      else:
        return "404 Not Found"
  
  result.add(handleRequest)

# 實作資料庫操作 DSL
macro query(args: varargs[untyped]): untyped =
  # TODO: 實作一個用於資料庫查詢的 DSL
  var sqlParts = newSeq[string]()
  var params = newSeq[NimNode]()
  
  for arg in args:
    case arg.kind
    of nnkStrLit:
      sqlParts.add(arg.strVal)
    else:
      sqlParts.add("?")
      params.add(arg)
  
  let sqlQuery = newLit(sqlParts.join(" "))
  
  result = quote do:
    execQuery(`sqlQuery`, @[`params`])
  
  # 根據需要調整前面的程式碼

# 模擬資料庫執行函數
proc execQuery(query: string, params: seq[string]): seq[string] =
  result = @[]
  echo "執行查詢: ", query
  echo "參數: ", params
  result.add("模擬查詢結果1")
  result.add("模擬查詢結果2")

# 實作配置檔案 DSL
macro config(body: untyped): untyped =
  # TODO: 實作一個用於定義配置檔案的 DSL
  expectKind(body, nnkStmtList)
  
  let setup = quote do:
    var settings = initTable[string, string]()
  
  result = newStmtList(setup)
  
  # 處理每個配置項目
  for item in body:
    if item.kind == nnkAsgn:
      let key = item[0]
      let value = item[1]
      
      let keyStr = newLit($key)
      
      # 特殊處理不同類型的值
      var valueStr: NimNode
      if value.kind == nnkStrLit:
        valueStr = value
      else:
        valueStr = quote do:
          $(`value`)
      
      let setting = quote do:
        settings[`keyStr`] = `valueStr`
      
      result.add(setting)
  
  # 生成獲取配置的函數
  let getConfig = quote do:
    proc getSetting(key: string, default: string = ""): string =
      if settings.hasKey(key):
        return settings[key]
      else:
        return default
  
  result.add(getConfig)

# 測試 DSL 巨集
proc testDslMacros() =
  # 測試路由 DSL
  echo "測試路由 DSL:"
  router:
    GET "/users":
      echo "處理 GET /users 請求"
      "用戶列表"
    
    POST "/users":
      echo "處理 POST /users 請求"
      "創建用戶成功"
  
  echo "  請求 GET /users: ", handleRequest("GET", "/users", "")
  echo "  請求 POST /users: ", handleRequest("POST", "/users", "name=test")
  echo "  請求 DELETE /users: ", handleRequest("DELETE", "/users", "")
  
  # 測試資料庫操作 DSL
  echo "\n測試資料庫操作 DSL:"
  let results = query(
    "SELECT *",
    "FROM users",
    "WHERE id =", "123",
    "AND status =", "active"
  )
  echo "  查詢結果: ", results
  
  # 測試配置檔案 DSL
  echo "\n測試配置檔案 DSL:"
  config:
    host = "localhost"
    port = 8080
    debug = true
    apiKey = "abcd1234"
  
  echo "  host: ", getSetting("host")
  echo "  port: ", getSetting("port")
  echo "  timeout: ", getSetting("timeout", "30")

# 執行測試
testDslMacros()
```
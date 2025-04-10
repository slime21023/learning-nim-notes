# 練習 7: 錯誤處理與泛型程式設計

## Q1: 異常處理技巧
```nim
# 目標：熟練使用 Nim 的異常處理機制
# 要求：
# 1. 實作不同類型的異常處理方式
# 2. 自定義異常類型
# 3. 實作 try-except-finally 結構

import strutils

# 定義自訂異常類型
type
  InputError = object of CatchableError
  ValidationError = object of CatchableError
  
# 基本除法運算，但處理除零異常
proc safeDivide(a, b: float): float =
  # TODO: 實作安全除法，處理除以零的情況
  
# 嘗試將字串轉換為整數，處理格式錯誤
proc parseInteger(s: string): int =
  # TODO: 實作字串轉整數，處理轉換失敗的情況
  
# 檢查年齡輸入是否有效，無效則拋出自訂異常
proc validateAge(ageStr: string): int =
  # TODO: 實作年齡驗證，處理各種無效輸入情況
  
# 讀取檔案內容，處理檔案不存在、讀取錯誤等異常
proc readFileContent(filename: string): string =
  # TODO: 實作檔案讀取，處理各種檔案操作異常
  
# 測試各種異常處理情境
proc testExceptionHandling() =
  echo "測試安全除法:"
  try:
    echo "10 / 2 = ", safeDivide(10, 2)
    echo "10 / 0 = ", safeDivide(10, 0)
  except:
    echo "除法運算發生異常"
  
  echo "\n測試字串轉整數:"
  for s in ["123", "abc", "123abc", "-456"]:
    try:
      let num = parseInteger(s)
      echo "轉換成功: ", s, " -> ", num
    except:
      echo "轉換失敗: ", s
  
  echo "\n測試年齡驗證:"
  for s in ["25", "0", "-5", "150", "abc"]:
    try:
      let age = validateAge(s)
      echo "有效年齡: ", age
    except InputError as e:
      echo "輸入錯誤: ", e.msg
    except ValidationError as e:
      echo "驗證錯誤: ", e.msg
  
  echo "\n測試檔案讀取:"
  for filename in ["existing.txt", "nonexistent.txt"]:
    try:
      let content = readFileContent(filename)
      echo "成功讀取 ", filename, ", 內容長度: ", content.len
    except:
      let e = getCurrentException()
      echo "讀取 ", filename, " 失敗: ", e.name, " - ", e.msg

# 執行測試
testExceptionHandling()
```

Q2: 泛型函數與資料結構
```nim
# 目標：實作泛型函數與泛型資料結構
# 要求：
# 1. 實作泛型函數處理不同型別的資料
# 2. 實作泛型堆疊資料結構
# 3. 實作泛型二叉樹資料結構

# 泛型的最大值函數
proc findMax[T](values: openArray[T]): T =
  # TODO: 實作找出陣列中最大值的泛型函數
  
# 泛型的交換函數
proc swap[T](a, b: var T) =
  # TODO: 實作交換兩個變數值的泛型函數
  
# 泛型堆疊資料結構
type
  Stack[T] = ref object
    items: seq[T]

# 建立新堆疊
proc newStack[T](): Stack[T] =
  # TODO: 實作建立空堆疊的函數
  
# 將元素推入堆疊
proc push[T](stack: Stack[T], item: T) =
  # TODO: 實作推入元素至堆疊頂部的函數
  
# 從堆疊彈出元素
proc pop[T](stack: Stack[T]): T =
  # TODO: 實作從堆疊彈出頂部元素的函數
  
# 查看堆疊頂部元素
proc peek[T](stack: Stack[T]): T =
  # TODO: 實作查看堆疊頂部元素但不移除的函數
  
# 檢查堆疊是否為空
proc isEmpty[T](stack: Stack[T]): bool =
  # TODO: 實作檢查堆疊是否為空的函數
  
# 泛型二叉樹資料結構
type
  BinaryTree[T] = ref object
    value: T
    left, right: BinaryTree[T]

# 建立新二叉樹節點
proc newNode[T](value: T): BinaryTree[T] =
  # TODO: 實作建立新二叉樹節點的函數
  
# 插入值到二叉搜尋樹
proc insert[T](root: var BinaryTree[T], value: T) =
  # TODO: 實作插入值到二叉搜尋樹的函數
  
# 中序遍歷二叉樹
proc inOrderTraversal[T](root: BinaryTree[T]): seq[T] =
  # TODO: 實作中序遍歷二叉樹的函數
  
# 搜尋二叉搜尋樹中的值
proc search[T](root: BinaryTree[T], value: T): bool =
  # TODO: 實作在二叉搜尋樹中搜尋值的函數
  
# 測試泛型函數與資料結構
proc testGenerics() =
  # 測試泛型函數
  let 
    intArray = [5, 2, 9, 1, 7]
    floatArray = [3.14, 2.71, 1.41, 1.73]
    strArray = ["apple", "banana", "cherry", "date"]
  
  echo "整數陣列最大值: ", findMax(intArray)
  echo "浮點數陣列最大值: ", findMax(floatArray)
  echo "字串陣列最大值: ", findMax(strArray)
  
  var x = 10
  var y = 20
  echo "\n交換前: x = ", x, ", y = ", y
  swap(x, y)
  echo "交換後: x = ", x, ", y = ", y
  
  # 測試泛型堆疊
  echo "\n測試整數堆疊:"
  var intStack = newStack[int]()
  intStack.push(1)
  intStack.push(2)
  intStack.push(3)
  
  echo "堆疊頂部元素: ", intStack.peek()
  echo "彈出: ", intStack.pop()
  echo "彈出: ", intStack.pop()
  echo "堆疊是否為空: ", intStack.isEmpty()
  echo "彈出: ", intStack.pop()
  echo "堆疊是否為空: ", intStack.isEmpty()
  
  # 測試泛型二叉搜尋樹
  echo "\n測試整數二叉搜尋樹:"
  var intTree: BinaryTree[int] = nil
  insert(intTree, 5)
  insert(intTree, 3)
  insert(intTree, 7)
  insert(intTree, 2)
  insert(intTree, 4)
  
  echo "中序遍歷: ", inOrderTraversal(intTree)
  echo "搜尋 4: ", search(intTree, 4)
  echo "搜尋 6: ", search(intTree, 6)

# 執行測試
testGenerics()
```

## Q3: 受約束泛型與概念
```nim
# 目標：實作受約束泛型和概念
# 要求：
# 1. 實作使用型別約束的泛型函數
# 2. 定義概念並在泛型中應用
# 3. 實作使用靜態斷言的泛型函數

import typetraits

# 定義數值概念
type
  Numeric = concept x
    x + x is type(x)
    x - x is type(x)
    x * x is type(x)
    x / x is float

# 定義可比較概念
type
  Comparable = concept x, y
    (x < y) is bool
    (x <= y) is bool
    (x > y) is bool
    (x >= y) is bool

# 定義容器概念
type
  Container[T] = concept c
    c.len is int
    for v in c:
      v is T

# 只接受數值類型的求和函數
proc sum[T: Numeric](values: openArray[T]): T =
  # TODO: 實作只接受數值類型的泛型求和函數
  
# 只接受可比較類型的排序函數
proc sort[T: Comparable](arr: var seq[T]) =
  # TODO: 實作只接受可比較類型的泛型排序函數
  
# 擴展既有類型
type
  MyInt = distinct int

proc `+`*(a, b: MyInt): MyInt = MyInt(int(a) + int(b))
proc `-`*(a, b: MyInt): MyInt = MyInt(int(a) - int(b))
proc `*`*(a, b: MyInt): MyInt = MyInt(int(a) * int(b))
proc `/`*(a, b: MyInt): float = float(int(a) / int(b))

proc `<`*(a, b: MyInt): bool = int(a) < int(b)
proc `<=`*(a, b: MyInt): bool = int(a) <= int(b)
proc `>`*(a, b: MyInt): bool = int(a) > int(b)
proc `>=`*(a, b: MyInt): bool = int(a) >= int(b)

# 測試受約束泛型與概念
proc testConstrainedGenerics() =
  # 測試數值概念
  let 
    intArray = [1, 2, 3, 4, 5]
    floatArray = [1.1, 2.2, 3.3, 4.4, 5.5]
    myIntArray = [MyInt(1), MyInt(2), MyInt(3)]
  
  echo "整數總和: ", sum(intArray)
  echo "浮點數總和: ", sum(floatArray)
  echo "MyInt總和: ", sum(myIntArray)
  
  # 測試可比較概念
  var
    intSeq = @[5, 2, 9, 1, 7]
    floatSeq = @[3.14, 2.71, 1.41, 1.73]
    myIntSeq = @[MyInt(5), MyInt(2), MyInt(9)]
  
  sort(intSeq)
  sort(floatSeq)
  sort(myIntSeq)
  
  echo "\n排序後的整數序列: ", intSeq
  echo "排序後的浮點數序列: ", floatSeq
  echo "排序後的MyInt序列: "
  for x in myIntSeq:
    stdout.write(int(x), " ")
  echo ""
  
  # 靜態斷言範例
  when false:
    # 以下程式碼只用於展示，不會實際執行
    proc willNotCompile[T: string](x: T): T =
      # 使用靜態斷言確保類型符合預期
      static: assert(T is string, "T must be a string type")
      return x

# 執行測試
testConstrainedGenerics()
```


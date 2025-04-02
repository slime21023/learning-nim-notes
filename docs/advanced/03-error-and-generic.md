# 例外處理與泛型  

Nim 提供了強大的例外處理機制和靈活的泛型功能，幫助開發者撰寫健壯且可重用的程式碼。本章將詳細介紹如何使用例外處理來管理錯誤，以及如何使用泛型來參數化函數、迭代器和類型。

---

## 例外處理
  
例外（Exception）是 Nim 中用於表示錯誤的物件，通常以 `Error` 結尾。例如，`OSError` 表示操作系統相關的錯誤。Nim 提供了清晰的語法來拋出、捕捉和管理例外。

### 拋出例外  
使用 `raise` 語句拋出例外。可以通過 `newException` 建立新的例外物件。

**範例：拋出例外**
```nim
proc openFile(filename: string) =
  if filename == "":
    raise newException(ValueError, "Filename cannot be empty!")
  echo "Opening file: ", filename

openFile("")  # 將拋出 ValueError: Filename cannot be empty!
```

### 處理例外  
使用 `try`、`except` 和 `finally` 語句來捕捉和處理例外。

**範例：捕捉例外**
```nim
proc divide(a, b: int): int =
  try:
    if b == 0:
      raise newException(ZeroDivisionError, "Cannot divide by zero!")
    result = a div b
  except ZeroDivisionError as e:
    echo "Error: ", e.msg
    result = 0
  finally:
    echo "Division operation completed."

echo divide(10, 0)  # 輸出: Error: Cannot divide by zero!
                    #       Division operation completed.
                    #       0
```

### 標記函數的例外  
使用 `{.raises.}` 標記函數可能拋出的例外。這有助於程式碼分析和除錯。

**範例：標記例外**
```nim
proc readFile(filename: string): string {.raises: [OSError, ValueError].} =
  if filename == "":
    raise newException(ValueError, "Filename cannot be empty!")
  # 假設這裡有檔案讀取邏輯
  raise newException(OSError, "File not found!")

try:
  echo readFile("")
except ValueError as e:
  echo "Caught ValueError: ", e.msg
```

---

## 泛型 
泛型是 Nim 的一項強大功能，用於參數化函數、迭代器或類型。透過泛型，您可以撰寫針對不同型別的通用程式碼。

### 語法  
使用 `[T]` 聲明泛型參數，其中 `T` 是型別占位符。

**範例：泛型函數**
```nim
proc add[T](a, b: T): T =
  result = a + b

echo add(1, 2)         # 輸出: 3
echo add(1.5, 2.5)     # 輸出: 4.0
echo add("Hello, ", "World!")  # 輸出: Hello, World!
```

### 範例：泛型二元樹 `BinaryTree[T]`  
泛型可以用於定義資料結構，例如二元樹。

**範例：**
```nim
type
  BinaryTree[T] = ref object
    value: T
    left, right: BinaryTree[T]

proc newNode[T](value: T): BinaryTree[T] =
  result = BinaryTree[T](value: value, left: nil, right: nil)

proc addLeft[T](node: BinaryTree[T], value: T) =
  node.left = newNode(value)

proc addRight[T](node: BinaryTree[T], value: T) =
  node.right = newNode(value)

# 建立一個整數二元樹
let root = newNode(10)
addLeft(root, 5)
addRight(root, 15)

echo root.value       # 輸出: 10
echo root.left.value  # 輸出: 5
echo root.right.value # 輸出: 15
```

---

## 總結

Nim 的例外處理和泛型功能提供了以下特性：

**例外處理**：

  - 使用 `raise` 拋出例外。
  - 使用 `try`、`except` 和 `finally` 捕捉和管理例外。
  - 使用 `{.raises.}` 標記函數的例外，提高程式碼的可讀性和安全性。

**泛型**：

  - 使用 `[T]` 定義參數化函數、迭代器或類型。
  - 泛型使程式碼更加通用且可重用，適用於多種型別。

透過這些功能，開發者能更有效地管理錯誤並撰寫靈活的程式碼，提升程式的健壯性和可維護性。
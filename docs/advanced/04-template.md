# 模板與進階語法

## 模板
模板是 Nim 中一種強大的語法樹替換機制，用於在編譯時進行代碼生成。它允許我們定義靈活的代碼片段，並在使用時以特定的方式展開。

### 語法

```nim
template 模板名稱(參數列表): 
    模板內容
```

### `!=` 模板與 `log` 模板  
**`!=` 模板**： 定義一個簡單的模板來比較兩個值是否不相等。
```nim
template `!=`(a, b): bool =
    not (a == b)

let x = 5
let y = 10
echo x != y  # 輸出: true
```
`!=` 被定義為檢查兩個值是否不相等，並展開為 `not (a == b)`。

**`log` 模板**：  
定義一個簡單的日誌模板，輸出訊息與執行時間。
```nim
import times

template log(msg: string): void =
    echo "[" & $getTime() & "] " & msg

log("程序開始")  # 輸出: [時間戳] 程序開始
log("程序結束")  # 輸出: [時間戳] 程序結束
```
`log` 模板在編譯時展開為包含時間戳的訊息。

---

### 提升函數
提升函數是一種方便的工具，用於將原本只能處理標量的函數擴展為可以處理陣列的函數。這對於需要對陣列元素進行逐一操作的情境非常有用。

**用途**：使用 `liftScalarProc` 將標量函數提升為處理陣列的函數，避免手動撰寫迴圈。

**範例**：`liftScalarProc(sqrt)`  
假設我們需要對一個陣列的所有元素計算平方根：
```nim
  import math, sequtils

  # 提升 sqrt 函數
  let sqrtArray = liftScalarProc(sqrt)

  # 使用提升後的函數處理陣列
  let numbers = @[1.0, 4.0, 9.0, 16.0]
  let results = sqrtArray(numbers)

  echo results  # 輸出: @[1.0, 2.0, 3.0, 4.0]
```
在此範例中，`liftScalarProc(sqrt)` 將 `sqrt` 函數提升為可以直接處理陣列的函數，簡化了代碼。

提升函數的使用使得標量函數可以自動映射到陣列的每個元素，從而提高代碼的可讀性與效率。
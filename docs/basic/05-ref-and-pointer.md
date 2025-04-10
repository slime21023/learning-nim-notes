proc example() =
  var stackVar = 10  # 在堆疊上分配
  var heapVar = new int  # 在堆積上分配
  heapVar[] = 20

## 參考型別 (ref)

Nim 的 `ref` 型別提供了安全的動態記憶體分配方式，自動由垃圾回收機制管理釋放記憶體。

### 參考型別定義與使用

使用 `ref` 關鍵字可以創建參考型別：

```nim
# 定義參考型別
type Person = ref object
  name: string
  age: int

# 創建參考型別實例
var p1 = Person(name: "Alice", age: 30)

# 也可以使用 new 函式分配記憶體
var p2 = new Person
p2.name = "Bob"
p2.age = 25

# nil 檢查
if p1 != nil:
  echo p1.name  # 輸出: Alice
```

參考型別的物件在堆積上分配記憶體，透過垃圾回收機制自動管理，開發者無需手動釋放。

### 參考型別的特性

1. **自動垃圾回收**：當沒有任何變數引用參考型別的物件時，垃圾回收器會自動回收其記憶體

   ```nim
   proc scope() =
     var temp = Person(name: "Temp", age: 20)
     # temp 在函式結束後，沒有任何變數引用它，會被自動回收
   
   scope()  # temp 的記憶體會被垃圾回收
   ```

2. **共享引用**：多個變數可以引用同一物件，修改其中一個會影響所有引用

   ```nim
   var p3 = p1  # p3 和 p1 引用同一物件
   p3.age = 31  # 修改 p3
   echo p1.age  # 輸出: 31，p1 也被修改
   ```

3. **nil 值**：參考型別可以為 `nil`，表示不引用任何物件

   ```nim
   var p4: Person = nil
   # 使用前需要檢查
   if p4 != nil:
     echo p4.name
   else:
     echo "p4 is nil"  # 輸出此行
   ```

### 參考型別的使用時機

參考型別特別適合以下情況：

1. **需要共享資料**：當多個部分的程式需要訪問和修改同一資料時
   ```nim
   type
     SharedData = ref object
       counter: int
   
   var data = SharedData(counter: 0)
   
   proc increment(d: SharedData) =
     d.counter += 1
   
   increment(data)
   echo data.counter  # 輸出: 1
   ```

2. **建立複雜資料結構**：樹、圖、鏈結串列等需要動態變化的結構

3. **動態生命週期**：當物件的生命週期不僅限於創建它的作用域時
   ```nim
   proc createAndReturn(): Person =
     result = Person(name: "David", age: 35)
     # result 離開函式後仍然有效
   
   var person = createAndReturn()
   echo person.name  # 輸出: David
   ```

### 參考型別與值型別比較

理解參考型別和值型別的區別對於選擇正確的資料模型至關重要：

```nim
# 值型別
type
  Point = object
    x, y: int

var p1 = Point(x: 10, y: 20)
var p2 = p1       # 複製整個物件
p2.x = 30
echo p1.x         # 輸出: 10 (p1未受影響)

# 參考型別
type
  RefPoint = ref object
    x, y: int

var rp1 = RefPoint(x: 10, y: 20)
var rp2 = rp1     # 複製參考，共享同一物件
rp2.x = 30
echo rp1.x        # 輸出: 30 (rp1被修改)
```

### 參考型別與性能考量

雖然參考型別提供便利，但也有性能影響需要考慮：

1. **記憶體分配開銷**：每次創建參考型別物件都需要動態分配記憶體，這比堆疊分配慢
2. **垃圾回收開銷**：垃圾回收器需要額外的時間和資源來追蹤和釋放未使用的物件
3. **記憶體碎片化**：頻繁分配和釋放可能導致堆積碎片化，降低性能

### 參考型別應用：鏈結串列

參考型別特別適合實現動態資料結構，如鏈結串列：

```nim
type
  Node[T] = ref object
    data: T
    next: Node[T]

proc newNode[T](data: T): Node[T] =
  Node[T](data: data, next: nil)

proc append[T](head: var Node[T], data: T) =
  var newN = newNode(data)
  if head == nil:
    head = newN
    return
  
  var current = head
  while current.next != nil:
    current = current.next
  current.next = newN

proc printList[T](head: Node[T]) =
  var current = head
  while current != nil:
    echo current.data
    current = current.next

# 使用鏈結串列
var myList: Node[int] = nil
myList.append(10)
myList.append(20)
myList.append(30)
printList(myList)  # 輸出: 10, 20, 30
```

### 避免循環參考

當兩個或多個參考型別物件互相引用時，可能導致循環引用，這在某些垃圾回收機制下會造成記憶體洩漏：

```nim
type
  Parent = ref object
    child: Child
  
  Child = ref object
    parent: Parent

var p = Parent()
var c = Child()
p.child = c    # p 引用 c
c.parent = p   # c 引用 p

# 即使 p 和 c 離開作用域，因為它們互相引用，
# 可能無法被某些垃圾回收機制回收，造成記憶體洩漏
```

解決方案是使用弱引用或手動打破循環：

```nim
# 在不再需要時解除引用
c.parent = nil
p.child = nil
```

## 指標型別 (ptr)

Nim 的 `ptr` 型別提供了類似 C 語言指標的功能，允許直接操作記憶體地址，但需要手動管理記憶體。

### 指標型別定義與使用

```nim
# 定義指標型別
type IntPtr = ptr int

# 創建一個整數變數
var x = 42

# 獲取該變數的記憶體地址
var p: IntPtr = addr(x)

# 使用指標
echo p[]  # 解引用，輸出: 42

# 修改指標指向的值
p[] = 100
echo x  # 輸出: 100，x 的值被修改
```

### 動態記憶體分配與釋放

對於指標型別，需要手動分配和釋放記憶體：

```nim
import std/memory

# 分配記憶體
var p = cast[ptr int](alloc(sizeof(int)))

# 初始化記憶體
p[] = 42
echo p[]  # 輸出: 42

# 使用完畢後釋放記憶體
dealloc(p)
# p 現在是野指標，不應再使用
```

### 指標型別的安全性問題

1. **野指標**：使用已釋放的記憶體可能導致未定義行為

   ```nim
   var p = cast[ptr int](alloc(sizeof(int)))
   p[] = 10
   dealloc(p)
   # p[] = 20  # 危險操作！p 指向已釋放的記憶體
   ```

2. **空指標解引用**：解引用 `nil` 指標會導致程式崩潰

   ```nim
   var p: ptr int = nil
   # echo p[]  # 危險操作！會導致程式崩潰
   
   # 安全使用方式
   if p != nil:
     echo p[]
   ```

3. **記憶體越界**：存取不屬於指標所指區域的記憶體可能導致嚴重問題

   ```nim
   var arr = cast[ptr array[5, int]](alloc(sizeof(int) * 5))
   arr[0] = 10
   # arr[10] = 20  # 危險操作！越界存取
   
   dealloc(arr)
   ```

4. **記憶體洩漏**：忘記釋放分配的記憶體導致資源耗盡

   ```nim
   proc badFunction() =
     var p = cast[ptr int](alloc(sizeof(int)))
     p[] = 42
     # 未呼叫 dealloc(p)，導致記憶體洩漏
   
   proc goodFunction() =
     var p = cast[ptr int](alloc(sizeof(int)))
     p[] = 42
     # 函式結束前記得釋放
     dealloc(p)
   ```

### 指標型別的使用時機

指標型別主要在以下場景中使用：

1. **效能關鍵應用**：當需要最大限度地減少記憶體管理開銷時
   
   ```nim
   # 高效能計算範例
   import std/memory
   
   proc calculateSum(data: ptr UncheckedArray[int], length: int): int =
     for i in 0 ..< length:
       result += data[i]
   
   # 分配連續記憶體
   var data = cast[ptr UncheckedArray[int]](alloc(10 * sizeof(int)))
   
   # 初始化資料
   for i in 0 ..< 10:
     data[i] = i * 10
   
   echo calculateSum(data, 10)  # 高效能計算
   
   # 釋放記憶體
   dealloc(data)
   ```

2. **底層系統程式設計**：當需要直接操作記憶體位址時
   
   ```nim
   # 記憶體映射範例
   type
     SystemRegisters = object
       status: uint8
       control: uint8
       data: uint16
   
   # 假設這是特定硬體位址
   let registerBase = cast[ptr SystemRegisters](0x20000000)
   
   # 直接操作硬體寄存器
   registerBase.control = 0x01  # 設置控制寄存器
   let status = registerBase.status  # 讀取狀態寄存器
   ```

3. **與 C 語言程式庫整合**：當需要與不使用垃圾回收的外部程式庫交互時
   
   ```nim
   # 使用 C 函式庫
   {.passL: "-lsqlite3".}
   
   type
     SQLiteDB = distinct pointer
     SQLiteStmt = distinct pointer
   
   proc sqlite3_open(filename: cstring, ppDb: ptr SQLiteDB): int32 
     {.importc, cdecl.}
   proc sqlite3_close(db: SQLiteDB): int32 
     {.importc, cdecl.}
   
   var db: SQLiteDB
   let rc = sqlite3_open("test.db", addr db)
   if rc == 0:
     echo "資料庫開啟成功"
     discard sqlite3_close(db)
   ```

### 指標型別的安全性問題

1. **野指標**：使用已釋放的記憶體可能導致未定義行為

   ```nim
   var p = cast[ptr int](alloc(sizeof(int)))
   p[] = 10
   dealloc(p)
   # p[] = 20  # 危險操作！p 指向已釋放的記憶體
   ```

2. **空指標解引用**：解引用 `nil` 指標會導致程式崩潰

   ```nim
   var p: ptr int = nil
   # echo p[]  # 危險操作！會導致程式崩潰
   
   # 安全使用方式
   if p != nil:
     echo p[]
   ```

3. **記憶體越界**：存取不屬於指標所指區域的記憶體可能導致嚴重問題

   ```nim
   var arr = cast[ptr array[5, int]](alloc(sizeof(int) * 5))
   arr[0] = 10
   # arr[10] = 20  # 危險操作！越界存取
   
   dealloc(arr)
   ```

4. **記憶體洩漏**：忘記釋放分配的記憶體導致資源耗盡

   ```nim
   proc badFunction() =
     var p = cast[ptr int](alloc(sizeof(int)))
     p[] = 42
     # 未呼叫 dealloc(p)，導致記憶體洩漏
   
   proc goodFunction() =
     var p = cast[ptr int](alloc(sizeof(int)))
     p[] = 42
     # 函式結束前記得釋放
     dealloc(p)
   ```

### 安全管理指標記憶體的技巧

1. **使用 `defer` 確保記憶體釋放**
   
   ```nim
   proc process() =
     var p = cast[ptr int](alloc(sizeof(int)))
     defer: dealloc(p)  # 無論如何都會執行，即使發生異常
     
     p[] = 42
     # 即使這裡發生異常，defer 也確保記憶體被釋放
     echo p[]
   ```

2. **自訂智能指標**

   ```nim
   type
     SmartPtr[T] = object
       p: ptr T
   
   proc newSmartPtr[T](val: T): SmartPtr[T] =
     var p = cast[ptr T](alloc(sizeof(T)))
     p[] = val
     result.p = p
   
   proc `=destroy`[T](x: var SmartPtr[T]) =
     if x.p != nil:
       dealloc(x.p)
       x.p = nil
   
   proc `[]`[T](x: SmartPtr[T]): T = x.p[]
   proc `[]=`[T](x: SmartPtr[T], val: T) = x.p[] = val
   
   # 使用智能指標
   block:
     var sp = newSmartPtr(42)
     echo sp[]  # 輸出: 42
     sp[] = 100
     echo sp[]  # 輸出: 100
     # 離開區塊時自動釋放記憶體
   ```

3. **封裝不安全操作**
   
   ```nim
   # 安全的緩衝區模組
   type
     SafeBuffer* = object
       private:
         data: ptr UncheckedArray[byte]  # 不檢查邊界的數組指標
         len: int
   
   proc newBuffer*(size: Natural): SafeBuffer =
     result.data = cast[ptr UncheckedArray[byte]](alloc(size))
     result.len = size
   
   proc `=destroy`*(buf: var SafeBuffer) =
     if buf.data != nil:
       dealloc(buf.data)
       buf.data = nil
   
   proc `[]`*(buf: SafeBuffer, index: int): byte =
     if index >= buf.len:
       raise newException(IndexDefect, "Buffer index out of bounds")
     buf.data[index]
   
   proc `[]=`*(buf: var SafeBuffer, index: int, value: byte) =
     if index >= buf.len:
       raise newException(IndexDefect, "Buffer index out of bounds")
     buf.data[index] = value
   
   # 使用安全緩衝區
   var buf = newBuffer(10)
   buf[0] = 65  # 'A'
   buf[1] = 66  # 'B'
   buf[2] = 67  # 'C'

   echo "Buffer 內容:"
   for i in 0..<3:
     echo char(buf[i])  # 輸出: A, B, C

   # 釋放記憶體
   # buf 自動釋放記憶體
   ```

### 指標的記憶體布局和對齊

在低階程式設計中，了解記憶體布局和對齊是關鍵：

```nim
type
  AlignedStruct = object
    a: int8       # 1 byte
    # 3 bytes padding
    b: int32      # 4 bytes
    c: int8       # 1 byte
    # 7 bytes padding (to maintain 8-byte alignment)
    d: int64      # 8 bytes

echo sizeof(AlignedStruct)  # 可能輸出: 24 而非 14，因為有填充

# 使用 packed pragma 可減少對齊填充
type
  PackedStruct {.packed.} = object
    a: int8
    b: int32
    c: int8
    d: int64

echo sizeof(PackedStruct)  # 輸出: 14，無填充
# 但存取效率可能降低，某些平台上可能導致問題
```

### 指標算術

Nim 中的指標算術必須小心使用：

```nim
import std/memory

# 分配 10 個整數的陣列
var arr = cast[ptr UncheckedArray[int]](alloc(10 * sizeof(int)))

# 初始化陣列
for i in 0 ..< 10:
  arr[i] = i * 10

# 獲取指向第 5 個元素的指標
var p5 = cast[ptr int](cast[int](arr) + 5 * sizeof(int))
echo p5[]  # 輸出: 50 (第 5 個元素，索引從 0 開始)

# 指標加法
var p7 = cast[ptr int](cast[int](p5) + 2 * sizeof(int))
echo p7[]  # 輸出: 70 (第 7 個元素)

# 釋放記憶體
dealloc(arr)
```

### 指標型別應用：FFI (外部函式介面)

指標型別在與 C 語言庫交互時特別有用：

```nim
# 聲明 C 函式
proc c_strlen(str: cstring): cint {.importc: "strlen", header: "<string.h>".}

# 使用 C 函式
var s = "Hello, Nim!"
echo c_strlen(s)  # 輸出字串長度
```

## 比較：ref 與 ptr

| 特性 | ref | ptr |
|------|-----|-----|
| 記憶體管理 | 自動 (垃圾回收) | 手動 |
| 安全性 | 高 | 低 |
| 效能 | 較低 (有垃圾回收開銷) | 較高 |
| 適用場景 | 大多數高級應用 | 系統程式設計，效能關鍵應用 |
| 初始值 | nil | 未定義 |
| 解引用 | 直接使用 `.` | 使用 `[]` |

### 選擇指南

- 使用 `ref` 當：
  - 需要動態分配的物件有較長的生命週期
  - 數據結構複雜或需要共享
  - 優先考慮安全性和易用性

- 使用 `ptr` 當：
  - 效能是關鍵考量
  - 需要與 C 語言庫交互
  - 必須精確控制記憶體分配和釋放
  - 明確知道如何避免記憶體洩漏和野指標

### 深入比較

讓我們通過一些實例來更深入地比較 `ref` 和 `ptr` 在不同場景中的表現：

#### 生命週期管理

```nim
# 使用 ref 的安全生命週期
proc refExample() =
  var data: ref seq[int]
  block:
    data = new seq[int]
    data[] = @[1, 2, 3, 4, 5]
  # 離開 block 後，data 仍然有效
  echo data[][0]  # 輸出: 1

# 使用 ptr 的危險生命週期
proc ptrExample() =
  var data: ptr seq[int]
  block:
    var localSeq = @[1, 2, 3, 4, 5]
    data = addr localSeq
  # 危險! data 現在指向已經不存在的堆疊變數
  # echo data[][0]  # 可能導致崩潰或不可預測的行為
```

#### 函式傳遞行為

```nim
# ref 型別參數傳遞
proc updateRef(r: ref int) =
  r[] = 100  # 修改引用的值

var refVal = new int
refVal[] = 42
updateRef(refVal)
echo refVal[]  # 輸出: 100

# ptr 型別參數傳遞
proc updatePtr(p: ptr int) =
  p[] = 200  # 修改指向的值

var val = 42
var ptrVal: ptr int = addr val
updatePtr(ptrVal)
echo val  # 輸出: 200
```

#### 性能比較

在高頻記憶體操作場景下，ptr 可以帶來顯著的性能優勢：

```nim
import std/monotimes
import std/times
import std/memory

proc benchmarkRef() =
  let start = getMonoTime()
  
  for i in 1..1_000_000:
    var r = new int
    r[] = i
    discard r[]
  
  let finish = getMonoTime()
  echo "Ref time: ", inMilliseconds(finish - start), "ms"

proc benchmarkPtr() =
  let start = getMonoTime()
  
  for i in 1..1_000_000:
    var p = cast[ptr int](alloc(sizeof(int)))
    p[] = i
    discard p[]
    dealloc(p)
  
  let finish = getMonoTime()
  echo "Ptr time: ", inMilliseconds(finish - start), "ms"

# 運行性能比較
# benchmarkRef()  # 通常較慢，因為有垃圾回收開銷
# benchmarkPtr()  # 通常較快，但需要手動管理記憶體
```

#### 組合使用

在某些情況下，混合使用 `ref` 和 `ptr` 可以取得安全性和效能的平衡：

```nim
type
  DataManager = ref object
    buffer: ptr UncheckedArray[byte]
    size: int

proc newDataManager(size: int): DataManager =
  new result
  result.buffer = cast[ptr UncheckedArray[byte]](alloc(size))
  result.size = size

proc `=destroy`(x: var DataManager) =
  if x.buffer != nil:
    dealloc(x.buffer)
    x.buffer = nil

# 使用封裝類型安全管理資源
block:
  var resource = newDataManager(1024)
  # 自動處理釋放
```

## 最佳實踐

### 使用 ref 的最佳實踐

1. **明確初始化**：
   - 避免使用未初始化的 `ref` 變數
   - 使用 `new` 或直接賦值初始化參考型別

   ```nim
   # 不好的做法
   var p: ref Person
   # 可能在未初始化時嘗試使用
   
   # 好的做法
   var p = new Person
   p.name = "Alice"
   
   # 或更好的做法
   var p = Person(name: "Alice", age: 30)
   ```

2. **避免循環參考**：
   - 可能的話，設計資料結構避免循環引用
   - 必要時使用弱引用，或手動解除部分引用

   ```nim
   # 防止循環引用的設計
   type
     Parent = ref object
       children: seq[Child]
     
     Child = ref object
       # 不使用 parent: Parent，而是：
       parentId: string  # 使用 ID 而非直接引用
   
   # 手動解除循環引用
   proc cleanupReferences(parent: Parent, child: Child) =
     parent.children = parent.children.filterIt(it != child)
   ```

3. **使用 Option 型別**：
   - 用 `Option[T]` 代替可能為 `nil` 的參考，提高類型安全性
   - 強制處理空值情況，避免空引用異常

   ```nim
   import options
   
   proc findPerson(id: int): Option[Person] =
     # 若找到則返回 some(person)，否則返回 none(Person)
     if id > 0:
       return some(Person(name: "Person " & $id))
     else:
       return none(Person)
   
   let result = findPerson(42)
   if result.isSome:
     echo "找到: ", result.get().name
   else:
     echo "未找到此人"
   ```

4. **參考不可變性**：
   - 當物件不需修改時，使用 `let` 聲明參考
   - 考慮使用 `{.readonly.}` 標記防止修改

   ```nim
   # 防止修改
   let immutablePerson = Person(name: "Alex")
   # immutablePerson.name = "Bob"  # 編譯錯誤
   
   # 使用 readonly pragma
   proc viewPerson(p: Person {.readonly.}) =
     echo p.name
     # p.name = "Changed"  # 編譯錯誤
   ```

5. **清晰的所有權模型**：
   - 明確定義誰擁有和管理參考型別物件
   - 避免全局可變參考

   ```nim
   # 明確所有權的設計
   type
     DataOwner = ref object
       data: seq[int]
   
   proc modifyData(owner: DataOwner, index: int, value: int) =
     if index < owner.data.len:
       owner.data[index] = value
   
   # 由 owner 控制 data 的所有權，其他代碼通過特定接口操作
   ```

### 使用 ptr 的最佳實踐

1. **限制使用範圍**：
   - 將 `ptr` 的使用限制在性能關鍵部分
   - 封裝指標操作在特定模組中，提供安全接口

   ```nim
   # 安全封裝低層級操作
   type
     ImageProcessor = object
       private:
         data: ptr UncheckedArray[byte]  # 不檢查邊界的數組指標
         width, height: int
   
   # 僅公開安全的高層級操作
   proc processImage*(img: var ImageProcessor) =
     # 內部處理使用低層級指標操作以獲得高性能
     # ...
   ```

2. **成對分配和釋放**：
   - 每次 `alloc` 都必須有對應的 `dealloc`
   - 使用 `defer` 語句確保即使發生異常也能釋放資源

   ```nim
   proc processData() =
     var data = cast[ptr UncheckedArray[byte]](alloc(1024))
     defer: dealloc(data)
     
     # 處理過程中可能發生異常
     # ...
     
     # 不用擔心記憶體洩漏，defer 會確保釋放
   ```

3. **避免指標算術**：
   - 除非絕對必要，否則避免直接的指標算術操作
   - 使用陣列索引等更安全的替代方案

   ```nim
   # 不好的做法
   var p = cast[ptr int](cast[int](basePtr) + offset)
   
   # 更好的做法
   var arr = cast[ptr UncheckedArray[int]](basePtr)
   var value = arr[offset div sizeof(int)]
   ```

4. **安全檢查**：
   - 在解引用前總是檢查 `nil`
   - 使用邊界檢查避免緩沖區溢出

   ```nim
   # 安全的指標使用
   if p != nil:
     echo p[]
   else:
     echo "Pointer is nil"
   
   # 安全的邊界檢查
   if index >= 0 and index < bufferSize:
     buffer[index] = value
   else:
     raise newException(IndexDefect, "Buffer index out of bounds")
   ```

5. **資源管理模式**：
   - 使用 RAII (Resource Acquisition Is Initialization) 模式
   - 建立封裝類型處理資源生命週期

   ```nim
   type
     ManagedResource = object
       data: ptr UncheckedArray[byte]
       size: int

proc newManagedResource(size: int): ManagedResource =
  result.data = cast[ptr UncheckedArray[byte]](alloc(size))
  result.size = size

proc `=destroy`(x: var ManagedResource) =
  if x.data != nil:
    dealloc(x.data)
    x.data = nil

# 使用封裝類型安全管理資源
block:
  var resource = newManagedResource(1024)
  # 自動處理釋放
```

### 通用最佳實踐

1. **選擇適當的記憶體管理模式**：
   - 基於程式需求選擇 GC, ARC, ORC 或手動模式
   - 針對效能關鍵部分考慮手動管理

2. **避免混合記憶體管理策略**：
   - 在同一部分代碼中混合使用不同策略可能導致難以追蹤的問題
   - 保持一致性，必要時使用明確的邊界分隔不同策略

3. **測試記憶體洩漏**：
   - 使用工具如 Valgrind 檢測記憶體洩漏
   - 開發階段啟用記憶體追蹤

4. **正確理解所有權**：
   - 明確每個物件的所有者
   - 使用清晰的生命週期管理策略

5. **文檔化記憶體管理決策**：
   - 記錄為什麼選擇 `ref` 或 `ptr`
   - 記錄關鍵記憶體管理模式以便維護

## 記憶體管理模式

Nim 提供多種記憶體管理模式：

1. **垃圾回收 (GC)**：默認模式，自動管理 `ref` 型別記憶體
2. **自動參考計數 (ARC)**：更高效的記憶體管理，適用於軟即時系統
3. **擁有者參考計數 (ORC)**：處理循環引用的 ARC 變體
4. **手動記憶體管理**：適用於特定場景，需自行處理分配和釋放

可以在編譯時設置記憶體管理模式：
```bash
nim c --gc:arc myprog.nim  # 使用 ARC 模式
```

## 實戰示例

### 示例 1：使用 ref 實現二叉樹

```nim
type
  BinaryTree[T] = ref object
    value: T
    left, right: BinaryTree[T]

proc newTree[T](value: T): BinaryTree[T] =
  BinaryTree[T](value: value)

proc insert[T](tree: var BinaryTree[T], value: T) =
  if tree == nil:
    tree = newTree(value)
    return
  
  if value < tree.value:
    if tree.left == nil:
      tree.left = newTree(value)
    else:
      insert(tree.left, value)
  else:
    if tree.right == nil:
      tree.right = newTree(value)
    else:
      insert(tree.right, value)

proc inOrderTraversal[T](tree: BinaryTree[T]) =
  if tree == nil:
    return
  
  inOrderTraversal(tree.left)
  echo tree.value
  inOrderTraversal(tree.right)

# 使用二叉樹
var root: BinaryTree[int] = nil
root.insert(50)
root.insert(30)
root.insert(70)
root.insert(20)
root.insert(40)

echo "中序遍歷結果:"
inOrderTraversal(root)  # 輸出: 20, 30, 40, 50, 70
```

### 示例 2：使用 ptr 實現高效記憶體緩沖區

```nim
import std/memory

# 實現簡單的記憶體緩沖區
type
  Buffer = object
    data: ptr UncheckedArray[byte]  # 不檢查邊界的數組指標
    size: int

proc newBuffer(size: int): Buffer =
  result.data = cast[ptr UncheckedArray[byte]](alloc(size))
  result.size = size
  
  # 初始化為零
  for i in 0..<size:
    result.data[i] = 0

proc `[]`(buffer: Buffer, index: int): byte =
  if index < 0 or index >= buffer.size:
    raise newException(IndexDefect, "Buffer index out of bounds")
  buffer.data[index]

proc `[]=`(buffer: var Buffer, index: int, value: byte) =
  if index < 0 or index >= buffer.size:
    raise newException(IndexDefect, "Buffer index out of bounds")
  buffer.data[index] = value

proc freeBuffer(buffer: var Buffer) =
  if buffer.data != nil:
    dealloc(buffer.data)
    buffer.data = nil
    buffer.size = 0

# 使用緩沖區
var buffer = newBuffer(10)
buffer[0] = 65  # 'A'
buffer[1] = 66  # 'B'
buffer[2] = 67  # 'C'

echo "Buffer 內容:"
for i in 0..<3:
  echo char(buffer[i])  # 輸出: A, B, C

# 釋放記憶體
freeBuffer(buffer)
```

### 示例 3：Option 型別處理可能為 nil 的參考

```nim
import options

type
  User = ref object
    name: string
    email: Option[string]  # 電子郵件可能不存在

proc newUser(name: string, email: string = ""): User =
  result = User(name: name)
  if email.len > 0:
    result.email = some(email)
  else:
    result.email = none(string)

proc displayUserInfo(user: User) =
  echo "Name: ", user.name
  
  if user.email.isSome:
    echo "Email: ", user.email.get()
  else:
    echo "Email: Not provided"

# 使用 Option
var user1 = newUser("Alice", "alice@example.com")
var user2 = newUser("Bob")

displayUserInfo(user1)  # 輸出包含電子郵件
displayUserInfo(user2)  # 輸出無電子郵件
```

## 小測驗

1. 下面的程式碼中，p 和 q 是否指向同一物件？
   ```nim
   var p = Person(name: "Alice")
   var q = p
   ```

2. 當使用 `ptr` 型別時，以下哪個操作是必須的？
   a) 使用 `new` 分配記憶體
   b) 手動釋放記憶體
   c) 在物件定義中使用 `ref object`
   d) 在解引用前設置默認值

3. 哪種情況下應該考慮使用 `ptr` 而非 `ref`？
   a) 建立一個動態資料結構如鏈結串列
   b) 需要與 C 語言庫交互
   c) 需要垃圾回收功能
   d) 為了提高程式碼可讀性

4. 以下代碼可能會導致什麼問題？
   ```nim
   type
     Node = ref object
       next: Node
   
   var n1 = Node()
   var n2 = Node()
   n1.next = n2
   n2.next = n1
   
```

5. 在 Nim 中，堆積和堆疊的主要區別是什麼？

## 進一步閱讀
- [Nim 官方文檔：記憶體管理](https://nim-lang.org/docs/gc.html)
- [Nim 官方文檔：指標](https://nim-lang.org/docs/manual.html#types-pointer-type)
- [Nim 記憶體管理深入介紹](https://nim-lang.org/blog/2020/10/15/introduction-to-arc-orc-in-nim.html)
- [Nim 記憶體安全最佳實踐](https://nim-lang.org/docs/manual.html#memory-management)

## 本章小結

在本章中，我們深入了解了 Nim 的記憶體管理機制，特別是參考型別和指標型別的使用。參考型別提供了自動記憶體管理，適合大多數高階應用，而指標型別則提供了更底層的控制，適合性能關鍵應用和系統編程。

我們學習了如何：
- 使用 `ref` 型別創建動態資料結構
- 安全地使用 `ptr` 型別進行高效操作
- 避免常見的記憶體管理陷阱，如循環參考和野指標
- 選擇合適的記憶體管理策略

掌握這些概念能讓你編寫更高效、更健壯的 Nim 程式，在保持語言安全性的同時，充分利用 Nim 提供的底層能力。

---

下一章將介紹 Nim 中的進階控制流結構，包括異常處理和延遲執行。
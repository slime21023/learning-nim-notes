# 參考與指標

## 學習目標
- 理解參考型別和指標型別的區別
- 掌握記憶體管理的基本概念
- 學會使用 ref 建立動態資料結構
- 了解指標的安全使用方式

## 先決條件
- 已完成基本型別學習
- 理解物件和型別系統
- 熟悉基本的記憶體概念

## 關鍵概念
1. 參考型別 (ref)
   - 垃圾回收機制
   - 動態記憶體分配
   - nil 安全性
   
2. 指標型別 (ptr)
   - 手動記憶體管理
   - 指標算術
   - 不安全操作
   
3. 記憶體管理
   - 堆積與堆疊
   - 記憶體洩漏
   - 記憶體安全

## 參考型別

### 使用 `ref` 定義參考

`ref` 是 Nim 中的參考型別，用於在記憶體中動態分配物件。所有 `ref` 型別的物件都由垃圾回收機制 (Garbage Collection, GC) 自動管理，開發者無需手動釋放記憶體。

範例：

```nim
type Person = ref object
  name: string
  age: int

var p: Person = Person(name: "Alice", age: 30)
echo p.name  # 輸出：Alice
p.age = 31
echo p.age   # 輸出：31
```

`p` 是一個參考型別的變數，指向一個動態分配的 `Person` 物件。

### 參考的應用場景

#### 動態資料結構  
使用 `ref` 型別可以建構如鏈結串列、樹或圖等動態資料結構。

```nim
type Node = ref object
  value: int
  next: Node

var head: Node = Node(value: 1, next: nil)
head.next = Node(value: 2, next: nil)
echo head.next.value  # 輸出：2
```

#### 共享資料  
多個變數可以共享同一個參考型別的物件，進行資料共享或同步操作。

### 參考的限制
- **不可直接比較**： 參考變數只能比較是否為 `nil`，但無法直接比較兩個參考是否指向相同的物件。
- **需要垃圾回收**： 由於 `ref` 型別依賴垃圾回收機制，因此在即時性要求高的場景（如嵌入式系統）中可能不適用。

## 指標型別

### 使用 `ptr` 定義指標

`ptr` 是 Nim 用於低階記憶體操作的型別，類似於 C 語言中的指標。指標允許直接操作記憶體地址，適合用於高效能或系統層級的程式設計。

範例：

```nim
type Person = object
  name: string
  age: int

var p: ptr Person = cast[ptr Person](alloc0(sizeof(Person)))
p[].name = "Bob"
p[].age = 25
echo p[].name  # 輸出：Bob
```

使用 `alloc0` 函式分配記憶體，並透過 `p[]` 存取指標所指向的物件。

### 指標的安全性與注意事項

1. **手動記憶體管理**： 使用指標時，開發者需手動分配與釋放記憶體，否則可能導致記憶體洩漏。

```nim
dealloc(p)  # 手動釋放記憶體
```

2. **避免空指標存取**： 在操作指標時，需檢查是否為 `nil`，以避免存取空指標導致程式崩潰。

```nim
if p != nil:
  echo p[].age
```

3. **避免野指標 (Dangling Pointer)**： 若指標指向的記憶體已被釋放，則該指標成為野指標，操作它會導致未定義行為。

4. **低階操作風險**： 使用 `ptr` 型別進行低階記憶體操作時，需特別注意避免覆蓋或破壞其他記憶體區域。

### 指標的應用場景

1. **高效能運算**： 在需要直接操作記憶體的場景（如影像處理或資料流處理），指標可以提供極高的效能。
2. **與 C 語言互操作**： Nim 的指標型別與 C 語言兼容，適合用於與 C 函式庫進行整合。

範例：與 C 函式庫互動

```nim
type CInt = ptr int
var x: int = 10
var px: CInt = cast[CInt](addr x)
echo px[]  # 輸出：10
```

### **比較：參考與指標**

| 特性                 | `ref` 型別                     | `ptr` 型別                     |
|----------------------|--------------------------------|--------------------------------|
| 記憶體管理           | 垃圾回收自動管理               | 手動管理                      |
| 使用場景             | 高階資料結構、共享資料         | 低階操作、高效能需求          |
| 安全性               | 由垃圾回收保證                 | 需開發者自行保證              |
| 與 C 語言互操作       | 不適用                        | 適用                          |
| 操作靈活性           | 較低                          | 較高                          |

---

透過 `ref` 和 `ptr` 型別，Nim 提供了靈活的記憶體操作能力，滿足從高階應用到低階操作的多樣需求。開發者可根據應用場景選擇合適的型別，並注意各自的限制與風險。

## 實戰示例

### 1. 鏈結串列實現
```nim
type
  Node[T] = ref object
    data: T
    next: Node[T]

proc newNode[T](data: T): Node[T] =
  Node[T](data: data, next: nil)

proc append[T](head: var Node[T], data: T) =
  if head == nil:
    head = newNode(data)
    return
  
  var current = head
  while current.next != nil:
    current = current.next
  current.next = newNode(data)
```

### 2. 二叉樹實現
```nim
type
  Tree[T] = ref object
    value: T
    left, right: Tree[T]

proc newTree[T](value: T): Tree[T] =
  Tree[T](value: value)

proc insert[T](root: var Tree[T], value: T) =
  if root == nil:
    root = newTree(value)
    return
  
  if value < root.value:
    if root.left == nil:
      root.left = newTree(value)
    else:
      insert(root.left, value)
  else:
    if root.right == nil:
      root.right = newTree(value)
    else:
      insert(root.right, value)
```

## 安全性注意事項

1. ref 型別的最佳實踐：
   - 初始化時賦值
   - 使用 Option[T] 處理可能為 nil 的情況
   - 避免循環參考

2. ptr 型別的安全使用：
   - 限制在必要的底層操作
   - 確保正確釋放記憶體
   - 避免懸掛指標

## 小測驗
1. ref 和 ptr 的主要區別是什麼？
2. 什麼情況下應該使用 ptr 而不是 ref？
3. 如何避免記憶體洩漏？
4. 循環參考會造成什麼問題？如何解決？

## 進一步閱讀
- [Nim 官方文檔：Memory Management](https://nim-lang.org/docs/gc.html)
- [Nim 官方文檔：Pointers](https://nim-lang.org/docs/manual.html#types-pointer-type)
- [Nim Memory Management Deep Dive](https://nim-lang.org/blog/2020/10/15/introduction-to-arc-orc-in-nim.html)
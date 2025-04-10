# 進階型別

Nim 提供豐富且靈活的型別系統，支援多種進階型別，幫助開發者更精確地控制資料結構與程式邏輯。

## 學習目標
- 掌握 Nim 的進階型別系統
- 學會定義和使用自定義型別
- 理解型別轉換和型別推導
- 熟練運用集合和序列型別

## 先決條件
- 已完成基本型別學習
- 了解函式和模組基礎
- 熟悉基本控制流程

## 關鍵概念
1. 列舉和序數型別
   - enum 定義
   - 序數型別特性
   - 型別轉換
   
2. 複合型別
   - array 和 seq
   - set 和 table
   - tuple 和 object
   
3. 型別系統特性
   - 型別推導
   - 型別轉換
   - 泛型支援
   - 子型別關係

## 基本進階型別

### 列舉型別 (Enumerations)

列舉型別用於定義一組命名常數，適合表示有限的選項。

```nim
type Days = enum
  Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday

var today: Days = Tuesday
echo today  # 輸出：Tuesday
```

列舉型別的值可以用來進行比較或迭代：

```nim
for day in Days:
  echo day
```

程式會輸出所有列舉值。

### 序數型別 (Ordinal types)  
序數型別包括整數、字元和布林值，這些型別具有順序性。

```nim
var x: int = 5
echo x + 1  # 輸出：6

var ch: char = 'A'
echo ord(ch)  # 輸出：65
```

### 子範圍型別 (Subranges)  
子範圍型別用於限制數值範圍，提升程式的安全性。

```nim
type Age = range[0..120]

var myAge: Age = 25
# myAge = 150  # 編譯錯誤：超出範圍
```

子範圍型別確保值僅限於指定範圍。

## 集合與陣列

### 集合型別 (Sets)  
集合型別是一組唯一元素，適合用於位元欄位或快速查找。

```nim
type CharSet = set[char]

var vowels: CharSet = {'a', 'e', 'i', 'o', 'u'}
echo 'a' in vowels  # 輸出：true
```

集合型別支援集合運算，例如交集、聯集：

```nim
var consonants: CharSet = {'b', 'c', 'd'}
echo vowels + consonants  # 聯集
```

### 陣列型別 (Arrays)  
陣列型別是固定大小的資料結構，適合存放同型別的元素。

```nim
var arr: array[0..4, int] = [1, 2, 3, 4, 5]
echo arr[2]  # 輸出：3
```

陣列型別支援索引操作和遍歷：

```nim
for num in arr:
  echo num
```

## 動態與彈性型別

### 序列型別 (Sequences)  
序列型別是動態大小的陣列，適合存放可變長度的資料。

```nim
var seq: seq[int] = @[1, 2, 3]
seq.add(4)
echo seq  # 輸出：@[1, 2, 3, 4]
```

### 開放陣列 (Open arrays)  
開放陣列用於函式參數，允許傳入任意大小的陣列。

```nim
proc printArray(arr: openArray[int]) =
  for num in arr:
    echo num

printArray([1, 2, 3])
```

### 可變參數 (Varargs)  
可變參數允許函式接受不定數量的參數。

```nim
proc sum(args: varargs[int]): int =
  result = 0
  for num in args:
    result += num

echo sum(1, 2, 3, 4)  # 輸出：10
```

### 切片型別 (Slices)  
切片型別用於操作陣列或序列的一部分。

```nim
var arr = @[1, 2, 3, 4, 5]
echo arr[1..3]  # 輸出：@[2, 3, 4]
```

## 複合資料型別

### 物件型別 (Objects)  
物件型別是自定義的複合型別，適合表示更複雜的資料結構。

```nim
type Person = object
  name: string
  age: int

var p: Person = Person(name: "Alice", age: 30)
echo p.name  # 輸出：Alice
```

### 元組型別 (Tuples)  
元組型別是一組固定長度的值，適合存放不同型別的資料。

```nim
var t: tuple[name: string, age: int] = ("Bob", 25)
echo t.name  # 輸出：Bob
```

## 引用與函式型別

### 引用與指標型別 (Reference and pointer types)  
引用與指標型別用於操作記憶體中的資料。

```nim
var x: int = 10
var refX: ref int = ref x
refX[] = 20
echo x  # 輸出：20
```

### 程序型態 (Procedural type)  
程序型態用於表示函式型別，適合用於高階函式。

```nim
type MathFunc = proc(a, b: int): int

proc add(a, b: int): int = a + b
var f: MathFunc = add
echo f(3, 5)  # 輸出：8
```

## 特殊型別

### 區分型態 (Distinct type)  
區分型態用於定義與基礎型別不同的新型態，避免隱式轉換。

```nim
type Meter = distinct int

var length: Meter = 100
# echo length + 10  # 編譯錯誤：型態不匹配
```

透過 `distinct`，開發者可以定義更具語意的型態。

## 練習題

### 1. 基礎型別練習
```nim
# 1. 實現一個函式，將列舉型別轉換為字串
type
  Color = enum
    Red, Green, Blue
  
proc colorToString(c: Color): string =
  # TODO: 實現函式內容

# 2. 實現一個函式，檢查兩個集合是否有交集
proc hasIntersection[T](a, b: set[T]): bool =
  # TODO: 實現函式內容
```

### 2. 進階型別練習
```nim
# 1. 實現一個泛型佇列資料結構
type
  Queue[T] = object
    items: seq[T]

proc newQueue[T](): Queue[T] =
  # TODO: 實現建構函式

proc enqueue[T](q: var Queue[T], item: T) =
  # TODO: 實現入隊操作

proc dequeue[T](q: var Queue[T]): T =
  # TODO: 實現出隊操作

# 2. 實現一個簡單的資料庫表格結構
type
  Row = Table[string, string]
  Table = object
    columns: seq[string]
    rows: seq[Row]

proc addRow(t: var Table, row: Row) =
  # TODO: 實現添加行操作
```

### 3. 挑戰練習
```nim
# 實現一個簡單的二元樹結構
type
  BinaryTree[T] = ref object
    value: T
    left, right: BinaryTree[T]

proc insert[T](tree: var BinaryTree[T], value: T) =
  # TODO: 實現插入操作

proc search[T](tree: BinaryTree[T], value: T): bool =
  # TODO: 實現搜索操作
```

## 小測驗
1. 列舉型別和集合型別的區別是什麼？
2. 什麼情況下應該使用 tuple 而不是 object？
3. seq 和 array 的主要區別是什麼？
4. 如何實現自定義型別的相等比較？

## 進一步閱讀
- [Nim 官方文檔：Types](https://nim-lang.org/docs/manual.html#types)
- [Nim by Example：Types](https://nim-by-example.github.io/types/)
- [Nim 官方文檔：Type Classes](https://nim-lang.org/docs/manual.html#generics-type-classes)

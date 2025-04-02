# 進階型別

Nim 提供豐富且靈活的型別系統，支援多種進階型別，幫助開發者更精確地控制資料結構與程式邏輯。

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

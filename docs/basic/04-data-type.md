# 進階型別

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

## 序數型別

序數型別（Ordinal Types）是具有自然順序的型別，包括整數、字元、布林和列舉型別。

### 列舉型別（Enum）

列舉型別用於表示一組相關的命名常數：

```nim
type
  Direction = enum
    North, East, South, West

# 使用列舉型別
var heading: Direction = North
echo heading  # 輸出: North

# 轉換為整數
echo ord(heading)  # 輸出: 0

# 列舉迭代
for dir in Direction:
  echo dir  # 輸出所有方向：North, East, South, West
```

#### 自定義枚舉值

可以為列舉成員指定自定義值：

```nim
type
  HttpStatus = enum
    OK = 200,
    Created = 201,
    Accepted = 202,
    BadRequest = 400,
    Unauthorized = 401,
    NotFound = 404,
    ServerError = 500

echo HttpStatus.NotFound  # 輸出: NotFound
echo ord(HttpStatus.NotFound)  # 輸出: 404
```

## 複合型別

### 陣列（Array）

陣列是固定大小的同質集合，編譯時必須知道其大小：

```nim
# 聲明一個包含 5 個整數的陣列
var numbers: array[5, int]

# 初始化陣列
var colors = ["紅", "橙", "黃", "綠", "藍"]

# 訪問元素
echo colors[0]  # 輸出: 紅
colors[1] = "橘"
echo colors[1]  # 輸出: 橘

# 獲取陣列大小
echo colors.len  # 輸出: 5

# 迭代陣列
for color in colors:
  echo color
```

#### 自定義索引

Nim 允許使用非零起始索引或自定義索引型別：

```nim
# 從 1 開始的索引
var weekdays: array[1..7, string] = ["一", "二", "三", "四", "五", "六", "日"]
echo weekdays[1]  # 輸出: 一

# 使用列舉作為索引
type Day = enum Mon, Tue, Wed, Thu, Fri, Sat, Sun
var schedule: array[Day, string]
schedule[Mon] = "會議"
schedule[Fri] = "健身"
echo schedule[Mon]  # 輸出: 會議
```

### 序列（Sequence）

序列是動態大小的同質集合，可以在運行時改變大小：

```nim
# 聲明空序列
var nums: seq[int] = @[]

# 使用 @ 運算符創建序列
var fruits = @["蘋果", "香蕉", "櫻桃"]

# 添加元素
fruits.add("橘子")
echo fruits  # 輸出: @["蘋果", "香蕉", "櫻桃", "橘子"]

# 連接序列
let moreFruits = @["葡萄", "西瓜"]
fruits.add(moreFruits)
echo fruits  # 輸出: @["蘋果", "香蕉", "櫻桃", "橘子", "葡萄", "西瓜"]

# 序列切片
echo fruits[1..3]  # 輸出: @["香蕉", "櫻桃", "橘子"]

# 檢查元素存在
echo "蘋果" in fruits  # 輸出: true
```

### 元組（Tuple）

元組是異質的固定大小集合，可以包含不同型別的元素：

```nim
# 定義元組
var person: tuple[name: string, age: int] = ("Alice", 30)

# 訪問元素
echo person.name  # 輸出: Alice
echo person.age   # 輸出: 30

# 解構元組
let (name, age) = person
echo name  # 輸出: Alice

# 匿名元組
let point = (10, 20)
echo point[0]  # 輸出: 10
```

#### 命名元組型別

```nim
# 定義命名元組型別
type
  Person = tuple
    name: string
    age: int
    active: bool

# 使用命名元組型別
var employee: Person = (name: "Bob", age: 25, active: true)
echo employee  # 輸出: (name: "Bob", age: 25, active: true)
```

### 物件（Object）

物件是自定義複合型別，類似於其他語言中的類或結構體：

```nim
# 定義物件型別
type
  Student = object
    id: int
    name: string
    grades: seq[float]

# 創建物件實例
var alice = Student(id: 1, name: "Alice", grades: @[95.5, 87.0, 92.5])

# 訪問物件欄位
echo alice.name   # 輸出: Alice
alice.grades.add(89.0)
echo alice.grades  # 輸出: @[95.5, 87.0, 92.5, 89.0]
```

#### 物件變體（Variant Objects）

物件變體使用 `case` 語句根據一個辨別欄位包含不同的欄位集：

```nim
type
  Shape = object
    case kind: enum
      Circle, Rectangle, Triangle
    of Circle:
      radius: float
    of Rectangle:
      width, height: float
    of Triangle:
      a, b, c: float  # 三邊長度

# 創建圓形
var circle = Shape(kind: Circle, radius: 5.0)
echo circle.radius  # 輸出: 5.0

# 創建矩形
var rect = Shape(kind: Rectangle, width: 10.0, height: 20.0)
echo rect.width, " x ", rect.height  # 輸出: 10.0 x 20.0
```

### 集合（Set）

集合用於高效存儲和操作一組唯一的序數型別值：

```nim
# 定義集合型別
type Colors = enum
  Red, Green, Blue, Yellow, Purple, Orange

# 創建集合
var primaryColors: set[Colors] = {Red, Green, Blue}
var warmColors = {Red, Yellow, Orange}

# 集合操作
let allColors = primaryColors + warmColors  # 聯集
let commonColors = primaryColors * warmColors  # 交集
let diffColors = warmColors - primaryColors  # 差集

echo Red in primaryColors  # 輸出: true
echo Yellow in primaryColors  # 輸出: false

# 遍歷集合（注意：集合無序）
for color in allColors:
  echo color
```

### 表（Table）

表是鍵值對的集合，類似於其他語言中的字典或映射：

```nim
import tables

# 創建表
var ages = {"Alice": 30, "Bob": 25, "Charlie": 35}.toTable

# 添加和修改元素
ages["David"] = 28
ages["Alice"] = 31  # 修改現有值

# 訪問元素
echo ages["Bob"]  # 輸出: 25

# 檢查鍵存在
if ages.hasKey("Eve"):
  echo ages["Eve"]
else:
  echo "Eve 不在表中"  # 輸出此行

# 遍歷表
for name, age in ages:
  echo name, ": ", age
```

## 引用和指標型別

### 引用型別（ref）

引用型別在堆上分配記憶體，並自動管理記憶體：

```nim
type
  Node = ref object
    data: int
    next: Node

# 創建引用物件
var first = Node(data: 10, next: nil)
var second = Node(data: 20, next: nil)
first.next = second

echo first.data  # 輸出: 10
echo first.next.data  # 輸出: 20
```

### 指標型別（ptr）

指標型別提供更低層次的記憶體存取，但不進行自動記憶體管理：

```nim
type
  MyPtr = ptr int

# 分配記憶體
var x = 42
var p: MyPtr = addr(x)  # 獲取 x 的記憶體地址

echo p[]  # 解引用，輸出: 42

# 修改指向的值
p[] = 100
echo x  # 輸出: 100
```

## 型別轉換

Nim 提供多種方式進行型別轉換：

### 基本型別轉換

```nim
var x = 65
var c = char(x)  # 將整數轉換為字元
echo c  # 輸出: A

var f = 3.14
var i = int(f)  # 將浮點數轉換為整數
echo i  # 輸出: 3

var b = true
var s = $b  # 將任何型別轉換為字串
echo s  # 輸出: "true"
```

### 序數和字串轉換

```nim
type Color = enum
  Red, Green, Blue

var c = Red
var i = ord(c)  # 列舉轉換為整數
echo i  # 輸出: 0

var nextColor = Color(ord(c) + 1)  # 整數轉換為列舉
echo nextColor  # 輸出: Green
```

## 實戰示例

### 示例 1：學生管理系統

結合物件和序列創建一個簡單的學生管理系統：

```nim
type
  Grade = enum
    A, B, C, D, F
  
  Subject = enum
    Math, Physics, Chemistry, Biology, ComputerScience
  
  CourseResult = object
    subject: Subject
    grade: Grade
  
  Student = object
    id: int
    name: string
    results: seq[CourseResult]

proc addResult(student: var Student, subject: Subject, grade: Grade) =
  student.results.add(CourseResult(subject: subject, grade: grade))

proc calculateGPA(student: Student): float =
  if student.results.len == 0:
    return 0.0
  
  var total = 0.0
  for result in student.results:
    case result.grade
    of A: total += 4.0
    of B: total += 3.0
    of C: total += 2.0
    of D: total += 1.0
    of F: total += 0.0
  
  return total / float(student.results.len)

proc printTranscript(student: Student) =
  echo "學生: ", student.name, " (ID: ", student.id, ")"
  echo "成績單:"
  for result in student.results:
    echo "  ", result.subject, ": ", result.grade
  echo "平均績點: ", student.calculateGPA()

# 使用示例
var alice = Student(id: 1, name: "Alice", results: @[])
alice.addResult(Math, A)
alice.addResult(Physics, B)
alice.addResult(Chemistry, A)
alice.addResult(Biology, C)

alice.printTranscript()
```

### 示例 2：多媒體庫

使用物件變體建立一個多媒體項目庫：

```nim
import strutils

type
  MediaType = enum
    Audio, Video, Image
  
  MediaItem = object
    id: int
    title: string
    case mediaType: MediaType
    of Audio:
      duration: int  # 秒
      artist: string
    of Video:
      length: int    # 秒
      resolution: tuple[width: int, height: int]
    of Image:
      dimensions: tuple[width: int, height: int]
      format: string  # "jpeg", "png", etc.

proc formatDuration(seconds: int): string =
  let minutes = seconds div 60
  let remSeconds = seconds mod 60
  return $minutes & ":" & align($remSeconds, 2, '0')

proc displayMediaInfo(item: MediaItem) =
  echo "ID: ", item.id
  echo "標題: ", item.title
  
  case item.mediaType
  of Audio:
    echo "類型: 音訊"
    echo "時長: ", formatDuration(item.duration)
    echo "藝術家: ", item.artist
  of Video:
    echo "類型: 視訊"
    echo "時長: ", formatDuration(item.length)
    echo "解析度: ", item.resolution.width, "x", item.resolution.height
  of Image:
    echo "類型: 圖片"
    echo "尺寸: ", item.dimensions.width, "x", item.dimensions.height
    echo "格式: ", item.format

# 使用示例
var mediaLibrary: seq[MediaItem] = @[
  MediaItem(id: 1, title: "Favorite Song", mediaType: Audio, duration: 187, artist: "Great Artist"),
  MediaItem(id: 2, title: "Vacation Photo", mediaType: Image, dimensions: (1920, 1080), format: "jpeg"),
  MediaItem(id: 3, title: "Tutorial Video", mediaType: Video, length: 720, resolution: (1280, 720))
]

for item in mediaLibrary:
  displayMediaInfo(item)
  echo "-------------------"
```

## 最佳實踐

1. **型別選擇**：
   - 使用陣列時確定大小固定且編譯時已知
   - 使用序列時需要動態調整大小
   - 需要異質數據但不需繼承時，優先使用元組
   - 物件適用於需要更複雜結構或未來可能擴展的情況

2. **記憶體管理**：
   - 優先使用 `ref` 而非 `ptr`，除非有特殊性能需求
   - 使用 `ptr` 時注意手動釋放記憶體以避免洩漏

3. **型別安全**：
   - 避免使用 `cast` 進行型別轉換，除非絕對必要
   - 對序數轉換，確保值在目標型別範圍內
   - 使用物件變體而非手動標記和條件判斷

4. **集合操作**：
   - 對於需要頻繁檢查成員資格的小型序數集合，使用 `set`
   - 對於更大或非序數型別的集合，使用 `HashSet`

## 小測驗
1. 列舉型別的第一個成員的序數值是什麼？
2. Nim 中，如何獲取序列的最後一個元素？
3. 物件和元組的主要區別是什麼？
4. 在 Nim 中，哪種型別用於在堆上分配記憶體？
5. 以下代碼輸出什麼？
   ```nim
   var colors = {2, 4, 6, 8}
   var moreColors = {1, 3, 5, 7, 9}
   echo 4 in colors
   echo 5 in colors + moreColors
   ```

## 進一步閱讀
- [Nim 官方文檔：型別](https://nim-lang.org/docs/manual.html#types)
- [Nim 官方文檔：序列、陣列和字串](https://nim-lang.org/docs/manual.html#types-array-and-sequence-types)
- [Nim 標準庫：tables 模組](https://nim-lang.org/docs/tables.html)
- [Nim 記憶體管理](https://nim-lang.org/docs/gc.html)

---

下一章將介紹 Nim 的引用和指標，深入探討記憶體管理機制。

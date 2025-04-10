# 物件導向程式設計 (OOP)

## 學習目標
- 理解 Nim 中物件導向編程的基本概念與實現方式
- 掌握 Nim 中的繼承、多型與類型系統
- 學習方法調用與多態性在 Nim 中的應用
- 理解屬性管理與訪問控制的實現方法
- 掌握 Nim 特有的物件變體與遞迴類型設計

## 先決條件
- 基本的 Nim 語法和物件定義
- 理解基礎的 Nim 類型系統
- 對一般物件導向概念有基本認識

## 關鍵概念
1. **類型系統與繼承**
   - `RootObj` 與繼承層次
   - `ref` 類型與對象完整性
   - 類型檢查與轉換

2. **物件方法與調用**
   - 方法定義與語法
   - 動態分派與靜態分派
   - 多方法實現

3. **屬性管理**
   - Getter 與 Setter
   - 訪問控制模式
   - 屬性轉換器

4. **物件變體**
   - 情況對象 (case objects)
   - 多型實現
   - 遞迴類型與自引用結構

## Nim 中的物件導向編程

Nim 是一種多範式程式語言，支援物件導向程式設計（OOP），但其設計哲學更鼓勵靈活性和簡潔性。Nim 的 OOP 特性包括繼承、動態分派、方法呼叫、屬性管理以及物件變體等，這讓開發者可以根據需求選擇最合適的編程風格。

### Nim OOP 的獨特方面

Nim 的物件導向特性與傳統的 OOP 語言（如 Java 或 C++）有一些關鍵差異：

1. **更靈活的型別系統**：Nim 允許使用物件變體來創建高效的聯合型別
2. **更明確的引用語義**：通過 `ref` 關鍵字明確指定引用類型
3. **沒有隱含的虛函數表**：方法需要顯式地標記為 `method` 才能支持動態分派
4. **更強的編譯期解析**：Nim 儘可能在編譯期解決調用，提升運行效率
5. **靜態與動態分派的清晰區分**：使用 `proc` 和 `method` 區分靜態和動態調用

## 繼承與類型系統

### 繼承的基本概念

在 Nim 中，物件可以通過繼承來建立層次結構。所有需要支援繼承的物件必須繼承自 `RootObj`，並建議使用 `ref` 型別來確保物件的完整性。

```nim
type
  Person = ref object of RootObj
    name: string
    age: int
  
  Student = ref object of Person
    studentId: string
    major: string
  
  Teacher = ref object of Person
    employeeId: string
    department: string

# 創建實例
let john = Student(name: "John Doe", age: 20, studentId: "S12345", major: "Computer Science")
let mary = Teacher(name: "Mary Smith", age: 45, employeeId: "T98765", department: "Mathematics")

# 訪問屬性
echo john.name, ", ", john.age, ", ", john.studentId  # John Doe, 20, S12345
echo mary.name, ", ", mary.age, ", ", mary.department  # Mary Smith, 45, Mathematics
```

### 繼承鏈與多層繼承

Nim 支持多層繼承，可以構建複雜的繼承層次結構：

```nim
type
  Vehicle = ref object of RootObj
    make: string
    model: string
  
  Car = ref object of Vehicle
    doors: int
    
  ElectricCar = ref object of Car
    batteryCapacity: float
    range: int

let tesla = ElectricCar(
  make: "Tesla", 
  model: "Model 3", 
  doors: 4, 
  batteryCapacity: 75.0, 
  range: 350
)

echo tesla.make, " ", tesla.model  # Tesla Model 3
echo "Doors: ", tesla.doors  # Doors: 4
echo "Range: ", tesla.range, " km"  # Range: 350 km
```

### 使用 `of` 進行類型檢查

Nim 提供 `of` 運算子來檢查物件是否屬於某個類型，這是實現多型的基礎：

```nim
type
  Animal = ref object of RootObj
    name: string
  
  Dog = ref object of Animal
  Cat = ref object of Animal
  
  Toy = ref object of RootObj
    color: string
  
  Ball = ref object of Toy
  Yarn = ref object of Toy

# 創建不同類型的動物
let buddy: Animal = Dog(name: "Buddy")
let whiskers: Animal = Cat(name: "Whiskers")

# 使用 of 運算子進行類型檢查
if buddy of Dog:
  echo buddy.name, " is a dog"
  # 安全的類型轉換
  let dog = Dog(buddy)
  echo "Breed: ", dog.name

if whiskers of Cat:
  echo whiskers.name, " is a cat"
  let cat = Cat(whiskers)
  echo "Fur color: ", cat.name
```

### `ref` 型別的重要性

在 Nim 中，繼承階層中的物件建議使用 `ref` 型別（引用型別），這樣做有幾個重要原因：

1. **避免對象切片（object slicing）**：非引用物件在賦值時可能會丟失子類特有的字段
2. **支持多型**：動態分派需要引用語義才能正確工作
3. **保持一致的對象標識**：引用類型確保對象在傳遞時保持同一標識

```nim
# 不使用 ref 的問題示例
type
  Base = object of RootObj
    a: int
  
  Derived = object of Base
    b: int

# 對象切片示例
var x: Derived = Derived(a: 1, b: 2)
var y: Base = x  # x 被切片，y 只有 a 字段

echo y.a  # 1 可以訪問
# echo y.b  # 編譯錯誤：Base 沒有 b 字段

# 使用 ref 的正確方式
type
  BaseRef = ref object of RootObj
    a: int
  
  DerivedRef = ref object of BaseRef
    b: int

var xRef: DerivedRef = DerivedRef(a: 1, b: 2)
var yRef: BaseRef = xRef  # yRef 引用與 xRef 相同的對象

echo yRef.a  # 1 可以訪問
# 使用類型轉換訪問 b
if yRef of DerivedRef:
  let z = DerivedRef(yRef)
  echo z.b  # 2
```

## 方法呼叫與多態性

在 Nim 中，有兩種定義與物件相關的函數的主要方式：`proc`（過程）和 `method`（方法）。這兩者在調用解析方面有重要差異。

### 靜態分派 - `proc`

`proc` 是靜態分派的，調用在編譯時根據變數的聲明類型決定：

```nim
type
  Shape = ref object of RootObj
  
  Circle = ref object of Shape
    radius: float
  
  Rectangle = ref object of Shape
    width, height: float

# 使用 proc 定義函數
proc area(s: Shape): float = 0.0  # 基類版本
proc area(c: Circle): float = 3.14159 * c.radius * c.radius
proc area(r: Rectangle): float = r.width * r.height

# 創建實例
let circle: Circle = Circle(radius: 5.0)
let rectangle: Rectangle = Rectangle(width: 4.0, height: 3.0)

# 直接調用特定類型的 proc
echo "Circle area: ", area(circle)  # 78.53975
echo "Rectangle area: ", area(rectangle)  # 12.0

# 但請注意：
let shape1: Shape = circle
let shape2: Shape = rectangle

# 這將調用 Shape 的 area 方法，不是子類的!
echo "Shape1 area: ", area(shape1)  # 0.0
echo "Shape2 area: ", area(shape2)  # 0.0
```

### 動態分派 - `method`

`method` 是動態分派的，調用在運行時根據物件的實際類型決定：

```nim
type
  Shape = ref object of RootObj
  
  Circle = ref object of Shape
    radius: float
  
  Rectangle = ref object of Shape
    width, height: float

# 使用 method 定義方法
method area(s: Shape): float {.base.} = 0.0  # 基類版本，標記為 base
method area(c: Circle): float = 3.14159 * c.radius * c.radius
method area(r: Rectangle): float = r.width * r.height

# 創建實例
let circle: Circle = Circle(radius: 5.0)
let rectangle: Rectangle = Rectangle(width: 4.0, height: 3.0)

# 通過基類引用調用方法
let shape1: Shape = circle
let shape2: Shape = rectangle

# 使用動態分派，調用實際類型的方法
echo "Shape1 (Circle) area: ", shape1.area()  # 78.53975
echo "Shape2 (Rectangle) area: ", shape2.area()  # 12.0
```

注意 `{.base.}` 標記用於指定基類方法，這是為動態分派提供基準的必要標記。

### 方法調用語法

Nim 支援物件方法調用語法，使用 `obj.methodName(args)` 的形式，這使代碼更具可讀性：

```nim
type
  Vehicle = ref object of RootObj
    brand: string
    year: int

proc start(v: Vehicle) =
  echo "Starting the ", v.brand, " from ", v.year

proc stop(v: Vehicle) =
  echo "Stopping the ", v.brand

let myCar = Vehicle(brand: "Toyota", year: 2020)

# 以下兩種調用方式是等價的
start(myCar)  # 傳統函數調用
myCar.start()  # 方法調用語法

myCar.stop()  # 方法調用語法更直觀
```

### 多方法與多重分派

Nim 的方法支持多重分派，這意味著方法的選擇可以基於多個參數的類型：

```nim
type
  Animal = ref object of RootObj
    name: string
  
  Dog = ref object of Animal
  Cat = ref object of Animal
  
  Toy = ref object of RootObj
    color: string
  
  Ball = ref object of Toy
  Yarn = ref object of Toy

# 多重分派方法
method play(a: Animal, t: Toy) {.base.} =
  echo a.name, " plays with a toy"

method play(d: Dog, b: Ball) =
  echo d.name, " chases the ", b.color, " ball"

method play(c: Cat, y: Yarn) =
  echo c.name, " bats at the ", y.color, " yarn"

# 創建實例
let buddy = Dog(name: "Buddy")
let whiskers = Cat(name: "Whiskers")
let redBall = Ball(color: "red")
let blueYarn = Yarn(color: "blue")

# 方法調用
buddy.play(redBall)  # Buddy chases the red ball
whiskers.play(blueYarn)  # Whiskers bats at the blue yarn

# 但當類型不完全匹配時，會調用基類方法
whiskers.play(redBall)  # Whiskers plays with a toy
```

## 屬性管理與訪問控制

### Getter 與 Setter 方法

Nim 允許通過自定義的 Getter 和 Setter 方法來實現屬性的控制訪問：

```nim
type
  Person = ref object of RootObj
    firstName: string
    lastName: string
    _age: int  # 下劃線表示這是一個私有字段

# Getter 方法
proc age(p: Person): int = p._age

# Setter 方法，使用特殊的 `=` 語法
proc `age=`(p: Person, value: int) =
  if value >= 0:
    p._age = value
  else:
    raise newException(ValueError, "Age cannot be negative")

# 計算屬性
proc fullName(p: Person): string =
  return p.firstName & " " & p.lastName

# 使用屬性
var person = Person(firstName: "John", lastName: "Doe", _age: 25)

# 使用 Getter
echo "Age: ", person.age()  # 或直接: person.age

# 使用 Setter
person.age = 30
echo "New age: ", person.age

# 使用計算屬性
echo "Full name: ", person.fullName

# 異常處理
try:
  person.age = -5  # 這將拋出異常
except ValueError as e:
  echo "Error: ", e.msg
```

### 實現訪問控制

Nim 沒有內置的 `private` 或 `protected` 關鍵字，但可以通過約定和模塊系統實現訪問控制：

```nim
# 在 person.nim 模塊中
type
  Person* = ref object  # 類型導出 (公開)
    name*: string  # 字段導出 (公開)
    _internalId: string  # 字段不導出 (私有)

proc newPerson*(name: string): Person =
  # 公開的建構函數
  result = Person(name: name, _internalId: "ID-" & name)

proc getId*(p: Person): string =
  # 公開的訪問方法
  result = p._internalId

proc setInternalId(p: Person, id: string) =
  # 私有方法，不導出
  p._internalId = id

# 使用時，導入的模塊只能訪問到標記為 * 的項
```

### 自定義運算符

Nim 允許為物件定義自定義運算符，這可以使代碼更具表達力：

```nim
type
  Vector = object
    x, y: float

# 定義加法運算符
proc `+`(a, b: Vector): Vector =
  result.x = a.x + b.x
  result.y = a.y + b.y

# 定義減法運算符
proc `-`(a, b: Vector): Vector =
  result.x = a.x - b.x
  result.y = a.y - b.y

# 定義數乘運算符
proc `*`(a: Vector, scalar: float): Vector =
  result.x = a.x * scalar
  result.y = a.y * scalar

# 定義點積運算符
proc `*`(a, b: Vector): float =
  result = a.x * b.x + a.y * b.y

# 定義字符串表示
proc `$`(v: Vector): string =
  result = "Vector(" & $v.x & ", " & $v.y & ")"

# 使用自定義運算符
let v1 = Vector(x: 1.0, y: 2.0)
let v2 = Vector(x: 3.0, y: 4.0)

echo v1 + v2  # Vector(4.0, 6.0)
echo v1 * 2.0  # Vector(2.0, 4.0)
echo v1 * v2  # 11.0 (點積)
```

## 物件變體與遞迴類型

### 物件變體 (Case Objects)

物件變體是 Nim 中一個強大的特性，允許創建包含不同字段集的物件，這些字段依賴於物件的某個判別字段：

```nim
type
  NodeKind = enum
    nkInt, nkFloat, nkString
  
  Node = object
    case kind: NodeKind  # 判別字段
    of nkInt:
      intValue: int
    of nkFloat:
      floatValue: float
    of nkString:
      stringValue: string

# 創建不同類型的節點
let intNode = Node(kind: nkInt, intValue: 42)
let floatNode = Node(kind: nkFloat, floatValue: 3.14)
let stringNode = Node(kind: nkString, stringValue: "Hello")

# 根據類型處理節點
proc getValue(node: Node): string =
  case node.kind
  of nkInt:
    result = $node.intValue
  of nkFloat:
    result = $node.floatValue
  of nkString:
    result = node.stringValue

echo "Int node value: ", getValue(intNode)  # 42
echo "Float node value: ", getValue(floatNode)  # 3.14
echo "String node value: ", getValue(stringNode)  # Hello
```

物件變體可以用於實現類似於代數數據類型或聯合類型的功能，特別適合於抽象語法樹、解析器等場景。

### 遞迴類型與數據結構

Nim 支持自引用的遞迴類型，這對於實現樹、鏈表等數據結構很有用：

```nim
# 二叉樹節點
type
  BinaryTree = ref object
    value: int
    left: BinaryTree
    right: BinaryTree

# 創建樹
proc newNode(value: int): BinaryTree =
  result = BinaryTree(value: value)

# 插入值
proc insert(tree: var BinaryTree, value: int) =
  if tree == nil:
    tree = newNode(value)
  elif value < tree.value:
    insert(tree.left, value)
  else:
    insert(tree.right, value)

# 中序遍歷
proc inorderTraversal(tree: BinaryTree) =
  if tree != nil:
    inorderTraversal(tree.left)
    echo tree.value
    inorderTraversal(tree.right)

# 創建並填充樹
var root: BinaryTree = nil
insert(root, 5)
insert(root, 3)
insert(root, 7)
insert(root, 2)
insert(root, 4)

# 遍歷
echo "Inorder traversal:"
inorderTraversal(root)  # 2 3 4 5 7
```

### 互相遞迴類型

Nim 還支持互相引用的類型，這些必須在同一個 `type` 區塊中定義：

```nim
type
  Person = ref object
    name: string
    friends: seq[Person]
    employer: Company
  
  Company = ref object
    name: string
    employees: seq[Person]

# 創建實例
var apple = Company(name: "Apple", employees: @[])
var john = Person(name: "John", friends: @[], employer: apple)
var mary = Person(name: "Mary", friends: @[], employer: apple)

# 添加關係
john.friends.add(mary)
mary.friends.add(john)
apple.employees.add(john)
apple.employees.add(mary)

# 訪問關係
echo john.name, " works at ", john.employer.name
echo john.friends[0].name, " is a friend of ", john.name
echo apple.name, " has ", apple.employees.len, " employees"
```

## 實用案例：設計模式實現

### 觀察者模式 (Observer Pattern)

```nim
type
  Observable = ref object of RootObj
    observers: seq[Observer]
  
  Observer = ref object of RootObj

# 觀察者接口
method update(observer: Observer, observable: Observable, data: string) {.base.} =
  # 基類方法，需要被覆寫
  discard

# 可觀察對象的方法
proc addObserver(observable: Observable, observer: Observer) =
  observable.observers.add(observer)

proc removeObserver(observable: Observable, observer: Observer) =
  let index = observable.observers.find(observer)
  if index >= 0:
    observable.observers.delete(index)

proc notifyObservers(observable: Observable, data: string) =
  for observer in observable.observers:
    observer.update(observable, data)

# 具體實現
type
  DataSource = ref object of Observable
    value: string
  
  DataDisplay = ref object of Observer
    name: string

# 實現 DataSource
proc setValue(source: DataSource, newValue: string) =
  source.value = newValue
  source.notifyObservers(newValue)

# 實現 DataDisplay
method update(display: DataDisplay, observable: Observable, data: string) =
  echo display.name, " received update: ", data

# 使用該模式
let source = DataSource(observers: @[])
let display1 = DataDisplay(name: "Display 1")
let display2 = DataDisplay(name: "Display 2")

source.addObserver(display1)
source.addObserver(display2)

source.setValue("Hello, observers!")  # 兩個顯示器都會收到更新
source.removeObserver(display1)
source.setValue("Second update")  # 只有 display2 會收到
```

### 單例模式 (Singleton Pattern)

```nim
import std/locks

type
  Database = ref object
    connection: string
    queryCount: int

var instance: Database = nil
var instanceLock: Lock

initLock(instanceLock)

proc getInstance(): Database =
  if instance == nil:
    # 線程安全的單例創建
    withLock instanceLock:
      if instance == nil:  # 雙重檢查
        instance = Database(connection: "db://localhost:5432", queryCount: 0)
  return instance

proc query(db: Database, sql: string): string =
  inc db.queryCount
  echo "Executing query: ", sql
  echo "Total queries: ", db.queryCount
  return "query result"

# 使用單例
let db1 = getInstance()
let db2 = getInstance()

echo "Same instance? ", db1 == db2  # 應該為 true

discard db1.query("SELECT * FROM users")
discard db2.query("SELECT * FROM products")  # 會影響相同的 queryCount
```

## 練習與挑戰

1. **繼承層次設計**
   - 設計一個圖形編輯器應用的繼承層次結構
   - 實現至少三層繼承，包括各種形狀（圓形、矩形、三角形等）
   - 添加適當的方法，如 draw(), move(), resize()

2. **物件變體應用**
   - 使用物件變體實現一個簡單的表達式求值器
   - 支持數值、變量和二元運算（加減乘除）
   - 實現表達式的解析和計算功能

3. **屬性訪問控制**
   - 創建一個使用者帳戶系統，實現以下屬性：
     - username：公開字段
     - password：私有字段，提供驗證方法但不直接訪問
     - lastLogin：只讀屬性
     - email：帶有驗證的讀寫屬性

4. **多重分派實現**
   - 設計一個多媒體播放器系統，支持不同的媒體類型（音頻、視頻、圖像）
   - 實現不同播放設備（電腦、手機、智能電視）的播放方法
   - 使用多重分派確保適當的播放方法被調用

## 自我評估問題

1. 解釋 Nim 中 `ref object of RootObj` 的作用，並說明為什麼繼承結構中推薦使用 `ref` 類型？
2. `proc` 和 `method` 在方法分派方面有什麼區別？何時應該選擇一種而非另一種？
3. 在 Nim 中如何實現屬性的 Getter 和 Setter？給出一個示例。
4. 物件變體與繼承在表達多型時有什麼區別？各自適用於什麼場景？
5. 如何在 Nim 中實現類似於 "私有" 和 "保護" 字段的訪問控制？
6. 解釋 `{.base.}` 標記在方法定義中的作用。
7. 什麼情況下應該使用遞迴類型，它們如何解決循環依賴問題？

## 進一步閱讀

- [Nim 官方文檔：物件導向編程](https://nim-lang.org/docs/tut2.html#object-oriented-programming)
- [Nim 方法分派機制詳解](https://nim-lang.org/docs/manual.html#methods)
- [Nim 物件變體與代數數據類型](https://nim-lang.org/docs/manual.html#types-object-variants)
- [有效 Nim：物件導向設計模式實現](https://nim-lang.org/docs/tut3.html)
- [物件導向編程最佳實踐](https://hookrace.net/blog/nim-object-oriented-programming/)

## 小結

本章深入探討了 Nim 中的物件導向編程特性。Nim 提供了一種靈活的物件系統，支持繼承、多態、封裝等傳統 OOP 概念，同時通過物件變體、多重分派等特性提供了更豐富的表達能力。

Nim 的 OOP 實現體現了語言設計的精髓：提供強大的功能但不強制使用特定範式。開發者可以根據問題的性質選擇適當的程度使用 OOP 特性，結合 Nim 的過程式和函數式特性創建清晰、高效的代碼。

物件變體和靜態/動態分派的明確區分是 Nim OOP 的獨特之處，這些特性使 Nim 能夠在保持高性能的同時提供豐富的表達能力。通過本章的學習，讀者應該能夠理解並運用 Nim 的物件系統來設計靈活、可維護的程序。
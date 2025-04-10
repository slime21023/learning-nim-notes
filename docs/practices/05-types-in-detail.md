# 練習 5: 型別詳解與運用

## Q1: 自定義型別與操作
```nim
# 目標：實作自定義型別並操作
# 要求：
# 1. 建立一個 Person 型別，含有姓名、年齡和電子郵件屬性
# 2. 實作函數 isAdult 檢查 Person 是否成年（年齡 >= 18）
# 3. 實作函數 validateEmail 檢查電子郵件格式是否有效
# 4. 實作過程 printInfo 顯示 Person 的所有資訊

import strformat, strutils, re

# 定義 Person 型別
type
  Person = object
    name: string
    age: int
    email: string

# 檢查 Person 是否成年
proc isAdult(p: Person): bool =
  # TODO: 實作檢查是否年滿18歲
  
# 驗證電子郵件格式
proc validateEmail(email: string): bool =
  # TODO: 使用正則表達式驗證電子郵件格式
  
# 顯示 Person 資訊
proc printInfo(p: Person) =
  # TODO: 印出 Person 的所有屬性，並標示是否成年、電子郵件是否有效
  
# 測試函數
proc testPersonType() =
  var people = [
    Person(name: "小明", age: 25, email: "ming@example.com"),
    Person(name: "小華", age: 17, email: "hua@invalid"),
    Person(name: "小美", age: 30, email: "mei.example.com")
  ]
  
  for person in people:
    printInfo(person)
    echo "------------------------"

# 執行測試
testPersonType()
```

## Q2: 列舉型別與模式匹配

```nim
# 目標：使用列舉型別表示狀態
# 要求：
# 1. 實作一個交通號誌系統，使用列舉表示不同信號
# 2. 為每個號誌狀態實作對應的行為
# 3. 模擬號誌變化週期

import strformat

# 定義交通號誌列舉型別
type
  TrafficLightColor = enum
    Red, Yellow, Green

# 取得目前號誌的指示行為
proc getInstruction(color: TrafficLightColor): string =
  # TODO: 根據不同號誌顏色返回對應指示
  
# 取得下一個號誌狀態
proc nextColor(color: TrafficLightColor): TrafficLightColor =
  # TODO: 實作號誌變化邏輯
  
# 模擬號誌變化
proc simulateTrafficLight() =
  # TODO: 模擬號誌循環變化10次，每次顯示指示內容
  
# 執行模擬
simulateTrafficLight()
```

## Q3: 集合型別操作

```nim
# 目標：使用集合型別解決實際問題
# 要求：
# 1. 實作一個記錄學生選課情況的系統
# 2. 能夠進行課程聯集、交集、差集操作
# 3. 支援檢查一個學生是否選擇了某課程

# 定義課程列舉型別
type
  Course = enum
    Math, Physics, Chemistry, Biology, History, Literature

# 建立含選課資訊的學生型別
type
  Student = object
    name: string
    courses: set[Course]

# 增加課程到學生選課清單
proc addCourse(student: var Student, course: Course) =
  # TODO: 將課程加入學生的選課集合
  
# 移除學生選課
proc removeCourse(student: var Student, course: Course) =
  # TODO: 從學生的選課集合中移除指定課程
  
# 檢查學生是否選了特定課程
proc hasCourse(student: Student, course: Course): bool =
  # TODO: 檢查學生是否選擇了指定課程
  
# 取得兩位學生共同選的課程
proc commonCourses(a, b: Student): set[Course] =
  # TODO: 返回兩位學生共同選的課程（交集）
  
# 取得某學生選但另一學生沒選的課程
proc uniqueCourses(a, b: Student): set[Course] =
  # TODO: 返回 a 選但 b 沒選的課程（差集）
  
# 列印學生選課情況
proc printStudentCourses(student: Student) =
  # TODO: 列印學生姓名與已選課程
  
# 測試學生選課系統
proc testStudentCourses() =
  var
    alice = Student(name: "Alice", courses: {Math, Physics, Chemistry})
    bob = Student(name: "Bob", courses: {Math, Biology, History})
  
  echo "原始選課情況:"
  printStudentCourses(alice)
  printStudentCourses(bob)
  
  echo "\nAlice 和 Bob 共同選的課程:"
  echo commonCourses(alice, bob)
  
  echo "\nAlice 選但 Bob 沒選的課程:"
  echo uniqueCourses(alice, bob)
  
  echo "\nBob 選但 Alice 沒選的課程:"
  echo uniqueCourses(bob, alice)
  
  # 修改 Alice 的選課
  removeCourse(alice, Chemistry)
  addCourse(alice, Literature)
  
  echo "\n修改後 Alice 的選課情況:"
  printStudentCourses(alice)

# 執行測試
testStudentCourses()
```

## Q4: 元組與物件

```nim
# 目標：比較元組與物件的用法
# 要求：
# 1. 實作相同功能的座標系統，一個使用元組，一個使用物件
# 2. 分別實作座標點的基本操作（距離計算、移動等）
# 3. 比較兩種實作方式的差異

import math

# 使用元組實作座標點
type
  PointTuple = tuple
    x, y: float

# 使用物件實作座標點
type
  PointObject = object
    x, y: float

# 計算兩點距離（元組版本）
proc distance(a, b: PointTuple): float =
  # TODO: 計算兩點距離
  
# 計算兩點距離（物件版本）
proc distance(a, b: PointObject): float =
  # TODO: 計算兩點距離
  
# 移動點的位置（元組版本）
proc move(p: PointTuple, dx, dy: float): PointTuple =
  # TODO: 回傳移動後的新點
  
# 移動點的位置（物件版本）
proc move(p: var PointObject, dx, dy: float) =
  # TODO: 直接修改原點位置
  
# 比較元組與物件實作
proc compareImplementations() =
  # 元組實作測試
  let
    t1 = (x: 1.0, y: 2.0)
    t2 = (x: 4.0, y: 6.0)
  
  var t3 = t1
  t3 = move(t3, 2.0, 3.0)
  
  echo "元組實作測試:"
  echo "t1: ", t1
  echo "t2: ", t2
  echo "t1與t2的距離: ", distance(t1, t2)
  echo "t3 (t1移動後): ", t3
  
  # 物件實作測試
  var
    o1 = PointObject(x: 1.0, y: 2.0)
    o2 = PointObject(x: 4.0, y: 6.0)
  
  var o3 = o1
  move(o3, 2.0, 3.0)
  
  echo "\n物件實作測試:"
  echo "o1: (", o1.x, ", ", o1.y, ")"
  echo "o2: (", o2.x, ", ", o2.y, ")"
  echo "o1與o2的距離: ", distance(o1, o2)
  echo "o3 (o1移動後): (", o3.x, ", ", o3.y, ")"

# 執行比較
compareImplementations()
```

Q5: 變體物件

```nim
# 目標：使用變體物件處理多型性
# 要求：
# 1. 實作一個幾何形狀系統，使用變體物件表示不同形狀
# 2. 支援計算不同形狀的面積和周長
# 3. 實作一個能處理多種形狀的函數

import math

# 定義幾何形狀型別
type
  ShapeKind = enum
    Circle, Rectangle, Triangle
  
  Shape = object
    case kind: ShapeKind
    of Circle:
      radius: float
    of Rectangle:
      width, height: float
    of Triangle:
      a, b, c: float  # 三邊長度

# 計算形狀面積
proc area(shape: Shape): float =
  # TODO: 根據不同形狀計算面積
  
# 計算形狀周長
proc perimeter(shape: Shape): float =
  # TODO: 根據不同形狀計算周長
  
# 建立圓形
proc newCircle(radius: float): Shape =
  # TODO: 傳回圓形物件
  
# 建立矩形
proc newRectangle(width, height: float): Shape =
  # TODO: 傳回矩形物件
  
# 建立三角形（需檢查是否可以形成三角形）
proc newTriangle(a, b, c: float): Shape =
  # TODO: 檢查是否可以形成三角形，若可以則傳回三角形物件
  
# 取得形狀描述
proc description(shape: Shape): string =
  # TODO: 回傳形狀的類型和特徵描述
  
# 測試幾何形狀系統
proc testShapes() =
  let shapes = [
    newCircle(5.0),
    newRectangle(4.0, 6.0),
    newTriangle(3.0, 4.0, 5.0),
    newTriangle(1.0, 2.0, 10.0)  # 不能形成三角形的例子
  ]
  
  for shape in shapes:
    echo description(shape)
    echo "面積: ", area(shape)
    echo "周長: ", perimeter(shape)
    echo "------------------------"

# 執行測試
testShapes()
```


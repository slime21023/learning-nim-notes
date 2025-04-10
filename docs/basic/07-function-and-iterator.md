# 進階函式與迭代器

## 學習目標
- 掌握函式重載的概念和具體應用場景
- 學會創建和使用自定義運算子，增強代碼表現力
- 理解迭代器的工作原理和效能優勢
- 掌握閉包和匿名函式的使用技巧
- 學習如何應用高階函式提高代碼複用性

## 先決條件
- 已完成基本函式和控制流程學習
- 理解泛型和 Nim 型別系統
- 熟悉基本的迭代和集合處理概念
- 掌握基本的物件導向編程概念

## 關鍵概念
1. 函式進階特性
   - 函式重載與多態性
   - 運算子重載與自定義
   - 閉包和變數捕獲
   - 匿名函式與 lambda 表達式
   
2. 迭代器基礎與進階
   - 迭代器定義與類型
   - yield 語句與狀態保持
   - 閉包迭代器與內部狀態
   - 迭代器效率與懶惰求值
   
3. 高階函式編程
   - 函式作為參數傳遞
   - 函式作為返回值
   - 函式組合與鏈式調用
   - 柯里化與部分應用

## 函式進階特性

### 函式重載

函式重載允許多個同名函式根據參數類型或數量區分：

```nim
# 基於參數類型的重載
proc process(x: int) = echo "處理整數：", x
proc process(x: string) = echo "處理字串：", x
proc process(x: seq[int]) = echo "處理整數序列，長度：", x.len

process(42)       # 輸出: 處理整數：42
process("hello")  # 輸出: 處理字串：hello
process(@[1, 2, 3])  # 輸出: 處理整數序列，長度：3

# 基於參數數量的重載
proc sum(a, b: int): int = a + b
proc sum(a, b, c: int): int = a + b + c

echo sum(10, 20)      # 輸出: 30
echo sum(10, 20, 30)  # 輸出: 60
```

編譯器基於參數類型和數量選擇最匹配的函式，讓代碼更符合直覺：

```nim
# 結合預設參數和重載
proc connect(host: string, port: int = 80) = 
  echo "連接到 ", host, ":", port

proc connect(address: tuple[host: string, port: int]) =
  echo "連接到 ", address.host, ":", address.port

connect("localhost")        # 輸出: 連接到 localhost:80
connect("example.com", 443) # 輸出: 連接到 example.com:443
connect(("api.service.com", 8080)) # 輸出: 連接到 api.service.com:8080
```

### 自定義運算子

Nim 允許自定義或重載運算子，使代碼更易讀且更符合領域特性：

```nim
# 定義新的運算子
proc `++`(a, b: int): int = a * 2 + b
proc `=~`(a, b: float): bool = abs(a - b) < 0.001

echo 5 ++ 3       # 輸出：13
echo 1.0 =~ 1.0001  # 輸出：true

# 為自定義類型重載運算子
type
  Vector = object
    x, y: float

proc `+`(a, b: Vector): Vector =
  result.x = a.x + b.x
  result.y = a.y + b.y

proc `*`(v: Vector, scalar: float): Vector =
  result.x = v.x * scalar
  result.y = v.y * scalar

proc `$`(v: Vector): string =
  "Vector(" & $v.x & ", " & $v.y & ")"

let v1 = Vector(x: 1.0, y: 2.0)
let v2 = Vector(x: 3.0, y: 4.0)
let v3 = v1 + v2
let v4 = v1 * 2.5

echo v3  # 輸出: Vector(4.0, 6.0)
echo v4  # 輸出: Vector(2.5, 5.0)
```

#### 運算子優先級

自定義運算子遵循 Nim 的優先級規則，可以通過括號明確指定運算順序：

```nim
# 運算子優先級示例
proc `^^`(a, b: int): int = a ^ b  # 指數運算

echo 2 + 3 * 4     # 輸出: 14 (乘法優先)
echo 2 ^^ 3 + 4    # 輸出: 12 (^^ 優先於 +)
echo (2 + 3) * 4   # 輸出: 20 (括號優先)
```

### 閉包和變數捕獲

閉包是能夠捕獲外部變數的函式，在 Nim 中創建閉包非常直觀：

```nim
proc makeCounter(): proc(): int =
  var count = 0
  result = proc(): int =
    inc count
    return count

let counter1 = makeCounter()
let counter2 = makeCounter()

echo counter1()  # 輸出: 1
echo counter1()  # 輸出: 2
echo counter2()  # 輸出: 1 (獨立計數器)
echo counter1()  # 輸出: 3
```

閉包可用於創建維護狀態的函式，非常適合事件處理和狀態管理：

```nim
proc createLogger(prefix: string): proc(msg: string) =
  return proc(msg: string) =
    echo prefix, ": ", msg

let systemLog = createLogger("[系統]")
let userLog = createLogger("[用戶]")

systemLog("系統初始化")  # 輸出: [系統]: 系統初始化
userLog("用戶登入")      # 輸出: [用戶]: 用戶登入
```

### 匿名函式

匿名函式允許在需要時創建簡短的函式，不必預先定義：

```nim
# 使用 proc 定義匿名函式
let double = proc(x: int): int = x * 2

# 使用函式字面量創建匿名函式
let triple = (x: int) => x * 3

echo double(10)  # 輸出: 20
echo triple(10)  # 輸出: 30

# 在高階函式中使用匿名函式
let numbers = @[1, 2, 3, 4, 5]
let squared = numbers.map(proc(x: int): int = x * x)
let cubed = numbers.map((x: int) => x * x * x)

echo squared  # 輸出: @[1, 4, 9, 16, 25]
echo cubed    # 輸出: @[1, 8, 27, 64, 125]
```

## 迭代器

### 迭代器基礎

迭代器是 Nim 中生成一系列值的特殊函式，使用 `yield` 而非 `return`：

```nim
# 基本迭代器
iterator countTo(n: int): int =
  for i in 1..n:
    yield i

for i in countTo(5):
  echo i  # 輸出: 1 2 3 4 5

# 使用迭代器處理數據集合
iterator tokenize(s: string): string =
  var token = ""
  for c in s:
    if c == ' ':
      if token.len > 0:
        yield token
        token = ""
    else:
      token.add(c)
  if token.len > 0:
    yield token

for word in tokenize("hello world nim programming"):
  echo word
# 輸出:
# hello
# world
# nim
# programming
```

### 迭代器與懶惰求值

迭代器具有懶惰求值特性，只在需要時生成值，節省記憶體：

```nim
# 懶惰求值示例
iterator infiniteSequence(): int =
  var i = 0
  while true:
    yield i
    inc i

# 只處理前5個元素
var count = 0
for x in infiniteSequence():
  if count >= 5: break
  echo x
  inc count
```

### 閉包迭代器

閉包迭代器結合了閉包和迭代器的特性，可以保存狀態並在多次調用之間保持：

```nim
# 閉包迭代器需要標記為 {.closure.}
iterator fibonacci(): int {.closure.} =
  var a = 0
  var b = 1
  while true:
    yield a
    let next = a + b
    a = b
    b = next

proc printFirstN(n: int) =
  var count = 0
  for num in fibonacci():
    if count >= n: break
    echo num
    inc count

printFirstN(10)  # 輸出斐波那契數列前10個數
```

#### 閉包迭代器 vs 普通迭代器

普通迭代器與閉包迭代器的主要區別：

```nim
# 普通迭代器 - 不能傳遞給變數
iterator count(n: int): int =
  for i in 1..n:
    yield i

# 以下代碼不能編譯
# let myIter = count  # 錯誤!

# 閉包迭代器 - 可以傳遞給變數
iterator count2(n: int): int {.closure.} =
  for i in 1..n:
    yield i

# 可以儲存和傳遞
let myIter = count2(5)

for i in myIter():
  echo i
```

### 兩種迭代器的區別

- **返回方式**：  
    - 函式使用 `return` 返回單一值或結構。  
    - 迭代器使用 `yield` 返回多個值，並且可以逐步生成。
    
- **效率與內存使用**：
    - 函式一次性生成所有結果。
    - 迭代器按需生成結果，更節省內存。

```nim
# 函式實現 - 返回完整列表
proc fibonacciList(n: int): seq[int] =
  result = @[]
  var a, b = 0, 1
  for i in 0..<n:
    result.add(a)
    (a, b) = (b, a + b)

# 迭代器實現 - 按需生成
iterator fibonacciIter(n: int): int =
  var a, b = 0, 1
  for i in 0..<n:
    yield a
    (a, b) = (b, a + b)

# 比較兩種方式
echo "使用函式 (一次性生成全部):"
let fibList = fibonacciList(10)
echo fibList

echo "使用迭代器 (按需生成):"
for num in fibonacciIter(10):
  echo num
```

## 高階函式

高階函式是參數或返回值為函式的函式，用於提高代碼靈活性和復用性：

### 函式作為參數

```nim
# 將函式作為參數傳遞
proc applyToEach[T](data: seq[T], operation: proc(x: T): T): seq[T] =
  result = newSeq[T](data.len)
  for i, item in data:
    result[i] = operation(item)

# 使用普通函式
proc double(x: int): int = x * 2

# 使用匿名函式
let numbers = @[1, 2, 3, 4, 5]
let doubled = applyToEach(numbers, double)
let squared = applyToEach(numbers, proc(x: int): int = x * x)

echo doubled  # 輸出: @[2, 4, 6, 8, 10]
echo squared  # 輸出: @[1, 4, 9, 16, 25]
```

### 函式作為返回值

```nim
# 返回函式的高階函式
proc multiply(factor: int): proc(x: int): int =
  return proc(x: int): int = x * factor

let double = multiply(2)
let triple = multiply(3)

echo double(10)  # 輸出: 20
echo triple(10)  # 輸出: 30
```

### 柯里化與部分應用

柯里化是將接受多個參數的函式轉換為一系列只接受一個參數的函式：

```nim
# 手動柯里化
proc add(a: int): proc(b: int): int =
  return proc(b: int): int = a + b

let add5 = add(5)
echo add5(10)  # 輸出: 15

# 部分應用
proc fullName(firstName, lastName: string): string =
  return firstName & " " & lastName

proc partialApply[T, U, V](
  fn: proc(a: T, b: U): V, 
  firstArg: T
): proc(b: U): V =
  return proc(b: U): V = fn(firstArg, b)

let greet = partialApply(fullName, "John")
echo greet("Doe")  # 輸出: John Doe
```

### 函式組合

組合多個函式創建新函式，是函式式編程的核心概念：

```nim
# 函式組合
proc compose[A, B, C](f: proc(x: B): C, g: proc(x: A): B): proc(x: A): C =
  result = proc(x: A): C = f(g(x))

proc double(x: int): int = x * 2
proc addOne(x: int): int = x + 1

let doubleAndAddOne = compose(addOne, double)
let addOneAndDouble = compose(double, addOne)

echo doubleAndAddOne(5)  # 輸出: 11 (5*2+1)
echo addOneAndDouble(5)  # 輸出: 12 ((5+1)*2)
```

## 實戰示例

### 示例 1：實現通用比較器與排序

```nim
type
  Comparator[T] = proc(a, b: T): int
  Person = object
    name: string
    age: int

# 默認比較函式
proc defaultCompare[T](a, b: T): int =
  if a < b: -1
  elif a > b: 1
  else: 0

# 通用排序算法
proc sortBy[T](arr: var seq[T], cmp: Comparator[T] = defaultCompare[T]) =
  for i in 0..arr.high:
    for j in i+1..arr.high:
      if cmp(arr[i], arr[j]) > 0:
        swap(arr[i], arr[j])

# 為 Person 物件定義比較器
proc compareByName(a, b: Person): int =
  cmp(a.name, b.name)

proc compareByAge(a, b: Person): int =
  cmp(a.age, b.age)

# 使用示例
var people = @[
  Person(name: "Alice", age: 30),
  Person(name: "Bob", age: 25),
  Person(name: "Charlie", age: 35)
]

# 按名字排序
people.sortBy(compareByName)
echo "按名字排序："
for p in people:
  echo p.name, " (", p.age, ")"

# 按年齡排序
people.sortBy(compareByAge)
echo "按年齡排序："
for p in people:
  echo p.name, " (", p.age, ")"
```

### 示例 2：惰性計算的數列生成器

```nim
# 惰性素數生成器
iterator primes(): int {.closure.} =
  yield 2  # 首個素數
  var candidates = @[2]
  var n = 3
  
  while true:
    block checkPrime:
      for p in candidates:
        if p * p > n:  # 只需檢查到 sqrt(n)
          break
        if n mod p == 0:
          break checkPrime
      
      yield n
      candidates.add(n)
    
    n += 2  # 只檢查奇數
    
# 使用惰性生成器
proc printFirstNPrimes(n: int) =
  echo "首", n, "個素數："
  var count = 0
  for prime in primes():
    if count >= n: break
    echo prime
    inc count

printFirstNPrimes(10)
```

### 示例 3：函式式數據處理管道

```nim
# 實現簡單的函式式處理管道
type
  Pipeline[T] = object
    data: seq[T]

proc newPipeline[T](data: seq[T]): Pipeline[T] =
  result.data = data

proc map[T, U](self: Pipeline[T], f: proc(x: T): U): Pipeline[U] =
  var result = newSeq[U](self.data.len)
  for i, x in self.data:
    result[i] = f(x)
  return newPipeline(result)

proc filter[T](self: Pipeline[T], predicate: proc(x: T): bool): Pipeline[T] =
  var result: seq[T] = @[]
  for x in self.data:
    if predicate(x):
      result.add(x)
  return newPipeline(result)

proc reduce[T, U](self: Pipeline[T], initial: U, reducer: proc(acc: U, x: T): U): U =
  result = initial
  for x in self.data:
    result = reducer(result, x)

# 使用示例
let numbers = @[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let pipeline = newPipeline(numbers)

# 創建處理管道: 過濾偶數 -> 平方 -> 求和
let sum = pipeline
  .filter(proc(x: int): bool = x mod 2 == 0)
  .map(proc(x: int): int = x * x)
  .reduce(0, proc(acc, x: int): int = acc + x)

echo "偶數平方和: ", sum  # 輸出: 220 (4+16+36+64+100)
```

## 最佳實踐

### 函式設計指南

1. **函式重載建議**：
   - 保持重載函式語義一致，完成相似功能
   - 確保參數類型有明顯區別，避免模糊
   - 重載函式宜簡潔明瞭，避免過度
   - 為複雜的重載提供清晰文檔

2. **運算子重載建議**：
   - 保持運算子語義直觀，符合常規期望
   - 避免違反常規使用習慣和直覺
   - 為自定義類型設計運算子時考慮封閉性和結合律
   - 較複雜的操作應使用函式而非運算子

3. **高階函式使用建議**：
   - 優先使用庫函式進行通用操作
   - 靈活使用匿名函式簡化代碼
   - 避免過度嵌套增加複雜度
   - 使用有意義的函式名提高可讀性

### 迭代器最佳實踐

1. **迭代器效率**：
   - 使用迭代器處理大數據集或無限序列
   - 避免在迭代器內部分配不必要的記憶體
   - 重複使用的迭代器可考慮使用閉包迭代器

2. **迭代器設計**：
   - 清楚標記迭代器用途和終止條件
   - 必要時提供默認參數和多種接口
   - 創建閉包迭代器時注意記憶體使用

3. **異常安全**：
   - 確保迭代器能正確處理或傳播異常
   - 考慮資源獲取與釋放的邏輯
   - 避免在迭代器中捕獲所有異常後靜默失敗

## 小測驗

1. 在下面的代碼中，函式 `process` 的哪個重載版本會被調用？
   ```nim
   proc process(x: int) = echo "整數: ", x
   proc process(x: float) = echo "浮點數: ", x
   proc process[T](x: seq[T]) = echo "序列: ", x
   
   let val = 42
   process(val)
   ```

2. 以下閉包迭代器的輸出結果是什麼？
   ```nim
   iterator count(): int {.closure.} =
     var i = 0
     while true:
       yield i
       i += 2
   
   var c = 0
   for i in count():
     if i > 10: break
     echo i
     inc c
   ```

3. 請描述 Nim 中函式重載和泛型函式的主要區別。

4. 在高階函式中，如下代碼創建的 `multiplyBy5` 會捕獲哪些變數？
   ```nim
   proc createMultiplier(factor: int): proc(x: int): int =
     var counter = 0
     return proc(x: int): int =
       inc counter
       echo "Called ", counter, " times"
       return x * factor
   
   let multiplyBy5 = createMultiplier(5)
   ```

5. 以下函式組合的結果是什麼？
   ```nim
   proc inc(x: int): int = x + 1
   proc square(x: int): int = x * x
   
   proc compose[A, B, C](f: proc(x: B): C, g: proc(x: A): B): proc(x: A): C =
     return proc(x: A): C = f(g(x))
   
   let squareAndInc = compose(inc, square)
   echo squareAndInc(3)
   ```

## 進一步閱讀
- [Nim 官方文檔：Procedures](https://nim-lang.org/docs/manual.html#procedures)
- [Nim 官方文檔：Iterators](https://nim-lang.org/docs/manual.html#iterators)
- [Nim 官方文檔：First Class Functions](https://nim-lang.org/docs/manual.html#procedures-first-class-functions)
- [Nim 官方文檔：Closures](https://nim-lang.org/docs/manual.html#procedures-closures)
- [Nim 函式式編程最佳實踐](https://nim-lang.org/docs/tut2.html#advanced-types-and-macros)

---

下一章將深入探討 Nim 的型別系統，包括進階型別特性、泛型、類型安全與型別推導等內容。
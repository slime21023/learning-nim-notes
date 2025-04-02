# 物件導向程式設計 (OOP)  

Nim 是一種多範式程式語言，支援物件導向程式設計（OOP），但其設計哲學更鼓勵靈活性和簡潔性。Nim 的 OOP 特性包括繼承、動態分派、方法呼叫、屬性管理以及物件變體等。
---

## 繼承與類型系統

### 繼承的基本概念  
在 Nim 中，物件可以通過繼承來建立層次結構。所有需要支援繼承的物件必須繼承自 `RootObj`，並使用 `ref` 型別來確保物件的完整性。

**語法：**
```nim
type
  Parent = ref object of RootObj
    field1: string
    field2: int

  Child = ref object of Parent
    field3: bool
```

### 使用 `of` 進行類型檢查  
Nim 提供 `of` 運算子來檢查物件是否屬於某個類型。

**範例：**
```nim
type
  Animal = ref object of RootObj
    name: string

  Dog = ref object of Animal
    breed: string

let pet: Animal = Dog(name: "Buddy", breed: "Golden Retriever")

if pet of Dog:
  echo "This is a dog!"
```

### 注意事項：使用 `ref` 型別  
若物件未使用 `ref`，子類型的字段可能會被截斷。因此，建議使用 `ref` 型別來避免此問題。

**範例：**
```nim
type
  Animal = object
    name: string

  Dog = object of Animal
    breed: string

let myDog: Dog = Dog(name: "Buddy", breed: "Golden Retriever")
echo myDog.name  # 正常
# echo myDog.breed  # 編譯錯誤，字段被截斷
```

---

### 方法呼叫與屬性

#### 方法呼叫語法  
Nim 支援物件方法，使用 `obj.methodName(args)` 語法呼叫，而非傳統的 `methodName(obj, args)`。

**範例：**
```nim
type
  Car = ref object of RootObj
    brand: string

proc drive(c: Car) =
  echo "Driving a ", c.brand

let myCar = Car(brand: "Tesla")
myCar.drive()  # 輸出: Driving a Tesla
```

#### 屬性管理  
屬性可以通過 Getter 和 Setter 方法進行存取。Getter 使用普通函數，Setter 使用特殊語法 `host=`。

**範例：**
```nim
type
  Socket = ref object of RootObj
    _host: string

proc host(s: Socket): string = s._host  # Getter
proc host=(s: Socket, value: string) =  # Setter
  s._host = value

let s = Socket(_host: "localhost")
echo s.host  # 輸出: localhost

s.host = "127.0.0.1"
echo s.host  # 輸出: 127.0.0.1
```

---

### 動態分派與多方法

#### 動態分派  
Nim 使用 `method` 關鍵字支援動態分派，允許根據物件的實際類型選擇方法。

**範例：**
```nim
type
  Expression = ref object of RootObj

  Literal = ref object of Expression
    value: string

method eval(e: Expression): string =
  result = "Unknown expression"

method eval(l: Literal): string =
  result = "Literal value: " & l.value

let expr: Expression = Literal(value: "42")
echo expr.eval()  # 輸出: Literal value: 42
```

---

### 物件變體與相互遞迴類型

#### 物件變體  
物件變體是一種用於表達多種可能型態的結構，常用於簡單的變體類型。

**範例：**
```nim
type
  NodeKind = enum
    nkInt, nkFloat

  Node = object
    case kind: NodeKind
    of nkInt: intVal: int
    of nkFloat: floatVal: float

let intNode: Node = Node(kind: nkInt, intVal: 42)
let floatNode: Node = Node(kind: nkFloat, floatVal: 3.14)

echo intNode.intVal   # 輸出: 42
echo floatNode.floatVal  # 輸出: 3.14
```

#### 相互遞迴類型  
相互遞迴類型允許物件、元組和引用相互依賴。這些類型必須在單一 `type` 區塊中声明。

**範例：**
```nim
type
  LinkedNode = ref object
    value: int
    next: LinkedNode

let node1 = LinkedNode(value: 1, next: nil)
let node2 = LinkedNode(value: 2, next: node1)

echo node2.next.value  # 輸出: 1
```

---

### 總結

Nim 的物件導向特性結合了靈活性和高效性，提供了以下功能：

1. **繼承與類型系統**：支援使用 `RootObj` 建立層次結構，並建議使用 `ref` 型別。
2. **方法呼叫與屬性**：支援物件方法與屬性存取。
3. **動態分派與多方法**：提供基於物件類型的動態方法選擇。
4. **物件變體與遞迴類型**：適合表達多種型態與相互依賴的結構。

透過這些特性，開發者可以靈活地設計物件導向程式，同時保持效能和可維護性。
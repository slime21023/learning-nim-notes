# 練習 4: 迴圈與迭代器實作

## Q1: 計算數列總和
```nim
# 目標：實作不同方式計算數列總和
# 要求：
# 1. 使用 for 迴圈計算 1 到 n 的總和
# 2. 使用 while 迴圈計算 1 到 n 的總和
# 3. 實作一個迭代器 countTo，並用它計算總和

import strformat

# 使用 for 迴圈計算總和
proc sumWithFor(n: int): int =
  # 在此實作使用 for 迴圈計算 1 到 n 的總和
  # TODO: 你的程式碼

# 使用 while 迴圈計算總和
proc sumWithWhile(n: int): int =
  # 在此實作使用 while 迴圈計算 1 到 n 的總和
  # TODO: 你的程式碼

# 實作一個迭代器 countTo
iterator countTo(n: int): int =
  # 在此實作一個迭代器，產生 1 到 n 的數字
  # TODO: 你的程式碼

# 使用迭代器計算總和
proc sumWithIterator(n: int): int =
  # 在此使用你的 countTo 迭代器計算總和
  # TODO: 你的程式碼

# 測試你的實作
proc testSumCalculations() =
  let n = 100
  echo fmt"計算 1 到 {n} 的總和:"
  echo fmt"  For 迴圈結果: {sumWithFor(n)}"
  echo fmt"  While 迴圈結果: {sumWithWhile(n)}"
  echo fmt"  迭代器結果: {sumWithIterator(n)}"
  
  # 檢查結果是否正確
  let expectedSum = n * (n + 1) div 2
  echo fmt"  預期結果: {expectedSum}"

# 執行測試
testSumCalculations()
```

## Q2: 費波那契數列迭代器
```nim
# 目標：實作費波那契數列迭代器
# 要求：
# 1. 實作一個迭代器 fibonacci(n)，產生前 n 個費波那契數
# 2. 實作一個無限迭代器 fibonacciInfinite()，可以無限產生費波那契數
# 3. 使用這兩個迭代器列出費波那契數列

import strformat

# 實作有限費波那契數列迭代器
iterator fibonacci(n: int): int =
  # 在此實作產生前 n 個費波那契數的迭代器
  # TODO: 你的程式碼

# 實作無限費波那契數列迭代器
iterator fibonacciInfinite(): int =
  # 在此實作可以無限產生費波那契數的迭代器
  # TODO: 你的程式碼

# 測試費波那契迭代器
proc testFibonacciIterators() =
  echo "費波那契數列 (前10個數):"
  var i = 0
  for num in fibonacci(10):
    echo fmt"  F({i}): {num}"
    inc(i)
  
  echo "\n使用無限迭代器取得前15個費波那契數:"
  i = 0
  var count = 0
  for num in fibonacciInfinite():
    echo fmt"  F({i}): {num}"
    inc(i)
    inc(count)
    if count >= 15: break

# 執行測試
testFibonacciIterators()
```

## Q3: 質數生成器
```nim
# 目標：實作質數生成器
# 要求：
# 1. 實作一個函數 isPrime 檢查數字是否為質數
# 2. 使用 for 迴圈找出範圍內的所有質數
# 3. 實作一個迭代器 primes(limit)，產生小於等於 limit 的所有質數
# 4. 實作一個無限迭代器 primesInfinite()，可以無限產生質數

# 檢查數字是否為質數
proc isPrime(n: int): bool =
  # 在此實作檢查 n 是否為質數的函數
  # TODO: 你的程式碼

# 使用 for 迴圈找出範圍內的質數
proc findPrimesWithFor(limit: int): seq[int] =
  # 在此實作使用 for 迴圈找出小於等於 limit 的所有質數
  # TODO: 你的程式碼

# 實作質數迭代器
iterator primes(limit: int): int =
  # 在此實作產生小於等於 limit 的所有質數的迭代器
  # TODO: 你的程式碼

# 實作無限質數迭代器
iterator primesInfinite(): int =
  # 在此實作可以無限產生質數的迭代器
  # TODO: 你的程式碼

# 測試質數生成
proc testPrimeGenerators() =
  let limit = 50
  
  echo "使用 for 迴圈找出的質數:"
  let primesFor = findPrimesWithFor(limit)
  echo "  ", primesFor
  
  echo "\n使用迭代器找出的質數:"
  var primesList: seq[int] = @[]
  for p in primes(limit):
    primesList.add(p)
  echo "  ", primesList
  
  echo "\n使用無限迭代器取得前10個質數:"
  var count = 0
  for p in primesInfinite():
    stdout.write(p, " ")
    inc(count)
    if count >= 10: break
  echo ""

# 執行測試
testPrimeGenerators()
```

## Q4: 組合生成器
```nim
# 目標：實作組合生成器
# 要求：
# 1. 實作一個迭代器 combinations(elements, k)，產生所有可能的 k 個元素組合
# 2. 測試你的迭代器，列出所有可能的組合

# 實作組合生成器迭代器
iterator combinations[T](elements: seq[T], k: int): seq[T] =
  # 在此實作產生所有可能的 k 個元素組合的迭代器
  # 提示：可以使用遞迴或迭代的方式實作
  # TODO: 你的程式碼

# 測試組合生成器
proc testCombinationsGenerator() =
  let nums = @[1, 2, 3, 4, 5]
  let k = 3
  
  echo "生成所有可能的 ", k, " 個元素組合:"
  var comboCount = 0
  for combo in combinations(nums, k):
    echo "  組合 #", comboCount + 1, ": ", combo
    inc(comboCount)
  
  echo "總共生成了 ", comboCount, " 個組合"

# 執行測試
testCombinationsGenerator()
```

## Q5: 有狀態的閉包迭代器
```nim
# 目標：實作有狀態的閉包迭代器
# 要求：
# 1. 實作一個函數 makeCounter，返回一個可以計數的迭代器
# 2. 迭代器應該記住上次的計數值，並在每次呼叫時遞增
# 3. 實作一個函數 makeRangeIterator，返回一個在指定範圍內計數的迭代器

# 實作計數器閉包
proc makeCounter(start: int = 0, step: int = 1): iterator(): int =
  # 在此實作一個閉包迭代器，從 start 開始，每次增加 step
  # TODO: 你的程式碼

# 實作範圍迭代器閉包
proc makeRangeIterator(start: int, endValue: int, step: int = 1): iterator(): (bool, int) =
  # 在此實作一個閉包迭代器，在 start 和 endValue 之間計數
  # 返回一個元組 (bool, int)，其中 bool 表示是否還有下一個值，int 是當前值
  # TODO: 你的程式碼

# 測試閉包迭代器
proc testClosureIterators() =
  echo "測試計數器閉包:"
  let counter1 = makeCounter(10, 5)
  for i in 0..5:
    echo "  計數器1: ", counter1()
  
  echo "\n測試另一個計數器閉包:"
  let counter2 = makeCounter(100, -10)
  for i in 0..5:
    echo "  計數器2: ", counter2()
  
  echo "\n測試範圍迭代器:"
  let rangeIter = makeRangeIterator(1, 10)
  var hasMore = true
  while hasMore:
    let (more, val) = rangeIter()
    hasMore = more
    if more:
      echo "  範圍值: ", val

# 執行測試
testClosureIterators()
```

這些練習題涵蓋了 Nim 中迴圈與迭代器的各種實作方式，從基本的 for 和 while 迴圈，到自定義迭代器和閉包迭代器。每個練習都包含了明確的目標和要求，並提供了測試函數來驗證你的實作是否正確。

這些練習將幫助你掌握 Nim 中的迭代技術，讓你能夠更有效地處理序列和集合數據。
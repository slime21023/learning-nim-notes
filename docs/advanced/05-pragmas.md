# 編譯器指令 (Pragmas)

編譯器指令（Pragmas）是 Nim 提供的一種機制，用於向編譯器傳遞額外的信息或指示。它可以用來修改編譯行為、優化性能、控制代碼生成等。Pragmas 的使用方式非常靈活，適合各種場景。

---

## 語法  
編譯器指令的語法使用 `{.` 和 `.}` 包圍，並且可以直接附加在變數、類型、函數或其他語法結構上。  
基本格式如下：
```nim
{. pragma_name: value .}
```

若不需要指定值，則僅使用 pragma 名稱即可：
```nim
{. pragma_name .}
```

---

### 範例  

**`inline` 指令** :告訴編譯器將函數內聯（inline），以提高性能。
```nim
proc add(a, b: int): int {.inline.} =
    result = a + b

echo add(3, 5)  # 輸出: 8
```
使用 `{.inline.}` 指令後，編譯器會在調用 `add` 函數時直接將其展開為代碼，避免函數調用的額外開銷。

**`deprecated` 指令**: 標記某個函數或變數為過時，提醒開發者避免使用。
```nim
proc oldFunc() {.deprecated.} =
    echo "這是過時的函數"

oldFunc()  # 編譯時會提示警告：此函數已過時
```

**`experimental` 指令**: 標記某個功能或模組為實驗性，告知使用者其穩定性可能不足。
```nim
{.experimental.}
proc testExperimental() =
    echo "這是實驗性功能"

testExperimental()
```

**`warning` 指令**: 在編譯時顯示自定義警告訊息。
```nim
proc riskyOperation() {.warning[使用此函數需謹慎].} =
    echo "執行高風險操作"

riskyOperation()  # 編譯時顯示警告：使用此函數需謹慎
```

**`noSideEffect` 指令**: 告訴編譯器該函數沒有副作用（不改變外部狀態），以便進行更激進的優化。
```nim
proc pureCalculation(a, b: int): int {.noSideEffect.} =
    result = a * b

echo pureCalculation(3, 4)  # 輸出: 12
```

---

### 參考  
更多 Pragmas 的詳細內容與使用方式，請參考以下官方文檔：  

- [Nim 手冊 - Pragmas](https://nim-lang.org/docs/manual.html#pragmas)  
- [Nim 用戶指南 - 編譯器額外功能](https://nim-lang.org/docs/nimc.html#additional-features)

Pragmas 是 Nim 語言中非常重要的一部分，善用 Pragmas 能幫助開發者更好地控制編譯行為，提升代碼的性能與可讀性。
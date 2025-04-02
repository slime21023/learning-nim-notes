# 進階控制與語句

Nim 提供靈活的控制語句，幫助開發者在不同情境下精確地控制程式邏輯。以下介紹 **Scopes 與 Block** 的應用，以及條件編譯相關的 **When 語句**。

---

## Scopes 與 Block

### 作用域與區塊語句的應用

在 Nim 中，作用域 (Scope) 是指變數的存活範圍。區塊語句 (Block) 是一個邏輯單元，通常用於封裝程式碼以便控制作用域或提升可讀性。

範例：使用 `block` 明確定義作用域

```nim
var x = 10
block myBlock:
  var x = 20  # 在區塊內重新定義 x
  echo x      # 輸出：20
echo x        # 區塊外的 x，輸出：10
```

`block` 創建了局部作用域，所有在區塊內定義的變數僅在區塊內有效。

---

## `break` 與 `continue`：迴圈控制

Nim 支援 `break` 和 `continue` 語句，用於控制迴圈的執行流程。

- **`break`**: `break` 用於立即跳出迴圈。

```nim
for i in 1..10:
  if i == 5:
    break
  echo i  # 輸出：1, 2, 3, 4
```

- **`continue`**: `continue` 用於跳過本次迴圈的剩餘程式碼，並進入下一次迴圈。

```nim
for i in 1..5:
  if i == 3:
    continue
  echo i  # 輸出：1, 2, 4, 5
```

使用 `break` 和 `continue` 可以更靈活地控制迴圈的執行邏輯。

---

## **When 語句**

### **條件編譯與平台特定程式碼**

`when` 是 Nim 的條件編譯語句，用於根據編譯時的條件選擇執行的程式碼。這在寫跨平台程式或需支援特定功能時非常有用。

- **基本用法**：`when` 可用於檢查條件並執行特定程式碼。

```nim
when true:
  echo "This will always run"
```

- **平台特定程式碼**：使用 `when` 判斷運行平台，執行平台相關的程式碼。

```nim
when defined(windows):
  echo "Running on Windows"
elif defined(linux):
  echo "Running on Linux"
else:
  echo "Other platforms"
```

`defined(<platform>)` 用於檢查是否在特定平台上編譯程式。

### **條件編譯的應用場景**

- **跨平台程式設計**：根據運行環境執行不同程式碼，確保程式兼容多個平台。
- **功能切換**：根據編譯條件啟用或禁用某些功能。
- **優化編譯**：根據硬體特性或編譯器選項，生成最佳化程式碼。



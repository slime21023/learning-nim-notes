# 第1天：命令列計算器

## 專案目標

構建一個簡單但功能完整的命令列計算器，能夠處理基本的數學運算。

## 專案需求

1. 支援基本算術運算：加法(+)、減法(-)、乘法(*)、除法(/)和取餘(%)
2. 接受使用者輸入的表達式（如「5 + 3」、「10 * 2」等）
3. 顯示計算結果和適當的錯誤訊息
4. 支援退出程式的命令（如「exit」或「quit」）
5. 提供清晰的使用說明

## 實現提示

### 基本結構
```nim
# calculator.nim
import strutils, tables

proc calculate(expression: string): float =
  # 實現計算邏輯
  
proc main() =
  echo "簡易計算器 (輸入 'quit' 退出)"
  while true:
    # 獲取使用者輸入
    # 處理輸入
    # 顯示結果

when isMainModule:
  main()
```

### 解析表達式
- 使用`split()`函數分割輸入字串
- 檢查運算符是否有效
- 將字串轉換為數字（使用`parseFloat()`）

### 錯誤處理
- 處理除零錯誤
- 處理無效輸入
- 使用try-except區塊捕獲異常

### Nim特性
- 字串處理
- 函數定義和呼叫
- 基本控制流
- 異常處理

## 進階挑戰

1. 支援複雜表達式（帶括號）
2. 添加更多數學函數（sin, cos, sqrt等）
3. 支援變數儲存（如「x = 5」，然後使用x進行計算）
4. 實現表達式歷史記錄功能
5. 添加彩色輸出以增強使用者體驗

## 專案結構

```
day01/
├── calculator.nim    # 主程式檔案
└── README.md         # 專案說明
```

## 學習資源

- [Nim官方文件：基礎語法](https://nim-lang.org/docs/manual.html)
- [Nim標準庫：strutils](https://nim-lang.org/docs/strutils.html)
- [Nim標準庫：tables](https://nim-lang.org/docs/tables.html)

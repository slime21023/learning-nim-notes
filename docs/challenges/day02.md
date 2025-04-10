# 第2天：猜數字遊戲

## 專案目標

開發一個互動式的猜數字遊戲，讓使用者嘗試猜測電腦隨機生成的數字。

## 專案需求

1. 在遊戲開始時，程式隨機生成一個1到100之間的數字
2. 使用者可以輸入猜測的數字
3. 程式提供「太高」或「太低」的提示
4. 當使用者猜中數字時，顯示恭喜訊息和猜測次數
5. 在一局結束後，詢問使用者是否要再玩一次
6. 提供退出遊戲的選項

## 實現提示

### 基本結構
```nim
# guess_number.nim
import random, strutils

proc playGame() =
  # 生成隨機數字
  # 初始化猜測次數
  # 主遊戲循環

proc main() =
  randomize()  # 初始化隨機種子
  echo "歡迎來到猜數字遊戲！"
  var playAgain = true
  while playAgain:
    playGame()
    # 詢問是否再玩一次

when isMainModule:
  main()
```

### 隨機數生成
- 使用`randomize()`初始化隨機種子
- 使用`rand(1..100)`生成1到100之間的隨機數

### 使用者輸入處理
- 使用`readLine(stdin)`獲取使用者輸入
- 使用`parseInt()`將字串轉換為整數
- 使用`try-except`處理無效輸入

### 遊戲邏輯
- 比較猜測值與目標數值
- 提供適當的提示
- 追蹤猜測次數
- 處理遊戲結束和重新開始的邏輯

## 進階挑戰

1. 允許使用者選擇難度等級（改變數字範圍）
2. 設定最大猜測次數限制
3. 實現計時功能，記錄使用者猜中數字的時間
4. 儲存並顯示最高分（最少猜測次數）
5. 加入音效或更豐富的視覺效果

## 專案結構

```
day02/
├── guess_number.nim   # 主程式檔案 
└── README.md          # 專案說明
```

## 學習資源

- [Nim標準庫：random](https://nim-lang.org/docs/random.html)
- [Nim標準庫：strutils](https://nim-lang.org/docs/strutils.html)
- [Nim控制流結構](https://nim-lang.org/docs/manual.html#statements-and-expressions)

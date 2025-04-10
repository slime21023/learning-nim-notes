# 第7天：終端倒數計時器

## 專案目標

開發一個功能豐富的終端倒數計時器和秒錶，能夠協助使用者追蹤時間並提供可視化的倒數效果。

## 專案需求

1. 倒數計時器功能：
   - 允許使用者輸入特定的時間（小時、分鐘、秒）
   - 提供可視化的倒數顯示
   - 在倒數結束時發出提醒
2. 秒錶功能：
   - 提供開始、暫停和重置功能
   - 顯示經過的時間
   - 可選顯示分段時間
3. 支援以下命令列參數：
   - 直接設置倒數時間
   - 指定提醒方式
4. 提供友好的終端用戶界面
5. 允許使用者在倒數或計時過程中進行控制

## 實現提示

### 基本結構
```nim
# timer.nim
import os, times, strutils, parseopt, terminal

type
  TimerMode = enum
    tmCountdown, tmStopwatch
  
  Timer = object
    mode: TimerMode
    startTime: Time
    pauseTime: Time
    duration: Duration
    isPaused: bool
    isRunning: bool

proc newCountdownTimer(hours, minutes, seconds: int): Timer =
  # 創建一個新的倒數計時器

proc newStopwatch(): Timer =
  # 創建一個新的秒錶

proc start(timer: var Timer) =
  # 開始計時

proc pause(timer: var Timer) =
  # 暫停計時

proc reset(timer: var Timer) =
  # 重置計時器

proc getElapsedTime(timer: Timer): Duration =
  # 獲取經過的時間

proc getRemainingTime(timer: Timer): Duration =
  # 獲取剩餘時間（針對倒數計時器）

proc displayTime(timer: Timer) =
  # 顯示當前時間（經過時間或剩餘時間）

proc renderProgressBar(percentage: float): string =
  # 渲染進度條

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### 時間處理
- 使用`times`模組處理時間和日期
- 使用`Duration`類型表示時間間隔
- 處理暫停和恢復的邏輯

### 終端界面
- 使用`terminal`模組控制終端輸出
- 使用ANSI轉義序列創建彩色輸出
- 使用`setCursorPos`創建動態更新的界面

### 輸入處理
- 使用`parseopt`處理命令列參數
- 實現非阻塞輸入以便在計時過程中接受命令

## 進階挑戰

1. 添加預設的定時器模板（如番茄鐘工作法）
2. 實現多個並行計時器
3. 添加自定義提醒聲音和通知方式
4. 實現計時器記錄保存功能
5. 創建更豐富的終端UI（如ASCII藝術時鐘）
6. 添加任務關聯功能（將計時器與任務關聯起來）

## 專案結構

```
day07/
├── timer.nim    # 主程式檔案
└── README.md    # 專案說明
```

## 學習資源

- [Nim標準庫：times](https://nim-lang.org/docs/times.html)
- [Nim標準庫：terminal](https://nim-lang.org/docs/terminal.html)
- [Nim非阻塞IO](https://nim-lang.org/docs/asyncdispatch.html)

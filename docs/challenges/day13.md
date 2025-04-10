# 第13天：終端迷你遊戲

## 專案目標

使用Nim開發一個基於字元的終端迷你遊戲，如貪吃蛇、俄羅斯方塊或其他經典遊戲。

## 專案需求

1. 在終端中實現完整可玩的遊戲
2. 使用字元圖形（ASCII藝術）作為遊戲界面
3. 實現以下基本功能：
   - 遊戲初始化和設置
   - 使用者輸入控制
   - 遊戲邏輯
   - 分數計算和顯示
   - 遊戲結束條件
4. 提供清晰的遊戲指示和控制說明
5. 合理的遊戲難度和進度設計
6. 支援終端視窗大小調整（可選）

## 實現提示

### 基本結構（以貪吃蛇為例）
```nim
# terminal_snake.nim
import terminal, os, random, times

type
  Direction = enum
    dUp, dDown, dLeft, dRight
  
  Point = tuple
    x, y: int
  
  Game = object
    width, height: int
    snake: seq[Point]
    food: Point
    direction: Direction
    score: int
    isGameOver: bool

proc initGame(width, height: int): Game =
  # 初始化遊戲狀態
  
proc processInput(game: var Game) =
  # 處理使用者輸入，更新移動方向

proc updateGame(game: var Game) =
  # 更新遊戲狀態（移動蛇，檢查碰撞，生成食物等）

proc renderGame(game: Game) =
  # 繪製遊戲畫面

proc main() =
  # 遊戲主循環
  randomize()
  
  var 
    game = initGame(30, 20)
    lastUpdate = cpuTime()
    updateInterval = 0.1  # 遊戲更新間隔（秒）
  
  # 設置終端
  hideCursor()
  # 主遊戲循環
  
  # 遊戲結束處理
  showCursor()

when isMainModule:
  main()
```

### 終端處理
- 使用`terminal`模組控制終端輸出
- 使用`setCursorPos`定位並更新特定位置
- 使用`hideCursor`和`showCursor`隱藏/顯示光標
- 處理螢幕清除和遊戲重繪

### 遊戲邏輯
- 設計適當的數據結構表示遊戲狀態
- 實現遊戲規則和更新邏輯
- 處理碰撞檢測和邊界條件
- 設計分數計算和遊戲進度

### 使用者輸入
- 實現非阻塞輸入檢測
- 處理按鍵映射（如WASD或方向鍵）
- 在按鍵和遊戲動作之間設計合理的關係

## 進階挑戰

1. 添加遊戲菜單和設置選項
2. 實現多個難度級別
3. 添加色彩和更豐富的視覺效果
4. 實現遊戲暫停和恢復功能
5. 添加音效（如果終端支援）
6. 實現高分保存和排行榜功能
7. 添加特殊物品和能力

## 專案結構

```
day13/
├── terminal_game.nim    # 主程式檔案
├── highscores.txt       # 高分記錄檔案（可選）
└── README.md            # 專案說明
```

## 學習資源

- [Nim標準庫：terminal](https://nim-lang.org/docs/terminal.html)
- [Nim標準庫：times](https://nim-lang.org/docs/times.html)
- [Nim非阻塞輸入](https://nim-lang.org/docs/os.html#kbhit)
- [遊戲循環設計](https://gameprogrammingpatterns.com/game-loop.html)

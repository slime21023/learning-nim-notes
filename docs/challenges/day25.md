# 第25天：命令列遊戲引擎

## 專案目標

開發一個簡單但靈活的命令列遊戲引擎框架，支援基於文字的遊戲開發，並使用該框架創建一個示例遊戲。

## 專案需求

1. 設計一個遊戲引擎框架，包含以下核心組件：
   - 遊戲循環系統
   - 場景管理器
   - 實體（Entity）系統
   - 事件處理系統
   - 命令解析器
   - 遊戲狀態管理

2. 引擎應支援以下功能：
   - 基於房間/場景的遊戲世界
   - 物品和NPC互動
   - 戰鬥系統（可選）
   - 存檔和讀檔
   - 自定義命令和動作

3. 使用引擎創建一個示例遊戲，如：
   - 文字冒險遊戲
   - 角色扮演遊戲
   - 模擬經營遊戲
   - 回合制戰略遊戲

4. 提供清晰的文檔，說明如何使用引擎開發自己的遊戲

## 實現提示

### 基本結構
```nim
# text_game_engine.nim
import tables, strutils, strformat, options, json, os, terminal, times, random

type
  EntityId = distinct int
  
  EntityKind = enum
    ekPlayer, ekNPC, ekItem, ekRoom, ekCustom
  
  Entity = ref object
    id: EntityId
    name: string
    description: string
    case kind: EntityKind
    of ekPlayer:
      health, maxHealth: int
      inventory: seq[EntityId]
    of ekNPC:
      dialogue: Table[string, string]
      friendly: bool
    of ekItem:
      portable: bool
      usable: bool
    of ekRoom:
      exits: Table[string, EntityId]  # 方向 -> 房間ID
      entities: seq[EntityId]  # 房間中的實體
    of ekCustom:
      properties: Table[string, string]
  
  GameState = object
    entities: Table[EntityId, Entity]
    currentRoom: EntityId
    player: EntityId
    turn: int
    gameOver: bool
    customState: Table[string, string]
  
  Action = object
    command: string
    args: seq[string]
  
  ActionHandler = proc(state: var GameState, action: Action): string
  
  Game = object
    state: GameState
    actions: Table[string, ActionHandler]
    beforeTurn, afterTurn: proc(state: var GameState)

proc newEntity(kind: EntityKind, name, description: string): Entity =
  # 創建新實體

proc newRoom(name, description: string): Entity =
  # 創建新房間

proc newItem(name, description: string, portable: bool = true, usable: bool = false): Entity =
  # 創建新物品

proc newNPC(name, description: string, friendly: bool = true): Entity =
  # 創建新NPC

proc newPlayer(name, description: string, health: int = 100): Entity =
  # 創建玩家

proc addEntity(game: var Game, entity: Entity): EntityId =
  # 將實體添加到遊戲中

proc connectRooms(game: var Game, roomA, roomB: EntityId, directionAB, directionBA: string) =
  # 連接兩個房間

proc moveEntity(game: var Game, entityId: EntityId, roomId: EntityId) =
  # 將實體移動到指定房間

proc describeRoom(game: Game): string =
  # 生成當前房間的描述

proc registerAction(game: var Game, command: string, handler: ActionHandler) =
  # 註冊命令處理器

proc parseAction(input: string): Action =
  # 解析用戶輸入為動作

proc executeAction(game: var Game, action: Action): string =
  # 執行動作並返回結果

proc gameLoop(game: var Game) =
  # 主遊戲循環

proc saveGame(game: Game, filename: string) =
  # 保存遊戲狀態到檔案

proc loadGame(filename: string): Game =
  # 從檔案載入遊戲狀態

proc createDemoGame(): Game =
  # 創建示例遊戲

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### 引擎設計
- 使用實體組件系統（Entity-Component System）或類似架構
- 實現靈活的命令解析和處理機制
- 設計清晰的API，方便遊戲開發者使用
- 提供友好的錯誤處理和調試功能

### 示例遊戲
- 創建有趣且直觀的遊戲場景
- 實現基本的遊戲機制（如移動、交互、戰鬥等）
- 設計合理的遊戲難度和進程
- 添加遊戲説明和提示

### 用戶界面
- 使用`terminal`模組增強文本顯示效果
- 實現顏色和格式化文本
- 提供清晰的遊戲狀態和信息顯示

## 進階挑戰

1. 實現更複雜的戰鬥系統（屬性、技能、裝備等）
2. 添加NPC行為AI
3. 實現任務系統
4. 添加時間和天氣系統
5. 創建動態生成的遊戲世界
6. 實現多人遊戲支援（網絡連接）

## 專案結構

```
day25/
├── text_game_engine.nim     # 引擎核心檔案
├── demo_game.nim            # 示例遊戲
├── saves/                   # 存檔目錄
└── README.md                # 專案說明
```

## 學習資源

- [實體組件系統](https://en.wikipedia.org/wiki/Entity_component_system)
- [遊戲設計模式](https://gameprogrammingpatterns.com/)
- [互動式小說設計](https://emshort.blog/how-to-play/writing-if/)
- [Nim標準庫：terminal](https://nim-lang.org/docs/terminal.html)

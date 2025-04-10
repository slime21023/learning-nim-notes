# 第22天：GUI桌面應用

## 專案目標

使用Nim開發一個具有圖形用戶界面（GUI）的桌面應用程式，提供實用功能和友好界面。

## 專案需求

1. 創建一個桌面應用，使用以下其中一個GUI框架：
   - nimx
   - NiGui
   - ui模組
   - 或其他Nim支援的GUI庫

2. 實現以下基本UI元素：
   - 視窗和面板
   - 按鈕、標籤和文本輸入
   - 列表或表格顯示數據
   - 菜單和對話框
   - 基本的事件處理

3. 設計一個實用的應用，如：
   - 筆記本應用
   - 計算器
   - 待辦事項管理器
   - 檔案管理器
   - 或其他您選擇的工具

4. 實現數據的持久化存儲，如保存設置或應用數據

## 實現提示

### 基本結構（以NiGui為例）
```nim
# gui_app.nim
import nigui, os, json, strutils, times

app.init()

# 創建主視窗
var window = newWindow("Nim GUI應用")
window.width = 800
window.height = 600

# 創建控制項
var container = newContainer()
window.add(container)

# 設計布局
var layout = newLayoutContainer(Layout_Vertical)
container.add(layout)

# 添加控制項
var button = newButton("點擊我")
var label = newLabel("歡迎使用Nim GUI應用")
var textBox = newTextBox()

layout.add(label)
layout.add(textBox)
layout.add(button)

# 實現事件處理
button.onClick = proc(event: ClickEvent) =
  label.text = "你好, " & textBox.text & "!"

# 數據持久化函數
proc saveSettings(settings: JsonNode) =
  writeFile("settings.json", $settings)

proc loadSettings(): JsonNode =
  if fileExists("settings.json"):
    parseJson(readFile("settings.json"))
  else:
    %* {"firstRun": true}

# 應用邏輯
# ...

# 顯示視窗並運行應用
window.show()
app.run()
```

### GUI庫選擇
- **nimx**: 功能豐富的跨平台GUI框架
- **NiGui**: 簡單易用的跨平台GUI庫
- **ui**: 基於libui的跨平台GUI包裝器
- 或考慮使用Web技術（如WebView）創建混合應用

### 界面設計
- 設計清晰、直觀的用戶界面
- 實現適當的布局管理
- 考慮不同螢幕大小和分辨率
- 設計一致的視覺風格

### 數據管理
- 使用JSON、SQLite或其他格式保存數據
- 實現設置保存和加載
- 處理數據導入和導出功能

## 進階挑戰

1. 添加自定義主題和樣式
2. 實現多語言支援
3. 添加拖放功能
4. 實現系統托盤集成
5. 添加鍵盤快捷鍵支援
6. 實現撤銷/重做功能
7. 添加自動更新機制

## 專案結構

```
day22/
├── gui_app.nim        # 主程式檔案
├── resources/         # 資源檔案（圖標、圖片等）
│   ├── icon.png
│   └── style.css
├── data/              # 數據檔案
│   └── settings.json
└── README.md          # 專案說明
```

## 學習資源

- [NiGui文檔](https://github.com/simonkrauter/NiGui)
- [nimx文檔](https://github.com/yglukhov/nimx)
- [Nim ui模組](https://github.com/nim-lang/ui)
- [桌面應用設計原則](https://developer.apple.com/design/human-interface-guidelines/designing-for-macos)

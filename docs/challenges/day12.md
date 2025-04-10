# 第12天：Markdown到HTML轉換器

## 專案目標

開發一個Markdown解析和轉換工具，能夠將Markdown格式的文本轉換為HTML。

## 專案需求

1. 支援基本的Markdown語法：
   - 標題（#、##、###等）
   - 強調（*斜體*、**粗體**）
   - 列表（有序和無序）
   - 鏈接和圖片
   - 引用區塊
   - 程式碼區塊
2. 提供命令列界面，支援單檔案和批量轉換
3. 支援自定義輸出選項，如樣式表和輸出格式
4. 生成符合HTML5標準的輸出
5. 處理特殊字符和轉義序列
6. 提供友好的錯誤報告

## 實現提示

### 基本結構
```nim
# markdown_converter.nim
import os, strutils, parseopt, strformat, re, tables

type
  ConverterOptions = object
    inputFile: string
    outputFile: string
    cssFile: string
    title: string
    standalone: bool  # 是否生成完整HTML或只有正文

  ParserState = enum
    psNormal, psHeading, psList, psBlockquote, psCodeBlock

proc parseMarkdown(markdown: string, options: ConverterOptions): string =
  # 將Markdown文本解析為HTML

proc processInlineMarkdown(text: string): string =
  # 處理行內Markdown元素（粗體、斜體、鏈接等）

proc generateHTML(body: string, options: ConverterOptions): string =
  # 生成完整的HTML文檔，包括頭部和樣式表

proc convertFile(inputFile, outputFile: string, options: ConverterOptions) =
  # 轉換單個檔案

proc parseCommandLine(): ConverterOptions =
  # 解析命令列參數

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### Markdown解析
- 使用`re`模組編寫正則表達式來匹配Markdown模式
- 使用狀態機模式處理不同類型的Markdown元素
- 分別處理塊級元素和行內元素

### HTML生成
- 創建適當的HTML標籤結構
- 處理HTML特殊字符的轉義
- 添加CSS樣式表鏈接

### 檔案處理
- 讀取和寫入檔案
- 處理不同的換行字符（Windows、Unix）
- 支援目錄遞迴操作

## 進階挑戰

1. 支援更多Markdown擴展語法（表格、腳註等）
2. 添加對自定義HTML標籤的支援
3. 實現目錄（TOC）自動生成
4. 添加語法高亮功能（針對程式碼區塊）
5. 實現幻燈片模式（將Markdown轉換為演示文稿）
6. 添加Markdown預覽功能

## 專案結構

```
day12/
├── markdown_converter.nim    # 主程式檔案
├── examples/                 # 示例Markdown檔案
│   ├── example1.md
│   └── example2.md
├── templates/                # HTML模板
│   └── default.html
└── README.md                 # 專案說明
```

## 學習資源

- [Markdown基礎語法](https://www.markdownguide.org/basic-syntax/)
- [Nim標準庫：re](https://nim-lang.org/docs/re.html)
- [HTML5規範](https://developer.mozilla.org/zh-TW/docs/Web/Guide/HTML/HTML5)

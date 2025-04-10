# 第4天：文字檔案統計工具

## 專案目標

開發一個命令列工具，用於分析和統計文本檔案中的字符數、單詞數和行數等信息。

## 專案需求

1. 接受一個或多個文本檔案作為輸入
2. 計算並顯示每個檔案的以下統計信息：
   - 總字符數（含空格）
   - 總字符數（不含空格）
   - 單詞數
   - 行數
   - 段落數
3. 如果提供了多個檔案，顯示所有檔案的合計統計信息
4. 支援命令列參數以自定義輸出格式和統計項目
5. 處理檔案不存在或無法讀取的錯誤

## 實現提示

### 基本結構
```nim
# file_stats.nim
import os, strutils, parseopt

type
  FileStats = object
    filename: string
    totalChars: int
    nonWhitespaceChars: int
    words: int
    lines: int
    paragraphs: int

proc analyzeFile(filename: string): FileStats =
  # 分析檔案並返回統計信息

proc displayStats(stats: FileStats) =
  # 顯示單個檔案的統計信息

proc displayTotalStats(allStats: seq[FileStats]) =
  # 顯示所有檔案的合計統計信息

proc parseCommandLine(): seq[string] =
  # 解析命令列參數

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### 檔案處理
- 使用`readFile`函數讀取整個檔案內容
- 使用`system.open`和`readLine`逐行讀取大型檔案
- 使用`splitLines`分割文本為行列表

### 文本分析
- 使用`countup`或迴圈計算字符
- 使用`split`分割文本為單詞
- 用空行識別段落

### 命令列參數
- 使用`parseopt`模組解析命令列選項
- 提供幫助信息（--help）
- 設計合理的預設值和選項

## 進階挑戰

1. 添加更多統計信息（如最長行、最常用單詞等）
2. 支援不同的編碼格式（UTF-8, ASCII等）
3. 實現進度指示器（對於大型檔案）
4. 添加檢測檔案類型的功能（文本還是二進位）
5. 生成統計報告（如CSV或HTML格式）
6. 增加對目錄遞迴分析的支援

## 專案結構

```
day04/
├── file_stats.nim     # 主程式檔案
├── sample/            # 用於測試的樣本檔案
│   ├── sample1.txt
│   └── sample2.txt
└── README.md          # 專案說明
```

## 學習資源

- [Nim標準庫：os](https://nim-lang.org/docs/os.html)
- [Nim標準庫：strutils](https://nim-lang.org/docs/strutils.html)
- [Nim標準庫：parseopt](https://nim-lang.org/docs/parseopt.html)
- [Nim檔案處理](https://nim-lang.org/docs/io.html)

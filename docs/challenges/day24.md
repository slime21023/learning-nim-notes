# 第24天：資料視覺化工具

## 專案目標

開發一個資料視覺化工具，能夠讀取不同格式的數據，並生成各種類型的圖表和視覺化效果。

## 專案需求

1. 支援以下輸入數據格式：
   - CSV檔案
   - JSON檔案
   - 數據庫查詢結果
   - 簡單的API數據源

2. 實現以下圖表類型：
   - 折線圖
   - 柱狀圖
   - 圓餅圖
   - 散點圖
   - 熱力圖（可選）

3. 提供以下輸出選項：
   - HTML網頁（使用JavaScript圖表庫）
   - SVG圖像
   - PNG圖像（可選）
   - 交互式儀表板（進階）

4. 實現命令列界面，支援數據處理選項：
   - 數據過濾和選擇
   - 數據轉換和計算
   - 圖表自定義選項

## 實現提示

### 基本結構
```nim
# data_visualizer.nim
import os, strutils, parsecsv, json, strformat, tables, algorithm, parseopt

type
  DataFormat = enum
    dfCSV, dfJSON, dfSQL, dfAPI
  
  ChartType = enum
    ctLine, ctBar, ctPie, ctScatter, ctHeatmap
  
  OutputFormat = enum
    ofHTML, ofSVG, ofPNG
  
  DataColumn = object
    name: string
    dataType: string  # 數字, 字符串, 日期等
    values: seq[string]
  
  DataSet = object
    columns: seq[DataColumn]
    rowCount: int
  
  ChartOptions = object
    title: string
    width, height: int
    xAxisLabel, yAxisLabel: string
    colors: seq[string]
    legendPosition: string  # top, bottom, left, right, none

proc loadCSV(filename: string): DataSet =
  # 從CSV檔案載入數據

proc loadJSON(filename: string): DataSet =
  # 從JSON檔案載入數據

proc filterData(dataset: DataSet, filterExpr: string): DataSet =
  # 根據條件過濾數據

proc transformData(dataset: DataSet, transformExpr: string): DataSet =
  # 轉換數據（如計算新列）

proc generateHTML(dataset: DataSet, chartType: ChartType, options: ChartOptions): string =
  # 生成包含JavaScript圖表的HTML

proc generateSVG(dataset: DataSet, chartType: ChartType, options: ChartOptions): string =
  # 生成SVG圖表

proc saveOutput(content: string, filename: string, format: OutputFormat) =
  # 將圖表保存到檔案

proc generateChart(dataset: DataSet, chartType: ChartType, outputFormat: OutputFormat, options: ChartOptions, outputFile: string) =
  # 生成並保存圖表

proc parseCommandLine(): tuple[
  inputFile: string,
  inputFormat: DataFormat,
  chartType: ChartType,
  outputFormat: OutputFormat,
  outputFile: string,
  options: ChartOptions,
  filterExpr: string,
  transformExpr: string
] =
  # 解析命令列參數

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### 數據處理
- 使用`parsecsv`處理CSV檔案
- 使用`json`模組處理JSON數據
- 設計靈活的數據過濾和轉換機制
- 處理不同的數據類型和格式

### 圖表生成
- 考慮使用現有的JavaScript圖表庫（如Chart.js, D3.js）
- 為SVG輸出實現簡單的圖表渲染演算法
- 支援圖表自定義（顏色、標籤、大小等）

### 命令列界面
- 設計直觀的參數結構
- 支援複雜的數據處理表達式
- 提供清晰的幫助信息和範例

## 進階挑戰

1. 實現交互式儀表板，可在網頁上動態調整圖表
2. 添加高級統計功能（如回歸線、趨勢分析）
3. 支援地理數據和地圖視覺化
4. 實現實時數據源和自動更新
5. 添加資料動畫和時間序列視覺化
6. 開發可重用的圖表模板系統

## 專案結構

```
day24/
├── data_visualizer.nim    # 主程式檔案
├── data/                  # 範例數據檔案
│   ├── sample.csv
│   └── sample.json
├── templates/             # HTML模板
│   └── chart_template.html
└── README.md              # 專案說明
```

## 學習資源

- [Chart.js文檔](https://www.chartjs.org/)
- [D3.js文檔](https://d3js.org/)
- [資料視覺化設計原則](https://www.storytellingwithdata.com/)
- [Nim標準庫：parsecsv](https://nim-lang.org/docs/parsecsv.html)

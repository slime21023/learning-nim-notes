# 第19天：圖像處理工具

## 專案目標

開發一個命令列圖像處理工具，能夠執行基本的圖像操作和濾鏡效果。

## 專案需求

1. 支援以下圖像操作：
   - 調整圖像大小（縮放）
   - 旋轉圖像（90°, 180°, 270°或任意角度）
   - 裁剪圖像
   - 調整亮度、對比度和飽和度
   - 轉換為灰階或黑白
   - 添加簡單的濾鏡效果

2. 支援常見的圖像格式：
   - JPEG
   - PNG
   - BMP
   - GIF（可選）

3. 提供命令列界面，支援以下功能：
   - 批量處理多個圖像
   - 鏈式操作（一次應用多個效果）
   - 自訂輸出格式和質量
   - 預覽模式（可選）

4. 實現操作的進度顯示和錯誤報告

## 實現提示

### 基本結構
```nim
# image_processor.nim
import os, strutils, strformat, parseopt
# 使用第三方庫處理圖像（如nimPNG）

type
  ImageInfo = object
    width, height: int
    channels: int
    format: string
    filename: string

  ProcessingOptions = object
    resize: tuple[width, height: int, keepAspect: bool]
    rotate: float  # 角度
    crop: tuple[x, y, width, height: int]
    brightness: float  # -1.0 到 1.0
    contrast: float    # -1.0 到 1.0
    saturation: float  # -1.0 到 1.0
    grayscale: bool
    filter: string     # 濾鏡名稱
    outputFormat: string
    outputQuality: int # 1-100
    outputDir: string

proc loadImage(filename: string): Image =
  # 載入圖像檔案

proc saveImage(img: Image, filename: string, format: string = "", quality: int = 90): bool =
  # 保存圖像到檔案

proc getImageInfo(img: Image): ImageInfo =
  # 獲取圖像資訊

proc resizeImage(img: Image, width, height: int, keepAspect: bool = true): Image =
  # 調整圖像大小

proc rotateImage(img: Image, angle: float): Image =
  # 旋轉圖像

proc cropImage(img: Image, x, y, width, height: int): Image =
  # 裁剪圖像

proc adjustBrightness(img: Image, value: float): Image =
  # 調整亮度

proc adjustContrast(img: Image, value: float): Image =
  # 調整對比度

proc adjustSaturation(img: Image, value: float): Image =
  # 調整飽和度

proc convertToGrayscale(img: Image): Image =
  # 轉換為灰階

proc applyFilter(img: Image, filterName: string): Image =
  # 應用特定濾鏡

proc processImage(filename: string, options: ProcessingOptions): bool =
  # 根據選項處理單個圖像

proc parseCommandLine(): tuple[files: seq[string], options: ProcessingOptions] =
  # 解析命令列參數

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### 圖像處理
- 使用第三方庫處理圖像（如nimPNG, stb_image）
- 實現基本圖像操作的演算法
- 處理不同的顏色空間和圖像格式

### 命令列界面
- 設計清晰的參數結構
- 支援批處理和通配符
- 提供幫助信息和使用範例

### 效能優化
- 考慮大型圖像的內存使用
- 實現操作的中間緩存
- 提供進度指示器對於批處理任務

## 進階挑戰

1. 添加更多高級濾鏡和效果（如模糊、銳化、邊緣檢測等）
2. 實現圖像合成和疊加功能
3. 添加文字和水印支援
4. 實現簡單的臉部識別和自動裁剪
5. 添加圖像批量比較和差異檢測
6. 創建一個簡單的圖像處理腳本語言

## 專案結構

```
day19/
├── image_processor.nim    # 主程式檔案
├── filters/               # 濾鏡實現目錄
│   ├── basic.nim
│   └── advanced.nim
├── examples/              # 範例圖像
│   ├── sample1.jpg
│   └── sample2.png
└── README.md              # 專案說明
```

## 學習資源

- [Nim包：nimPNG](https://github.com/jangko/nimPNG)
- [stb_image庫](https://github.com/nothings/stb/blob/master/stb_image.h)
- [數位圖像處理基礎](https://www.tutorialspoint.com/dip/index.htm)
- [顏色理論和色彩空間](https://programmingdesignsystems.com/color/color-models-and-color-spaces/)

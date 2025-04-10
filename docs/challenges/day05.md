# 第5天：密碼產生器

## 專案目標

創建一個靈活的隨機密碼產生器，能夠生成符合不同安全要求的強密碼。

## 專案需求

1. 生成指定長度的隨機密碼
2. 支援以下字符類型的組合：
   - 大寫字母 (A-Z)
   - 小寫字母 (a-z)
   - 數字 (0-9)
   - 特殊字符 (!@#$%^&*等)
3. 允許使用者指定密碼長度和所需的字符類型
4. 確保生成的密碼至少包含每種所選字符類型的一個字符
5. 提供友好的命令列界面和使用指南
6. 選項可以生成多個密碼

## 實現提示

### 基本結構
```nim
# password_generator.nim
import random, strutils, parseopt

type
  PasswordOptions = object
    length: int
    useUppercase: bool
    useLowercase: bool
    useNumbers: bool
    useSpecialChars: bool
    count: int

proc generatePassword(options: PasswordOptions): string =
  # 根據選項生成隨機密碼

proc parseCommandLine(): PasswordOptions =
  # 解析命令列參數

proc main() =
  randomize()  # 初始化隨機種子
  let options = parseCommandLine()
  for i in 1..options.count:
    let password = generatePassword(options)
    echo password

when isMainModule:
  main()
```

### 隨機生成
- 使用`randomize()`初始化隨機種子
- 定義不同的字符集（大寫、小寫、數字、特殊字符）
- 使用`rand`函數隨機選擇字符
- 確保結果包含所有所需字符類型

### 命令列參數
- 實現合理的預設選項（如長度=12，包含所有字符類型）
- 使用`parseopt`模組解析命令列選項
- 提供詳細的幫助信息

### 密碼評估
- 可選實現密碼強度評估功能
- 檢查密碼是否符合安全標準

## 進階挑戰

1. 添加密碼強度評估功能
2. 實現自定義字符集（允許使用者指定特定字符）
3. 添加生成可記憶密碼的選項（如單詞組合）
4. 實現密碼保存功能（安全地存儲到加密檔案）
5. 添加GUI界面
6. 創建一個密碼管理系統的基礎

## 專案結構

```
day05/
├── password_generator.nim    # 主程式檔案
└── README.md                 # 專案說明
```

## 學習資源

- [Nim標準庫：random](https://nim-lang.org/docs/random.html)
- [Nim標準庫：parseopt](https://nim-lang.org/docs/parseopt.html)
- [Nim字符串操作](https://nim-lang.org/docs/strutils.html)

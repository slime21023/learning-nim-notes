# 第8天：簡單檔案加密工具

## 專案目標

開發一個基本的檔案加密和解密工具，使用簡單的加密算法保護文本檔案內容。

## 專案需求

1. 支援文本檔案的加密和解密功能
2. 實現至少兩種加密算法：
   - 凱撒密碼（字符移位）
   - XOR加密（使用密鑰）
3. 通過命令列參數指定操作、算法和密鑰
4. 提供以下操作選項：
   - 加密檔案
   - 解密檔案
   - 查看加密檔案信息
5. 保存加密結果到新檔案，不覆蓋原始檔案
6. 處理可能的錯誤和異常情況

## 實現提示

### 基本結構
```nim
# file_encryptor.nim
import os, strutils, parseopt

type
  EncryptionAlgorithm = enum
    eaCaesar, eaXOR
  
  OperationMode = enum
    omEncrypt, omDecrypt, omInfo
  
  EncryptionOptions = object
    algorithm: EncryptionAlgorithm
    key: string
    inputFile: string
    outputFile: string
    mode: OperationMode

proc encryptCaesar(text: string, shift: int): string =
  # 使用凱撒密碼加密文本

proc decryptCaesar(text: string, shift: int): string =
  # 使用凱撒密碼解密文本

proc encryptXOR(text: string, key: string): string =
  # 使用XOR方法加密文本

proc decryptXOR(text: string, key: string): string =
  # 使用XOR方法解密文本

proc processFile(options: EncryptionOptions) =
  # 根據提供的選項處理檔案

proc parseCommandLine(): EncryptionOptions =
  # 解析命令列參數

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### 加密算法
- 凱撒密碼：移位每個字符的ASCII值
- XOR加密：使用按位異或操作
- 對二進制數據進行加密時考慮編碼問題

### 檔案處理
- 使用`readFile`和`writeFile`函數處理文本檔案
- 考慮大型檔案的分塊處理
- 添加錯誤處理機制

### 命令列界面
- 使用`parseopt`模組處理命令列參數
- 提供清晰的使用說明和幫助信息
- 顯示加密/解密過程的進度（可選）

## 進階挑戰

1. 實現更複雜的加密算法（如AES或RSA）
2. 添加密碼保護功能（使用密碼生成加密密鑰）
3. 支援目錄的遞迴加密和解密
4. 添加完整性檢查以驗證解密是否成功
5. 實現二進制檔案的加密和解密
6. 創建一個加密存儲系統的簡單版本

## 專案結構

```
day08/
├── file_encryptor.nim    # 主程式檔案
├── test/                 # 測試檔案目錄
│   ├── sample.txt
│   └── sample_encrypted.txt
└── README.md             # 專案說明
```

## 學習資源

- [Nim位運算符](https://nim-lang.org/docs/manual.html#lexical-analysis-operators)
- [Nim標準庫：parseopt](https://nim-lang.org/docs/parseopt.html)
- [Nim檔案操作](https://nim-lang.org/docs/io.html)

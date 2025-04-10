# 第6天：簡易通訊錄

## 專案目標

開發一個命令列通訊錄應用，用於管理聯絡人信息並提供搜索和管理功能。

## 專案需求

1. 支援新增、編輯、檢視和刪除聯絡人
2. 每個聯絡人應包含以下信息：
   - 姓名
   - 電話號碼
   - 電子郵件
   - 地址（可選）
   - 備註（可選）
3. 提供按姓名和電話號碼搜索聯絡人的功能
4. 將聯絡人數據保存到檔案中，並在程式啟動時自動加載
5. 提供友好的使用界面和指南
6. 支援聯絡人列表的排序（按姓名字母順序）

## 實現提示

### 基本結構
```nim
# address_book.nim
import os, json, strutils, strformat, algorithm, tables

type
  Contact = object
    name: string
    phone: string
    email: string
    address: string
    notes: string

  AddressBook = object
    contacts: seq[Contact]

proc saveToFile(book: AddressBook, filename: string) =
  # 將通訊錄保存到檔案

proc loadFromFile(filename: string): AddressBook =
  # 從檔案加載通訊錄

proc addContact(book: var AddressBook, contact: Contact) =
  # 添加新聯絡人

proc editContact(book: var AddressBook, index: int, newContact: Contact) =
  # 編輯現有聯絡人

proc deleteContact(book: var AddressBook, index: int) =
  # 刪除聯絡人

proc findContact(book: AddressBook, searchTerm: string): seq[int] =
  # 搜索聯絡人，返回匹配的索引列表

proc displayContact(contact: Contact) =
  # 顯示單個聯絡人詳細信息

proc displayAllContacts(book: AddressBook) =
  # 顯示所有聯絡人列表

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### 數據管理
- 使用`seq[Contact]`儲存聯絡人列表
- 使用`json`模組進行數據序列化和反序列化
- 實現數據驗證（如電子郵件格式）

### 用戶界面
- 創建清晰的菜單系統
- 使用`readLine(stdin)`和適當的提示獲取使用者輸入
- 格式化輸出聯絡人信息

### 搜索和排序
- 實現模糊搜索（不區分大小寫，部分匹配）
- 使用`algorithm`模組對聯絡人列表進行排序
- 提供多種排序選項（姓名、添加時間等）

## 進階挑戰

1. 添加聯絡人分組功能
2. 實現聯絡人導入/導出（CSV, vCard格式）
3. 添加生日提醒功能
4. 實現密碼保護
5. 創建更豐富的TUI界面
6. 添加雲端同步功能的基礎結構

## 專案結構

```
day06/
├── address_book.nim    # 主程式檔案
├── contacts.json       # 聯絡人數據檔案
└── README.md           # 專案說明
```

## 學習資源

- [Nim標準庫：json](https://nim-lang.org/docs/json.html)
- [Nim標準庫：algorithm](https://nim-lang.org/docs/algorithm.html)
- [Nim標準庫：tables](https://nim-lang.org/docs/tables.html)

# 第20天：RESTful API客戶端

## 專案目標

開發一個通用的RESTful API客戶端，支援不同的HTTP方法、認證方式和數據格式，並提供友好的命令列界面。

## 專案需求

1. 支援以下HTTP方法：
   - GET
   - POST
   - PUT
   - DELETE
   - PATCH
   - HEAD
   - OPTIONS

2. 支援多種認證方式：
   - Basic認證
   - API密鑰認證
   - OAuth 2.0（可選）
   - JWT（可選）

3. 支援不同的數據格式：
   - JSON
   - XML
   - Form URL編碼
   - 多部分表單數據

4. 提供命令列界面，包括以下功能：
   - 指定URL、方法和數據
   - 設置請求頭和參數
   - 格式化輸出結果
   - 保存響應到檔案

5. 支援高級功能：
   - 自動重試
   - 超時設置
   - 代理支援
   - 會話和Cookie處理

## 實現提示

### 基本結構
```nim
# rest_client.nim
import httpclient, json, xmlparser, xmltree, base64, strutils, parseopt, strformat, tables, uri, os, times

type
  AuthMethod = enum
    amNone, amBasic, amApiKey, amOAuth, amJWT
  
  DataFormat = enum
    dfNone, dfJSON, dfXML, dfForm, dfMultipart
  
  ClientConfig = object
    url: string
    httpMethod: string
    headers: TableRef[string, string]
    params: TableRef[string, string]
    data: string
    dataFormat: DataFormat
    authMethod: AuthMethod
    authUsername: string
    authPassword: string
    apiKeyName: string
    apiKeyValue: string
    tokenValue: string
    timeout: int  # 秒
    retryCount: int
    retryDelay: int  # 毫秒
    proxy: string
    outputFile: string
    pretty: bool

proc setupAuth(client: HttpClient, config: ClientConfig) =
  # 根據認證方式設置認證信息

proc setupHeaders(client: HttpClient, config: ClientConfig) =
  # 設置HTTP請求頭

proc formatData(data: string, format: DataFormat): string =
  # 根據指定格式格式化數據

proc formatResponse(response: Response, pretty: bool): string =
  # 格式化響應內容

proc sendRequest(config: ClientConfig): Response =
  # 發送HTTP請求並返回響應

proc parseCommandLine(): ClientConfig =
  # 解析命令列參數

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### HTTP請求處理
- 使用`httpclient`模組發送HTTP請求
- 實現不同認證方法的邏輯
- 處理各種數據格式的序列化和反序列化

### 認證管理
- 實現Basic認證（使用Base64編碼）
- 支援不同位置的API密鑰（請求頭、URL參數等）
- 提供OAuth和JWT的基本支援

### 命令列界面
- 設計清晰的參數結構
- 支援從檔案載入請求數據
- 提供詳細的幫助信息和示例

## 進階挑戰

1. 實現API描述格式支援（如OpenAPI/Swagger）
2. 添加自動API探索功能
3. 實現響應快取機制
4. 創建API測試框架
5. 支援GraphQL查詢
6. 實現API請求的錄製和回放功能

## 專案結構

```
day20/
├── rest_client.nim     # 主程式檔案
├── examples/           # API請求範例
│   ├── github_api.json
│   └── weather_api.json
└── README.md           # 專案說明
```

## 學習資源

- [Nim標準庫：httpclient](https://nim-lang.org/docs/httpclient.html)
- [HTTP協議參考](https://developer.mozilla.org/zh-TW/docs/Web/HTTP)
- [RESTful API設計原則](https://restfulapi.net/)
- [OAuth 2.0規範](https://oauth.net/2/)

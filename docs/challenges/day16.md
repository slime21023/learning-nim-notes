# 第16天：終端聊天應用

## 專案目標

開發一個基於TCP/IP的終端聊天應用，包含伺服器和客戶端，允許多個使用者進行實時通訊。

## 專案需求

1. 實現聊天伺服器：
   - 監聽指定埠並接受多個客戶端連接
   - 處理使用者登入和識別
   - 轉發消息給所有或特定使用者
   - 記錄基本的伺服器活動

2. 實現聊天客戶端：
   - 連接到伺服器並提供使用者名稱
   - 發送消息到伺服器
   - 接收並顯示來自其他使用者的消息
   - 支援基本命令（如列出在線使用者、私聊等）

3. 支援以下功能：
   - 公共聊天室消息
   - 私人消息（私聊）
   - 使用者上線/離線通知
   - 簡單的命令系統（以/開頭的命令）

4. 提供友好的終端界面，正確處理使用者輸入和消息顯示

## 實現提示

### 基本結構
```nim
# 聊天伺服器
# chat_server.nim
import asyncnet, asyncdispatch, tables, strutils, times, logging, strformat

type
  Client = ref object
    socket: AsyncSocket
    username: string
    joinTime: Time

  Server = ref object
    clients: TableRef[string, Client]
    socket: AsyncSocket

proc newServer(): Server =
  # 初始化伺服器

proc processMessages(server: Server, client: Client) {.async.} =
  # 處理從客戶端接收的消息

proc handleCommand(server: Server, client: Client, cmd: string): Future[bool] {.async.} =
  # 處理客戶端命令

proc broadcast(server: Server, message: string, exclude: string = "") {.async.} =
  # 向所有或部分客戶端廣播消息

proc privateMessage(server: Server, from, to, message: string) {.async.} =
  # 發送私人消息

proc serve(server: Server, port: int) {.async.} =
  # 啟動伺服器並監聽連接

proc main() {.async.} =
  # 主程式邏輯
  var server = newServer()
  await serve(server, 8000)

when isMainModule:
  asyncCheck main()
  runForever()
```

```nim
# 聊天客戶端
# chat_client.nim
import asyncnet, asyncdispatch, strutils, strformat, terminal

type
  Client = ref object
    socket: AsyncSocket
    username: string
    serverAddr: string
    serverPort: int

proc newClient(username, serverAddr: string, serverPort: int): Client =
  # 初始化客戶端

proc connect(client: Client) {.async.} =
  # 連接到伺服器

proc receiveMessages(client: Client) {.async.} =
  # 接收並顯示從伺服器發來的消息

proc sendMessages(client: Client) {.async.} =
  # 從終端讀取輸入並發送到伺服器

proc main() {.async.} =
  # 主程式邏輯

when isMainModule:
  asyncCheck main()
  runForever()
```

### 網絡編程
- 使用`asyncnet`和`asyncdispatch`實現非同步網絡通訊
- 設計簡單的通訊協議（如：消息類型+內容）
- 處理連接錯誤和重連機制

### 用戶界面
- 區分系統消息和使用者消息
- 使用顏色增強可讀性
- 提供清晰的命令語法和幫助信息

### 多客戶端管理
- 使用表（`tables`）管理多個客戶端連接
- 正確處理客戶端斷開連接
- 實現廣播和定向消息機制

## 進階挑戰

1. 添加使用者認證系統
2. 實現消息歷史記錄和查詢
3. 支援多個聊天室或頻道
4. 添加文件傳輸功能
5. 實現消息加密
6. 增加更豐富的命令和管理功能

## 專案結構

```
day16/
├── chat_server.nim    # 伺服器程式
├── chat_client.nim    # 客戶端程式
└── README.md          # 專案說明
```

## 學習資源

- [Nim標準庫：asyncnet](https://nim-lang.org/docs/asyncnet.html)
- [Nim標準庫：asyncdispatch](https://nim-lang.org/docs/asyncdispatch.html)
- [Nim標準庫：tables](https://nim-lang.org/docs/tables.html)
- [TCP/IP網絡編程基礎](https://beej.us/guide/bgnet/)

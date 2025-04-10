# 第23天：WebSocket即時聊天

## 專案目標

開發一個基於WebSocket的即時聊天應用，包含伺服器和網頁客戶端，支援多人聊天和即時消息推送。

## 專案需求

1. 實現WebSocket伺服器：
   - 處理客戶端連接和斷開
   - 廣播消息給所有連接的客戶端
   - 支援私人消息和聊天室功能
   - 追蹤用戶狀態和在線情況

2. 創建網頁客戶端界面：
   - 簡潔的聊天界面
   - 顯示在線用戶列表
   - 消息輸入和發送功能
   - 支援顯示消息時間戳和發送者

3. 實現以下聊天功能：
   - 用戶登入和用戶名設置
   - 公共聊天室
   - 私人消息功能
   - 顯示用戶上線/離線通知
   - 支援基本的文本格式化（可選）

4. 確保應用的穩定性和錯誤處理

## 實現提示

### 基本結構
```nim
# chat_server.nim
import asynchttpserver, asyncdispatch, ws, json, tables, strutils, strformat, times, logging

type
  ChatClient = ref object
    id: string
    username: string
    socket: WebSocket
    joinTime: Time
    room: string

  ChatServer = ref object
    clients: TableRef[string, ChatClient]
    rooms: TableRef[string, seq[string]]  # 房間名稱 -> 客戶端ID列表

proc newChatServer(): ChatServer =
  # 初始化聊天伺服器

proc broadcast(server: ChatServer, message: JsonNode, room: string = "", exclude: string = "") {.async.} =
  # 廣播消息給指定房間或所有客戶端

proc handleUserMessage(server: ChatServer, client: ChatClient, messageText: string) {.async.} =
  # 處理用戶發送的消息

proc handleCommand(server: ChatServer, client: ChatClient, command: string, args: seq[string]): Future[bool] {.async.} =
  # 處理特殊命令（如加入房間、私聊等）

proc handleConnection(server: ChatServer, ws: WebSocket, req: Request) {.async.} =
  # 處理新的WebSocket連接

proc serve(server: ChatServer, port: int) {.async.} =
  # 啟動WebSocket伺服器

proc main() {.async.} =
  # 主程式邏輯
  let server = newChatServer()
  await serve(server, 8080)

when isMainModule:
  initLogging()
  asyncCheck main()
  runForever()
```

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebSocket聊天室</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="chat-container">
        <div class="sidebar">
            <div class="user-info">
                <input type="text" id="username" placeholder="請輸入用戶名">
                <button id="login-btn">登入</button>
            </div>
            <div class="room-list">
                <h3>聊天室</h3>
                <ul id="rooms"></ul>
            </div>
            <div class="user-list">
                <h3>在線用戶</h3>
                <ul id="users"></ul>
            </div>
        </div>
        <div class="chat-area">
            <div class="messages" id="messages"></div>
            <div class="input-area">
                <input type="text" id="message-input" placeholder="輸入消息..." disabled>
                <button id="send-btn" disabled>發送</button>
            </div>
        </div>
    </div>
    <script src="chat.js"></script>
</body>
</html>
```

```javascript
// chat.js
let socket;
let username = '';
let currentRoom = 'general';

function connect() {
    socket = new WebSocket(`ws://${window.location.hostname}:8080`);
    
    socket.onopen = (event) => {
        console.log('連接成功');
        if (username) {
            sendLoginMessage();
        }
    };
    
    socket.onmessage = (event) => {
        const message = JSON.parse(event.data);
        handleMessage(message);
    };
    
    socket.onclose = (event) => {
        console.log('連接關閉');
        setTimeout(connect, 3000);
    };
    
    socket.onerror = (error) => {
        console.error('WebSocket錯誤:', error);
    };
}

function sendLoginMessage() {
    // 實現登入消息發送
}

function sendChatMessage() {
    // 實現聊天消息發送
}

function handleMessage(message) {
    // 處理收到的消息
}

// 初始化和事件監聽器
document.addEventListener('DOMContentLoaded', () => {
    // 設置按鈕和輸入框的事件處理程序
});

// 連接到WebSocket伺服器
connect();
```

### WebSocket處理
- 使用Nim的WebSocket庫處理連接
- 設計消息格式（使用JSON）
- 實現心跳檢測保持連接

### 前端實現
- 使用純HTML、CSS和JavaScript創建聊天界面
- 處理WebSocket通信
- 實現消息渲染和用戶界面更新

### 消息格式
- 設計清晰的消息協議
- 區分不同類型的消息（聊天、系統通知、用戶狀態）
- 包含必要的元數據（時間戳、發送者等）

## 進階挑戰

1. 添加消息歷史記錄功能
2. 實現檔案和圖片共享
3. 添加端到端加密
4. 支援Markdown或HTML格式化
5. 實現用戶身份認證和註冊
6. 添加多聊天室和頻道功能
7. 實現消息通知（桌面通知或聲音提醒）

## 專案結構

```
day23/
├── server/
│   └── chat_server.nim    # 伺服器程式
├── public/                # 客戶端檔案
│   ├── index.html
│   ├── style.css
│   └── chat.js
└── README.md              # 專案說明
```

## 學習資源

- [Nim WebSocket庫](https://github.com/treeform/ws)
- [WebSocket協議](https://developer.mozilla.org/zh-TW/docs/Web/API/WebSocket)
- [前端WebSocket API](https://developer.mozilla.org/zh-TW/docs/Web/API/WebSocket)
- [即時應用設計模式](https://www.amazon.com/Real-Time-Communication-Web-WebRTC-WebSockets/dp/1449371869)

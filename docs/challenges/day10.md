# 第10天：天氣CLI工具

## 專案目標

開發一個命令列天氣查詢工具，使用公共API獲取和顯示當前天氣和天氣預報信息。

## 專案需求

1. 允許使用者通過城市名稱、郵政編碼或地理坐標查詢天氣
2. 顯示以下天氣信息：
   - 當前溫度、體感溫度
   - 天氣狀況（晴、多雲、雨等）
   - 濕度、氣壓和風速
   - 日出和日落時間
3. 提供未來幾天的天氣預報選項
4. 支援溫度單位切換（攝氏度/華氏度）
5. 支援命令列參數和交互式模式
6. 處理API錯誤和網絡問題
7. 提供友好、易讀的輸出格式

## 實現提示

### 基本結構
```nim
# weather_cli.nim
import httpclient, json, parseopt, strutils, strformat, times, terminal

type
  WeatherConfig = object
    apiKey: string
    defaultCity: string
    units: string  # metric, imperial
    forecastDays: int

  WeatherData = object
    cityName: string
    temp: float
    feelsLike: float
    humidity: int
    pressure: int
    windSpeed: float
    description: string
    sunrise: Time
    sunset: Time

proc loadConfig(filename: string): WeatherConfig =
  # 從設定檔加載配置

proc getWeather(city: string, config: WeatherConfig): WeatherData =
  # 從API獲取當前天氣數據

proc getForecast(city: string, days: int, config: WeatherConfig): seq[WeatherData] =
  # 從API獲取天氣預報數據

proc displayWeather(weather: WeatherData, colored: bool = true) =
  # 顯示天氣信息，可選使用彩色輸出

proc displayForecast(forecast: seq[WeatherData], colored: bool = true) =
  # 顯示天氣預報，可選使用彩色輸出

proc getWeatherEmoji(description: string): string =
  # 返回與天氣描述相對應的表情符號

proc parseCommandLine(): tuple[city: string, forecast: bool, days: int, units: string] =
  # 解析命令列參數

proc main() =
  # 主程式邏輯

when isMainModule:
  main()
```

### API集成
- 使用免費的天氣API（如OpenWeatherMap、WeatherAPI等）
- 使用`httpclient`模組發送HTTP請求
- 使用`json`模組解析API響應

### 數據顯示
- 使用`terminal`模組創建彩色輸出
- 使用天氣圖標或ASCII藝術增強視覺效果
- 格式化顯示日期和時間信息

### 錯誤處理
- 處理網絡連接問題
- 處理API錯誤和限制
- 提供有用的錯誤信息和故障排除提示

## 進階挑戰

1. 添加地理位置自動檢測功能
2. 實現天氣警報和通知功能
3. 添加歷史天氣數據查詢
4. 實現天氣變化圖表（使用ASCII圖表）
5. 支援多語言和國際化
6. 添加天氣資訊的本地緩存，減少API調用

## 專案結構

```
day10/
├── weather_cli.nim    # 主程式檔案
├── config.json        # 設定檔（包含API密鑰等）
└── README.md          # 專案說明
```

## 學習資源

- [Nim標準庫：httpclient](https://nim-lang.org/docs/httpclient.html)
- [Nim標準庫：json](https://nim-lang.org/docs/json.html)
- [Nim標準庫：terminal](https://nim-lang.org/docs/terminal.html)
- [OpenWeatherMap API文檔](https://openweathermap.org/api)

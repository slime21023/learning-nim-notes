# 練習 3: 條件處理結構
```nim
# 目標：掌握 Nim 中的條件判斷結構
# 實作要點：
# - 實作具有多層邏輯的 if-elif-else 結構
# - 實作等效的 case 陳述式
# - 比較兩種實作方法的程式碼效率與可讀性

import strutils # 引入字串處理模組
import times # 引入時間處理模組

echo "=== Nim 條件處理結構練習 ==="

# 範例 1: 成績評分系統
proc gradeWithIfElse(score: int): string =
  # 使用 if-elif-else 結構實作成績評分
  if score >= 90:
    return "優"
  elif score >= 80:
    return "良"
  elif score >= 70:
    return "中"
  elif score >= 60:
    return "及格"
  else:
    return "不及格"

proc gradeWithCase(score: int): string =
  # 使用 case 陳述式實作成績評分
  case score
  of 90..100:
    return "優"
  of 80..89:
    return "良"
  of 70..79:
    return "中"
  of 60..69:
    return "及格"
  of 0..59:
    return "不及格"
  else:
    return "無效分數"

# 測試成績評分系統
let testScores = [95, 82, 75, 60, 45]
echo "\n成績評分系統比較："
for score in testScores:
  echo "分數: ", score
  echo "  if-elif-else 結果: ", gradeWithIfElse(score)
  echo "  case 陳述式結果: ", gradeWithCase(score)

# 範例 2: 日期相關判斷
proc getDayTypeWithIfElse(day: string): string =
  # 使用 if-elif-else 判斷日期類型
  let lowercaseDay = day.toLowerAscii()
  
  if lowercaseDay == "saturday" or lowercaseDay == "sunday":
    return "週末"
  elif lowercaseDay in ["monday", "tuesday", "wednesday", "thursday", "friday"]:
    return "工作日"
  else:
    return "無效日期"

proc getDayTypeWithCase(day: string): string =
  # 使用 case 陳述式判斷日期類型
  case day.toLowerAscii()
  of "saturday", "sunday":
    return "週末"
  of "monday", "tuesday", "wednesday", "thursday", "friday":
    return "工作日"
  else:
    return "無效日期"

# 測試日期判斷
let days = ["Monday", "Saturday", "Wednesday", "Sunday", "Holiday"]
echo "\n日期類型判斷比較："
for day in days:
  echo "日期: ", day
  echo "  if-elif-else 結果: ", getDayTypeWithIfElse(day)
  echo "  case 陳述式結果: ", getDayTypeWithCase(day)

```
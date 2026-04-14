# DXA 骨質密度檢查 — 放射師教學工具

## 📁 檔案結構

```
dxa-teaching/
├── index.html          # 主程式（單一檔案，含 JSON 資料 + CSS + JS）
├── img/
│   ├── hip-correct.png
│   ├── hip-overturn.png
│   ├── hip-underturn.png
│   ├── spine-correct.png
│   └── spine-tilted.png
└── README.md           # 本文件
```

## 🏗️ 程式架構

`index.html` 內部結構：

```html
<head>
  <!-- 1. JSON 資料區（修改內容在這裡）-->
  <script id="app-data" type="application/json">
    { cheatsheet, quickNav, teachSections, questions, appendix }
  </script>
  
  <!-- 2. CSS 樣式 -->
  <style>...</style>
</head>
<body>
  <!-- 3. HTML 容器（由 JS 動態生成內容）-->
  <main>
    <div id="pg-teach"></div>
    <div id="pg-quiz"></div>
    <div id="pg-extra"></div>
  </main>
  
  <!-- 4. JavaScript 邏輯 -->
  <script>
    const DATA = JSON.parse(document.getElementById('app-data').textContent);
    // 渲染函數...
  </script>
</body>
```

---

## 📝 JSON 資料格式

### 1. 速查 (cheatsheet)

```json
{
  "cheatsheet": [
    {
      "title": "接單判斷",
      "items": [
        "停經／亂經／男≥50 → 全身 + TBS + FRAX（T-score）",
        "未停經／男<50 → 全身（Z-score）",
        "90歲以上不做 TBS+FRAX"
      ]
    }
  ]
}
```

### 2. 快速跳轉 (quickNav)

```json
{
  "quickNav": [
    {
      "group": "order",           // 對應 teachSections 的 group
      "groupNum": "一",
      "groupTitle": "接單決策",
      "links": [
        {"id": "order1", "label": "① 年齡/經期"},
        {"id": "order2", "label": "② 參考族群"}
      ]
    }
  ]
}
```

### 3. 教學內容 (teachSections)

```json
{
  "teachSections": [
    {
      "group": "order",           // 群組 ID
      "groupNum": "一",           // 顯示編號
      "groupTitle": "接單決策",   // 群組標題
      "cards": [
        {
          "id": "order1",                              // 卡片 ID（對應 quickNav 的 link.id）
          "title": "① 確認年齡 / 經期 → 決定做法",   // 卡片標題
          "content": "<table>...</table>"             // HTML 內容
        }
      ]
    }
  ]
}
```

**content 支援的 HTML 標籤：**
- `<table>` - 表格
- `<ul>` / `<ol>` - 列表
- `<div class="info-box blue">` - 藍色提示框
- `<div class="info-box amber">` - 黃色警告框
- `<ol class="step-ol">` - 步驟列表（帶數字圓圈）
- `<strong>` / `<b>` - 粗體

### 4. 測驗題目 (questions)

```json
{
  "questions": [
    {
      "id": 1,
      "badge": "接單決策 ②",                    // 題目分類標籤
      "scenario": "60歲停經女性，來自美國...",  // 情境描述
      "question": "關於參考族群的設定，...",    // 問題
      "options": ["選項A", "選項B", "選項C", "選項D"],
      "answer": "B",                            // 正確答案（A/B/C/D）
      "link": "section-order2",                 // 連結到教學卡片
      "linkText": "接單 ② 參考族群",           // 連結顯示文字
      "feedback": "解釋文字，可用 <b> 標籤"    // 回饋說明
    }
  ]
}
```

**特殊題型：**

```json
// 含圖片的題目
{
  "images": [
    {"src": "img/hip-overturn.png", "cap": "A"},
    {"src": "img/hip-correct.png", "cap": "B"}
  ]
}

// 含表格的題目（題目3 專用）
{
  "table": true
}
```

### 5. 附錄 (appendix)

```json
{
  "appendix": [
    {
      "id": "A",
      "title": "骨質疏鬆症基礎",
      "content": "<p>...</p><ul>...</ul>"
    }
  ]
}
```

---

## 🔧 常見修改範例

### 新增一道測驗題

在 `questions` 陣列末尾加入：

```json
{
  "id": 11,
  "badge": "接單決策 ①",
  "scenario": "70歲男性，無特殊病史，來做 DXA。",
  "question": "應該如何設定？",
  "options": ["只做 BMD", "全身 + TBS + FRAX", "只做 L-spine", "不需要做"],
  "answer": "B",
  "link": "section-order1",
  "linkText": "接單 ① 年齡/經期",
  "feedback": "男性 ≥ 50歲應做全身 + TBS + FRAX。"
}
```

### 修改教學內容

找到對應的 `teachSections.cards[].content`，修改 HTML 字串。

### 新增速查項目

在 `cheatsheet` 對應的 section.items 陣列中加入新字串。

### 新增附錄

在 `appendix` 陣列末尾加入新物件。

---

## 🤖 給 Claude 的修改指令模板

複製以下模板，貼到 Claude 對話中：

```
請幫我修改 DXA 教學工具的 JSON 資料：

【修改類型】（新增題目 / 修改教學 / 新增速查 / 其他）

【詳細內容】
...

【注意事項】
- 請只修改 <script id="app-data"> 內的 JSON
- 保持 HTML 標籤格式正確（跳脫 < 為 &lt;）
- 題目 answer 只能是 A/B/C/D
- link 要對應現有的 section-xxx ID
```

---

## 🚀 部署方式

### GitHub Pages

1. 建立新 repository（如 `dxa-teaching`）
2. 上傳 `index.html` 和 `img/` 資料夾
3. Settings → Pages → Source 選 `main` branch
4. 網址：`https://username.github.io/dxa-teaching/`

### 醫院內網

1. 將檔案放到網頁伺服器目錄
2. 單檔 + 圖片即可運作，無需後端

---

## 📊 版本紀錄

| 版本 | 日期 | 變更 |
|------|------|------|
| v4.0 | 2026-04-14 | 重構為 JSON 資料架構 |
| v3.x | 之前 | 原始 HTML 手寫版本 |

---

## ⚠️ 注意事項

1. **JSON 語法**：所有字串內的雙引號要用 `\"` 跳脫
2. **HTML 跳脫**：`<` 要寫成 `&lt;`，`>` 要寫成 `&gt;`
3. **圖片路徑**：相對路徑 `img/xxx.png`
4. **ID 命名**：保持一致性（`order1`, `position-a`, `setting6`）

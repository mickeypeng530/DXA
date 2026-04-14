[README (1).md](https://github.com/user-attachments/files/26715925/README.1.md)
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
    { cheatsheet, teachSections, questions, appendix }
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
    // quickNav 自動從 teachSections 生成
    const quickNav = DATA.teachSections.map(section => ({...}));
    // linkText 自動從 teachSections 查找
    function getLinkText(link) {...}
  </script>
</body>
```

### 自動化機制

- **quickNav**：由程式自動從 `teachSections` 生成，不需手動維護
- **linkText**：測驗題目的連結文字自動從教學卡片標題查找

---

## 📝 JSON 資料格式

### 1. 速查 (cheatsheet)

```json
{
  "cheatsheet": [
    {
      "title": "檢查前",
      "items": [
        "排程禁忌：顯影劑 2 天、核醫 48-72 hr、鋇劑/I-131 一週",
        "停經／亂經／男≥50 → 全身 + TBS + FRAX（T-score）"
      ]
    }
  ]
}
```

### 2. 教學內容 (teachSections)

```json
{
  "teachSections": [
    {
      "group": "pre",             // 群組 ID（pre/during/post）
      "groupNum": "一",           // 顯示編號
      "groupTitle": "檢查前",     // 群組標題
      "cards": [
        {
          "id": "pre-schedule",   // 卡片 ID（格式：{group}-{name}）
          "title": "排程禁忌",    // 卡片標題
          "content": "<table>...</table>"  // HTML 內容
        }
      ]
    }
  ]
}
```

**目前架構：**

| 群組 | group | 卡片 |
|------|-------|------|
| 一、檢查前 | `pre` | 排程禁忌、單/多部位、年齡/經期、參考族群、加作規則 |
| 二、檢查中 | `during` | L Spine 擺位、Femur 擺位 |
| 三、檢查後 | `post` | 排除條件、Trending、TBS + FRAX、影像傳輸順序 |

**content 支援的 HTML 標籤：**
- `<table>` - 表格
- `<ul>` / `<ol>` - 列表
- `<div class="info-box blue">` - 藍色提示框
- `<div class="info-box amber">` - 黃色警告框
- `<ol class="step-ol">` - 步驟列表（帶數字圓圈）
- `<strong>` / `<b>` - 粗體

### 3. 測驗題目 (questions)

```json
{
  "questions": [
    {
      "id": 1,
      "badge": "檢查前",                          // 題目分類標籤
      "scenario": "60歲停經女性，來自美國...",    // 情境描述
      "question": "關於參考族群的設定，...",      // 問題
      "options": ["選項A", "選項B", "選項C", "選項D"],
      "answer": "B",                              // 正確答案（A/B/C/D）
      "link": "section-pre-ethnic",               // 連結到教學卡片
      "feedback": "解釋文字，可用 <b> 標籤"       // 回饋說明
    }
  ]
}
```

> **注意**：不需要 `linkText` 欄位，程式會自動從 `teachSections` 查找對應的卡片標題。

**特殊題型：**

```json
// 含圖片的題目
{
  "images": [
    {"src": "img/hip-overturn.png", "cap": "A"},
    {"src": "img/hip-correct.png", "cap": "B"}
  ]
}

// 含表格的題目
{
  "table": true
}
```

### 4. 附錄 (appendix)

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

**目前附錄：**
- A｜骨質疏鬆症基礎
- B｜DXA 原理
- C｜T-Score 與 Z-Score
- D｜TBS 簡介與分級
- E｜FRAX 風險評估（詳細）
- F｜醫令碼與自費價目
- G｜輻射劑量比較

---

## 🔧 常見修改範例

### 新增一道測驗題

在 `questions` 陣列末尾加入：

```json
{
  "id": 11,
  "badge": "檢查前",
  "scenario": "70歲男性，無特殊病史，來做 DXA。",
  "question": "應該如何設定？",
  "options": ["只做 BMD", "全身 + TBS + FRAX", "只做 L-spine", "不需要做"],
  "answer": "B",
  "link": "section-pre-age",
  "feedback": "男性 ≥ 50歲應做全身 + TBS + FRAX。"
}
```

### 修改教學內容

找到對應的 `teachSections.cards[].content`，修改 HTML 字串。

### 新增速查項目

在 `cheatsheet` 對應的 section.items 陣列中加入新字串。

### 新增附錄

在 `appendix` 陣列末尾加入新物件。

### 新增教學卡片

在對應的 `teachSections[].cards` 陣列中加入：

```json
{
  "id": "pre-newcard",      // 格式：{group}-{name}
  "title": "新卡片標題",
  "content": "<p>內容...</p>"
}
```

> quickNav 會自動更新，不需手動修改。

---

## 🤖 給 Claude 的修改指令模板

複製以下模板，貼到 Claude 對話中：

```
請幫我修改 DXA 教學工具的 JSON 資料：

【修改類型】（新增題目 / 修改教學 / 新增速查 / 新增附錄 / 其他）

【詳細內容】
...

【注意事項】
- 請只修改 <script id="app-data"> 內的 JSON
- 保持 HTML 標籤格式正確（跳脫 < 為 &lt;）
- 題目 answer 只能是 A/B/C/D
- link 格式為 section-{group}-{name}，例如 section-pre-age
- 不需要寫 linkText，程式會自動查找
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
| v5.0 | 2026-04-14 | 架構重組（檢查前/中/後）、quickNav 自動生成、linkText 自動化、新增附錄 F/G、擴充附錄 D |
| v4.1 | 2026-04-14 | Lucide icon（Nav/速查/開關燈/詳解連結）、預設深色模式、跳轉位置修正（二次滾動） |
| v4.0 | 2026-04-14 | 重構為 JSON 資料架構（方案 C） |
| v3.x | 之前 | 原始 HTML 手寫版本 |

---

## 📋 待辦事項（TODO）

### 內容補充

- [ ] 新增更多測驗題（針對排除條件、影像傳輸順序）
- [ ] 補充 VFA 椎體骨折評估的詳細說明
- [ ] 補充身體組成分析（Body Composition）的操作流程

### 功能優化

- [ ] 測驗結果匯出功能
- [ ] 學習進度追蹤（localStorage）
- [ ] 錯題收藏功能

---

## ⚠️ 注意事項

1. **JSON 語法**：所有字串內的雙引號要用 `\"` 跳脫
2. **HTML 跳脫**：`<` 要寫成 `&lt;`，`>` 要寫成 `&gt;`
3. **圖片路徑**：相對路徑 `img/xxx.png`
4. **ID 命名規則**：統一使用 `{group}-{name}` 格式
   - 檢查前：`pre-schedule`, `pre-site`, `pre-age`, `pre-ethnic`, `pre-add`
   - 檢查中：`during-spine`, `during-hip`
   - 檢查後：`post-exclude`, `post-trending`, `post-tbs`, `post-transfer`
5. **link 格式**：測驗題目的 link 為 `section-{id}`，例如 `section-pre-age`

# Remon · 月報工作流

> 用 Claude 驅動的 AI 產業動態月報系統
> 蒐集 → 篩選 → 撰寫 → 發布 | 全流程對話式操作

---

## 📐 架構一覽

```
remon/
├── index.html              ← GitHub Pages 首頁（模板，不需每月改）
├── data/
│   ├── index.json          ← 月份索引（讓網頁知道有哪幾期）
│   ├── 2026-04.json        ← 月報資料（每個月一份）
│   └── 2026-05.json
├── drafts/                 ← 給 Claude 篩選的草稿
│   └── selection_2026-04.md
└── README.md
```

**技術棧**：純 HTML + CSS + 原生 JavaScript + JSON。
**不需要**：資料庫、後端、build tool、框架、Node.js。
**部署**：GitHub Pages（免費，推 `main` branch 自動更新）。

---

## 🔁 每月工作流（四步）

### Step 1 · 蒐集
> 對 Claude 說：「Remon，幫我產生 2026 年 5 月月報，時間範圍 5/1 到 5/20」

Claude 會：
- 針對三個分類分別做多輪 web search
- 去重、分類、摘要
- 產出 `drafts/selection_YYYY-MM.md`（Markdown 勾選清單）

### Step 2 · 篩選（你來做）
1. 打開 `drafts/selection_YYYY-MM.md`
2. 要入選的項目，把 `- [ ]` 改成 `- [x]`
3. 要做深度分析的項目（每類 1–2 則），在標題後加 `#deep`
4. 存檔

### Step 3 · 撰寫
> 對 Claude 說：「Remon，根據 `selection_2026-05.md` 產生月報 JSON」

Claude 會：
- 讀取你的勾選結果
- 為每則入選新聞寫 `one_liner`（一句話重點）
- 為 `#deep` 項目寫 1–2 段深度分析（含對台灣/你工作脈絡的連結）
- 輸出 `data/2026-05.json`

### Step 4 · 發布
```bash
# 把新的 JSON 加入索引
# （或請 Claude 幫你更新 data/index.json）
git add data/2026-05.json data/index.json
git commit -m "月報 2026-05"
git push
```

30 秒後 GitHub Pages 自動更新。

---

## 💬 Claude 指令範本（直接複製）

### 【指令 1】蒐集

```
Remon 月報蒐集任務

月份：2026 年 5 月
時間範圍：2026-05-01 至 2026-05-20
輸出檔名：drafts/selection_2026-05.md

三個分類：
1. 算力建設（國家級、與民間合作模式）
   關鍵字：national AI compute、sovereign AI、data center、GPU cluster、
   CHIPS Act、AI infrastructure、submarine cable（算力連結）、
   台灣數位港、算力中心、AI 資料中心

2. LLM 技術、治理與應用
   關鍵字：LLM release、frontier model、AI governance、AI Act、
   model evaluation、benchmark、alignment、AI 政策、生成式 AI 治理

3. AI 應用開發平台
   關鍵字：AI agent platform、AI SDK、MCP、LangChain、Vercel AI、
   Azure AI Foundry、Amazon Bedrock、agent workflow、AI 應用開發

搜尋要求：
- 每類至少搜 3 輪不同 query
- 優先原始來源（公司公告、政府新聞稿、一流媒體）
- 排除重複、排除過期資訊（> 指定範圍）
- 每類產出 8–15 則候選
- 格式按照現有 selection_2026-04.md 範本
```

### 【指令 2】撰寫月報

```
Remon 月報撰寫任務

輸入：drafts/selection_2026-05.md（已勾選）
輸出：data/2026-05.json

撰寫規則：
- summary_headlines：所有勾選項目都要納入，one_liner 用 1 句中文總結重點（≤ 40 字）
- deep_analyses：只納入標 #deep 的項目，每則寫 2–3 段
  · 第 1 段：事件本身與關鍵事實
  · 第 2 段：產業意涵或政策邏輯
  · 第 3 段（可選）：對台灣 / 對 NRI Digital Port 類型計畫的啟示
- 筆調：NRI 顧問風格，客觀、具分析性、避免口語
- 同時更新 data/index.json，加入本月條目
```

### 【指令 3】常見調整

```
# 單類補料
「Part I 要聞太少，再補 5 則關於 sovereign AI 的新聞」

# 改寫某則分析
「Part II 的深度分析第 1 則，請改從技術面（而非治理面）切入」

# 重排優先順序
「要聞排序改為：台灣/亞太 > 美國 > 歐洲 > 其他」
```

---

## 🏗️ JSON 資料結構

見 `data/2026-04.json`。關鍵欄位：

| 欄位 | 說明 |
|------|------|
| `categories.<key>.label` | 分類名稱（顯示用） |
| `categories.<key>.sublabel` | 副標（可選） |
| `categories.<key>.summary_headlines[]` | 要聞清單（全部入選新聞） |
| `categories.<key>.deep_analyses[]` | 深度分析（1–2 則） |

三個分類的 key（勿改動，網頁寫死）：
- `compute_infrastructure`
- `llm_tech_governance`
- `ai_app_platform`

---

## 🚀 GitHub Pages 部署（首次）

1. 在 GitHub 建立 repo（例如 `remon-monthly`）
2. 把本資料夾所有檔案推上去
3. Repo → Settings → Pages
4. Source 選 `main` branch / `/ (root)`
5. 幾分鐘後會得到 `https://<your-username>.github.io/remon-monthly/`

（若想用自訂網域或放私人 repo，可另外討論）

---

## 🔮 之後可以擴充

- [ ] 下拉選單選擇歷史月份（讀 `data/index.json`）
- [ ] 每類新增「趨勢標籤」自動分群（如 #sovereign-AI、#governance）
- [ ] 年度彙整頁（從多個月 JSON 自動產出年終回顧）
- [ ] RSS feed 輸出
- [ ] 加上搜尋框

---

_Last updated: 2026-04-16_

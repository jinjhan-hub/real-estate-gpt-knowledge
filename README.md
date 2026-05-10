# 房仲 GPT Knowledge 資料庫

這是一套給房仲 GPTs、GitHub Knowledge、外部 RAG 或第三方工具調用的模組化資料庫。

## 設計目的

1. 不把所有規則塞進單一 GPT Instructions，降低上下文負擔。
2. 讓不同 GPT 可以共用同一套規則、模板與風格。
3. 依任務精準載入所需檔案，例如只做 FB 文案時不載入平面圖轉換規則。
4. 保留隨機性，但把品質底線固定住。
5. 本版專注在圖卡、文案、周邊機能、格局圖、房市觀點、稅務試算器與 API / OpenAPI 工具開發。

## 目錄結構

- `manifest.json`：資料庫總覽與模組索引
- `00_index/`：任務索引、路由表、通用變數
- `01_global/`：全域硬限制、確認流程、事實來源、輸出語氣
- `02_sales_generator/`：銷售圖卡與社群文案
- `03_nearby_facilities/`：周邊機能、地址與經緯度、API 查詢結果整理
- `04_floorplan_converter/`：手繪格局圖轉數位平面圖
- `06_market_content/`：房市新聞、政策、報稅、房貸觀點文
- `07_tax_calculator/`：房地合一稅、土地增值稅試算器規格
- `08_api_actions/`：API、OpenAPI、Vercel Actions、GitHub Knowledge 載入規則
- `99_examples/`：範例資料
- `INSTRUCTIONS_MASTER_REFERENCE.txt`：GPTs 主控 Instructions 參考版

## 使用方式

建議 GitHub 目錄結構保持不變。第三方工具或 GPT Actions 可先讀取：

- `manifest.json`
- `00_index/00_PROMPT_INDEX.txt`
- `00_index/01_ROUTING_TABLE.txt`
- `01_global/00_GLOBAL_HARD_RULES.txt`
- `01_global/01_CONFIRMATION_FLOW.txt`
- `01_global/02_FACT_AND_SOURCE_POLICY.txt`
- `01_global/03_OUTPUT_STYLE_BASELINE.txt`

接著依照使用者任務載入對應模組，例如：

- 產製銷售圖卡與文案：載入 `02_sales_generator/`
- 查詢周邊機能：載入 `03_nearby_facilities/`
- 轉換手繪格局圖：載入 `04_floorplan_converter/`
- 產製房市新聞觀點文：載入 `06_market_content/`
- 稅務試算器開發與修正：載入 `07_tax_calculator/`
- API / OpenAPI / Vercel Actions：載入 `08_api_actions/`

## 載入原則

- 不要一次載入全部 Knowledge。
- 先讀索引與全域規則。
- 再依任務載入對應模組。
- 若任務跨模組，先判斷主模組，再補讀輔助模組。
- 若找不到對應模組，不要硬套錯誤規則，應回到全域規則並請使用者補充需求。

## 建議維護規則

- 新增模板時，放入對應模組，不要改全域規則。
- 重大限制才放入 `01_global/`。
- 每個檔案只處理一類任務，避免一個檔案過長。
- 案例可放 `99_examples/`，讓模型學風格，不要把案例混進硬規則。
- 不要把單一物件資料、個人名片資訊或一次性任務寫進通用規則。
- 修改檔案後需 commit changes，確保版本有被保存。
- 若修改 API 契約，需同步檢查 OpenAPI、Vercel API 與 GPT Actions。

## 版本管理建議

每次修改可用簡短 commit message，例如：

- `update sales generator rules`
- `update nearby facilities module`
- `update tax calculator specs`
- `update api contract`
- `refine global hard rules`

2026-05-09 更新：
完成周邊機能 API 串接。
API endpoint：https://real-estate-nearby-api.vercel.app/api/nearby-facilities
支援分類：school、transport、shopping、park、medical
限制：預設一次查詢 school、transport、shopping、park、medical 五種 categories
Request 欄位：address、lat、lng、radius、categories
新增 summary 欄位，供 GPT Actions 直接回覆使用。

## 注意事項

本資料庫用於固定流程、限制、風格與輸出品質，不代表所有輸出都必須完全一致。  
實際文案仍應依物件條件、平台、風格、使用者需求與可確認資料調整。

# Real Estate GPT System｜版本更新歷程

## v1.0｜單一 Prompt GPTs

初始版本。

架構：

* 單一 Instructions
* 單一 Knowledge
* 固定 Prompt
* 靜態輸出

特徵：

* 所有規則集中在 GPTs Instructions
* 所有知識集中於單一 Knowledge
* 無法動態載入
* 容易 context 汙染
* 長對話穩定性低

主要功能：

* 房仲圖卡生成
* FB / IG / Threads 文案
* 基礎格局圖轉換

---

# v2.0｜Knowledge Modularization

開始拆分 Knowledge。

新增：

* Template Library
* Style Library
* CTA Library
* Platform Rules

改善：

* 降低單一 Knowledge 過大問題
* 提高文案風格控制
* 提高平台差異化輸出

開始建立：

* 投資理性感
* 自住溫暖感
* 首購友善感
* 高質感精品感

---

# v3.0｜Dynamic Knowledge API

架構重大升級。

新增：

* GitHub Knowledge Database
* Vercel API
* GPT Actions

架構：

GPTs
→ Actions
→ Vercel API
→ GitHub Modules

改善：

* 不再依賴單一 GPTs Knowledge
* 開始支援 Dynamic Knowledge Loading
* 可外部更新知識庫
* 降低 GPTs context 壓力

完成：

* `/api/knowledge-module`
* `getKnowledgeModule`

---

# v4.0｜Folder-based Modules

開始建立真正模組化架構。

新增：

* sales_generator
* nearby_facilities
* floorplan_converter
* api_actions
* examples

改善：

* 模組分離
* 不同任務獨立知識庫
* 避免不同功能互相污染

開始支援：

* Folder-based Knowledge
* Multi-file Retrieval

---

# v5.0｜Granular Section Loading

完成：

Section-level Retrieval。

支援：

```txt
module=sales_generator&section=08_FB_TEMPLATE
```

改善：

* 每次只載入必要 section
* 大幅降低 context 汙染
* 提高輸出穩定性
* 提高文案風格一致性

開始拆分：

* FB Template
* IG Template
* Threads Template
* CTA Library
* Hook Library
* Observation Library

---

# v6.0｜Workflow System

建立 Workflow Architecture。

新增：

* Property Confirmation Workflow
* Platform Selection Workflow
* Style Selection Workflow
* Image Generation Workflow
* Copy Generation Workflow
* Final Review Workflow

改善：

* GPT 開始具備流程控制
* 不再直接跳生成
* 圖卡與文案開始分離
* 生成順序開始可控

完成：

* Workflow Routing
* Workflow Modules
* Image / Copy Separation

---

# v6.5｜Workflow Enforcement

加入：

Hard Blocking Rules。

改善：

* 禁止模糊文案繞過
* 禁止資料不足直接生成
* 禁止自行補資料
* 強制資料確認

開始具備：

* Safety Enforcement
* Generation Blocking
* Confirmation-first Generation

正式建立：

```txt
Workflow > Generation
```

架構。

---

# v7.0｜State Machine Architecture

建立：

Conversation State System。

新增：

* PROPERTY STATE
* PLATFORM STATE
* STYLE STATE
* IMAGE STATE
* COPY STATE
* FINAL REVIEW STATE
* ERROR RECOVERY STATE

改善：

* 多輪對話穩定性
* 降低重複詢問
* 降低流程跳步
* 降低 context 混亂

開始具備：

* Multi-turn Workflow Control
* Conversational Pipeline
* Error Recovery
* State Persistence Logic

正式從：

```txt
Prompt System
```

進化為：

```txt
Conversational Agent Framework
```

---

# 目前架構（v7.0）

目前系統：

GPTs Instructions
→ Router / Workflow / State Control Layer

GPT Actions
→ getKnowledgeModule
→ searchNearbyFacilities

Vercel API
→ knowledge-module
→ nearby-facilities

GitHub
→ Dynamic Knowledge Database

目前能力：

* Dynamic Modular Knowledge
* Folder-based Modules
* Granular Section Loading
* Workflow Enforcement
* State Machine Control
* Image / Copy Separation
* Few-shot Retrieval
* Hook Injection
* Emotional Transition Injection
* Platform-specific Generation

---

# 下一階段規劃（v8+）

預計新增：

* Session Memory
* Auto Context Compression
* Smart Defaults
* Self Evaluation
* Response Scoring
* Multi-Agent Routing
* Retry Recovery
* Auto Tool Arbitration

目標：

建立：

```txt
Autonomous Real Estate Content Agent System
```


## v0.2.3｜GitHub Knowledge 修正版

本版修正重點：

- 同步 v0.2.2 銷售圖卡修正：FB圖文 = 先圖卡後文案。
- 新增三段式圖片生成流程：資料確認 → 產圖設定 → 圖片生成。
- 新增短版產圖摘要與產圖失敗降級機制。
- 補強名片、人物照片與 QR Code 條件式使用規則。
- 新增 `02_sales_generator/23_ERROR_CASES.txt`。
- 修正 `merged_upload_files`，確保 `02_SALES_GENERATOR_MASTER.txt` 與 `08_API_ACTIONS_MASTER.txt` 正確合併，不是空檔。
- 統一周邊機能 API 欄位為 `lat` / `lng`。
- 周邊機能 API categories 固定支援：`school`、`transport`、`shopping`、`park`、`medical`，預設一次查詢五種固定類別。
- API 只回傳文字規則與查詢資料，不產圖、不合成圖、不輸出 PNG / PDF。

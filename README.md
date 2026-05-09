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
限制：每次最多查詢 2 種 categories
新增 summary 欄位，供 GPT Actions 直接回覆使用。

## 注意事項

本資料庫用於固定流程、限制、風格與輸出品質，不代表所有輸出都必須完全一致。  
實際文案仍應依物件條件、平台、風格、使用者需求與可確認資料調整。

# 房仲 GPT Knowledge 資料庫

這是一套給房仲 GPTs、GitHub Knowledge、外部 RAG 或第三方工具調用的模組化資料庫。

## 設計目的

1. 不把所有規則塞進單一 GPT Instructions，降低上下文負擔。
2. 讓不同 GPT 可以共用同一套規則、模板與風格。
3. 依任務精準載入所需檔案，例如只做 FB 文案時不載入平面圖轉換規則。
4. 保留隨機性，但把品質底線固定住。
5. 本版專注在圖卡、文案、周邊機能、格局圖、房市觀點與工具開發。

## 使用方式

建議 GitHub 目錄結構保持不變。第三方工具或 GPT Actions 可先讀取：

- `00_index/00_PROMPT_INDEX.txt`
- `00_index/01_ROUTING_TABLE.txt`
- `01_global/00_GLOBAL_HARD_RULES.txt`
- `01_global/01_CONFIRMATION_FLOW.txt`

接著依照使用者任務載入對應模組，例如：

- 產製銷售圖卡與文案：載入 `02_sales_generator/`
- 查詢周邊機能：載入 `03_nearby_facilities/`
- 轉換手繪格局圖：載入 `04_floorplan_converter/`
- 產製房市新聞觀點文：載入 `06_market_content/`
- 稅務試算器開發與修正：載入 `07_tax_calculator/`
- API / OpenAPI / Vercel Actions：載入 `08_api_actions/`

## 建議維護規則

- 新增模板時，放入對應模組，不要改全域規則。
- 重大限制才放入 `01_global/`。
- 每個檔案只處理一類任務，避免一個檔案過長。
- 案例可放 `99_examples/`，讓模型學風格，不要把案例混進硬規則。

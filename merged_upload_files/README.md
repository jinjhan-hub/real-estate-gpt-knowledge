# MERGED UPLOAD FILES｜GPTs Knowledge 上傳用合併檔

這個資料夾是給 GPTs Knowledge 上傳用的合併版本。  
原始可維護檔案仍在各 module 資料夾中。

建議上傳：

- `00_INDEX_AND_GLOBAL_RULES.txt`
- `02_SALES_GENERATOR_MASTER.txt`
- `03_NEARBY_FACILITIES_MASTER.txt`
- `04_FLOORPLAN_CONVERTER_MASTER.txt`
- `06_MARKET_CONTENT_MASTER.txt`
- `07_TAX_CALCULATOR_MASTER.txt`
- `08_API_ACTIONS_MASTER.txt`
- `99_EXAMPLES_MASTER_OPTIONAL.txt`（可選）
- `manifest.json`

v0.2.3 修正：

- `02_SALES_GENERATOR_MASTER.txt` 已正確合併銷售模組與 `23_ERROR_CASES.txt`。
- `08_API_ACTIONS_MASTER.txt` 已正確合併 API 規則，且周邊機能 API 欄位統一為 `lat` / `lng`。
- 圖片生成規則採三段式流程，並區分 FB圖文、IG圖文、銷售圖卡與純文案任務。
- API 只負責文字規則與查詢資料，不產圖、不合成圖、不輸出 PNG / PDF。

# v8 拆分上傳說明｜Real Estate GPT Knowledge

本 ZIP 以 GitHub repo 根目錄為起點製作。

## 使用方式

1. 下載 ZIP 並解壓縮。
2. 將解壓後的所有資料夾與檔案，上傳到 GitHub repo 根目錄。
3. 若 GitHub 顯示同名檔案，選擇覆蓋。
4. Commit message 建議填：`v0.2.5 granular sales generator structure`
5. 上傳後檢查以下路徑是否存在：
   - `02_sales_generator/platforms/FB_RULES.txt`
   - `02_sales_generator/sizes/RATIO_4_5.txt`
   - `02_sales_generator/styles/WARM_HOME_STYLE.txt`
   - `02_sales_generator/workflows/IMAGE_GENERATION_WORKFLOW.txt`
   - `02_sales_generator/templates/IMAGE_PROMPT_TEMPLATE.txt`

## 本版目標

將銷售圖卡與社群文案規則拆成「平台、尺寸、風格、流程」四條線，降低一次性載入過多提示詞造成的 context 汙染與圖片生成卡住問題。



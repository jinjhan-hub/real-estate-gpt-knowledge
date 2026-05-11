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

## 注意

GitHub 網頁版通常不會自動解壓 ZIP。請先在電腦解壓後，再整批拖拉資料夾與檔案上傳。

本 ZIP 只更新 Knowledge 檔案，不會自動修改 Vercel API 程式碼。若你的 API 目前只支援 `module=sales_generator&section=舊檔名`，短期仍可用相容舊檔；要完全發揮精準調用效果，後續需再更新 Vercel loader 讓它支援 platform / ratio / style / stage 參數。

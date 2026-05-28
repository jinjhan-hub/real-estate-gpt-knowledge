# 房仲銷售圖卡 Lite v1 Stage Rules

本資料夾用於「台灣房仲 FB 4:5 銷售圖卡生成 GPT｜Lite 分段規則版」。

本版本參考既有穩定版 `fb_card_public` 規則邏輯，並調整為 Lite 架構使用。

---

## 一、唯一支援任務

本規則只支援：

- 台灣房仲 FB 4:5 銷售圖卡

不支援：

1. FB 長文
2. IG 貼文
3. Threads 貼文
4. 短影音腳本
5. 周邊機能圖
6. 格局圖
7. 稅務試算
8. 新聞分析
9. 非房仲銷售圖卡任務

---

## 二、Lite 架構分工

GPTs 負責：

1. 主控對話流程
2. 判讀使用者本輪上傳素材
3. 整理物件資料
4. 合規檢查
5. 風格選擇
6. 產圖前最終摘要
7. 組合圖片生成提示詞
8. 在 GPTs 對話端生成圖片
9. 產圖後人工檢查與修正重產

API 只負責：

1. `verifyStore`
2. `getStageRules`

Supabase 只負責：

1. 店家授權
2. 功能權限
3. 有效期限
4. 經紀業名稱
5. 經紀人姓名
6. 經紀人證號

GitHub markdown 只負責：

1. 各階段規則
2. 判讀標準
3. 合規檢查
4. 風格規則
5. 產圖提示詞規則
6. 人工檢查規則

---

## 三、最高原則

1. 未通過 `verifyStore`，不得載入任何 stage rules。
2. `verifyStore.disclosure` 是唯一合法公版揭露資料來源。
3. 不預設任何經紀業名稱、經紀人姓名、經紀人證號。
4. 不自行補物件資料。
5. 不生成 QR Code。
6. 不生成未提供的房屋、室內、外觀、街景、地圖、人物。
7. 圖片、名片、QR Code、物件照片、PDF、591 截圖、樂屋截圖，只存在 GPTs 對話中。
8. API / Supabase 不儲存使用者素材、圖片路徑、圖片 URL、base64、propertyData、finalImagePrompt、imagePackage、generationResult。
9. 每一階段必須先呼叫 `getStageRules(stage)` 取得本階段規則。
10. 不得跳過使用者確認。
11. 不得在合規檢查未通過時產圖。
12. 不得在使用者未選擇風格時產圖。
13. 不得在最終產圖摘要未完成時產圖。

---

## 四、disclosure 欄位對應

`verifyStore` 回傳的 disclosure 對應如下：

- `brokerageName` = 經紀業名稱
- `brokerName` = 經紀人姓名
- `brokerLicenseNo` = 經紀人證號

正式圖卡必須使用 disclosure 作為公版揭露資料來源。

名片、PDF、截圖、使用者文字不得覆蓋 disclosure。

若 disclosure 缺少任一欄位，停止製作圖卡。

---

## 五、Lite stage 檔案

本資料夾包含以下階段規則：

1. `material_check.md`
   - 檢查使用者本輪上傳素材狀態

2. `property_extract.md`
   - 整理物件資料，禁止推測與補資料

3. `compliance_check.md`
   - 檢查公版揭露、物件資料真實性、照片、人物、QR Code、廣告用語

4. `style_selection.md`
   - 提供固定 7 種圖卡風格

5. `image_prompt.md`
   - 組合正式圖片生成提示詞

6. `final_generation_brief.md`
   - 產圖前最終摘要層，建立本次唯一產圖依據

7. `manual_review.md`
   - 產圖後人工檢查清單

---

## 六、正式流程順序

GPTs 必須依序執行：

1. 店家認證：`verifyStore`
2. 素材收集
3. 素材檢查：`getStageRules(material_check)`
4. 物件資料整理：`getStageRules(property_extract)`
5. 使用者確認物件資料
6. 合規檢查：`getStageRules(compliance_check)`
7. 風格選擇：`getStageRules(style_selection)`
8. 使用者選擇風格
9. 圖片提示詞規則：`getStageRules(image_prompt)`
10. 最終產圖摘要：`getStageRules(final_generation_brief)`
11. GPTs 對話端生成圖片
12. 人工檢查：`getStageRules(manual_review)`
13. 若不通過，依人工檢查結果修正重產

---

## 七、禁止舊流程

Lite 版不得使用以下舊流程概念：

1. sessionId
2. currentStage
3. nextStage
4. nextAction
5. mustCallNext
6. startSession
7. updateMaterialStatus
8. savePropertyData
9. confirmPropertyData
10. saveComplianceCheck
11. selectImageStyle
12. saveFinalImagePrompt
13. saveImagePolicies
14. generateImage API
15. completeGeneration
16. imagePackage

---

## 八、資料保存限制

本規則允許 GPTs 在本次對話中暫時整理：

1. 素材狀態
2. 物件資料
3. 名片與聯絡資訊
4. 合規檢查結果
5. 使用者選擇風格
6. 最終產圖摘要
7. 最終圖片提示詞

但禁止將上述內容寫入 API、Supabase、GitHub 或任何外部資料庫。

---

## 九、核心安全邏輯

正式圖卡不得包含：

1. 不實資訊
2. 未確認資料
3. 誇大用語
4. 保證獲利
5. 保證增值
6. 保證貸款
7. 保證收益
8. 絕對化比較
9. 錯誤經紀業名稱
10. 錯誤經紀人姓名
11. 錯誤經紀人證號
12. 假 QR Code
13. 未提供的房屋、室內、外觀、街景或地圖
14. 未提供的人物
15. 被 AI 重畫、換臉、卡通化或改五官的人物

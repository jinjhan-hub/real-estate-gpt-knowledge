# 06｜Image Generation Failsafe Rule
# ruleKey: 06_image_generation_failsafe
# requiredStage: IMAGE_POLICIES / IMAGE_GENERATION_READY
# nextAction: saveImagePolicies / generateImage
# mustCallNext: saveImagePolicies / generateImage

本規則用於「圖片生成安全規則與 failsafe 階段」。

本階段目標：
1. 在圖片生成前再次檢查 final_image_prompt。
2. 強制禁止 AI 生成 QR Code。
3. 強制禁止 AI 生成未授權的證號、地址、電話或揭露文字。
4. 強制禁止假人物、假物件照片、假聯絡資訊。
5. 確保 generateImage 後必須等待人工檢查。
6. 人工檢查未通過時，不得呼叫 completeGeneration。

---

## 一、流程硬鎖

進入本階段時，必須遵守：

1. 未保存 image policies 前，不得呼叫 generateImage。
2. 未成功 generateImage 前，不得呼叫 completeGeneration。
3. generateImage 後必須停止，等待人工檢查。
4. 人工檢查未通過，不得呼叫 completeGeneration。
5. 不得使用 ChatGPT 內建圖片生成工具取代 API 流程。
6. 不得使用 image_gen.text2im，除非本專案流程明確要求。
7. 不得把圖片 URL、圖片路徑、base64 存入 API / DB，除非 API 回傳流程本身已設計處理。
8. 不得修改已完成 session。
9. 不得重開已完成 session。
10. 不得重跑 B1、B2、B3。
11. 不得跳關。

---

## 二、QR Code Failsafe｜改為預留區

圖片生成階段禁止生成 QR Code。

無論使用者是否提供 QR Code：

1. 不得生成 QR Code。
2. 不得重繪 QR Code。
3. 不得仿製 QR Code。
4. 不得產生類 QR Code 黑白方塊圖樣。
5. 不得產生看似可掃描但實際失效的假 QR Code。
6. 只能在版面上預留 QR Code 放置區。
7. QR Code 放置區應保持乾淨、方正、留白足夠，方便後製貼上原始 QR Code。
8. 若圖片中出現任何 AI 生成的 QR Code 或類 QR Code 圖樣，人工檢查必須判定 failed。
9. 不得描述 QR Code 的黑白圖樣細節。
10. 不得要求模型「照著 QR Code 畫出來」。
11. 不得將 QR Code 圖檔交給 AI 重繪。
12. 真正 QR Code 必須由後製流程或人工貼上原始 QR Code 圖檔。

final_image_prompt 必須包含：

```text
請預留 QR Code 放置區，不要生成 QR Code 圖樣。
```

人工檢查時：

若圖卡出現 AI 生成 QR Code，failedReason 必須包含：

```text
QR_CODE_GENERATED_BY_AI
```

可追加 failedReason：

```text
FAKE_QR_CODE
QR_CODE_REDRAWN
QR_CODE_LIKE_PATTERN
```

---

## 三、營業員證號與聯絡資訊 Failsafe

圖片生成階段不得產生未授權的營業員或公司聯絡資訊。

禁止事項：

1. 不得生成未提供的營業員證號。
2. 不得生成未提供的營業員電話。
3. 不得生成未提供的公司地址。
4. 不得生成未提供的公司電話。
5. 不得生成未提供的經紀業字號。
6. 不得生成未提供的經紀人字號。
7. 不得生成看似正式的假營業員證號。
8. 不得生成看似正式的假經紀業字號。
9. 若名片文字不清楚，不得在圖卡上補出推測內容。
10. 圖卡中的營業員證號必須來自名片、使用者輸入或 API disclosure。
11. 圖卡中的公司地址與公司電話必須來自名片、使用者輸入或 API disclosure。
12. 不得使用其他案例殘留資料。
13. 不得使用測試資料。
14. 不得使用其他店家或其他業務資料。
15. 不得根據品牌、縣市、店名自行推測揭露資訊。

若圖片中出現來源不明的營業員證號、經紀業字號、公司地址或公司電話，人工檢查必須判定 failed。

failedReason：

```text
WRONG_AGENT_LICENSE_NO
UNVERIFIED_AGENT_LICENSE_NO
WRONG_BROKER_LICENSE_TEXT
UNAUTHORIZED_DISCLOSURE_TEXT
UNVERIFIED_COMPANY_CONTACT
WRONG_COMPANY_CONTACT
```

---

## 四、人物生成 Failsafe

圖片生成階段必須遵守人物素材限制。

若使用者沒有提供人物照：

1. 不得生成真人業務。
2. 不得生成看似真實房仲人物。
3. 不得生成拿名片、拿牌子、站在房屋前的假業務。
4. 不得生成與使用者或品牌相關的假人像。
5. 可使用純版面、房屋照片、圖形元素、文字排版。

若使用者有提供人物照：

1. 不得改變人物身份。
2. 不得生成不同臉孔。
3. 不得過度美化導致不像本人。
4. 不得生成多餘人物。
5. 不得將人物放入誤導性場景。

若圖片中出現未授權假人物，人工檢查必須判定 failed。

failedReason：

```text
FAKE_PERSON_GENERATED
UNAUTHORIZED_PERSON_GENERATION
WRONG_PERSON_IDENTITY
```

---

## 五、物件照片 Failsafe

圖片生成階段必須保護物件照片真實性。

1. 必須以使用者提供的物件照片為主。
2. 不得替換成其他房屋。
3. 不得生成不存在的外觀。
4. 不得生成不存在的室內空間。
5. 不得改變房屋結構造成誤導。
6. 不得生成不存在的車庫、庭院、電梯、景觀。
7. 不得把其他物件照片混入本案。
8. 不得將範例圖當成本案照片。
9. 不得用 AI 想像圖取代真實照片。

若圖片中物件照片不正確，人工檢查必須判定 failed。

failedReason：

```text
WRONG_PROPERTY_PHOTO
FAKE_PROPERTY_PHOTO
PROPERTY_PHOTO_REPLACED
UNAUTHORIZED_IMAGE_ELEMENT
```

---

## 六、文字生成 Failsafe

圖片中的文字必須遵守：

1. 只能使用 final_image_prompt 內已確認資料。
2. 不得生成未提供的價格。
3. 不得生成未提供的坪數。
4. 不得生成未提供的地址。
5. 不得生成未提供的格局。
6. 不得生成未提供的屋齡。
7. 不得生成未提供的車位。
8. 不得生成未提供的證號。
9. 不得生成未提供的電話。
10. 不得生成未提供的公司地址。
11. 不得生成未提供的公司電話。
12. 不得生成未提供的經紀業字號。
13. 不得出現測試字樣。
14. 不得出現測試業務。
15. 不得出現測試電話。
16. 不得出現其他案例殘留資訊。

若圖片文字與 final_image_prompt 或已確認資料不一致，人工檢查必須判定 failed。

failedReason：

```text
WRONG_TEXT_GENERATED
UNAUTHORIZED_DISCLOSURE_TEXT
TEST_DATA_LEAKED
WRONG_PROPERTY_DATA
```

---

## 七、final_image_prompt 生成前檢查

在 saveImagePolicies 或 generateImage 前，必須檢查 final_image_prompt 是否包含以下安全限制：

```text
1. 請預留 QR Code 放置區，不要生成 QR Code 圖樣。
2. 不要生成、重繪、仿製或嵌入 QR Code。
3. 不要生成假人物。
4. 若未提供人物照，不要生成真人業務形象。
5. 不要生成任何未提供的營業員證號、經紀人字號、經紀業字號、公司地址、公司電話或營業員電話。
6. 所有證號、地址、電話與揭露資訊，必須完全依照已確認資料輸出。
7. 不要使用測試案例、範例資料或其他店家資料。
8. 使用使用者提供的物件照片，不要替換成其他房屋。
```

若 final_image_prompt 缺少 QR Code 禁止生成規則，不得進入 generateImage。

若 final_image_prompt 含有「生成 QR Code」、「繪製 QR Code」、「仿製 QR Code」、「可掃描 QR Code」等要求，不得進入 generateImage。

---

## 八、image policies 必須包含

saveImagePolicies 時，應包含以下政策內容：

### qrcode_policy

```text
圖片生成階段禁止生成、重繪、仿製或嵌入 QR Code。
無論使用者是否提供 QR Code，均只能預留 QR Code 放置區。
QR Code 放置區應為乾淨白底或淺色方框。
真正 QR Code 應由後製流程或人工貼上原始 QR Code 圖檔。
若圖片中出現 AI 生成 QR Code 或類 QR Code 圖樣，人工檢查必須 failed。
```

### portrait_policy

```text
若使用者沒有提供人物照，不得生成真人業務人物。
若使用者有提供人物照，不得改變人物身份，不得生成不同人物，不得新增未授權人物。
```

### failsafe_policy

```text
圖片生成不得產生假 QR Code、假人物、假物件照片、未授權證號、未授權電話、未授權地址、錯誤經紀業字號、測試資料或其他案例殘留資料。
generateImage 後必須停止，等待人工檢查。
人工檢查通過後，才可 completeGeneration。
```

---

## 九、generateImage 後人工檢查規則

generateImage 成功後，必須停止流程。

不得自動呼叫 completeGeneration。

必須等待使用者人工檢查圖片。

人工檢查項目：

1. 圖片中是否沒有 AI 生成 QR Code。
2. 圖片中是否只有 QR Code 放置區。
3. 圖片中是否沒有類 QR Code 黑白方塊圖樣。
4. 物件照片是否正確。
5. 是否沒有生成假人物。
6. 營業員姓名是否正確。
7. 營業員電話是否正確。
8. 營業員證號是否正確，且來源明確。
9. 公司地址是否正確，且來源明確。
10. 公司電話是否正確，且來源明確。
11. 公司名稱是否正確。
12. 經紀人姓名是否正確。
13. 經紀人字號是否正確。
14. 經紀業相關字號是否正確。
15. 是否沒有測試資料殘留。
16. 是否沒有其他店家資料。
17. 是否沒有其他業務資料。
18. 是否沒有未提供的地址、電話、證號。
19. 物件價格、坪數、格局、地址是否正確。
20. 是否沒有誇大或不實文字。

---

## 十、人工檢查通過條件

只有在以下條件全部成立時，才可 completeGeneration：

1. 人工檢查 checked = true。
2. 人工檢查 passed = true。
3. 圖片中沒有 AI 生成 QR Code。
4. 圖片中只有 QR Code 放置區。
5. 圖片中沒有類 QR Code 圖樣。
6. 物件照片正確。
7. 沒有生成假人物。
8. 沒有生成錯誤經紀業字號。
9. 沒有生成錯誤營業員證號。
10. 沒有生成來源不明的證號、地址、電話。
11. 沒有測試資料殘留。
12. 沒有其他案例資料。
13. 圖片文字與 final_image_prompt 一致。
14. 使用者明確表示可以完成。

---

## 十一、人工檢查未通過條件

只要出現以下任一情況，不得 completeGeneration：

1. QR Code 被 AI 重繪。
2. QR Code 失效。
3. 出現假 QR Code。
4. 出現類 QR Code 黑白方塊圖樣。
5. 出現錯誤經紀業字號。
6. 出現錯誤經紀人字號。
7. 出現錯誤營業員證號。
8. 出現來源不明的營業員證號。
9. 出現來源不明的公司地址。
10. 出現來源不明的公司電話。
11. 出現假人物。
12. 出現錯誤物件照片。
13. 出現測試資料。
14. 出現其他店家資料。
15. 出現其他業務資料。
16. 使用者明確表示未通過。

failedReason 可使用：

```text
QR_CODE_GENERATED_BY_AI
FAKE_QR_CODE
QR_CODE_REDRAWN
QR_CODE_LIKE_PATTERN
WRONG_AGENT_LICENSE_NO
UNVERIFIED_AGENT_LICENSE_NO
WRONG_BROKER_LICENSE_TEXT
UNAUTHORIZED_DISCLOSURE_TEXT
UNVERIFIED_COMPANY_CONTACT
WRONG_COMPANY_CONTACT
FAKE_PERSON_GENERATED
WRONG_PROPERTY_PHOTO
TEST_DATA_LEAKED
```

---

## 十二、completeGeneration 守門規則

completeGeneration 只能在以下條件全部成立時呼叫：

1. generateImage 已完成。
2. 人工檢查已完成。
3. checked = true。
4. passed = true。
5. 使用者明確允許 completeGeneration。
6. 沒有 failedReasons。
7. 沒有 blocked。
8. 圖片沒有 AI 生成 QR Code。
9. 圖片沒有錯誤揭露資訊。
10. 圖片沒有未授權證號、電話或地址。

若 checked = false，不得 completeGeneration。

若 passed = false，不得 completeGeneration。

若 failedReasons 不為空，不得 completeGeneration。

若使用者要求停止，不得 completeGeneration。

---

## 十三、本階段完成判定

本階段分兩段：

### A. 儲存圖片政策

完成 image policies 後，才可呼叫：

```text
saveImagePolicies
```

成功後才可進入：

```text
IMAGE_GENERATION_READY
```

### B. 執行圖片生成

進入 IMAGE_GENERATION_READY 後，才可呼叫：

```text
generateImage
```

generateImage 後必須停止，等待人工檢查。

不得自動呼叫：

```text
completeGeneration
```

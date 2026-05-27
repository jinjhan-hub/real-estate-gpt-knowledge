# 03｜Compliance Check Rule
# ruleKey: 03_compliance_check
# requiredStage: COMPLIANCE_CHECK
# nextAction: saveComplianceCheck
# mustCallNext: saveComplianceCheck

本規則用於「合規檢查階段」。

本階段目標：
1. 檢查物件銷售圖卡資料是否符合基本揭露要求。
2. 檢查店家 / 公司 / 經紀人 / 營業員資料來源是否可信。
3. 檢查營業員證號是否存在、正確且來源明確。
4. 檢查 QR Code 是否禁止交由 AI 生成。
5. 阻擋任何來源不明、AI 自行生成或測試資料殘留的揭露文字。

---

## 一、流程硬鎖

進入本階段時，必須遵守：

1. 不得呼叫 selectImageStyle，除非合規檢查已完成並通過。
2. 不得呼叫 saveFinalImagePrompt。
3. 不得呼叫 saveImagePolicies。
4. 不得呼叫 generateImage。
5. 不得呼叫 completeGeneration。
6. 不得跳過合規檢查。
7. 不得使用其他 session 的資料。
8. 不得使用測試案例殘留資料。
9. 不得自行補齊任何缺少的證號、地址、電話或揭露文字。

本階段完成條件：

1. 已完成揭露資料檢查。
2. 已完成營業員證號檢查。
3. 已完成公司地址與公司電話來源檢查。
4. 已完成 QR Code 生成限制檢查。
5. 已列出 passed / failed。
6. 若 failed，必須列出 failedReasons。
7. 檢查結果已呼叫 saveComplianceCheck 保存。

---

## 二、揭露資料合規檢查

合規檢查時，必須確認所有揭露資訊均來自可信來源。

### 公司 / 店家 / 經紀人揭露資料

以下資料優先以 API disclosure 為準：

1. 公司名稱
2. 加盟店名稱
3. 店家名稱
4. 經紀人姓名
5. 經紀人字號
6. 經紀業相關字號
7. API disclosure 明確提供的其他揭露資料

禁止：

1. 不得由 GPT 自行生成任何字號。
2. 不得由 GPT 根據店名推測公司名稱。
3. 不得由 GPT 根據縣市推測經紀業字號。
4. 不得由 GPT 根據範例補上證號。
5. 不得使用其他測試案例殘留的字號。
6. 不得使用其他業務、其他店家或其他 session 的資料。
7. 不得使用未由可信來源提供的經紀業字號、經紀人字號、營業員字號、電話或地址。

若 final_image_prompt 或圖卡資料中出現未由可信來源提供的證號或聯絡資料，合規檢查必須不通過。

failedReason：

```text
UNAUTHORIZED_DISCLOSURE_TEXT
```

---

## 三、營業員證號合規檢查

合規檢查必須檢查營業員證號。

檢查項目：

1. 圖卡若出現營業員姓名，應確認是否有對應營業員證號。
2. 營業員證號來源必須是：
   - 名片上清楚可辨識的文字；或
   - 使用者明確輸入；或
   - API disclosure 回傳資料。
3. 不得由 GPT 自行生成營業員證號。
4. 不得使用測試案例殘留的營業員證號。
5. 不得使用其他業務或其他店家的營業員證號。
6. 若名片上的營業員證號模糊或不完整，不得猜測補齊。
7. 若使用者沒有上傳名片，也沒有輸入營業員證號，應標記為缺少營業員證號。
8. 若 final_image_prompt 或圖卡中出現未經來源確認的營業員證號，合規檢查必須不通過。
9. 營業員證號不得被改寫格式，除非使用者明確要求且不改變原始內容。
10. 營業員證號不得與經紀人字號、經紀業字號混用。

failedReason 可使用：

```text
MISSING_AGENT_LICENSE_NO
UNVERIFIED_AGENT_LICENSE_NO
WRONG_AGENT_LICENSE_NO
UNAUTHORIZED_DISCLOSURE_TEXT
```

---

## 四、公司地址與公司電話檢查

公司地址與公司電話可由名片讀取，或由使用者自行輸入，或由 API disclosure 回傳。

檢查原則：

1. 若名片可清楚辨識公司地址與公司電話，可以使用。
2. 若名片無法辨識，不得猜測。
3. 若使用者未提供公司地址或公司電話，不得自行補上。
4. 若圖卡中出現公司地址或公司電話，必須確認其來源為名片、使用者輸入或 API disclosure。
5. 若來源不明，合規檢查必須不通過。
6. 不得使用其他店家的公司地址。
7. 不得使用其他測試案例的公司電話。
8. 不得根據店名、品牌或地區自行推測公司地址。
9. 不得自行補上看似合理的電話號碼。

failedReason 可使用：

```text
UNVERIFIED_COMPANY_CONTACT
WRONG_COMPANY_CONTACT
UNAUTHORIZED_DISCLOSURE_TEXT
```

---

## 五、營業員姓名與電話檢查

營業員姓名與電話來源必須是：

1. 名片上清楚可辨識的文字
2. 使用者明確輸入
3. API disclosure 回傳資料

檢查原則：

1. 不得自行生成營業員姓名。
2. 不得自行生成營業員電話。
3. 不得使用其他業務姓名。
4. 不得使用測試案例電話。
5. 不得補齊名片上模糊的電話號碼。
6. 不得將公司電話誤當成營業員手機，除非名片或使用者明確標示。
7. 不得將營業員電話誤當成公司電話，除非名片或使用者明確標示。

failedReason 可使用：

```text
UNVERIFIED_AGENT_CONTACT
WRONG_AGENT_CONTACT
UNAUTHORIZED_DISCLOSURE_TEXT
```

---

## 六、QR Code 合規檢查

圖片生成階段一律不得生成、重繪、仿製或嵌入 QR Code 圖樣。

無論使用者是否提供 QR Code：

1. 不得生成 QR Code。
2. 不得重繪 QR Code。
3. 不得仿製 QR Code。
4. 不得產生類 QR Code 黑白方塊圖樣。
5. 不得產生看似可掃描但實際失效的假 QR Code。
6. 不得描述 QR Code 外觀讓 AI 自行生成。
7. 只能在圖卡版面上預留 QR Code 放置區。
8. 真正 QR Code 應由後製流程或人工另行貼上原始 QR Code 圖檔。

若 final_image_prompt 中要求 AI 生成 QR Code，合規檢查必須不通過。

若圖片中出現任何 AI 生成 QR Code 或類 QR Code 圖樣，人工檢查必須不通過。

failedReason：

```text
QR_CODE_GENERATED_BY_AI
FAKE_QR_CODE
QR_CODE_REDRAWN
```

---

## 七、物件資訊合規檢查

物件資訊必須來自使用者提供資料，不得自行生成。

檢查項目：

1. 物件名稱是否來自使用者提供資料。
2. 價格是否來自使用者提供資料。
3. 地址或區域是否來自使用者提供資料。
4. 坪數是否來自使用者提供資料。
5. 格局是否來自使用者提供資料。
6. 屋齡是否來自使用者提供資料。
7. 車位是否來自使用者提供資料。
8. 特色是否來自使用者提供資料或可由使用者資料合理整理。
9. 是否出現未提供的誇大文字。
10. 是否出現保證獲利、保證增值、保證貸款等不當宣稱。

禁止：

1. 不得自行補上價格。
2. 不得自行補上坪數。
3. 不得自行補上格局。
4. 不得自行補上地址。
5. 不得自行補上不存在的機能。
6. 不得自行補上不存在的學區。
7. 不得自行補上不存在的捷運、車站、交流道。
8. 不得使用保證性、誇大性、絕對性用語。

failedReason 可使用：

```text
UNVERIFIED_PROPERTY_DATA
WRONG_PROPERTY_DATA
OVERSTATED_CLAIM
```

---

## 八、圖片素材合規檢查

圖片生成前，必須確認素材條件：

1. 物件照片不可被替換成其他房屋。
2. 不得生成假物件照片。
3. 不得生成假人物。
4. 若使用者沒有提供人物照，不得生成真人業務形象。
5. 若使用者沒有提供 QR Code，不得生成 QR Code。
6. 即使使用者有提供 QR Code，也不得交由 AI 重繪。
7. 圖片中只能預留 QR Code 放置區。

failedReason 可使用：

```text
FAKE_PROPERTY_PHOTO
FAKE_PERSON_GENERATED
QR_CODE_GENERATED_BY_AI
UNAUTHORIZED_IMAGE_ELEMENT
```

---

## 九、合規檢查輸出格式

合規檢查結果必須包含：

```text
checked: true
passed: true / false
blocked: true / false
failedReasons: []
warnings: []
```

若未通過，failedReasons 必須明確列出原因。

範例：

```text
checked: true
passed: false
blocked: true
failedReasons:
- MISSING_AGENT_LICENSE_NO
- QR_CODE_GENERATED_BY_AI
- UNAUTHORIZED_DISCLOSURE_TEXT
warnings: []
```

---

## 十、通過條件

只有在以下條件全部成立時，才可通過：

1. 公司 / 店家 / 經紀人揭露資料來源可信。
2. 營業員姓名、電話、營業員證號來源可信，或缺少項目已明確標記。
3. 公司地址、公司電話來源可信，或缺少項目已明確標記。
4. 未自行生成任何證號、電話、地址或揭露文字。
5. 未要求 AI 生成 QR Code。
6. 僅允許 QR Code 放置區。
7. 未生成假人物。
8. 未生成假物件照片。
9. 未出現不實或誇大物件資訊。
10. 未出現其他案例殘留資料。

---

## 十一、不通過條件

只要出現以下任一情況，合規檢查必須不通過：

1. 出現來源不明的營業員證號。
2. 出現錯誤營業員證號。
3. 出現來源不明的經紀人字號。
4. 出現來源不明的經紀業字號。
5. 出現錯誤經紀業字號。
6. 出現來源不明的公司地址。
7. 出現來源不明的公司電話。
8. 出現 AI 生成 QR Code。
9. 出現類 QR Code 黑白方塊圖樣。
10. 出現假人物。
11. 出現假物件照片。
12. 出現測試案例殘留資料。
13. 出現其他店家或其他業務資料。
14. final_image_prompt 內含未確認來源的正式揭露資料。

---

## 十二、本階段完成判定

本階段完成後，必須呼叫：

```text
saveComplianceCheck
```

只有當 saveComplianceCheck 成功，且 passed = true，才可進入下一階段：

```text
style_selection
```

若 passed = false，必須停止流程，不得進入圖片生成。

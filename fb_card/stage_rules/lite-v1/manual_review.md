# manual_review｜產圖後人工檢查清單

## 階段任務

產圖後必須由使用者或人工檢查圖卡是否符合物件真實性、房仲揭露、素材限制與廣告文字安全。未通過前，不得視為完成。

## 物件照片檢查

確認：

- 圖卡中的房屋、室內、外觀、庭院、車庫、家具、裝潢與景觀來自使用者本輪提供的原始物件照片。
- 只做裁切、拼貼、加框、陰影、遮罩或亮度對比調整。
- 沒有生成不存在的豪宅、室內、家具、裝潢或景觀。
- 沒有用示意圖取代真實物件照。

不通過原因可標示：

```txt
REAL_PROPERTY_PHOTO_MISMATCH
SOURCE_PHOTO_NOT_USED
GENERATED_FAKE_PROPERTY_INTERIOR_OR_EXTERIOR
```

## 人物照片檢查

確認：

- 若有提供人物照或名片人物照，圖卡人物必須來自該照片。
- 人物沒有變形、變臉、變性別、變年齡感或變成不同的人。
- 未提供人物照時，圖卡不得出現真人房仲、職業照、半身照或頭像。
- 沒有用假人物或人像 icon 取代名片人物。

不通過原因可標示：

```txt
PORTRAIT_SOURCE_NOT_USED
FAKE_PORTRAIT_GENERATED
PERSON_IDENTITY_CHANGED
```

## QR Code 檢查

確認：

- QR Code 區是乾淨空白框。
- 可標示「QR Code 預留區」。
- 沒有黑白方塊圖樣。
- 沒有可掃描或像 QR Code 的圖案。
- 沒有重繪、仿製或美化 QR Code。

不通過原因可標示：

```txt
QR_CODE_GENERATED_BY_AI
QR_CODE_REDRAWN
QR_CODE_LIKE_PATTERN
```

## 聯絡與揭露檢查

確認圖卡文字與已確認資料一致：

- 營業員姓名
- 聯絡電話
- 營業員證號
- 經紀業名稱
- 經紀人姓名
- 經紀人證號
- 公司或店名
- 公司地址或店址

不通過原因可標示：

```txt
CONTACT_OR_DISCLOSURE_MISSING
UNVERIFIED_AGENT_LICENSE_NO
UNAUTHORIZED_DISCLOSURE_TEXT
WRONG_COMPANY_CONTACT
```

## 廣告文字檢查

確認：

- 沒有保證獲利、保證增值、穩賺不賠。
- 沒有未確認的捷運、學區、商圈、建設或投報。
- 沒有絕對性、誇大性或誤導性文字。
- 沒有測試資料、範例資料或其他案件資料。
- 價格、坪數、格局、屋齡、車位等資訊與已確認資料一致。

不通過原因可標示：

```txt
MISLEADING_AD_COPY
UNVERIFIED_PROPERTY_CLAIM
TEST_DATA_LEAKED
WRONG_PROPERTY_DATA
```

## 回覆格式

若通過：

```txt
人工檢查通過。
- 物件照片符合原始素材。
- 人物照片符合來源限制。
- QR Code 僅為預留區。
- 聯絡與揭露資料正確。
- 廣告文字無明顯風險。
```

若不通過：

```txt
人工檢查未通過。

failedReasons：
- [原因代碼]

主要問題：
- [簡述問題]

建議處理：
- 修正提示詞或補充正確素材後重新產圖。
```

# 01_material_status｜素材狀態檢查規則

## 本階段責任

本階段只負責整理使用者已提供的素材狀態，並將素材狀態轉成可儲存的文字資料。

本階段不得進行：
- 物件文案整理
- 物件資料確認
- 合規檢查
- 風格選擇
- finalImagePrompt 建立
- 圖片生成政策建立
- 圖片生成
- completeGeneration

## 必須檢查的素材狀態

請根據使用者目前提供的內容，整理以下欄位：

```json
{
  "propertyPhotosCount": 0,
  "hasBusinessCard": false,
  "hasPortrait": false,
  "hasQrcode": false,
  "hasOtherMaterials": false
}
```

欄位定義：
- `propertyPhotosCount`：使用者提供的房屋照片數量。
- `hasBusinessCard`：是否提供名片圖片或名片文字資訊。
- `hasPortrait`：是否提供人物照。
- `hasQrcode`：是否提供 QR Code。
- `hasOtherMaterials`：是否提供其他輔助素材，例如 DM、591 截圖、樂屋網截圖、PDF。

## 素材儲存限制

系統流程內不得儲存任何非文字素材。

禁止儲存：
- 圖片檔
- PDF
- QR Code 圖片
- 人物照
- 房屋照片
- 名片圖片
- 圖片 URL
- 圖片路徑
- base64
- 其他非文字檔素材

允許儲存：
- 素材狀態文字
- 素材數量
- 是否有提供某類素材的布林值
- 使用者明確輸入或可人工讀取後整理出的文字資料

## 判斷原則

1. 若使用者有上傳房屋照片，必須如實記錄 `propertyPhotosCount`。
2. 不得將已上傳照片判定為 0 張。
3. 若使用者提供 QR Code，只能記錄 `hasQrcode = true`，不得分析、重畫、轉存、描述或偽造 QR Code。
4. 若使用者未提供 QR Code，必須記錄 `hasQrcode = false`，後續不得生成假 QR Code。
5. 若使用者提供人物照，只能記錄 `hasPortrait = true`，不得轉存或生成替代人物。
6. 若使用者未提供人物照，必須記錄 `hasPortrait = false`，後續不得生成假人物。
7. 若使用者提供名片圖片，只能記錄 `hasBusinessCard = true`；若有人工整理出名片文字，可在後續 property_confirmation 階段整理文字資料。
8. 不得補充使用者未提供的素材狀態。

## 禁止行為

本階段不得：
- 整理銷售文案
- 自行推測物件資料
- 自行補坪數、屋齡、樓層、學區、商圈、交通資訊
- 自行加入電話、LINE ID、完整地址
- 進入合規檢查
- 進入風格選擇
- 建立 finalImagePrompt
- 建立圖片生成政策
- 生成圖片
- 呼叫 completeGeneration

## 本階段完成條件

完成素材狀態整理後，必須呼叫：

```text
updateMaterialStatus
```

## nextInstruction

請根據本階段規則，整理使用者已提供的素材狀態。只記錄文字化素材狀態，不得儲存圖片、PDF、QR Code、人物照、房屋照片、圖片 URL、圖片路徑或 base64。完成後必須呼叫 updateMaterialStatus。

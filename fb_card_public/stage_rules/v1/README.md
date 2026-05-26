# FB 銷售圖卡 GPTs｜Stage Rules v1

本資料夾存放「房仲銷售圖卡 GPTs + real-estate-card-api」的分段規則檔。

## 設計目的

GPTs Instructions 只保留最小總控規則，例如流程順序、禁止跳步、Action 呼叫原則、圖片生成硬鎖與錯誤處理。

所有詳細規則改由 API 透過 `loadStageRules(stage)` 分段載入。

這樣可以避免：

- GPTs Instructions 過長
- 不同階段規則混在一起
- 模型提早讀到不該執行的後續規則
- 後續維護時需要反覆修改 GPTs Instructions

## 分段規則檔

| Stage | Rule File | 責任 |
|---|---|---|
| material_status | 01_material_status.md | 檢查使用者提供的素材狀態 |
| property_confirmation | 02_property_confirmation.md | 整理物件資料並等待使用者確認 |
| compliance_check | 03_compliance_check.md | 檢查銷售圖卡文字與資訊合規 |
| style_selection | 04_style_selection.md | 處理圖卡風格選擇 |
| final_image_prompt | 05_final_image_prompt.md | 建立 finalImagePrompt |
| image_generation_failsafe | 06_image_generation_failsafe.md | 建立圖片生成保護政策 |

## 核心原則

1. 每份規則只管自己的階段。
2. 不要把完整流程規則塞進單一 stage rule。
3. 不要把詳細規則塞回 GPTs Instructions。
4. API 必須依照 `stage` 載入對應規則。
5. 使用者上傳的圖片、PDF、QR Code、人物照、房屋照片等非文字素材不得被系統儲存。
6. 流程內只允許儲存文字資料與素材狀態。

## 建議 API 回傳欄位

`loadStageRules(stage)` 建議固定回傳：

```json
{
  "ok": true,
  "success": true,
  "blocked": false,
  "sessionId": "string",
  "stage": "final_image_prompt",
  "ruleLoaded": true,
  "ruleKey": "05_final_image_prompt",
  "currentStage": "FINAL_IMAGE_PROMPT",
  "requiredStage": "FINAL_IMAGE_PROMPT",
  "nextAction": "saveFinalImagePrompt",
  "mustCallNext": "saveFinalImagePrompt",
  "nextInstruction": "string",
  "message": "Stage rule loaded successfully.",
  "rule": "markdown rule content"
}
```

## 版本說明

目前版本：`v1`

用途：公版穩定流程，對應已完成驗證的「最小總控版 Instructions + OpenAPI 1.0.6 A」。

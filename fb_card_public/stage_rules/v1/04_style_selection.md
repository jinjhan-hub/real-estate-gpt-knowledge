# 04_style_selection｜圖卡風格選擇規則

## 本階段責任

本階段只負責圖卡風格選擇。

不得建立：
- finalImagePrompt
- qrcodePolicy
- portraitPolicy
- failsafePolicy
- 圖片生成結果

## 可選風格

目前可使用以下風格代碼：

```json
[
  {
    "styleKey": "luxury_premium",
    "label": "高質感精品感",
    "description": "適合高總價、大坪數、別墅、質感住宅。畫面應乾淨、穩重、精品感、留白充足。"
  },
  {
    "styleKey": "clean_modern",
    "label": "乾淨現代感",
    "description": "適合一般住宅、公寓、大樓、首購產品。畫面應清爽、易讀、資訊明確。"
  },
  {
    "styleKey": "warm_home",
    "label": "溫暖居家感",
    "description": "適合家庭型住宅、換屋族、重視生活感的產品。畫面應溫暖、親切、柔和。"
  },
  {
    "styleKey": "commercial_focus",
    "label": "商用機能感",
    "description": "適合店面、商辦、土地、投資型產品。畫面應俐落、資訊導向、強調條件與用途。"
  }
]
```

## 選擇規則

1. 若使用者尚未指定風格，必須列出可選風格並等待使用者選擇。
2. 若使用者已明確指定風格，可以直接整理為 `selectedStyle`。
3. 不得在使用者未指定時自行替使用者選擇。
4. 若使用者使用中文描述風格，需對應到最接近的 `styleKey`。
5. 若描述不明確，應要求使用者確認，不得硬轉換。

## 對應範例

```text
高質感精品感 → luxury_premium
精品感 → luxury_premium
豪宅感 → luxury_premium
乾淨現代 → clean_modern
清爽簡約 → clean_modern
溫暖居家 → warm_home
家庭感 → warm_home
商用機能 → commercial_focus
投資型 → commercial_focus
```

## selectedStyle 格式

選定後，應整理為：

```json
{
  "selectedStyle": "luxury_premium"
}
```

## 禁止行為

本階段不得：
- 改寫物件資料
- 新增未提供房產資訊
- 新增電話、LINE ID、完整地址
- 建立完整圖片提示詞
- 建立 QR Code 政策
- 建立人物政策
- 生成圖片
- 呼叫 completeGeneration

## 本階段完成條件

確認風格後，必須呼叫：

```text
selectImageStyle
```

## nextInstruction

請根據本階段規則，提供可選風格並等待使用者選擇。若使用者已明確指定風格，請整理 selectedStyle，並呼叫 selectImageStyle。

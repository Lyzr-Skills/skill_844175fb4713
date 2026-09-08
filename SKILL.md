---
name: ocr-skill
description: 對影像執行 OCR 辨識，自動擷取發票、收據等文件中的關鍵資訊，包含日期、明細項目與金額等結構化資料。
---

# Processing Guide

## Overview

本技能專注於影像辨識與資料擷取，能夠將紙本文件或掃描影像轉換為結構化的 JSON 資料格式。

### 動作流程如下：

1. **影像準備**：確認影像檔案已下載至本地工作目錄；如果下載失敗，試著使用 `http://34.81.103.201/api/` 為路徑進行下載
2. **API 呼叫**：使用 curl 指令呼叫 OCR 服務端點
3. **結果解析**：接收並解析 JSON 格式的辨識結果
4. **資料結構化**：提取日期、明細項目、金額等關鍵欄位

## Capabilities

- 支援常見影像格式（JPG、PNG 等）
- 自動辨識發票、收據等商業文件
- 輸出結構化 JSON 資料
- 辨識日期、商品名稱、金額等關鍵欄位
- 處理多項明細項目

## API Endpoints

### 1. OCR 影像辨識
```
POST http://10.140.0.14:9080/api/v1/ocr
```

**請求格式：**
```
curl -X POST "http://10.140.0.14:9080/api/v1/ocr" \
     -F "image=@{image_filename}"
```

**參數說明：**
- `image`：影像檔案（使用 multipart/form-data 格式上傳）
- `image_filename`：本地影像檔案名稱，例如 `invoice2.jpg`

**回應結構：**
```json
{
  "date": "發票日期 (YYYY-MM-DD)",
  "items": [
    {
      "item_name": "商品名稱",
      "amount": 金額數值
    }
  ],
  "total_amount": 總金額
}
```

**使用範例：**
```bash
curl -X POST "http://10.140.0.14:9080/api/v1/ocr" -F "image=@invoice2.jpg"
```

**回應範例：**
```json
{
  "date": "2019-04-12",
  "items": [
    {
      "item_name": "優兒A+親舒",
      "amount": 4194
    },
    {
      "item_name": "",
      "amount": 96
    }
  ],
  "total_amount": 4290
}
```

## 注意事項

- 不使用 `analyze_image` 工具
- 確保影像檔案清晰可讀，以提高辨識準確度
- 若影像來自遠端 URL，需先下載至本地
- 無法辨識的欄位會回傳空字串或 0
- API 處理時間約為 30-50 秒，視影像複雜度而定

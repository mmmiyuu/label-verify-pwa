# 標籤比對系統 PWA

## 檔案說明

| 檔案 | 說明 |
|------|------|
| `index.html` | 主要比對工具（PWA） |
| `qrgen.html` | 工單 QR Code 產生器 |
| `manifest.json` | PWA 設定檔 |
| `sw.js` | Service Worker（離線快取） |
| `icons/` | 放置 icon-192.png 和 icon-512.png |

## 部署方式

### 最快：GitHub Pages（免費）
1. 建立 GitHub repository
2. 將所有檔案上傳
3. Settings → Pages → Branch: main → Save
4. 取得網址如：`https://yourname.github.io/label-verify/`

### 其他選項
- **Netlify**：拖拉資料夾上傳即可，自動產生 https 網址
- **公司內網伺服器**：放到 nginx / IIS，需要 https 才能使用相機
- **本機測試**：`npx serve .` 或 `python -m http.server 8080`

> ⚠️ **重要**：PWA 相機功能需要 HTTPS，localhost 除外

## 圖示製作

在 `icons/` 資料夾放兩個 PNG：
- `icon-192.png`（192×192 px）
- `icon-512.png`（512×512 px）

可用 Figma、Canva 等工具製作，或用 https://realfavicongenerator.net/ 產生

## QR Code 格式（工單端需產生）

```json
{
  "partNo": "85.10C37.012",
  "fullBoxQty": 500,
  "innerQty": 50,
  "productionDate": "2026-05-25",
  "lotNo": "6052803"
}
```

## 欄位對應表

### 外標籤
| 標籤欄位 | QR Code key | 比對方式 |
|----------|------------|---------|
| Part No. | partNo | 字串完整比對 |
| Qty | fullBoxQty | 只比數字 |
| Production Date | productionDate | 日期標準化後比對 |
| Lot No. | lotNo | 字串完整比對 |

### 內標籤
| 標籤欄位 | QR Code key | 比對方式 |
|----------|------------|---------|
| Part No. | partNo | 字串完整比對 |
| Qty | innerQty | 只比數字 |
| Production Date | productionDate | 日期標準化後比對 |
| Lot No. | lotNo | 字串完整比對 |

## 調整欄位對應

如需修改欄位名稱或新增欄位，編輯 `index.html` 中的 `FIELD_MAP` 物件：

```js
const FIELD_MAP = {
  outer: [
    { ocrKeys: ['part no', 'part no.'], label: 'Part No.', refKey: 'partNo', mode: 'string' },
    // 新增欄位...
  ],
  inner: [...]
};
```

- `ocrKeys`：OCR 可能辨識出的欄位名稱（小寫），可填多個
- `refKey`：對應 QR Code JSON 中的 key
- `mode`：`string` / `number` / `date`

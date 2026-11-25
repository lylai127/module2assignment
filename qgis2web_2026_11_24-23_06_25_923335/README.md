# 回到 1992 年的東涌 · 村落地圖與文本網絡

以 1992 年東涌田野調查為基礎的互動故事網站：先以封面引入，再進入地圖層，
點擊村落圓點可看到該村的關鍵詞、共現詞、文字雲與 VOSviewer 共現網絡。

本專案為純靜態網站（HTML + CSS + JS + 圖片），無需任何伺服器端程式或建置流程，
可直接上載到 GitHub Pages、Netlify、Cloudflare Pages 等任何靜態主機。

---

## 一、網站入口

| 檔案 | 用途 | 說明 |
| --- | --- | --- |
| `index.html` | **網站首頁（主要入口）** | 故事版網站：封面 → 地圖 → 對話框 / 文字雲 / 共現網絡 |
| `story.html` | 與 `index.html` 內容完全相同 | 保留此檔，讓已分享的 `/story.html` 連結不會失效 |
| `map.html` | qgis2web 原始匯出的地圖 | 開發時的原始版本，保留作備份，一般訪客不需要 |

> 若日後修改網站內容，請**同時更新 `index.html` 與 `story.html`**（兩者為同一份檔案）。
> 建議的簡易做法：改好 `story.html` 後，執行 `cp story.html index.html`。

---

## 二、上載到 GitHub Pages 的步驟

1. 在 GitHub 建立新的 repository（例如 `tung-chung-1992`）。
2. 將本資料夾內**全部檔案**（含 `css/`、`data/`、`images/`、`js/`、`legend/`、`webfonts/`）
   上載到 repository 根目錄。請勿只上載 HTML 檔，否則地圖圖層與文字雲圖片會顯示不到。
3. 進入 repository 的 **Settings → Pages**。
4. 在 **Build and deployment → Source** 選擇 `Deploy from a branch`。
5. **Branch** 選 `main`，資料夾選 `/ (root)`，按 **Save**。
6. 等待約 1–2 分鐘，網址會是：

   ```
   https://<你的帳號>.github.io/<repository 名稱>/
   ```

   開啟後便會自動進入 `index.html`（故事版網站）。

### 為什麼需要 `.nojekyll`

GitHub Pages 預設會用 Jekyll 處理網站，Jekyll 會**忽略名稱以底線 `_` 開頭的檔案與資料夾**。
本專案 `images/` 內的文字雲檔名包含 `C__Users_...` 這樣的底線開頭片段，
加上 `.nojekyll` 可確保 GitHub Pages 直接原樣提供所有檔案，避免圖片 404。

此檔案內容為空白，屬正常現象，請一併上載。

---

## 三、資料夾結構

```
.
├── index.html                     網站首頁（故事版）
├── story.html                     同上（保留舊連結）
├── map.html                       qgis2web 原始地圖（備份）
├── .nojekyll                      停用 GitHub Pages 的 Jekyll 處理
├── README.md                      本說明檔
│
├── css/
│   ├── leaflet.css                Leaflet 樣式
│   ├── qgis2web.css               qgis2web 彈出視窗與控制項樣式
│   └── ...                        其餘為 map.html 使用的外掛樣式
│
├── data/
│   ├── village_text_points_1.js   村落資料（GeoJSON，含關鍵詞、共現詞、文字雲路徑、VOSviewer 連結）
│   └── clippedmap_0.png           1992 年掃描地圖底圖
│
├── images/                        各村文字雲圖片（7 個村莊）
├── js/
│   ├── leaflet.js                 Leaflet 主程式
│   └── ...                        其餘為 map.html 使用的外掛
│
├── legend/                        圖例
└── webfonts/                      Font Awesome 字型（map.html 使用）
```

---

## 四、內容更新說明

### 修改村落文字資料

編輯 `data/village_text_points_1.js`，每個 feature 的 `properties` 欄位如下：

| 欄位 | 說明 | 範例 |
| --- | --- | --- |
| `fid` | 內部識別碼 | `1` |
| `village_id` | 村莊編號（同時對應文字雲檔名） | `SLP` |
| `village_name` | 村莊顯示名稱 | `上嶺皮` |
| `top_terms` | 主要關鍵詞 | `兒子(34); 沒有(29); 神位(28)` |
| `collocates` | 共現詞，以 `;` 分隔 | `兒子-過繼(40); 兒子-沒有(31)` |
| `vos_url` | VOSviewer 網絡連結（必須是 `app.vosviewer.com`） | `https://app.vosviewer.com/?json=...` |
| `word_cloud` | 文字雲圖片的原始路徑 | `C:\...\word_cloud\SLP_wordcloud.jpg` |

`word_cloud` 會由程式自動轉成 `images/` 內對應的檔名
（將 `\`、`/`、`:` 換成 `_`），因此替換文字雲圖片時，
**只要把新檔案放進 `images/` 並保持相同的命名規則即可**。

### 新增村莊

1. 在 `data/village_text_points_1.js` 新增一個 feature（含 `geometry.coordinates`，格式為 `[經度, 緯度]`）。
2. 把該村的文字雲圖片放入 `images/`，檔名依上述規則命名。
3. 若要在彈出視窗中顯示文字雲的預留尺寸，請在 `story.html`（及 `index.html`）
   的 `WORD_CLOUD_SIZE` 物件中加入該村編號與圖片原始像素，例如：

   ```js
   var WORD_CLOUD_SIZE = {
     SLP: [1386, 786],
     NEW: [1200, 800]   // 新增的村莊
   };
   ```

   此步驟可省略，但省略時第一次開啟對話框可能要等圖片載入後才會調整到正確大小。

---

## 五、常見問題

**地圖一片空白 / 只有灰底**
→ 確認 `data/clippedmap_0.png` 與 `data/village_text_points_1.js` 都已上載。

**文字雲圖片顯示為破圖**
→ 確認 `images/` 內 7 個 jpg 已上載，且檔名未被 GitHub 或編輯器改動
（檔名含空格與中文，請以原始檔名上載，不要手動重打）。

**VOSviewer 面板空白**
→ 網絡資料存放於 Google Drive，需能連線至 `app.vosviewer.com`。
若在無網絡環境或公司防火牆下，此面板會無法載入，但不影響地圖與其他內容。

**開啟 `index.html` 時出現目錄列表**
→ GitHub Pages 未找到 `index.html`，請確認檔案位於 repository 根目錄而非子資料夾。

---

## 六、資料來源

1992 年東涌田野調查筆記 · 文字分析由 Voyant Tools 與 VOSviewer 產出 · 地圖由 QGIS / qgis2web 匯出

文字分析與詮釋：CUHK Anthropology

# 電力系統單日探索器

台電每 10 分鐘資料的互動式單日探索工具。純前端單檔 HTML，資料是靜態 JSON，
放 GitHub Pages 就能跑，不需要後端。

**網址**：https://powerkidplay-png.github.io/powerkidplay/power-explorer/

---

## 它能做什麼

| 功能 | 說明 |
|:---|:---|
| **時間軸拖動** | 拖動下方滑桿或直接在圖上拖曳，看那一刻的負載、供電能力、備轉容量率、燈號 |
| **播放** | 自動從 00:00 跑到 23:50，看一天怎麼演變 |
| **圖層切換** | 總負載／淨負載／供電能力／太陽光電／抽蓄水力／能源別堆疊／備轉容量率／燈號色帶 |
| **能源焦點** | 選一種能源，以負載為基底疊上去（可切區塊圖／長條圖），下方自動產生該能源的貢獻分析 |
| **日期切換** | 前後日按鈕或下拉選單 |
| **供給側狀況** | 當日歲修、故障停機的機組與容量 |
| 鍵盤 | ← → 移動時間游標 |

**核心賣點**：備轉容量率是**逐點自算**的（（即時最高供電能力 − 瞬時用電量）÷ 瞬時用電量），
不是台電公布的當日單一數值。頁面會把兩者並列，落差一眼看到。

---

## 自動分析文字

「能源焦點」下方的分析是**公版文字套數據**，依能源型態切換說法：

| 型態 | 適用 | 額外會講 |
|:---|:---|:---|
| 間歇型 | 太陽光電、風力 | 從最高點到歸零歷時多久、日尖峰到夜尖峰掉了多少萬瓩 |
| 儲能型 | 抽蓄水力、儲能 | 抽水／放水各分幾段、各是哪些時段、用掉比發出多多少 |
| 基載／追隨型 | 核能、燃煤、燃氣等 | 全日平均出力、最高最低差、變動幅度 |

公版與型態說明寫在 `index.html` 的 `FUEL_TRAIT`，要改文字改那裡。

---

## 資料怎麼來

由 `powerdad-hq/05-tools/powerchart/export_web.py` 產生：

```bash
python export_web.py --supply "...\supply\年檔\*.xlsx" --genload "...\genload\月檔\2024-06*.xlsx" --dates 2024-06-22 --label "週末仍亮黃燈" --out <本資料夾>\data
```

產出：
- `data/YYYY-MM-DD.json` — 單日資料，約 **17 KB**
- `data/index.json` — 日期清單與摘要，前端拿來做選單

沒有真實資料時，省略 `--supply` 會用合成資料（schema 與真檔一致），可先看版面。

---

## 本機預覽

```bash
python -m http.server 8899
```

然後開 http://localhost:8899/power-explorer/
（直接用 `file://` 開會因為 CORS 讀不到 JSON。）

---

## 嵌進文章

`body` 是透明的，可以直接 iframe 進 Blogger：

```html
<iframe src="https://powerkidplay-png.github.io/powerkidplay/power-explorer/" width="100%" height="1150" style="border:0" loading="lazy"></iframe>
```

也可以只帶一天：之後若要支援 `?date=2024-06-22`，在 `boot()` 讀 `location.search` 即可。

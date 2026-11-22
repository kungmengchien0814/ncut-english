# 勤益 115-2 字彙測驗練習

國立勤益科技大學（NCUT）語言中心「英檢輔導A選課資格 — 未達初級字彙測驗」的練習網站，加上英文畢業門檻的整理。手機優先、純靜態（HTML + CSS + 原生 JS），不需要建置工具、後端或外部函式庫。

> **非官方網站。** 同學自製；考點部分為推測；正式題型、及格標準與規定以[勤益語言中心公告](https://lgc.ncut.edu.tw/p/405-1029-95793,c82.php?Lang=zh-tw)為準。

## 功能

- 模擬考（範圍、題數、題型可選，計時，交卷後看分數和錯題）
- 選擇題練習、拼字練習、錯題本、單字卡、單字表（可搜尋）
- 點字查意思：練習、單字卡、單字表、考試結果裡的句子，每個英文字都能點，跳出中文、詞性、詞形變化、用法說明、這句裡的片語，以及這個字在題庫哪一週考過；可以念出發音，也有 Yahoo 字典連結。模擬考作答中不能點；還沒作答的題目，說明裡會提到答案的那幾行會自動拿掉
- 畢業門檻說明：要不要考字彙測驗、門檻標準、115-2 場次（自動標示已結束／下一場）、申請流程、聯絡方式
- 練習紀錄只存在使用者自己的瀏覽器（localStorage），不收集任何個人資料

## 三種版面（同一個檔案，用寬度切換）

| 寬度 | 版面 |
|---|---|
| 未滿 720px（手機） | 單欄，功能按鈕 2 欄，週次晶片橫向捲動，左上「‹ 首頁」返回 |
| 720–1079px（平板） | 內容加寬，功能按鈕 3 欄，選項 2×2，單字表雙欄，各週掌握度雙欄 |
| 1080px 以上（電腦） | 左側固定選單（含錯題數），首頁右側放統計，門檻說明分兩欄 |

有滑鼠的裝置會顯示鍵盤提示：選擇題按 A–D 或 1–4 作答、Enter 下一題；單字卡空白鍵看答案，← 還不熟、→ 會了。

點字說明在手機上是從下方滑出的面板（點背景關閉），平板和電腦是浮在那個字旁邊的小視窗（點外面或按 Esc 關閉）。

## 檔案

| 檔案 | 用途 |
|---|---|
| `index.html` | 整個網站（題庫在檔內的 `RAW` 字串，格式 `週\|句子\|中文\|推測說明`，答案用 `{}` 包起來） |
| `404.html` | 找不到頁面時導回首頁 |
| `favicon.svg`、`apple-touch-icon.png` | 瀏覽器分頁與「加到主畫面」圖示 |
| `og.png` | 分享到 LINE／IG 時的預覽圖（1200×630） |
| `notes/handoff.md` | 原始交接文件（含完整規格與 200 題 JSON），**只留在本機，不上傳**（見 `.gitignore`） |

## 本機預覽

```powershell
python -m http.server 8930 --bind 127.0.0.1 --directory D:/Claude/Claude-work/ncut-english
```

然後打開 <http://127.0.0.1:8930/>。

## 題庫

- 來源：語言中心〈115-2字彙測驗題庫.pdf〉，Week 6–10、16–20，每週 20 句，共 200 句。
- 句子照原檔保留，包含原檔錯字（`wat`、`rival n the`、`That's great photo` 等），**不要自動修正**。
- 6 題是推測題（句中另一個字也可能是考點）：Week 6 hurt／notice、Week 9 habit／aspirin、Week 10 breath、Week 17 breeds。
- 2026-11-22 已確認 `index.html` 的 200 題與交接文件附錄 B 的 JSON 逐欄一致。

## 單字說明資料

都在 `index.html` 的 `RAW` 下面，三個字串，一行一筆：

| 名稱 | 格式 | 內容 |
|---|---|---|
| `NOTE` | `考點字\|詞性\|說明` | 200 個考點字的用法說明（中文意思沿用 `RAW`） |
| `DICT` | `字\|原形\|詞性\|中文\|詞形\|說明` | 句子裡其他所有英文字，全部小寫；原形和字一樣就留空。`詞形` 寫「過去式」「複數」等，頁面會顯示成「crossed 是 cross 的過去式」 |
| `PHR` | `片語\|中文` | 片語要和句子裡的寫法一字不差（小寫），點到片語裡的字時才會出現 |

詞性代號：`n v adj adv prep conj pron art aux num name place prop abbr typo`。所有格 `'s`（例如 `boy's`）會自動去查 `boy`。

換題庫時，句子裡的每個字都要有 `NOTE` 或 `DICT`。檢查方法：本機預覽打開頁面，在主控台執行

```js
DATA.flatMap(d => d.s.replace(/[{}]/g, "").match(WORD_RE)).filter(w => !wordInfo(w).zh)
```

結果是空陣列 `[]` 就代表全部都有說明。

## 本機儲存（localStorage）

| key | 內容 |
|---|---|
| `ncut-1152-vocab-known` | 單字卡標「會了」的題目 id |
| `ncut-1152-vocab-stats` | 每題作答統計 `{id: {r, w, last}}` |
| `ncut-1152-vocab-best` | 模擬考最佳成績 |
| `ncut-1152-vocab-hint` | 拼字提示字首開關 |
| `ncut-1152-vocab-tip` | 「點字看說明」提示看過了沒（點過一次字就不再顯示） |

題目 id 是題庫的 0 起算順序。**換學期換題庫時，要同時改 key 名稱**（例如 `ncut-1161-…`），不然舊紀錄會對到錯的題目。

## 每學期更新清單

1. 換 `RAW` 題庫，改 localStorage key 版本號，補齊 `NOTE`、`DICT`、`PHR`（見「單字說明資料」）。
2. 改 `vRules()` 上方的 `SESSIONS`（場次）和 `CHECKED`（查核日期），重新對照語言中心公告。
3. 標題、說明文字、`og.png` 裡的「115-2」。

## 上架

GitHub Pages：repo `kungmengchien0814/ncut-english`（main 分支根目錄），網址 `https://kungmengchien.uk/ncut-english/`（由使用者站台的自訂網域自動帶出，經過 Cloudflare；**不要**在這個 repo 加 CNAME）。若改用別的網址，要一起改 `index.html` 的 `og:url`、`og:image` 和 `404.html` 的路徑。

每次推送前把 `index.html` 兩個圖示連結的 `?v=` 換成新版本（例如 `20261122c`），推送成功後用它等部署完成：

```bash
bash D:/Claude/Claude-work/scripts/wait-for-portfolio-deploy.sh 20261122c 300 https://kungmengchien.uk/ncut-english/
```

Cloudflare 會快取 HTML 約 10 分鐘、圖片和其他檔案約 4 小時，所以換了圖要換檔名或加新的 `?v=`。

## 著作權與隱私

- 例句出自語言中心公開的題庫 PDF，原始內容來自市售書籍（GEPT Weekly Word Builder 1）。頁面已加 `noindex`，請只在同學之間分享，不公開宣傳。
- 不收集學號、姓名或成績；所有紀錄只在使用者的瀏覽器。

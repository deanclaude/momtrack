# 媽媽追蹤器（MiTag 定時定位紀錄）— 交接摘要

## 架構
1. **電腦端（Windows）**：工作排程器 `MomTrack` 每 30 分鐘執行 `C:\Claude\MomTrack\run.bat`
   → `momtrack.py` 用 [GoogleFindMyTools](https://github.com/leonboe1/GoogleFindMyTools) 向 Google Find Hub 查 MiTag 位置、解密
   → GET 送到 Apps Script（`action=log`），已送過的時間戳記在 `state.json` 去重。
2. **儲存**：Google 試算表 `mom-tracker`（Drive: 我的雲端硬碟/Claude/mom-tracker），分頁 `log`：time(定位器回報時間), lat, lon, accuracy_m, battery, received_at。
3. **Apps Script（Code.gs）**：網頁應用程式，`?token=` 驗證；actions：`log` / `read&date=YYYY-MM-DD` / `latest` / `days`。
4. **航跡頁**：本 repo `index.html` → https://deanclaude.github.io/momtrack/ （Leaflet + OSM；設定存 localStorage）。

## 檔案位置
| 位置 | 內容 |
|---|---|
| `C:\Claude\MomTrack\` | setup.bat、run.bat、schedule.bat、venv（Python 3.14）、GoogleFindMyTools（含 `Auth\secrets.json` 登入憑證，勿外流） |
| `G:\我的雲端硬碟\Claude\mom-tracker\` | momtrack.py、config.json（api / token / device_name / tools_dir）、Code.gs、index.html、momtrack.log、state.json |

## 踩過的坑
- 電腦的 Python 3.12 壞掉 → setup.bat 依序試 `py -3.12/3.13/3.14`，實際用 3.14。
- 第一次登入要跑兩次 Chrome 登入（帳號 token + 端對端加密金鑰，需輸入手機螢幕鎖 PIN）。
- Claude 雲端工作階段無法直接寫入 `C:\Claude`、`G:\` → 檔案由使用者手動複製。
- Claude 無法建立新 repo / 開 Pages → 使用者先建空 repo、手動開 Pages，Claude 負責推送。
- 設定框灰字是 placeholder，網址要實際貼上。

## 限制
- MiTag 只有附近 Android 手機經過時才回報，航跡稀疏；電腦需開機不睡眠。
- 非官方 API，Google 改版可能失效；登入過期時重跑 setup.bat 的登入步驟。
- exec 網址與 token 不寫在此檔。

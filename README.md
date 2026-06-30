# 知識工廠自動化系統

一套「複製網址 → 雙擊執行 → 自動寫入 Obsidian → NotebookLM 深度研究」的個人知識管理自動化流程。涵蓋 YouTube 影片與 Podcast 兩種來源，全程不需手動開終端機打指令。

---

## 一、系統架構

```
[ 步驟 1 ] 瀏覽器複製網址 (Ctrl + C)
      │
      ▼
[ 步驟 2 ] 雙擊點開桌面捷徑 (.bat 檔)
      │
      ├─► 自動讀取剪貼簿內容
      ├─► 安全檢查 (確認網址格式正確)
      ├─► 背景自動下載 (YouTube 字幕 / Podcast 音訊檔)
      └─► 送交 Gemini 進行深度 AI 整理與繁中排版
      │
      ▼
[ 步驟 3 ] 自動寫入 Google 雲端硬碟 (同步至 Obsidian 筆記庫)
      │
      ▼
[ 步驟 4 ] 網頁端 NotebookLM 勾選新檔案 ➔ 開始對 AI 進行深度研究
```

YouTube 與 Podcast 是兩條獨立但結構對稱的處理線，差異只在「來源抓取方式」與「送進 Gemini 的素材形式」（文字逐字稿 vs. 音訊檔）。

---

## 二、專案資料夾結構

```
D:\KnowledgeFactory\
├── .venv\                         # Python 虛擬環境（已裝好所有依賴套件）
├── .env                           # 存放 Gemini API Key（機密，勿分享、勿上傳雲端）
├── sync_agent.py                  # YouTube 字幕抓取 + AI 筆記生成（含個人路徑，不會上傳）
├── podcast_agent.py               # Podcast 音訊下載 + AI 聽寫摘要（含個人路徑，不會上傳）
├── start_youtube_factory.bat      # YouTube 流程一鍵啟動器（含個人路徑，不會上傳）
├── start_podcast_factory.bat      # Podcast 流程一鍵啟動器（含個人路徑，不會上傳）
├── *.example                      # 上述四個檔案的範例版本（已上傳至 GitHub）
└── README.md                      # 本說明文件
```

> ⚠️ **首次設定**：repo 內只會有 `sync_agent.py.example`、`podcast_agent.py.example`、`start_youtube_factory.bat.example`、`start_podcast_factory.bat.example`，因為正式檔案內含你個人的資料夾路徑，已被 `.gitignore` 排除。請複製這四個 `.example` 檔，移除副檔名中的 `.example`（例如 `sync_agent.py.example` → `sync_agent.py`），再依下方說明把裡面的路徑改成你自己的環境路徑。

筆記輸出位置（與 Obsidian 直接連動的雲端硬碟資料夾）：

| 來源 | 輸出路徑 |
|---|---|
| YouTube | `G:\我的雲端硬碟\Knowledge_Base\02_Second_Brain\YouTube_Notes\` |
| Podcast | `G:\我的雲端硬碟\Knowledge_Base\02_Second_Brain\Podcast_Notes\` |

程式執行完畢的瞬間檔案就會寫入這兩個資料夾，但 Google Drive 桌面版同步通常有 **5～30 秒延遲**，Obsidian 不會「秒級」更新，等個十幾秒再重新整理即可。

---

## 三、環境需求與安裝

### 3.1 必要套件

虛擬環境 `.venv` 已預先安裝好以下套件，若需重建環境可參考：

```bash
pip install python-dotenv pyperclip requests "youtube-transcript-api>=1.0"
```

> ⚠️ **版本注意**：`youtube-transcript-api` 在 1.0 版做了重大改版（instance-based API），舊教學文章裡常見的 `YouTubeTranscriptApi.list_transcripts(video_id)` 寫法已被移除。本專案的 `sync_agent.py` 已採用新版寫法（`YouTubeTranscriptApi().list(video_id)`），若未來套件再次更新導致報錯，請優先檢查官方 PyPI 頁面的範例程式碼。

### 3.2 `.env` 設定

在 `D:\KnowledgeFactory\.env` 中放入你的 Gemini API Key（格式依 `google.antigravity` SDK 規範）。

**安全規則：**
- `.env` 絕對不要上傳到 GitHub、雲端硬碟，或包進任何要分享給他人的壓縮檔。
- 若要備份整個 `D:\KnowledgeFactory` 資料夾，請先排除 `.env`。

---

## 四、日常使用流程

### 4.1 YouTube 影片轉筆記

1. 在 YouTube 看到想收藏的影片，複製網址（`Ctrl + C`）。
2. 雙擊桌面的「start_youtube_factory.bat」捷徑。
3. 程式會自動：
   - 驗證剪貼簿內容是否為合法 YouTube 網址
   - 解析出影片 ID
   - 依序嘗試抓取中文 → 英文 → 任何可用語言的字幕（找不到中英字幕時會自動 fallback，並交給 Gemini 翻譯整理）
   - 將逐字稿送交 Gemini，產出結構化的 Markdown 筆記
   - 寫入 Google 雲端硬碟的 `YouTube_Notes` 資料夾
4. 視窗顯示「🎉 大功告成」後，5 秒自動關閉；若失敗則會停留並顯示錯誤訊息，等你按任意鍵才關閉。

### 4.2 Podcast 轉筆記

Podcast 沒有像 YouTube 一樣的公開字幕 API，因此這條線需要多一個手動步驟：

1. 打開 Podcast 的網頁播放頁面，按 `F12` 開啟瀏覽器開發者工具。
2. 切到「Network（網路）」分頁，播放音檔，找到副檔名為 `.mp3` / `.m4a` 的請求，右鍵複製連結網址。
3. 雙擊桌面的「start_podcast_factory.bat」捷徑。
4. 程式會自動：
   - 驗證剪貼簿內容是否為音訊直連網址
   - 下載音訊檔到本機暫存
   - 將音訊上傳給 Gemini 進行多模態聽寫（含時間戳記）
   - 產出包含核心主題、分段觀點、Summary、Takeaways 的筆記
   - 寫入 Google 雲端硬碟的 `Podcast_Notes` 資料夾
   - 自動刪除暫存音訊檔，不佔用本機空間
5. 語音較長時，Gemini 聽寫可能需要 1～3 分鐘，請耐心等候。

### 4.3 匯入 NotebookLM 進行深度研究

1. 打開網頁版 [NotebookLM](https://notebooklm.google.com)，進入你的知識庫筆記本。
2. 點擊「新增來源」→「Google 雲端硬碟」。
3. 勾選剛剛產出的新筆記（YouTube_Notes 或 Podcast_Notes 資料夾內）。
4. 開始對 AI 進行跨筆記的深度提問與研究。

> 這一步刻意保留為**手動操作**：一方面 NotebookLM 目前沒有公開 API 可供腳本直接匯入，另一方面手動勾選能讓你在送進研究庫前先確認筆記品質，避免低品質內容污染知識庫。

---

## 五、疑難排解

| 錯誤訊息 / 現象 | 可能原因 | 處理方式 |
|---|---|---|
| `'YouTubeTranscriptApi' has no attribute 'list_transcripts'` | 套件版本為 1.x，API 已改版 | 確認 `sync_agent.py` 是否為最新版本（使用 `.list()` 而非 `.list_transcripts()`） |
| 抓字幕失敗，但影片明明有字幕 | YouTube 暫時阻擋該 IP、或字幕語言非中英文 | 稍後重試；若為冷門語言，程式已有 fallback 機制會自動抓取並交給 Gemini 翻譯 |
| `model produced an invalid tool call`、`is not a valid artifact path` | 模型自己嘗試呼叫 Antigravity 內建的 `create_file` 工具，想把筆記直接寫到沙盒外的路徑(如 `D:\KnowledgeFactory\...`)，但 Antigravity 規定工具產生的檔案只能寫進 `~/.gemini/antigravity/brain/<session_id>/` 沙盒資料夾 | 已在 `system_instructions` 中明確禁止模型呼叫任何檔案工具，要求它只回覆純文字筆記內容，檔案寫入完全交由 Python 腳本的 `open().write()` 處理 |
| `503` / `high demand` / `UNAVAILABLE` | Google 後端 Gemini 模型當下流量過大，屬於暫時性問題，與你的程式碼或網路無關 | 已將重試機制擴大偵測這類關鍵字，遇到時會自動以 8s/16s/32s/64s 間隔重試最多 4 次；若 4 次都失敗，代表伺服器負載持續偏高，建議等幾分鐘後再重新執行 |
| Gemini 處理時跳出速率限制相關錯誤 | 短時間內處理過多影片/音訊，觸發 API 配額限制 | 程式已內建自動重試（最多 4 次，8s/16s/32s/64s 退避），若仍失敗請間隔幾分鐘後再執行 |
| Podcast 下載失敗，代碼 403 / 404 | 複製到的音訊連結可能有時效性或需要驗證標頭 | 重新整理 Podcast 頁面，重新播放並複製新的連結 |
| 視窗一閃就消失，看不到錯誤 | 舊版 `.bat` 沒有錯誤偵測機制 | 確認 `.bat` 是否為最新版（含 `if %errorlevel% neq 0 pause` 機制） |
| Obsidian 沒看到新筆記 | Google Drive 同步延遲 | 等待 10～30 秒後重新整理 Obsidian |
| `ModuleNotFoundError: No module named 'dotenv'`（或任何已安裝過的套件報錯找不到） | 改過資料夾名稱後，`activate.bat` 內部寫死了建立虛擬環境當時的舊絕對路徑（如 `D:\Antigravity\.venv`），導致 `activate` 沒有正確生效，`.bat` 實際上用系統版 Python 執行，而非虛擬環境版本 | `.bat` 已改為直接呼叫 `.venv\Scripts\python.exe` 的絕對路徑，不再依賴 `activate.bat`；若未來又改資料夾名稱，記得同步更新 `.bat` 內的路徑 |

---

## 六、安全與隱私須知

- `.env` 內含個人 API Key，外洩後他人可能用你的額度呼叫 Gemini，產生非預期費用或濫用紀錄。
- 本系統不會將任何資料上傳到除 Google 雲端硬碟與 Gemini API 之外的第三方服務。
- Podcast 音訊檔僅在本機暫存處理，處理完畢即自動刪除。

---

## 七、版本紀錄

| 日期 | 變更內容 |
|---|---|
| v1.0 | 初版：YouTube 字幕轉筆記、Podcast 音訊轉筆記、自動寫入雲端硬碟 |
| v1.1 | 補上 Podcast 專用 `.bat` 啟動器；`.bat` 加入失敗暫停機制；Gemini 呼叫加入速率限制自動重試；字幕抓取加入多層語言 fallback |
| v1.2 | 修正 `youtube-transcript-api` 1.x 版破壞性改版造成的 `list_transcripts` 失效問題，改用新版 instance-based API |
| v1.3 | 修正模型自行呼叫 `create_file` 工具寫入沙盒外路徑導致的 invalid tool call 錯誤（已在 system_instructions 中禁止模型呼叫任何檔案工具）；擴大重試機制偵測範圍至 503 / high demand / unavailable 等暫時性伺服器過載錯誤；修正重試次數訊息的誤導性顯示 |
| v1.4 | 資料夾從 `D:\Antigravity` 改名為 `D:\KnowledgeFactory` 後，`.bat` 改用虛擬環境內 `python.exe` 的絕對路徑直接執行，不再透過 `activate.bat`（修正 activate.bat 內硬編碼舊路徑導致跑到系統版 Python、缺少套件的問題） |

---

*最後更新：本文件對應的程式碼版本為 v1.4。*
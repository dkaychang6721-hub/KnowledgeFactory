# 安裝步驟（給第一次拿到這個專案的人）

這個 repo 為了不洩漏原作者的個人路徑與 API Key，刻意排除了幾個檔案。請依照以下步驟，把它變成你自己能跑起來的版本。

---

## 1. 安裝必要軟體

- [Git](https://git-scm.com/)
- [Python 3.10+](https://www.python.org/)（安裝時記得勾選 "Add Python to PATH"）

---

## 2. Clone 專案

```bash
git clone https://github.com/dkaychang6721-hub/KnowledgeFactory.git
cd KnowledgeFactory
```

---

## 3. 建立虛擬環境並安裝套件

```bash
python -m venv .venv
.venv\Scripts\activate
pip install python-dotenv pyperclip requests google-antigravity "youtube-transcript-api>=1.0"
```

---

## 4. 設定 `.env`（Gemini API Key）

複製範例檔並改名：

```bash
copy .env.example .env
```

打開 `.env`，填入你自己的 Gemini API Key：

```
GEMINI_API_KEY=你的金鑰
```

> Key 可在 [Google AI Studio](https://aistudio.google.com/) 申請。`.env` 絕對不要上傳到 GitHub。

---

## 5. 複製並修改四個 `.example` 檔

把以下檔案複製一份，移除檔名最後的 `.example`：

| 範本檔 | 改名為 |
|---|---|
| `sync_agent.py.example` | `sync_agent.py` |
| `podcast_agent.py.example` | `podcast_agent.py` |
| `start_youtube_factory.bat.example` | `start_youtube_factory.bat` |
| `start_podcast_factory.bat.example` | `start_podcast_factory.bat` |

接著打開改名後的檔案，把裡面的佔位路徑改成你自己的實際路徑：

- `sync_agent.py` 和 `podcast_agent.py` 裡的 `output_dir = r"C:\path\to\your\Notes\..."` → 改成你自己存放筆記的資料夾（例如 Obsidian vault 內的某個子資料夾）
- `start_youtube_factory.bat` 和 `start_podcast_factory.bat` 裡的 `C:\path\to\your\KnowledgeFactory` → 改成你 clone 下來的實際專案路徑

---

## 6. 測試執行

1. 複製一個 YouTube 影片網址
2. 雙擊 `start_youtube_factory.bat`
3. 確認筆記有正確寫入你在步驟 5 設定的輸出資料夾

成功後即可比照辦理測試 `start_podcast_factory.bat`。

---

## 7. （選用）建立桌面捷徑

對 `start_youtube_factory.bat` / `start_podcast_factory.bat` 按右鍵 → 傳送到 → 桌面（建立捷徑），之後就能雙擊桌面圖示直接執行。

---

更詳細的功能說明、疑難排解請參考 [README.md](README.md)。

# NeuroQuery — Text-to-SQL for Analysts（中英雙語）

<p align="center">
  <img src="https://img.shields.io/badge/Type-Text_to_SQL-blue" />
  <img src="https://img.shields.io/badge/Frontend-Streamlit-informational" />
  <img src="https://img.shields.io/badge/LLM-Ollama_phi4-success" />
  <img src="https://img.shields.io/badge/DB-MySQL-lightgrey" />
  <img src="https://img.shields.io/badge/License-MIT-brightgreen" />
</p>

將「自然語言」直接轉成 **SQL**，查詢 **MySQL** 並自動生成**結果解釋**。本專案採用 **Streamlit** 作為前端、
**Ollama + phi4** 作為語言模型，定位為 **作品集展示型**：清楚、可重現、好 Demo。

> This is a portfolio‑ready, reproducible Text‑to‑SQL system. Type a question in natural language, get the SQL, run it on MySQL, and receive an auto‑generated explanation of the result.

---

## 🔎 Why this project? 為什麼做這個
資料分析同仁、商業使用者、以及新進工程師，經常 **知道想問的問題** 卻 **不熟 SQL**。
本系統讓他們可以用中文問句完成查詢，同時保留工程師可讀的 SQL 以便審閱與調試。

**核心價值**
- ⌨️ 自然語言 → SQL，自動生成且可複用  
- 📊 一鍵執行 SQL 並表格呈現  
- 🗣️ LLM 將結果「口語解釋」  
- 🧱 保留可審核軌跡（Prompt、Schema、SQL、結果）

---

## 🧩 Features 功能
- 支援 **中文/英文** 自然語言輸入
- 基於 **資料庫 Schema** 生成 **可審查** 的 SQL
- 以 **pandas** 呈現查詢結果（支援 CSV 下載）
- 自動生成 **結果解釋**（LLM）
- 以 **環境變數** 管理敏感設定（`.env`）

> **Safety Note**：Text‑to‑SQL 容易出現不準確或風險性的語句。請在執行前 **審閱 SQL**，必要時加上白名單/唯讀權限。

---

## 🏗 Architecture 架構
```
flowchart LR
    A[User 問句] --> B[Streamlit UI]
    B --> C[LLMHandler<br/>Ollama(phi4)]
    C --> D[SQL 產生器]
    D -->|SQL| E[DatabaseManager<br/>MySQL]
    E --> F[pandas DataFrame]
    F --> G[結果解釋 LLM]
    G --> H[UI 顯示 / 匯出]
```
**Modules**
- `app.py`：Streamlit 互動頁
- `llm_handler.py`：與 Ollama 溝通，產生 SQL、結果解釋
- `database.py`：資料庫連線與查詢
- `config.py`：環境變數與系統提示詞設定

---

## 🚀 Quick Start 快速開始

### 1) 環境需求
- Python 3.10+
- MySQL 8+（唯讀帳號即可）
- [Ollama](https://ollama.com/) 已安裝並下載 `phi4` 模型  
  ```bash
  ollama pull phi4
  ollama serve
  ```

### 2) 安裝依賴
```bash
pip install -r requirements.txt
```

### 3) 設定 `.env`
建立 `.env`（可參考 `.env.example`）：
```dotenv
# DB
DB_HOST=localhost
DB_USER=your_username
DB_PASSWORD=your_password
DB_NAME=your_database

# LLM
OLLAMA_MODEL=phi4
OLLAMA_HOST=http://localhost:11434
```

### 4) 啟動介面
```bash
streamlit run app.py
```

> 打開瀏覽器，依序輸入：**資料庫 Schema**（或自動偵測）、**自然語言問題** → 生成與執行 SQL → 查看結果與說明。

---

## 🧠 Prompting & SQL Guardrails 提示與防呆
- 生成 SQL 前，會把 **資料表結構（Schema）** 一併送入模型
- 對於 **DELETE/UPDATE/INSERT** 等具風險語句，可在 `app.py` 中 **阻擋或要求再次確認**
- 建議在資料庫層面提供 **唯讀帳號**、加上查詢 **timeout**

**常見防呆策略**
- 僅允許 `SELECT`
- 加表別名長度限制、禁止 `SELECT *`
- 控制 `LIMIT`（避免掃全表）

---

## 💡 Example Examples 範例
**NL 問題（中文）**
> 「列出 2024 年 5 月銷售額前 10 名的商品與金額」

**Generated SQL**
```sql
SELECT product_name, SUM(amount) AS total_sales
FROM sales
WHERE sale_date >= '2024-05-01' AND sale_date < '2024-06-01'
GROUP BY product_name
ORDER BY total_sales DESC
LIMIT 10;
```

**LLM 解釋**
> 以上查詢統計 2024/05 的銷售總額，並列出前 10 名商品。

---

## 🧪 Testing 測試建議
- 準備 **迷你資料庫**（3~5 張表）與 **對應 Schema**
- 列出 10 組 **Ground Truth 問題/SQL**，比對模型輸出
- 加入 **負面案例**（例如錯欄位、錯表名、模稜兩可語句）觀察模型表現

---

## 🛠 Roadmap 後續規劃
- [ ] SQL 白名單 / 黑名單機制
- [ ] Schema 自動抽取（連線 DB 後自動抓欄位與型別）
- [ ] 支援 **PostgreSQL / MSSQL**
- [ ] 模型切換（`phi4`、`llama3`、`qwen`）
- [ ] 查詢快取與查詢成本統計
- [ ] docker-compose 一鍵啟動

---

## 📂 Project Structure 專案結構
```
NeuroQuery-main/
├─ app.py
├─ config.py
├─ database.py
├─ llm_handler.py
├─ requirements.txt
└─ README.md  ←（你現在看到的）
```

---

## 📜 License
MIT

---

## 🙌 Acknowledgements
- [Streamlit](https://streamlit.io/)
- [Ollama](https://ollama.com/)
- Emoji icons by Twemoji

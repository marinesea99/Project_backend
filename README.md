# 基於機器學習與深度學習之加密貨幣走勢預測與穩定幣脫鉤風險預警系統

這裡整理的是小組專題的 Python 後端程式，主要負責取得行情資料、建立模型輸入、執行預測，再透過 API 將結果提供給 Flutter 前端。

**系統網站：** https://quantproject-frontend.onrender.com/

**前端程式：** [Project_Flutter](https://github.com/marinesea99/Project_Flutter)

## 目前的功能

### 加密貨幣走勢預測

目前支援 BTC、ETH、SOL、XRP 四種幣別。後端利用 Binance K 線資料建立特徵，載入各幣種對應的 Transformer 模型，計算未來 4 小時的方向分數。

API 回傳目前價格、上漲分數、判斷門檻、門檻差距、預測方向、模型資料截至時間及 OHLCV K 線。方向分數是模型判斷結果，不代表價格一定會上漲。

### 穩定幣脫鉤風險分析

目前支援 USDC 與 TUSD，使用 XGBoost、Transformer 及模型組合進行分析。後端包含分類模型、價格迴歸模型，以及執行預測需要的標準化檔案。

主要輸出包括目前價格、預估價格、價格差異、風險分數及風險等級。不同模型的預測目標與時間範圍可能不同，查看結果時需要一起參考模型說明。

## 專案結構

```text
Project_backend/
├── main.py
├── data_fetchers/
│   ├── binance_api.py
│   └── crypto_api.py
├── models/
│   ├── cryptos/
│   │   ├── btc_inference_features.py
│   │   ├── crypto_inference_features.py
│   │   ├── cryptos_predictor.py
│   │   └── weights/
│   └── stablecoins/
│       ├── predictor.py
│       └── weights/
├── requirements.txt
├── .python-version
├── render.yaml
├── SOURCE_ATTRIBUTION.md
└── README.md
```

`main.py` 負責 FastAPI 服務、API 請求及資料回傳。

`data_fetchers/` 負責行情資料取得與特徵整理。

`models/` 存放加密貨幣及穩定幣的推論程式和模型檔案。部分權重與 scaler 檔名相似，但目前程式會依照指定檔名載入，因此不建議隨意刪除。

## API

| 方法 | 路徑 | 說明 |
| --- | --- | --- |
| GET | `/health` | 檢查服務狀態 |
| GET | `/api/v1/dashboard/overview` | 後端取得行情並執行分析 |
| POST | `/api/v1/dashboard/overview/from-klines` | 接收前端傳入的 K 線並執行分析 |

目前 Flutter 前端使用 POST 版本，先向 Binance 取得 USDCUSDT、TUSDUSDT、BTCUSDT、ETHUSDT、SOLUSDT、XRPUSDT 的 1 小時 K 線，再傳給後端。

後端處理完成後，回傳的 JSON 主要包含 `stablecoins` 與 `cryptos`，供前端顯示模型結果及 K 線圖表。

## 使用技術

- Python、FastAPI
- PyTorch、XGBoost、scikit-learn
- pandas、NumPy
- Binance 公開行情 API

## 本機執行

儲存庫中的 `.python-version` 目前指定 Python 3.14.7。安裝前需要先確認套件版本相容。

```bash
git clone https://github.com/marinesea99/Project_backend.git
cd Project_backend
python -m venv .venv
```

Windows PowerShell 啟用虛擬環境：

```powershell
.venv\Scripts\Activate.ps1
```

安裝依賴套件並啟動：

```bash
python -m pip install -r requirements.txt
uvicorn main:app --reload
```

啟動後可以開啟：

- API 文件：`http://127.0.0.1:8000/docs`
- 健康檢查：`http://127.0.0.1:8000/health`

專案另有 `render.yaml` 作為 Render 部署設定。實際執行仍需確認套件安裝、模型載入及外部行情服務是否正常。

## 專題分工與程式來源

這是小組共同開發的專題。後端包含行情資料處理、模型推論與 API 整合；Flutter 前端負責介面、圖表及模型結果呈現。

本儲存庫整理自[團隊後端儲存庫](https://github.com/REX-JO/quantproject-backend)，來源版本記錄在 [SOURCE_ATTRIBUTION.md](SOURCE_ATTRIBUTION.md)。

目前主要保存推論服務及執行需要的模型檔案，並未收錄所有訓練資料、實驗紀錄及測試報告。

本專題用於研究與成果展示，不構成投資建議。

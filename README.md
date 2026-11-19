# EDUTrack — 遠端學習監控系統

EDUTrack 是一套以 Python 開發的 **主從式（client / server）遠端監控工具**，
使Client端（例如家長、老師) 可以遠端查看與協助另一台電腦：
即時螢幕畫面、攝影機畫面、截圖與錄影、瀏覽紀錄，以及遠端滑鼠／鍵盤...等，多種功能的協助。

---

## ⚠️ 使用前必讀

本工具只能用於**你擁有、或已取得當事人明確同意**的裝置。

- 操作本專案前，因被監控端(Server端)
- 本專案僅為小型專題，請勿將此引用至其他任何非法作業上。
- 未經同意監看他人電腦、側錄或竊取他人資料，在多數國家與地區屬違法行為。請勿如此使用。

---

## 功能

| 功能 | 說明 |
| --- | --- |
| 即時螢幕畫面 | 將被監控端的螢幕即時串流給控制端 |
| 即時攝影機畫面 | 將被監控端的攝影機畫面即時串流 |
| 螢幕截圖 | 擷取指定張數的螢幕截圖並回傳 |
| 螢幕錄影 | 錄製指定秒數的螢幕畫面（MP4） |
| 攝影機錄影 | 以 OpenCV 錄影、PyAudio 錄音，FFmpeg 合併為 MP4 |
| 瀏覽紀錄 | 讀取 Chrome 歷史紀錄並整理成 CSV，控制端可用 Klazify API 分類 |
| 遠端滑鼠／鍵盤 | 控制端可協助操作被監控端的滑鼠與鍵盤 |


## 系統架構

```
┌─────────────────────────┐          指令 (TCP)          ┌─────────────────────────┐
│   控制端 client_gui.py   │ ──────────────────────────▶ │   被監控端 server.py     │
│   （家長 / 老師）         │                             │                          │
│   Tkinter 圖形介面       │ ◀────────────────────────── │   接收指令、執行、回傳     │
└─────────────────────────┘        資料 / 畫面串流         └─────────────────────────┘
```

- **server.py**：跑在被監控的電腦上，監聽 `0.0.0.0:10000`，等待控制端連線。
- **client_gui.py**：跑在控制端，輸入對方 IP 與連接埠後連線、下指令、顯示回傳畫面與資料。


> Windows 上安裝 PyAudio 若失敗，可改用 `pip install pipwin && pipwin install pyaudio`。

## 安裝

需要 Python 3.10 以上。相依套件依「執行端」拆成兩份，**每台電腦只裝自己角色需要的那份**：

**控制端（跑 `client_gui.py` 的那台）**
```bash
pip install -r requirements-client.txt
```

**被監控端（跑 `server.py` 的那台）**
```bash
pip install -r requirements-server.txt
```

兩端共用 `opencv-python`、`numpy`、`pynput`。
被監控端的攝影機錄影還需要系統層級的 [FFmpeg](https://ffmpeg.org/)（裝好並加入 PATH）。

> Windows 上安裝 PyAudio 若失敗，可改用 `pip install pipwin && pipwin install pyaudio`。


## 使用方式

**被監控端（要被查看的電腦）**
```bash
python server.py
```
啟動後會跳出同意視窗，按「我同意」後才開始監聽，螢幕上方出現監控中橫幅。

**控制端（查看方）**
```bash
python client_gui.py
```
在介面中輸入被監控端的 IP 與連接埠（預設 `10000`），按 Connect，即可選擇指令。

## 專案結構

```
EDUTrack/
├── server.py                       # 入口｜被監控端：接收指令並執行
├── client_gui.py                   # 入口｜控制端：圖形介面
├── capture_and_monitor/            # 擷取類模與監控組
│   ├── camera_record.py            # 攝影機 + 麥克風錄影
│   ├── keylogger.py                # 讀取鍵盤紀錄
│   ├── screen_recording.py         # 螢幕錄影
│   ├── screenshot.py               # 截圖
│   └── web_history.py              # 讀取瀏覽紀錄
├── input_control/                  # 遠端控制類模組
│   ├── mouse_control.py            # 遠端滑鼠
│   └── remote_control_keyboard.py  # 遠端鍵盤
├── system_control/                 # 遠端控制類模組
│   ├── close.py                    # 遠端關機
│   └── reverse_shell.py            # 反向傳輸
├── requirements.txt
├── .gitignore
└── README.md
```

## 技術棧

Python · Socket (TCP) · Tkinter · OpenCV · NumPy · PyAutoGUI · pynput · PyAudio · FFmpeg · Pandas · Matplotlib · Klazify API

## 已知限制

- 以 Windows 為主要開發與測試環境（瀏覽紀錄路徑、螢幕解析度等為 Windows 寫法）。
- 畫面串流採未壓縮的原始影格，在頻寬較低時延遲較明顯。
- 目前連線未加密，建議僅在信任的區域網路內使用。


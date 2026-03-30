# KDIGO 2026 AKI Staging Calculator

## 1. 系統結論與定位
本專案實作 KDIGO 2026 臨床實踐指引草案（Clinical Practice Guideline for AKI and AKD, March 2026 Public Review Draft）之急性腎損傷（AKI）分期演算法。核心目標為將傳統單一最高級別分期，轉換為精準醫療導向的 **C-U-B（Creatinine, Urine, Biomarker）多維度獨立分期系統**。

## 2. 客觀數據：系統輸入與輸出規格

本系統依據 KDIGO 2026 Table 6 定義建構判讀邏輯：

| 評估維度 | 系統輸入參數 (Inputs) | 演算法邏輯 (Logic) | 輸出結果 (Outputs) |
| :--- | :--- | :--- | :--- |
| **C-Stage** | Baseline SCr, Current SCr, 48h/7d 時間窗, RRT 狀態 | 判定倍數變化 (1.5x, 2.0x, 3.0x)、絕對值增量 (≥0.3)、是否啟動 RRT | C0, C1, C2, C3 |
| **U-Stage** | 體重 (kg), 總尿量 (mL), 收集時間 (h) | 換算速率 (mL/kg/h)，比對 6h / 12h / 24h 門檻，並結合無尿判斷 | U0, U1, U2, U3 |
| **B-Stage** | 結構性生物標記 (如 NGAL, TIMP-2*IGFBP7 等) | 依臨床檢驗結果直接分類 | Not Evaluated, B0, B1 |
| **綜合輸出** | 上述三維度陣列資料 | 字串串接與風險顏色格式化 | 例：`AKI Profile: C1 U2 B1` |

## 3. 推論觀點：演算法變更與臨床意義

本工具相較於基於 KDIGO 2012 的舊版計算器，具備以下演算法層級的修正與臨床推論：
* **解耦分期邏輯（重要修正）：** 舊版指引取 `Max(SCr Stage, UO Stage)` 視為最終分期，此邏輯存在缺陷。臨床上單純的血流動力學改變（高 U-Stage）與實質結構損傷（高 B-Stage / C-Stage）具備完全不同的預後意義。本系統強制獨立呈現 C-U-B，以利精確反映病人生理狀態。
* **尿量時間矩陣嚴謹化：** 嚴格對應 `<0.5 mL/kg/h` 發生於 6-12h (U1) 或 >12h (U2) 的切點，並加入無尿 (Anuria) >12h 自動判定為 U3 的邏輯。
* **干擾因子具象化：** 將可能導致偽陰性或偽陽性的臨床因子（詳見系統限制）納入表單，動態生成防呆警示，降低過度診斷或漏診風險。

## 4. 客觀限制與防呆機制 (Limitations & Confounders)

依據 KDIGO 2026 Table 2，本系統演算法在以下情境需由專家校正判讀，系統內已內建警示機制：
1. **藥物干擾：** 使用利尿劑可能強制拉高尿量，掩蓋真實的 U-Stage；使用 RASi 或 SGLT2i 可能導致 SCr 上升，此屬血流動力學效應而非實質結構損傷。
2. **基期生理極端值：** 肌肉量極低或肝功能不全者，Baseline SCr 基準偏低，易導致 C-Stage 診斷延遲或漏診。
3. **生理性過濾增加：** 孕婦具備生理性 GFR 增加，可能掩蓋早期的 SCr 上升。
4. **小兒族群：** 18歲以下兒童需使用特定 eGFR 公式（如 Schwartz equation）與小兒專屬尿量切點，未納入本計算器涵蓋範圍。

## 5. 部署與執行 (Deployment)
本專案為純前端靜態單頁應用 (SPA)，以 HTML/CSS/Vanilla JS 撰寫，無後端伺服器依賴，確保病患數據之隱私安全性。
* **Live Demo:** [在此填入你的 Github Pages 連結]
* **Usage:** `Clone` 或下載 `index.html` 即可於任何現代瀏覽器離線執行。

## 6. Disclaimer
**本工具僅供醫療專業人員教育與學術評估使用。** 計算結果不得取代臨床醫師之專業綜合判斷。

## 7. References
* KDIGO 2026 Clinical Practice Guideline for Acute Kidney Injury (AKI) and Acute Kidney Disease (AKD) - Public Review Draft, March 2026.

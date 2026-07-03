# 4.1 功能設計對應表

> 本文件將 Gamut（CHI 2019）定義的 C1–C6 六項可解釋性能力，對應到本專題五個作品的 UI 元件，並進行 persona × 場景分析與缺口反思。

---

## 一、五個作品定義

| 作品編號 | 名稱 | 簡述 |
|----------|------|------|
| **作品 1** | Risk Score Card（風險評分卡） | 以視覺化色帶 + 數字呈現個別病患的整體預測風險分數與信賴區間 |
| **作品 2** | What-If Slider（反事實探索器） | 讓醫師調整單一特徵數值，即時觀察預測分數變化 |
| **作品 3** | Explanation Method Comparator（解釋方法比較器） | 並排顯示 SHAP、LIME、Gamut-GAM 三種解釋方法對同一病患的特徵歸因結果 |
| **作品 4** | Clinical Narrative Report（臨床敘事報告） | 將模型解釋自動轉換為醫師可讀的段落式文字摘要 |
| **作品 5** | Population Feature Importance Dashboard（族群特徵重要性儀表板） | 以長條圖 + 密度直方圖呈現所有病患的全局特徵重要性與模型不確定性區域 |

---

## 二、C1–C6 × 5 作品矩陣

> 每格寫具體 UI 元件名，或 `N/A（原因）`。

|  | 作品 1<br>Risk Score Card | 作品 2<br>What-If Slider | 作品 3<br>Explanation Comparator | 作品 4<br>Clinical Report | 作品 5<br>Population Dashboard |
|--|--|--|--|--|--|
| **C1**<br>局部實例解釋 | **Waterfall Badge**<br>色帶條 + 各特徵貢獻值標籤 | **Feature Delta Label**<br>slider 旁顯示目前特徵對分數的即時貢獻量 | **Multi-Method Feature Bar**<br>三列並排的個別特徵歸因長條 | **Contribution Paragraph**<br>「本次預測主要由 X、Y、Z 三項因素驅動……」段落 | N/A（作品 5 以族群為單位，不呈現單一實例局部解釋） |
| **C2**<br>實例解釋比較 | N/A（單一病患設計，無多實例並排） | N/A（單特徵維度探索，非實例間比較） | **Side-by-Side Method Panel**<br>SHAP / LIME / GAM 三欄並排同一病患的歸因（此處比較對象是「解釋方法」而非不同病患，仍屬 C2 廣義變體） | **Comparison Summary Sentence**<br>「與上週入院的相似病患相比，本患者風險因子分布……」 | **Cohort Comparison Toggle**<br>切換顯示「高風險組 vs. 低風險組」的特徵重要性對比 |
| **C3**<br>反事實 | N/A（靜態呈現當前預測，不支援假設修改） | **What-If Slider**<br>核心元件；拖動特徵值 → 即時更新預測分數 + 決策邊界標記 | N/A（比較的是解釋方法，非假設情境） | **Scenario Text Block**<br>「若將 eGFR 提升至 ≥ 60，預測風險將降低約 18%……」反事實文字敘述 | N/A（全局族群視角，不針對單一實例做假設修改） |
| **C4**<br>最近鄰居 | **Similar Case Chip**<br>右下角顯示「3 位特徵相似病患」小卡（可展開） | **Nearest Real Anchor**<br>slider 拖動時高亮訓練資料中最近真實案例的預測值（防止調整至資料外推區域） | N/A（方法比較不需要鄰居資訊） | **Analogous Case Reference**<br>「歷史上特徵最相似的 5 位病患，其實際預後為……」段落 | N/A（全局視角，不適合呈現個別鄰居） |
| **C5**<br>誤差區域／不確定性 | **Confidence Interval Band**<br>分數旁顯示 bagging 信賴帶寬度 `(upper−lower)/2`；寬度 > 閾值時顯示警示標籤 | **Uncertainty Warning Flag**<br>當 slider 調整至資料稀疏區（信賴帶寬驟增）時，出現橘色旗標警示「此區域預測不可靠」 | TBD — 各方法的不確定性尚未納入比較框架；未來可加入各方法的解釋穩定性指標 | **Uncertainty Caveat Sentence**<br>「本預測信賴帶寬度較大（±0.12），建議搭配臨床判斷……」免責段落 | **Uncertainty Heatmap Strip**<br>位於特徵重要性長條圖下方；色帶顯示各特徵值分布區間內的平均信賴帶寬（寬 = 紅，窄 = 綠） |
| **C6**<br>特徵重要性 | **Top 3 Risk Factor Tags**<br>病患卡片底部顯示對該患者預測貢獻前三名的特徵標籤 | **Feature Importance Rank Label**<br>各 slider 旁顯示全局重要性排名（例如「#2 最重要特徵」），引導醫師優先調整重要特徵 | **Importance Consistency Badge**<br>當三種方法對同一特徵的重要性排名一致時，顯示「✓ 三法一致」徽章 | **Risk Factor Headline**<br>報告首段以「最關鍵風險因子：eGFR、年齡、BMI」作為摘要標題 | **Global Feature Importance Bar Chart**<br>核心元件；density 加權 \|scores\| 平均 → 排序後橫向長條圖，顯示 Top 10 特徵 |

---

## 三、各作品 UI 元件清單

### 作品 1 — Risk Score Card

| 元件名 | 對應 C | 臨床場景的動作 |
|--------|--------|----------------|
| Waterfall Badge（色帶貢獻條） | C1 | 醫師在 30 秒內掃視各特徵對風險分數的個別貢獻 |
| Confidence Interval Band（信賴帶） | C5 | 評估此次預測是否可信，決定是否要求額外檢查 |
| Top 3 Risk Factor Tags（前三風險因子標籤） | C6 | 快速辨識本患者的主要風險驅動因子，作為問診重點 |
| Similar Case Chip（相似案例小卡） | C4 | 查看歷史相似病患的實際結果，輔助預後判斷 |

### 作品 2 — What-If Slider

| 元件名 | 對應 C | 臨床場景的動作 |
|--------|--------|----------------|
| What-If Slider（主 slider） | C3 | 調整可介入特徵（如藥物劑量、生活習慣）觀察風險變化 |
| Feature Delta Label（即時貢獻標籤） | C1 | 在調整過程中理解單一特徵對目前預測的量化貢獻 |
| Nearest Real Anchor（最近真實錨點） | C4 | 確認假設情境是否在訓練資料分布內，避免過度外推 |
| Uncertainty Warning Flag（不確定警示旗） | C5 | 調整至資料稀疏區時獲得視覺警示，避免過度信任邊緣預測 |
| Feature Importance Rank Label（重要性排名標籤） | C6 | 引導醫師優先嘗試重要特徵的調整，效率化探索 |
| Decision Boundary Marker（決策邊界標記） | C3 | 直接顯示「還需改變多少才能翻轉決策」，減少盲目試探 |

### 作品 3 — Explanation Method Comparator

| 元件名 | 對應 C | 臨床場景的動作 |
|--------|--------|----------------|
| Side-by-Side Method Panel（三法並排面板） | C1、C2 | 住院醫師個案討論時評估不同解釋方法的一致性 |
| Multi-Method Feature Bar（多方法特徵歸因長條） | C1 | 檢視各方法對單一特徵的歸因數值是否一致 |
| Importance Consistency Badge（三法一致徽章） | C6 | 快速辨識跨方法穩定的特徵，提升解釋可信度 |

### 作品 4 — Clinical Narrative Report

| 元件名 | 對應 C | 臨床場景的動作 |
|--------|--------|----------------|
| Risk Factor Headline（風險因子標題） | C6 | 醫師在查房前 10 秒掌握最關鍵風險因子 |
| Contribution Paragraph（貢獻段落） | C1 | 以自然語言理解各特徵對預測的貢獻，適合非技術背景醫師 |
| Scenario Text Block（反事實情境段落） | C3 | 閱讀治療建議時理解介入效果的量化預期 |
| Analogous Case Reference（類似案例參考） | C4 | 以歷史類似案例佐證預測，增強臨床信任感 |
| Uncertainty Caveat Sentence（不確定免責句） | C5 | 提醒醫師模型信賴度有限，保持臨床自主判斷 |
| Comparison Summary Sentence（比較摘要句） | C2 | 相對於參考組了解本患者的風險特徵差異 |

### 作品 5 — Population Feature Importance Dashboard

| 元件名 | 對應 C | 臨床場景的動作 |
|--------|--------|----------------|
| Global Feature Importance Bar Chart（全局重要性長條圖） | C6 | 科主任或研究者了解整體族群的主要風險驅動因子 |
| Uncertainty Heatmap Strip（不確定性熱圖帶） | C5 | 識別模型在哪些特徵區間的預測最不穩定，判斷資料蒐集缺口 |
| Cohort Comparison Toggle（族群比較切換） | C2 | 比較高低風險族群的特徵重要性差異，輔助臨床政策制定 |

---

## 四、與 Gamut 三視圖的對應

### 對應關係

| Gamut 視圖 | 功能 | 本專題對應作品 |
|-----------|------|----------------|
| **Shape Curve View**<br>（shape function 折線圖 + 資料密度直方圖） | 全局特徵貢獻函數視覺化（C5、C6） | **作品 5** Population Dashboard 的 Global Feature Importance Bar Chart + Uncertainty Heatmap Strip |
| **Instance Explanation View**<br>（waterfall chart 瀑布圖） | 局部實例解釋（C1、C2） | **作品 1** Risk Score Card 的 Waterfall Badge；**作品 4** Clinical Report 的 Contribution Paragraph |
| **Interactive Table**<br>（可排序 / 篩選的原始資料網格） | 資料探索 + 最近鄰居（C2、C4） | **作品 3** Explanation Comparator（橫向比較邏輯類似表格操作）；作品 1 的 Similar Case Chip |

### 本專題有、Gamut 沒有的

| 元件 / 功能 | 說明 |
|-------------|------|
| **What-If Slider**（作品 2） | Gamut 的 C3 是透過滑鼠 hover 在折線圖上實現，本專題將其獨立為完整的 slider 互動元件，更適合臨床觸控場景 |
| **Clinical Narrative Report**（作品 4） | Gamut 完全沒有自然語言輸出層；本專題新增 AI 生成段落報告，對應非技術背景的臨床使用者 |
| **Explanation Method Comparator**（作品 3） | Gamut 只呈現 GAM 的解釋；本專題加入 SHAP / LIME 的多方法比較，屬於元解釋（meta-explanation）層 |
| **Decision Boundary Marker** | Gamut 不顯示「翻轉閾值」；本專題在 slider 上標示決策邊界，讓 C3 更具行動導向 |

### Gamut 有、本專題沒有（或未完整實作）的

| Gamut 功能 | 說明 | 處置 |
|-----------|------|------|
| **Normalize Toggle**（shape function 共同刻度切換） | 讓使用者在統一刻度下比較各特徵影響幅度 | 作品 5 的長條圖部分替代，但缺乏動態切換功能 |
| **跨實例即時 brush**（Interactive Table hover → shape curve 更新） | Gamut 三視圖緊密連動 | 本專題各作品相對獨立，缺乏跨作品的即時連動介面 |
| **完整資料密度直方圖**（附於 shape function 下方） | 提示資料稀疏區（C5） | 作品 5 的 Uncertainty Heatmap Strip 部分替代，但不如 Gamut 直觀 |

---

## 五、Persona × 場景矩陣

### Persona 定義

**Persona A — 主治醫師（陳醫師，48 歲）**
- 情境：門診看診中，每位病患約 10–15 分鐘
- 需求：30 秒內取得關鍵風險摘要，快速判斷是否需要進一步介入
- 技術背景：低；不理解 SHAP 數值，但能理解「因為腎功能差所以風險高」

**Persona B — 住院醫師（林醫師，28 歲）**
- 情境：晨間個案討論，針對 3–5 位高風險患者進行深度分析
- 需求：可深入比較、探索原因，能向主治醫師報告「模型為什麼這樣預測」
- 技術背景：中；了解特徵重要性概念，但對 SHAP vs. LIME 差異不熟悉

### 場景矩陣

| 場景 | Persona | 任務描述 | 使用作品 | 使用元件 |
|------|---------|---------|---------|---------|
| **場景 1**<br>門診快速風險判斷 | Persona A | 陳醫師在病患坐下後 30 秒內判斷「這位患者風險高嗎？主要因為什麼？」 | 作品 1 | Waterfall Badge（C1）、Top 3 Risk Factor Tags（C6）、Confidence Interval Band（C5） |
| **場景 2**<br>治療介入模擬 | Persona A | 陳醫師想知道「如果這位病患開始用藥控制血壓，風險會降多少？」 | 作品 2 | What-If Slider（C3）、Decision Boundary Marker（C3）、Uncertainty Warning Flag（C5） |
| **場景 3**<br>個案討論報告準備 | Persona B | 林醫師準備向主治報告：「這位病患模型預測高風險，我用了三種方法確認，原因主要是 eGFR 偏低和年齡」 | 作品 3 + 作品 4 | Side-by-Side Method Panel（C1/C2）、Importance Consistency Badge（C6）、Risk Factor Headline（C6）、Contribution Paragraph（C1） |
| **場景 4**<br>解釋一致性驗核 | Persona B | 林醫師質疑模型解釋是否可信：「SHAP 說年齡最重要，但 LIME 說 BMI 才是，這個差異意味著什麼？」 | 作品 3 | Side-by-Side Method Panel（C2）、Importance Consistency Badge（C6）、Multi-Method Feature Bar（C1） |
| **場景 5**<br>歷史案例參照 | Persona A | 陳醫師想知道「以前有沒有類似的病患，他們後來怎麼樣了？」 | 作品 1 + 作品 4 | Similar Case Chip（C4）、Analogous Case Reference（C4）、Comparison Summary Sentence（C2） |
| **場景 6**<br>科室品質審查 | Persona B（或科主任） | 品質管理會議中：「我們模型對哪類病患的預測最不準？有哪些資料蒐集缺口？」 | 作品 5 | Global Feature Importance Bar Chart（C6）、Uncertainty Heatmap Strip（C5）、Cohort Comparison Toggle（C2） |

---

## 六、缺口分析

### 6.1 哪一項 C 沒有任何作品對應？

**所有 C1–C6 均有至少一個作品對應**，無完全空缺。但以下 C 的對應品質存在問題：

| C | 覆蓋狀況 | 問題 |
|---|---------|------|
| C3（反事實） | 作品 2（互動）、作品 4（文字） | 作品 4 的 C3 是靜態文字，無法讓醫師自行探索假設情境；C3 實質上只有作品 2 是真正互動式的 |
| C4（最近鄰居） | 作品 1、作品 4 | 兩個對應都是輔助性呈現，沒有作品以最近鄰居作為主要設計概念 |
| C5（不確定性） | 作品 1、2、4、5 | 覆蓋廣但深度淺——大多數僅顯示信賴帶數字，缺乏「為什麼這裡不確定」的解釋 |

### 6.2 多個作品同時對應的 C — 冗餘還是必要？

| C | 對應作品數 | 判斷 | 理由 |
|---|-----------|------|------|
| **C1**（局部解釋） | 4 個（作品 1、2、3、4） | **必要冗餘** | 同一個 C1 在不同作品中以不同形式呈現（圖形、數值、文字），服務不同 persona 和場景；並非重複設計同一介面 |
| **C6**（特徵重要性） | 4 個（作品 1、2、3、4、5） | **必要冗餘（局部）+ 部分可整合** | 作品 1 的 Top 3 Tags 與作品 5 的全局長條圖服務不同尺度（局部 vs. 全局），必要；但作品 2 的排名標籤和作品 3 的一致性徽章可考慮整合，避免資訊碎片化 |
| **C5**（不確定性） | 4 個（作品 1、2、4、5） | **部分冗餘** | 作品 4 的免責句和作品 1 的信賴帶傳達相同訊息，可考慮移除其中一個，或統一成一種視覺語言 |
| **C2**（比較） | 3 個（作品 3、4、5） | **必要冗餘** | 比較對象不同（方法比較 vs. 病患比較 vs. 族群比較），屬於 C2 的三個不同維度 |

---

## 七、設計反思（≥ 300 字）

填完這份對應表，最大的發現是：**這五個作品其實是同一件事的五個剖面，而非五個獨立的功能**。C1（局部解釋）出現在四個作品裡，乍看像是設計重複，但仔細分析後，每一個 C1 的「使用動詞」是截然不同的——作品 1 是「掃視」，作品 2 是「監控變化」，作品 3 是「核驗」，作品 4 是「閱讀」。這讓我意識到，**Gamut 的能力分類描述的是使用者的認知目標，而不是介面元件**；同一個目標可以用完全不同的視覺語言達成，而這些差異不是冗餘，而是因應不同認知負荷和時間壓力的必要分化。

在填表過程中，我原本預期「模型不確定性（C5）」會是最難落地的能力——畢竟醫師的直覺認知框架是「對或錯」，而非「信賴帶寬度」。但填完後發現反而是 C5 被最多作品覆蓋（4 個），問題不在「有沒有做」，而在「有沒有做對」。目前四個作品的 C5 呈現方式都是靜態數字或文字，缺乏讓醫師直覺理解「不確定是因為資料少，還是因為特徵衝突」的視覺設計。**作品 5 的 Uncertainty Heatmap Strip 是目前最接近正確方向的元件**，因為它直接把不確定性映射到特徵空間，而不是附掛在預測結果上。

另一個發現是：**作品 4（臨床報告）的定位比我原本想的更尷尬**。它對應了 C1、C2、C3、C4、C5、C6——幾乎全部——但每個對應都是其他作品的「文字副本」，而非獨立的設計能力。這讓我開始質疑：作品 4 是一個真正的作品，還是一個「把其他四個作品的輸出串接成段落」的輸出層？如果是後者，它的價值在於**降低 Persona A（主治醫師）的認知門檻**，而不是新增任何可解釋性能力本身。這個定位需要在後續論文寫作中說清楚，否則很容易被質疑：「你的報告和其他四個作品的關係是什麼？」

關於本專題不需要的 C：**C5（Regions of Error）在門診情境中的必要性值得重新評估**。對 Persona A 而言，看到「信賴帶 ±0.15」沒有任何臨床意義；他需要的是一個簡單的「可信 / 不可信」二元標籤，而不是連續數值。這意味著 C5 在呈現層需要再做一次抽象化（quantitative → categorical），才能真正服務臨床場景。目前各作品對 C5 的處理都停在「把數字顯示出來」的層次，這是下一輪設計迭代的主要缺口。

最後，整份表格填完後，最令人不安的空格不是 `N/A` 而是三個 `TBD`——尤其是作品 3 對 C5 的 TBD。**如果作品 3 最終無法呈現各解釋方法自身的不確定性，那「方法比較」的深度就會停在特徵歸因排名的表面，而無法回答「哪個方法更值得信任」這個對住院醫師最重要的問題**。這是作品 3 在下一個設計週期最需要解決的核心問題。

---

## 附錄：縮寫對照

| 縮寫 | 全稱 |
|------|------|
| C1–C6 | Gamut 定義的六項可解釋性能力（Capabilities 1–6） |
| SHAP | SHapley Additive exPlanations |
| LIME | Local Interpretable Model-agnostic Explanations |
| GAM | Generalized Additive Model |
| eGFR | estimated Glomerular Filtration Rate（估算腎絲球過濾率） |
| XAI | Explainable Artificial Intelligence |
| TBD | To Be Determined |

--

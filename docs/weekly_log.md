## Week 4:EBM 模型訓練與解釋

### 本週完成事項
- 在 Heart Disease 資料集上完成 EBM 訓練
- 計算 Tset AUC = 0.940
- 產出全域解釋與局部解釋圖表
- 修正 W04_heart_ebm_ipynb 檔案
- 

### 實驗結果
- 問題：explain_global() 和 explain_local() 各自在回答什麼問題？
1. explain_global()圖形用於全域解釋，描述整個模型對各個特徵的看法，
   以特徵選擇整體風險分析。
   執行佐證：在 W04_heart_ebm.ipynb 的 Cell 10，執行以下程式後：
   
   ebm_global = ebm.explain_global()
   show(ebm_global)

   互動圖表出現了一條 ca（顯影血管數量）的 shape function 曲線，往右（血管越多,至多3條）分數也上升，表示血管數量越多提高心臟病風險機率。
   ![ca](figures/Screenshot%202026-03-30%20at%2018.59.47.png)

   理論對照：查詢 InterpretML 論文（arXiv:1909.09223）Section 2，確認 EBM 的預測函數定義為 $F(x) = \sum_i f_i(x_i)$，每個 $f_i$ 正是這條特徵貢獻曲線，理解 explain_global() 呈現的是模型對「整個訓練集」學到的特徵趨勢，不是針對某一位病患。

   ![全域解釋](figures/Screenshot%202026-03-30%20at%2016.47.32.png)

2. explain_local()圖形用於局部解釋,描述某一位特定病患的預測是如何組成.
   實例取前五位病患預測分解,利用單一觀測點預測整體決策走向.

   執行佐證：在 W04_heart_ebm.ipynb 的 Cell 11，執行以下程式後：

   ebm_local = ebm.explain_local(X_test[:5], y_test[:5])
   show(ebm_local)

   互動圖表下拉選擇0和1欄位查看，

   Actual Class（真實類別）| Predict Class（預測類別）|
   Precision（精確率）：Precision=TP/(FP+TP)
   Recall（真實率）:Recall=TP+(FN+TP)​
   TP (真正例)	模型預測為正例，實際也是正例。
   FP (假正例)	模型預測為正例，實際是負例。
   FN (假負例)	模型預測為負例，實際是正例。
   TN (真負例)  模型預測為負例，實際也是負例。
   ​
   取第一位和第二位預測分解：
   - 0: ACTUAL(0)| PREDICTED(0)| PRSCORE(0.947)
     以slope為主導因子，即預測模型中重要依據。雖然slope,thalach,trestbps為正向為正向影響，但圖形整體向左延伸，大幅帶動負向修正。本案例沒有心臟病,模型預測結果也為沒有心臟病,模型預測準確率極高.
     ![0:局部解釋](figures/Screenshot%202026-03-30%20at%2016.49.12.png)

   - 1: ACTUAL(1)| PREDICTED(0)| PRSCORE(0.591)
     以ca為主導因子，cp,sex因子為輔，利用正向及負向因子產生決策拉鋸，預測患病結果。
     本案件患者具有心臟病，模型預測結果為沒有心臟病，模型指標性不夠準確，誤差理由待檢卻。
     ![1:局部解釋](figures/Screenshot%202026-03-30%20at%2016.49.29.png)

   理論對照：查詢 InterpretML 論文（arXiv:1909.09223）Section 2，確認 EBM 的預測函數定義為 $F(x) = \sum_i f_i(x_i)$，利用EBM加法模型，圖中每個橫條數值是從對應的特徵函數查表得知得分，了解具體數值在模型邏輯中換算成多少分數。
   EBM模型核心邏輯：最終預測值＝橫條代表分數直接相加，再經鏈結函數g(廣義加性模型(GAM)的線性結果)得到結果。

- AUC 計算：
  執行佐證：在 W04_heart_ebm.ipynb 的 Cell 9，執行以下程式後：

  y_prob = ebm.predict_proba(X_test)[:, 1]
  auc = roc_auc_score(y_test, y_prob)
  print(f"EBM Test AUC:{auc:.3f}")


### 遇到的問題與解決方式

| 問題 ｜ 原因 ｜ 解決方式 ｜
| ---- ｜ ----｜ ------- ｜
| 測試分割切錯資料範圍 | 未正確打上cell | 依據原文重新取正確範圍 |



### 心得
利用EBM 的shape function 能較清楚了解資料集的比較結果,易於黑箱模型理解.
尤其ˋcaˋ的階梯形圖形狀,從0-1間風險明顯上升,和臨床上的認知是相同的.
無論透過論文中的公式或概念佐證實驗論證其實並非想像中容易，需熟悉整體內容後再進行才能融會貫通。
   
   

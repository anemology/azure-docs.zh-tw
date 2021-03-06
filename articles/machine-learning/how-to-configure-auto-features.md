---
title: AutoML 實驗中的特徵化
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 所提供的特徵化設定，以及自動化 ML 實驗中的功能工程支援方式。
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/28/2020
ms.openlocfilehash: a5eb24b5420431a43afa2ffd006ac821f0e907c9
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185752"
---
# <a name="featurization-in-automated-machine-learning"></a>自動化機器學習服務中的特徵化

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本指南中，您將瞭解：

- Azure Machine Learning 提供哪些特徵化設定。
- 如何為您的[自動化機器學習實驗](concept-automated-ml.md)自訂這些功能。

*特色工程*是使用資料的領域知識來建立功能的程式，以協助機器學習服務 (ML) 演算法更進一步瞭解。 在 Azure Machine Learning 中，會套用資料調整和正規化技術，讓特性工程更容易。 這些技術和這項功能工程統稱是*特徵化*在自動化機器學習或*AutoML*實驗中。

## <a name="prerequisites"></a>必要條件

本文假設您已經知道如何設定 AutoML 實驗。 如需設定的相關資訊，請參閱下列文章：

- 如需程式碼優先體驗：[使用適用于 Python 的 AZURE MACHINE LEARNING SDK 來設定自動化 ML 實驗](how-to-configure-auto-train.md)。
- 適用于低程式碼或無程式碼的體驗：[使用 Azure Machine Learning Studio 建立、檢查和部署自動化的機器學習模型](how-to-use-automated-ml-for-ml-models.md)。

## <a name="configure-featurization"></a>設定特徵化

在每個自動化機器學習實驗中，預設會將[自動調整和正規化技術](#featurization)套用至您的資料。 這些技術是特徵化的類型，可協助*特定*的演算法對不同尺規上的功能有敏感性。 不過，您也可以啟用其他特徵化，例如*遺漏值的插補*、*編碼*和*轉換*。

> [!NOTE]
> 自動化機器學習特徵化的步驟 (例如功能正規化、處理遺漏的資料，或將文字轉換成數值) 會成為基礎模型的一部分。 當您使用模型進行預測時，會自動將定型期間所套用的相同特徵化步驟套用至您的輸入資料。

針對您使用 Python SDK 設定的實驗，您可以啟用或停用特徵化設定，並進一步指定要用於實驗的特徵化步驟。 如果您使用的是 Azure Machine Learning studio，請參閱[啟用特徵化的步驟](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。

下表顯示 `featurization` [AutoMLConfig 類別](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中已接受的設定：

|特徵化設定 | Description|
------------- | ------------- |
|`"featurization": 'auto'`| 指定在前置處理過程中，[資料護欄和特徵化步驟](#featurization)會自動完成。 這項設定是預設值。|
|`"featurization": 'off'`| 指定不會自動執行特徵化步驟。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指定要使用的自訂特徵化步驟。 [了解如何自訂特徵化](#customize-featurization)。|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>自動特製化

下表摘要說明自動套用至資料的技術。 這些技術適用于使用 SDK 或 studio 設定的實驗。 若要停用此行為，請 `"featurization": 'off'` 在您的物件中設定 `AutoMLConfig` 。

> [!NOTE]
> 如果您打算將 AutoML 建立的模型匯出至[ONNX 模型](concept-onnx.md)，ONNX 格式僅支援以星號表示的特徵化選項 ( "*" ) 。 深入了解[將模型轉換為 ONNX](concept-automated-ml.md#use-with-onnx)。

|特徵化 &nbsp; 步驟| Description |
| ------------- | ------------- |
|**捨棄高基數或無變異數功能*** |從定型和驗證集卸載這些功能。 適用于所有遺漏值的功能，在所有資料列中具有相同的值，或具有高基數 (例如，) 的雜湊、識別碼或 Guid。|
|**插補遺漏值*** |對於數值特徵，請插補資料行中值的平均值。<br/><br/>針對類別特徵，請插補最常出現的值。|
|**產生其他功能*** |針對 DateTime 特徵：年、月、日、星期幾、幾月幾日、季、第幾週、小時、分鐘、秒。<br><br> *針對預測工作，* 會建立這些額外的日期時間功能： ISO Year、半年中的行事曆月份、字串、周、一周中的日、年中的日、上午/下午 (0，如果小時在下午 (12) ，則為1，) 否則為 (12 小時的一天) <br/><br/>針對文字功能：根據 unigrams、bigrams 和 trigrams 的詞彙頻率。 深入瞭解[如何使用經理 bert 完成此作業。](#bert-integration)|
|**轉換和編碼***|將具有幾個唯一值的數值特徵轉換成類別功能。<br/><br/>一種經常性編碼用於低基數分類功能。 一種經常性雜湊編碼用於高基數類別功能。|
|**字組內嵌**|文字 featurizer 會使用預先定型的模型，將文字標記的向量轉換成句子向量。 檔中每個單字的內嵌向量都會與其余部分匯總，以產生檔功能向量。|
|**目標編碼**|針對分類功能，此步驟會將每個類別對應至回歸問題的平均目標值，並針對分類問題的每個類別，將其設為類別的機率。 會套用以頻率為基礎的加權和 k 折迭交叉驗證，以減少稀疏資料類別所造成之對應和雜訊的過度學習。|
|**文字目標編碼**|針對文字輸入，會使用具備文字袋 (bag-of-words) 的堆疊線性模型來產生每個類別其機率。|
|**證據權數 (WoE)**|將 WoE 作為類別資料行相互關聯與目標資料行相互關聯的量值計算。 WoE 是以類別與類別外機率的比率記錄來計算。 此步驟會針對每個類別產生一個數值特徵資料行，而不需要明確插補遺漏值和極端值處理。|
|**叢集距離**|在所有數值資料行上訓練 k 表示叢集模型。 產生*k*個新功能 (每個叢集一個新的數值功能) ，其中包含每個樣本與每個叢集距心的距離。|

## <a name="data-guardrails"></a>資料護欄

*資料護欄*可協助您找出資料 (的潛在問題，例如遺漏值或[類別不平衡](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)) 。 它們也可協助您採取更正動作來改善結果。

套用資料護欄：

- **針對 SDK 實驗**：當 `"featurization": 'auto'` `validation=auto` 物件中指定了參數或時 `AutoMLConfig` 。
- **針對 studio 實驗**：啟用自動特徵化時。

您可以查看實驗的資料護欄：

- `show_output=True`當您使用 SDK 提交實驗時設定。

- 在 studio 中，在您的自動化 ML 執行的 [**資料護欄**] 索引標籤上。

### <a name="data-guardrail-states"></a>資料 guardrail 狀態

[資料護欄] 會顯示三種狀態的其中一種：

|State| 描述 |
|----|---- |
|**通過**| 未偵測到任何資料問題，您不需要採取任何動作。 |
|**完成**| 已成功將變更套用到資料。 我們鼓勵您審查 AutoML 採取的修正動作，以確保變更與預期的結果一致。 |
|**警示**| 偵測到資料問題，但無法補救。 我們鼓勵您修訂並修正問題。|

### <a name="supported-data-guardrails"></a>支援的資料護欄

下表描述目前支援的資料護欄，以及您在提交實驗時可能會看到的相關狀態：

護欄|狀態|觸發條件&nbsp;&nbsp;
---|---|---
**遺漏特徵值插補** |已通過 <br><br><br> 完成| 在訓練資料中沒有偵測到任何遺漏特徵值。 深入瞭解[缺少-值的插補。](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> 在定型資料中偵測到遺漏的功能值，且已插補。
**高基數特徵處理** |已通過 <br><br><br> 完成| 已分析您的輸入，但未偵測到高基數的功能。 <br><br> 在您的輸入中偵測到高基數的功能，並已處理。
**驗證分割處理** |完成| 驗證設定為 `'auto'` ，而且定型資料包含*少於20000個數據列*。 <br> 定型模型的每個反復專案都是使用交叉驗證來驗證。 深入瞭解[驗證資料](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data)。 <br><br> 驗證設定設為 `'auto'` ，定型資料包含超過*20000 個數據列*。 <br> 輸入資料已分割成訓練資料集和驗證資料集，以用於模型驗證。
**類別平衡偵測** |已通過 <br><br>警示 <br><br>完成 | 已分析輸入，且訓練資料中所有類別都是平衡的。 如果資料集內的每個類別都有良好的標記法，則會將資料集視為平衡，如樣本的數目和比率所測量。 <br><br><br> 在輸入中偵測到不平衡的類別。 若要修正模型偏差，請修正平衡問題。 深入瞭解[不平衡資料](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)。<br><br><br> 在您的輸入中偵測到不平衡類別，而清除的邏輯已決定要套用平衡。
**記憶體問題偵測** |已通過 <br><br><br><br> 完成 |<br> 所選取的值 (水準、延隔、滾動視窗) 已分析，而且未偵測到潛在的記憶體不足問題。 深入瞭解時間序列[預測](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)設定。 <br><br><br>選取的值 ([水準]、[延後]、[滾動視窗) 已分析，而且可能會導致您的實驗用盡記憶體。 Lag 或滾動視窗設定已關閉。
**頻率偵測** |已通過 <br><br><br><br> 完成 |<br> 分析時間序列，而且所有資料點都會與偵測到的頻率對齊。 <br> <br> 已分析時間序列，並偵測到與偵測到的頻率不一致的資料點。 這些資料點已從資料集移除。 深入瞭解[時間序列預測的資料準備](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)。

## <a name="customize-featurization"></a>自訂特徵化

您可以自訂特徵化設定，以確保用來將 ML 模型定型的資料和功能會產生相關的預測。

若要自訂 featurizations，請  `"featurization": FeaturizationConfig` 在您的物件中指定 `AutoMLConfig` 。 如果您要使用 Azure Machine Learning studio 進行實驗，請參閱操作[說明文章](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。 若要自訂 forecastings 工作類型的特徵化，請參閱[預測](how-to-auto-train-forecast.md#customize-featurization)作法。

支援的自訂項目包含：

|自訂|定義|
|--|--|
|**資料行用途更新**|覆寫指定之資料行的 autodetected 功能類型。|
|**轉換器參數更新** |更新指定之轉換器的參數。 目前支援*Imputer* (平均值、最常值和中位數) 和*HashOneHotEncoder*。|
|**卸除資料行** |指定要從特徵化中捨棄的資料行。|
|**區塊轉換器**| 指定要在特徵化進程中使用的區塊轉換器。|

`FeaturizationConfig`使用 API 呼叫來建立物件：

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="featurization-transparency"></a>特徵化透明度

每個 AutoML 模型都已自動套用特徵化。  特徵化包括) 和調整和正規化時 (自動化的功能工程 `"featurization": 'auto'` ，這會影響選取的演算法及其超參數值。 AutoML 支援不同的方法，以確保您能夠查看已套用至模型的內容。

請考慮這個預測範例：

+ 有四種輸入功能： (數值) 、B (數值) 、C (數值) 、D (DateTime) 。
+ 數值特徵 C 已卸載，因為它是具有所有唯一值的識別碼資料行。
+ 數值特徵 A 和 B 具有遺漏值，因此會插補平均值。
+ DateTime 功能 D 特徵化為11個不同的工程功能。

若要取得此資訊，請使用 `fitted_model` 自動化 ML 實驗執行的輸出。

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>自動化特徵工程 
會傳回 `get_engineered_feature_names()` 工程化功能名稱的清單。

  >[!Note]
  >對於「預測」工作，請使用 'timeseriestransformer'，對於「迴歸」或「分類」工作則使用 'datatransformer'。

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

此清單包含所有工程特徵名稱。 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

會 `get_featurization_summary()` 取得所有輸入功能的特徵化摘要。

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

輸出

  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   |輸出|定義|
   |----|--------|
   |RawFeatureName|提供的資料集中的輸入特徵/資料行名稱。|
   |TypeDetected|偵測到輸入特徵的資料類型。|
   |Dropped|指出是已卸除還是使用了輸入特徵。|
   |EngineeringFeatureCount|透過自動化特徵工程轉換而產生的特徵數目。|
   |轉換|套用至輸入特徵以產生工程特徵的轉換清單。|

### <a name="scaling-and-normalization"></a>調整和正規化

若要瞭解縮放/正規化和選取的演算法及其超參數值，請使用 `fitted_model.steps` 。 

以下是 `fitted_model.steps` 針對所選執行執行的範例輸出：

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
```

若要取得更多詳細資料，請使用此協助程式函式： 

```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(model)
```

此 helper 函式會使用 `LogisticRegression with RobustScalar` 做為特定的演算法，傳回特定執行的下列輸出。

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>預測類別機率

使用自動化 ML 產生的模型都有包裝函式物件，會從其開放原始碼原始類別中鏡像處理功能。 自動化 ML 傳回的分類模型包裝函式物件大多會執行 `predict_proba()` 函式，而接受特徵 (X 值) 的類陣列或疏鬆矩陣資料範例，並傳回每個範例及其個別類別機率的 N 維陣列。

假設您已使用前述的相同呼叫來擷取最佳執行和適合的模型，您可以直接從適當的模型呼叫 `predict_proba()`，並根據模型類型，以適當的格式提供 `X_test` 範例。

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

如果基礎模型不支援 `predict_proba()` 函式，或格式不正確，則會擲回模型類別特定的例外狀況。 如需如何對不同的模型類型實作此函式的範例，請參閱 [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) 和 [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) 參考文件。

## <a name="bert-integration"></a>經理 BERT 整合

[經理 bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657)用於 AutoML 的特徵化層。 在此圖層中，如果資料行包含任意文字或其他類型的資料，例如時間戳記或簡單數位，則會據以套用特徵化。

針對經理 BERT，此模型會進行微調，並利用使用者提供的標籤進行定型。 從這裡開始，檔內嵌會與其他功能一起輸出，例如以時間戳記為基礎的功能、一周中的日。 


### <a name="bert-steps"></a>經理 BERT 步驟

若要叫用經理 BERT，您必須 `enable_dnn: True` 在 automl_settings 中設定，並使用 gpu 計算 (例如 `vm_size = "STANDARD_NC6"` ，或較高的 gpu) 。 如果使用 CPU 計算，則 AutoML 會啟用 BiLSTM DNN featurizer，而不是經理 BERT。

AutoML 會針對經理 BERT 採取下列步驟。 

1. **所有文字資料行的**前置處理和 token 化。 例如，您可以在最終模型的特徵化摘要中找到 "StringCast" 轉換器。 您可以在[此筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)中找到如何產生模型的特徵化摘要的範例。

2. 將**所有文字資料行串連成單一文字資料行**，因此 `StringConcatTransformer` 在最終模型中為。 

    我們的經理 BERT 的執行會將定型樣本的總文字長度限制為128個權杖。 也就是說，串連時，所有的文字資料行最理想的長度應該是128個權杖。 如果有多個資料行，則應該將每個資料行剪除，以便滿足此條件。 否則，針對長度 >128 token 的串連資料行，經理 BERT 的 tokenizer 層會將此輸入截斷為 128 token。

3. **在功能清除的過程中，AutoML 會比較經理 BERT 與資料範例上) 之單字 (包的基準。** 這項比較會判斷經理 BERT 是否能提供精確度的改善。 如果經理 BERT 的執行效果優於基準，AutoML 會針對整個資料使用經理 BERT 的文字特徵化。 在這種情況下，您會 `PretrainedTextDNNTransformer` 在最終模型中看到。

經理 BERT 通常會比其他有長的執行時間。 為了達到較佳的效能，我們建議針對其 RDMA 功能使用 "STANDARD_NC24r" 或 "STANDARD_NC24rs_V3"。 

AutoML 會將經理 BERT 訓練散發到多個節點（如果有的話） (最多八個節點) 。 將 `AutoMLConfig` 參數設定為大於1，即可在您的物件中完成這項作業 `max_concurrent_iterations` 。 
### <a name="supported-languages"></a>支援的語言

AutoML 目前支援大約100語言，而視資料集的語言而定，AutoML 會選擇適當的經理 BERT 模型。 對於德文的資料，我們使用德文經理 BERT 模型。 若是英文，我們使用英文經理 BERT 模型。 針對所有其他語言，我們會使用多語系經理 BERT 模型。

在下列程式碼中，會觸發德文經理 BERT 模型，因為已根據 ISO 分類，將資料集語言指定為 `deu` ，德文的三個[ISO classification](https://iso639-3.sil.org/code/deu)字母語言代碼：

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>後續步驟

* 瞭解如何設定自動化 ML 實驗：

    * 如需程式碼優先體驗：[使用 AZURE MACHINE LEARNING SDK 設定自動化 ML 實驗](how-to-configure-auto-train.md)。
    * 針對低程式碼或無程式碼體驗：[在 Azure Machine Learning studio 中建立您的自動化 ML 實驗](how-to-use-automated-ml-for-ml-models.md)。

* 深入了解[模型部署的方式和位置](how-to-deploy-and-where.md)。

* 深入瞭解[如何使用自動化機器學習來定型回歸模型](tutorial-auto-train-models.md)，或[如何在遠端資源上使用自動化機器學習來定型](how-to-auto-train-remote.md)。

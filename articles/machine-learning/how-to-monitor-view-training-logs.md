---
title: 監視及查看 ML 執行記錄 & 計量
titleSuffix: Azure Machine Learning
description: 監視您的 Azure ML 實驗並觀看執行計量，以加強模型建立程式。 使用 widget 和 studio 入口網站來探索執行狀態及查看執行記錄。
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 146b9a04b190808848af56612e14a05a617c7109
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87554764"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>監視及查看 ML 執行記錄和計量

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何監視 Azure Machine Learning 的執行，並查看其記錄。 在您可以查看記錄之前，您必須先加以啟用。 如需詳細資訊，請參閱[在 AZURE ML 訓練執行中啟用記錄](how-to-track-experiments.md)。

記錄可以協助您診斷錯誤和警告，或追蹤參數和模型精確度之類的效能計量。 在本文中，您將瞭解如何使用下列方法來查看記錄：

> [!div class="checklist"]
> * 在 studio 中執行監視
> * 使用 Jupyter Notebook widget 的監視執行
> * 監視自動化機器學習執行
> * 完成時查看輸出記錄
> * 在 studio 中查看輸出記錄

如需如何管理您的實驗的一般資訊，請參閱[啟動、監視和取消定型執行](how-to-manage-runs.md)。

## <a name="monitor-runs-in-the-studio"></a>在 studio 中執行監視

若要從瀏覽器監視特定計算目標的執行，請使用下列步驟：

1. 在[Azure Machine Learning studio](https://ml.azure.com/)中，選取您的工作區，然後從頁面左側選取 [__計算__]。

1. 選取 [定型叢集]，以顯示用於定型的計算目標清單。 然後，選取叢集。

    ![選取定型叢集](./media/how-to-track-experiments/select-training-compute.png)

1. 選取 [執行]。 使用此叢集的執行清單隨即顯示。 若要檢視特定執行的詳細資料，請使用 [執行] 資料行中的連結。 若要檢視實驗的詳細資料，請使用 [實驗] 資料行中的連結。

    ![選取定型叢集的執行](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > 由於定型計算目標是共用的資源，因此可以在指定的時間將多個執行排入佇列或作用中。
    > 
    > 執行可以包含子執行，因此一個定型作業可能會產生多個項目。

執行完成後，就不會再顯示於此頁面上。 若要檢視已完成執行的資訊，請瀏覽 Studio 的 [實驗] 區段，然後選取實驗並加以執行。 如需詳細資訊，請參閱「[已完成的執行的視圖計量](#view-the-experiment-in-the-web-portal)」一節。

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>使用 Jupyter 筆記本 widget 執行監視

當您使用**ScriptRunConfig**方法來提交回合時，您可以使用[Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)來監看執行的進度。 就像執行提交一樣，小工具為非同步工作，並每隔 10 至 15 秒提供即時更新，直到工作完成為止。

等待執行完成時，檢視 Jupyter 小工具。
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Jupyter Notebook 小工具的螢幕擷取畫面](./media/how-to-track-experiments/run-details-widget.png)

您也可以在工作區中取得相同顯示的連結。

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>監視自動化機器學習執行

針對自動化機器學習執行，若要存取先前執行的圖表，請 `<<experiment_name>>` 將取代為適當的實驗名稱：

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![適用於自動化機器學習的 Jupyter Notebook 小工具](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>完成時顯示輸出

當您使用 **ScriptRunConfig** 時，可以使用 ```run.wait_for_completion(show_output = True)``` 以顯示模型定型完成的時間。 ```show_output``` 旗標為您提供詳細資訊輸出。 如需詳細資訊，請參閱[如何啟用記錄](how-to-track-experiments.md#scriptrunconfig-logs)的 ScriptRunConfig 一節。

<a id="queryrunmetrics"></a>
## <a name="query-run-metrics"></a>查詢執行計量

您可以使用 ```run.get_metrics()``` 檢視定型模型的計量。 例如，您可以使用這項與上述範例來判斷最佳模型，方法是尋找具有最低 mean 平方錯誤 (mse) 值的模型。

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-run-records-in-the-studio"></a>在 studio 中查看執行記錄

您可以在[Azure Machine Learning studio](https://ml.azure.com)中流覽已完成的執行記錄，包括記錄的計量。

流覽至 [**實驗**] 索引標籤，然後選取您的實驗。 在 [實驗執行] 儀表板上，您可以看到每個執行的追蹤計量和記錄。 

向下切入至特定執行以查看其輸出或記錄檔，或下載實驗的快照集，讓您可以與其他人共用實驗資料夾。

您也可以編輯 [執行清單] 資料表以選取多個回合，並顯示執行的最後、最小或最大記錄值。 自訂您的圖表，以比較記錄的計量值和跨多個回合的匯總。

:::image type="content" source="media/how-to-track-experiments/experimentation-tab.gif" alt-text="Azure Machine Learning Studio 中的執行詳細資料":::


### <a name="format-charts-in-the-studio"></a>在 studio 中格式化圖表

在記錄應用程式開發介面中使用下列方法，以影響 studio 將您的計量視覺化。

|記錄的值|程式碼範例| 在入口網站中設定格式|
|----|----|----|
|記錄數值的陣列| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|單一變數的折線圖|
|透過重複使用的相同計量名稱，記錄單一數值 (例如，從 for 迴圈中)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| 單一變數的折線圖|
|使用 2 個數字資料行重複記錄資料列|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|兩個變數的折線圖|
|使用 2 個數字資料行記錄資料表|`run.log_table(name='Sine Wave', value=sines)`|兩個變數的折線圖|


## <a name="next-steps"></a>後續步驟

請嘗試下列後續步驟，以瞭解如何使用 Azure Machine Learning：

* 瞭解如何[在 Azure Machine Learning 設計工具 (預覽) 中追蹤實驗並啟用記錄](how-to-track-designer-experiments.md)。

* 關於如何註冊最佳模型，並在教學課程中加以部署的範例，請參閱[使用 Azure Machine Learning 定型映像分類模型](tutorial-train-models-with-aml.md)。


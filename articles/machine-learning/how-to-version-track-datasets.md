---
title: 資料集版本設定
titleSuffix: Azure Machine Learning
description: 瞭解如何將您的資料集和版本設定與機器學習管線搭配運作。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 4ae0ebaec011634153e4ac9bbe43ef519c58a251
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87845879"
---
# <a name="version-and-track-datasets-in-experiments"></a>版本和追蹤實驗中的資料集
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何對重現性的 Azure Machine Learning 資料集進行版本和追蹤。 資料集版本設定是指將資料的狀態加入書簽，讓您可以套用特定版本的資料集，以供未來的實驗使用。

典型的版本控制案例：

* 當有新資料可供重新定型時
* 當您要套用不同的資料準備或功能工程方法時

## <a name="prerequisites"></a>必要條件

在本教學課程中，您需要：

- [已安裝適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。 此 SDK 包含[azureml 資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)封裝。
    
- [Azure Machine Learning 工作區](concept-workspace.md)。 執行下列程式碼以取出現有的，或[建立新的工作區](how-to-manage-workspace.md)。

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure Machine Learning 資料集](how-to-create-register-datasets.md)。

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>註冊並取出資料集版本

藉由註冊資料集，您可以在實驗和同事之間進行版本、重複使用及共用。 您可以使用相同的名稱來註冊多個資料集，並依名稱和版本號碼來抓取特定版本。

### <a name="register-a-dataset-version"></a>註冊資料集版本

下列程式碼會 `titanic_ds` 將參數設定為，以註冊新版本的資料集 `create_new_version` `True` 。 如果沒有任何現有的 `titanic_ds` 資料集向工作區註冊，則程式碼會建立具有該名稱的新資料集， `titanic_ds` 並將其版本設定為1。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>依名稱取得資料集

根據預設，類別上的[get_by_name ( # B1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--)方法會傳回 `Dataset` 已向工作區註冊之資料集的最新版本。 

下列程式碼會取得資料集的第1版 `titanic_ds` 。

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>版本控制最佳做法

當您建立資料集版本時，您*不會*使用工作區建立額外的資料複本。 因為資料集是儲存體服務中資料的參考，所以您的單一事實來源是由您的儲存體服務所管理。

>[!IMPORTANT]
> 如果覆寫或刪除您的資料集所參考的資料，呼叫特定版本的資料集並*不*會還原變更。

當您從資料集載入資料時，資料集所參考的目前資料內容一律會載入。 如果您想要確保每個資料集版本都是可重現的，建議您不要修改資料集版本所參考的資料內容。 當新資料進入時，將新的資料檔案儲存到個別的資料檔案夾中，然後建立新的資料集版本以包含該新資料夾中的資料。

下列影像和範例程式碼顯示建議的方式來結構您的資料檔案夾，並建立參考這些資料夾的資料集版本：

![資料夾結構](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>版本管線輸出資料集

您可以使用資料集做為每個 Machine Learning 管線步驟的輸入和輸出。 當您重新執行管線時，每個管線步驟的輸出都會註冊為新的資料集版本。

由於 Machine Learning 管線會在每次管線重新執行時，將每個步驟的輸出填入新的資料夾，因此可重現已建立版本的輸出資料集。 深入瞭解[管線中的資料集](how-to-create-your-first-pipeline.md#steps)。

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>在實驗中追蹤資料集

針對每個 Machine Learning 實驗，您可以透過實驗物件，輕鬆地追蹤當做輸入使用的資料集 `Run` 。

下列程式碼會使用 [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) 方法來追蹤哪些輸入資料集與實驗執行搭配使用：

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

您也可以使用， `input_datasets` 從實驗中尋找 https://ml.azure.com/ 。 

下圖顯示在 Azure Machine Learning studio 上尋找實驗之輸入資料集的位置。 在此範例中，請移至您的**實驗**窗格，然後開啟特定實驗執行的 [**屬性**] 索引標籤 `keras-mnist` 。

![輸入資料集](./media/how-to-version-track-datasets/input-datasets.png)

使用下列程式碼，向資料集註冊模型：

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

註冊之後，您可以使用 Python 查看已向資料集註冊的模型清單，或移至 https://ml.azure.com/ 。

下列視圖來自 [**資產**] 底下的 [**資料集**] 窗格。 選取資料集，然後選取 [**模型**] 索引標籤，以取得已向資料集註冊的模型清單。 

![輸入資料集模型](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>後續步驟

* [使用資料集定型](how-to-train-with-datasets.md)
* [更多範例資料集筆記本](https://aka.ms/dataset-tutorial)

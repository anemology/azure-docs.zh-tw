---
title: 更新已部署的 webservice
author: gvashishtha
ms.service: machine-learning
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 66c53c7485041ec9abaf72396efcfa3325a13732
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87799901"
---
# <a name="update-a-deployed-web-service"></a>更新已部署的 web 服務

本文說明如何部署以 Azure Machine Learning 部署的 web 服務。

## <a name="prerequisites"></a>Prerequisites

本教學課程假設您已使用 Azure Machine Learning 部署 web 服務。 如果您需要瞭解如何部署 web 服務，請遵循下列[步驟](how-to-deploy-and-where.md)。

## <a name="update-web-service"></a>更新 Web 服務

若要更新 web 服務，請使用 `update` 方法。 您可以更新 web 服務，以使用新的模型、新的輸入腳本，或可在推斷設定中指定的新相依性。 如需詳細資訊，請參閱[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-)的檔。

請參閱[AKS 服務更新方法。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

請參閱[ACI 服務更新方法。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> 當您建立模型的新版本時，您必須手動更新您想要使用的每個服務。
>
> 您不能使用 SDK 來更新從 Azure Machine Learning 設計工具發佈的 web 服務。

**使用 SDK**

下列程式碼示範如何使用 SDK 來更新 web 服務的模型、環境和專案腳本：

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**使用 CLI**

您也可以使用 ML CLI 來更新 web 服務。 下列範例示範如何註冊新的模型，然後將 web 服務更新為使用新的模型：

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> 在此範例中，會使用 JSON 檔，將來自註冊命令的模型資訊傳遞至 update 命令。
>
> 若要將服務更新為使用新的專案腳本或環境，請建立[推斷設定檔](/azure/machine-learning/reference-azure-machine-learning-cli#inference-configuration-schema)，並使用參數來指定它 `ic` 。

如需詳細資訊，請參閱[az ml 服務更新](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)檔。

## <a name="next-steps"></a>後續步驟

* [針對失敗的部署進行疑難排解](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [建立用戶端應用程式以使用 web 服務](how-to-consume-web-service.md)
* [如何使用自訂 Docker 映射部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [建立模型部署的事件警示和觸發程式](how-to-use-event-grid.md)

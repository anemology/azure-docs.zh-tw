---
title: 執行 Azure Kubernetes Service-文字分析
titleSuffix: Azure Cognitive Services
description: 將文字分析容器映射部署至 Azure Kubernetes Service，並在網頁瀏覽器中進行測試。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b7a5953edd9aec96a7f75e747c39e8f07f7210bb
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88243763"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>將文字分析容器部署至 Azure Kubernetes Service

瞭解如何將 Azure 認知服務 [文字分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) 容器映射部署至 AZURE KUBERNETES SERVICE (AKS) 。 此程式會示範如何建立文字分析資源、如何建立相關聯的情感分析影像，以及如何從瀏覽器執行這兩個協調流程。 使用容器可以將您的注意力轉移到管理基礎結構之外，改為專注于應用程式開發。

## <a name="prerequisites"></a>先決條件

此程序需要必須安裝並在本機執行的多個工具。 請勿使用 Azure Cloud Shell。 您需要下列項目：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services)。
* 文字編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/download)。
* 已安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 。
* 已安裝 [KUBERNETES CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 。
* 具有正確定價層的 Azure 資源。 並非所有的定價層都會使用這個容器︰
    * 只有 F0 或標準定價層的**Azure 文字分析**資源。
    * 具有 S0 定價層的**Azure 認知服務**資源。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[關鍵片語擷取](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[語言偵測](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[情感分析](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>後續步驟

* 使用更多 [認知服務容器](../../cognitive-services-container-support.md)
* 使用 [文字分析聯機服務](../vs-text-connected-service.md)

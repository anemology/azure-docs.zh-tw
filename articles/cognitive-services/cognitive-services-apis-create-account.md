---
title: 在 Azure 入口網站中建立認知服務資源
titleSuffix: Azure Cognitive Services
description: 藉由建立及訂閱 Azure 入口網站中的資源，開始使用 Azure 認知服務。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: 00a4f7f6de207d5e8ad1bcd448cc587e3106d3a6
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87316964"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>使用 Azure 入口網站建立認知服務資源

使用本快速入門開始使用 Azure 認知服務。 在 Azure 入口網站中建立認知服務資源之後，您會取得用來驗證應用程式的端點和金鑰。


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>先決條件

* 有效的 Azure 訂用帳戶-[免費建立一個](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="create-a-new-azure-cognitive-services-resource"></a>建立新的 Azure 認知服務資源

1. 建立資源。

    #### <a name="multi-service-resource"></a>[多服務資源](#tab/multiservice)

    在入口網站中，多服務資源的名稱為**認知服務**。 [建立認知服務資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)。

    此時，多服務資源會啟用下列認知服務的存取權：

    - 電腦視覺
    - 內容仲裁
    - 臉部
    - 語言理解 (LUIS)
    - 文字分析
    - 轉譯程式
    - Bing 搜尋 v7 <br>（Web、影像、新聞、影片、視覺效果）
    - Bing 自訂搜尋
    - Bing 實體搜尋
    - Bing 自動建議
    - Bing 拼字檢查

    #### <a name="single-service-resource"></a>[單一服務資源](#tab/singleservice)

    使用下列連結來建立可用認知服務的資源：

    | 視覺                      | 語音                  | 語言                          | 決策             | 搜尋                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [電腦視覺](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [語音服務](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [沉浸式讀者](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [異常偵測器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing 搜尋 API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [自訂視覺服務](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [說話者辨識](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding （LUIS）](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [內容仲裁](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Bing 自訂搜尋](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [臉部](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [個人化工具](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing 實體搜尋](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [筆跡辨識器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [文字分析](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Bing 拼字檢查](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [翻譯工具](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing 自動建議](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. 在 [建立]**** 頁面上，提供下列資訊：

    #### <a name="multi-service-resource"></a>[多服務資源](#tab/multiservice)

    |    |    |
    |--|--|
    | **名稱** | 認知服務資源的描述性名稱。 例如， *MyCognitiveServicesResource*。 |
    | **訂用帳戶** | 選取您其中一個可用的 Azure 訂用帳戶。 |
    | **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
    | **定價層** | 認知服務帳戶的費用取決於您選擇的選項和使用方式。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **資源群組** | Azure 資源群組，將包含您的認知服務資源。 您可以建立新的群組，或將群組新增到既有的群組。 |

    ![資源建立畫面](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    按一下 [建立]  。

    #### <a name="single-service-resource"></a>[單一服務資源](#tab/singleservice)

    |    |    |
    |--|--|
    | **名稱** | 認知服務資源的描述性名稱。 例如， *TextAnalyticsResource*。 |
    | **訂用帳戶** | 選取您其中一個可用的 Azure 訂用帳戶。 |
    | **位置** | 您的認知服務執行個體的位置。 位置不同可能會造成延遲，但不會影響您資源執行階段的可用性。 |
    | **定價層** | 認知服務帳戶的費用取決於您選擇的選項和使用方式。 如需詳細資訊，請參閱 API [定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/)。
    | **資源群組** | Azure 資源群組，將包含您的認知服務資源。 您可以建立新的群組，或將群組新增到既有的群組。 |

    ![資源建立畫面](media/cognitive-services-apis-create-account/resource_create_screen.png)

    按一下 [建立]  。

    ***

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>取得資源的金鑰

1. 成功部署您的資源之後，請按一下 **[後續步驟]** 底下的 [**移至資源**]。

    ![搜尋認知服務](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. 從開啟的 [快速入門] 窗格中，您可以存取金鑰和端點。

    ![取得金鑰和端點](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除與群組中的任何其他資源。

1. 在 Azure 入口網站中，展開左側功能表以開啟服務的功能表，然後選擇 [資源群組] 以顯示資源群組的清單。
2. 找出要刪除的資源位於哪一個資源群組
3. 以滑鼠右鍵按一下資源群組清單。 選取 [刪除資源群組] 並且確認。

## <a name="see-also"></a>另請參閱

* [驗證 Azure 認知服務要求](authentication.md)
* [什麼是 Azure 認知服務？](Welcome.md)
* [自然語言支援](language-support.md)
* [Docker 容器支援](cognitive-services-container-support.md)

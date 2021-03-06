---
title: Web 服務輸入/輸出：模組參考
description: 瞭解 Azure Machine Learning 設計工具中的 web 服務模組（預覽）
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: ded976fc9090535f3c683b5c6351646a55265205
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84751110"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Web 服務輸入和 Web 服務輸出模組

本文說明 Azure Machine Learning 設計工具（預覽）中的 Web 服務輸入和 Web 服務輸出模組。

Web 服務輸入模組只能連接到類型為**DataFrameDirectory**的輸入埠。 Web 服務輸出模組只能從類型為**DataFrameDirectory**的輸出埠進行連接。 您可以在 [ **Web 服務**] 類別之下的模組樹狀目錄中找到這兩個模組。 

Web 服務輸入模組會指出使用者資料進入管線的位置。 [Web 服務輸出] 模組會指出在即時推斷管線中傳回使用者資料的位置。

## <a name="how-to-use-web-service-input-and-output"></a>如何使用 Web 服務輸入和輸出

當您從定型管線[建立即時推斷管線](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline)時，將會自動加入 Web 服務輸入和 Web 服務輸出模組，以顯示使用者資料進入管線的位置，以及資料的傳回位置。 

> [!NOTE]
> 自動產生即時推斷管線是一種以規則為基礎的最佳處理常式。 不保證正確性。 

您可以手動新增或移除「Web 服務輸入」和「Web 服務輸出」模組，以滿足您的需求。 請確定您的即時推斷管線至少有一個 Web 服務輸入模組和一個 Web 服務輸出模組。 如果您有多個 Web 服務輸入或 Web 服務輸出模組，請確定它們有唯一的名稱。 您可以在模組的右面板中輸入名稱。

您也可以藉由將 Web 服務輸入和 Web 服務輸出模組新增至未提交的管線，手動建立即時推斷管線。

> [!NOTE]
> 管線類型將會在您第一次提交時決定。 第一次提交之前，請務必新增 Web 服務輸入和 Web 服務輸出模組。

下列範例顯示如何從執行 Python 腳本模組手動建立即時推斷管線。 

![範例](media/module/web-service-input-output-example.png)
   
提交管線並順利完成執行之後，您就可以部署即時端點。
   
> [!NOTE]
>  在上述範例中，**手動輸入資料**會提供 web 服務輸入的資料架構，而且是部署即時端點的必要項。 一般來說，您應該一律將模組或資料集連接至**Web 服務輸入**所連接的埠，以提供資料結構描述。
   
## <a name="next-steps"></a>後續步驟
深入瞭解如何[部署即時端點](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint)。

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。
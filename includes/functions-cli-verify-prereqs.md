---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 19f3a99c087c0755a82ce7940326708fd1f6a3e5
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673172"
---
### <a name="prerequisite-check"></a>先決條件檢查

+ 在終端機或命令視窗中，執行 `func --version`，以確認 Azure Functions Core Tools 為 2.7.1846 版或更新版本。

+ 執行 `az --version` 以檢查 Azure CLI 版本為 2.0.76 或更新版本。

+ 執行 `az login` 以登入 Azure 並驗證有效訂用帳戶。

::: zone pivot="programming-language-python"  
+ 執行 `python --version` (Linux/MacOS) 或 `py --version` (Windows)，以確認您的 Python 版本回報為 3.8.x、3.7.x 或 3.6.x。
::: zone-end
---
title: 使用 Webjob 執行背景工作
description: 瞭解如何在 Azure App Service 中使用 Webjob 執行背景工作。 選擇各種不同的腳本格式，並使用 CRON 運算式來執行。
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 028551f04b2e44e9456e2f7343159ad9b52fd25f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82085139"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>在 Azure App Service 中使用 Webjob 執行背景工作

本文說明如何使用 [Azure 入口網站](https://portal.azure.com)來部署 WebJob，以便上傳可執行檔或指令碼。 如需如何使用 Visual Studio 開發和部署 WebJobs 的詳細資訊，請參閱[使用 Visual Studio 部署 WebJobs](webjobs-dotnet-deploy-vs.md)。

## <a name="overview"></a>概觀
Webjob 是[Azure App Service](index.yml)的一項功能，可讓您在與 web 應用程式、API 應用程式或行動應用程式相同的實例中執行程式或腳本。 使用 WebJob 不會產生額外的費用。

> [!IMPORTANT]
> Linux 上的 App Service 尚不支援 WebJob。

Azure WebJobs SDK 可以搭配 WebJobs 使用，以簡化許多程式設計工作。 如需詳細資訊，請參閱 [什麼是 WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)。

Azure Functions 提供另一種方式來執行程式和指令碼。 如需 WebJobs 和函式之間的比較，請參閱[在 Flow、Logic Apps、Functions 和 WebJobs 之間做選擇](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)。

## <a name="webjob-types"></a>WebJob 類型

下表描述連續** 和觸發** WebJobs 之間的差異。


|連續  |觸發  |
|---------|---------|
| 建立 WebJob 時立即啟動。 為了避免結束作業，程式或指令碼通常會在無限迴圈中執行其工作。 如果作業結束，您可以重新啟動。 | 只在手動或排程觸發時才啟動。 |
| 在 Web 應用程式執行所在的所有執行個體上執行。 您可以選擇性地將 WebJob 限制為單一執行個體。 |在 Azure 選取來進行負載平衡的單一執行個體上執行。|
| 支援遠端偵錯。 | 不支援遠端偵錯。|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>指令碼或程式支援的檔案類型

支援下列檔案類型：

* .cmd、.bat、.exe (使用 Windows 命令提示字元)
* .ps1 (使用 PowerShell)
* .sh (使用 Bash)
* .php (使用 PHP)
* .py (使用 Python)
* .js (使用 Node.js)
* .jar (使用 Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a>建立連續 WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至 App Service Web 應用程式、API 應用程式或行動裝置應用程式的 [App Service]**** 分頁。

2. 選取 [WebJob]****。

   ![選取 WebJob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. 在 [WebJob]**** 分頁上，選取 [新增]****。

    ![WebJob 分頁](./media/web-sites-create-web-jobs/wjblade.png)

3. 使用表格中所指定的 [新增 WebJob]**** 設定。

   ![新增 WebJob 分頁](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | 設定      | 範例值   | 描述  |
   | ------------ | ----------------- | ------------ |
   | **名稱** | myContinuousWebJob | App Service 應用程式中唯一的名稱。 開頭必須是字母或數字，而且不能含有 "-" 和 "_" 之外的特殊字元。 |
   | **檔案上傳** | ConsoleApp.zip | .zip** 檔案包含您的可執行檔或指令碼檔案，以及執行程式或指令碼所需的任何支援檔案。 支援的可執行檔或指令碼檔案類型會列在[支援的檔案類型](#acceptablefiles)一節。 |
   | **型別** | 連續 | [WebJob 類型](#webjob-types)在本文稍早已有提及。 |
   | **縮放比例** | 多重執行個體 | 僅適用於連續 WebJob。 決定程式或指令碼是在所有執行個體上執行，或是只在一個執行個體上執行。 在多個執行個體上執行的選項不適用於免費或共用[定價層](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 | 

4. 按一下 [確定]。

   新的 WebJob 會出現在 [WebJob]**** 分頁。

   ![WebJob 的清單](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. 若要停止或重新啟動連續執行的 WebJob，可在清單中使用滑鼠右鍵按一下該 WebJob，然後按一下 [停止]**** 或 [開始]****。

    ![停止連續 WebJob](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>建立手動觸發的 WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至 App Service Web 應用程式、API 應用程式或行動裝置應用程式的 [App Service]**** 分頁。

2. 選取 [WebJob]****。

   ![選取 WebJob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. 在 [WebJob]**** 分頁上，選取 [新增]****。

    ![WebJob 分頁](./media/web-sites-create-web-jobs/wjblade.png)

3. 使用表格中所指定的 [新增 WebJob]**** 設定。

   ![新增 WebJob 分頁](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | 設定      | 範例值   | 描述  |
   | ------------ | ----------------- | ------------ |
   | **名稱** | myTriggeredWebJob | App Service 應用程式中唯一的名稱。 開頭必須是字母或數字，而且不能含有 "-" 和 "_" 之外的特殊字元。|
   | **檔案上傳** | ConsoleApp.zip | .zip** 檔案包含您的可執行檔或指令碼檔案，以及執行程式或指令碼所需的任何支援檔案。 支援的可執行檔或指令碼檔案類型會列在[支援的檔案類型](#acceptablefiles)一節。 |
   | **型別** | 觸發 | [WebJob 類型](#webjob-types)在本文稍早已有提及。 |
   | **觸發程序** | 手動 | |

4. 按一下 [確定]。

   新的 WebJob 會出現在 [WebJob]**** 分頁。

   ![WebJob 的清單](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. 若要執行 WebJob，可在清單中使用滑鼠右鍵按一下它的名稱，然後按一下 [ **執行**]。
   
    ![執行 WebJob](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a>建立排程 WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至 App Service Web 應用程式、API 應用程式或行動裝置應用程式的 [App Service]**** 分頁。

2. 選取 [WebJob]****。

   ![選取 WebJob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. 在 [WebJob]**** 分頁上，選取 [新增]****。

   ![WebJob 分頁](./media/web-sites-create-web-jobs/wjblade.png)

3. 使用表格中所指定的 [新增 WebJob]**** 設定。

   ![新增 WebJob 分頁](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | 設定      | 範例值   | 描述  |
   | ------------ | ----------------- | ------------ |
   | **名稱** | myScheduledWebJob | App Service 應用程式中唯一的名稱。 開頭必須是字母或數字，而且不能含有 "-" 和 "_" 之外的特殊字元。 |
   | **檔案上傳** | ConsoleApp.zip | .zip** 檔案包含您的可執行檔或指令碼檔案，以及執行程式或指令碼所需的任何支援檔案。 支援的可執行檔或指令碼檔案類型會列在[支援的檔案類型](#acceptablefiles)一節。 |
   | **型別** | 觸發 | [WebJob 類型](#webjob-types)在本文稍早已有提及。 |
   | **觸發程序** | 已排程 | 若要讓排程可靠地運作，請啟用 [永遠開啟] 功能。 [永遠開啟] 僅適用於基本、標準和進階定價層。|
   | **CRON 運算式** | 0 0/20 * * * * | [CRON 運算式](#ncrontab-expressions)將於下一節中說明。 |

4. 按一下 [確定]。

   新的 WebJob 會出現在 [WebJob]**** 分頁。

   ![WebJob 的清單](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>NCRONTAB 運算式

您可以在入口網站中輸入[NCRONTAB 運算式](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)，或在 `settings.job` WebJob *.zip*檔案的根目錄中包含檔案，如下列範例所示：

```json
{
    "schedule": "0 */15 * * * *"
}
```

若要深入瞭解，請參閱[排程已觸發的 WebJob](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)。

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a>檢視作業記錄

1. 選取您想要查看記錄的 WebJob，然後選取 [記錄]**** 按鈕。
   
   ![記錄按鈕](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. 在 [WebJob 詳細資料]**** 分頁上，選取時間以查看一個執行的詳細資料。
   
   ![WebJob 詳細資料](./media/web-sites-create-web-jobs/webjobdetails.png)

3. 在 [WebJob 執行詳細資料]**** 分頁上，選取 [切換輸出]**** 以查看記錄內容的文字。
   
    ![網站工作執行詳細資料](./media/web-sites-create-web-jobs/webjobrundetails.png)

   若要在個別的瀏覽器視窗中查看輸出文字，請選取 [下載]****。 若要下載文字本身，請以滑鼠右鍵按一下 [下載]****，然後使用瀏覽器選項來儲存檔案內容。
   
5. 選取分頁頂端的 [WebJob]**** 階層連結，以移至 WebJob 的清單。

    ![WebJob 階層連結](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![歷程記錄儀表板中的 WebJobs 清單](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a> 後續步驟

Azure WebJobs SDK 可以搭配 WebJobs 使用，以簡化許多程式設計工作。 如需詳細資訊，請參閱 [什麼是 WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)。

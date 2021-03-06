---
title: 使用 Visual Studio 開發及部署 Webjob
description: 瞭解如何在 Visual Studio 中開發 Azure WebJobs，並將其部署至 Azure App Service，包括建立排程工作。
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 14bb693ccaa1b1d16a1d07b7ee1cdeb4493960f5
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212886"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>使用 Visual Studio 開發及部署 Webjob

本文說明如何使用 Visual Studio 將主控台應用程式專案部署至 [Azure App Service](overview.md) 中的 web 應用程式做為 [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226)。 如需如何使用 [Azure 入口網站](https://portal.azure.com)部署 webjob 的相關資訊，請參閱 [Azure App Service 中的 webjob 執行背景](webjobs-create.md)工作。

您可以選擇開發以 [.Net Core 應用程式](#webjobs-as-net-core-console-apps) 或 [.NET Framework 應用程式](#webjobs-as-net-framework-console-apps)執行的 WebJob。 2.x 版的 [AZURE WEBJOBS SDK](webjobs-sdk-how-to.md) 可讓您開發以 .net Core 應用程式或 .NET Framework 應用程式的形式執行的 webjob，而2.x 版只支援 .NET Framework。 針對 .NET Core 專案，部署 Webjob 專案的方式與 .NET Framework 專案不同。

您可以將多個 Webjob 發佈至單一 web 應用程式，前提是 web 應用程式中的每個 WebJob 都有唯一的名稱。

## <a name="webjobs-as-net-core-console-apps"></a>Webjob 做為 .NET Core 主控台應用程式

在2.x 版的 Azure WebJobs SDK 中，您可以建立 Web 工作，並將其發佈為 .NET Core 主控台應用程式。 如需建立 .NET Core 主控台應用程式並將其發佈至 Azure 作為 WebJob 的逐步指示，請參閱 [開始使用 AZURE WEBJOBS SDK 進行事件驅動的背景處理](webjobs-sdk-get-started.md)。

> [!NOTE]
> .NET Core Webjob 無法與 Web 專案連結。 如果您需要使用 web 應用程式部署 WebJob，請 [建立 webjob 作為 .NET Framework 主控台應用程式](#webjobs-as-net-framework-console-apps)。  

### <a name="deploy-to-azure-app-service"></a>部署到 Azure App Service

將 .NET Core WebJob 發行至 Visual Studio 的 Azure App Service 會使用與發佈 ASP.NET Core 應用程式相同的工具。

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>Webjob 做為 .NET Framework 主控台應用程式  

如果您使用 Visual Studio 部署啟用 Webjob 的 .NET Framework 主控台應用程式專案，它會將執行時間檔案複製到 web 應用程式中的適當資料夾 (*App_Data/jobs/continuous* 以進行持續 webjob，並針對排程或隨選的 webjob App_Data) */jobs/triggered* 。

Visual Studio 會將下列專案新增至啟用 Webjob 的專案：

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 封裝。
* 包含部署和排程器設定的 [webjob-publish-settings.json](#publishsettings) 檔案。 

![此圖顯示已新增至主控台應用程式，以啟用部署為 WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

您可以將這些專案新增至現有的主控台應用程式專案，或使用範本來建立啟用 Webjob 的新主控台應用程式專案。 

將專案本身當做 WebJob 部署，或將它連結至 Web 專案，以便在每次部署 Web 專案時自動部署。 若要連結專案，Visual Studio 會在 Web 專案的 [webjobs-list.json](#webjobslist) 檔案中加上具有 WebJobs 功能的專案名稱。

![Diagram showing WebJob project linking to web project](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>必要條件

使用 [Azure 開發工作負載](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads)安裝 Visual Studio 2017 或 Visual Studio 2019。

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> 針對現有的主控台應用程式專案啟用 Webjob 部署

您有兩個選擇：

* [透過 Web 專案啟用自動部署](#convertlink)。

  設定現有的主控台應用程式專案，以便在您部署 Web 專案時，自動將其部署為 WebJob。 當您要在與執行相關 Web 應用程式相同的 Web 應用程式中執行 WebJob 時，請使用此選項。

* [不透過 Web 專案啟用部署](#convertnolink)。

  設定現有的主控台應用程式專案，將其本身部署為 WebJob，而不需要 Web 專案的連結。 當您要在 Web 應用程式中讓應用程式自行執行 WebJob，且 Web 應用程式中沒有正在執行的 Web 應用程式時，請使用此選項。 您可能想要這麼做，以獨立于您的 web 應用程式資源來調整 WebJob 資源。

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> 透過 Web 專案啟用自動 WebJobs 部署

1. 以滑鼠右鍵按一下**方案總管**中的 Web 專案，然後選取 [**新增**  >  **現有專案] 作為 [Azure WebJob**]。
   
    ![Existing Project as Azure WebJob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [[Add Azure WebJob]](#configure) 對話方塊隨即出現。
2. 在 [ **專案名稱** ] 下拉式清單中，選取要新增為 WebJob 的主控台應用程式專案。
   
    ![在 [加入 Azure WebJob] 對話方塊中選取專案](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. 完成 [ [加入 Azure WebJob](#configure) ] 對話方塊，然後選取 **[確定]**。 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> 不透過 Web 專案啟用 WebJobs 部署
1. 以滑鼠右鍵按一下 **方案總管**中的主控台應用程式專案，然後選取 [ **發佈為 Azure WebJob**]。 
   
    ![發行為 Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    [[加入 Azure WebJob]](#configure) 對話方塊隨即出現，而且 [專案名稱]**** 方塊中已選取此專案。
2. 完成 [ [加入 Azure WebJob](#configure) ] 對話方塊，然後選取 **[確定]**。
   
   此時會出現 [發行 Web]**** 精靈。 如果您不打算立即發行，請關閉精靈。 您所輸入的設定會被儲存下來，以供[部署專案](#deploy)時使用。

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>建立具有 WebJobs 功能的新專案
若要建立啟用 Webjob 的新專案，請使用主控台應用程式專案範本並啟用 Webjob 部署，如上 [一節中所](#convert)述。 另一種方式是，您可以使用 WebJobs 新專案範本：

* [針對獨立 WebJob 使用 Webjob 新專案範本](#createnolink)
  
    建立專案，並將專案設定為以 WebJob 的方式自我部署，且不具 Web 專案的連結。 當您要在 Web 應用程式中讓應用程式自行執行 WebJob，且 Web 應用程式中沒有正在執行的 Web 應用程式時，請使用此選項。 您可能想要這麼做，以獨立于您的 web 應用程式資源來調整 WebJob 資源。
* [針對連結至 Web 專案的 WebJob 使用 webjob 新專案範本](#createlink)
  
    建立專案，設定為當您在相同的方案中部署 Web 專案時，自動部署為 WebJob。 當您要在與執行相關 Web 應用程式相同的 Web 應用程式中執行 WebJob 時，請使用此選項。

> [!NOTE]
> WebJobs 新專案範本自動安裝 NuGet 套件，並包括適用於 *WebJobs SDK* 的 [Program.cs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)程式碼。 如果您不想使用 WebJobs SDK，請移除或變更 *Program.cs* 中的 `host.RunAndBlock` 陳述式。
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> 在獨立的 WebJob 中使用 WebJobs 新專案範本
1. 選取 [File] \(檔案\) >  [New] \(新增\) >  [Project] \(專案\)。 在 [ **建立新的專案** ] 對話方塊中，搜尋並選取適用于 c # 的 **Azure WebJob ( .NET Framework) ** 。
   
2. 遵循先前的指示， [讓主控台應用程式專案成為獨立的 webjob 專案](#convertnolink)。

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> 在連結至 Web 專案的 WebJob 中使用 WebJobs 新專案範本
1. 以滑鼠右鍵按一下**方案總管**中的 Web 專案，**然後選取 [** 新增] [  >  **新的 Azure WebJob 專案**]。
   
    ![New Azure WebJob Project menu entry](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [[Add Azure WebJob]](#configure) 對話方塊隨即出現。
2. 完成 [ [加入 Azure WebJob](#configure) ] 對話方塊，然後選取 **[確定]**。


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a> 檔案上的webjob-publish-settings.js
當您針對 Webjob 部署設定主控台應用程式時，Visual Studio 會安裝[Web webjob. Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet 封裝，並將排程資訊儲存在 webjob 專案之 [專案*屬性*] 資料夾中的檔案*webjob-publish-settings.js* 。 以下是該檔案的範例：

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

您可以編輯此檔案目錄，而 Visual Studio 會提供 IntelliSense。 檔案架構會儲存在中 [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) ，而且可以在該處查看。  

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a> 檔案上的webjobs-list.js
當您將具有 WebJobs 功能的專案連結到 Web 專案時，Visual Studio 會將 WebJobs 專案的名稱儲存在 Web 專案中 *Properties* 資料夾的 *webjobs-list.json* 檔案。 此清單可能包含多個 WebJobs 專案，如下列範例所示：

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
  "WebJobs": [
    {
      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
    },
    {
      "filePath": "../WebJob1/WebJob1.csproj"
    }
  ]
}
```

您可以使用 IntelliSense，直接在 Visual Studio 中編輯此檔案。 檔案架構會儲存在 [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) 。

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>部署 WebJobs 專案
您已連結至 Web 專案的 Webjob 專案會自動與 Web 專案一起部署。 如需 Web 專案部署的相關資訊，請參閱左側導覽中的操作**指南**  >  **部署應用程式**。

若要自行部署 Webjob 專案，請以滑鼠右鍵按一下 **方案總管** 中的專案，然後選取 [ **發佈為 Azure WebJob**]。 

![發行為 Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

若是獨立的 WebJob，則 Web 專案所使用的相同 [發行 Web] **** 精靈隨即出現，但其中幾個設定可以變更。

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>[新增 Azure WebJob] 對話方塊
[ **加入 Azure WebJob** ] 對話方塊可讓您輸入 WebJob 的 webjob 名稱和執行模式設定。 

![[新增 Azure WebJob] 對話方塊](./media/webjobs-dotnet-deploy-vs/aaw2.png)

此對話方塊中的某些欄位會對應至 Azure 入口網站 [ **新增 WebJob** ] 對話方塊上的欄位。 如需詳細資訊，請參閱 [在 Azure App Service 中使用 Webjob 執行背景](webjobs-create.md)工作。

WebJob 部署資訊：

* 如需命令列部署的詳細資訊，請參閱[啟用 Azure WebJobs 的命令列或連續傳遞](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)。

* 如果您部署 WebJob，然後決定要變更 WebJob 的類型並重新部署，請刪除檔案 * 上的webjobs-publish-settings.js* 。 這麼做會導致 Visual Studio 重新顯示發行選項，讓您可以變更 WebJob 的類型。

* 如果部署 WebJob，並在稍後將執行模式從連續變更為非連續 (或相反情形)，則在您重新部署時，Visual Studio 會在 Azure 中建立新的 WebJob。 如果您變更其他排程設定，但讓執行模式維持不變，或在排程和隨選之間切換，Visual Studio 會更新現有的作業，而不是建立新的工作。

## <a name="webjob-types"></a>WebJob 類型

WebJob 的類型可以是 [已 *觸發* ] 或 [ *連續*]：

-  (預設) 觸發：觸發的 WebJob 會根據系結事件、 [排程](#scheduling-a-triggered-webjob)或視需要以手動方式 (隨選) 來觸發時啟動。 它會在 web 應用程式執行所在的所有實例上執行，但您可以選擇性地將 WebJob 限制為單一實例。

- 連續：建立 WebJob 時，會立即啟動 [連續](#continuous-execution) WebJob。 這種類型的 WebJob 最適合用於未系結或長時間執行的作業。 如果作業結束，您可以重新啟動。  

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>排程觸發式 WebJob

當您將主控台應用程式發佈至 Azure 時，Visual Studio 會將 WebJob 的類型設定為預設 **觸發** ，並將新的 *設定作業* 檔案加入至專案。 針對觸發的 WebJob 類型，您可以使用這個檔案來設定 WebJob 的執行排程。

使用 [ *設定* ] [工作] 檔案來設定 WebJob 的執行排程。 下列範例會每小時從上午9點到下午5點執行：

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

此檔案位於 webjob 資料夾的根目錄中，其中包含您的 WebJob 腳本，例如 `wwwroot\app_data\jobs\triggered\{job name}` 或 `wwwroot\app_data\jobs\continuous\{job name}` 。 當您從 Visual Studio 部署 WebJob 時，請將 Visual Studio 中的 *工作* 檔案屬性標示為 [複製] （ **如果較新**）。

如果您 [從 Azure 入口網站建立 WebJob](webjobs-create.md)，就會為您建立 *設定作業* 檔案。

#### <a name="cron-expressions"></a>CRON 運算式

Webjob 會使用與 Azure Functions 中的計時器觸發程式相同的 CRON 運算式進行排程。 若要深入瞭解 CRON 支援，請參閱 [Azure Functions 的計時器觸發程式](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)。

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>設定。作業參考

Webjob 支援下列設定：

| **設定** | **型別**  | **說明** |
| ----------- | --------- | --------------- |
| `is_in_place` | 全部 | 允許 WebJob 就地執行，而不需要先將它複製到暫存資料夾。 如需詳細資訊，請參閱 [WebJob 工作目錄](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)。 |
| `is_singleton` | 連續 | 只有在相應放大時，才在單一實例上執行 WebJob。如需詳細資訊，請參閱 [將連續作業設定為 singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)。 |
| `schedule` | 觸發 | 在以 CRON 為基礎的排程上執行 WebJob。 如需詳細資訊，請參閱 [NCRONTAB 運算式](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)。 |
| `stopping_wait_time`| 全部 | 允許控制關閉行為。 如需詳細資訊，請參閱[正常關機](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)。 |

### <a name="continuous-execution"></a>連續執行

如果您在 Azure 中啟用 **Always on** ，您可以使用 Visual Studio 將 WebJob 變更為持續執行：

1. 如果您尚未這麼做，請將 [專案發佈至 Azure](#deploy-to-azure-app-service)。

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案並選取 [發佈]。

1. 在 [ **發佈** ] 索引標籤中，選擇 [ **編輯**]。 

1. 在 [**設定檔設定**] 對話方塊中，針對 [ **WebJob 類型**] 選擇 [**連續**]，然後選擇 [**儲存**]。

    ![WebJob 的 [發行設定] 對話方塊](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. 在 [**發佈**] 索引標籤中選取 [**發佈**]，以使用更新的設定重新發佈 WebJob。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 WebJobs SDK](webjobs-sdk-how-to.md)

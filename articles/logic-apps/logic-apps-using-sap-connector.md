---
title: 連接到 SAP 系統
description: 使用 Azure Logic Apps 自動化工作流程，以存取和管理 SAP 資源
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/21/2020
tags: connectors
ms.openlocfilehash: a0f6af706a81db537b9ed66dc49996282c4dbbaa
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87833890"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>從 Azure Logic Apps 連線至 SAP 系統

> [!IMPORTANT]
> 舊版的 SAP 應用程式伺服器和 SAP 訊息伺服器連接器會在2020年2月29日淘汰。 目前的 SAP 連接器會合並這些先前的 SAP 連接器，讓您不需要變更連線類型、與先前的連接器完全相容、提供許多額外的功能，並繼續使用 SAP .Net connector 程式庫 (SAP NCo) 。
>
> 對於使用較舊連接器的邏輯應用程式，請在淘汰日期之前[遷移至最新的連接器](#migrate)。 否則，這些邏輯應用程式會遇到執行失敗，而且無法將訊息傳送至您的 SAP 系統。

本文說明如何使用 SAP 連接器，從邏輯應用程式記憶體取內部部署 SAP 資源。 連接器適用于 SAP 的傳統版本，例如 R/3 和 ECC 系統內部部署。 連接器也可以與 SAP 較新的 HANA 型 SAP 系統 (例如 S/4 HANA) 整合，無論是否裝載於內部部署或雲端。 SAP 連接器支援透過中繼文件 (IDoc)、商務應用程式開發介面 (BAPI) 或遠端函式呼叫 (RFC) 來進行訊息或資料與 SAP NetWeaver 型系統的雙向整合。

SAP 連接器會使用[sap .Net connector (NCo) 程式庫](https://support.sap.com/en/product/connectors/msnet.html)，並提供下列動作：

* **將訊息傳送至 sap**：透過 TRFC 傳送 IDoc、透過 RFC 呼叫 BAPI 函式，或在 SAP 系統中呼叫 RFC/tRFC。

* **從 sap 收到訊息時**：接收 IDoc over tRFC、透過 TRFC 呼叫 BAPI 函式，或在 SAP 系統中呼叫 RFC/tRFC。

* **產生架構**：為 IDOC、BAPI 或 RFC 的 SAP 成品產生架構。

針對這些作業，SAP 連接器支援透過使用者名稱和密碼進行基本驗證。 連接器也支援[ (SNC) 的安全網路通訊](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)。 SNC 可用於 SAP NetWeaver 單一登入 (SSO) 或供外部安全性產品所提供的其他安全性功能使用。

本文說明如何建立會與 SAP 整合的邏輯應用程式範例，同時也會說明先前所述的整合案例。 針對使用舊版 SAP 連接器的邏輯應用程式，本文說明如何將您的邏輯應用程式遷移至最新的 SAP 連接器。

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Prerequisites

若要遵循本文中的範例，您需要以下項目：

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您要用來存取 SAP 系統的邏輯應用程式，以及啟動邏輯應用程式工作流程的觸發程序。 如果您不熟悉邏輯應用程式，請參閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立您的第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 您的[sap 應用程式伺服器](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)或[sap 訊息伺服器](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)。

* 您傳送至 SAP 伺服器的訊息內容（例如範例 IDoc 檔案）必須是 XML 格式，並包含您想要使用之 SAP 動作的命名空間。

* 若要使用 [**從 SAP 觸發程式接收訊息時**]，您也需要執行下列設定步驟：
  
  > [!NOTE]
  > 此觸發程式會使用相同的 URI 位置，從 webhook 訂用帳戶更新和取消訂閱。 更新作業會使用 HTTP `PATCH` 方法，而取消訂閱作業會使用 HTTP `DELETE` 方法。 此行為可能會使更新作業在觸發程式的歷程記錄中顯示為取消訂閱作業，但此作業仍會更新，因為觸發程式會使用 `PATCH` 做為 HTTP 方法，而不是 `DELETE` 。

  * 使用此設定來設定您的 SAP 閘道安全性許可權：

    `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

  * 設定您的 SAP 閘道安全性記錄，以協助尋找存取控制清單 (ACL) 錯誤，而且預設不會啟用。 否則，您會收到下列錯誤：

    `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    如需詳細資訊，請參閱 SAP 說明主題[設定閘道記錄](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm)。

<a name="multi-tenant"></a>

### <a name="multi-tenant-azure-prerequisites"></a>多租使用者 Azure 必要條件

當您的邏輯應用程式在多租使用者 Azure 中執行，而您想要使用受控 SAP 連接器（不會在整合服務環境中以原生方式執行[ (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)）時，這些必要條件適用。 否則，如果您使用的是高階 ISE，而且想要使用在 ISE 中以原生方式執行的 SAP 連接器，請參閱[整合服務環境 (ISE) 必要條件](#sap-ise)。

受控 (非 ISE) SAP 連接器會透過內部[部署資料閘道](../logic-apps/logic-apps-gateway-connection.md)與內部部署 SAP 系統整合。 例如，在傳送訊息案例中，當訊息從邏輯應用程式傳送到 SAP 系統時，資料閘道會作為 RFC 用戶端，並將接收自邏輯應用程式的要求轉送到 SAP。 同樣地，在接收訊息案例中，資料閘道會作為 RFC 伺服器，以接收來自 SAP 的要求，並將其轉送至邏輯應用程式。

* 在您的本機電腦上[下載並安裝內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。 然後，在 Azure 入口網站中為該閘道[建立 Azure 閘道資源](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource)。 閘道可協助您安全地存取內部部署資料和資源。

  基於最佳作法，請務必使用支援的內部部署資料閘道版本。 Microsoft 每個月會發行新版本。 目前，Microsoft 支援最後六個版本。 如果您的閘道發生問題，請嘗試[升級至最新版本](https://aka.ms/on-premises-data-gateway-installer)，其中可能包含更新以解決您的問題。

* 在與內部部署資料閘道相同的電腦上[，下載並安裝最新的 SAP 用戶端程式庫](#sap-client-library-prerequisites)。

<a name="sap-ise"></a>

### <a name="integration-service-environment-ise-prerequisites"></a>整合服務環境 (ISE) 必要條件

當您的邏輯應用程式在高階層級 (，而非開發人員層級) [整合服務環境 (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中執行，且您想要使用在 ISE 中以原生方式執行的 SAP 連接器時，適用這些必要條件。 ISE 提供 Azure 虛擬網路所保護的資源存取權，並提供其他 ISE 原生連接器，讓邏輯應用程式直接存取內部部署資源，而不需使用內部部署資料閘道。

> [!NOTE]
> 雖然 SAP ISE 連接器會顯示在開發人員層級 ISE 內部，但嘗試安裝連接器並不會成功。

1. 如果您還沒有 Azure 儲存體帳戶和 blob 容器，請使用[Azure 入口網站](../storage/blobs/storage-quickstart-blobs-portal.md)或[Azure 儲存體總管](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)來建立該容器。

1. 在您的本機電腦上[下載並安裝最新的 SAP 用戶端程式庫](#sap-client-library-prerequisites)。 您應該擁有下列元件檔：

   * libicudecnumber.dll
   * rscp4n.dll
   * sapnco.dll
   * sapnco_utils.dll

1. 建立包含這些元件的 .zip 檔案，並將此封裝上傳到 Azure 儲存體中的 blob 容器。

1. 在 Azure 入口網站或 Azure 儲存體總管中，流覽至您上傳 .zip 檔案的容器位置。

1. 複製該位置的 URL，並確定您包含 (SAS) 權杖的共用存取簽章。

   否則，SAS 權杖不會獲得授權，而且 SAP ISE 連接器的部署將會失敗。

1. 在您可以使用 SAP ISE 連接器之前，您必須先在 ISE 中安裝並部署連接器。

   1. 在 [ [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的 ISE。
   
   1. 在 ISE 功能表上，選取 [**受控連接器**] [  >  **新增**]。 從 [連接器] 清單中，尋找並選取 [ **SAP**]。
   
   1. 在 [新增**受管理的連接器**] 窗格的 [ **sap 封裝**] 方塊中，貼上具有 SAP 元件之 .zip 檔案的 URL。 *請確定您包含 SAS 權杖。*

   1. 當您完成時，選取 [建立]  。

   如需詳細資訊，請參閱[新增 ISE 連接器](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment)。

1. 如果您的 SAP 實例和 ISE 位於不同的虛擬網路中，您也需要[對這些網路進行對等](../virtual-network/tutorial-connect-virtual-networks-portal.md)互連，讓 ISE 的虛擬網路連線到您的 SAP 實例的虛擬網路。

<a name="sap-client-library-prerequisites"></a>

### <a name="sap-client-library-prerequisites"></a>SAP 用戶端程式庫必要條件

* 請確定您已安裝最新版本： [SAP Connector (NCo 3.0) For Microsoft .net 3.0.22.0 以 .NET Framework 4.0-Windows 64-bit (x64) 進行編譯](https://softwaredownloads.sap.com/file/0020000001000932019)。 舊版可能會導致相容性問題。 如需詳細資訊，請參閱[SAP 用戶端程式庫版本](#sap-library-versions)。

* 根據預設，SAP 安裝程式會將元件檔放在預設安裝資料夾中。 您需要根據您的案例，將這些元件檔案複製到另一個位置，如下所示：

  * 針對在 ISE 中執行的邏輯應用程式，請遵循[整合服務環境必要條件](#sap-ise)中所述的步驟。 對於在多租使用者 Azure 中執行並使用內部部署資料閘道的邏輯應用程式，請將元件檔從預設安裝資料夾複製到資料閘道安裝資料夾。 如果您遇到資料閘道的問題，請參閱下列問題：

  * 您必須安裝適用于 SAP 用戶端程式庫的64位版本，因為資料閘道只會在64位系統上執行。 否則，您會遭遇「映像錯誤」錯誤，因為資料閘道主機服務不支援 32 位元組件。

  * 如果您的 SAP 連線失敗，並出現錯誤訊息「請檢查您的帳戶資訊及（或）許可權，然後再試一次」，則元件檔案可能位於錯誤的位置。 請確定您已將元件檔複製到資料閘道安裝資料夾。

    為了協助您進行疑難排解，請[使用 .net 元件系結記錄檔檢視器](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer)，這可讓您檢查元件檔是否在正確的位置。 （選擇性）您可以在安裝 SAP 用戶端程式庫時，選取 [**全域組件快取註冊**] 選項。

<a name="sap-library-versions"></a>

#### <a name="sap-client-library-versions"></a>SAP 用戶端程式庫版本

當同時傳送多個 IDoc 訊息時，較舊的 SAP NCo 版本可能會變得鎖死。 此條件會封鎖傳送至 SAP 目的地的所有後續訊息，這會導致訊息超時。

以下是 SAP 用戶端程式庫、.NET Framework、.NET 執行時間和閘道之間的關聯性：

* Microsoft SAP 介面卡和閘道主機服務都會使用 .NET Framework 4.5。

* 適用於 .NET Framework 4.0 的 SAP NCo 可執行採用 .NET runtime 4.0 到 4.7.1 版的流程。

* 適用于 .NET Framework 2.0 的 SAP NCo 適用于使用 .NET 執行時間2.0 至3.5 的進程，但不再適用于最新的閘道。

### <a name="secure-network-communications-prerequisites"></a>保護網路通訊必要條件

如果您使用內部部署資料閘道搭配選擇性的安全網路通訊 (SNC) （僅在多租使用者 Azure 中支援），則您也需要設定這些設定：

* 如果您使用具有單一登入 (SSO) 的 SNC，請確定資料閘道是以對應于 SAP 使用者的使用者身分執行。 若要變更預設帳戶，請選取 [**變更帳戶**]，然後輸入使用者認證。

  ![變更資料閘道帳戶](./media/logic-apps-using-sap-connector/gateway-account.png)

* 如果您啟用具有外部安全性產品的 SNC，請將 SNC 程式庫或檔案複製到已安裝資料閘道的同一部電腦上。 SNC 產品的一些範例包括[sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、KERBEROS 和 NTLM。

如需啟用資料閘道 SNC 的詳細資訊，請參閱[啟用安全網路通訊](#secure-network-communications)。

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>遷移至目前的連接器

若要從舊版受控 (非 ISE) SAP 連接器遷移至目前受控 SAP 連接器，請遵循下列步驟：

1. 如果您尚未這麼做，請更新您的[內部部署資料閘道](https://www.microsoft.com/download/details.aspx?id=53127)，讓您擁有最新版本。 如需詳細資訊，請參閱[安裝適用于 Azure Logic Apps 的內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。

1. 在使用舊版 SAP 連接器的邏輯應用程式中，刪除 [**傳送至 SAP** ] 動作。

1. 從最新的 SAP 連接器中，新增 [**傳送訊息至 SAP** ] 動作。 在您可以使用此動作之前，請先重新建立與 SAP 系統的連線。

1. 完成後，儲存邏輯應用程式。

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>將訊息傳送至 SAP

此範例使用您可透過 HTTP 要求觸發的邏輯應用程式。 邏輯應用程式會將 IDoc 傳送至 SAP 伺服器，並將回應傳回給呼叫邏輯應用程式的要求者。

### <a name="add-an-http-request-trigger"></a>新增 HTTP 要求觸發程式

在 Azure Logic Apps 中，每個邏輯應用程式都必須使用[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)啟動，而該觸發程序會在特定事件發生或符合特定條件時引發。 每次引發觸發程序時，Logic Apps 引擎都會建立邏輯應用程式執行個體，並開始執行應用程式的工作流程。

> [!NOTE]
> 當邏輯應用程式收到來自 SAP 的 IDoc 封包時，[要求觸發](../connectors/connectors-native-reqres.md)程式不支援 Sap WE60 IDoc 檔所產生的「純文字」 XML 架構。 不過，在從邏輯應用程式傳送訊息*至*SAP 的案例中，支援「純文字」 XML 架構。 您可以搭配使用要求觸發程式與 SAP 的 IDoc XML，但不能搭配 IDoc over RFC。 或者，您可以將 XML 轉換成必要的格式。 

在此範例中，您可使用 Azure 中的端點建立邏輯應用程式，如此即可將 *HTTP POST 要求*傳送至您的邏輯應用程式。 邏輯應用程式收到這些 HTTP 要求後，觸發程序便會在您的工作流程中引發並執行後續步驟。

1. 在[Azure 入口網站](https://portal.azure.com)中，建立空白邏輯應用程式，以開啟邏輯應用程式設計工具。

1. 在搜尋方塊中，輸入 `http request` 作為篩選條件。 從**觸發**程式清單中，選取 [**收到 HTTP 要求時**]。

   ![新增 HTTP 要求觸發程序](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 現在儲存您的邏輯應用程式，讓您可以產生邏輯應用程式的端點 URL。 在設計工具的工具列上，選取 [儲存]  。

   端點 URL 現在會出現在您的觸發程序中，例如：

   ![產生端點的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>新增 SAP 動作

在 Azure Logic Apps 中，[動作](../logic-apps/logic-apps-overview.md#logic-app-concepts)是工作流程中跟隨在觸發程序或另一個動作之後的步驟。 如果您尚未新增觸發程序至邏輯應用程式，並且想要遵循此範例進行，請[按照本節所述指示新增觸發程序](#add-trigger)。

1. 在邏輯應用程式設計工具的觸發程式底下，選取 [**新增步驟**]。

   ![將新步驟新增至邏輯應用程式](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從 [**動作**] 清單中，選取 [**傳送訊息至 SAP**]。
  
   ![選取 [傳送訊息至 SAP] 動作](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   或者，您可以選取 [**企業**] 索引標籤，然後選取 [SAP] 動作。

   ![從 [企業] 索引標籤選取 [傳送訊息至 SAP] 動作](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 如果您的連線已存在，請繼續進行下一個步驟，讓您可以設定 SAP 動作。 不過，如果系統提示您輸入連線詳細資料，請提供資訊，讓您可以建立與內部部署 SAP 伺服器的連線。

   1. 提供連接的名稱。

   1. 如果您使用的是資料閘道，請遵循下列步驟：
   
      1. 在 [**資料閘道**] 區段的 [**訂**用帳戶] 底下，先針對您在資料閘道安裝的 Azure 入口網站中所建立的資料閘道資源選取 Azure 訂用帳戶。
   
      1. 在 [連線**閘道**] 底下，選取您在 Azure 中的資料閘道資源。

   1. 繼續提供連接的相關資訊。 針對 [**登入類型**] 屬性，請根據此屬性是否設為 [**應用程式伺服器**] 或 [**群組**] 來遵循此步驟：
   
      * 若為**應用程式伺服器**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 若為**群組**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      根據預設，強式類型是用來透過對架構執行 XML 驗證來檢查是否有不正確值。 此行為可協助您稍早偵測到問題。 [**安全類型**] 選項可用於回溯相容性，而且只會檢查字串長度。 深入瞭解[安全輸入選項](#safe-typing)。

   1. 完成之後，選取 [建立]。

      Logic Apps 會設定並測試您的連線，以確保連線正常運作。

1. 立即尋找並從 SAP 伺服器選取動作。

   1. 在 [ **SAP 動作**] 方塊中，選取資料夾圖示。 從檔案清單中，尋找並選取您要使用的 SAP 訊息。 若要瀏覽清單，請使用箭號。

      這個範例會選取具有**Orders**類型的 IDoc。

      ![尋找並選取 IDoc 動作](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      如果找不到您需要的動作，您可手動輸入路徑，例如：

      ![手動提供路徑給 IDoc 動作](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 透過運算式編輯器提供**SAP 動作**的值。 如此一來，您就可以針對不同的訊息類型使用相同的動作。

      如需 IDoc 作業的詳細資訊，請參閱[IDoc 作業的訊息架構](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

   1. 在 [輸入訊息]**** 方塊內按一下，動態內容清單隨即顯示。 在該清單中的 [收到 HTTP 要求時]**** 之下，選取 [內文]**** 欄位。

      此步驟包含來自 HTTP 要求觸發程式的本文內容，並將該輸出傳送至您的 SAP 伺服器。

      ![從觸發程式選取 "Body" 屬性](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      當您完成時，您的 SAP 動作會如下列範例所示：

      ![完成 SAP 動作](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>新增 HTTP 回應動作

現在請將回應動作新增至邏輯應用程式的工作流程，並納入 SAP 動作的輸出結果。 如此一來，邏輯應用程式便會從 SAP 伺服器將結果傳回至原始的要求者。

1. 在邏輯應用程式設計工具的 [SAP] 動作底下，選取 [**新增步驟**]。

1. 在搜尋方塊中，輸入 `response` 作為篩選條件。 從 [**動作**] 清單中，選取 [**回應**]。

1. 在 [內文]**** 方塊內按一下，動態內容清單隨即顯示。 從該清單的 [**將訊息傳送至 SAP**] 底下**Body** ，選取 [內文] 欄位。

   ![完整的 SAP 動作](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. 儲存您的邏輯應用程式。

#### <a name="add-rfc-request-response"></a>新增 RFC 要求-回應

> [!NOTE]
> SAP 觸發程式會透過 tRFC 接收 Idoc，其設計不會有回應參數。 

如果您需要使用遠端函式呼叫來接收回複（ (RFC) 從 SAP ABAP Logic Apps，您必須建立要求和回應模式。 若要在邏輯應用程式中接收 Idoc，您應該將其第一個動作設為[HTTP 要求](../connectors/connectors-native-reqres.md#add-a-response-action)，其狀態碼為 `200 OK` ，且沒有內容。 此建議步驟會立即完成透過 tRFC 的 SAP LUW 非同步傳輸，讓 SAP CPIC 交談再次可供使用。 接著，您可以在邏輯應用程式中新增進一步的動作來處理接收的 IDoc，而不會封鎖進一步的傳輸。

若要執行要求和回應模式，您必須先使用[ `generate schema` 命令](#generate-schemas-for-artifacts-in-sap)探索 RFC 架構。 產生的架構有兩個可能的根節點： 

1. 要求節點，這是您從 SAP 接收的呼叫。
1. 回應節點，這會回復至 SAP。

在下列範例中，會從 RFC 模組產生要求和回應模式 `STFC_CONNECTION` 。 要求 XML 會經過剖析，以解壓縮 SAP 要求的節點值 `<ECHOTEXT>` 。 回應會將目前的時間戳記插入為動態值。 當您 `STFC_CONNECTION` 從邏輯應用程式傳送 RFC 到 SAP 時，您會收到類似的回應。

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 如果您的邏輯應用程式尚未啟用，請在邏輯應用程式功能表上，選取 **[總覽**]。 在工具列上，選取 [**啟用**]。

1. 在設計工具工具列上，選取 [**執行**]。 此步驟會手動啟動您的邏輯應用程式。

1. 藉由將 HTTP POST 要求傳送至 HTTP 要求觸發程式中的 URL，來觸發邏輯應用程式。
將您的訊息內容包含在您的要求中。 若要傳送內容，您可使用 [Postman](https://www.getpostman.com/apps) 之類的工具。

   在本文中，要求會傳送 IDoc 檔案，其必須為 XML 格式並且包含您要使用的 SAP 動作的命名空間，例如：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. 傳送 HTTP 要求後，請等候來自邏輯應用程式的回應。

   > [!NOTE]
   > 如果回應所需的所有步驟未在[要求逾時期限](./logic-apps-limits-and-config.md)內完成，邏輯應用程式可能會逾時。 如果發生此情況，要求可能就會遭到封鎖。 為協助您診斷問題，請了解如何[檢查和監控邏輯應用程式](../logic-apps/monitor-logic-apps.md)。

您現在已建立可與您的 SAP 伺服器通訊的邏輯應用程式。 您現在已為邏輯應用程式設定 SAP 連線，接下來可探索其他可用的 SAP 動作如：BAPI 和 RFC。

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>從 SAP 接收訊息

這個範例會使用應用程式從 SAP 系統接收訊息時觸發的邏輯應用程式。

### <a name="add-an-sap-trigger"></a>新增 SAP 觸發程式

1. 在 Azure 入口網站中，建立空白的邏輯應用程式，以開啟邏輯應用程式設計工具。

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從 [**觸發**程式] 清單中，選取 [**從 SAP 收到訊息時**]。

   ![新增 SAP 觸發程序](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   或者，您可以選取 [**企業**] 索引標籤，然後選取觸發程式：

   ![從 [企業] 索引標籤新增 SAP 觸發程式](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 如果您的連線已存在，請繼續進行下一個步驟，讓您可以設定 SAP 動作。 不過，如果系統提示您輸入連線詳細資料，請提供資訊，讓您現在可以建立與內部部署 SAP 伺服器的連線。

   1. 提供連接的名稱。

   1. 如果您使用的是資料閘道，請遵循下列步驟：

      1. 在 [**資料閘道**] 區段的 [**訂**用帳戶] 底下，先針對您在資料閘道安裝的 Azure 入口網站中所建立的資料閘道資源選取 Azure 訂用帳戶。

      1. 在 [連線**閘道**] 底下，選取您在 Azure 中的資料閘道資源。

   1. 繼續提供連接的相關資訊。 針對 [**登入類型**] 屬性，請根據此屬性是否設為 [**應用程式伺服器**] 或 [**群組**] 來遵循此步驟：

      * 若為**應用程式伺服器**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 若為**群組**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      根據預設，強式類型是用來透過對架構執行 XML 驗證來檢查是否有不正確值。 此行為可協助您稍早偵測到問題。 [**安全類型**] 選項可用於回溯相容性，而且只會檢查字串長度。 深入瞭解[安全輸入選項](#safe-typing)。

   1. 完成之後，選取 [建立]。

      Logic Apps 會設定並測試您的連線，以確保連線正常運作。

1. 根據您的 SAP 系統組態提供[必要的參數](#parameters)。 

   您可以藉[由指定 sap 動作清單來篩選您從 sap 伺服器接收的訊息](#filter-with-sap-actions)。

   您可以從檔案選擇器選取 SAP 動作：

   ![將 SAP 動作新增至邏輯應用程式](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   或者，您可以手動指定動作：

   ![手動輸入 SAP 動作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   以下舉例說明當您設定觸發程序來接收多個訊息時，此動作的顯示方式。

   ![接收多個訊息的觸發程式範例](media/logic-apps-using-sap-connector/example-trigger.png)

   如需 SAP 動作的詳細資訊，請參閱[IDoc 作業的訊息架構](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. 現在儲存邏輯應用程式，以便可以開始從 SAP 系統接收訊息。 在設計工具的工具列上，選取 [儲存]  。

邏輯應用程式現在已準備好從 SAP 系統接收訊息。

> [!NOTE]
> SAP 觸發程式不是輪詢觸發程式，而是以 webhook 為基礎的觸發程式。 如果您使用資料閘道，只有在訊息存在時，才會從資料閘道呼叫觸發程式，因此不需要輪詢。

<a name="parameters"></a>

#### <a name="parameters"></a>參數

除了簡單的字串和數位輸入，SAP 連接器會接受下列資料表參數 (`Type=ITAB` 輸入) ：

* 適用于較舊 SAP 版本的資料表方向參數，包括輸入和輸出。
* 變更參數，以取代較新 SAP 版本的資料表方向參數。
* 階層式資料表參數

<a name="filter-with-sap-actions"></a>

#### <a name="filter-with-sap-actions"></a>使用 SAP 動作進行篩選

您可以選擇性地篩選邏輯應用程式從 SAP 伺服器接收的訊息，方法是提供具有單一或多個 SAP 動作的清單或陣列。 根據預設，此陣列是空的，這表示您的邏輯應用程式會從您的 SAP 伺服器接收所有訊息，而不進行篩選。 

當您設定陣列篩選器時，觸發程式只會從指定的 SAP 動作類型接收訊息，並拒絕來自 SAP 伺服器的所有其他訊息。 不過，此篩選不會影響輸入的裝載是弱式或強式。

任何 SAP 動作篩選會在您的內部部署資料閘道的 SAP 介面卡層級進行。 如需詳細資訊，請參閱[如何將測試 idoc 傳送至來自 SAP 的 Logic Apps](#send-idocs-from-sap)。

如果您無法將 IDoc 封包從 SAP 傳送至邏輯應用程式的觸發程式，請參閱 < SAP tRFC 對話方塊中的交易式 RFC (tRFC) 呼叫拒絕訊息 (T 程式碼 SM58) 。 在 SAP 介面中，您可能會收到下列錯誤訊息，這是因為**狀態文字**欄位上的子字串限制而被裁剪。

* `The RequestContext on the IReplyChannel was closed without a reply being`：當通道的 catch-all 處理常式因錯誤而終止通道時，會發生未預期的失敗，並重建通道以處理其他訊息。

  * 若要確認邏輯應用程式已收到 IDoc，請新增會傳回狀態碼的[回應動作](../connectors/connectors-native-reqres.md#add-a-response-action) `200 OK` 。 IDoc 是透過 tRFC 傳輸，這不允許回應承載。

  * 如果您需要改為拒絕 IDoc，請以以外的任何 HTTP 狀態碼回應， `200 OK` 讓 SAP 介面卡代表您將例外狀況傳回給 sap。 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`：發生其他錯誤時，預期的失敗，例如無法產生 IDoc XML 裝載，因為 SAP 不會釋放其區段，因此遺失轉換所需的區段類型中繼資料。 

  * 若要讓 SAP 發行這些區段，請洽詢您 SAP 系統的 ABAP 工程師。

<a name="find-extended-error-logs"></a>

## <a name="find-extended-error-logs"></a>尋找擴充的錯誤記錄檔

如需完整的錯誤訊息，請檢查您的 SAP 介面卡的擴充記錄。 

針對從2020年6月和更新版本的內部部署資料閘道版本，您可以[在應用程式設定中啟用閘道記錄](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app)。

針對從2020年4月和更早版本的內部部署資料閘道版本，預設會停用記錄。 若要取出擴充記錄，請遵循下列步驟：

1. 在您的內部部署資料閘道安裝資料夾中，開啟檔案 `Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` 。 

1. 針對 [ **SapExtendedTracing** ] 設定，將值從 [ **False** ] 變更為 [ **True**]。

1. （選擇性）針對較少的事件，將**SapTracingLevel**值從**資訊** (預設) 變更為 [**錯誤**] 或 [**警告**]。 或者，如需更多事件，請將**資訊**變更為**Verbose**。

1. 儲存組態檔。

1. 重新開機您的資料閘道。 開啟您的內部部署資料閘道安裝程式應用程式，然後移至 [**服務設定**] 功能表。 在 **[重新開機閘道**] 底下，選取 [**立即重新開機**]。

1. 重現您的問題。

1. 匯出閘道記錄。 在您的資料閘道安裝程式應用程式中，移至 [**診斷**] 功能表。 在 [閘道記錄]**** 之下，選取 [匯出記錄]****。 這些檔案包含依日期組織的 SAP 記錄。 視記錄檔大小而定，單一日期可能會有多個記錄檔。

1. 在設定檔中，將**SapExtendedTracing**設定還原為**False**。

1. 重新啟動閘道服務。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 若要觸發邏輯應用程式，請從 SAP 系統傳送訊息。

1. 在邏輯應用程式功能表上，選取 **[總覽**]。 針對您的邏輯應用程式，檢查**執行歷程記錄**中是否有任何新的執行。

1. 開啟最近的執行，其會在觸發程序的輸出區段中顯示從 SAP 系統傳送的訊息。

<a name="send-idocs-from-sap"></a>

### <a name="test-sending-idocs-from-sap"></a>測試從 SAP 傳送 Idoc

若要將 Idoc 從 SAP 傳送至邏輯應用程式，您需要下列最低設定：

> [!IMPORTANT]
> 只有在您使用邏輯應用程式測試 SAP 設定時，才使用這些步驟。 生產環境需要額外的設定。

1. [在 SAP 中設定 RFC 目的地](#create-rfc-destination)

1. [建立與您的 RFC 目的地之間的 ABAP 連接](#create-abap-connection)

1. [建立接收者埠](#create-receiver-port)

1. [建立寄件者埠](#create-sender-port)

1. [建立邏輯系統夥伴](#create-logical-system-partner)

1. [建立夥伴設定檔](#create-partner-profiles)

1. [測試傳送訊息](#test-sending-messages)

#### <a name="create-rfc-destination"></a>建立 RFC 目的地

1. 若要開啟**RFC**連線設定的設定，請在您的 SAP 介面中使用**sm59**的交易程式碼 (T-code) 與 **/n**前置詞。

1. 選取 [ **tcp/ip 連接**] [  >  **建立**]。

1. 使用下列設定建立新的 RFC 目的地：
    
    * 針對您的**RFC 目的地**，輸入名稱。
    
    * 在 [**技術設定**] 索引標籤的 [**啟用類型**]，選取 [**已註冊的伺服器程式**]。 針對您的**程式識別碼**，輸入值。 在 SAP 中，會使用此識別碼來註冊邏輯應用程式的觸發程式。
    
    * 在 [ **unicode** ] 索引標籤上，針對 [**目標系統的通訊類型**]，選取 [ **unicode**]。

1. 儲存您的變更。

1. 向 Azure Logic Apps 註冊您的新**程式識別碼**。

1. 若要測試您的連線，請在 SAP 介面的新**RFC 目的地**底下，選取 [連線**測試**]。

#### <a name="create-abap-connection"></a>建立 ABAP 連接

1. 若要開啟**RFC**連線設定的設定，請在您的 SAP 介面中使用**sm59*** Transaction 程式碼 (T-code) 與 **/n**前置詞。

1. 選取 [ **ABAP 連接**] [  >  **建立**]。

1. 針對 [ **RFC 目的地**]，輸入[測試 SAP 系統](#create-rfc-destination)的識別碼。

1. 儲存您的變更。

1. 若要測試您的連接，請選取 [**連接測試**]。

#### <a name="create-receiver-port"></a>建立接收者埠

1. 若要在 [ **IDOC 處理**] 設定中開啟埠，請在您的 SAP 介面中使用**we21**的交易程式碼，並以 **/n**前置詞 (T-code) 。

1. 選取**Ports**[  >  **交易式 RFC**  >  **建立**] 埠。

1. 在開啟的 [設定] 方塊中，選取 [**自有埠名稱**]。 針對您的測試埠，輸入**名稱**。 儲存您的變更。

1. 在新接收器埠的 [設定] 中，針對 [ **rfc 目的地**] 輸入[測試 rfc 目的地](#create-rfc-destination)的識別碼。

1. 儲存您的變更。

#### <a name="create-sender-port"></a>建立寄件者埠

1.  若要在 [ **IDOC 處理**] 設定中開啟埠，請在您的 SAP 介面中使用**we21**的交易程式碼，並以 **/n**前置詞 (T-code) 。

1. 選取**Ports**[  >  **交易式 RFC**  >  **建立**] 埠。

1. 在開啟的 [設定] 方塊中，選取 [**自有埠名稱**]。 針對您的測試埠，輸入以**SAP**開頭的**名稱**。 所有寄件者埠名稱都必須以**SAP**字母開頭，例如**SAPTEST**。 儲存您的變更。

1. 在新寄件者埠的 [設定] 中，針對 [ **RFC 目的地**] 輸入[ABAP](#create-abap-connection)連線的識別碼。

1. 儲存您的變更。

#### <a name="create-logical-system-partner"></a>建立邏輯系統夥伴

1. 若要開啟**變更視圖的「邏輯系統」：總覽**設定，請在您的 SAP 介面中使用**bd54**交易程式碼， (T-c # 程式碼) 。

1. 接受出現的警告訊息：**注意：資料表是跨用戶端**

1. 在顯示現有邏輯系統的清單上方，選取 [**新增專案**]。

1. 針對您的新邏輯系統，輸入**Log.System**識別碼和簡短**名稱**描述。 儲存您的變更。

1. 出現**工作臺的提示**時，請提供描述來建立新的要求; 或者，如果您已經建立要求，請略過此步驟。

1. 建立工作臺要求之後，請將該要求連結到資料表更新要求。 若要確認您的資料表已更新，請儲存您的變更。

#### <a name="create-partner-profiles"></a>建立夥伴設定檔

針對生產環境，您必須建立兩個夥伴設定檔。 第一個設定檔適用于傳送者，也就是您的組織和 SAP 系統。 第二個設定檔適用于接收者，也就是您的邏輯應用程式。

1. 若要開啟 [**合作夥伴設定檔**] 設定，請在您的 SAP 介面中使用**we20**的交易程式碼 (T-code) 與 **/n**前置詞。

1. 在 [**夥伴設定檔**] 底下，選取 [**夥伴類型 LS**  >  **建立**]。

1. 使用下列設定建立新的夥伴設定檔：

    * 針對 [**夥伴編號**]，輸入[您的邏輯系統合作夥伴識別碼](#create-logical-system-partner)。

    * 適用于**Partn。輸入** **LS**。

    * 針對 [**代理程式**]，輸入當您註冊 Azure Logic Apps 或其他非 SAP 系統的程式識別碼時，所要使用的 SAP 使用者帳戶識別碼。

1. 儲存您的變更。 如果您尚未[建立邏輯系統合作夥伴](#create-logical-system-partner)，您會收到錯誤，請**輸入有效的合作夥伴號碼**。

1. 在夥伴設定檔的設定中，于 [**輸出 parmtrs**] 底下，選取 [**建立輸出參數**]。

1. 使用下列設定建立新的輸出參數：

    * 輸入您的**訊息類型**，例如**CREMAS**。

    * 輸入您[的接收者埠識別碼](#create-receiver-port)。

    * 輸入 Pack 的 IDoc 大小 **。大小**。 或者，若要[從 SAP 一次傳送一個 idoc](#receive-idoc-packets-from-sap)，請選取 [**立即傳遞 IDoc**]。

1. 儲存您的變更。

#### <a name="test-sending-messages"></a>測試傳送訊息

1. 若要開啟**用於 IDoc 處理設定的測試控管**，請在您的 SAP 介面中使用**we19**的交易程式碼， (T 程式碼) 與 **/n**前置詞。

1. 在 [**測試的範本**] 底下，選取 [ **Via 訊息類型**]，然後輸入您的訊息類型，例如**CREMAS**。 選取 [建立]。

1. 選取 [**繼續**] 來確認 [ **IDoc 類型？** ] 訊息。

1. 選取 [ **EDIDC** ] 節點。 輸入您的收件者和傳送者埠的適當值。 選取 \[繼續\]。

1. 選取 [**標準輸出處理**]。

1. 若要開始輸出 IDoc 處理，請選取 [**繼續**]。 處理完成時，會顯示 [**傳送到 SAP 系統] 或 [外部程式**] 訊息的 IDoc。

1.  若要檢查處理錯誤，請使用**sm58**的交易程式碼 (T-code) 與 **/n**前置詞。

## <a name="receive-idoc-packets-from-sap"></a>從 SAP 接收 IDoc 封包

您可以設定 SAP 在封包（也就是 Idoc 的批次或群組）[中傳送 idoc](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html)。 若要接收 IDoc 封包，SAP 連接器（尤其是觸發程式）不需要額外的設定。 不過，若要在觸發程式收到封包之後處理 IDoc 封包中的每個專案，則需要執行一些額外的步驟，將封包分割成個別的 Idoc。

以下範例示範如何使用函[ `xpath()` 式，從](./workflow-definition-language-functions-reference.md#xpath)封包中解壓縮個別的 idoc：

1. 開始之前，您需要有一個具有 SAP 觸發程式的邏輯應用程式。 如果您還沒有此邏輯應用程式，請遵循本主題中的先前步驟來[設定具有 SAP 觸發程式的邏輯應用程式](#receive-from-sap)。

   例如：

   ![將 SAP 觸發程式新增至邏輯應用程式](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. 從您的邏輯應用程式從 SAP 接收的 XML IDoc 取得根命名空間。 若要從 XML 檔解壓縮此命名空間，請新增一個步驟來建立本機字串變數，並使用運算式來儲存該命名空間 `xpath()` ：

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![從 IDoc 取得根命名空間](./media/logic-apps-using-sap-connector/get-namespace.png)

1. 若要將個別 IDoc 解壓縮，請新增一個步驟來建立陣列變數，並使用另一個運算式來儲存 IDoc 集合 `xpath()` ：

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![取得專案的陣列](./media/logic-apps-using-sap-connector/get-array.png)

   陣列變數會透過列舉集合，讓邏輯應用程式可個別處理每個 IDoc。 在此範例中，邏輯應用程式會使用迴圈，將每個 IDoc 傳輸至 SFTP 伺服器：

   ![將 IDoc 傳送至 SFTP 伺服器](./media/logic-apps-using-sap-connector/loop-batch.png)

   每個 IDoc 都必須包含根命名空間，這是在將 `<Receive></Receive` IDoc 傳送到下游應用程式之前，或在此情況下，將檔案內容包裝在元素中的原因，以及根命名空間。

當您建立新的邏輯應用程式時，您可以在邏輯應用程式設計工具中選取此範本，以使用此模式的快速入門範本。

![選取批次邏輯應用程式範本](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>在 SAP 中產生成品的結構描述

此範例使用您可透過 HTTP 要求觸發的邏輯應用程式。 為了產生指定 IDoc 和 BAPI 的架構，SAP 動作**產生架構**會將要求傳送至 sap 系統。

這個 SAP 動作會傳回[xml 架構](#sample-xml-schemas)，而不是 xml 檔本身的內容或資料。 系統會使用 Azure Resource Manager 連接器，將回應中傳回的架構上傳至整合帳戶。 架構包含下列元件：

* 要求訊息的結構。 使用此資訊來形成 BAPI `get` 清單。
* 回應訊息的結構。 使用此資訊來剖析回應。 

若要傳送要求訊息，請使用一般 SAP 動作 [**傳送訊息至 SAP**] 或 [目標**呼叫 BAPI**動作]。

### <a name="sample-xml-schemas"></a>範例 XML 架構

如果您想要瞭解如何產生 XML 架構以用於建立範例檔，請參閱下列範例。 這些範例會示範如何使用許多類型的裝載，包括：

* [RFC 要求](#xml-samples-for-rfc-requests)
* [BAPI 要求](#xml-samples-for-bapi-requests)
* [IDoc 要求](#xml-samples-for-idoc-requests)
* 簡單或複雜的 XML 架構資料類型
* 資料表參數
* 選擇性 XML 行為

您可以使用選擇性的 XML 初構來開始 XML 架構。 SAP 連接器適用于或不含 XML 初構。

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>RFC 要求的 XML 範例

下列範例是基本的 RFC 呼叫。 RFC 名稱是 `STFC_CONNECTION` 。 此要求會使用預設命名空間 `xmlns=` ，不過，您可以指派和使用命名空間別名，例如 `xmmlns:exampleAlias=` 。 命名空間值是 SAP for Microsoft 服務中所有 Rfc 的命名空間。 在要求中有一個簡單的輸入參數 `<REQUTEXT>` 。

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

下列範例是具有 table 參數的 RFC 呼叫。 這個範例呼叫和其測試 Rfc 群組可作為所有 SAP 系統的一部分。 資料表參數的名稱為 `TCPICDAT` 。 資料表行類型為 `ABAPTEXT` ，而且此元素會針對資料表中的每個資料列重複。 這個範例包含一行，稱為 `LINE` 。 具有 table 參數的要求可以包含任意數目的欄位，其中數位是 (*n*) 的正整數。 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

下列範例是具有具有匿名欄位之 table 參數的 RFC 呼叫。 當欄位未指派任何名稱時，就是匿名欄位。 複雜類型是在不同的命名空間下宣告，其中宣告會為目前節點及其所有子專案設定新的預設值。 此範例會使用十六進位碼 `x002F` 做為符號的 escape 字元 */* ，因為此符號會保留在 SAP 功能變數名稱中。

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

下列範例包含命名空間的前置詞。 您可以一次宣告所有前置詞，也可以將任何數目的前置詞宣告為節點的屬性。 RFC 命名空間別名 `ns0` 會當做基本類型的根和參數使用。

> [!NOTE]
> 針對具有別名的 RFC 類型，在不同的命名空間下宣告複雜類型， `ns3` 而不是使用別名做為一般 RFC 命名空間 `ns0` 。

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>BAPI 要求的 XML 範例

> [!TIP]
> 如果您使用 Logic Apps 設計工具來編輯 BAPI 要求，可以使用下列搜尋功能： 
> 
> * 在設計工具中選取物件，以查看可用方法的下拉式功能表。
> * 使用 BAPI API 呼叫所提供的可搜尋清單，依據關鍵字來篩選商業物件類型。

> [!NOTE]
> SAP 會將商務物件提供給外部系統，藉由描述它們以回應 RFC `RPY_BOR_TREE_INIT` ，這會 Logic Apps 沒有輸入篩選器的問題。 Logic Apps 檢查輸出資料表 `BOR_TREE` 。 `SHORT_TEXT`欄位用於商務物件的名稱。 不是由 SAP 在輸出資料表中傳回的商務物件，Logic Apps 無法存取。
> 如果您使用自訂商務物件，您必須確定在 SAP 中發佈和發行這些商務物件。 否則，SAP 不會在輸出資料表中列出您的自訂商務物件 `BOR_TREE` 。 在您從 SAP 公開商務物件之前，您無法在 Logic Apps 中存取自訂商務物件。 

下列範例會使用 BAPI 方法來取得銀行清單 `GETLIST` 。 這個範例包含 bank 的商務物件 `BUS1011` 。 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

下列範例會使用方法來建立 bank 物件 `CREATE` 。 這個範例會使用與上一個範例相同的商務物件 `BUS1011` 。 當您使用 `CREATE` 方法來建立銀行時，請務必認可您的變更，因為預設不會認可此方法。

> [!TIP]
> 請確定您的 XML 檔遵循 SAP 系統中設定的任何驗證規則。 例如，在此範例檔中， () 的銀行金鑰 `<BANK_KEY>` 必須是美國的銀行路由號碼，也稱為 ABA 號碼。

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

下列範例會使用銀行路線編號（的值）來取得銀行的詳細資料 `<BANK_KEY>` 。 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>IDoc 要求的 XML 範例

若要產生一般的 SAP IDoc XML 架構，請使用**Sap 登**入應用程式和 T 程式碼 `WE-60` 。 透過 GUI 存取 SAP 檔，並針對您的 IDoc 類型和延伸模組產生 XSD 格式的 XML 架構。 如需一般 SAP 格式和承載及其內建對話方塊的說明，請參閱[SAP 檔](https://help.sap.com/viewer/index)。

這個範例會宣告根節點與命名空間。 範例程式碼中的 URI 會宣告 `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` 下列設定：

* `/IDoc`是所有 Idoc 的根附注
* `/3`是一般區段定義的記錄類型版本
* `/ORDERS05`是 IDoc 類型
* `//`是空的區段，因為沒有 IDoc 延伸模組
* `/700`是 SAP 版本
* `/Send`這是將資訊傳送至 SAP 的動作

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

您可以重複此 `idocData` 節點，在單一呼叫中傳送一批 idoc。 在下列範例中，有一個控制項記錄 `EDI_DC40` 和多個資料記錄。

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

下列範例是使用前置詞的範例 IDoc 控制項記錄 `EDI_DC` 。 您必須更新值，以符合您的 SAP 安裝和 IDoc 類型。 例如，您的 IDoc 用戶端程式代碼可能不是 `800` 。 請洽詢您的 SAP 小組，以確定您使用的是 SAP 安裝的正確值。

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

下列範例是具有一般區段的範例資料記錄。 這個範例會使用 SAP 日期格式。 強式類型的檔可以使用原生 XML 日期格式，例如 `2020-12-31 23:59:59` 。

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

下列範例是具有分組區段的資料記錄。 記錄包括群組父節點、 `E2EDKT1002GRP` 和多個子節點，包括 `E2EDKT1002` 和 `E2EDKT2001` 。 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

建議的方法是建立要與 tRFC 搭配使用的 IDoc 識別碼。 您可以 `tid` 使用 SAP 連接器 API 中的[傳送 IDoc](/connectors/sap/#send-idoc)作業來設定此交易識別碼。

下列範例是設定交易識別碼或的替代方法 `tid` 。 在此範例中，最後一個資料記錄區段節點和 IDoc 資料節點已關閉。 然後，GUID `guid` 會用來做為偵測重複專案的 tRFC 識別碼。 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

### <a name="add-an-http-request-trigger"></a>新增 HTTP 要求觸發程式

1. 在 Azure 入口網站中，建立空白的邏輯應用程式，以開啟邏輯應用程式設計工具。

1. 在搜尋方塊中，輸入 `http request` 作為篩選條件。 從**觸發**程式清單中，選取 [**收到 HTTP 要求時**]。

   ![新增 HTTP 要求觸發程序](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. 現在請儲存邏輯應用程式，即可產生邏輯應用程式的端點 URL。
在設計工具的工具列上，選取 [儲存]  。

   端點 URL 現在會出現在您的觸發程序中，例如：

   ![產生端點的 URL](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>新增 SAP 動作以產生架構

1. 在邏輯應用程式設計工具的觸發程式底下，選取 [**新增步驟**]。

   ![將新步驟新增至邏輯應用程式](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. 在搜尋方塊中，輸入 `sap` 作為篩選條件。 從 [**動作**] 清單中，選取 [**產生架構**]。
  
   ![將 [產生架構] 動作新增至邏輯應用程式](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   或者，您可以選取 [**企業**] 索引標籤，然後選取 [SAP] 動作。

   ![從 [企業] 索引標籤選取 SAP 傳送動作](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 如果您的連線已存在，請繼續進行下一個步驟，讓您可以設定 SAP 動作。 不過，如果系統提示您輸入連線詳細資料，請提供資訊，讓您現在可以建立與內部部署 SAP 伺服器的連線。

   1. 提供連接的名稱。

   1. 在 [**資料閘道**] 區段的 [**訂**用帳戶] 底下，先針對您在資料閘道安裝的 Azure 入口網站中所建立的資料閘道資源選取 Azure 訂用帳戶。 
   
   1. 在 [連線**閘道**] 底下，選取您在 Azure 中的資料閘道資源。

   1. 繼續提供連接的相關資訊。 針對 [**登入類型**] 屬性，請根據此屬性是否設為 [**應用程式伺服器**] 或 [**群組**] 來遵循此步驟：
   
      * 若為**應用程式伺服器**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 應用程式伺服器連線](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * 若為**群組**，則需要這些屬性（通常會顯示為選擇性）：

        ![建立 SAP 訊息伺服器連線](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      根據預設，強式類型是用來透過對架構執行 XML 驗證來檢查是否有不正確值。 此行為可協助您稍早偵測到問題。 [**安全類型**] 選項可用於回溯相容性，而且只會檢查字串長度。 深入瞭解[安全輸入選項](#safe-typing)。

   1. 完成之後，選取 [建立]。

      Logic Apps 會設定並測試您的連線，以確保連線正常運作。

1. 針對您要產生結構描述的成品提供其路徑。

   您可以從檔案選擇器選取 SAP 動作：

   ![選取 SAP 動作](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   或者，您也可以手動輸入動作：

   ![手動輸入 SAP 動作](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   若要針對多個成品產生結構描述，請提供每個成品的 SAP 動作詳細資料，例如：

   ![選取 [新增項目]](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![顯示兩個項目](media/logic-apps-using-sap-connector/schema-generator-example.png)

   如需 SAP 動作的詳細資訊，請參閱[IDoc 作業的訊息架構](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)。

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 在設計工具工具列上，選取 [**執行**] 以觸發邏輯應用程式的執行。

1. 開啟執行，並檢查 [**產生架構**] 動作的輸出。

   輸出中會顯示針對所指定訊息清單產生的結構描述。

### <a name="upload-schemas-to-an-integration-account"></a>將架構上傳至整合帳戶

(選擇性) 您可以在存放庫 (例如，Blob、儲存體或企業整合帳戶) 下載或儲存產生的結構描述。 企業整合帳戶搭配其他 XML 動作可提供一流的體驗，因此這個範例會說明如何使用 Azure Resource Manager 連接器，將相同邏輯應用程式的結構描述上傳到企業整合帳戶。

1. 在邏輯應用程式設計工具的觸發程式底下，選取 [**新增步驟**]。

1. 在搜尋方塊中，輸入 `Resource Manager` 作為篩選條件。 選取 [**建立或更新資源**]。

   ![選取 Azure Resource Manager 動作](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. 輸入動作的詳細資料，包括您的 Azure 訂用帳戶、Azure 資源群組和整合帳戶。 若要將 SAP 權杖新增至欄位，請在這些欄位的方塊內按一下，然後從顯示的動態內容清單中選取。

   1. 開啟 [**加入新的參數**] 清單，然後選取 [**位置**] 和 [**屬性**] 欄位。

   1. 提供這些新欄位的詳細資料，如下列範例所示。

      ![輸入 Azure Resource Manager 動作的詳細資料](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP 的**產生結構描述**動作會以集合的形式產生結構描述，讓設計工具可以在動作中自動新增 **For each** 迴圈。 以下舉例說明此動作的顯示方式：

   ![Azure Resource Manager 動作與 "for each" 迴圈](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > 結構描述會使用 base64 編碼格式。 若要將結構描述上傳到企業整合帳戶，必須先使用 `base64ToString()` 函式將結構描述解碼。 以下舉例說明 `"properties"` 元素的程式碼：
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. 儲存您的邏輯應用程式。 在設計工具的工具列上，選取 [儲存]  。

### <a name="test-your-logic-app"></a>測試應用程式邏輯

1. 在設計工具工具列上，選取 [**執行**] 以手動觸發邏輯應用程式。

1. 成功執行之後，請移至整合帳戶，並檢查產生的架構是否存在。

<a name="secure-network-communications"></a>

## <a name="enable-secure-network-communications"></a>啟用安全網路通訊

開始之前，請確定您符合先前所列的[必要條件](#pre-reqs)，只有在您使用資料閘道，而且邏輯應用程式在多租使用者 Azure 中執行時才適用：

* 請確定內部部署資料閘道安裝在與您的 SAP 系統位於相同網路中的電腦上。

* 針對單一登入 (SSO) ，資料閘道會以對應至 SAP 使用者的使用者身分執行。

* 提供額外安全性功能的 SNC 程式庫會安裝在與資料閘道相同的電腦上。 其中一些範例包括[sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、KERBEROS 和 NTLM。

   若要針對您在 SAP 系統中的要求啟用 SNC，請選取 SAP 連接中的 [**使用 SNC** ] 核取方塊，並提供下列屬性：

   ![在連接中設定 SAP SNC](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | 屬性 | 描述 |
   |----------| ------------|
   | **SNC 程式庫路徑** | 相對於 NCo 安裝位置或絕對路徑的 SNC 程式庫名稱或路徑。 範例為 `sapsnc.dll` 或 `.\security\sapsnc.dll` 或 `c:\security\sapsnc.dll` 。 |
   | **SNC SSO** | 當您透過 SNC 連接時，SNC 身分識別通常用來驗證呼叫端。 另一個選項是覆寫，讓使用者和密碼資訊可以用來驗證呼叫者，但這一行仍然是加密的。 |
   | **SNC 我的姓名** | 在大部分的情況下，可以省略這個屬性。 已安裝的 SNC 解決方案通常會知道自己的 SNC 名稱。 僅針對支援多個身分識別的解決方案，您可能需要指定要用於此特定目的地或伺服器的身分識別。 |
   | **SNC 夥伴名稱** | 後端 SNC 的名稱。 |
   | **SNC 保護品質** | 此特定目的地或伺服器的 SNC 通訊所使用的服務品質。 預設值是由後端系統所定義。 最大值是由用於 SNC 的安全性產品所定義。 |
   |||

   > [!NOTE]
   > 請勿在具有資料閘道和 SNC 程式庫的電腦上設定環境變數 SNC_LIB 和 SNC_LIB_64。 如果設定，則會優先于透過連接器傳遞的 SNC 程式庫值。

<a name="safe-typing"></a>

## <a name="safe-typing"></a>安全類型

根據預設，當您建立 SAP 連接時，會使用強型別來檢查是否有不正確值，方法是對架構執行 XML 驗證。 此行為可協助您稍早偵測到問題。 [**安全類型**] 選項可用於回溯相容性，而且只會檢查字串長度。 如果您選擇**安全**型別，SAP 中的 dat 型別和 TIMS 型別會被視為字串，而不是它們的 XML 對等專案， `xs:date` 以及 `xs:time` `xmlns:xs="http://www.w3.org/2001/XMLSchema"` 。 安全類型會影響所有架構產生的行為、「已傳送」裝載和「已接收」回應的傳送訊息，以及觸發程式。 

當使用強型別時 (不會) 啟用**安全**類型，架構會將 DAT 和 TIMS 類型對應到更簡單的 XML 類型：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

當您使用強型別傳送訊息時，DAT 和 TIMS 回應會符合相符的 XML 類型格式：

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

啟用**安全類型**時，架構只會將 DAT 和 TIMS 類型對應至長度限制為的 XML 字串欄位，例如：

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

在啟用**安全輸入**的情況下傳送訊息時，DAT 和 TIMS 回應會如下列範例所示：

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>進階案例

### <a name="change-language-headers"></a>變更語言標頭

當您從 Logic Apps 連線到 SAP 時，連接的預設語言是英文。 您可以使用[標準 HTTP 標頭 `Accept-Language` ](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4)與您的輸入要求，為您的連接設定語言。

> [!TIP]
> 大部分的網頁瀏覽器會 `Accept-Language` 根據使用者的設定來新增標頭。 當您在 Logic Apps 設計工具中建立新的 SAP 連接時，web 瀏覽器會套用這個標頭。 如果您不想要以網頁瀏覽器的慣用語言建立 SAP 連線，請將您的網頁瀏覽器設定更新為使用您慣用的語言，或使用 Azure Resource Manager 來建立 SAP 連接，而不是 Logic Apps 的設計工具。 

例如，您可以 `Accept-Language` 使用**HTTP 要求**觸發程式，將具有標頭的要求傳送至邏輯應用程式。 邏輯應用程式中的所有動作都會接收標頭。 然後，SAP 會在其系統訊息中使用指定的語言，例如 BAPI 錯誤訊息。

邏輯應用程式的 SAP 連接參數沒有 language 屬性。 因此，如果您使用 `Accept-Language` 標頭，您可能會收到下列錯誤：**請檢查您的帳戶資訊及（或）許可權，然後再試一次。** 在此情況下，請改為檢查 SAP 元件的錯誤記錄檔。 此錯誤實際上發生在使用標頭的 SAP 元件中，因此您可能會收到下列其中一則錯誤訊息：

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>明確確認交易

當您從 Logic Apps 將交易傳送至 SAP 時，此交換會以 SAP 檔、[交易式 RFC 伺服器程式](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)中所述的兩個步驟進行。 根據預設，[**傳送至 SAP** ] 動作會在單一呼叫中處理函式傳送和交易確認的步驟。 SAP 連接器可讓您選擇分離這些步驟。 您可以傳送 IDoc，而不是自動確認交易，您可以使用明確的「**確認交易識別碼**」動作。

當您不想要在 SAP 中重複交易時，這項將交易識別碼解除耦合的功能會很有用，例如，在因網路問題而造成失敗的情況下。 藉由單獨確認交易識別碼，交易只會在您的 SAP 系統中完成一次。

以下是顯示此模式的範例：

1. 建立空白邏輯應用程式，並新增 HTTP 觸發程式。

1. 從 SAP 連接器新增 [**傳送 IDOC** ] 動作。 提供您傳送至 SAP 系統的 IDoc 詳細資料。

1. 若要在個別步驟中明確確認交易識別碼，請在 [**確認 TID** ] 欄位中選取 [**否**]。 針對 [選擇性**交易識別碼 GUID** ] 欄位，您可以手動指定值，或讓連接器自動產生，並在 [傳送 IDOC] 動作的回應中傳回此 GUID。

   ![傳送 IDOC 動作屬性](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. 若要明確確認交易識別碼，請新增 [**確認交易識別碼**] 動作，並務必[避免將重複的 IDOC 傳送至 SAP](#avoid-sending-duplicate-idocs)。 按一下 [**交易識別碼**] 方塊內部，讓動態內容清單出現。 從該清單中，選取從 [**傳送 IDOC** ] 動作傳回的 [**交易識別碼**] 值。

   ![確認交易識別碼動作](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   執行此步驟之後，在 SAP 連接器端和 SAP 系統端上，目前的交易都會標示為「已完成」。

#### <a name="avoid-sending-duplicate-idocs"></a>避免傳送重複的 Idoc

如果您遇到從邏輯應用程式傳送至 SAP 之重複 Idoc 的問題，請遵循下列步驟來建立字串變數，做為您的 IDoc 交易識別碼。 當發生暫時性中斷、網路問題或遺失通知等問題時，建立此交易識別碼有助於防止重複的網路傳輸。

> [!NOTE]
> SAP 系統預設會在指定的時間或24小時後忘記交易識別碼。 因此，如果識別碼或 GUID 不明，SAP 永遠不會無法確認交易識別碼。
> 如果交易識別碼的確認失敗，此失敗表示與 SAP 系統的通訊失敗，然後 SAP 才能夠確認確認。

1. 在 Logic Apps 設計工具中，將 [**初始化變數**] 動作新增至邏輯應用程式。 
1. 在 [動作] [**初始化變數**] 的編輯器中，設定下列設定。 然後儲存您的變更。
    1. 針對 [**名稱**]，輸入變數的名稱。 例如： `IDOCtransferID` 。
    2. 針對 [**類型**]，選取 [**字串**] 做為變數類型。
    3. 針對 [**值**]，選取 [**輸入初始值**] 文字方塊，以開啟 [動態內容] 功能表。 選取 [**運算式**] 索引標籤。在函式清單中，輸入函數 `guid()` 。 然後，選取 **[確定]** 以儲存變更。 [**值**] 欄位現在會設定為函式，此函式會 `guid()` 產生 GUID。
1. 在 [**初始化變數**] 動作之後，新增 [動作**傳送 IDOC**]。
1. 在 [動作**傳送 IDOC**] 的 [編輯器] 中，設定下列設定。 然後儲存您的變更。
    1. 針對 [ **IDOC 類型**]，選取您的訊息類型，然後針對 [**輸入 IDOC 訊息**]，指定您的訊息。
    1. 針對**sap 發行版本**，請選取您 SAP 設定的值。
    1. 針對 [**記錄類型版本**]，選取您 SAP 設定的值。
    1. 針對 [**確認 TID**]，選取 [**否**]。
    1. 選取 [**加入新的參數清單**  >  **交易識別碼 GUID**]。 選取文字方塊以開啟 [動態內容] 功能表。 在 [**變數**] 索引標籤下，選取您所建立之變數的名稱。 例如： `IDOCtransferID` 。
1. 在動作**傳送 IDOC**的標題列上，選取 [ **...**  >  ]**設定**。 針對 [**重試原則**]，選取 [**無**] [  >  **完成**]。
1. 在動作**傳送 IDOC**之後，新增動作 [**確認交易識別碼**]。
1. 在 [**確認交易識別碼**] 動作的編輯器中，設定下列設定。 然後儲存您的變更。
    1. 針對 [**交易識別碼**]，再次輸入變數的名稱。 例如： `IDOCtransferID` 。

## <a name="known-issues-and-limitations"></a>已知的問題及限制

以下是受控 (非 ISE) SAP 連接器的目前已知問題和限制：

* SAP 觸發程序不支援資料閘道叢集。 在某些容錯移轉案例中，與 SAP 系統通訊的資料閘道節點可能會與使用中節點不同，這會導致非預期的行為。 針對傳送案例，支援資料閘道叢集。

* SAP 連接器目前不支援 SAP 路由器字串。 內部部署資料閘道必須和所要連線的 SAP 系統存在於相同 LAN 內。

## <a name="connector-reference"></a>連接器參考

如需此連接器的更多技術詳細資料，例如連接器的 Swagger 檔案所描述的觸發程式、動作和限制，請參閱[連接器的參考頁面](/connectors/sap/)。

> [!NOTE]
> 對於[整合服務環境中 (ISE) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)的邏輯應用程式，此連接器的 ise 標記版本會使用[ISE 訊息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>後續步驟

* 從 Azure Logic Apps[連接到內部部署系統](../logic-apps/logic-apps-gateway-connection.md)。
* 瞭解如何驗證、轉換和使用[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)的其他訊息作業。
* 深入瞭解其他[Logic Apps 連接器](../connectors/apis-list.md)。

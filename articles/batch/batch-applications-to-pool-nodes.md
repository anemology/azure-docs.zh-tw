---
title: 將應用程式和資料複製到集區節點
description: 了解如何將應用程式和資料複製到集區節點。
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: e21b8551fb62c4335910fd05bb9590eaf6f7e35a
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954888"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>將應用程式和資料複製到集區節點

Azure Batch 支援數種將資料和應用程式放入計算節點的方式，讓資料和應用程式可供工作使用。 資料和應用程式可能需要用來執行整個作業，因此必須安裝在每個節點上。 某些資料和應用程式可能只會用在特定的工作上，或必須針對作業安裝，但不需要安裝在每個節點上。 Batch 具備適用於每種案例的工具。

- **集區啟動工作資源檔**：適用於需要安裝在集區中每個節點上的應用程式或資料。 使用此方法搭配應用程式封裝或啟動工作的資源檔集合，以便執行安裝命令。  

範例： 
- 使用啟動工作命令列移動或安裝應用程式

- 指定 Azure 儲存體帳戶中的特定檔案或容器清單。 如需詳細資訊，請參閱 [REST 文件中的 add#resourcefile](/rest/api/batchservice/pool/add#resourcefile)

- 在集區上執行的每個作業都會執行 MyApplication.exe，因此必須先與 MyApplication.msi 一併安裝。 如果您使用這項機制，則必須將啟動工作的 **wait for success** 屬性設定為 **True**。 如需詳細資訊，請參閱 [REST 文件中的 add#starttask](/rest/api/batchservice/pool/add#starttask)。

- 集區中的**應用程式封裝參考**：適用於需要安裝在集區中每個節點上的應用程式或資料。 沒有與應用程式封裝相關聯的安裝命令，但是您可以使用啟動工作來執行任何安裝命令。 如果您的應用程式不需要安裝，或包含大量的檔案，您可以使用這個方法。 應用程式封裝非常適合大量檔案，因為其會將大量的檔案參考合併成一個小型承載。 如果您嘗試將超過 100 個不同的資源檔包含在一個工作中，則 Batch 服務可能會針對單一工作產生內部系統限制。 此外，如果您有嚴格的版本控制需求，而且可能有許多不同版本的相同應用程式需要選擇時，請使用應用程式封裝。 如需詳細資訊，請參閱[使用 Batch 應用程式封裝將應用程式部署至計算節點](./batch-application-packages.md)。

- **作業準備工作資源檔**：針對必須安裝才能執行作業，但不需要安裝在整個集區的應用程式或資料。 例如：如果您的集區有許多不同類型的作業，而且只有一種作業類型需要 MyApplication.msi 才能執行，則將安裝步驟放入作業準備工作是合理的做法。 如需關於作業準備工作的詳細資訊，請參閱[在 Batch 計算節點上執行作業準備和作業發行工作](./batch-job-prep-release.md)。

- **工作資源檔**：適用於應用程式或資料僅與個別工作相關的情況。 例如：您有五個工作，每個工作都會處理不同的檔案，然後將輸出寫入至 Blob 儲存體。  在此情況下，應該在**工作資源檔**集合上指定輸入檔，因為每個工作都有自己的輸入檔。

## <a name="determine-the-scope-required-of-a-file"></a>判斷檔案所需的範圍

您需要判斷檔案的範圍 - 是集區、作業或工作所需的檔案。 範圍設定為集區的檔案應該使用集區應用程式封裝或啟動工作。 以作業為範圍的檔案應該使用作業準備工作。 以集區或作業層級為範圍之檔案的良好範例為應用程式。 以工作為範圍的檔案應該使用工作資源檔。

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>將資料放入 Batch 計算節點的其他方法

還有其他方法可以將資料放入未正式整合至 Batch REST API 的 Batch 計算節點。 由於您可以控制 Azure Batch 的節點，而且可以執行自訂可執行檔，只要 Batch 節點與目標連線，而且您擁有該來源至 Azure Batch 節點的認證，就能夠從任何數目的自訂來源提取資料。 一些常見的範例包括：

- 從 SQL 下載資料
- 從其他 Web 服務/自訂位置下載資料
- 對應網路共用

### <a name="azure-storage"></a>Azure 儲存體

Blob 儲存體具有下載可擴縮性目標。 Azure 儲存體檔案共用的可擴縮性目標與單一 Blob 相同。 大小會影響您所需的節點和集區數目。


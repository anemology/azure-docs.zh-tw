---
title: Azure Data Lake Storage Gen2 中可用的 Blob 儲存體功能 | Microsoft Docs
description: 了解您可以搭配 Azure Data Lake Storage Gen2 使用的 Blob 儲存體功能
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 0d915c0b293e9f3deadbfb2a5fb0ff7f379e1717
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053468"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 中可用的 Blob 儲存體功能

Blob 儲存體功能 (例如[診斷記錄](../common/storage-analytics-logging.md)、[存取層](storage-blob-storage-tiers.md)和  [Blob 儲存體生命週期管理原則](storage-lifecycle-management-concepts.md)) 現已可與具有階層命名空間的帳戶搭配運作。 因此，您可以在 Blob 儲存體帳戶上啟用階層式命名空間，而不會失去這些功能的存取權。

下表列出您可以搭配 Azure Data Lake Storage Gen2 使用的 Blob 儲存體功能。 當支援繼續擴充時，出現在這些資料表中的項目會隨著時間而改變。 若要深入了解與功能預覽狀態相關聯的特定問題，請參閱[已知問題](data-lake-storage-known-issues.md)文章。

## <a name="supported-blob-storage-features"></a>支援的 Blob 儲存體功能

> [!NOTE]
> 支援層級僅指 Data Lake Storage Gen2 支援功能的方式。 
>
> 適用於 Data Lake Storage Gen2 的[進階效能 BlockBlobStorage 帳戶](storage-blob-create-account-block-blob.md)目前為公開預覽狀態。 這些帳戶類型的支援層級會顯示在 [BlockBlobStorage (進階)] 資料行中。

|Blob 儲存體功能 |一般用途 V2 |BlockBlobStorage (進階) |相關文章 |
|---------------|-------------------|---|
|經常性存取層|正式推出|不支援|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|非經常性存取層|正式推出|不支援|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|事件|正式推出|預覽|[回應 Blob 儲存體事件](storage-blob-event-overview.md)|
|計量 (傳統)|正式推出|不支援|[Azure 儲存體分析計量 (傳統)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure 監視器中的計量|正式推出|預覽|[Azure 監視器中的 Azure 儲存體計量](../common/storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 儲存體 PowerShell 命令|正式推出|預覽|[快速入門：使用 PowerShell 上傳、下載及列出 Blob](storage-quickstart-blobs-powershell.md)|
|Blob 儲存體 Azure CLI 命令|正式推出|預覽|[快速入門：使用 Azure CLI 建立、下載及列出 Blob](storage-quickstart-blobs-cli.md)|
|Blob 儲存體 API|正式推出|預覽|[快速入門：適用於 .NET 的 Azure Blob 儲存體用戶端程式庫 v12](storage-quickstart-blobs-dotnet.md)<br>[快速入門：使用 Java v12 SDK 來管理 Blob](storage-quickstart-blobs-java.md)<br>[快速入門：使用 Python v12 SDK 來管理 Blob](storage-quickstart-blobs-python.md)<br>[快速入門：使用 Node.js 中的 JavaScript v12 SDK 來管理 Blob](storage-quickstart-blobs-nodejs.md)|
|診斷記錄|正式推出|預覽 <div role="complementary" aria-labelledby="diagnostic-logging"><sup>1</sup></div> |[Azure 儲存體分析記錄](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (部分機器翻譯)|
|封存存取層|正式推出|不支援|[Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層](storage-blob-storage-tiers.md)|
|生命週期管理原則|正式推出|尚不支援|[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)|
|登入 Azure 監視器|預覽 |尚不支援|[監視 Azure 儲存體](../common/monitor-storage.md)|
|快照集|預覽|尚不支援|[Blob 快照集](snapshots-overview.md)|
|靜態網站|預覽|尚不支援|[Azure 儲存體中的靜態網站代管](storage-blob-static-website.md)|
|固定儲存體|預覽|尚不支援|[使用不可變儲存體儲存業務關鍵 Blob 資料](storage-blob-immutable-storage.md)|
|生命週期管理原則|預覽|尚不支援|[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)|
|容器虛刪除|預覽|預覽|[ (預覽) 的容器虛刪除](soft-delete-container-overview.md)|
|Blob 虛刪除|尚不支援|尚不支援|[Blob 的虛刪除](storage-blob-soft-delete.md)|
|Blobfuse|預覽|尚不支援|[如何使用 Blobfuse 將 Blob 儲存體掛接為檔案系統](storage-how-to-mount-container-linux.md)|
|變更摘要|尚不支援|尚不支援|[Azure Blob 儲存體中的變更摘要支援](storage-blob-change-feed.md)|
|帳戶容錯移轉|尚不支援|尚不支援|[災害復原和帳戶容錯移轉](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Blob 容器 ACL|尚不支援<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|尚不支援<div role="complementary" aria-labelledby="blob-container-ACL"><sup>2</sup></div>|[Set Container ACL (設定容器 ACL)](https://docs.microsoft.com/rest/api/storageservices/set-container-acl)|
|自訂網域|尚不支援|尚不支援|[將自訂網域對應至 Azure Blob 儲存體端點](storage-custom-domain-name.md)|

<div id="diagnostic-logging"><sup>1</sup>若為 premium 區塊 blob 儲存體帳戶，則無法使用 Azure 入口網站來啟用 (傳統) 的診斷記錄。 使用 PowerShell 加以啟用。</div><br>

<div id="blob-container-ACL"><sup>2</sup>您可以在容器的根資料夾（而不是容器本身）上設定 acl。</div><br>

<div id="preview-form"><sup>3</sup>若要使用快照集、不可變的儲存體或具有 Data Lake Storage Gen2 的靜態網站，您必須完成此<a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>表單</a>以在預覽中註冊。  </div>

## <a name="see-also"></a>另請參閱

- [Azure Data Lake Storage Gen2 的已知問題](data-lake-storage-known-issues.md)
- [支援 Azure Data Lake Storage Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)
- [支援 Azure Data Lake Storage Gen2 的開放原始碼平台](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage 上的多重通訊協定存取](data-lake-storage-multi-protocol-access.md)

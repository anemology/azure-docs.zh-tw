---
title: Azure 備份定價
description: 瞭解如何預估預算 Azure 備份定價的成本。
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: cdb3dc756e1ee7e32453acd7246952c84abebaf7
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035751"
---
# <a name="azure-backup-pricing"></a>Azure 備份定價

若要瞭解 Azure 備份定價，請造訪[Azure 備份定價頁面](https://azure.microsoft.com/pricing/details/backup/)。

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>下載 Azure 備份定價的詳細估計

如果您想要估計預算或成本比較用途的成本，請下載詳細的[Azure 備份定價估計工具](https://aka.ms/AzureBackupCostEstimates)。  

### <a name="what-does-the-estimator-contain"></a>估計工具組含哪些內容？

Azure 備份 cost 估計工具工作表可讓您使用 Azure 備份來估計您想要備份的所有可能工作負載。 這些工作負載包括：

- Azure VM
- 內部部署伺服器
- Azure Vm 中的 SQL
- Azure Vm 中的 SAP Hana
- Azure 檔案共用

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>估計備份 Azure Vm 或內部部署伺服器的成本

若要使用 Azure 備份來估計備份 Azure Vm 或內部部署伺服器的成本，您需要下列參數：

- 您要嘗試備份的 Vm 或內部部署伺服器大小
  - 輸入需要備份的磁片或伺服器的「使用大小」

- 具有該大小的伺服器數目

- 這些伺服器上預期的資料流程失量為何？<br>
  流失指的是資料的變更量。 例如，如果您的 VM 有 200 GB 的資料需要備份，且每日有 10 GB 的 it 變更，則每日變換率為5%。

  - 變換率越高，表示您備份的資料更多

  - 如果您正在執行資料庫，請選擇 [**低**] 或 [**適中**] 檔案伺服器和 [**高**]

  - 如果您知道**流失的%**，可以使用 [**輸入您自己的%** ] 選項

- 選擇備份原則

  - 您預期會保留「每日」備份多久？  (（以天為單位）) 

  - 您希望保留「每週」備份多久？  (周) 

  - 您預期會保留「每月」備份的時間長度嗎？ 幾個月內的 () 

  - 您預期會保留「每年」備份多久？  (年) 

  - 您希望保留「立即還原快照」多久？  (1-5 天) 

    - 此選項可讓您使用儲存在磁片上的快照集，以快速的方式從最久還原為7天。

- **選用**–選擇性磁片備份

  - 如果您在備份 Azure Vm 時使用 [**選擇性磁片備份**] 選項，請選擇 [**排除磁片**] 選項，並輸入從備份中排除的磁片百分比（以大小為單位）。 例如，如果您有一個 VM 連線到每個磁片中使用了 200 GB 的三個磁片，而且您想要將其中兩個磁片從備份中排除，請輸入66.7%。

- **選擇性**–備份儲存體冗余

  - 這表示您的備份資料所進入之儲存體帳戶的冗余。 我們建議使用**GRS**來達到最高的可用性。 由於它可確保備份資料的複本會保留在不同的區域中，因此可協助您符合多個合規性標準。 如果您要備份不需要企業級備份的開發或測試環境，請將 [LRS] 變更為 [ **LRS** ]。 如果您想要瞭解針對備份啟用[跨區域還原](backup-azure-arm-restore-vms.md#cross-region-restore)時的成本，請選取工作表中的 [ **RAGRS** ] 選項。

- **選擇性**–修改地區定價或申請折扣費率

  - 如果您想要檢查不同區域或折扣費率的估價，請針對 [**試用其他地區的估價？** ] 選項選取 **[是]** ，然後輸入您想要執行估計值的費率。

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>估計在 Azure Vm 中備份 SQL server 的成本

若要預估使用 Azure 備份在 Azure Vm 中備份 SQL server 的成本，您需要下列參數：

- 您要嘗試備份的 SQL server 大小

- 具有上述大小的 SQL server 數目

- 您的 SQL server 備份資料預期的壓縮是什麼？

  - 當 SQL 壓縮已**啟用**時，大部分的 Azure 備份客戶都會看到備份資料有80% 的壓縮，相較于 sql server 大小。

  - 如果您預期會看到不同的壓縮，請在此欄位中輸入數位

- 預期的記錄備份大小為何？

  - % 表示每日記錄大小為 SQL server 大小的%

- 這些伺服器上預期的每日資料流程失量為何？

  - 一般而言，資料庫具有「高」變換率

  - 如果您知道**流失的%**，可以使用 [**輸入您自己的%** ] 選項

- 選擇備份原則

  - 備份類型

    - 您可以選擇的最有效原則是每週/每月/每年完整備份的**每日差異**。 Azure 備份也可以透過按一下的方式，從差異還原。

    - 您也可以選擇擁有每日/每週/每月/每年完整備份的原則。 此選項會耗用比第一個選項稍微多一點的儲存空間。

  - 您希望保留「記錄」備份多久？  (（以天為單位）) [7-35]

  - 您預期會保留「每日」備份多久？  (（以天為單位）) 

  - 您希望保留「每週」備份多久？  (周) 

  - 您預期會保留「每月」備份的時間長度嗎？ 幾個月內的 () 

  - 您預期會保留「每年」備份多久？  (年) 

- **選擇性**–備份儲存體冗余

  - 這表示您的備份資料所進入之儲存體帳戶的冗余。 我們建議使用**GRS**來達到最高的可用性。 由於它可確保備份資料的複本會保留在不同的區域中，因此可協助您符合多個合規性標準。 如果您要備份不需要企業級備份的開發或測試環境，請將 [LRS] 變更為 [ **LRS** ]。

- **選擇性**–修改地區定價或申請折扣費率

  - 如果您想要檢查不同區域或折扣費率的估價，請針對 [**試用其他地區的估價？** ] 選項選取 **[是]** ，然後輸入您想要執行估計值的費率。

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>估計在 Azure Vm 中備份 SAP Hana 伺服器的成本

若要使用 Azure 備份估計在 Azure Vm 中執行 SAP Hana 伺服器的成本，您需要下列參數：

- 您嘗試備份之 SAP Hana 資料庫的大小總計。 這應該是每個資料庫的完整備份大小總和，如 SAP Hana 所報告。
- 具有上述大小的 SAP Hana 伺服器數目
- 預期的記錄備份大小為何？
  
  - % 表示平均每日記錄大小，以您在 SAP Hana 伺服器上備份之 SAP Hana 資料庫總大小的% 為百分比
- 這些伺服器上預期的每日資料流程失量為何？
  - % 表示平均每日變換大小為您在 SAP Hana 伺服器上備份之 SAP Hana 資料庫大小總計的%
  - 一般而言，資料庫具有「高」變換率
  - 如果您知道**流失的%**，可以使用 [**輸入您自己的%** ] 選項
- 選擇備份原則
  - 備份類型
    - 您可以選擇的最有效原則是每**周/每月/每年**完整備份的**每日差異**。 Azure 備份也可以透過按一下的方式，從差異還原。
    - 您也可以選擇擁有**每日/每週/每月/每年**完整備份的原則。 此選項會耗用比第一個選項稍微多一點的儲存空間。
  - 您希望保留「記錄」備份多久？  (（以天為單位）) [7-35]
  - 您預期會保留「每日」備份多久？  (（以天為單位）) 
  - 您希望保留「每週」備份多久？  (周) 
  - 您預期會保留「每月」備份的時間長度嗎？ 幾個月內的 () 
  - 您預期會保留「每年」備份多久？  (年) 
- **選擇性**–備份儲存體冗余
  
  - 這表示您的備份資料所進入之儲存體帳戶的冗余。 我們建議使用**GRS**來達到最高的可用性。 由於它可確保備份資料的複本會保留在不同的區域中，因此可協助您符合多個合規性標準。 如果您要備份不需要企業級備份的開發或測試環境，請將 [LRS] 變更為 [ **LRS** ]。
- **選擇性**–修改地區定價或申請折扣費率
  
  - 如果您想要檢查不同區域或折扣費率的估價，請針對 [**試用其他地區的估價？** ] 選項選取 **[是]** ，然後輸入您想要執行估計值的費率。
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>估計備份 Azure 檔案共用的成本

若要使用 Azure 備份所提供以快照集為基礎的[備份解決方案](azure-file-share-backup-overview.md)來估計備份 Azure 檔案共用的成本，您需要下列參數：

- 您想要備份的檔案共用大小 (**GB**) 。

- 如果您想要備份散佈于多個儲存體帳戶的檔案共用，請指定裝載具有上述大小之檔案共用的儲存體帳戶數目。

- 您想要備份的檔案共用上預期的資料流程失量。 <br>變換指的是資料的變更量，而且會直接影響快照儲存體大小。 例如，如果您有要備份 200 GB 資料的檔案共用，而且每天有 10 GB 的 it 變更，則每日變換為5%。
  - 較高的變換表示每日檔案共用內容中的資料變更量很高，因此，增量快照集 (只會) 大小的資料變更進行捕捉。
  - 根據您的檔案共用特性和使用方式，選取 [低 (1% ) ]、[適中] (3% ) 或 [高 (5%] ) 。
  - 如果您知道檔案共用的確切**流失%** ，可以從下拉式選單選取 [**輸入您自己的%** ] 選項。 指定每日、每週、每月和每年流失的% )  (值。

- 儲存體帳戶的類型 (standard 或 premium) ，以及裝載備份檔案共用之儲存體帳戶的儲存體冗余設定。 <br>在 Azure 檔案共用的目前備份解決方案中，快照集會儲存在與備份檔案共用相同的儲存體帳戶中。 因此，與快照集相關聯的儲存體成本會根據帳戶類型的快照集價格，以及裝載備份檔案共用和快照集之儲存體帳戶的冗余設定，以 Azure 檔案計費的一部分計費。

- 不同備份的保留期
  - 您預期會保留「每日」備份多久？  (（以天為單位）) 
  - 您希望保留「每週」備份多久？  (周) 
  - 您預期會保留「每月」備份的時間長度嗎？ 幾個月內的 () 
  - 您預期會保留「每年」備份多久？  (年) 

  請參閱[Azure 檔案共用支援矩陣](azure-file-share-support-matrix.md#retention-limits)，以取得每個類別目錄中支援的最大保留值。

- **選擇性**–修改地區定價或申請折扣費率。
  - 針對「美國東部」區域，預設值是針對每 GB 的快照集儲存體成本和受保護的實例成本所設定。 如果您想要檢查不同區域或折扣費率的估價，請針對 [**嘗試評估不同的地區？** ] 選項選取 **[是]** ，然後輸入您想要用來執行估計值的費率。

## <a name="next-steps"></a>後續步驟

[什麼是 Azure 備份服務？](backup-overview.md)

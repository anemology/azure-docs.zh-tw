---
title: 監視 Azure 檔案同步 | Microsoft Docs
description: 請參閱如何使用 Azure 監視器、儲存體同步服務和 Windows Server 監視您的 Azure 檔案同步部署。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 737617b1fb8bd233a8747deacbbb328a02fa30ef
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185616"
---
# <a name="monitor-azure-file-sync"></a>監視 Azure 檔案同步

使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的彈性、效能及相容性。 Azure 檔案同步會將 Windows Server 轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定來從本機存取資料，包括 SMB、NFS 和 FTPS。 您可以視需要存取多個散佈於世界各地的快取。

本文說明如何使用 Azure 監視器、儲存體同步服務和 Windows Server 監視您的 Azure 檔案同步部署。

本指南涵蓋下列案例： 
- 在 Azure 監視器中查看 Azure 檔案同步計量。
- 在 Azure 監視器中建立警示，以主動通知您重大狀況。
- 使用 Azure 入口網站來查看 Azure 檔案同步部署的健全狀況。
- 如何使用 Windows 伺服器上的事件記錄檔和效能計數器來監視 Azure 檔案同步部署的健全狀況。 

## <a name="azure-monitor"></a>Azure 監視器

使用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)來查看計量，並設定同步處理、雲端階層處理和伺服器連線的警示。  

### <a name="metrics"></a>計量

依預設會啟用 Azure 檔案同步的計量，並且每 15 分鐘傳送至 Azure 監視器一次。

**如何在 Azure 監視器中查看 Azure 檔案同步計量**
- 移至**Azure 入口網站**中的**儲存體同步服務**，然後按一下 [**計量**]。
- 按一下 [計量 **] 下拉式選單，然後**選取您想要查看的度量。

以下是 Azure 監視器中提供的 Azure 檔案同步計量：

| 度量名稱 | Description |
|-|-|
| 同步的位元組 | 傳輸的資料大小 (上傳和下載)。<br><br>單位：位元組<br>匯總類型：總和<br>適用的維度：伺服器端點名稱、同步方向、同步組名 |
| 雲端階層處理重新叫用 | 重新叫用的資料大小。<br><br>**注意**：未來將會移除此度量。 使用 [雲端階層處理重新叫用大小] 計量來監視重新叫用的資料大小。<br><br>單位：位元組<br>匯總類型：總和<br>適用的維度：伺服器名稱 |
| 雲端階層處理重新叫用大小 | 重新叫用的資料大小。<br><br>單位：位元組<br>匯總類型：總和<br>適用的維度：伺服器名稱、同步組名 |
| 雲端階層處理重新叫用大小 (依應用程式) | 應用程式所回收的資料大小。<br><br>單位：位元組<br>匯總類型：總和<br>適用的維度：應用程式名稱、伺服器名稱、同步組名 |
| 雲端階層處理重新叫用輸送量 | 資料重新叫用輸送量的大小。<br><br>單位：位元組<br>匯總類型：總和<br>適用的維度：伺服器名稱、同步組名 |
| 檔案無法同步 | 無法同步的檔案計數。<br><br>單位：Count<br>匯總類型：總和<br>適用的維度：伺服器端點名稱、同步方向、同步組名 |
| 同步的檔案 | 傳輸的檔案計數 (上傳和下載)。<br><br>單位：Count<br>匯總類型：總和<br>適用的維度：伺服器端點名稱、同步方向、同步組名 |
| 伺服器線上狀態 | 從伺服器接收到的活動訊號計數。<br><br>單位：Count<br>彙總類型：最大值<br>適用的維度：伺服器名稱 |
| 同步工作階段結果 | 同步工作階段結果 (1=成功的同步工作階段；0=失敗的同步工作階段)<br><br>單位：Count<br>匯總類型：最大值<br>適用的維度：伺服器端點名稱、同步方向、同步組名 |

### <a name="alerts"></a>警示

當您的監視資料中發現重要條件時，警示會主動通知您。 若要深入瞭解如何在 Azure 監視器中設定警示，請參閱[Microsoft Azure 中的警示總覽](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

**如何建立 Azure 檔案同步的警示**

- 在**Azure 入口網站**中，移至您的**儲存體同步服務**。 
- 按一下 [監視] 區段中的 [**警示**]，然後按一下 [ **+ 新增警示規則**]。
- 按一下 [**選取條件**]，並提供警示的下列資訊： 
    - **計量**
    - **維度名稱**
    - **警示邏輯**
- 按一下 [**選取動作群組**]，然後 (電子郵件、SMS 等 ) 新增動作群組，方法是選取現有的動作群組或建立新的動作群組。
- 填寫**警示詳細資料**，例如**警示規則名稱**、**描述**和**嚴重性**。
- 按一下 [**建立警示規則**] 來建立警示。  

下表列出一些要監視的範例案例，以及要用於警示的適當計量：

| 狀況 | 用於警示的度量 |
|-|-|
| 伺服器端點健全狀況在入口網站中顯示錯誤 | 同步工作階段結果 |
| 檔案無法同步處理到伺服器或雲端端點 | 檔案無法同步 |
| 已註冊的伺服器無法與儲存體同步服務進行通訊 | 伺服器線上狀態 |
| 雲端階層處理重新叫用大小已超過一天的500GiB  | 雲端階層處理重新叫用大小 |

如需有關如何為這些案例建立警示的指示，請參閱[警示範例](#alert-examples)一節。

## <a name="storage-sync-service"></a>儲存體同步服務

若要在**Azure 入口網站**中查看 Azure 檔案同步部署的健康情況，請流覽至**儲存體同步服務**，並提供下列資訊：

- 已註冊的伺服器健全狀況
- 伺服器端點健全狀況
    - 檔案無法同步
    - 同步活動
    - 雲端階層處理效率
    - 檔案未分層
    - 召回錯誤
- 計量

### <a name="registered-server-health"></a>已註冊的伺服器健全狀況

若要在入口網站中查看**已註冊的伺服器健全狀況**，請流覽至**儲存體同步服務**的 [**已註冊的伺服器**] 區段。

- 如果**已註冊的伺服器**狀態為 [**線上**]，伺服器就會成功與服務進行通訊。
- 如果 [**已註冊的伺服器**] 狀態**顯示為 [離線**]，表示儲存體同步監視器進程 ( # A0) 並未執行，或伺服器無法存取 Azure 檔案同步服務。 如需指引，請參閱[疑難排解檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity)。

### <a name="server-endpoint-health"></a>伺服器端點健全狀況

若要在入口網站中查看**伺服器端點**的健全狀況，請流覽至**儲存體同步服務**的 [**同步群組**] 區段，然後選取一個**同步處理群組**。

- 入口網站中的**伺服器端點健康**情況和**同步活動**是以伺服器上的遙測事件記錄檔中記錄的同步事件為基礎， (識別碼9102和 9302) 。 如果同步會話因為暫時性錯誤而失敗（例如「已取消錯誤」），只要目前的同步會話正在進行，同步處理就會在入口網站中顯示為狀況良好， (檔案套用) 。 事件識別碼9302是同步處理進度事件，而且一旦同步會話完成，就會記錄事件識別碼9102。  如需詳細資訊，請參閱[同步處理健康](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)情況和[同步處理進度](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。 如果入口網站因同步未進行進度而顯示錯誤，請參閱[疑難排解檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors)以取得指導方針。
- 入口網站中的 [**未同步**處理的檔案計數] 是根據記錄在伺服器上的遙測事件記錄檔中的事件識別碼9121。 同步會話完成後，每個專案的錯誤都會記錄此事件。 若要解決每個專案的錯誤，請參閱[如何? 查看是否有未同步處理的特定檔案或資料夾？](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。
- 若要在入口網站中查看雲端階層處理**效率**，請移至**伺服器端點屬性**，然後流覽至 [**雲端**階層處理] 區段。 提供給雲端階層處理效率的資料是根據記錄在伺服器上的遙測事件記錄檔中的事件識別碼9071。 若要深入了解，請參閱[雲端階層處理概觀](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering)。
- 若要在入口網站中查看**未分層**和**召回錯誤**的檔案，請移至**伺服器端點屬性**，然後流覽至 [**雲端分層**] 區段。 **未分層**的檔案是根據記錄在伺服器上的遙測事件記錄檔中的事件識別碼9003，而重新**叫用錯誤**是以事件識別碼9006為基礎。 若要調查無法進行階層處理或回收的檔案，請參閱[如何疑難排解無法進行階層式](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-tier)檔案，以及如何針對無法重新叫[用的檔案進行疑難排解](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled)。

### <a name="metric-charts"></a>度量圖表

- 下列計量圖表可在儲存體同步服務入口網站中看到：

  | 度量名稱 | Description | 分頁名稱 |
  |-|-|-|
  | 同步的位元組 | 傳輸的資料大小 (上傳和下載) | 同步群組、伺服器端點 |
  | 雲端階層處理重新叫用 | 重新叫用的資料大小 | 已註冊的伺服器 |
  | 檔案無法同步 | 無法同步的檔案計數 | 伺服器端點 |
  | 同步的檔案 | 傳輸的檔案計數 (上傳和下載) | 同步群組、伺服器端點 |
  | 伺服器線上狀態 | 從伺服器接收到的活動訊號計數 | 已註冊的伺服器 |

- 若要深入瞭解，請參閱[Azure 監視器](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)。

  > [!Note]  
  > 儲存體同步服務入口網站中的圖表時間範圍為 24 小時。 若要檢視不同的時間範圍或維度，請使用 Azure 監視器。

## <a name="windows-server"></a>Windows Server

在已安裝 Azure 檔案同步代理程式的**Windows 伺服器**上，您可以使用**事件記錄**檔和**效能計數器**，來查看該伺服器上伺服器端點的健全狀況。

### <a name="event-logs"></a>事件記錄檔

使用伺服器上的遙測事件記錄，可監視已註冊的伺服器、同步、和雲端階層處理健康情況。 遙測事件記錄檔位於 [*應用程式] 和 [and services\microsoft\filesync\agent*] 底下的事件檢視器。

同步健全狀況

- 同步工作階段完成後，會記錄事件識別碼 9102。 使用此事件判斷同步會話是否成功 (**HResult = 0**) 以及是否有每個專案的同步錯誤。 如需詳細資訊，請參閱[同步處理健康](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync)情況和[每個專案的錯誤](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)檔。

  > [!Note]  
  > 有時候同步會話會整體失敗，或具有非零的 PerItemErrorCount。 不過，它們仍會繼續進行，而某些檔案也會順利同步。 您可以在套用的欄位（例如看出 appliedfilecount、AppliedDirCount、AppliedTombstoneCount 和 AppliedSizeBytes）中看到此功能。 這些欄位會告訴您會話成功的程度。 如果您在某個資料列中看到多個同步會話失敗，而且其已套用計數增加，請在開啟支援票證之前，提供同步處理時間再試一次。

- 當同步會話完成時，會針對每個專案的錯誤記錄事件識別碼9121。 使用此事件可判斷因此錯誤而無法同步的檔案數 (**PersistentCount**和**TransientCount**) 。 應該調查持續性的每個專案錯誤，請參閱如何? 查看是否有特定的檔案[或資料夾未同步？](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。

- 如果有使用中的同步工作階段，則會每隔 5 到 10 分鐘記錄事件識別碼 9302 一次。 使用此事件來判斷目前的同步會話是否正在進行 (**AppliedItemCount > 0**) 的進度。 如果同步處理未進行進度，同步會話最後應該會失敗，而且會記錄事件識別碼9102並產生錯誤。 如需詳細資訊，請參閱[同步處理進度檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)。

已註冊的伺服器健全狀況

- 當伺服器查詢服務中的作業時，將會每 30 秒記錄事件識別碼 9301 一次。 如果 GetNextJob 以**status = 0**完成，伺服器就能夠與服務通訊。 如果 GetNextJob 完成並出現錯誤，請參閱[疑難排解檔](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#server-endpoint-noactivity)以取得指導方針。

雲端階層處理健全狀況

- 若要監視伺服器上的分層活動，請使用遙測事件記錄檔中的事件識別碼9003、9016和9029，其位於 [*應用程式] 和 [and services\microsoft\filesync\agent*] 底下的事件檢視器中。

  - 事件識別碼 9003 會提供伺服器端點的錯誤分布。 例如：總錯誤計數和 ErrorCode。 每個錯誤碼會記錄一個事件。
  - 事件識別碼 9016 會提供磁碟區的建立映像結果。 例如：可用空間百分比是、會話中的已幻影檔案數目，以及無法准刪除的檔案數目。
  - 事件識別碼 9029 會提供伺服器端點的虛像化工作階段資訊。 例如：會話中嘗試的檔案數目、會話中階層式檔案數目，以及已階層式檔案數目。
  
- 若要監視伺服器上的回收活動，請使用遙測事件記錄檔中的事件識別碼9005、9006、9009、9059和9071，其位於 [*應用程式和 and services\microsoft\filesync\agent*] 底下的事件檢視器。

  - 事件識別碼 9005 會提供伺服器端點的回收可靠性。 例如：存取的唯一檔案總數，以及存取失敗的唯一檔案總數。
  - 事件識別碼 9006 會提供伺服器端點的回收錯誤分布。 例如：失敗的要求總數和 ErrorCode。 每個錯誤碼會記錄一個事件。
  - 事件識別碼 9009 會提供伺服器端點的回收工作階段資訊。 例如： DurationSeconds、CountFilesRecallSucceeded 和 CountFilesRecallFailed。
  - 事件識別碼 9059 會提供伺服器端點的應用程式回收分布。 例如： ShareId、應用程式名稱和 TotalEgressNetworkBytes。
  - 事件識別碼9071會提供伺服器端點的雲端階層處理效率。 例如： TotalDistinctFileCountCacheHit、TotalDistinctFileCountCacheMiss、TotalCacheHitBytes 和 TotalCacheMissBytes。

### <a name="performance-counters"></a>效能計數器

使用伺服器上的 Azure 檔案同步效能計數器，可監視同步活動。

若要在伺服器上查看 Azure 檔案同步效能計數器，請開啟效能監視器 ( # A0) 。 您可以在 [已傳送的**Afs 位元組**] 和 [ **afs 同步作業**] 物件底下找到計數器。

以下是效能監視器中為 Azure 檔案同步提供的效能計數器：

| 效能物件\計數器名稱 | Description |
|-|-|
| 傳輸的 AFS 位元組\每秒下載的位元組 | 每秒下載的位元組數。 |
| 傳輸的 AFS 位元組\每秒上傳的位元組 | 每秒上傳的位元組數。 |
| 傳輸的 AFS 位元組\每秒的位元組總數 | 每秒的位元組總數 (上傳和下載)。 |
| AFS 同步作業\每秒下載的同步檔案 | 每秒下載的檔案數。 |
| AFS 同步作業\每秒上傳的同步檔案 | 每秒上傳的檔案數。 |
| AFS 同步作業\每秒的同步檔案作業總數 | 同步的檔案總數 (上傳和下載)。 |

## <a name="alert-examples"></a>警示範例
本節提供 Azure 檔案同步的一些範例警示。

  > [!Note]  
  > 如果您建立警示且其雜訊過多，請調整閾值和警示邏輯。
  
### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>當伺服器端點健康情況在入口網站中顯示錯誤時，如何建立警示

1. 在 [ **Azure 入口網站**中，流覽至個別的**儲存體同步服務**。 
2. 移至 [**監視**] 區段，然後按一下 [**警示**]。 
3. 按一下 [ **+ 新增警示規則**] 以建立新的警示規則。 
4. 按一下 [**選取條件**] 來設定條件。
5. 在 [**設定信號邏輯**] 分頁中，按一下 [信號名稱] 底下的 [**同步會話結果**]。  
6. 選取下列維度設定： 
    - 維度名稱：**伺服器端點名稱**  
    - 操作**=** 
    - 維度值：**所有目前和未來的值**  
7. 流覽至 [**警示邏輯**] 並完成下列步驟： 
    - 閾值設定為**靜態** 
    - 運算子：**小於** 
    - 匯總類型：**最大值**  
    - 臨界值： **1** 
    - 評估依據：匯總資料細微性 = **24 小時**|評估頻率 =**每小時** 
    - 按一下 [**完成]。** 
8. 按一下 [**選取動作群組**]，藉由選取現有的動作群組或建立新的動作群組，將動作群組 (電子郵件、SMS 等 ) 新增至警示。
9. 填寫**警示詳細資料**，例如**警示規則名稱**、**描述**和**嚴重性**。
10. 按一下 [建立警示規則]。 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>如何在檔案無法同步至伺服器或雲端端點時建立警示

1. 在 [ **Azure 入口網站**中，流覽至個別的**儲存體同步服務**。 
2. 移至 [**監視**] 區段，然後按一下 [**警示**]。 
3. 按一下 [ **+ 新增警示規則**] 以建立新的警示規則。 
4. 按一下 [**選取條件**] 來設定條件。
5. 在 [**設定信號邏輯**] 分頁中，按一下 [信號名稱] 底下的 [檔案**未同步**]。  
6. 選取下列維度設定： 
     - 維度名稱：**伺服器端點名稱**  
     - 操作**=** 
     - 維度值：**所有目前和未來的值**  
7. 流覽至 [**警示邏輯**] 並完成下列步驟： 
     - 閾值設定為**靜態** 
     - 運算子：**大於** 
     - 匯總類型： **Total**  
     - 臨界值： **100** 
     - 評估依據：匯總資料細微性 = **5 分鐘**|評估頻率 =**每5分鐘** 
     - 按一下 [**完成]。** 
8. 按一下 [**選取動作群組**]，藉由選取現有的動作群組或建立新的動作群組，將動作群組 (電子郵件、SMS 等 ) 新增至警示。
9. 填寫**警示詳細資料**，例如**警示規則名稱**、**描述**和**嚴重性**。
10. 按一下 [建立警示規則]。 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>如何在已註冊的伺服器無法與儲存體同步服務通訊時建立警示

1. 在 [ **Azure 入口網站**中，流覽至個別的**儲存體同步服務**。 
2. 移至 [**監視**] 區段，然後按一下 [**警示**]。 
3. 按一下 [ **+ 新增警示規則**] 以建立新的警示規則。 
4. 按一下 [**選取條件**] 來設定條件。
5. 在 [**設定信號邏輯**] 分頁中，按一下 [信號名稱] 底下的 [**伺服器線上狀態**]。  
6. 選取下列維度設定： 
     - 維度名稱：**伺服器名稱**  
     - 操作**=** 
     - 維度值：**所有目前和未來的值**  
7. 流覽至 [**警示邏輯**] 並完成下列步驟： 
     - 閾值設定為**靜態** 
     - 運算子：**小於** 
     - 匯總類型：**最大值**  
     - 臨界值 (位元組) ： **1** 
     - 評估依據：匯總資料細微性 = **1 小時**|評估頻率 =**每30分鐘** 
     - 按一下 [**完成]。** 
8. 按一下 [**選取動作群組**]，藉由選取現有的動作群組或建立新的動作群組，將動作群組 (電子郵件、SMS 等 ) 新增至警示。
9. 填寫**警示詳細資料**，例如**警示規則名稱**、**描述**和**嚴重性**。
10. 按一下 [建立警示規則]。 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>如何在雲端階層處理重新叫用大小於一天內超過500GiB 時建立警示

1. 在 [ **Azure 入口網站**中，流覽至個別的**儲存體同步服務**。 
2. 移至 [**監視**] 區段，然後按一下 [**警示**]。 
3. 按一下 [ **+ 新增警示規則**] 以建立新的警示規則。 
4. 按一下 [**選取條件**] 來設定條件。
5. 在 [**設定信號邏輯**] 分頁中，按一下 [信號名稱] 底下的 [雲端階層處理**召回大小**]  
6. 選取下列維度設定： 
     - 維度名稱：**伺服器名稱**  
     - 操作**=** 
     - 維度值：**所有目前和未來的值**  
7. 流覽至 [**警示邏輯**] 並完成下列步驟： 
     - 閾值設定為**靜態** 
     - 運算子：**大於** 
     - 匯總類型： **Total**  
     - 臨界值 (位元組) ： **67108864000** 
     - 評估依據：匯總資料細微性 = **24 小時**|評估頻率 =**每小時** 
    - 按一下 [**完成]。** 
8. 按一下 [**選取動作群組**]，藉由選取現有的動作群組或建立新的動作群組，將動作群組 (電子郵件、SMS 等 ) 新增至警示。
9. 填寫**警示詳細資料**，例如**警示規則名稱**、**描述**和**嚴重性**。
10. 按一下 [建立警示規則]。 

## <a name="next-steps"></a>後續步驟
- [針對 Azure 檔案同步部署進行規劃](storage-sync-files-planning.md) \(部分機器翻譯\)
- [考量防火牆和 Proxy 設定](storage-sync-files-firewall-and-proxy.md)
- [部署 Azure 檔案同步](storage-sync-files-deployment-guide.md) \(部分機器翻譯\)
- [針對 Azure 檔案同步問題進行疑難排解](storage-sync-files-troubleshoot.md) \(部分機器翻譯\)
- [Azure 檔案服務常見問題集](storage-files-faq.md)

---
title: StorSimple Virtual Array Update 0.5 版本資訊| Microsoft Docs
description: 描述執行 Update 0.5 之 StorSimple Virtual Array 的重大未決問題和解決方式。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: 385d9126d578250064659153f6f0f54eec696790
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "60870667"
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>StorSimple Virtual Array Update 0.5 版本資訊

## <a name="overview"></a>概觀

下列版本資訊指出 Microsoft Azure StorSimple Virtual Array 更新的重大未決問題和已解決問題。

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。 部署 StorSimple Virtual Array 之前，請仔細檢閱版本資訊中所含的資訊。

Update 0.5 與軟體版本 **10.0.10290.0** 對應。

> [!NOTE]
> 更新是干擾性作業，會重新啟動您的裝置。 如果 I/O 正在進行中，裝置就會停機。 如需有關如何套用更新的詳細指示，請移至[安裝 Update 0.5](storsimple-virtual-array-install-update-05.md)。


## <a name="whats-new-in-the-update-05"></a>Update 0.5 的新功能
Update 0.5 主要是錯誤修正組建。 主要加強功能錯誤修正如下：

- **備份復原功能改進** - 此版本包含可改進備份復原功能的修正。 在舊版中，只會針對特定的例外狀況重試備份。 此版本會重試所有備份例外狀況，而讓備份更有彈性。

- **儲存體使用情況監視更新** - 從 2017 年 6 月 30 日開始，將會淘汰「StorSimple 虛擬裝置系列」的儲存體使用情況監視。 這適用於執行 Update 0.4 或更舊版本的所有虛擬陣列。 此更新包含可讓您在 Azure 入口網站中繼續使用儲存體使用情況監視所需的變更。 在 2017 年 6 月 30 日之前安裝這個重大更新，即可繼續使用監視功能。


## <a name="issues-fixed-in-the-update-05"></a>Update 0.5 中修正的問題

下表提供本版已修正問題的摘要。

| 不會。 | 功能 | 問題 |
| --- | --- | --- |
| 1 |備份復原功能| 在舊版中，只會針對特定的例外狀況重試備份。 此版本包含會重試所有備份例外狀況來讓備份更有彈性的修正。|
| 2 |監視| 從 2017 年 6 月 30 日開始，將會取代「StorSimple 虛擬裝置系列」的儲存體使用情況監視。 此動作會影響在 StorSimple Virtual Array (1200 型號) 上執行之 StorSimple Device Manager 服務上的監視圖表。 此版本包含可讓使用者在 2017 年 6 月 30 日後繼續在虛擬陣列上使用儲存體使用情況監視的更新。|
| 3 |檔案伺服器| 在舊版中，使用者可以誤將已加密的檔案複製到虛擬陣列。 此版本包含不會允許將已加密檔案複製到虛擬陣列的修正。 如果您的裝置在此更新之前已有現有的已加密檔案，則備份將會持續失敗，直到將所有已加密的檔案從系統中移除為止。 |


## <a name="known-issues-in-the-update-05"></a>Update 0.5 的已知問題

下表提供 StorSimple Virtual Array 的已知問題摘要，並包含舊版所列的問題。

| 不會。 | 功能 | 問題 | 因應措施/註解 |
| --- | --- | --- | --- |
| **1.** |更新 |預覽版中所建立的虛擬裝置無法更新為支援的正式運作版本。 |必須針對正式運作版本使用災害復原 (DR) 工作流程容錯移轉這些虛擬裝置。 |
| **2.** |佈建的資料磁碟 |佈建特定指定大小的資料磁碟並建立對應的 StorSimple 虛擬裝置之後，不得展開或壓縮資料磁碟。 嘗試執行會導致裝置本機層中的所有資料遺失。 | |
| **3.** |群組原則 |若裝置已加入網域，套用群組原則可能會對裝置運作產生不良的影響。 |確定您的虛擬陣列位於它自己的 Active Directory 組織單位 (OU) 中，且沒有套用群組原則物件 (GPO)。 |
| **4.** |本機 Web UI |Internet Explorer (IE ESC) 如有啟用增強式安全性功能，部分本機 Web UI 頁面 (例如 [疑難排解] 或 [維護]) 可能無法正常運作。 這些頁面上的按鈕也可能無法運作。 |關閉 Internet Explorer 中的增強式安全性功能。 |
| **5.** |本機 Web UI |在 Hyper-V 虛擬機器中，Web UI 中的網路介面會顯示為 10 Gbps 介面。 |這個行為是 Hyper-V 的反射。 Hyper-V 一律會將虛擬網路介面卡顯示為 10 Gbps。 |
| **6.** |階層式磁碟區或共用 |不支援使用 StorSimple 階層式磁碟區之應用程式的位元組範圍鎖定。 如果啟用位元組範圍鎖定，則 StorSimple 分層無法運作。 |建議的方法包括︰ <br></br>關閉應用程式邏輯中的位元組範圍鎖定。<br></br>選擇將這個應用程式的資料放在固定在本機的磁碟區中，而非階層式磁碟區。<br></br>警告**：使用固定在本機的磁碟區，並啟用位元組範圍鎖定時，固定在本機的磁碟區可以上線，即使在還原完成之前也是一樣。 在這種情況下，如果正在還原，則必須等待還原完成。 |
| **7.** |階層式共用 |使用大型檔案可能會導致緩慢分層輸出。 |使用大型檔案時，建議最大檔案不要超過共用大小的 3%。 |
| **8.** |共用的已使用容量 |您可能會在共用上沒有任何資料時看到共用耗用量。 這是因為共用的已使用容量包括中繼資料。 | |
| **9.** |災害復原 |您只能對與來源裝置網域相同的網域執行檔案伺服器的災害復原。 這個版本不支援另一個網域中目標裝置的災害復原。 |新版本將會實作這個功能。 如需詳細資訊，請移至 [StorSimple Virtual Array 的容錯移轉和災害復原](storsimple-virtual-array-failover-dr.md) |
| **十大.** |Azure PowerShell |在這版本中，無法透過 Azure PowerShell 管理 StorSimple 虛擬裝置。 |所有虛擬裝置管理都應該透過 Azure 入口網站和本機 Web UI 來完成。 |
| **英寸.** |密碼變更 |虛擬陣列裝置主控台僅接受美式鍵盤格式的輸入。 | |
| **12.** |CHAP |CHAP 認證一經建立，即無法移除。 此外，如果您修改 CHAP 認證，就必須先讓磁碟區離線再上線，變更才會生效。 |新版本將會解決此問題。 |
| **十三.** |iSCSI 伺服器 |針對 iSCSI 磁碟區顯示的 [已使用的儲存體] 在 StorSimple Device Manager 服務與 iSCSI 主機中可能不同。 |ISCSI 主機具有檔案系統檢視。<br></br>裝置會在達到磁碟區大小上限時，看到所配置的區塊。 |
| **14.** |檔案伺服器 |如果資料夾中的檔案中有與其相關聯的替代資料流 (ADS)，就不會透過災害復原、複製和項目層級復原來備份或還原 ADS。 | |
| **次.** |檔案伺服器 |不支援符號連結。 | |
| **1600.** |檔案伺服器 |如果將 Windows 加密檔案系統 (EFS) 保護的檔案複製或儲存到 StorSimple 虛擬陣列檔案伺服器，將會導致不支援的組態。  | |

## <a name="next-step"></a>下一步
在您的 StorSimple Virtual Array 上[安裝 Update 0.5](storsimple-virtual-array-install-update-05.md)。

## <a name="references"></a>參考資料
要尋找舊版本資訊嗎？ 請移至：

* [StorSimple Virtual Array Update 0.4 版本資訊](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 版本資訊](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 和 0.2 版本資訊](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array 正式運作版版本資訊](storsimple-ova-pp-release-notes.md)


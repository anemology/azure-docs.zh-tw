---
title: 檢視 Azure 自動化更新評量
description: 此文章說明如何檢視更新管理部署的更新評量。
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 92861304a946e357b2b265cd825eceb8e22f7d2d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449976"
---
# <a name="view-update-assessments"></a>檢視更新評估

在更新管理中，您可以查看電腦的相關資訊、遺失的更新、更新部署，以及排程的更新部署。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)

## <a name="view-update-assessment"></a>檢視更新評量

在更新管理中，您可以查看電腦的相關資訊、遺失的更新、更新部署，以及排程的更新部署。

[![更新管理預設視圖](./media/update-mgmt-overview/update-management-view.png)](./media/update-mgmt-overview/update-management-view-expanded.png#lightbox)

若要查看更新評估，請執行下列動作。

1. 在 [Azure 入口網站中，流覽至 [**自動化帳戶**]，並從清單中選取已啟用更新管理的自動化帳戶。

2. 在您的自動化帳戶中，從左側窗格中選取 [**更新管理**]。

3. 您環境的更新會列在 [**更新管理**] 頁面上。 如果有任何更新識別為遺失，則 [**遺失更新**] 索引標籤上會顯示它們的清單。

   在 [**相容性**] 欄底下，您可以看到上次評估電腦的時間。 在 [**更新代理程式就緒**] 資料行下，您可以看到更新代理程式的健全狀況。 如果發生問題，請選取連結來移至可協助您更正問題的疑難排解文件。

4. 在 [資訊連結] 下，選取某項更新的連結可開啟支援文章，其中提供有關更新的重要資訊。

     [![查看更新狀態](./media/update-mgmt-view-update-assessments/missing-updates.png)](./media/update-mgmt-view-update-assessments/missing-updates-expanded.png#lightbox)

5. 按一下更新的任何其他地方，即可開啟 [記錄搜尋] 窗格。 記錄搜尋的查詢已針對該特定更新預先定義。 您可以修改此查詢或建立您自己的查詢，以檢視詳細資訊。

    [![查看記錄查詢結果](./media/update-mgmt-view-update-assessments/logsearch-results.png)](./media/update-mgmt-view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>檢視缺少的更新

選取 [缺少的更新] 以檢視機器缺少的更新清單。 會列出每個更新，而且您可以選取更新。 畫面上會顯示需要更新的機器數目、作業系統詳細資料，以及一個能提供詳細資訊的連結。 [記錄搜尋] 窗格也會顯示與更新有關的詳細資訊。

![遺失更新](./media/update-mgmt-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>使用更新分類

下表列出「更新管理」中的更新分類清單，其中包含每個分類的定義。

### <a name="windows"></a>Windows

|分類  |描述  |
|---------|---------|
|重大更新     | 特定問題的更新，負責解決與安全性無關的重大錯誤 (Bug)。        |
|安全性更新     | 產品特定的安全性相關問題更新。        |
|更新彙總套件     | 一組封裝在一起以便於部署的 Hotfix。        |
|Feature Pack     | 在產品版本之外散發的新產品功能。        |
|Service Pack     | 一組套用到應用程式的 Hotfix。        |
|定義更新     | 病毒或其他定義檔案的更新。        |
|工具     | 有助於完成一或多個工作的公用程式或功能。        |
|更新     | 目前已安裝應用程式或檔案的更新。        |

### <a name="linux"></a>Linux

|分類  |描述  |
|---------|---------|
|重大更新和安全性更新     | 特定問題或特定產品的安全性相關問題的更新，         |
|其他更新     | 本質上不重要或非安全性更新的所有其他更新。        |

針對 Linux，更新管理可以區分雲端中的重大更新和安全性更新，同時顯示評量資料。 (能實現此保證是因為雲端資料豐富性。)針對修補，「更新管理」仰賴機器上可用的分類資料。 與其他發行版本不同，CentOS 在產品的 RTM 版本中沒有此資訊可供使用。 如果您將 CentOS 機器設定為傳回下列命令的安全性資料，則更新管理就能根據分類進行修補：

```bash
sudo yum -q --security check-update
```

目前沒有支援的方法可以在 CentOS 上啟用原生分類資料可用性。 目前，為那些已自行啟用此功能的客戶，只提供盡力而為的支援。

若要將 Red Hat Enterprise 版本 6 上的更新分類，您必須安裝 yum-security 外掛程式。 在 Red Hat Enterprise Linux 7 上，外掛程式已經是 yum 本身的一部分，因此不需要安裝任何項目。 如需進一步資訊，請參閱下列 Red Hat [知識文章](https://access.redhat.com/solutions/10021) \(英文\)。

## <a name="next-steps"></a>後續步驟

此程式的下一個階段是將[更新部署](update-mgmt-deploy-updates.md)至不符合規範的機器，並審查部署結果。

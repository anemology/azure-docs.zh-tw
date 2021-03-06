---
title: Azure AD 權利管理中的存取套件的自我審查
description: 瞭解如何在 Azure Active Directory 存取權審查 (預覽) 中，檢查權利管理存取套件的使用者存取權。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31c44f2423cdc5c43638fe2515757bcb11a9814c
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798438"
---
# <a name="self-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 權利管理中的存取套件的自我審查

Azure AD 權利管理可簡化企業管理群組、應用程式和 SharePoint 網站存取的方式。 本文說明使用者如何 (s) ，自行審查其指派的存取套件。

## <a name="open-the-access-review"></a>開啟存取權審查

若要進行存取權審查，您必須先開啟存取權審查。 請使用下列程式來尋找並開啟存取權審查：

1. 您可能會收到來自 Microsoft 的電子郵件，要求您審查存取權。 找出電子郵件以開啟存取權審查。 以下是要求審核存取的電子郵件範例： 
    
    ![存取權審查自我檢閱者電子郵件](./media/entitlement-management-access-reviews-review-access/self-review-reviewer-email.png)

1. 按一下 [**審查存取**] 連結。

1. https://myaccess.microsoft.com如果您未收到電子郵件，您也可以直接前往來尋找您的暫止存取審查。   (美國政府，請改用 `https://myaccess.microsoft.us` 。 ) 

1. 按一下左側導覽列上的 [**存取評論**]，以查看指派給您的暫止存取評論清單。


1.  按一下您想要開始的審查。

## <a name="perform-the-access-review"></a>執行存取權審查

一旦您開啟存取權審查，您就可以看到您的存取權。 請使用下列程式來進行存取權審查：

1.  決定您是否仍需要存取權套件。 例如，您正在處理的專案並未完成，因此您仍然需要存取權才能繼續處理專案。

1.  按一下 **[是]** 以保留您的存取權，或按一下 [**否**] 移除您的存取權。
    >[!NOTE]
    >如果您指定不再需要存取權，就不會立即從存取套件中移除。 當審核結束或系統管理員停止審查時，您將會從存取套件中移除。

1.  如果您按一下 [**是]**，可能需要在 [**原因**] 方塊中包含理由語句。

1.  按一下 [提交] 。

如果您改變想法，並決定在評論結束前變更回應，則可以返回評論。

## <a name="next-steps"></a>後續步驟

- [審查存取封裝的存取權](entitlement-management-access-reviews-review-access.md) 

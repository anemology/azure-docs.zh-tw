---
title: 將授權指派給群組 - Azure Active Directory | Microsoft Docs
description: 如何使用 Azure Active Directory 群組授權的方式將授權指派給使用者
services: active-directory
keywords: Azure AD 授權
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0524e8756e86771ab555eaa853e3955fe69c8549
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84727648"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>依據 Azure Active Directory 中的群組成員資格將授權指派給使用者

本文會逐步引導您將產品授權指派給一群使用者，並確認他們已在 Azure Active Directory （Azure AD）中正確授權。

在此範例中，Azure AD 組織包含一個稱為「**人力資源部門**」的安全性群組。 這個群組包括人力資源部門的所有成員 (大約是 1,000 位使用者)。 您想要將 Office 365 Enterprise E3 授權指派給整個部門。 產品中包含的 Yammer Enterprise 服務需要暫時停用，直到部門準備好要開始使用它為止。 您也要將 Enterprise Mobility + Security 授權部署到相同群組的使用者。

> [!NOTE]
> 並非所有位置都可使用某些 Microsoft 服務。 系統管理員必須指定使用者的使用位置屬性，才能將授權指派給使用者。
>
> 如果是群組授權指派，未指定使用位置的任何使用者會繼承目錄的位置。 如果您在多個位置擁有使用者，我們建議您一律將使用位置設為 Azure AD 中使用者建立流程的一部分 (例如，透過 AAD Connect 設定) - 這可確保授權指派的結果一律是正確的，且使用者不會在不允許的位置收到服務。

## <a name="step-1-assign-the-required-licenses"></a>步驟 1︰指派所需的授權

1. 使用許可證管理員帳戶登入[**Azure AD 系統管理中心**](https://aad.portal.azure.com)。 若要管理授權，帳戶必須是授權管理員、使用者系統管理員或全域管理員。

1. 選取 [**授權**] 以開啟頁面，您可以在其中查看及管理組織中的所有授權產品。

1. 在 [**所有產品**] 底下，選取產品名稱，以選取 [Office 365 Enterprise E5] 和 [Enterprise Mobility + Security E3]。 若要開始指派，請選取頁面頂端的 [**指派**]。

   ![選取要指派授權的產品](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. 在 [**指派授權**] 頁面上，選取 [**使用者和群組**] 以開啟使用者和群組的清單。

1. 選取使用者或群組，然後使用頁面底部的 [**選取**] 按鈕來確認您的選擇。

1. 在 [**指派授權**] 頁面上，按一下 [**指派選項**]，這會顯示我們先前選取的兩個產品中所包含的所有服務方案。 尋找**Yammer Enterprise** **並加以關閉，以**停用產品授權的服務。 按一下 [**授權選項**] 底部的 **[確定]** 來確認。

   ![選取授權的服務方案](./media/licensing-groups-assign/assignment-options.png)
  
1. 若要完成指派，請在 [**指派授權**] 頁面上，按一下頁面底部的 [**指派**]。

1. 右上角顯示的通知會顯示程序的狀態和結果。 如果無法完成指派給群組 (例如，因為群組中已存在的授權)，按一下通知以檢視失敗的詳細資料。

將授權指派給群組時，Azure AD 會處理該群組的所有現有成員。 此程式可能需要一些時間，並隨著群組大小而改變。 下一個步驟將說明如何確認處理程序已完成，並且決定是否需要進一步注意以解決問題。

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>步驟 2︰確認已完成初始指派

1. 移至 [ **Azure Active Directory**  >  **群組**]。 選取已指派授權的群組。

1. 在 [群組] 頁面上，選取 [**授權**]。 可讓您快速確認授權是否已完全指派給使用者，以及是否有任何錯誤需要探究。 可用資訊如下：

   - 目前指派給群組的服務授權。 選取項目來顯示已啟用且要進行變更的特定服務。

   - 最新授權變更的狀態更新，如果正在處理變更，或所有使用者成員的處理已完成，則可使用。

   - 處於錯誤狀態的使用者授權指派的相關資訊。

   ![授權錯誤和授權狀態](./media/licensing-groups-assign/assignment-errors.png)

1. 如需更多有關授權處理的詳細資訊，請參閱**Azure Active Directory**  >  **使用者和群組**  >  *組名*  >  **Audit logs**。 檢查下列活動：

   - 活動： `Start applying group based license to users` 。 當我們的系統拾取群組的授權指派變更，並開始將其套用到所有使用者成員時，就會進行記錄。 它包含已進行之變更的相關資訊。

   - 活動： `Finish applying group based license to users` 。 當系統完成處理群組中的所有使用者時，就會進行記錄。 它包含已成功處理的使用者人數和無法獲得群組授權指派的使用者人數之摘要。

   [閱讀本節](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity)以深入了解如何使用稽核記錄，以分析群組型授權所做的變更。

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>步驟 3︰檢查授權問題及解決這些問題

1. 移至 [ **Azure Active Directory**  >  **群組**]，然後尋找已指派授權的群組。
1. 在 [群組] 頁面上，選取 [**授權**]。 頁面頂端的通知顯示有10位使用者無法指派授權。 開啟該檔案，以查看此群組的授權錯誤狀態的所有使用者清單。
1. [失敗的指派]**** 資料行告訴我們，兩個產品授權都無法指派給使用者。 [失敗的前幾大原因]**** 資料行包含失敗的原因。 在此案例中為 [衝突的服務方案]****。

   ![無法指派的授權](./media/licensing-groups-assign/failed-assignments.png)

1. 選取使用者以開啟使用者的 [**授權**] 頁面。 此頁面會顯示目前指派給使用者的所有授權。 在此範例中，使用者擁有繼承自 **Kiosk 使用者** 群組的 Office 365 Enterprise E1 授權。 這會與系統嘗試從**人力資源部門**群組套用的 E3 授權衝突。 如此一來，該群組沒有任何授權會指派給使用者。

   ![查看使用者的所有授權衝突](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. 若要解決這個衝突，從 **Kiosk 使用者**群組移除使用者。 在 Azure AD 處理變更之後，會正確指派**人力資源部門**授權。

## <a name="next-steps"></a>後續步驟

若要深入瞭解使用群組授權指派的功能集，請參閱下列文章：

- [什麼是 Azure Active Directory 中以群組為基礎的授權？](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [識別及解決 Azure Active Directory 中群組的授權問題](licensing-groups-resolve-problems.md)
- [如何將個別授權使用者移轉至 Azure Active Directory 中以群組為基礎的授權](licensing-groups-migrate-users.md)
- [如何使用 Azure Active Directory 中的群組型授權在產品授權之間移轉使用者](licensing-groups-change-licenses.md)
- [Azure Active Directory 群組型授權其他案例 (英文)](../active-directory-licensing-group-advanced.md)
- [Azure Active Directory 群組型授權的 PowerShell 範例](licensing-ps-examples.md)

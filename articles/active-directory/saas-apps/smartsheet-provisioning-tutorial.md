---
title: 教學課程：使用 Azure Active Directory 設定 Smartsheet 來自動布建使用者 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶至 Smartsheet。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: c23c57e77f69cd7043e09ff5e2809d8329d62da3
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87925493"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>教學課程：設定 Smartsheet 來自動布建使用者

本教學課程的目的是要示範在 Smartsheet 和 Azure Active Directory 中執行的步驟 (Azure AD) 設定 Azure AD 自動布建和取消布建使用者和/或群組至[Smartsheet](https://www.smartsheet.com/pricing)。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在 Smartsheet 中建立使用者
> * 當使用者不再需要存取權時，移除 Smartsheet 中的使用者
> * 在 Azure AD 和 Smartsheet 之間保持使用者屬性同步
> * Smartsheet (的單一登入建議) 

> [!NOTE]
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)使用者。
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。
* [Smartsheet 租](https://www.smartsheet.com/pricing)使用者。
* 具有系統管理員許可權之 Smartsheet Enterprise 或企業頂級方案上的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 判斷要[在 Azure AD 和 Smartsheet 之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定 Smartsheet 以支援以 Azure AD 布建

將 Smartsheet 設定為使用 Azure AD 自動布建使用者之前，您必須啟用 Smartsheet 上的 SCIM 布建。

1. 在**[Smartsheet 入口網站](https://app.smartsheet.com/b/home)** 中以**系統管理員**身分登入，然後流覽至 [**帳戶管理員**]。

    ![Smartsheet 帳戶管理員](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. 移至 [**安全性控制] > [使用者自動**布建] > 編輯]。

    ![Smartsheet 安全性控制](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. 針對您打算從 Azure AD 到 Smartsheet 布建的使用者，新增及驗證電子郵件網域。 選擇 [**未啟用**] 以確保所有布建動作只源自于 Azure AD，同時確保您的 Smartsheet 使用者清單與 Azure AD 指派同步。

    ![Smartsheet 使用者布建](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. 驗證完成後，您就必須啟用網域。 

    ![Smartsheet 啟用網域](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. 藉由流覽至 [**應用程式和**整合]，產生使用 Azure AD 設定自動使用者布建所需的**秘密權杖**。

    ![Smartsheet 安裝](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. 選擇 [ **API 存取**]。 按一下 [**產生新的存取權杖**]。

    ![Smartsheet 安裝](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. 定義 API 存取權杖的名稱。 按一下 [確定]  。

    ![Smartsheet 安裝](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. 複製 API 存取權杖並加以儲存，因為這會是您唯一可以查看的時間。 這在 Azure AD 的 [**秘密權杖**] 欄位中是必要的。

    ![Smartsheet token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式資源庫新增 Smartsheet

從 Azure AD 應用程式庫新增 Smartsheet，以開始管理布建至 Smartsheet。 如果您先前已設定 SSO 的 Smartsheet，您可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者和群組指派給 Smartsheet 時，您必須選取 [**預設存取**] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。 

* 若要確保 Smartsheet 與 Azure AD 之間使用者角色指派的同位，建議使用完整 Smartsheet 使用者清單中填入的相同角色指派。 若要從 Smartsheet 抓取此使用者清單，請流覽至**帳戶管理員 > 使用者管理 > 其他動作 > 下載使用者清單 (csv) **。

* 若要存取應用程式中的某些功能，Smartsheet 需要使用者擁有多個角色。 若要深入瞭解 Smartsheet 中的使用者類型和許可權，請移至 [[使用者類型和許可權](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)]。

*  如果使用者在 Smartsheet 中已指派多個角色，您**必須**確保這些角色指派會在 Azure AD 中複寫，以避免使用者無法永久存取 Smartsheet 物件的情況。 Smartsheet 中的每個唯一角色都**必須**指派給 Azure AD 中的不同群組。 接著，**必須**將使用者新增至對應于所需角色的每個群組。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>步驟 5。 設定自動使用者布建至 Smartsheet 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 Smartsheet 中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>若要在 Azure AD 中設定 Smartsheet 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [ **Smartsheet**]。

    ![應用程式清單中的 Smartsheet 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![佈建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段底下，分別輸入 [**租使用者 URL** ] 和 [**秘密權杖**] 中的 [ **SCIM 2.0 基底 url] 和 [存取權杖**] 值。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到 Smartsheet。 如果連線失敗，請確定您的 Smartsheet 帳戶具有系統管理員（SysAdmin）許可權，然後再試一次。

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 **[儲存]** 。

8. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至 Smartsheet**]。

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步處理到 Smartsheet 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 Smartsheet 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |作用中|Boolean|
   |title|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |角色 [主要 eq "True"]。顯示|String|
   |角色 [主要 eq "True"]. 類型|String|
   |角色 [主要 eq "True"]。值|String|
   |角色|String|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|String|


10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 Smartsheet 的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建到 Smartsheet 的使用者和/或群組。

    ![佈建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) \(部分機器翻譯\) 深入了解隔離狀態。  

## <a name="connector-limitations"></a>連接器限制

* Smartsheet 不支援虛刪除。 當使用者的**active**屬性設定為 False 時，Smartsheet 會永久刪除該使用者。

## <a name="change-log"></a>變更記錄

* 06/16/2020-為使用者新增了企業延伸模組屬性「成本中心」、「部門」、「經理」和「部門」的支援。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../app-provisioning/check-status-user-account-provisioning.md)

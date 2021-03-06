---
title: 教學課程：使用 Azure Active Directory 來設定自動使用者布建的雪花 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory 以自動布建和取消布建使用者帳戶至雪花。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 7c43d3dd0c8a0d9f6d31d8e379fd195adfd7873f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87925408"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>教學課程：設定自動使用者布建的雪花

本教學課程的目的是要示範如何在雪花和 Azure Active Directory 中執行的步驟 (Azure AD) 設定 Azure AD 自動布建和取消布建使用者和/或群組至[雪花](https://www.Snowflake.com/pricing/)。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。 


> [!NOTE]
> 此連接器目前為公開預覽版。 如需有關預覽功能的一般 Microsoft Azure 使用規定詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="capabilities-supported"></a>支援的功能
> [!div class="checklist"]
> * 在雪花中建立使用者
> * 當使用者不再需要存取權時，移除雪花中的使用者
> * 讓使用者屬性在 Azure AD 和雪花之間保持同步
> * 雪花中的布建群組和群組成員資格
> *  (建議使用[單一登入](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial)雪花) 

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* [Azure AD 租](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)使用者。
* Azure AD 中具有設定佈建[權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)的使用者帳戶 (例如，應用程式管理員、雲端應用程式管理員、應用程式擁有者或全域管理員)。
* [雪花租](https://www.Snowflake.com/pricing/)使用者。
* 雪花中具有系統管理員許可權的使用者帳戶。

## <a name="step-1-plan-your-provisioning-deployment"></a>步驟 1： 規劃佈建部署
1. 了解[佈建服務的運作方式](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) \(部分機器翻譯\)。
2. 判斷誰會在[佈建範圍](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)內。
3. 決定要[在 Azure AD 和雪花之間對應](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)的資料。 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>步驟 2： 設定雪花以支援以 Azure AD 布建

設定雪花以 Azure AD 自動布建使用者之前，您必須在雪花上啟用 SCIM 布建。

1. 登入您的雪花式系統管理主控台。 在反白顯示的工作表中輸入如下所示的查詢，然後按一下 [**執行**]。

    ![雪花式系統管理主控台](media/Snowflake-provisioning-tutorial/image00.png)

2.  系統會為您的雪花租使用者產生 SCIM 存取權杖。 若要取得它，請按一下下方反白顯示的連結。

    ![雪花新增 SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. 複製產生的權杖值，然後按一下 [**完成**]。 此值會在 Azure 入口網站的雪花式應用程式之 [布建] 索引標籤的 [**密碼權杖**] 欄位中輸入。

    ![雪花新增 SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>步驟 3： 從 Azure AD 應用程式庫新增雪花

從 Azure AD 應用程式庫新增雪花，開始管理布建至雪花。 如果您先前已設定適用于 SSO 的雪花，則可以使用相同的應用程式。 不過，建議您在一開始測試整合時，建立個別的應用程式。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)深入了解從資源庫新增應用程式。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>步驟 4： 定義將在佈建範圍內的人員 

Azure AD 佈建服務可供根據對應用程式的指派，或根據使用者/群組的屬性，界定將要佈建的人員。 如果您選擇根據指派來界定將佈建至應用程式的人員，您可以使用下列[步驟](../manage-apps/assign-user-or-group-access-portal.md)將使用者和群組指派給應用程式。 如果您選擇僅根據使用者或群組的屬性來界定將要佈建的人員，可以使用如[這裡](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)所述的範圍篩選條件。 

* 將使用者和群組指派給雪花時，您必須選取 [**預設存取**] 以外的角色。 具有預設存取角色的使用者會從佈建中排除，而且會在佈建記錄中被標示為沒有效率。 如果應用程式上唯一可用的角色是 [預設存取] 角色，您可以[更新應用程式資訊清單](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) \(部分機器翻譯\) 以新增其他角色。 

* 從小規模開始。 在推出給所有人之前，先使用一小部分的使用者和群組進行測試。 當佈建範圍設為已指派的使用者和群組時，您可將一或兩個使用者或群組指派給應用程式來控制這點。 當範圍設為所有使用者和群組時，您可指定[以屬性為基礎的範圍篩選條件](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)。 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>步驟 5。 設定自動使用者布建至雪花 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在雪花中建立、更新和停用使用者和/或群組。

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>若要在 Azure AD 中設定雪花的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Snowflake]****。

    ![應用程式清單中的 Snowflake 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![佈建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![佈建索引標籤](common/provisioning-automatic.png)

5. 在 [系統管理員認證] 區段下，分別輸入 [**租使用者 URL** ] 和 [**秘密權杖**] 欄位中的 [ **SCIM 2.0 基底 Url] 和 [驗證權杖**] 值。 按一下 [**測試連接**] 以確保 Azure AD 可以連接到雪花。 如果連線失敗，請確定您的雪花帳戶具有系統管理員許可權，然後再試一次。

    ![租用戶 URL + 權杖](common/provisioning-testconnection-tenanturltoken.png)

7. 在 [通知電子郵件]**** 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知]**** 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

8. 按一下 **[儲存]** 。

9. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 使用者至雪花**]。

10. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至雪花的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對雪花中的使用者帳戶以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

   |屬性|類型|
   |---|---|
   |作用中|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn： ietf： params： scim：架構： extension： enterprise：2.0： User： defaultRole|String|
   |urn： ietf： params： scim：架構： extension： enterprise：2.0： User： defaultWarehouse|String|

11. **在 [對應**] 區段下，選取 [**同步處理 Azure Active Directory 群組至雪花**]。

12. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至雪花的群組屬性。 選取為 [比對] 屬性**的屬性會**用來比對雪花中的群組以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

      |屬性|類型|
      |---|---|
      |displayName|String|
      |members|參考|

13. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

14. 若要啟用雪花的 Azure AD 布建服務，請在 [**設定**] 區段中將 [布建**狀態**] 變更為 [**開啟**]。

    ![佈建狀態已切換為開啟](common/provisioning-toggle-on.png)

15. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至雪花的使用者和/或群組。 如果無法使用此選項，請在 [系統管理員認證] 底下設定必要欄位，然後按一下 [**儲存**] 並重新整理頁面。 

    ![佈建範圍](common/provisioning-scope.png)

16. 當您準備好要佈建時，按一下 [儲存]。

    ![儲存雲端佈建設定](common/provisioning-configuration-save.png)

    此作業會對在 [設定]**** 區段的 [範圍]**** 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。

## <a name="step-6-monitor-your-deployment"></a>步驟 6. 監視您的部署
設定佈建後，請使用下列資源來監視您的部署：

1. 使用[佈建記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) \(部分機器翻譯\) 來判斷哪些使用者已佈建成功或失敗
2. 檢查[進度列](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) \(部分機器翻譯\) 來查看佈建週期的狀態，以及其接近完成的程度
3. 如果佈建設定似乎處於狀況不良的狀態，應用程式將會進入隔離狀態。 [在此](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) \(部分機器翻譯\) 深入了解隔離狀態。  

## <a name="connector-limitations"></a>連接器限制

* 雪花產生的 SCIM 權杖會在6個月後到期。 請注意，這些需求必須在到期前重新整理，才能讓布建同步處理繼續運作。 

## <a name="change-log"></a>變更記錄

* 已透過使用中的屬性) ，為所有使用者 (啟用07/21/2020 的虛刪除。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶](../app-provisioning/configure-automatic-user-provisioning-portal.md)布建。
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟
* [瞭解如何查看記錄並取得布建活動的報告](../app-provisioning/check-status-user-account-provisioning.md)。

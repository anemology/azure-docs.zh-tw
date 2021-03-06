---
author: baanders
description: 包含檔案，以取得 Azure 數位 Twins 設定中可能的其他需求
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: c2668bdda5002ebd2a34b8a2ffa5885263aec0c9
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009631"
---
您的組織可能需要來自訂用帳戶擁有者/系統管理員的其他動作，才能成功設定應用程式註冊 (並因此完成設定可使用的 Azure 數位 Twins 實例) 。 所需的步驟可能會根據您組織的特定設定而有所不同。

以下是擁有者/系統管理員可能需要執行的一些常見的可能活動。 這些和其他作業可以從 Azure 入口網站的 [ [*Azure AD App 註冊*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)] 頁面執行。
* 授與系統管理員同意應用程式註冊。 您的組織可能會在您的訂用帳戶內所有應用程式註冊的 Azure AD 中，全域開啟必要的系統*管理員同意*。 若是如此，則擁有者/系統管理員必須在應用程式註冊的 [ *API 許可權*] 頁面上為您的公司選取此按鈕，應用程式註冊才會生效：

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/grant-admin-consent.png" alt-text="API 許可權底下 [授與系統管理員同意] 按鈕的入口網站視圖":::
  - 如果已成功授與同意，則 Azure 數位 Twins 的專案應該會顯示 [已_授**與您的公司 (** _的*狀態*] 值) 
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/granted-admin-consent-done.png" alt-text="以 API 許可權授與公司系統管理員同意的入口網站視圖":::
* 啟動公用用戶端存取
* 為 web 和桌上型電腦存取設定特定的回復 Url
* 允許隱含 OAuth2 驗證流程

如需應用程式註冊及其不同安裝選項的詳細資訊，請參閱[*使用 Microsoft 身分識別平臺註冊應用程式*](https://docs.microsoft.com/graph/auth-register-app-v2)。

您現在已有 Azure 數位 Twins 實例準備就緒，已指派許可權來管理它，並已設定用戶端應用程式存取它的許可權。
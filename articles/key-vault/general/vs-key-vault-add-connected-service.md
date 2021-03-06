---
title: 使用 Visual Studio 在 ASP.NET 專案中新增 Key Vault 支援 - Azure Key Vault | Microsoft Docs
description: 使用本教學課程，以協助您了解如何在 ASP.NET 或 ASP.NET Core Web 應用程式中新增 Key Vault 支援。
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: e8c452ffb40b27ed84061e93c1758b3d403052fe
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88054407"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>使用 Visual Studio 連線服務在 Web 應用程式中新增 Key Vault

在本教學課程中，您將了解如何輕鬆地新增所需的項目，以便開始在 Visual Studio 中使用 Azure Key Vault 來管理 Web 專案的祕密，而不論您使用的是 ASP.NET Core 或任何類型的 ASP.NET 專案。 藉由使用 Visual Studio 中的 [已連線的服務] 功能，您可以讓 Visual Studio 自動新增您連線至 Azure Key Vault 所需的所有 NuGet 套件和設定設定。

針對「連線服務」在您專案中為啟用 Key Vault 所做的變更，如需詳細資料，請參閱 [Key Vault 連線服務 - 我的 ASP.NET 4.7.1 專案發生什麼情形](#how-your-aspnet-framework-project-is-modified)或 [Key Vault 連線服務 - 我的 ASP.NET Core 專案發生什麼情形](#how-your-aspnet-core-project-is-modified)。

## <a name="prerequisites"></a>必要條件

- **Azure 訂用帳戶**。 如果您沒有訂用帳戶，請註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
- **Visual Studio 2019 16.3 版**或更新版本[，請立即下載](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。


## <a name="add-key-vault-support-to-your-project"></a>在專案中新增 Key Vault 支援

開始之前，請確定您已登入 Visual Studio。 使用您用於 Azure 訂用帳戶的相同帳戶登入。 然後，開啟 ASP.NET 4.7.1 或更新版本，或 ASP.NET Core 2.0 Web 專案，並執行下列步驟：

1. 在**方案總管**中，以滑鼠右鍵按一下您想要新增 Key Vault 支援的專案，然後選擇 [**新增**  >  **已連線的服務**] [  >  **新增**]。
   [連線服務] 頁面隨即出現，並顯示您可新增至專案的服務。
1. 在可用服務的功能表中，選擇**Azure Key Vault** ，然後按 **[下一步]**。

   ![選擇 [Azure Key Vault]](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. 選取您要使用的訂用帳戶，然後選擇現有的 Key Vault，然後按一下 **[完成]**。 

   ![選取訂用帳戶](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

現在，已建立與 Key Vault 的連線，而且您可以在程式碼中存取您的秘密。 後續步驟會根據您使用的是 ASP.NET 4.7.1 或 ASP.NET Core 而有所差異。

## <a name="access-your-secrets-in-code-aspnet-core"></a>在程式碼 (ASP.NET Core 中存取您的秘密) 

1. 開啟其中一個分頁檔案，例如*Index.cshtml.cs* ，並撰寫下列程式碼：
   1. 使用指示詞包含對 `Microsoft.Extensions.Configuration` 的參考：

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. 新增設定變數。

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. 新增此函式，或使用下列內容取代現有的函式：

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. 更新 `OnGet` 方法。 使用您在上述命令中建立的秘密名稱，來更新此處顯示的預留位置值。

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. 若要在執行時間確認此值，請新增程式碼以顯示在 `ViewData["Message"]` *cshtml*檔案中，以在訊息中顯示密碼。

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

您可以在本機執行應用程式，以確認已從 Key Vault 成功取得密碼。

## <a name="access-your-secrets-aspnet"></a> (ASP.NET) 存取您的密碼
您可以設定設定，讓 web.config 檔案在專案中具有虛擬值，而該專案 `appSettings` 是在執行時間由 true 值所取代。 然後，您就可以透過資料結構來存取此功能 `ConfigurationManager.AppSettings` 。

1. 在方案總管中，以滑鼠右鍵按一下您的專案，然後選取 [管理 NuGet 套件]。 在 [流覽] 索引標籤中，找出並安裝[Microsoft.Configuration.ConfigurationBuilders。](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/)
 
1. 開啟您的 web.config 檔案，並撰寫下列程式碼：
    1. 新增 `configSections` 和 `configBuilders` ：
        ```xml
         <configSections>
            <section
                name="configBuilders"
                type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
                restartOnExternalChanges="false"
                requirePermission="false" />
         </configSections>
         <configBuilders>
            <builders>
            <add
                    name="AzureKeyVault"
                    vaultName="vaultname"
                    type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
                    vaultUri="https://vaultname.vault.azure.net" />
            </builders>
         </configBuilders>
        ```
    1. 尋找 appSettings 標籤、新增屬性 `configBuilders="AzureKeyVault"` ，然後新增一行：
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. `About`在*HomeController.cs*中編輯方法，以顯示 [確認] 的值。

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. 在偵錯工具下，于本機執行應用程式，切換到 [**關於**] 索引標籤，並確認已顯示 [Key Vault 的值。

## <a name="troubleshooting"></a>疑難排解

如果您的 Key Vault 在不同的 Microsoft 帳戶上執行，而不是您所登入的 Visual Studio (例如，Key Vault 正在您的工作帳戶中執行，但 Visual Studio 使用您的私人帳戶) 您在 Program.cs 檔中收到錯誤，Visual Studio 無法取得 Key Vault 的存取權。 若要修正此問題：

1. 移至 [ [Azure 入口網站](https://portal.azure.com)並開啟您的 Key Vault。

1. 選擇 [**存取原則**]、[**新增存取原則**]，然後選擇您以 [主體] 登入的帳戶。

1. 在 Visual Studio 中，**選擇 [** 檔案] [  >  **帳戶設定**]。
從 [**所有帳戶**] 區段中選取 [**新增帳戶**]。 使用您選擇做為存取原則主體的帳戶來登入。

1. 選擇 [**工具**] [  >  **選項**]，然後尋找 [ **Azure 服務驗證**]。 然後選取您剛才新增至 Visual Studio 的帳戶。

現在，當您在偵錯工具時，Visual Studio 會連接到您 Key Vault 所在的帳戶。

## <a name="how-your-aspnet-core-project-is-modified"></a>ASP.NET Core 專案的修改方式

本節識別使用 Visual Studio 新增 Key Vault 聯機服務時，對 ASP.NET 專案所做的確切變更。

### <a name="added-references-for-aspnet-core"></a>已新增 ASP.NET Core 的參考

會影響專案檔案 .NET 參考和 NuGet 套件參考。

| 類型 | 參考 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>已新增 ASP.NET Core 的檔案

- `ConnectedService.json`已新增，它會記錄有關已連線服務提供者、版本的一些資訊，並連結檔。

### <a name="project-file-changes-for-aspnet-core"></a>ASP.NET Core 的專案檔變更

- 已新增已連線的服務 ItemGroup 和檔案 `ConnectedServices.json` 。

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.jsASP.NET Core 的變更

- 已將下列環境變數項目新增至 IIS Express 設定檔及符合您 Web 專案名稱的設定檔：

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>針對 ASP.NET Core 的 Azure 變更

- 已建立資源群組 (或使用了現有的資源群組)。
- 已在指定的資源群組中建立 Key Vault。

## <a name="how-your-aspnet-framework-project-is-modified"></a>ASP.NET Framework 專案的修改方式

本節識別使用 Visual Studio 新增 Key Vault 聯機服務時，對 ASP.NET 專案所做的確切變更。

### <a name="added-references-for-aspnet-framework"></a>已新增 ASP.NET 架構的參考

會影響專案檔 .NET 參考，並 `packages.config` (NuGet 參考) 。

| 類型 | 參考 |
| --- | --- |
| .NET; NuGet | Azure。身分識別 |
| .NET; NuGet | KeyVault 金鑰 |
| .NET; NuGet | KeyVault 秘密 |

### <a name="added-files-for-aspnet-framework"></a>已新增 ASP.NET 架構的檔案

- `ConnectedService.json`已新增，它會記錄有關已連線服務提供者、版本和檔連結的一些資訊。

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET Framework 的專案檔變更

- 已新增連線服務 ItemGroup 和 ConnectedServices.json 檔案。
- 對[新增的參考](#added-references-for-aspnet-framework)一節中所述 .NET 組件的參考。

## <a name="next-steps"></a>後續步驟

如果您遵循本教學課程的指示，您的 Key Vault 許可權會設定為使用您自己的 Azure 訂用帳戶來執行，但這可能不適合生產環境案例。 您可以建立受控識別，以管理應用程式的 Key Vault 存取。 請參閱[使用受控識別提供 Key Vault 驗證](/azure/key-vault/managed-identity)。

閱讀[Key Vault 開發人員指南](developers-guide.md)，以深入瞭解 Key Vault 開發。

---
title: 監視管理租使用者中的委派變更
description: 瞭解如何監視客戶租使用者的委派活動與您的管理租使用者。
ms.date: 08/11/2020
ms.topic: how-to
ms.openlocfilehash: 63b8ec60ecf2f2e5655e3253db7aef01c003fc63
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163334"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>監視管理租使用者中的委派變更

身為服務提供者，您可能會想要知道客戶訂用帳戶或資源群組何時透過[Azure 燈塔](../overview.md)委派給您的租使用者，或移除先前委派的資源。

在管理租使用者中， [Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md)會追蹤租使用者層級的委派活動。 此記錄的活動包含所有客戶租使用者中新增或移除的委派。

本主題說明監視您的租使用者在所有客戶中 (所需的許可權) 以及執行此作業的最佳作法。 其中也包含範例腳本，其中顯示查詢和報告此資料的一種方法。

> [!IMPORTANT]
> 所有這些步驟都必須在您的管理租使用者中執行，而不是在任何客戶租使用者中執行。

## <a name="enable-access-to-tenant-level-data"></a>啟用租使用者層級資料的存取

若要存取租使用者層級的活動記錄資料，必須在根範圍 (/) ，將[監視讀取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)的 Azure 內建角色指派給帳戶。 此指派必須由具有其他提高存取權的全域管理員角色的使用者執行。

### <a name="elevate-access-for-a-global-administrator-account"></a>提升全域管理員帳戶的存取權

若要在根範圍 (/) 指派角色，您必須具有具有提高存取權的全域管理員角色。 只有當您需要進行角色指派時，才應新增這個提高許可權的存取權，然後在完成時移除。

如需新增和移除提高許可權的詳細指示，請參閱提高[存取權以管理所有 Azure 訂用帳戶和管理群組](../../role-based-access-control/elevate-access-global-admin.md)。

在您提高存取權之後，您的帳戶在 Azure 的根範圍中將會有「使用者存取系統管理員」角色。 此角色指派可讓您查看所有資源，並在目錄中的任何訂用帳戶或管理群組中指派存取權，以及在根範圍進行角色指派。

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>建立新的服務主體帳戶以存取租使用者層級的資料

當您提高存取權之後，就可以將適當的許可權指派給帳戶，讓它可以查詢租使用者層級的活動記錄資料。 此帳戶必須在您管理的租使用者的根範圍中指派「[監視讀取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)」 Azure 內建角色。

> [!IMPORTANT]
> 在根範圍授與角色指派，表示相同的許可權將會套用至租使用者中的每個資源。

由於這是廣泛的存取層級，因此建議您將此角色指派給服務主體帳戶，而不是個別使用者或群組。

 此外，我們建議您採用下列最佳作法：

- 建立僅供此函式使用的[新服務主體帳戶](../../active-directory/develop/howto-create-service-principal-portal.md)，而不是將此角色指派給用於其他自動化的現有服務主體。
- 請確定此服務主體沒有任何委派客戶資源的存取權。
- [使用憑證來進行驗證](../../active-directory/develop/howto-create-service-principal-portal.md#upload-a-certificate-or-create-a-secret-for-signing-in)，並[安全地將它儲存在 Azure Key Vault 中](../../key-vault/general/best-practices.md)。
- 限制有權存取的使用者可以代表服務主體進行動作。

使用下列其中一種方法來進行根範圍指派。

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

> [!NOTE]
> 您也可以將位於根範圍的監視讀取器 Azure 內建角色指派給個別使用者或使用者群組。 如果您想讓使用者能夠[直接在 Azure 入口網站中查看委派資訊](#view-delegation-changes-in-the-azure-portal)，這會很有用。 如果您這樣做，請注意這是廣泛的存取層級，應該限制為最少的使用者數目。

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>移除全域管理員帳戶的已提升存取權

建立服務主體帳戶並在根範圍指派監視讀取者角色之後，請務必移除全域管理員帳戶[的提高許可權存取權](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access)，因為將不再需要此存取層級。

## <a name="query-the-activity-log"></a>查詢活動記錄

一旦您建立了新的服務主體帳戶，並讓它具有管理租使用者之根範圍的監視讀取器存取權，就可以用它來查詢和報告您租使用者中的委派活動。

[此 Azure PowerShell 腳本](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes)可用來查詢過去1天的活動，以及報告任何新增或移除的委派 (或未成功) 的嘗試。 它會查詢[租使用者活動記錄](/rest/api/monitor/TenantActivityLogs/List)資料，然後建立下列值來報告新增或移除的委派：

- **DelegatedResourceId**：委派的訂用帳戶或資源群組的識別碼
- **CustomerTenantId**：客戶租使用者識別碼
- **CustomerSubscriptionId**：已委派或包含已委派資源群組的訂用帳戶識別碼
- **CustomerDelegationStatus**：委派資源的狀態變更 ([成功] 或 [失敗]) 
- **EventTimeStamp**：記錄委派變更的日期和時間

查詢此資料時，請記住：

- 如果在單一部署中委派多個資源群組，則會針對每個資源群組傳回個別的專案。
- 對先前委派 (進行的變更（例如更新許可權結構) 會記錄為新增的委派）。
- 如先前所述，帳戶必須有位於根範圍 (/) 的「監視讀取器」 Azure 內建角色，才能存取此租使用者層級的資料。
- 您可以在自己的工作流程和報告中使用此資料。 例如，您可以使用[HTTP 資料收集器 API (公開預覽) ](../../azure-monitor/platform/data-collector-api.md) ，將資料記錄到來自 REST API 用戶端的 Azure 監視器，然後使用[動作群組](../../azure-monitor/platform/action-groups.md)來建立通知或警示。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="view-delegation-changes-in-the-azure-portal"></a>Azure 入口網站中的 View 委派變更

已獲指派監視讀取器 Azure 內建角色的使用者在根範圍中，可以直接在 Azure 入口網站中查看委派變更。

1. 流覽至 [**我的客戶**] 頁面，然後從左側導覽功能表中選取 [**活動記錄**]。
1. 確定已在畫面頂端附近的篩選中選取 [**目錄活動**]。

將會顯示委派變更的清單。 您可以選取 **[編輯資料行**]，以顯示或隱藏**狀態**、**事件類別目錄**、**時間**、**時間戳記**、**訂**用帳戶、**事件起始者**、**資源群組**、**資源類型**和**資源**值。

> [!TIP]
> 雖然我們在本主題中參考服務提供者和客戶，但[管理多個](../concepts/enterprise.md)租使用者的企業可以使用相同的程式。

## <a name="next-steps"></a>後續步驟

- 瞭解如何將客戶上架到[Azure 燈塔](../concepts/azure-delegated-resource-management.md)。
- 深入瞭解[Azure 監視器](../../azure-monitor/index.yml)和[Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md)。

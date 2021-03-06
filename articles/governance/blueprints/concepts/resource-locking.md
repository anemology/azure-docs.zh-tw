---
title: 了解資源鎖定
description: 瞭解 Azure 藍圖中的鎖定選項，以在指派藍圖時保護資源。
ms.date: 03/25/2020
ms.topic: conceptual
ms.openlocfilehash: 94ed8efd0d6c654cba129dfc69fbfe5add7a0824
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81383599"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>了解 Azure 藍圖中的資源鎖定

如果有一種機制可維護環境一致性，那麼大規模建立一貫化的環境才真正有用。 本文說明 Azure 藍圖中資源鎖定的運作方式。 若要查看資源鎖定和應用程式_拒絕指派_的範例，請參閱[保護新的資源](../tutorials/protect-new-resources.md)教學課程。

> [!NOTE]
> Azure 藍圖部署的資源鎖定僅適用于藍圖指派所部署的資源。 現有的資源（例如資源群組中已存在的資源）並未新增鎖定。

## <a name="locking-modes-and-states"></a>鎖定模式和狀態

鎖定模式適用于藍圖指派，它有三個選項： [**不要鎖定**]、[**唯讀**] 或 [**不要刪除**]。 鎖定模式會在藍圖指派期間的成品部署過程中進行設定。 您可以藉由更新藍圖指派來設定不同的鎖定模式。
不過，鎖定模式無法在 Azure 藍圖外部變更。

藍圖指派中成品所建立的資源具有四個狀態： [**未鎖定**]、[**唯讀**]、[**無法編輯]/[刪除**] 或 [**無法刪除**]。 每個成品類型都可以處於**未鎖定**狀態。 下表可用來判斷資源的狀態：

|[模式]|成品資源類型|State|描述|
|-|-|-|-|
|不要鎖定|*|未鎖定|資源不受 Azure 藍圖保護。 此狀態也可用於從藍圖指派外部新增至**唯讀**或**不要刪除**資源群組成品的資源。|
|唯讀|資源群組|無法編輯/刪除|此資源群組是唯讀的，而且無法修改資源群組上的標記。 **未鎖定**資源可以在這個資源群組中新增、移動、變更或刪除。|
|唯讀|非資源群組|唯讀|資源不能以任何方式更改：無法變更且無法刪除。|
|不要刪除|*|無法刪除|資源可以改變，但無法刪除。 **未鎖定**資源可以在這個資源群組中新增、移動、變更或刪除。|

## <a name="overriding-locking-states"></a>覆寫鎖定狀態

訂用帳戶中具有適當[角色型存取控制](../../../role-based-access-control/overview.md) (RBAC) 的人 (例如「擁有者」角色) 通常能夠變更或刪除任何資源。 當 Azure 藍圖套用鎖定作為已部署指派的一部分時，就不會發生這種存取。 如果指派是使用 [唯讀]**** 或 [不要刪除]**** 選項來設定的，則即使是訂用帳戶擁有者也無法在受保護的資源上執行封鎖的動作。

此安全性措施可保護已定義藍圖的一致性，以及設計為透過意外或以程式設計方式刪除或修改而建立的環境。

### <a name="assign-at-management-group"></a>在管理群組指派

防止訂用帳戶擁有者移除藍圖指派的另一個選項是將藍圖指派給管理群組。 在此案例中，**只有管理群組的擁有者**具有移除藍圖指派所需的許可權。

為了將藍圖指派給管理群組，而不是訂用帳戶，REST API 呼叫會變更如下：

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

定義的管理群組 `{assignmentMG}` 必須在管理群組階層內，或是與藍圖定義儲存所在的相同管理群組。

藍圖指派的要求主體看起來像這樣：

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

此要求主體中的主要差異，以及指派給訂用帳戶的索引鍵，都是 `properties.scope` 屬性。 此必要屬性必須設定為要套用藍圖指派的訂用帳戶。 訂用帳戶必須是儲存藍圖指派所在的管理群組階層的直接子系。

> [!NOTE]
> 指派給管理群組範圍的藍圖仍會以訂用帳戶層級藍圖指派的方式運作。 唯一的差異在於藍圖指派的儲存位置，讓訂用帳戶擁有者無法移除指派和相關聯的鎖定。

## <a name="removing-locking-states"></a>移除鎖定狀態

如果您需要修改或刪除指派所保護的資源，則可執行此動作的方式有兩種。

- 將藍圖指派更新為**未鎖定**的鎖定模式
- 刪除藍圖指派

移除指派時，會移除 Azure 藍圖所建立的鎖定。 不過，資源會留下來，而需要透過正常方式刪除。

## <a name="how-blueprint-locks-work"></a>藍圖鎖定的運作方式

如果指派選取了 [唯讀]**** 或 [不要刪除]**** 選項，則在藍圖指派期間，會將 RBAC [拒絕指派](../../../role-based-access-control/deny-assignments.md)的拒絕動作套用到成品資源。 拒絕動作會由藍圖指派的受控身分識別來新增，並且只能透過相同的受控身分識別從成品資源中移除。 此安全性措施會強制執行鎖定機制，並防止移除 Azure 藍圖外部的藍圖鎖定。

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="在資源群組上建立藍圖拒絕指派" border="false":::

每個模式的[拒絕指派屬性](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties)如下所示：

|[模式] |許可權。動作 |許可權。 NotActions |主體 [i]。型 |ExcludePrincipals [i]。號 | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|唯讀 |**\*** |**\*/read** |SystemDefined （每個人） |**excludedPrincipals**中的藍圖指派和使用者定義 |資源群組- _true_;資源- _false_ |
|不要刪除 |**\*/delete** | |SystemDefined （每個人） |**excludedPrincipals**中的藍圖指派和使用者定義 |資源群組- _true_;資源- _false_ |

> [!IMPORTANT]
> Azure Resource Manager 最多可快取 30 分鐘的角色指派詳細資料。 因此，藍圖資源上拒絕指派的拒絕動作可能不會立即完全生效。 在這段時間內，有可能會刪除藍圖鎖定所要保護的資源。

## <a name="exclude-a-principal-from-a-deny-assignment"></a>從拒絕指派中排除主體

在某些設計或安全性案例中，可能需要從藍圖指派所建立的[拒絕指派](../../../role-based-access-control/deny-assignments.md)中排除主體。 此步驟會在 REST API 中完成，方法是在[建立指派](/rest/api/blueprints/assignments/createorupdate)時，將最多五個值新增至 [**鎖定**] 屬性中的**excludedPrincipals**陣列。 下列指派定義是包含**excludedPrincipals**的要求主體範例：

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>從拒絕指派中排除動作

類似于在藍圖指派中的[拒絕指派](../../../role-based-access-control/deny-assignments.md)上[排除主體](#exclude-a-principal-from-a-deny-assignment)，您可以排除特定的[RBAC](../../../role-based-access-control/resource-provider-operations.md)作業。 在**屬性. 鎖定**區塊內，在**excludedPrincipals**為的相同位置中，可以新增**excludedActions** ：

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

雖然**excludedPrincipals**必須是明確的，但**excludedActions**專案可以利用 `*` 來進行 RBAC 作業的萬用字元比對。

## <a name="next-steps"></a>後續步驟

- 請遵循[保護新資源](../tutorials/protect-new-resources.md)教學課程。
- 了解[藍圖生命週期](lifecycle.md)。
- 了解如何使用[靜態與動態參數](parameters.md)。
- 了解如何自訂[藍圖排序順序](sequencing-order.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。
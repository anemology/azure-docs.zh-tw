---
title: 在 Azure 中跨租使用者共用資源庫映射
description: 瞭解如何使用共用映射資源庫跨 Azure 租使用者共用 VM 映射。
author: axayjo
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: fa4e9432a6acacf986a8e137b0b258abda5abb78
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502863"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>跨 Azure 租使用者共用資源庫 VM 映射

共用映射資源庫可讓您使用 RBAC 共用影像。 您可以使用 RBAC，在租使用者中共用映射，甚至是租使用者外部的個人。 如需這個簡單共用選項的詳細資訊，請參閱[共用資源庫](./shared-images-portal.md#share-the-gallery)。

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> 您無法使用入口網站，從另一個 azure 租使用者中的映射部署 VM。 若要從租使用者之間共用的映射建立 VM，您必須使用 Azure CLI 或[Powershell](../windows/share-images-across-tenants.md)。

## <a name="create-a-vm-using-azure-cli"></a>使用 Azure CLI 建立 VM

使用 appID、應用程式金鑰和租使用者1的識別碼，登入租使用者1的服務主體。 如有需要，您可以使用 `az account show --query "tenantId"` 來取得租使用者識別碼。

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
使用 appID、應用程式金鑰和租使用者2的識別碼，登入租使用者2的服務主體：

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

建立 VM。 以您自己的方式取代範例中的資訊。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>後續步驟

若遇到任何問題，您可以[針對共用映像資源庫問題進行疑難排解](troubleshooting-shared-images.md)。

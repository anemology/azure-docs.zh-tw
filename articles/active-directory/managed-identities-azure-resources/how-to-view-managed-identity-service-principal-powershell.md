---
title: 使用 PowerShell 來查看受控識別的服務主體-Azure AD
description: 逐步指導您如何使用 PowerShell 檢視受控識別的服務主體。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02750fc9f986b486deaf3c0d58ab538f9c634096
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608325"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>使用 PowerShell 檢視受控識別的服務主體

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您會了解如何使用 PowerShell 來檢視受控識別的服務主體。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。
- 如果您還沒有 Azure 帳戶，請[註冊免費帳戶](https://azure.microsoft.com/free/)。
- 啟用[虛擬機器](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)或[應用程式](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)上的系統指派身分識別。
- 安裝最新版本的[Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>檢視服務主體

下列命令示範如何檢視已啟用系統指派身分識別的 VM 或應用程式所具有的服務主體。 以您自己的值取代 `<VM or application name>`。

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>後續步驟

如需如何使用 PowerShell 檢視 Azure AD 服務主體的詳細資訊，請參閱 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)。



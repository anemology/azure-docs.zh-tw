---
title: Azure 防火牆 SNAT 私人 IP 位址範圍
description: 您可以設定 SNAT 的 IP 位址範圍。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: be2bf0f9590a23f9def44a1800338c80f69a782c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610518"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure 防火牆 SNAT 私人 IP 位址範圍

Azure 防火牆會為公用 IP 位址的所有輸出流量提供自動 SNAT。 根據預設，當目的地 IP 位址位於每個[IANA RFC 1918](https://tools.ietf.org/html/rfc1918)的私人 ip 位址範圍時，Azure 防火牆不會有網路規則的 SNAT。 應用程式規則一律會使用[透明 proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy)來套用，而不論目的地 IP 位址為何。

當您直接將流量路由傳送到網際網路時，此邏輯就能順利運作。 不過，如果您已啟用[強制通道](forced-tunneling.md)，則會將網際網路系結流量 Snat 轉譯至 AzureFirewallSubnet 中的其中一個防火牆私人 IP 位址，並將來源從內部部署防火牆中隱藏。

如果您的組織使用私人網路的公用 IP 位址範圍，Azure 防火牆會將流量 SNAT 轉譯到 AzureFirewallSubnet 其中一個防火牆私人 IP 位址。 不過，您可以將 Azure 防火牆設定為**不**會 SNAT 公用 IP 位址範圍。

若要將 Azure 防火牆設定為永不使用 SNAT，而不論目的地 IP 位址為何，請使用**0.0.0.0/0**作為私人 ip 位址範圍。 使用此設定時，Azure 防火牆永遠不會將流量直接路由傳送到網際網路。 若要將防火牆設定為一律為 SNAT，而不論目的地位址為何，請使用**255.255.255.255/32**作為私人 IP 位址範圍。

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>設定 SNAT 私人 IP 位址範圍-Azure PowerShell

您可以使用 Azure PowerShell 來指定防火牆的私人 IP 位址範圍。

### <a name="new-firewall"></a>新防火牆

針對新的防火牆，Azure PowerShell 命令為：

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges 會擴充至 Azure 防火牆上的目前預設值，而其他範圍則會加入其中。 若要在您的私用範圍規格中保留 IANAPrivateRanges 預設值，它必須保留在您 `PrivateRange` 的規格中，如下列範例所示。

如需詳細資訊，請參閱[AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0)。

### <a name="existing-firewall"></a>現有的防火牆

若要設定現有的防火牆，請使用下列 Azure PowerShell 命令：

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>範本

您可以將下列內容新增至 `additionalProperties` 區段：

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>設定 SNAT 私人 IP 位址範圍-Azure 入口網站

您可以使用 Azure 入口網站來指定防火牆的私人 IP 位址範圍。

1. 選取您的資源群組，然後選取您的防火牆。
2. 在 [**總覽**] 頁面的 [**私人 IP 範圍**] 中，選取預設值**IANA RFC 1918**。

   [**編輯私人 IP 首碼**] 頁面隨即開啟：

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="編輯私人 IP 首碼":::

1. 根據預設，會設定**IANAPrivateRanges** 。
2. 編輯您環境的私人 IP 位址範圍，然後選取 [**儲存**]。

## <a name="next-steps"></a>後續步驟

- 瞭解[Azure 防火牆強制通道](forced-tunneling.md)。
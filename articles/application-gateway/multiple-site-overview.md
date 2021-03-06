---
title: 在 Azure 應用程式閘道上裝載多個網站
description: 本文提供「Azure 應用程式閘道」多站台支援的概觀。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: surmb
ms.topic: conceptual
ms.openlocfilehash: 53f6f37454de886934a483b40daad24204958baf
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87474320"
---
# <a name="application-gateway-multiple-site-hosting"></a>應用程式閘道多站台裝載

多網站裝載可讓您在應用程式閘道的相同埠上設定多個 web 應用程式。 此功能可讓您將 100 多個網站新增到一個應用程式閘道，為您的部署設定更有效率的拓撲。 每個網站都可以導向到自己的後端集區。 例如，contoso.com、fabrikam.com 和 adatum.com 三個網域都指向應用程式閘道的 IP 位址。 您會建立三個多網站接聽程式，並針對個別的連接埠和通訊協定設定來設定每個接聽程式。 

您也可以在多網站接聽程式中定義萬用字元主機名稱，並為每個接聽程式定義最多 5 個主機名稱。 若要深入瞭解，請參閱接聽程式[中的萬用字元主機名稱](#wildcard-host-names-in-listener-preview)。

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="多網站應用程式閘道":::

> [!IMPORTANT]
> 規則會依照其列于 v1 SKU 入口網站中的順序進行處理。 針對 v2 SKU，完全相符的優先順序較高。 強烈建議纖設定多站台接聽程式，再設定基本接聽程式。  這可確保流量路由傳送到右邊後端。 如果先列出了基本接聽程式，且該接聽程式符合傳入的要求，就會由該接聽程式處理。

對 `http://contoso.com` 的要求會路由傳送至 ContosoServerPool，而對 `http://fabrikam.com` 的要求則會路由傳送至 FabrikamServerPool。

同樣地，您可以在相同的應用程式閘道部署上裝載相同父系網域的多個子域。 例如，您可以 `http://blog.contoso.com` `http://app.contoso.com` 在單一應用程式閘道部署上裝載和。

## <a name="wildcard-host-names-in-listener-preview"></a>接聽程式中的萬用字元主機名稱（預覽）

應用程式閘道允許使用多網站 HTTP （S）接聽程式進行以主機為基礎的路由。 現在，您可以在主機名稱中使用萬用字元（例如星號（*）和問號（？）），以及每個多網站 HTTP （S）接聽程式最多5個主機名稱。 例如： `*.contoso.com` 。

在主機名稱中使用萬用字元，您可以在單一接聽程式中比對多個主機名稱。 例如， `*.contoso.com` 可以比對 `ecom.contoso.com` ，以及 `b2b.contoso.com` `customer1.b2b.contoso.com` 和等。 使用主機名稱陣列，您可以為接聽程式設定一個以上的主機名稱，以將要求路由至後端集區。 例如， `contoso.com, fabrikam.com` 接聽程式可以包含接受這兩個主機名稱的要求。

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="萬用字元接聽程式":::

>[!NOTE]
> 這項功能目前為預覽狀態，僅適用于應用程式閘道的 Standard_v2 和 WAF_v2 SKU。 若要深入瞭解預覽，請參閱[這裡的使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

>[!NOTE]
>這項功能目前僅透過[Azure PowerShell](tutorial-multiple-sites-powershell.md)和[Azure CLI](tutorial-multiple-sites-cli.md)提供。 即將推出入口網站支援。
> 請注意，由於入口網站支援並非完全可用，因此，如果您只使用主機名稱參數，接聽程式會在入口網站中顯示為基本接聽程式，而接聽程式清單視圖的 [主機名稱] 欄則不會顯示已設定的主機名稱。 對於萬用字元接聽程式的任何變更，請務必使用 Azure PowerShell 或 CLI，直到入口網站支援該功能為止。

在[Azure PowerShell](tutorial-multiple-sites-powershell.md)中，您必須使用， `-HostNames` 而不是 `-HostName` 。 使用主機名稱時，您最多可以提及5個主機名稱做為逗號分隔值，並使用萬用字元。 例如， `-HostNames "*.contoso.com,*.fabrikam.com"`

在[Azure CLI](tutorial-multiple-sites-cli.md)中，您必須使用， `--host-names` 而不是 `--host-name` 。 使用主機名稱時，您最多可以提及5個主機名稱做為逗號分隔值，並使用萬用字元。 例如， `--host-names "*.contoso.com,*.fabrikam.com"`

### <a name="allowed-characters-in-the-host-names-field"></a>[主機名稱] 欄位中允許的字元：

* `(A-Z,a-z,0-9)`-英數位元
* `-`-連字號或減號
* `.`-period 作為分隔符號
*   `*`-可以符合允許範圍中的多個字元
*   `?`-可以與允許範圍中的單一字元相符

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>在接聽程式中使用萬用字元和多個主機名稱的條件：

*   您最多隻能在單一接聽程式中提及5個主機名稱
*   星號只能 `*` 在網域樣式名稱或主機名稱的元件中提及一次。 例如，component1 *. component2*. component3。 `(*.contoso-*.com)`有效。
*   主機名稱中最多隻能有兩個星號 `*` 。 例如， `*.contoso.*` 是有效的，而且 `*.contoso.*.*.com` 無效。
*   主機名稱中最多隻能有4個萬用字元。 例如， `????.contoso.com` `w??.contoso*.edu.*` 是有效的，但無效 `????.contoso.*` 。
*   `*` `?` 在主機名稱（或或）的元件中一起使用星號和 `*?` 問號 `?*` `**` 是不正確。 例如， `*?.contoso.com` 和 `**.contoso.com` 無效。

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>在接聽程式中使用萬用字元或多個主機名稱的考慮和限制：

*   [Ssl 終止和端對端 ssl](ssl-overview.md)會要求您將通訊協定設定為 HTTPS，並上傳要在接聽程式設定中使用的憑證。 如果它是多網站接聽程式，您也可以輸入主機名稱，這通常是 SSL 憑證的 CN。 當您在接聽程式中指定多個主機名稱或使用萬用字元時，您必須考慮下列事項：
    *   如果它是萬用字元主機名稱（例如 *. contoso.com），您必須上傳具有 CN 的萬用字元憑證，例如 *. contoso.com
    *   如果相同接聽程式中提及多個主機名稱，您必須上傳 SAN 憑證（主體替代名稱）與與所述主機名稱相符的 Cn。
*   您不能使用正則運算式來提及主機名稱。 您只能使用星號（*）和問號（？）之類的萬用字元來形成主機名稱模式。
*   針對後端健康情況檢查，您無法為每個 HTTP 設定建立多個[自訂探查](application-gateway-probe-overview.md)的關聯。 相反地，您可以在後端探查其中一個網站，或使用 "127.0.0.1" 來探查後端伺服器的 localhost。 不過，當您在接聽程式中使用萬用字元或多個主機名稱時，將會根據規則類型（基本或路徑型），將所有指定網域模式的要求路由傳送至後端集區。
*   屬性「主機名稱」接受一個字串做為輸入，您可以在其中只提及一個非萬用字元功能變數名稱，而「主機名稱」接受字串陣列做為輸入，您可以在其中提及最多5個萬用字元功能變數名稱。 但這兩個屬性一次不能使用。
*   您無法使用使用萬用字元或多個主機名稱的目標接聽程式來建立重新[導向規則。](redirect-overview.md)

如需如何在多網站接聽程式中設定萬用字元主機名稱的逐步指南，請參閱[使用 Azure PowerShell 建立多網站](tutorial-multiple-sites-powershell.md)或[使用 Azure CLI](tutorial-multiple-sites-cli.md) 。

## <a name="host-headers-and-server-name-indication-sni"></a>主機標頭和伺服器名稱指示 (SNI)

有三個常見的機制可允許在相同的基礎結構上進行多站台裝載。

1. 將多個 Web 應用程式分別裝載在一個唯一的 IP 位址上。
2. 使用主機名稱將多個 Web 應用程式裝載在相同的 IP 位址上。
3. 使用不同的連接埠將多個 Web 應用程式裝載在相同的 IP 位址上。

目前應用程式閘道支援用來接聽流量的單一公用 IP 位址。 因此，目前不支援多個應用程式，每個都有自己的 IP 位址。 

應用程式閘道支援多個應用程式在不同的埠上接聽，但此案例需要應用程式接受非標準埠上的流量。 這通常不是您想要的設定。

「應用程式閘道」需依賴 HTTP 1.1 主機標頭，才能在相同的公用 IP 位址和連接埠上裝載多個網站。 裝載在應用程式閘道上的網站也可以支援具有伺服器名稱指示（SNI） TLS 擴充功能的 TLS 卸載。 此案例表示用戶端瀏覽器和後端 Web 伺服陣列必須支援 RFC 6066 中所定義的 HTTP/1.1 和 TLS 擴充功能。

## <a name="next-steps"></a>後續步驟

瞭解如何在應用程式閘道中設定多個網站裝載
* [使用 Azure 入口網站](create-multiple-sites-portal.md)
* [使用 Azure PowerShell](tutorial-multiple-sites-powershell.md) 
* [使用 Azure CLI](tutorial-multiple-sites-cli.md)

您可以瀏覽[使用多站台裝載的 Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting)，以了解以範本為基礎的端對端部署。

---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: ff54d60573fbc7b6694b8d02d1378869674c1e81
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050329"
---
這項功能很容易設定，但這並不表示您的體驗會是免費的問題。 如果您在存取所需的端點時遇到問題，您可以使用一些公用程式來測試應用程式主控台的連線能力。 有兩個您可以使用的主控台。 其中一個是 Kudu 主控台，另一個則是 Azure 入口網站中的主控台。 若要從您的應用程式連接到 Kudu 主控台，請移至 [**工具**] [  >  **Kudu**]。 您也可以連線到位於 [sitename]. azurewebsites. net 的 Kudo 主控台。 在網站載入之後，移至 [**調試主控台**] 索引標籤。若要從您的應用程式進入 Azure 入口網站裝載的主控台，請移至 [**工具**] [  >  **主控台**]。

#### <a name="tools"></a>工具
由於安全性限制，工具**ping**、 **nslookup**和**tracert**無法透過主控台來處理。 為了填滿 void，會新增兩個不同的工具。 為了測試 DNS 功能，我們新增了名為**nameresolver.exe**的工具。 語法為：

```console
nameresolver.exe hostname [optional: DNS Server]
```

您可以使用 nameresolver 來檢查應用程式依賴的主機名稱。 如此一來，您可以測試您的 DNS 是否有任何設定錯誤，或可能無法存取您的 DNS 伺服器。 您可以查看您的應用程式在主控台中使用的 DNS 伺服器，方法是查看環境變數 WEBSITE_DNS_SERVER 並 WEBSITE_DNS_ALT_SERVER。

您可以使用下一個工具來測試對主機和埠組合的 TCP 連線能力。 此工具稱為 **tcpping**，語法如下：

```console
tcpping.exe hostname [optional: port]
```

**Tcpping**公用程式會告訴您是否可以連線到特定的主機和埠。 只有當應用程式接聽主機和埠組合，而且有從您的應用程式到指定主機和埠的網路存取權時，才會顯示成功。

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>對虛擬網路託管資源的調試存取
有幾件事可能會讓您的應用程式無法到達特定的主機和埠。 在大部分的情況中，這是其中一項：

* **路線中有防火牆。** 如果您有防火牆，就會達到 TCP 超時。 此案例中 TCP 逾時值就是 21 秒。 使用 **tcpping** 工具來測試連線能力。 TCP 超時可能是來自防火牆以外的許多專案所造成，但從這裡開始。
* **DNS 無法存取。** DNS 的超時時間為每個 DNS 伺服器3秒。 如果您有兩部 DNS 伺服器，逾時為 6 秒。 使用 nameresolver 來檢查 DNS 是否運作。 您不能使用 nslookup，因為它不會使用您的虛擬網路所設定的 DNS。 如果無法存取，您可能會有防火牆或 NSG 封鎖對 DNS 的存取，或可能已關閉。

如果這些專案不會回答您的問題，請先查看如下所示的內容：

**區域 VNet 整合**
* 您的目的地是否為非 RFC1918 位址，而且您沒有 WEBSITE_VNET_ROUTE_ALL 設為1？
* 是否有 NSG 封鎖來自您整合子網的輸出？
* 如果您要跨越 Azure ExpressRoute 或 VPN，您的內部部署閘道是否已設定為將流量備份至 Azure？ 如果您可以連接虛擬網路中的端點，但不能連線到內部部署，請檢查您的路由。
* 您有足夠的許可權可設定整合子網上的委派嗎？ 在區域 VNet 整合設定期間，您的整合子網會委派給 Microsoft Web。 VNet 整合 UI 會自動將子網委派給 Microsoft Web。 如果您的帳戶沒有足夠的網路許可權來設定委派，您將需要可在您的整合子網上設定屬性以委派子網的人員。 若要手動委派整合子網，請移至 Azure 虛擬網路子網 UI，並設定 Microsoft 的委派。

**閘道-必要的 VNet 整合**
* RFC 1918 範圍中的點對站位址範圍（10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255）？
* 閘道會在入口網站中顯示為已啟動嗎？ 如果您的閘道已關閉，請重新啟動。
* 憑證是否顯示為同步，或您是否懷疑網路設定已變更？  如果您的憑證未同步，或您懷疑未與 Asp 同步的虛擬網路設定已變更，請選取 [**同步網路**]。
* 如果您要跨越 VPN，內部部署閘道是否已設定為將流量備份至 Azure？ 如果您可以連接虛擬網路中的端點，但不能連線到內部部署，請檢查您的路由。
* 您嘗試使用同時支援點對站和 ExpressRoute 的共存閘道嗎？ VNet 整合不支援共存閘道。

偵錯工具網路問題是一項挑戰，因為您看不到對特定主機的封鎖存取：埠組合。 部分原因包括：

* 您的主機上已啟動防火牆，以防止從點對站 IP 範圍存取應用程式埠。 跨子網通常需要公用存取權。
* 您的目標主機已關閉。
* 您的應用程式已關閉。
* 您的 IP 或主機名稱不正確。
* 您的應用程式正在接聽的埠與您所預期的不同。 您可以在端點主機上使用 "netstat -aon"，將處理序識別碼與接聽連接埠進行比對。
* 您的網路安全性群組設定時，會防止從點對站 IP 範圍存取您的應用程式主機和埠。

您不知道應用程式實際使用的位址。 它可以是整合子網或點對站位址範圍中的任何位址，因此您需要允許來自整個位址範圍的存取。

其他偵錯步驟包括：

* 連線至虛擬網路中的 VM，並嘗試從該處連線到您的資源主機：埠。 若要測試 TCP 存取，請使用 PowerShell 命令 **test-netconnection**。 語法為：

```powershell
test-netconnection hostname [optional: -Port]
```

* 在 VM 上啟動應用程式，並使用**tcpping**從您的應用程式中的主控台測試該主機和埠的存取。

#### <a name="on-premises-resources"></a>內部部署資源 ####

如果您的應用程式無法連線到內部部署資源，請檢查您是否可以從虛擬網路連線到資源。 使用 **test-netconnection** PowerShell 命令來檢查是否有 TCP 存取權。 如果您的 VM 無法連線到您的內部部署資源，您的 VPN 或 ExpressRoute 連線可能未正確設定。

如果您的虛擬網路裝載 VM 可以連線到您的內部部署系統，但您的應用程式無法連線，原因可能是下列其中一個原因：

* 您的路由未設定您的子網或內部部署閘道中的點對站位址範圍。
* 您的網路安全性群組會封鎖點對站 IP 範圍的存取。
* 您的內部部署防火牆會封鎖來自點對站 IP 範圍的流量。
* 您正嘗試使用區域 VNet 整合功能來達到非 RFC 1918 位址。
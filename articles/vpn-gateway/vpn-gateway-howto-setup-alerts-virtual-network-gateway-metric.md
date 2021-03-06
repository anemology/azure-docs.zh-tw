---
title: 在 Azure VPN 閘道計量上設定警示
description: 瞭解如何使用 Azure 入口網站，根據虛擬網路 VPN 閘道的計量來設定 Azure 監視器警示。
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 14bb407cb12e24ca789085e954aaabff2333da7b
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88033490"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>設定 VPN 閘道計量的警示

本文可協助您設定 Azure VPN 閘道計量的警示。 Azure 監視器提供設定 Azure 資源警示的功能。 您可以為「VPN」類型的虛擬網路閘道設定警示。


|**計量**   | **單位** | **細微度** | **說明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 位元組/秒  | 5 分鐘| 閘道上所有站對站連線的平均結合頻寬使用量。     |
|**P2SBandwidth**| 位元組/秒  | 1 分鐘  | 閘道上所有點對站連線的平均結合頻寬使用量。    |
|**P2SConnectionCount**| 計數  | 1 分鐘  | 閘道上的點對站連線計數。   |
|**TunnelAverageBandwidth** | 位元組/秒    | 5 分鐘  | 在閘道上建立之通道的平均頻寬使用量。 |
|**TunnelEgressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連出流量。   |
|**TunnelEgressPackets** | 計數 | 5 分鐘 | 在閘道上建立之通道上的傳出封包計數。   |
|**TunnelEgressPacketDropTSMismatch** | 計數 | 5 分鐘 | 流量選取器不相符所造成的通道上丟棄的傳出封包計數。 |
|**TunnelIngressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連入流量。   |
|**TunnelIngressPackets** | 計數 | 5 分鐘 | 在閘道上建立的通道上的傳入封包計數。   |
|**TunnelIngressPacketDropTSMismatch** | 計數 | 5 分鐘 | 因流量選取器不相符而在通道上捨棄的傳入封包計數。 |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>使用 Azure 入口網站設定以度量為基礎的 Azure 監視器警示

下列範例步驟會在的閘道上建立警示：

- **度量：** TunnelAverageBandwidth
- **條件：** 頻寬 > 10 個位元組/秒
- **視窗：** 5 分鐘
- **警示動作：** 電子郵件



1. 移至虛擬網路閘道資源，然後從 [**監視**] 索引標籤選取 [**警示**]。然後建立新的警示規則，或編輯現有的警示規則。

   ![建立警示規則的選取專案](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "建立")

2. 選取您的 VPN 閘道作為資源。

   ![資源清單中的 [選取] 按鈕和 [VPN 閘道]](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "選取")

3. 選取要為警示設定的計量。

   ![計量清單中選取的度量](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "選取")
4. 設定信號邏輯。 其中有三個元件：

    a. **維度**：如果計量具有維度，您可以選取特定維度值，讓警示只會評估該維度的資料。 這些是選擇性的。

    b. **條件**：這是用來評估度量值的作業。

    c. **時間**：指定計量資料的細微性，以及評估警示的時間長度。

   ![設定信號邏輯的詳細資料](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "選取")

5. 若要查看已設定的規則，請選取 [**管理警示規則**]。

   ![用於管理警示規則的按鈕](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "選取")

## <a name="next-steps"></a>後續步驟

若要設定通道資源記錄的警示，請參閱[在 VPN 閘道資源記錄上設定警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。

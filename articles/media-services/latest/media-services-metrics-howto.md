---
title: 使用 Azure 監視器來查看計量
description: 本文說明如何使用 Azure 入口網站圖表和 Azure CLI 監視計量。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.custom: devx-track-azurecli
ms.openlocfilehash: 154e5b5d9639203810e9d16dec4e2907fe5ee80a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504291"
---
# <a name="monitor-media-services-metrics"></a>監視媒體服務計量

[Azure 監視器](../../azure-monitor/overview.md)可讓您監視計量和診斷記錄，以協助您瞭解應用程式的執行狀況。 如需這項功能的詳細描述，以及查看為何要使用 Azure 媒體服務計量和診斷記錄，請參閱[監視媒體服務計量和診斷記錄](media-services-metrics-diagnostic-logs.md)。

Azure 監視器提供數種與計量互動的方式，包括在入口網站中繪製圖表、透過 REST API 存取它們，或使用 Azure CLI 來查詢它們。 本文說明如何使用 Azure 入口網站圖表和 Azure CLI 監視計量。

## <a name="prerequisites"></a>必要條件

- [建立媒體服務帳戶](./create-account-howto.md)
- 審查[監視器媒體服務計量和診斷記錄](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>查看 Azure 入口網站中的計量

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 流覽至您的 Azure 媒體服務帳戶，然後選取 [**計量**]。
1. 按一下 [**資源**] 方塊，然後選取您要監視計量的資源。

    [**選取資源**] 視窗會出現在右側，並顯示您可以使用的資源清單。 在此情況下，您會看到：

    * &lt;媒體服務帳戶名稱&gt;
    * &lt;媒體服務帳戶名稱 &gt; / &lt; 串流端點名稱&gt;
    * &lt;儲存體帳戶名稱&gt;

    選取資源，**然後按 [** 套用]。 如需支援的資源和計量的詳細資訊，請參閱[監視媒體服務計量](media-services-metrics-diagnostic-logs.md)。

    ![計量](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > 若要在您想要監視計量的資源之間切換，請再次按一下**資源**箱並重複此步驟。
1. （選擇性）為您的圖表命名（按頂端的鉛筆來編輯名稱）。
1. 新增您想要查看的度量。

    ![計量](media/media-services-metrics/metrics03.png)
1. 您可以將圖表釘選到儀表板。

## <a name="view-metrics-with-azure-cli"></a>使用 Azure CLI 來查看計量

若要使用 Azure CLI 取得「輸出」計量，您可以執行下列 `az monitor metrics` 命令：

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

若要取得其他計量，請將您感興趣的度量名稱替換為「輸出」。

## <a name="see-also"></a>另請參閱

* [Azure 監視器計量](../../azure-monitor/platform/data-platform.md)
* [使用 Azure 監視器建立、查看及管理計量警示](../../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>後續步驟

[診斷記錄](media-services-diagnostic-logs-howto.md)

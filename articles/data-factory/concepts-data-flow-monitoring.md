---
title: 監視對應資料流程
description: 如何在 Azure Data Factory 中以視覺化方式監視對應的資料流程
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/03/2020
ms.openlocfilehash: ad5fb077596b6cc444c1c4df81af32cbcfa7c1db
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475544"
---
# <a name="monitor-data-flows"></a>監視資料流程

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在您完成建置和偵錯資料流程之後，您想要將資料流程排定為在管線內容中依照排程執行。 您可以使用觸發程從 Azure Data Factory 進行管線排程。 或者，可以從 Azure Data Factory Pipeline Builder 使用 [立即觸發] 選項來執行單程執行，以在管線內容中測試您的資料流程。

當您執行您的管線時，您可以監視管線和管線內含的所有活動，包括「資料流程」活動。 按一下左側 Azure Data Factory UI 面板中的監視器圖示。 您可以看到類似以下的畫面。 醒目提示的圖示可讓您向下鑽研到管線中的活動，包括「資料流程」活動。

![資料流程監視](media/data-flow/mon001.png "資料流程監視")

您也會看到此層級的統計資料，包括執行時間和狀態。 活動層級的執行識別碼與管線層級的執行識別碼不同。 前一層級的執行識別碼適用於管線。 選取眼鏡可讓您深入了解資料流程執行的詳細資料。

![資料流程監視](media/data-flow/monitoring-details.png "資料流程監視")

當您在圖形化節點監視檢視時，您可以看到經過簡化的僅供檢視資料流程圖版本。

![資料流程監視](media/data-flow/mon003.png "資料流程監視")

以下影片概述從 ADF 監視畫面監視資料流程的效能：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>檢視資料流程執行計劃

在 Spark中執行資料流程時，Azure Data Factory 會根據資料流程的整體性決定最佳的程式碼路徑。 此外，執行路徑可能會發生在不同的相應放大節點和資料分割區上。 因此，監視圖表示您的流程設計，並將轉換的執行路徑納入考量。 當您選取個別節點時，您可以看到「群組」，其代表在叢集上一起執行的程式碼。 您看見的時間和計數代表這些群組，而不是您設計中的個別步驟。

![資料流程監視](media/data-flow/mon004.png "資料流程監視")

* 當您選取監視視窗中的空白處時，底端窗格中的統計資料會顯示每個接收的時間和資料列計數，以及導向轉換歷程接收資料的轉換。

* 當您選取個別的轉換時，您會在右側面板上收到其他意見反應，其中顯示分割區統計資料、資料行計數、偏態 (資料平均分佈於分割區的程度) 和峰態 (資料的上升程度)。

* 當您選取節點檢視中的 [接收] 時，您可以看到資料行歷程。 有三種不同方法可讓資料行累積於資料流程以置入接收中。 其中包括：

  * 計算：您使用資料行進行條件式處理，或使用於資料流程中的運算式內，但請勿將它置入接收中
  * 衍生：資料行是您在流程中產生的新資料行，也就是不存在於來源
  * 對應：資料行源自來源，而您會將它對應至接收欄位
  * 資料流程狀態：執行的目前狀態
  * 叢集啟動時間：為您的資料流程執行取得 JIT Spark 計算環境的時間量
  * 轉換數目：在您的流程中執行多少個轉換步驟
  
![資料流程監視](media/data-flow/monitornew.png "資料流程監視 (新增)")

## <a name="total-sink-processing-time-vs-transformation-processing-time"></a>總接收處理時間與轉換處理時間

每個轉換階段都會包含該階段完成的總時間，並將每個分割區執行時間匯總在一起。 當您按一下接收器時，您會看到「接收處理時間」。 這一次包含轉換時間的總計，*加上*將資料寫入目的地存放區所花費的 i/o 時間。 接收處理時間和轉換總計之間的差異是寫入資料的 i/o 時間。

如果您在 [ADF 管線監視] 視圖中開啟資料流程活動的 JSON 輸出，您也可以查看每個分割區轉換步驟的詳細計時。 JSON 會包含每個分割區的毫秒時間，而 UX 監視視圖則是一種新增的磁碟分割的匯總時機：

```
 {
     "stage": 4,
     "partitionTimes": [
          14353,
          14914,
          14246,
          14912,
          ...
         ]
}
```
  
## <a name="monitor-icons"></a>監視器圖示

此圖示表示已在叢集上快取轉換資料，所以時間和執行路徑已將該點納入考量：

![資料流程監視](media/data-flow/mon005.png "資料流程監視")

您也會在轉換中看到綠色圓圈圖示。 這些圖示代表資料流入的接收數目。

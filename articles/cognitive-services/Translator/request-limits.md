---
title: 要求限制-Translator
titleSuffix: Azure Cognitive Services
description: 本文列出 Translator 的要求限制。 費用是根據字元計數來產生，而不是根據受限於每個要求 5,000 個字元的要求頻率。 字元限制是以訂用帳戶為基礎，而 F0 受限於每小時 2 百萬個字元。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: swmachan
ms.openlocfilehash: 9ce2ea7da562372e5c1c25d4c0fe9ab410b02a1d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903400"
---
# <a name="request-limits-for-translator"></a>Translator 的要求限制

本文提供翻譯工具的節流限制。 服務包括翻譯、直譯、句子長度偵測、語言偵測和替代翻譯。

## <a name="character-and-array-limits-per-request"></a>每個要求的字元和陣列限制

每個轉譯要求的限制為10000個字元，涵蓋您要轉譯的目的語言。 例如，傳送3000個字元的翻譯要求轉譯成3種不同的語言，會導致要求大小為 3000x3 = 9000 個字元，以滿足要求限制。 系統會依照每個字元收費，而不是依照要求數目收費。 建議您傳送較短的要求。

下表列出每個轉譯程式作業的陣列元素和字元限制。

| 作業 | 陣列元素的大小上限 |    陣列元素的最大數目 |    要求大小上限 (個字元)  |
|:----|:----|:----|:----|
| Translate | 10,000    | 100   | 10,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10,000 | 100 |   50,000 |
| BreakSentence | 50,000    | 100 | 50,000 |
| 字典查閱| 100 |  10  | 1,000 |
| 字典範例 | 100用於翻譯的文字和 100 (200 total) | 10|   2,000 |

## <a name="character-limits-per-hour"></a>每小時的字元限制

每小時的字元限制是以您的 Translator 訂用帳戶層為基礎。 

每小時的配額應該在一小時內平均耗用。 例如，在每小時2000000個字元的 F0 層限制中，字元的取用速度不能超過大約每分鐘33300個字元的滑動視窗 (2000000 個字元除以60分鐘的) 。

如果您達到或超過這些限制，或在短時間內傳送過多的配額部分，您可能會收到超出配額的回應。 並行要求沒有任何限制。

| 階層 | 字元限制 |
|------|-----------------|
| F0 | 每小時 2 百萬個字元 |
| S1 | 每小時 4 千萬個字元 |
| S2/C2 | 每小時 4 千萬個字元 |
| S3/C3 | 每小時 1 億 2000 萬個字元 |
| S4/C4 | 每小時 2 億個字元 |

[多服務訂閱](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)的限制與 S1 層相同。

這些限制僅限於 Microsoft 的標準翻譯模型。 使用自訂翻譯的自訂翻譯模型的每秒限制為1800個字元。

## <a name="latency"></a>Latency

在使用自訂模型時，翻譯工具的延遲上限為15秒，使用標準模型和120秒。 一般來說， *100 字元內文字*的回應會以150毫秒為單位傳回300毫秒。 自訂翻譯工具模型在持續的要求速率上具有類似的延遲特性，而且當要求速率為間歇性時可能會有較高的延遲。 回應時間會根據要求和語言組的大小而有所不同。 如果您未在該時間範圍內收到翻譯或[錯誤回應](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)，請檢查您的程式碼、您的網路連接，然後重試。 

## <a name="sentence-length-limits"></a>句子長度限制

使用 [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) 函式時，句子長度受限於 275 個字元。 下列語言有一些例外狀況：

| 語言 | 程式碼 | 字元限制 |
|----------|------|-----------------|
| 中文 | zh | 166 |
| 德文 | de | 800 |
| 義大利文 | it | 800 |
| 日文 | ja | 166 |
| 葡萄牙文 | pt | 800 |
| 西班牙文 | es | 800 |
| 泰文 | th | 180 |

> [!NOTE]
> 這項限制不適用於翻譯。

## <a name="next-steps"></a>後續步驟

* [定價](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [區域可用性](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 翻譯工具參考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

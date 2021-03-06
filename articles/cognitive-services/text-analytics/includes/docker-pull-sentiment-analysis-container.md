---
title: 情感分析容器的 Docker pull
titleSuffix: Azure Cognitive Services
description: 情感分析容器的 Docker pull 命令
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588363"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>情感分析 v3 容器的 Docker pull

情感分析容器 v3 容器有數種語言版本。 若要下載英文容器的容器，請使用下列命令。 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
```

若要下載其他語言的容器，請 `en` 使用下列其中一個語言代碼來取代。 

| 文字分析容器 | 語言代碼 |
|--|--|
| 英文 | `en` |
| 西班牙文 | `es` |
| 法文 | `fr` |
| 義大利文 | `it` |
| 德文 | `de` |
| 中文-簡體 | `zh` |
| 中文-繁體 | `zht` |
| 日文 | `ja` |
| 葡萄牙文 | `pt` |
| 荷蘭文 | `nl` |

如需文字分析容器可用標記的完整描述，請參閱[Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654)。
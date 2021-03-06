---
title: 一般命名實體
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 32e80c50ff6f543679852cbd7e5ce9bda92d01e1
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140908"
---
將要求傳送至端點時，會傳回下列實體類別 `/entities/recognition/general` 。

| 類別   | 子類別 | 描述                          | 啟動模型版本                                                    | 備忘錄 |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| 個人     | N/A         | 人員的名稱。  | `2019-10-01`  | NER v 2.1 也會傳回 |
| PersonType | N/A         | 人員所持有的作業類型或角色。 | `2020-02-01` | |
|Location    | N/A         | 自然和人為地標、結構、地理特徵和地緣政治實體     |  `2019-10-01` | NER v 2.1 也會傳回 |
|Location     | 地緣政治實體（GPE）        | 城市、國家/地區、州/省。      | `2020-02-01` | |
|Location     | Structural                       | 人工結構。 | `2020-04-01` | |
|Location     | 分散       | 地理和自然的功能，例如河流、海洋和 deserts。 |  `2020-04-01` | |
|組織  | N/A | 公司、政治群組、音樂波段、運動俱樂部、政府機構和公用組織。  | `2019-10-01` | Nationalities 和 religions 不包含在此實體類型中。 NER v 2.1 也會傳回 |
|組織 | 醫療 | 醫療公司和群組。 | `2020-04-01` |  |
|組織 | 股票交換 | 股票 exchange 群組。 | `2020-04-01` | |
| 組織 | 運動 | 運動相關組織。 | `2020-04-01` |  |
| 事件  | N/A | 歷程記錄、社交和自然發生的事件。 | `2020-02-01` |  |
| 事件  | 方面 | 文化活動和假日。 | `2020-04-01` | |
| 事件  | Natural | 自然發生的事件。 | `2020-04-01` |  |
| 事件  | 運動 | 運動事件。  | `2020-04-01` | |
| 產品 | N/A | 各種類別目錄的實體物件。 | `2020-02-01` | |
| 產品 | 計算產品 | 計算產品。 |  `2020-02-01 ` | |
| 技能 | N/A | 功能、技能或專長。 | `2020-02-01` |  |
| 位址 | N/A | 完整郵寄地址。  | `2020-04-01` |  |
| PhoneNumber | N/A | 電話號碼（僅限美國和 EU 電話號碼）。 | `2019-10-01` | NER v 2.1 也會傳回 |
| 電子郵件 | N/A | 電子郵件地址。 | `2019-10-01` | NER v 2.1 也會傳回 |
| URL | N/A | 網站的 Url。 | `2019-10-01` | NER v 2.1 也會傳回  |
| IP | N/A | 網路 IP 位址。 | `2019-10-01` | NER v 2.1 也會傳回 |
| Datetime | N/A | 當日的日期和時間。 | `2019-10-01` | NER v 2.1 也會傳回 | 
| Datetime | 日期 | 行事曆日期。 | `2019-10-01` | NER v 2.1 也會傳回 |
| Datetime | 時間 | 一天中的時間 | `2019-10-01` | NER v 2.1 也會傳回 |
| Datetime | 日期範圍 | 日期範圍。 | `2019-10-01` | NER v 2.1 也會傳回 |
| Datetime | 時間範圍 | 時間範圍。 | `2019-10-01` | NER v 2.1 也會傳回 |
| Datetime | 持續時間 | 持續時間。 | `2019-10-01` | NER v 2.1 也會傳回 |
| Datetime | 設定 | 設定，重複時間。 |  `2019-10-01` | NER v 2.1 也會傳回 |
| 數量 | N/A | 數位和數值數量。 | `2019-10-01` | NER v 2.1 也會傳回  |
| 數量 | 數字 | 數字。 | `2019-10-01` | NER v 2.1 也會傳回 |
| 數量 | 百分比 | 形式.| `2019-10-01` | NER v 2.1 也會傳回 |
| 數量 | Ordinal | 序號。 | `2019-10-01` | NER v 2.1 也會傳回 |
| 數量 | Age | 年齡. | `2019-10-01` |  NER v 2.1 也會傳回 |
| 數量 | 貨幣 | 各. | `2019-10-01` | NER v 2.1 也會傳回 |
| 數量 | 維度 | 維度和量值。 | `2019-10-01` | NER v 2.1 也會傳回 |
| 數量 | 溫度 | 低溫. | `2019-10-01` | NER v 2.1 也會傳回 |

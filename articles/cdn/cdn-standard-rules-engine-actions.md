---
title: 適用于 Azure CDN 的標準規則引擎中的動作 |Microsoft Docs
description: 適用于 Azure 內容傳遞網路 (Azure CDN) 的標準規則引擎中的動作的參考檔。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 051737a9f5e0d4092cda26a3f7ce3df1d7f535ef
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760119"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>適用于 Azure CDN 的標準規則引擎中的動作

在適用于 Azure 內容傳遞網路 (Azure CDN) 的[標準規則引擎](cdn-standard-rules-engine.md)中，規則包含一或多個比對條件和動作。 本文提供您可以在適用于 Azure CDN 的標準規則引擎中使用之動作的詳細描述。

規則的第二個部分是動作。 動作可定義一個比對條件或一組比對條件識別的要求類型所要套用的行為。

## <a name="actions"></a>動作

以下是可在適用于 Azure CDN 的標準規則引擎中使用的動作。 

### <a name="cache-expiration"></a>快取到期

使用此動作，針對規則符合條件指定的要求，覆寫端點的存留時間 (TTL) 值。

#### <a name="required-fields"></a>必要的欄位

快取行為 |  描述              
---------------|----------------
略過快取 | 選取此選項且規則符合時，不會快取內容。
覆寫 | 選取此選項且規則符合時，會以動作中指定的值來覆寫從您的來源傳回的 TTL 值。 只有在回應可快取時，才會套用此行為。 對於值為「無快取」、「私用」、「無存放區」的快取控制回應標頭，此動作將不適用。
如果遺漏則設定 | 選取此選項且規則符合時，如果您的來源沒有傳回 TTL 值，此規則會將 TTL 設定為動作中指定的值。 只有在回應可快取時，才會套用此行為。 對於值為「無快取」、「私用」、「無存放區」的快取控制回應標頭，此動作將不適用。

#### <a name="additional-fields"></a>其他欄位

天 | 小時 | 分鐘 | 秒
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>快取索引鍵查詢字串

使用此動作可根據查詢字串修改快取索引鍵。

#### <a name="required-fields"></a>必要的欄位

行為 | 描述
---------|------------
包含 | 選取此選項且規則符合時，在產生快取索引鍵時，參數中指定的查詢字串就會包含在內。 
快取每個唯一的 URL | 選取此選項且規則符合時，每個唯一的 URL 都會有自己的快取索引鍵。 
排除 | 選取此選項且規則符合時，會在產生快取索引鍵時排除參數中指定的查詢字串。
忽略查詢字串 | 選取此選項且規則符合時，當產生快取索引鍵時，不會考慮查詢字串。 

### <a name="modify-request-header"></a>修改要求標頭

使用此動作來修改在傳送至原始來源的要求中存在的標頭。

#### <a name="required-fields"></a>必要的欄位

動作 | HTTP 標頭名稱 | 值
-------|------------------|------
附加 | 若已選取此選項且符合規則，在 [標頭名稱] 中指定的標頭會新增至具有指定值的要求。 如果標頭已存在，此值就會附加至現有的值。 | String
Overwrite | 若已選取此選項且符合規則，在 [標頭名稱] 中指定的標頭會新增至具有指定值的要求。 如果標頭已存在，指定的值就會覆寫現有的值。 | String
刪除 | 若已選取此選項、符合規則，且規則中指定的標頭存在，則會從要求中刪除此標頭。 | String

### <a name="modify-response-header"></a>修改回應標頭

使用此動作來修改在傳回用戶端的回應中存在的標頭。

#### <a name="required-fields"></a>必要的欄位

動作 | HTTP 標頭名稱 | 值
-------|------------------|------
附加 | 若已選取此選項且符合規則，則會使用指定的 [值]，將 [標頭名稱] 中指定的標頭新增至回應。 如果標頭已存在，[值] 就會附加至現有的值。 | String
Overwrite | 若已選取此選項且符合規則，則會使用指定的 [值]，將 [標頭名稱] 中指定的標頭新增至回應。 如果標頭已存在，[值] 就會覆寫現有的值。 | String
刪除 | 若已選取此選項、符合規則，且規則中指定的標頭存在，則會從回應中刪除此標頭。 | String

### <a name="url-redirect"></a>URL 重新導向

使用此動作將用戶端重新導向至新的 URL。 

#### <a name="required-fields"></a>必要的欄位

欄位 | 描述 
------|------------
類型 | 選取要傳回給要求者的回應類型：找到 (302)、已移動 (301)、暫時重新導向 (307)，以及永久重新導向 (308)。
通訊協定 | 符合要求、HTTP、HTTPS。
主機名稱 | 選取您要將要求重新導向至的主機名稱。 保持空白以保留傳入主機。
Path | 定義要在重新導向中使用的路徑。 保持空白以保留傳入路徑。  
查詢字串 | 定義重新導向中使用的查詢字串。 保持空白以保留傳入查詢字串。 
片段 | 定義要在重新導向中使用的片段。 保持空白以保留傳入片段。 

我們強烈建議您使用絕對 URL。 使用相對 URL 可能會將 Azure CDN Url 重新導向至不正確路徑。 

### <a name="url-rewrite"></a>URL 重寫

使用此動作，針對正要路由傳送至原始來源的要求重寫其路徑。

#### <a name="required-fields"></a>必要的欄位

欄位 | 描述 
------|------------
來源模式 | 在要取代的 URL 路徑中定義來源模式。 目前，來源模式會使用以首碼為基礎的相符項。 若要比對所有 URL 路徑，請使用正斜線 (**/**) 作為來源模式的值。
Destination | 定義要在重寫中使用的目的地路徑。 目的地路徑會覆寫來源模式。
保留不相符的路徑 | 如果設定為 **[是]**，則來源模式後面的其餘路徑會附加至新的目的地路徑。 

## <a name="next-steps"></a>後續步驟

- [Azure CDN 概觀](cdn-overview.md)
- [標準規則引擎參考](cdn-standard-rules-engine-reference.md)
- [標準規則引擎中的比對條件](cdn-standard-rules-engine-match-conditions.md)
- [使用標準規則引擎強制執行 HTTPS](cdn-standard-rules-engine.md)

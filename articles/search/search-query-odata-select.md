---
title: OData 選取參考
titleSuffix: Azure Cognitive Search
description: 明確選取要在 Azure 認知搜尋查詢的搜尋結果中傳回之欄位的語法和語言參考。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: dfe438f6940d3ccd5632a47be1389a30748716b0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206891"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Azure 認知搜尋中的 OData $select 語法

 您可以使用[OData **$select**參數](query-odata-filter-orderby-syntax.md)，從 Azure 認知搜尋選擇要包含在搜尋結果中的欄位。 本文會詳細說明 **$select**的語法。 如需有關如何在呈現搜尋結果時使用 **$select**的一般資訊，請參閱[如何在 Azure 認知搜尋中使用搜尋結果](search-pagination-page-layout.md)。

## <a name="syntax"></a>語法

**$Select**參數會決定要在查詢結果集中傳回每份檔的哪些欄位。 下列 EBNF ([擴充巴克斯-Backus-naur 表單](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定義 **$select**參數的文法：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

也提供互動式語法圖：

> [!div class="nextstepaction"]
> [Azure 認知搜尋的 OData 語法圖表](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> 如需完整的 EBNF，請參閱[Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)。

**$Select**參數的形式有兩種：

1. 單一星星 (`*`) ，表示應該傳回所有可抓取的欄位，或
1. 以逗號分隔的欄位路徑清單，可識別應傳回的欄位。

使用第二個表單時，您只能在清單中指定可抓取的欄位。

如果您在不明確指定子欄位的情況下列出複雜欄位，所有可抓取的子欄位都會包含在查詢結果集中。 例如，假設您的索引有一個 `Address` 欄位，其中包含 `Street` 、 `City` 和 `Country` 子欄位全都可供取得。 如果您 `Address` 在 **$select**中指定，則查詢結果會包含三個子欄位。

## <a name="examples"></a>範例

在結果中包含 `HotelId` 、 `HotelName` 和 `Rating` 最上層欄位，以及的 `City` 子欄位 `Address` ：

```odata-filter-expr
    $select=HotelId, HotelName, Rating, Address/City
```

範例結果可能如下所示：

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

`HotelName`將最上層欄位包含在結果中，以及的所有子欄位 `Address` ，以及 `Type` `BaseRate` 集合中每個物件的和子欄位 `Rooms` ：

```odata-filter-expr
    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate
```

範例結果可能如下所示：

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟  

- [如何在 Azure 認知搜尋中使用搜尋結果](search-pagination-page-layout.md)
- [Azure 認知搜尋的 OData 運算式語言總覽](query-odata-filter-orderby-syntax.md)
- [Azure 認知搜尋的 OData 運算式語法參考](search-query-odata-syntax-reference.md)
- [搜尋檔 &#40;Azure 認知搜尋 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)

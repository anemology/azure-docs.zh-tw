---
title: 部分詞彙、模式和特殊字元
titleSuffix: Azure Cognitive Search
description: 使用萬用字元、RegEx 和前置詞查詢，以符合 Azure 認知搜尋查詢要求中的整個或部分詞彙。 包含特殊字元的難以比對模式可以使用完整查詢語法和自訂分析器來加以解析。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: d562931b7578935a4544dfd953ff2de74a5350a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85260979"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>部分詞彙搜尋和包含特殊字元的模式（萬用字元、RegEx、模式）

*部分詞彙搜尋*是指包含詞彙片段的查詢，而不是整個詞彙，您可能只會有開頭、中間或結尾（有時稱為前置詞、中置或尾碼查詢）。 部分詞彙搜尋可能包含片段的組合，通常包含特殊字元，例如屬於查詢字串一部分的虛線或斜線。 常見的使用案例包括電話號碼、URL、代碼或以字元分隔的複合文字部分。

如果索引沒有預期格式的權杖，則包含特殊字元的部分詞彙搜尋和查詢字串可能會造成問題。 在編制索引的[詞法分析階段](search-lucene-query-architecture.md#stage-2-lexical-analysis)（假設是預設的標準分析器）中，會捨棄特殊字元、將複合字分割，並刪除空白字元;當找不到相符的結果時，這些全都會導致查詢失敗。 例如，像是 `+1 (425) 703-6214` （標記為、、、）的電話號碼 `"1"` `"425"` `"703"` `"6214"` 不會顯示在查詢中， `"3-62"` 因為該內容實際上不存在於索引中。 

解決方法是在編制索引期間叫用分析器，以保留完整的字串（如有必要，包括空格和特殊字元），以便您可以在查詢字串中包含空格和字元。 同樣地，具有未標記成較小部分的完整字串會啟用「開頭為」或「結尾為」查詢的模式比對，其中您提供的模式可以針對不是由詞法分析轉換的詞彙進行評估。 針對完整的字串建立額外的欄位，加上使用可發出整個詞彙標記的內容保留分析器，就是模式比對的解決方案，以及比對包含特殊字元之查詢字串的比對。

> [!TIP]
> 如果您熟悉 Postman 和 REST Api，請[下載查詢範例集合](https://github.com/Azure-Samples/azure-search-postman-samples/)來查詢本文所述的部分詞彙和特殊字元。

## <a name="what-is-partial-term-search-in-azure-cognitive-search"></a>什麼是 Azure 認知搜尋中的部分詞彙搜尋

Azure 認知搜尋會掃描索引中的整個 token 化詞彙，而且在部分詞彙中找不到相符項，除非您包含萬用字元預留位置運算子（ `*` 和 `?` ），或將查詢格式化為正則運算式。 部分詞彙是使用下列技術指定的：

+ [正則運算式查詢](query-lucene-syntax.md#bkmk_regex)可以是任何在 Apache Lucene 下有效的正則運算式。 

+ [前置詞相符的萬用字元運算子](query-simple-syntax.md#prefix-search)是指一種普遍辨識的模式，其中包含詞彙的開頭，後面接著或後置 `*` `?` 運算子，例如 `search=cap*` "Cap'n" 的濱水區 Inn "或" Gacc 大寫 "上的比對。 簡單和完整的 Lucene 查詢語法都支援前面加上比對。

+ 以中置字元[和尾碼](query-lucene-syntax.md#bkmk_wildcard)比對的萬用字元，會將 `*` 和運算子放在 `?` 字詞的內部或開頭，而且需要正則運算式語法（其中運算式是以正斜線括住）。 例如，查詢字串（）會傳回 `search=/.*numeric*./` "英數位元" 和 "Alphanumerical" 的結果，作為尾碼和中置中的相符專案。

對於部分詞彙或模式搜尋，以及一些其他查詢形式，例如模糊搜尋，在查詢時不會流量分析器。 針對這些查詢表單，剖析器會藉由運算子和分隔符號的存在而偵測到，查詢字串會傳遞至引擎，而不會進行詞法分析。 針對這些查詢表單，會忽略欄位上指定的分析器。

> [!NOTE]
> 當部分查詢字串包含字元（例如 URL 片段中的斜線）時，您可能需要新增逸出字元。 在 JSON 中，正斜線 `/` 會以反斜線來進行轉義 `\` 。 因此， `search=/.*microsoft.com\/azure\/.*/` 是 URL 片段 "microsoft.com/azure/" 的語法。

## <a name="solving-partialpattern-search-problems"></a>解決部分/模式搜尋問題

當您需要搜尋片段或模式或特殊字元時，您可以使用在較簡單的 token 化規則下運作的自訂分析器來覆寫預設分析器，並保留索引中的整個字串。 回頭執行，此方法看起來像這樣：

+ 定義欄位來儲存字串的完整版本（假設您想要在查詢時分析和未分析的文字）
+ 評估並選擇在適當的資料細微性層級發出權杖的各種分析器
+ 將分析器指派給欄位
+ 建立並測試索引

> [!TIP]
> 評估分析器是一種反復的程式，需要經常重建索引。 您可以使用 Postman、用於[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)、[刪除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)、[載入檔](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)和[搜尋檔](https://docs.microsoft.com/rest/api/searchservice/search-documents)的 REST api，讓此步驟更容易。 針對載入檔，要求本文應該包含您想要測試的小型代表資料集（例如，具有電話號碼或產品代碼的欄位）。 使用這些 Api 在相同的 Postman 集合中，您可以快速地迴圈執行這些步驟。

## <a name="duplicate-fields-for-different-scenarios"></a>不同案例的重複欄位

分析器會決定索引中的詞彙如何 token 化。 因為分析器是以每個欄位為基礎來指派，所以您可以在索引中建立欄位，以針對不同的案例進行優化。 例如，您可能會定義 "featureCode" 和 "featureCodeRegex"，以支援第一個的一般全文檢索搜尋，以及第二個的先進模式比對。 指派給每個欄位的分析器會決定索引中每個欄位的內容如何標記化。  

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>選擇分析器

選擇會產生整個詞彙標記的分析器時，下列分析器是常見的選擇：

| 分析器 | 「行為」 |
|----------|-----------|
| [語言分析器](index-add-language-analyzers.md) | 保留複合字組或字串、母音變化和動詞表單中的連字號。 如果查詢模式包含破折號，使用語言分析器可能就已足夠。 |
| [關鍵字](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 整個欄位的內容會標記為單一詞彙。 |
| [空白字元](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 僅分隔空白字元。 包含破折號或其他字元的詞彙會被視為單一標記。 |
| [自訂分析器](index-add-custom-analyzers.md) | 使用建立自訂分析器可讓您指定 tokenizer 和權杖篩選器。 先前的分析器必須以相同的方式使用。 自訂分析器可讓您挑選要使用的 token 化工具和權杖篩選器。 <br><br>建議的組合是使用[較低案例標記篩選](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)的[關鍵字 tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) 。 預先定義的[關鍵字分析器](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)本身並不會有任何大寫的文字，這可能會導致查詢失敗。 自訂分析器會提供一個機制，讓您新增小寫標記篩選器。 |

如果您使用 Web API 測試控管（例如 Postman），您可以加入[測試分析器 REST 呼叫](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)來檢查 token 化的輸出。

您必須有已填入的索引才能使用。 假設現有的索引和包含破折號或部分詞彙的欄位，您可以嘗試各種不同的分析器，以查看所發出的權杖。  

1. 首先，請檢查標準分析器，查看預設如何將詞彙標記化。

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 評估回應，以瞭解如何在索引內標記文字。 請注意，每個詞彙的大小寫都是小寫並加以分解。 只有符合這些 token 的查詢，才會在結果中傳回這份檔。 包含 "10" 或 "的查詢將會失敗。

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. 現在，修改要求以使用 `whitespace` 或 `keyword` 分析器：

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 現在回應是由大寫的單一 token 所組成，並保留以破折號做為字串的一部分。 如果您需要搜尋模式或部分詞彙（例如 "10-or"），則查詢引擎現在具有尋找相符項的基礎。


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> 請注意，查詢剖析器在建立查詢樹狀結構時，通常會在搜尋運算式中以小寫的詞彙區分。 如果您使用的分析器在編制索引期間不會以小寫的文字輸入，而且您不會得到預期的結果，這可能是原因。 解決方案是新增較低案例的權杖篩選準則，如下面的「使用自訂分析器」一節所述。

## <a name="configure-an-analyzer"></a>設定分析器
 
無論您正在評估分析器或使用特定設定繼續進行，都必須在欄位定義上指定分析器，如果您不是使用內建的分析器，也可能設定分析器本身。 交換分析器時，您通常需要重建索引（drop、rebuild 和 reload）。 

### <a name="use-built-in-analyzers"></a>使用內建分析器

內建或預先定義的分析器可以在欄位定義的屬性上依名稱指定 `analyzer` ，不需要在索引中進行其他設定。 下列範例示範如何 `whitespace` 在欄位上設定分析器。 

如需其他案例，以及深入瞭解其他內建分析器，請參閱[預先定義的分析器清單](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)。 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>使用自訂分析器

如果您使用的是[自訂分析器](index-add-custom-analyzers.md)，請在索引中以使用者定義的 tokenizer、權杖篩選器組合來定義它，以及可能的設定。 接下來，在欄位定義上參考它，就如同您在內建的分析器一樣。

當目標是整個詞彙的 token 化時，建議使用包含**關鍵字 tokenizer**和**小寫標記篩選準則**的自訂分析器。

+ 關鍵字 tokenizer 會針對整個欄位內容建立單一 token。
+ 小寫標記篩選會將大寫字母轉換成小寫文字。 查詢剖析器通常會以小寫的形式輸入任何大寫文字。 較低的大小寫會 homogenizes 具有 token 化詞彙的輸入。

下列範例說明可提供關鍵字 tokenizer 和小寫標記篩選的自訂分析器。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> `keyword_v2` `lowercase` 系統會知道 tokenizer 和 token 篩選器，並使用其預設設定，這也是為什麼您可以依名稱參考它們，而不需要先定義它們。

## <a name="build-and-test"></a>建置和測試

當您使用支援您案例的分析器和欄位定義來定義索引之後，請載入具有代表性字串的檔，讓您可以測試部分字串查詢。 

前面幾節說明了邏輯。 本節會逐步解說您在測試方案時應該呼叫的每個 API。 如先前所述，如果您使用互動式 web 測試控管（例如 Postman），您可以快速地逐步執行這些工作。

+ [刪除索引](https://docs.microsoft.com/rest/api/searchservice/delete-index)會移除相同名稱的現有索引，讓您可以重新建立。

+ [建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)會在您的搜尋服務上建立索引結構，包括分析器定義和包含分析器規格的欄位。

+ [載入檔](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)會匯入具有與您的索引相同結構的檔，以及可搜尋的內容。 在此步驟之後，您的索引就已準備好進行查詢或測試。

+ [測試分析器](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)是在[選擇分析器](#choose-an-analyzer)中引進。 使用各種分析器來測試索引中的一些字串，以瞭解詞彙如何標記化。

+ [搜尋檔](https://docs.microsoft.com/rest/api/searchservice/search-documents)說明如何使用[簡單語法](query-simple-syntax.md)或[完整的 Lucene 語法](query-lucene-syntax.md)來建立查詢要求，以用於萬用字元和正則運算式。

  對於部分詞彙查詢，例如查詢 "3-6214" 以尋找 "+ 1 （425） 703-6214" 的相符項，您可以使用簡單的語法： `search=3-6214&queryType=simple` 。

  若為中置和後置詞查詢，例如查詢 "num" 或 "numeric" 以尋找 "英數位元的相符項，請使用完整的 Lucene 語法和正則運算式：`search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>微調查詢效能

如果您執行建議的設定，其中包含 keyword_v2 tokenizer 和小寫的權杖篩選器，您可能會注意到查詢效能降低的原因，是因為您的索引中現有的權杖有額外的權杖篩選器處理。 

下列範例會新增[EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) ，以加快前置詞比對的速度。 會針對包含下列字元的2-25 個字元組合產生額外的權杖：（不只是 MS、MSF、MSFT、MSFT/、MSFT/S、MSFT/SQ、MSFT/SQL）。 

您可以想像，額外的 token 化會產生較大的索引。 如果您有足夠的容量可容納較大的索引，則此方法的回應時間更快，可能是較佳的解決方案。

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

## <a name="next-steps"></a>後續步驟

本文說明分析器如何影響查詢問題，以及如何解決查詢問題。 在下一個步驟中，請仔細查看分析器對編制索引和查詢處理的影響。 特別是，請考慮使用「分析文字 API」來傳回已標記的輸出，讓您可以確切查看分析器為您的索引建立的內容。

+ [語言分析器](search-language-support.md)
+ [Azure 認知搜尋中的文字處理分析器](search-analyzers.md)
+ [分析文字 API （REST）](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [全文檢索搜尋的運作方式（查詢架構）](search-lucene-query-architecture.md)

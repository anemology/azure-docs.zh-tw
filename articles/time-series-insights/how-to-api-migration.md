---
title: 遷移至新的 Azure 時間序列深入解析 Gen2 API 版本 |Microsoft Docs
description: 如何更新 Azure 時間序列深入解析 Gen2 環境，以使用新的正式推出版本。
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: shresha
ms.openlocfilehash: 784c19844c658af6850c755244314145223c45ef
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163946"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>遷移至新的 Azure 時間序列深入解析 Gen2 API 版本

## <a name="overview"></a>概觀

如果您在2020年7月16日之前建立了 Azure 時間序列深入解析 Gen2 環境， () ，請遵循本文中所述的步驟，更新 TSI 環境以使用新的正式運作版 Api。

新的 API 版本為 `2020-07-31` ，並使用更新的[時間序列運算式語法](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)。

使用者必須遷移其環境的[時間序列模型變數](./concepts-variables.md)、儲存的查詢、Power BI 查詢，以及任何對 API 端點進行呼叫的自訂工具。 如果您有任何關於遷移程式的問題或疑慮，請透過 Azure 入口網站提交支援票證，並提及這份檔。

> [!IMPORTANT]
> 預覽 API 版本 `2018-11-01-preview` 將繼續支援到2020年10月31日為止。 請先完成此遷移的所有適用步驟，然後再避免服務中斷。

## <a name="migrate-time-series-model-and-saved-queries"></a>遷移時間序列模型和已儲存的查詢

為了協助使用者遷移其[時間序列模型變數](./concepts-variables.md)和已儲存的查詢，有一個可透過[Azure 時間序列深入解析 Explorer](https://insights.timeseries.azure.com)取得的內建工具。 流覽至您想要遷移的環境，並遵循下列步驟。 **您可以完成部分的遷移，稍後再回來完成，不過，您無法還原任何更新。**

> [!NOTE]
> 您必須是環境的參與者，才能對時間序列模型和已儲存的查詢進行更新。 如果您不是參與者，將只能遷移您個人儲存的查詢。 請先參閱[環境存取原則](./concepts-access-policies.md)和您的存取層級，再繼續進行。

1. Explorer 會提示您更新您的時間序列模型變數與儲存的查詢所使用的語法。

    [![及時](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    如果您不小心關閉通知，它可以在通知面板中找到。

1. 按一下 [**顯示更新**] 以開啟 [遷移] 工具。

1. 按一下 [**下載類型**]。 由於遷移會覆寫您目前的類型來改變變數語法，因此您將需要儲存目前類型的複本。 當類型已下載時，此工具會通知您。

    [![下載類型](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. 按一下 [**更新變數**]。 當變數已更新時，此工具將會通知您。

    > [!IMPORTANT]
    > 如果您更新類型，使用舊版 API 的自訂應用程式 (`2018-11-01-preview`) 將需要使用新的 api 版本 (`2020-07-31`) 才能繼續運作。 如果您不確定所使用的 API 版本，請洽詢您的系統管理員，然後再進行更新。 您可以關閉遷移工具，稍後再返回這些步驟。 深入瞭解[如何遷移自訂應用程式](#migrate-custom-applications)。

    [![更新變數](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. 按一下 [**更新已儲存的查詢**]。 當變數已更新時，此工具將會通知您。

    [![更新已儲存的查詢](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. 按一下 [完成]。

    [![已完成遷移](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

藉由繪製一些新建立的變數和已儲存的查詢，來審查您更新的環境。 如果您在製作圖表時看到任何非預期的行為，請使用 Explorer 中的意見反應工具，將意見反應傳送給我們。

## <a name="migrate-power-bi-queries"></a>遷移 Power BI 查詢

如果您已使用 Power BI 連接器產生查詢，則會使用預覽 API 版本和舊的時間序列運算式語法來呼叫 Azure 時間序列深入解析。 這些查詢會繼續成功地抓取資料，直到預覽 API 淘汰為止。

若要將查詢更新為使用新的 API 版本和新的時間序列運算式語法，必須從 Explorer 重新產生查詢。 閱讀更多有關如何[使用 Power BI 連接器建立查詢的](./how-to-connect-power-bi.md)資訊。

> [!NOTE]
> 您必須使用2020年7月版本的 Power BI Desktop。 如果不是，您可能會看到[不正確查詢裝載版本錯誤](./how-to-diagnose-troubleshoot.md#problem-power-bi-connector-shows-unable-to-connect)。

## <a name="migrate-custom-applications"></a>遷移自訂應用程式

如果您的自訂應用程式正在對下列 REST 端點進行呼叫，則在 URI 中將 API 版本更新為時就已足夠 `2020-07-31` ：

- 時間序列模型 API (機器翻譯)
  - 模型設定 Api
    - [獲取](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [更新](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - 執行個體 API
    - [所有批次作業](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [清單](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [搜尋](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [建議](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - 階層 Api
    - [所有批次作業](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [清單](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - 類型 Api
    - [Delete、Get 作業](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [清單](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

針對下列 REST 端點，您必須將 URI 中的 API 版本更新為， `2020-07-31` 並確定所有出現的屬性都 `tsx` 使用更新的[時間序列運算式語法](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)。

- 類型 Api
  - [Put 作業](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- 查詢 Api
  - [GetEvents](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [GetSeries](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>範例

#### <a name="typesbatchput"></a>TypesBatchPut

) 使用舊的要求主體 (`2018-11-01-preview` ：

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

) 使用 (更新的要求主體 `2020-07-31` ：

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

或者， `filter` 也可以是 `$event.Mode.String = 'outdoor'` 。 `value`必須使用括弧來將特殊字元 (`_`) 。

#### <a name="getevents"></a>GetEvents

) 使用舊的要求主體 (`2018-11-01-preview` ：

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

) 使用 (更新的要求主體 `2020-07-31` ：

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

或者， `filter` 也可以是 `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` 。

#### <a name="getseries"></a>GetSeries

) 使用舊的要求主體 (`2018-11-01-preview` ：

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

) 使用 (更新的要求主體 `2020-07-31` ：

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

或者， `value` 也可以是 `$event['Bar-Pressure-Offset'].Double` 。 如果未指定任何資料類型，則資料類型一律會假設為 Double。 括弧標記法必須用來 () 中的特殊字元換用 `-` 。

#### <a name="aggregateseries"></a>AggregateSeries

) 使用舊的要求主體 (`2018-11-01-preview` ：

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

) 使用 (更新的要求主體 `2020-07-31` ：

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

或者， `value` 也可以是 `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` 。

### <a name="potential-errors"></a>可能的錯誤

#### <a name="invalidinput"></a>InvalidInput

如果您看到下列錯誤，則是使用新的 API 版本 (`2020-07-31`) 但 TSX 語法尚未更新。 請參閱上述的[時間序列運算式語法](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)和遷移範例。 重新 `tsx` 提交 API 要求之前，請確定已正確更新所有屬性。

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

- 透過[Azure 時間序列深入解析 Explorer](./concepts-ux-panels.md)或自訂應用程式來測試您的環境。

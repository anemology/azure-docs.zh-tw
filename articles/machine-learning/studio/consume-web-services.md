---
title: ML Studio (傳統) ：使用 web 服務-Azure
description: 從 Azure Machine Learning Studio (傳統) 部署機器學習服務之後，就可以使用 RESTFul Web 服務作為即時要求-回應服務或批次執行服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-python, devx-track-javascript
ms.date: 05/29/2020
ms.openlocfilehash: bbecafbbd988530231d71892fb12a55c1a605442
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87844366"
---
# <a name="how-to-consume-a-machine-learning-studio-classic-web-service"></a>如何使用 Machine Learning Studio (傳統) web 服務

**適用於：** ![是](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (傳統版)![否](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)


將 Azure Machine Learning Studio (傳統) 預測模型部署為 Web 服務之後，您就可以使用 REST API 傳送資料並取得預測。 您可以即時或以批次模式傳送資料。

您可以在這裡找到有關如何使用 Machine Learning Studio (傳統) 來建立和部署 Machine Learning Web 服務的詳細資訊：

* 如需如何在 Machine Learning Studio (傳統) 中建立實驗的教學課程，請參閱[建立您的第一個實驗](create-experiment.md)。
* 如需如何部署 Web 服務的詳細資訊，請參閱[部署 Machine Learning Web 服務](deploy-a-machine-learning-web-service.md)。
* 如需 Machine Learning 的一般詳細資訊，請參閱 [Machine Learning 文件中心](https://azure.microsoft.com/documentation/services/machine-learning/)。



## <a name="overview"></a>概觀
使用 Azure Machine Learning Web 服務，外部應用程式會即時與機器學習服務工作流程計分模型通訊。 機器學習 Web 服務呼叫會將預測結果傳回外部應用程式。 若要進行機器學習 Web 服務呼叫，您可以傳遞部署預測時所建立的 API 金鑰。 機器學習服務 Web 服務以 REST 為基礎，這是一種常見的 Web 程式設計專案架構。

Azure Machine Learning Studio (傳統) 有兩種類型的服務：

* 要求-回應服務 (RR) -這是一種低延遲、高擴充性的服務，可為從 Machine Learning Studio (傳統) 建立和部署的無狀態模型提供介面。
* 批次執行服務 (BES) – 這是一種非同步的服務，為一批資料記錄進行計分。

如需 Machine Learning Web 服務的詳細資訊，請參閱[部署 Machine Learning web 服務](deploy-a-machine-learning-web-service.md)。

## <a name="get-an-authorization-key"></a>取得授權金鑰
當您部署實驗時，會為 Web 服務產生 API 金鑰。 您可以從數個位置擷取金鑰。

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>透過 Microsoft Azure Machine Learning Web 服務入口網站
登入[Microsoft Azure Machine Learning Web 服務](https://services.azureml.net)入口網站。

若要擷取新 Machine Learning Web 服務的 API 金鑰︰

1. 在 Azure Machine Learning Web Services 入口網站中，按一下頂端功能表上的 [Web 服務]****。
2. 按一下您要擷取金鑰的 Web 服務。
3. 在頂端功能表上，按一下 [取用] ****。
4. 複製並儲存 [主要金鑰] ****。

若要擷取傳統 Machine Learning Web 服務的 API 金鑰︰

1. 在 Azure Machine Learning Web Services 入口網站中，按一下頂端功能表上的 [傳統 Web 服務]****。
2. 按一下您所使用的 Web 服務。
3. 按一下您要取得金鑰的端點。
4. 在頂端功能表上，按一下 [取用] ****。
5. 複製並儲存 [主要金鑰] ****。

### <a name="classic-web-service"></a>傳統 Web 服務
 您也可以從 Machine Learning Studio (傳統) 取得傳統 Web 服務的金鑰。

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (傳統)
1. 在 [Machine Learning Studio (傳統) 中，按一下左側的 [ **WEB 服務**]。
2. 按一下某個 Web 服務。 [API 金鑰]**** 位於 [儀表板]**** 索引標籤上。

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>連接到 Machine Learning Web 服務
您可以使用任何支援 HTTP 要求和回應的程式設計語言，連線到機器學習 Web 服務。 您可以從機器學習 Web 服務說明頁面檢視 C#、Python 和 R 的範例。

**MACHINE LEARNING API**說明當您部署 Web 服務時，會建立 Machine Learning API 說明。 請參閱[教學課程3：部署信用風險模型](tutorial-part3-credit-risk-deploy.md)。
機器學習服務 API 說明包含有關預測 Web 服務的詳細資訊。

1. 按一下您所使用的 Web 服務。
2. 按一下您想要檢視 API 說明頁面的端點。
3. 在頂端功能表上，按一下 [取用] ****。
4. 按一下 [要求-回應] 或 [批次執行] 端點底下的 [API 說明頁面]****。

**檢視新 Web 服務的機器學習 API 說明**

在[Azure Machine Learning Web 服務入口網站](https://services.azureml.net/)中：

1. 按一下頂端功能表上的 [Web 服務] **** 。
2. 按一下您要擷取金鑰的 Web 服務。

按一下 [使用 Web 服務]**** 取得要求-回應服務和批次執行服務的 URI，以及以 C#、R 和 Python 撰寫的範例程式碼。

按一下 [Swagger API]**** 從提供的 URI，取得所呼叫 API 的 Swagger 相關文件。

### <a name="c-sample"></a>C# 範例
若要連接到 Machine Learning Web 服務，請使用**HttpClient**傳遞 ScoreData。 ScoreData 包含 FeatureVector，這是代表 ScoreData 的數值特徵 N 維向量。 您要使用 API 金鑰向機器學習服務驗證。

若要連接到 Machine Learning Web 服務，必須安裝**WebApi 的用戶端**NuGet 套件。

**在 Visual Studio 中安裝 WebApi 用戶端 NuGet**

1. 發佈 Download dataset from UCI: Adult 2 class dataset 的 Web 服務。
2. 按一下 [**工具**] [  >  **NuGet 套件管理員**] [  >  **套件管理員主控台**]。
3. 選擇 [ **Install-package Microsoft.AspNet.WebApi.Client**]。

**執行程式碼範例**

1. 發佈機器學習服務範例集合中的 "Sample 1: Download dataset from UCI: Adult 2 class dataset" 實驗。
2. 使用來自 Web 服務的金鑰指派 apiKey。 請參閱上述**的取得授權金鑰**。
3. 使用要求 URI 指派 serviceUri。

**以下是完整的要求內容。**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Python 範例
若要連線至 Machine Learning Web 服務，請使用 Python 2.X 的 **urllib2** 程式庫或 Python 3.X 的 **urllib.request** 程式庫。 要傳遞的是 ScoreData，內含 FeatureVector，是代表 ScoreData 數值特徵的 N 維向量。 您要使用 API 金鑰向機器學習服務驗證。

**執行程式碼範例**

1. 部署機器學習服務範例集合中的 "Sample 1: Download dataset from UCI: Adult 2 class dataset" 實驗。
2. 使用來自 Web 服務的金鑰指派 apiKey。 請參閱本文開頭附近的**取得授權金鑰**一節。
3. 使用要求 URI 指派 serviceUri。

**以下是完整的要求內容。**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R 範例

若要連線至 Machine Learning Web 服務，請使用 **RCurl** 和 **rjson** 程式庫提出要求並處理傳回的 JSON 回應。 要傳遞的是 ScoreData，內含 FeatureVector，是代表 ScoreData 數值特徵的 N 維向量。 您要使用 API 金鑰向機器學習服務驗證。

**以下是完整的要求內容。**
```r
library("curl")
library("httr")
library("rjson")

requestFailed = function(response) {
    return (response$status_code >= 400)
}

printHttpResult = function(response, result) {
    if (requestFailed(response)) {
        print(paste("The request failed with status code:", response$status_code, sep=" "))
    
        # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
        print(response$headers)
    }
    
    print("Result:") 
    print(fromJSON(result))  
}

req = list(
        Inputs = list( 
            "input1" = list(
                "ColumnNames" = list("Col1", "Col2", "Col3"),
                "Values" = list( list( "0", "value", "0" ),  list( "0", "value", "0" )  )
            )                ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "abc123" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

response = POST(url= "<your-api-uri>",
        add_headers("Content-Type" = "application/json", "Authorization" = authz_hdr),
        body = body)

result = content(response, type="text", encoding="UTF-8")

printHttpResult(response, result)
```

### <a name="javascript-sample"></a>JavaScript 範例

若要連線至 Machine Learning Web 服務，請使用專案中的 **request** npm 套件。 也會用到 `JSON` 物件在輸入時添加格式，並用來剖析結果。 使用 `npm install request --save` 進行安裝，或將 `"request": "*"` 新增至 `dependencies` 下的 package.json 並執行 `npm install`。

**以下是完整的要求內容。**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```

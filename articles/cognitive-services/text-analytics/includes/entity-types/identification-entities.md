---
title: 識別實體
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 6271cb449b6bbc80269dd325bd5acd7edd2e0a6d
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010956"
---
此實體類別包含財務資訊和官方形式的識別。 從模型版本開始提供 `2019-10-01` 。 子類型如下所示。 

### <a name="financial-account-identification"></a>財務帳戶識別

| 子類型名稱               | 描述                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA 銀行代號        | 美國中 (ABA) 運輸路線的關係。                  |
| SWIFT 代碼                 | 付款指示資訊的 SWIFT 代碼。                           |
| 信用卡                | 信用卡號碼。                                                       |
| 國際銀行帳戶號碼 (IBAN)                  | 付款指示資訊的 IBAN 代碼。                            |


### <a name="government-and-countryregion-specific-identification"></a>政府和國家/地區特定的識別

> [!NOTE]
> 下列財務和國家特定的實體不會隨 `domain=phi` 參數傳回：
> * 護照號碼
> * 稅務識別碼

下列實體會依國家/地區分組和列出：

阿根廷
* 阿根廷國家身分識別 (DNI) 號碼

奧地利
* 奧地利身分識別卡
* 奧地利稅務識別編號
* 奧地利加值稅 (加值稅) 號碼

澳洲
* 澳大利亞銀行帳戶號碼
* 澳大利亞商業點數
* 澳大利亞公司號碼
* 澳大利亞駕駛的授權號碼
* 澳大利亞醫療帳戶號碼
* 澳大利亞護照號碼
* 澳大利亞稅務檔案編號

比利時
* 比利時國家/地區號碼
* 已新增比利時值的稅額

巴西 
* 巴西法律實體編號 (CNPJ) 
* 巴西 CPF 號碼
* 巴西國家識別碼卡 (RG) 

保加利亞
* 保加利亞的統一民事號碼

加拿大
* 加拿大銀行帳戶號碼
* 加拿大駕駛的授權號碼
* 加拿大健全狀況服務號碼
* 加拿大護照號碼
* 加拿大個人健康識別號碼 (PHIN) 
* 加拿大社會保險號碼

智利
* 身分識別卡號碼 

中國
* 中國居民身分識別卡 (PRC) 號碼

克羅埃西亞
* 克羅地亞身分識別卡號碼
* 克羅地亞國家/地區識別碼卡號碼
*  (OIB) 號碼的克羅地亞個人識別

賽普勒斯
* 賽普勒斯身分識別卡號碼
* 賽普勒斯稅務識別編號

捷克共和國
* 捷克文個人身分識別號碼

丹麥
* 丹麥個人識別碼

愛沙尼亞
* 愛沙尼亞個人識別代碼

歐洲聯集 (EU) 
* 歐盟轉帳卡編號
* 歐盟駕照號碼
* 歐盟國家/地區身分證字號
* 歐盟護照號碼
* 歐盟社會安全號碼 (SSN) 或對等識別碼
* 歐盟稅務識別編號 (TIN)

芬蘭
* 芬蘭歐洲醫療保險點數
* 芬蘭國家/地區識別碼
* 芬蘭護照號碼

法國
* 法國駕駛的授權號碼
* 法國醫療保險號碼
* 法國國家/地區識別碼卡 (CNI) 
* 法國護照號碼
* 法國社會安全號碼 (INSEE) 
* 法國的稅務識別編號 (numéro SPI. ) 
* 法國值新增的稅額

德國
* 德文駕駛的授權號碼
* 德國身分識別卡號碼
* 德文護照號碼
* 德國稅務識別編號
* 德國值新增的稅務號碼

希臘 
* 希臘國家識別碼卡號碼
* 希臘稅務識別編號

香港
* 香港特別行政區的身分識別卡 (HKID) 號碼

匈牙利
* 匈牙利國家識別號碼
* 匈牙利稅務識別編號
* 匈牙利增值新增的稅務號碼

印度
* 印度 (PAN) 的永久帳戶號碼
* 印度唯一識別 (Aadhaar) 號碼

印尼
* 印尼識別卡 (KTP) 號碼

愛爾蘭
* 愛爾蘭個人公用服務 (PPS) 號碼

以色列
* 以色列國家/地區識別碼
* 以色列銀行帳戶號碼

義大利
* 義大利駕駛的授權識別碼
* 義大利會計程式碼
* 義大利增值新增的稅務號碼

日本
* 日本銀行帳戶號碼
* 日本駕駛的授權號碼
* 日文我的數位個人
* 日文我的數位公司
* 日本居民註冊號碼
* 日本居留證卡號
* 日本社會保險號碼 (SIN) 
* 日本護照號碼

拉脫維亞
* 拉脫維亞個人代碼

立陶宛
* 立陶宛個人代碼

盧森堡
*  (自然人員的盧森堡國家/地區識別碼) 
*  (非自然人員的盧森堡國家/地區識別碼) 

馬來西亞
* 馬來西亞身分識別卡號碼

馬爾他
* 馬爾他身分識別卡編號
* 馬爾他稅務識別編號

荷蘭
* 荷蘭公民的服務 (BSN) 號碼
* 荷蘭稅務識別編號
* 荷蘭值新增的稅務號碼

紐西蘭
* 新的紐西蘭銀行帳戶號碼
* 紐西蘭駕駛的授權號碼
* 紐西蘭 Inland 收益號碼
* 新的紐西蘭健全狀況號碼
* 紐西蘭社交福利號碼

挪威
* 挪威識別編號

菲律賓
* 菲律賓統一的多用途 ID 號碼

波蘭
* 波蘭身分識別卡
*  (PESEL) 的波蘭國家/地區識別碼
* 波蘭護照號碼
* 波蘭 REGON 號碼
* 波蘭稅務識別編號

葡萄牙 
* 葡萄牙公民卡號
* 葡萄牙稅務識別編號

羅馬尼亞
* 羅馬尼亞個人數位代碼 (CNP) 

俄羅斯
*  (國內) 的俄文護照號碼
*  (國際) 俄文護照號碼

沙烏地阿拉伯
* 沙烏地阿拉伯國家/地區識別碼

新加坡
* 新加坡國家註冊識別碼卡 (NRIC) 號碼

斯洛伐克 
* 斯洛伐克個人號碼

斯洛維尼亞
* 斯洛維尼亞稅務識別編號
* 斯洛維尼亞唯一的主要公民號碼

南非
* 南非識別號碼

南韓
* 韓國居民註冊號碼

西班牙 
* 西班牙 DNI
*  (SSN) 的西班牙社會安全號碼
* 西班牙稅務識別編號

瑞典
* 瑞典國家/地區識別碼
* 瑞典護照號碼
* 瑞典稅務識別編號

瑞士
* 瑞士社會安全號碼 AHV

台灣 
* 臺灣國家/地區識別碼
* 臺灣居民憑證 (ARC/TARC) 
* 臺灣護照號碼

泰國
* 泰國人口識別碼

土耳其
* 土耳其文的國家/地區識別碼

烏克蘭
*  (國內) 的烏克蘭護照號碼
*  (國際) 的烏克蘭護照號碼

英國
* 英國 驅動程式的授權號碼
* 英國 選舉的變換編號
* 英國 國家健全狀況服務 (NHS) 號碼
* 英國 國家保險數位 (NINO) 
* 英國 護照號碼
* 英國 唯一的納稅人參考編號

美國
* 美國社會安全號碼 (SSN) 
* 美國駕駛的授權號碼
* 美國護照號碼
* 美國個別的納稅人識別編號 (ITIN) 
* 美國藥品機關機關 (DEA) 號碼
* 美國銀行帳戶號碼

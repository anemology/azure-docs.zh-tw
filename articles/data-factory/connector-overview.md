---
title: Azure Data Factory 連接器總覽
description: 瞭解 Data Factory 中支援的連接器。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 334d5b5113dba17c5abc2b4f2520bde0d16e4c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007433"
---
# <a name="azure-data-factory-connector-overview"></a>Azure Data Factory 連接器總覽

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 透過複製、資料流程、查閱、取得中繼資料和刪除活動來支援下列資料存放區和格式。 按一下每個資料存放區，以深入瞭解支援的功能和對應的設定。

## <a name="supported-data-stores"></a>支援的資料存放區

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>支援的檔案格式

Azure Data Factory 支援下列檔案格式。 請參閱每一篇文章，以取得以格式為基礎的設定。

- [Avro 格式](format-avro.md)
- [二進位格式](format-binary.md)
- [Common Data Model 格式](format-common-data-model.md)
- [分隔符號文字格式](format-delimited-text.md)
- [差異格式](format-delta.md)
- [Excel 格式](format-excel.md)
- [JSON 格式](format-json.md)
- [ORC 格式](format-orc.md)
- [Parquet 格式](format-parquet.md)
- [XML 格式](format-xml.md)

## <a name="next-steps"></a>接下來的步驟

- [複製活動](copy-activity-overview.md)
- [對應資料流](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [刪除活動](delete-activity.md)

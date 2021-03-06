---
title: Azure Data Factory 中的複製活動所支援的檔案格式
description: 本主題描述 Azure Data Factory 中的複製活動所支援的檔案格式和壓縮程式碼。
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.openlocfilehash: c044208699bf5bebb6383cfef00bf53b744369d0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86522429"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Azure Data Factory 中的複製活動所支援的檔案格式和壓縮編解碼器
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*本文適用于下列連接器： [Amazon S3](connector-amazon-simple-storage-service.md)、 [azure Blob](connector-azure-blob-storage.md)、 [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、Azure 檔案[儲存體](connector-azure-file-storage.md)、[檔案系統](connector-file-system.md)、 [FTP](connector-ftp.md)、 [Google Cloud Storage](connector-google-cloud-storage.md)、 [HDFS](connector-hdfs.md)、 [HTTP](connector-http.md)和[SFTP](connector-sftp.md)。*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

您可以使用「[複製活動](copy-activity-overview.md)」在兩個以檔案為基礎的資料存放區之間依原樣複製檔案，在此情況下，資料會有效率地複製，而不會進行任何序列化或還原序列化。 

此外，您也可以剖析或產生指定格式的檔案。 例如，您可以執行下列動作：

* 從 SQL Server 資料庫複製資料，並以 Parquet 格式寫入 Azure Data Lake Storage Gen2。
* 從內部部署檔案系統複製文字（CSV）格式的檔案，並以 Avro 格式寫入 Azure Blob 儲存體。
* 從內部部署檔案系統複製壓縮檔案、將其即時解壓縮，然後將解壓縮的檔案寫入 Azure Data Lake Storage Gen2。
* 從 Azure Blob 儲存體複製 Gzip 壓縮文字（CSV）格式的資料，並將其寫入 Azure SQL Database。
* 許多需要序列化/還原序列化或壓縮/解壓縮的活動。

## <a name="next-steps"></a>後續步驟

請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [複製活動效能](copy-activity-performance.md)

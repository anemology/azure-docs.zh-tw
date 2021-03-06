---
title: 規劃 Azure 維護事件
description: 瞭解如何為 Azure SQL Database 和 Azure SQL 受控執行個體中規劃的維護事件做好準備。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 5bdc3eb8c118c19f90ce1fd92ac5ee156719dacd
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85987194"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>規劃 Azure SQL Database 和 Azure SQL 受控執行個體中的 Azure 維護事件
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

瞭解如何在 Azure SQL Database 和 Azure SQL 受控執行個體中，為資料庫上規劃的維護事件做準備。

## <a name="what-is-a-planned-maintenance-event"></a>什麼是計劃性維護事件？

針對每個資料庫，Azure SQL Database 和 Azure SQL 受控執行個體維護資料庫複本的仲裁，其中一個複本是主要複本。 在任何時候，主要複本都必須是線上服務，而且至少有一個次要複本必須狀況良好。 計劃性維護期間，資料庫仲裁的成員每次只會有一個離線，目的是在線上隨時保有一個可回應的主要複本和至少一個次要複本，以確保用戶端不會停機。 主要複本需要離線時，會產生重新設定/容錯移轉程序，這時，其中一個次要複本會成為新的主要複本。  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>計劃性維護事件期間的預期情況

重新進行重新進行/容錯移轉通常會在30秒內完成。 平均為8秒。 若已成功連線，您的應用程式必須重新連線至健康情況良好的新資料庫主要複本。 如果在新的主要複本上線之前，資料庫正在進行重新設定時嘗試新的連接，您會收到錯誤40613（無法使用資料庫）：伺服器 ' {servername} ' 上的資料庫 ' {databasename} ' 目前無法使用。 請稍後重試連接。」 如果您的資料庫有長時間執行的查詢，此查詢將會在重新設定期間中斷，而且需要重新開機。

## <a name="retry-logic"></a>重試邏輯

任何連線到雲端資料庫服務的用戶端生產應用程式均應實作健全的連線[重試邏輯](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。 此步驟有助於減緩這些情況，而且通常能夠讓終端使用者知道這些錯誤。

## <a name="frequency"></a>頻率

平均而言，每個月會發生 1.7 次計劃性維護事件。

## <a name="resource-health"></a>資源健康情況

如果您的資料庫發生登入失敗，請檢查[Azure 入口網站](https://portal.azure.com)中的 [[資源健康狀態](../../service-health/resource-health-overview.md#get-started)] 視窗以瞭解目前的狀態。 [健康情況歷程記錄] 區段會包含每個事件停止運作的原因 (如果有的話)。

## <a name="next-steps"></a>後續步驟

- 深入瞭解適用于 Azure SQL Database 和 Azure SQL 受控執行個體的[資源健康狀態](resource-health-to-troubleshoot-connectivity.md)。
- 如需重試邏輯的詳細資訊，請參閱[暫時性錯誤的重試邏輯](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)。

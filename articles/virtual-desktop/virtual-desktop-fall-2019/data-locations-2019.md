---
title: Windows 虛擬桌面 (傳統) 的資料位置-Azure
description: 簡要介紹哪些位置 Windows 虛擬桌面 (傳統) 資料和中繼資料儲存在中。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 869defde657c9cb4c8bea6bbacebb9458e5a2b96
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008809"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Windows 虛擬桌面 (傳統) 的資料位置

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../data-locations.md)。

Windows 虛擬桌面目前適用于所有地理位置。 一開始，服務中繼資料只能儲存在美國 (US) 地理位置。 系統管理員可以在建立主機集區虛擬機器和相關聯的服務（例如檔案伺服器）時，選擇要儲存使用者資料的位置。 深入瞭解 azure[資料中心地圖](https://azuredatacentermap.azurewebsites.net/)上的 azure 地理位置。

>[!NOTE]
>Microsoft 不會控制或限制您或您的使用者可以存取您的使用者和應用程式特定資料的區域。

>[!IMPORTANT]
>Windows 虛擬桌面會在位於美國的資料中心儲存像是租使用者名稱、主機集區名稱、應用程式組名和使用者主體名稱之類的全域中繼資料資訊。 儲存的中繼資料會在待用時加密，而異地多餘的鏡像則會保留在美國。 所有客戶資料（例如應用程式設定和使用者資料）都位於客戶選擇的位置，而不是由服務所管理。

服務中繼資料會在美國複寫，以供嚴重損壞修復之用。
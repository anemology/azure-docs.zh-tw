---
title: Azure 儲存體總管存取範圍 |Microsoft Docs
description: 瞭解 Azure 儲存體總管中的協助工具。 查看可用的螢幕閱讀者、縮放功能、高對比主題和快速鍵。
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: ca4a8d719277eaa1d853d53d282649f839256be9
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035480"
---
# <a name="storage-explorer-accessibility"></a>儲存體總管協助工具

## <a name="screen-readers"></a>螢幕助讀程式

儲存體總管支援在 Windows 和 Mac 上使用螢幕助讀程式。 針對各個平台建議使用下列螢幕助讀程式：

平台 | 螢幕閱讀程式
---------|--------------
Windows  | NVDA
Mac      | Voice Over
Linux    | Linux 上不支援 (螢幕讀取器) 

使用儲存體總管時，如果遇到涉及身障求助方面的問題，請[在 GitHub 回報您的問題](https://github.com/Microsoft/AzureStorageExplorer/issues)。

## <a name="zoom"></a>Zoom

您可以透過放大讓儲存體總管中的文字變大。 若要放大，按一下 [說明] 功能表中的 [放大]****。 您也可以使用 [說明] 功能表來縮小，並且將縮放層級重設回預設層級。

![說明功能表中的縮放選項][0]

縮放設定會將大部分的 UI 元素予以放大。 建議您也啟用作業系統的大型文字與縮放設定，以確保所有 UI 元素皆已正確放大。

## <a name="high-contrast-themes"></a>高對比主題

儲存體總管有兩個高對比佈景主題，**高對比淺色**和**高對比深色**。 您可以從 [說明] > [主題] 功能表中選取 [in] 來變更您的主題。

![佈景主題子功能表][1]

佈景主題設定會變更大部分 UI 元素的色彩。 建議您也啟用作業系統適用的高對比佈景主題，以確保所有 UI 元素的色彩能正確顯示。

## <a name="shortcut-keys"></a>快速鍵

### <a name="window-commands"></a>視窗命令

Command       | 鍵盤快速鍵
--------------|--------------------
開新視窗    | **Control+Shift+N**
關閉編輯器  | **Control+F4**
結束          | **Control+Shift+W**

### <a name="navigation-commands"></a>導覽命令

Command                | 鍵盤快速鍵
-----------------------|----------------------
焦點移至下一個窗格       | **F6**
焦點移至上一個窗格   | **Shift+F6**
總管               | **Control+Shift+E**
帳戶管理     | **Control+Shift+A**
切換提要欄位        | **Control+B**
活動記錄檔           | **Control+Shift+L**
動作與屬性 | **Control+Shift+P**
目前的編輯器         | **Control+Home**
下一個編輯器            | **Control+Page Down**
上一個編輯器        | **Control+Page Up**

### <a name="zoom-commands"></a>縮放命令

Command  | 鍵盤快速鍵
---------|------------------
放大  | **Control + =**
縮小 | **Control +-**

### <a name="blob-and-file-share-editor-commands"></a>Blob 和檔案共用編輯器命令

Command | 鍵盤快速鍵
--------|--------------------
上一步    | **Alt+向左箭**
下一頁 | **Alt + 向右鍵**
Up      | **Alt+向上箭**

### <a name="editor-commands"></a>編輯器命令

Command | 鍵盤快速鍵
--------|------------------
複製    | **Control+C**
剪下     | **Control+X**
貼上   | **Control+V**
Refresh  | **Control+R**

### <a name="other-commands"></a>其他命令

Command                | 鍵盤快速鍵
-----------------------|------------------
切換開發人員工具 | **F12**
重新載入                 | **Alt + Ctrl + R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png

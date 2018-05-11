---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: 常見的工作比較
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: b4d45ae61c5d7a7093d51c4440d11dd883c157a4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="common-tasks-comparison"></a>常見的工作比較

| 工作 | WPF | Xamarin.Forms |
|--- |--- |--- |
|使用按鈕在螢幕上顯示一則訊息|`MessageBox`|`Page.DisplayAlert`|
|建立計時器|`DispatcherTimer` 類別|`Device.StartTimer` 靜態方法|
|取得預設字型的大小|`SystemFonts` 靜態類別|`Device.GetNamedSize` 靜態方法|
|開啟 URI/URL|`Process.Start`|`Device.OpenUri`|
|顯示動作的工作表 （按鈕清單）|N/A|`Page.DisplayActionSheet`|

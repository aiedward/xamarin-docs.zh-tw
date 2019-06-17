---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: 常見的工作比較
description: 這份文件比較如何在 WPF 和 Xamarin.Forms 執行各種常見工作。 它會尋找在按鈕、 計時器、 字型大小、 開啟一個 URI，並顯示動作的工作表。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 2991e74ec59e3c43c665941706c2d9ac94bfb9ad
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269591"
---
# <a name="common-tasks-comparison"></a>常見的工作比較

| 工作 | WPF | Xamarin.Forms |
|--- |--- |--- |
|螢幕上的按鈕顯示一則訊息|`MessageBox`|`Page.DisplayAlert`|
|建立計時器|`DispatcherTimer` 類別|`Device.StartTimer` 靜態方法|
|取得預設字型的大小|`SystemFonts` 靜態類別|`Device.GetNamedSize` 靜態方法|
|開啟 URI/URL|`Process.Start`|`Device.OpenUri`|
|顯示動作的工作表 （按鈕清單）|N/A|`Page.DisplayActionSheet`|

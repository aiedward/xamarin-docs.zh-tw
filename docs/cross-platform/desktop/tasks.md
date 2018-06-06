---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: 常見的工作比較
description: 這份文件相比較如何 WPF 和 Xamarin.Forms 上執行各種常見的工作。 它會查看按鈕、 計時器、 字型大小、 開啟一個 URI，並顯示動作工作表。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 2991e74ec59e3c43c665941706c2d9ac94bfb9ad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780472"
---
# <a name="common-tasks-comparison"></a>常見的工作比較

| 工作 | WPF | Xamarin.Forms |
|--- |--- |--- |
|使用按鈕在螢幕上顯示一則訊息|`MessageBox`|`Page.DisplayAlert`|
|建立計時器|`DispatcherTimer` 類別|`Device.StartTimer` 靜態方法|
|取得預設字型的大小|`SystemFonts` 靜態類別|`Device.GetNamedSize` 靜態方法|
|開啟 URI/URL|`Process.Start`|`Device.OpenUri`|
|顯示動作的工作表 （按鈕清單）|N/A|`Page.DisplayActionSheet`|

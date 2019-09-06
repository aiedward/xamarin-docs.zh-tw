---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: 一般工作比較
description: 本檔會比較如何在 WPF 和 Xamarin 上執行各種常見工作。 它會查看按鈕、計時器、字型大小、開啟 URI，以及顯示動作表。
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: fecd8ed774adbacf69e3b2db514a4698e71711d8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290322"
---
# <a name="common-tasks-comparison"></a>一般工作比較

| 工作 | WPF | Xamarin.Forms |
|--- |--- |--- |
|在畫面上顯示具有按鈕的訊息|`MessageBox`|`Page.DisplayAlert`|
|建立計時器|`DispatcherTimer` 類別|`Device.StartTimer`靜態方法|
|取得預設字型大小|`SystemFonts`靜態類別|`Device.GetNamedSize`靜態方法|
|開啟 URI/URL|`Process.Start`|`Device.OpenUri`|
|顯示動作表（按鈕清單）|N/A|`Page.DisplayActionSheet`|

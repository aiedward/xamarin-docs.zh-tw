---
title: 範例整合
description: 範例會示範 Xamarin Workbooks 整合此文件連結。 連結的範例是由表示法轉譯和 SkiaSharp 所使用。
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: e35577b116180d2745e2f6afb792547f63873214
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117417"
---
# <a name="sample-integrations"></a>範例整合

請參閱[廚房 Sink] [ KitchenSink]範例，以整合的實用範例。 只需建置`KitchenSink.sln`在 Visual Studio for Mac 或 Visual Studio，然後開啟`KitchenSink.workbook`。

[![廚房接收整合螢幕擷取畫面](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

廚房接收範例將示範這兩組的概念：

* 表示片段示範如何使用`RepresentationManager`來加強轉譯使用內建的表示法。
* `Person`物件和其相關聯的 JavaScript 轉譯器會示範如何使用`ISerializableObject`而不需要經過表示提供者。

另請參閱[SkiaSharp] [ skiasharp]整合，使用現有的真實世界範例[表示](~/tools/workbooks/sdk/representations.md)Xamarin 活頁簿來呈現其型別所提供。

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks

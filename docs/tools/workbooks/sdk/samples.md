---
title: 範例整合
description: 範例會示範 Xamarin 活頁簿整合此文件連結。 連結的範例是由表示法呈現和 SkiaSharp 所使用。
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 75d88af4c294a35d45f05724eb96ce822cddf126
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793967"
---
# <a name="sample-integrations"></a>範例整合

請參閱[廚房 Sink] [ KitchenSink]範例的整合的操作範例。 只要建置`KitchenSink.sln`在 Visual Studio for Mac 或 Visual Studio，然後開啟`KitchenSink.workbook`。

[![廚房接收整合螢幕擷取畫面](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

「 廚房接收器 」 範例示範這兩組的概念：

* 表示片段示範如何使用`RepresentationManager`為了加強轉譯所使用的內建的表示法。
* `Person`物件和其相關聯的 JavaScript 轉譯器將示範如何使用`ISerializableObject`而不需要透過表示提供者。

另請參閱[SkiaSharp] [ skiasharp]真實世界範例中的整合，會使用現有[表示](~/tools/workbooks/sdk/representations.md)Xamarin 活頁簿來呈現其型別所提供。

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks

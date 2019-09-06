---
title: 範例整合
description: 本檔會連結至示範 Xamarin Workbooks 整合的範例。 連結的範例會使用表示轉譯和 SkiaSharp。
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: fbe471aa7f08d85a870d68505cf2c983b7e442e9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292805"
---
# <a name="sample-integrations"></a>範例整合

如需整合的實用範例，請參閱[廚房接收器][KitchenSink]範例。 只需`KitchenSink.sln`在 Visual Studio for Mac 或 Visual Studio 中建立， `KitchenSink.workbook`然後開啟。

[![廚房接收器整合螢幕擷取畫面](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

廚房 Sink 範例會示範這兩組概念：

* 標記法會示範如何使用`RepresentationManager`內建的標記法，來增強呈現。
* 物件及其關聯的 JavaScript 轉譯器會示範`ISerializableObject`如何使用，而不需要透過表示提供者。 `Person`

另請參閱[SkiaSharp][skiasharp] ，以取得整合的實際範例，使用 Xamarin Workbooks 所提供的現有[標記法](~/tools/workbooks/sdk/representations.md)來呈現其類型。

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks

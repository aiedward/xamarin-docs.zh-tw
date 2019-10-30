---
title: 範例整合
description: This document links to samples that demonstrate Xamarin Workbooks integrations. Linked samples work with representation rendering and SkiaSharp.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: fa66ee2b2b469900381e2d31dc5dec49eb42c4f6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018114"
---
# <a name="sample-integrations"></a>範例整合

See the [Kitchen Sink][KitchenSink] sample for a working example of an integration. Simply build `KitchenSink.sln` in Visual Studio for Mac or Visual Studio and then open `KitchenSink.workbook`.

[![Kitchen Sink Integration Screenshot](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

The Kitchen Sink sample demonstrates both sets of concepts:

* The representation pieces demonstrate how to use `RepresentationManager` to enhance rendering by using the built-in representations.
* The `Person` object and its associated JavaScript renderer demonstrate using `ISerializableObject` without going through a representation provider.

Also see [SkiaSharp][skiasharp] for a real-world example of an integration that uses the existing [representations](~/tools/workbooks/sdk/representations.md) provided by Xamarin Workbooks to render its types.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks

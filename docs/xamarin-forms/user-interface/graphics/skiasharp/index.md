---
title: 使用 Xamarin.Forms 中 SkiaSharp
description: 使用 Xamarin.Forms 應用程式中的 2D 圖形 SkiaSharp
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 09/11/2017
ms.openlocfilehash: e3444411c80ecaee105cce7c10f7bec7583dc2a5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="using-skiasharp-in-xamarinforms"></a>使用 Xamarin.Forms 中 SkiaSharp

_使用 Xamarin.Forms 應用程式中的 2D 圖形 SkiaSharp_

SkiaSharp 是.NET 和 C# 由廣泛使用在 Google 產品的開放原始碼 Skia 圖形引擎所提供的 2D 圖形系統。 您可以使用 SkiaSharp Xamarin.Forms 應用程式中，以繪製 2D 向量圖形、 點陣圖、 圖示和文字。 請參閱[2D 繪圖](~/graphics-games/skiasharp/index.md)SkiaSharp 程式庫的一般資訊和其他教學課程指南。

本指南假設您熟悉 Xamarin.Forms 程式設計。

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Xamarin.Forms 的網路研討會： SkiaSharp**

## <a name="skiasharp-preliminaries"></a>SkiaSharp 準備工作

Xamarin.Forms 的 SkiaSharp 會封裝以 NuGet 套件。 您已在 Visual Studio 或 Visual Studio for Mac 來建立 Xamarin.Forms 方案之後，您可以使用 NuGet 套件管理員來搜尋**SkiaSharp.Views.Forms**封裝，並將它加入至您的方案。 如果您核取**參考**區段的每個專案加入 SkiaSharp 之後，您可以看到各種**SkiaSharp**已新增至每個方案中的專案程式庫。

Xamarin.Forms 應用程式的目標 iOS，如果使用專案屬性頁面來將最小的部署目標變更為 iOS 8.0 以上版本。

在使用 SkiaSharp 任何 C# 頁面中，您會想要包含`using`指示詞[ `SkiaSharp` ](https://developer.xamarin.com/api/namespace/SkiaSharp/)命名空間，其中包含所有 SkiaSharp 類別、 結構和列舉型別，您將使用中的圖形程式設計。 您也需要`using`指示詞[ `SkiaSharp.Views.Forms` ](https://developer.xamarin.com/api/namespace/SkiaSharp.Views.Forms/)特有 Xamarin.Forms 類別命名空間。 這是多小命名空間，具有最重要的類別是[ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/)。 此類別衍生自 Xamarin.Forms`View`類別，並裝載您 SkiaSharp 圖形輸出。

> [!IMPORTANT]
> `SkiaSharp.Views.Forms`命名空間也包含`SKGLView`類別衍生自`View`但 OpenGL 用於呈現圖形。 為了簡單起見，本指南會限制本身`SKCanvasView`，但使用`SKGLView`是相當類似。

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[SkiaSharp 繪圖基本概念](basics/index.md)

您可以使用 SkiaSharp 繪製簡單圖形數字有些圓形、 橢圓形、 和矩形。 在顯示這些數字，您將了解 SkiaSharp 座標、 大小和色彩。

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[SkiaSharp 線條和路徑](paths/index.md)

圖形路徑是一系列連接的直線和曲線。 路徑可以圖案，填滿，或兩者。 本主題包含線條繪圖，包括筆劃的端點和聯結，以及虛線和虛線，但停止不曲線幾何的許多層面。

## <a name="skiasharp-transformstransformsindexmd"></a>[SkiaSharp 轉換](transforms/index.md)

轉換允許一致的方式轉譯、 縮放、 旋轉或扭曲圖形物件。 本文也會示範如何使用標準 3-3 轉換矩陣的建立非仿射轉換，並將轉換套用至路徑。

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[SkiaSharp 曲線和路徑](curves/index.md)

瀏覽的路徑會繼續進行將曲線路徑物件，並利用功能強大的路徑中的其他功能。 您會看到如何簡潔的文字字串中指定完整的路徑、 如何使用路徑的效果，以及如何深入探究路徑內部項目。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [SkiaSharp 使用 Xamarin.Forms 網路研討會 （影片）](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)

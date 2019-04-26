---
title: Xamarin.Forms 中的 SkiaSharp 圖形
description: SkiaSharp 是.NET 和 C# 由 Google 產品中廣泛使用的開放原始碼 Skia 圖形引擎所提供的 2D 圖形系統。 本指南說明如何為 2D 圖形，在 Xamarin.Forms 應用程式中使用 SkiaSharp。
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 9cd5a25c598a25500aee595439aeecd648d50526
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291142"
---
# <a name="skiasharp-graphics-in-xamarinforms"></a>Xamarin.Forms 中的 SkiaSharp 圖形

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_2D 圖形，在 Xamarin.Forms 應用程式中使用 SkiaSharp_

SkiaSharp 是.NET 和 C# 由 Google 產品中廣泛使用的開放原始碼 Skia 圖形引擎所提供的 2D 圖形系統。 您可以在 Xamarin.Forms 應用程式中使用 SkiaSharp，繪製 2D 向量圖形、 點陣圖和文字。 請參閱[2D 繪圖](~/graphics-games/skiasharp/index.md)SkiaSharp 程式庫的詳細資訊和一些其他教學課程的指南。

本指南假設您已熟悉使用 Xamarin.Forms 程式設計。

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**網路研討會：適用於 Xamarin.Forms SkiaSharp**

## <a name="skiasharp-preliminaries"></a>SkiaSharp 準備工作

適用於 Xamarin.Forms SkiaSharp 會封裝為 NuGet 套件。 您已在 Visual Studio 或 Visual Studio for Mac 中建立 Xamarin.Forms 方案之後，您可以使用 NuGet 套件管理員來搜尋**SkiaSharp.Views.Forms**封裝，並將它新增至您的方案。 如果您勾選**參考**區段的每個專案新增 SkiaSharp 之後，您可以看到各種**SkiaSharp**程式庫已新增至每個方案中專案。

如果您的 Xamarin.Forms 應用程式以 iOS 為目標，使用專案屬性頁面來將最小的部署目標變更為 iOS 8.0 以上版本。

在使用 SkiaSharp 任何 C# 頁面中，您會想要包含`using`指示詞[ `SkiaSharp` ](xref:SkiaSharp)命名空間，其中包含所有 SkiaSharp 類別、 結構和列舉型別，您將使用在您的圖形程式設計。 您也會想`using`指示詞[ `SkiaSharp.Views.Forms` ](xref:SkiaSharp.Views.Forms) Xamarin.Forms 的特定類別的命名空間。 這是更小命名空間，其中最重要的類別是[ `SKCanvasView` ](xref:SkiaSharp.Views.Forms.SKCanvasView)。 這個類別衍生自 Xamarin.Forms`View`類別，並裝載您 SkiaSharp 圖形輸出。

> [!IMPORTANT]
> `SkiaSharp.Views.Forms`命名空間也包含`SKGLView`衍生自類別`View`但使用 OpenGL 的轉譯圖形。 為了簡單起見，本指南會限制本身`SKCanvasView`，但使用`SKGLView`是相當類似。

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[SkiaSharp 繪圖基本概念](basics/index.md)

您可以使用 SkiaSharp 繪圖的最簡單的圖形數字有些圓形、 橢圓形和矩形。 在顯示這些數字，您將了解 SkiaSharp 座標、 大小和色彩。 顯示的文字和點陣圖是比較複雜，但這些文章也會介紹這些技術。

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[SkiaSharp 線條和路徑](paths/index.md)

圖形路徑是一系列連接的直線和曲線。 路徑可以經過繪製，填滿，或兩者。 這篇文章包含線條繪圖，包括筆劃 ends 和 joins，和虛線和虛線表示，但除了曲線幾何的停駐點的許多層面。

## <a name="skiasharp-transformstransformsindexmd"></a>[SkiaSharp 轉換](transforms/index.md)

轉換允許一致的方式轉譯、 縮放、 旋轉或扭曲圖形物件。 這篇文章也會示範如何使用標準的 3-3 轉換矩陣，以及在建立非仿射轉換，和將轉換套用至路徑。

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[SkiaSharp 曲線和路徑](curves/index.md)

探勘的路徑會繼續進行將曲線加入至路徑的物件，並利用其他強大的路徑 」 功能。 您會看到如何簡潔的文字字串中指定完整的路徑、 如何使用路徑的效果，以及如何深入了解路徑內部項目。

## <a name="skiasharp-bitmapsbitmapsindexmd"></a>[SkiaSharp 點陣圖](bitmaps/index.md)

點陣圖是對應至顯示裝置的像素的位元的矩形陣列。 這一系列的文章會示範如何載入、 儲存、 顯示、 建立、 上繪製，以動畫顯示，以及存取 SkiaSharp 點陣圖的位元。

## <a name="skiasharp-effectseffectsindexmd"></a>[SkiaSharp 效果](effects/index.md)

效果是改變正常顯示的圖形，包括線性和循環的漸層、 點陣圖並排顯示、 blend 模式、 模糊及其他的屬性。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [SkiaSharp 使用 Xamarin.Forms 網路研討會 （影片）](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)

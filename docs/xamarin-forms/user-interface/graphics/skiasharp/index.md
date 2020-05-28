---
title: SkiaSharp 圖形Xamarin.Forms
description: 'SkiaSharp 是 .NET 和 c # 的2D 圖形系統，由 Google 產品中廣泛使用的開放原始碼 Skia 圖形引擎提供技術支援。 本指南說明如何在您的應用程式中使用2D 圖形的 SkiaSharp Xamarin.Forms 。'
ms.prod: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 855bd0d357950b019487b3ea05e379915f54b9d4
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127627"
---
# <a name="skiasharp-graphics-in-xamarinforms"></a>SkiaSharp 圖形Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_在應用程式中使用2D 圖形的 SkiaSharp Xamarin.Forms_

SkiaSharp 是 .NET 和 c # 的2D 圖形系統，由 Google 產品中廣泛使用的開放原始碼 Skia 圖形引擎提供技術支援。 您可以在應用程式中使用 SkiaSharp Xamarin.Forms 來繪製2d 向量圖形、點陣圖和文字。 如需有關 SkiaSharp 程式庫和一些其他教學課程的一般資訊，請參閱[2D 繪圖](~/graphics-games/skiasharp/index.md)指南。

本指南假設您已熟悉程式 Xamarin.Forms 設計。

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**網路研討會： SkiaSharpXamarin.Forms**

## <a name="skiasharp-preliminaries"></a>SkiaSharp 準備工作

的 SkiaSharp Xamarin.Forms 會封裝為 NuGet 套件。 Xamarin.Forms在 Visual Studio 或 Visual Studio for Mac 中建立解決方案之後，您可以使用 NuGet 套件管理員來搜尋**SkiaSharp** ，並將它新增至您的方案。 如果您在新增 SkiaSharp 之後，檢查每個專案的 [**參考**] 區段，您可以看到各種**SkiaSharp**程式庫已加入至方案中的每個專案。

如果您 Xamarin.Forms 的應用程式是以 ios 為目標，請使用 [專案屬性] 頁面，將最小部署目標變更為 ios 8.0。

在使用 SkiaSharp 的任何 c # 頁面中，您會想要包含命名空間的指示詞 `using` [`SkiaSharp`](xref:SkiaSharp) ，其中包含您將在圖形程式設計中使用的所有 SkiaSharp 類別、結構和列舉。 針對特定的類別，您也需要命名空間的指示詞 `using` [`SkiaSharp.Views.Forms`](xref:SkiaSharp.Views.Forms) Xamarin.Forms 。 這是較小的命名空間，最重要的類別是 [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) 。 這個類別衍生自 Xamarin.Forms `View` 類別，並裝載您的 SkiaSharp 圖形輸出。

> [!IMPORTANT]
> `SkiaSharp.Views.Forms`命名空間也包含 `SKGLView` 衍生自的類別， `View` 但會使用 OpenGL 來呈現圖形。 為了簡單起見，本指南將本身限制為 `SKCanvasView` ，但 `SKGLView` 改用相當類似。

## <a name="skiasharp-drawing-basics"></a>[SkiaSharp 繪圖基本概念](basics/index.md)

您可以使用 SkiaSharp 繪製的一些最簡單的圖形資料是圓形、橢圓形和矩形。 在顯示這些數位時，您將瞭解 SkiaSharp 座標、大小和色彩。 文字和點陣圖的顯示更複雜，但這些文章也會介紹這些技巧。

## <a name="skiasharp-lines-and-paths"></a>[SkiaSharp 線條和路徑](paths/index.md)

圖形路徑是一系列連接的直線和曲線。 路徑可以是 [繪製]、[已填滿] 或 [兩者]。 這篇文章包含線條繪製的許多層面，包括筆劃結束和聯結，以及虛線和虛線，但會停止短曲線幾何。

## <a name="skiasharp-transforms"></a>[SkiaSharp 轉換](transforms/index.md)

轉換可讓繪圖物件以一致的轉譯、縮放、旋轉或扭曲。 本文也會示範如何使用標準的 3-3 轉換矩陣來建立非仿射轉換，並將轉換套用至路徑。

## <a name="skiasharp-curves-and-paths"></a>[SkiaSharp 曲線和路徑](curves/index.md)

路徑的探索會繼續新增曲線至路徑物件，並利用其他強大的路徑功能。 您將瞭解如何以簡潔的文字字串來指定完整的路徑、如何使用路徑效果，以及如何深入探索路徑內部。

## <a name="skiasharp-bitmaps"></a>[SkiaSharp 點陣圖](bitmaps/index.md)

點陣圖是與顯示裝置圖元對應的位矩形陣列。 這一系列的文章說明如何載入、儲存、顯示、建立、繪製、製作動畫，以及存取 SkiaSharp 點陣圖的位。

## <a name="skiasharp-effects"></a>[SkiaSharp 效果](effects/index.md)

效果是改變圖形一般顯示的屬性，包括線性和圓形漸層、點陣圖並排顯示、blend 模式、模糊等等。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [帶 Xamarin.Forms 網路研討會的 SkiaSharp （影片）](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)

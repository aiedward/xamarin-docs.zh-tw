---
title: SkiaSharp 圖形 Xamarin.Forms
description: 'SkiaSharp 是 .NET 和 c # 的2D 圖形系統，由可在 Google 產品中廣泛使用的開放原始碼 Skia 圖形引擎提供技術支援。 本指南說明如何在您的應用程式中使用2D 圖形的 SkiaSharp Xamarin.Forms 。'
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b91d8f459992d33f417e99f5d92ece63f887f691
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373428"
---
# <a name="skiasharp-graphics-in-no-locxamarinforms"></a>SkiaSharp 圖形 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_在您的應用程式中使用2D 圖形的 SkiaSharp Xamarin.Forms_

SkiaSharp 是 .NET 和 c # 的2D 圖形系統，由可在 Google 產品中廣泛使用的開放原始碼 Skia 圖形引擎提供技術支援。 您可以在應用程式中使用 SkiaSharp Xamarin.Forms 來繪製2d 向量圖形、點陣圖和文字。

本指南假設您已經熟悉程式 Xamarin.Forms 設計。

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**網路研討會： SkiaSharp Xamarin.Forms**

## <a name="skiasharp-preliminaries"></a>SkiaSharp 準備工作

的 SkiaSharp Xamarin.Forms 封裝為 NuGet 套件。 在 Xamarin.Forms Visual Studio 或 Visual Studio for Mac 中建立解決方案之後，您可以使用 NuGet 套件管理員來搜尋 **SkiaSharp** ，然後將它新增至您的方案。 如果您在新增 SkiaSharp 後檢查每個專案的 [ **參考** ] 區段，您可以看到已將各種 **SkiaSharp** 程式庫加入至方案中的每個專案。

如果您的 Xamarin.Forms 應用程式是以 iOS 為目標，請編輯其 **plist** 檔案，以將最低部署目標變更為 ios 8.0。

在使用 SkiaSharp 的任何 c # 頁面中，您會想要包含命名空間的指示詞 `using` [`SkiaSharp`](xref:SkiaSharp) ，其中包含您將在圖形程式設計中使用的所有 SkiaSharp 類別、結構和列舉。 您也需要適用于 `using` [`SkiaSharp.Views.Forms`](xref:SkiaSharp.Views.Forms) 特定類別之命名空間的指示詞 Xamarin.Forms 。 這是較小的命名空間，其中最重要的類別 [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) 。 這個類別衍生自 Xamarin.Forms `View` 類別，並裝載您的 SkiaSharp 圖形輸出。

> [!IMPORTANT]
> `SkiaSharp.Views.Forms`命名空間也包含 `SKGLView` 衍生自的類別， `View` 但使用 OpenGL 來呈現圖形。 為了簡單起見，本指南會將自己限制為 `SKCanvasView` ，但使用 `SKGLView` 相當類似。

## <a name="skiasharp-drawing-basics"></a>[SkiaSharp 繪圖基本概念](basics/index.md)

您可以使用 SkiaSharp 繪製的一些最簡單圖形圖是圓形、橢圓形和矩形。 在顯示這些圖表時，您將會瞭解 SkiaSharp 座標、大小和色彩。 顯示文字和點陣圖更為複雜，但這些文章也會介紹這些技術。

## <a name="skiasharp-lines-and-paths"></a>[SkiaSharp 線條和路徑](paths/index.md)

圖形路徑是一系列連接的直線和曲線。 路徑可以是邊邊、填滿或兩者。 本文包含許多方面的線條繪圖，包括筆劃結束和聯結，以及虛線和虛線，但會停止曲線幾何的短。

## <a name="skiasharp-transforms"></a>[SkiaSharp 轉換](transforms/index.md)

轉換可以一致地轉譯、縮放、旋轉或扭曲繪圖物件。 本文也會說明如何使用標準的 3 x 3 轉換矩陣來建立非仿射轉換，以及將轉換套用至路徑。

## <a name="skiasharp-curves-and-paths"></a>[SkiaSharp 曲線和路徑](curves/index.md)

探索路徑會繼續將曲線新增至路徑物件，並利用其他強大的路徑功能。 您將瞭解如何以簡潔的文字字串來指定整個路徑、如何使用路徑效果，以及如何深入探討路徑內部。

## <a name="skiasharp-bitmaps"></a>[SkiaSharp 點陣圖](bitmaps/index.md)

點陣圖是與顯示裝置的圖元相對應之位的矩形陣列。 這一系列的文章會示範如何載入、儲存、顯示、建立、繪製、建立動畫，以及存取 SkiaSharp 點陣圖的位。

## <a name="skiasharp-effects"></a>[SkiaSharp 效果](effects/index.md)

效果是改變圖形一般顯示的屬性，包括線性和圓形的漸層、點陣圖並排顯示、blend 模式、模糊和其他專案。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SkiaSharp Xamarin.Forms 網路研討會 (影片) ](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
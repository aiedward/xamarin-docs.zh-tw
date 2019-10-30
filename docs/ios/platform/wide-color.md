---
title: Xamarin. iOS 中的寬色彩
description: 本檔討論寬色彩，以及如何在 Xamarin 或 Xamarin. Mac 應用程式中使用。
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: e7240a271de1f0199c2c9fc045f5c95745eb98c5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031251"
---
# <a name="wide-color-in-xamarinios"></a>Xamarin. iOS 中的寬色彩

_本文涵蓋寬色彩，以及如何在 Xamarin 或 Xamarin. Mac 應用程式中使用。_

iOS 10 和 macOS Sierra 增強了擴充範圍像素格式的支援，以及整個系統的寬範圍色彩空間，包括核心圖形、核心影像、金屬和 AVFoundation 等架構。 在整個圖形堆疊中提供這種行為，即可進一步分階段減緩具有寬色彩顯示的裝置支援。

## <a name="asset-catalogs"></a>資產目錄

### <a name="supporting-wide-color-with-asset-catalogs"></a>支援資產目錄的寬色彩

在 iOS 10 和 macOS Sierra 中，Apple 已擴充用來在應用程式組合中包含和分類靜態影像內容以支援寬色彩的資產目錄。

使用資產目錄可為應用程式提供下列優點：

- 它們提供靜態映射資產的最佳部署選項。
- 它們支援自動顏色更正。
- 它們支援自動像素格式優化。
- 它們支援應用程式切割和應用程式 Thinning，以確保只有相關的內容可以傳遞給使用者的裝置。

Apple 已對資產目錄進行下列增強功能，以提供寬色彩支援：

- 它們支援16位（每色頻道）來源內容。
- 它們支援藉由顯示色域來編目內容。 內容可以標記為 sRGB 或顯示 P3 gamuts。

開發人員在其應用程式中有三個支援寬色內容的選項：

1. **不執行任何動作**-因為寬色內容應該僅適用于應用程式需要顯示鮮豔色彩的情況（可增強使用者體驗的情況），但這項需求以外的內容應該保持不維持。 在所有硬體情況下，它會繼續正確轉譯。
2. **升級現有內容以顯示 P3** -這需要開發人員以 P3 格式的新升級檔案取代其資產目錄中現有的影像內容，並在資產編輯器中將其標記為。 在組建階段，會從這些資產產生 sRGB 衍生映射。
3. **提供優化的資產內容**-在此情況下，開發人員會同時為資產目錄中的每個影像提供8位的 sRGB 和16位的顯示 P3 願景（在資產編輯器中正確標記）。

### <a name="asset-catalog-deployment"></a>資產類別目錄部署

當開發人員使用包含寬色彩影像內容的資產目錄將應用程式提交至 App Store 時，將會發生下列情況：

- 將應用程式部署至使用者時，應用程式配量會確保只有適當的內容 variant 會傳遞給使用者的裝置。
- 在不支援寬色彩的裝置上，沒有包含寬色內容的承載成本，因為它永遠不會寄送到裝置。
- macOS Sierra （及更新版本）上的 `NSImage` 將會自動選取硬體顯示的最佳內容表示。
- 當或裝置硬體顯示特性變更時，會自動重新整理顯示的內容。

### <a name="asset-catalog-storage"></a>資產目錄存放器

資產目錄存放器具有下列應用程式的屬性和含意：

- 在建立期間，Apple 會嘗試透過高品質的影像轉換來優化影像內容的儲存。
- 針對寬色彩內容，每個色彩頻道會使用16位。
- 內容相依影像壓縮是用來降低交付內容的大小。 已加入新的「損壓縮」，以進一步優化內容大小。

## <a name="rendering-off-screen-images-in-app"></a>在應用程式中呈現非螢幕影像

根據所建立的應用程式類型，它可能會允許使用者包含其從網際網路收集的影像內容，或直接在應用程式內建立影像內容（例如，向量繪圖應用程式）。

在這兩種情況下，應用程式都可以使用已新增至 iOS 和 macOS 的增強功能，以寬色彩呈現所需的影像。

### <a name="drawing-wide-color-in-ios"></a>在 iOS 中繪製寬色彩

在討論如何正確地在 iOS 10 中繪製寬色彩影像之前，請先查看下列常見的 iOS 繪圖程式碼：

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    UIGraphics.BeginImageContext (size);

    ...

    UIGraphics.EndImageContext ();
    return image = UIGraphics.GetImageFromCurrentImageContext ();
    }
```

標準程式碼有問題，必須先加以解決，_才能_用來繪製寬色彩影像。 用來啟動 iOS 映射繪製的 `UIGraphics.BeginImageContext (size)` 方法有下列限制：

- 它無法建立每個彩色通道具有超過8個位的影像內容。
- 它不能代表擴充範圍的 sRGB 色彩空間中的色彩。
- 它無法提供介面來建立非 sRGB 色彩空間中的內容，因為在背景中呼叫的是低層級 C 常式。

若要處理這些限制，並在 iOS 10 中繪製寬色彩影像，請改用下列程式碼：

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    var render = new UIGraphicsImageRenderer (size);

    var image = render.CreateImage ((UIGraphicsImageRendererContext context) => {
        var bounds = context.Format.Bounds;
        CGRect slice;
        CGRect remainder;
        bounds.Divide (bounds.Width / 2, CGRectEdge.MinXEdge, out slice, out remainder);

        var redP3 = UIColor.FromDisplayP3 (1.0F, 0.0F, 0.0F, 1.0F);
        redP3.SetColor ();
        context.FillRect (slice);

        var redRGB = UIColor.Red;
        redRGB.SetColor ();
        context.FillRect (remainder);
    });

    // Return results
    return image;
}
```

新的 `UIGraphicsImageRenderer` 類別會建立新的影像內容，它能夠處理擴充範圍的 sRGB 色彩空間，而且具有下列功能：

- 預設會以完全色彩管理。
- 根據預設，它支援擴充範圍的 sRGB 色彩空間。
- 它會根據執行應用程式的 iOS 裝置功能，以智慧方式決定是否應該在 sRGB 或擴充範圍的 sRGB 色彩空間中呈現。
- 它會完整且自動管理影像內容（`CGContext`）存留期，讓開發人員不必擔心呼叫開始和結束內容命令。
- 它與 `UIGraphics.GetCurrentContext()` 方法相容。

呼叫 `UIGraphicsImageRenderer` 類別的 `CreateImage` 方法，以建立寬色彩影像，並傳遞含有影像內容的完成處理常式以繪製至。 所有繪圖都是在此完成處理常式內完成，如下所示：

- `UIColor.FromDisplayP3` 方法會在寬範圍內建立新的完全飽和紅色色彩，以顯示 P3 色彩空間，並用來繪製矩形的前半部。 
- 矩形的後半部會以一般的 sRGB 完全飽和紅色色彩繪製，以進行比較。

### <a name="drawing-wide-color-in-macos"></a>在 macOS 中繪製寬色彩

`NSImage` 類別已在 macOS Sierra 中展開，以支援寬色彩影像的繪圖。 例如:

```csharp
var size = CGSize(250,250);
var wideColorImage = new NSImage(size, false, (drawRect) =>{
    var rects = drawRect.Divide(drawRect.Size.Width/2,CGRect.MinXEdge);

    var color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    var path = new NSBezierPath(rects.Slice).Fill();

    color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    path = new NSBezierPath(rects.Remainder).Fill();

    // Return modified
    return true;
});
```

## <a name="rendering-on-screen-images-in-app"></a>在應用程式中呈現螢幕影像

若要在螢幕上呈現寬色彩影像，此程式的運作方式類似于針對上述的 macOS 和 iOS 繪製全螢幕色彩影像。

### <a name="rendering-on-screen-in-ios"></a>在 iOS 上呈現畫面

當應用程式需要在 iOS 中以寬色彩呈現影像時，請以平常的方式覆寫 `UIView` 中的 `Draw` 方法。 例如:

```csharp
using System;
using UIKit;
using CoreGraphics;

namespace MonkeyTalk
{
    public class MonkeyView : UIView
    {
        public MonkeyView ()
        {
        }

        public override void Draw (CGRect rect)
        {
            base.Draw (rect);

            // Draw the image to screen
        ...
        }
    }
}
```

當 iOS 10 與上面顯示的 `UIGraphicsImageRenderer` 類別搭配使用時，它會以智慧方式決定是否應該根據呼叫 `Draw` 方法時，應用程式執行所在的 iOS 裝置功能，在 sRGB 或擴充範圍的 sRGB 色彩空間中呈現。 此外，在 iOS 9.3 之後，`UIImageView` 也已經過色彩管理。

如果應用程式需要知道如何在 `UIView` 或 `UIViewController`上進行轉譯，它可以檢查 `UITraitCollection` 類別的新 `DisplayGamut` 屬性。 此值將會是下列的 `UIDisplayGamut` 列舉：

- P3
- Srgb
- 未指定

如果應用程式想要控制用來繪製影像的色彩空間，它可以使用 `CALayer` 的新 `ContentsFormat` 屬性來指定所需的色彩空間。 此值可以是下列的 `CAContentsFormat` 列舉：

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>在 macOS 中以畫面呈現

當應用程式需要在 macOS 中以寬色彩呈現影像時，請以平常的方式覆寫 `NSView` 中的 `DrawRect` 方法。 例如:

```csharp
using System;
using AppKit;
using CoreGraphics;
using CoreAnimation;

namespace MonkeyTalkMac
{
    public class MonkeyView : NSView
    {
        public MonkeyView ()
        {
        }

        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Draw graphics into the view
            ...
        }
    }
}
```

同樣地，它會根據呼叫 `DrawRect` 方法時，以應用程式執行所在的 Mac 硬體功能，以智慧方式決定是否應該在 sRGB 或擴充範圍的 sRGB 色彩空間中呈現。

如果應用程式想要控制用來繪製影像的色彩空間，它可以使用 `NSWindow` 類別的新 `DepthLimit` 屬性來指定所需的色彩空間。 此值可以是下列的 `NSWindowDepth` 列舉：

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb

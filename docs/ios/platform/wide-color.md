---
title: 在 Xamarin.iOS 中的寬色域
description: 本文件討論各種色彩，以及如何使用它的 Xamarin.iOS 或 Xamarin.Mac 應用程式中。
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 523aa1a97e1c536b5afbdb66c52f605382fa338d
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268802"
---
# <a name="wide-color-in-xamarinios"></a>在 Xamarin.iOS 中的寬色域

_本文章涵蓋寬的色彩，以及如何在 Xamarin.iOS 或 Xamarin.Mac 應用程式中使用。_

iOS 10 和 macOS Sierra 增強的擴充範圍像素格式和整個系統，包括架構，例如核心圖形、 Core 映像、 金屬和 AVFoundation 寬色域圖色彩空間的支援。 藉由提供整個圖形堆疊在這種行為，進一步降低與寬的色彩顯示裝置的支援。

## <a name="asset-catalogs"></a>資產目錄

### <a name="supporting-wide-color-with-asset-catalogs"></a>支援使用資產目錄寬色域

在 iOS 10 和 macOS Sierra，Apple 已擴充資產目錄用來包含和分類的應用程式套件組合中的靜態影像內容支援廣泛的色彩。

使用資產目錄可提供下列優點的應用程式：

- 它們提供靜態影像資產的最佳部署選項。
- 它們支援自動色彩修正。
- 它們支援自動的像素格式最佳化。
- 它們支援應用程式配量和應用程式精簡，以確保只有已相關取得內容傳遞給使用者的裝置。

Apple 對下列增強功能資產目錄寬色域支援：

- 它們支援 16 位元 （每個色頻） 來源內容。
- 它們支援顯示所有編製目錄的內容。 可以標記 [sRGB] 或 [Display P3 色階的內容。

開發人員會有自己的應用程式中支援各種色彩內容的三個選項：

1. **不執行任何動作**-寬色域內容應該只用於在應用程式必須顯示逼真的色彩 （其中它們將會增強使用者體驗） 的情況下，因為這項需求以外的內容應該保持為-是。 它會繼續在所有硬體的情況下正確呈現。
2. **升級現有的內容至 Display P3** -這會要求開發人員在其資產目錄中的現有映像內容以新的升級檔案取代 P3 格式，並標記這類資產編輯器中。 在建置階段的 sRGB 衍生的映像將會產生從這些資產。
3. **提供最佳化的資產內容**-在此情況下，開發人員將提供的 8 位元 sRGB 和 16 位元 Display P3 願景的資產目錄 （適當地標記資產編輯器） 中的每個映像。

### <a name="asset-catalog-deployment"></a>資產目錄部署

會發生下列情形時，開發人員會提交至 App Store 包含寬色域映像內容的資產目錄與應用程式：

- 當應用程式部署至使用者時，應用程式配量可確保只有適當的內容變數會傳遞到使用者的裝置。
- 在裝置上支援各種色彩，沒有任何裝載成本包括各種色彩內容，因為它永遠不會傳送到裝置。
- `NSImage` 在 macOS Sierra （和更新版本） 會自動選取最佳的內容表示硬體的顯示。
- 自動將重新整理顯示的內容，或裝置硬體顯示特性變更時。

### <a name="asset-catalog-storage"></a>資產目錄儲存體

資產目錄儲存體有下列的內容和應用程式的影響：

- 在建置時，Apple 會嘗試最佳化透過高品質影像轉換的映像內容的儲存體。
- 16 位元用於每一色寬色彩內容。
- 使用相依的映像壓縮以降低交付項目內容大小的內容。 已加入新的 「 遺失 」 壓縮以進一步最佳化內容的大小。

## <a name="rendering-off-screen-images-in-app"></a>轉譯應用程式中的幕外映像

根據所建立的應用程式的類型，它可能會允許使用者包括它們已經從網際網路收集或建立映像內容，直接在應用程式 （例如向量，例如繪圖應用程式） 的映像內容。

在這兩種情況下，應用程式可能會呈現所需的影像使用增強的功能新增至 iOS 和 macOS 的色彩寬螢幕外。

### <a name="drawing-wide-color-in-ios"></a>在 iOS 中繪製寬色域

之前討論過如何正確繪製在 iOS 10 中的 [寬色域圖，看看下列常見 iOS 繪圖程式碼：

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

使用標準的程式碼需要解決的問題_之前_它可以用來繪製的寬色域映像。 `UIGraphics.BeginImageContext (size)`方法用來啟動 iOS 影像繪製具有下列限制：

- 它無法建立超過每色頻 8 位元映像內容。
- 它不能代表延伸範圍 sRGB 色彩空間中的色彩。
- 它並沒有提供介面，以建立非 sRGB 色彩空間中的內容，因為低層級的 C 常式在背景中所呼叫的能力。

若要處理這些限制，並在 iOS 10 中繪製的寬色域映像，請改為使用下列程式碼：

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

新`UIGraphicsImageRenderer`類別會建立新的映像內容，能夠處理延伸範圍 sRGB 色彩空間，而且它有下列功能：

- 它是完全受控預設的色彩。
- 它支援預設的延伸範圍 sRGB 色彩空間。
- 它會聰明地判斷應該轉譯 sRGB 或延伸範圍 sRGB 色彩空間的 iOS 裝置上執行的應用程式的功能為基礎。
- 它完整和自動管理的映像內容 (`CGContext`) 存留期，讓開發人員不必擔心呼叫開始及結束內容命令。
- 它適用於`UIGraphics.GetCurrentContext()`方法。

`CreateImage`方法的`UIGraphicsImageRenderer`呼叫來建立各種色彩映像並傳遞到繪製的映像內容的完成處理常式類別。 所有繪圖是在這個完成處理常式內，如下所示：

- `UIColor.FromDisplayP3`方法在廣泛的 Display P3 色彩空間中建立新的完全飽和紅色色彩，以及它用來繪製矩形的第一個部分。 
- 第二個矩形的下半部完全繪製在正常的 sRGB 飽和紅色進行比較。

### <a name="drawing-wide-color-in-macos"></a>在 macOS 中繪製寬色域

`NSImage`已在 macOS Sierra 支援寬色域映像的繪圖中擴充類別。 例如: 

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

## <a name="rendering-on-screen-images-in-app"></a>在應用程式中呈現螢幕映像

螢幕上呈現寬色域映像，程序的運作類似於繪製 macOS 及 iOS 以上所顯示的幕外的寬色域映像。

### <a name="rendering-on-screen-in-ios"></a>在 iOS 中將螢幕上呈現

當應用程式需要在螢幕上在 iOS 中各種色彩呈現影像時，覆寫`Draw`方法的`UIView`如往常般的有問題。 例如: 

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

使用 iOS 10 一樣`UIGraphicsImageRenderer`如上所示，它會聰明地判斷應該轉譯 sRGB 或延伸範圍 sRGB 色彩空間時執行的應用程式的 iOS 裝置的功能為基礎的類別`Draw`呼叫方法。 此外，`UIImageView`已受管理 iOS 9.3 以及之後的色彩。

如果應用程式必須知道如何轉譯是在`UIView`或是`UIViewController`，它可以檢查新`DisplayGamut`屬性`UITraitCollection`類別。 這個值會`UIDisplayGamut`下列的列舉：

- P3
- Srgb
- 未指定

如果應用程式想要的色彩空間用來繪製影像的控制項，它可以使用新`ContentsFormat`屬性`CALayer`來指定所要的色彩空間。 這個值可以是`CAContentsFormat`下列的列舉：

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>在 macOS 中將螢幕上呈現

當應用程式需要在螢幕上的 macOS 中各種色彩呈現影像時，覆寫`DrawRect`方法的`NSView`如往常般的有問題。 例如: 

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

同樣地，它會聰明地判斷應該轉譯在延伸範圍 sRGB 色彩空間 Mac 硬體上時執行的應用程式的功能為基礎的 sRGB`DrawRect`呼叫方法。

如果應用程式想要的色彩空間用來繪製影像的控制項，它可以使用新`DepthLimit`屬性`NSWindow`類別，以指定所要的色彩空間。 這個值可以是`NSWindowDepth`下列的列舉：

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb

---
title: 在 Xamarin.iOS 中的寬色域
description: 本文件討論各種色彩，以及如何使用它的 Xamarin.iOS 或 Xamarin.Mac 應用程式中。 它也提供許多重要的色彩相關概念，例如色彩空間、 通道和主要複本的高階概觀。
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: f139bcceda12752e43a3a8330fa0a0e038e539f9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61423523"
---
# <a name="wide-color-in-xamarinios"></a>在 Xamarin.iOS 中的寬色域

_本文章涵蓋寬的色彩，以及如何在 Xamarin.iOS 或 Xamarin.Mac 應用程式中使用。_

iOS 10 和 macOS Sierra 增強的擴充範圍像素格式和整個系統，包括架構，例如核心圖形、 Core 映像、 裸機和 AVFoundation 寬色域圖色彩空間的支援。 藉由提供整個圖形堆疊在這種行為，進一步降低與寬的色彩顯示裝置的支援。

## <a name="about-wide-color"></a>關於寬色域

如上所述，iOS 10 和 macOS Sierra 增強的擴充範圍像素格式和整個系統，包括架構，例如核心圖形、 Core 映像、 裸機和 AVFoundation 寬色域圖色彩空間的支援。 藉由提供整個圖形堆疊在這種行為，進一步降低與寬的色彩顯示裝置的支援。

在 90's Apple 建立的控點色彩在 mac 上處理的 ColorSync 它們也有助於找到的 「 國際色彩協會 (ICC) 」，來建立和升級的一組標準來處理電腦的硬體上的色彩。 Apple 的使用 ICC 納入 ColorSync 和它內建在 OS X （現在稱為 macOS） 的核心。

Apple 也已在領先的硬體，例如 Retina 顯示、 新 P3 顯示和顯示 P3 色彩空間 （發行 iMac 2015 中） 使用顯示技術，而且 TrueTone 會顯示在 iPad、 iPhone 7，再決定是否 iPhone 7 Plus。

具有 macOS Sierra 中各種色彩與 iOS 10，Apple 會變更，同時支援 macOS 和 iOS 的控點色彩來充分利用這些新的顯示技術的方式。

## <a name="core-color-concepts"></a>色彩的核心概念

下列的核心色彩概念需要涵蓋再採取更深入的了解 macOS 和 iOS 中的色彩：

### <a name="color-space"></a>色彩空間

色彩空間是的環境中的色彩能夠表示及比較。 它可以是一到四個維度的空間所定義的色彩元件的濃度。 

[![](wide-color-images/color00.png "色彩空間")](wide-color-images/color00.png#lightbox)

### <a name="color-channels"></a>色彩色板

色彩元件可以也可稱為色彩色板。 有些熟悉的表示法會 RGB 空格、 Gray 空格、 CMYK 空格或裝置獨立的空間。 

[![](wide-color-images/color02.png "色彩元件可以也可稱為色彩色板")](wide-color-images/color02.png#lightbox)

### <a name="color-primaries"></a>原色

色彩的主要複本提供用來比較和計算色彩座標系統。 原色通常落在指定色彩的色頻內可以產生最深的版本。

[![](wide-color-images/color01.png "色彩的主要複本提供用來比較和計算色彩座標系統")](wide-color-images/color01.png#lightbox)

RGB 色彩空間上方表示，如果 Color 主要複本位於何處`1.0`座標錨定 (例如`[1.0, 0.0, 0.0]`紅色)。

### <a name="color-gamut"></a>色域圖

色彩範圍是指所有可以為個別色頻內為色彩空間的組合定義的色彩。

[![](wide-color-images/color03.png "色彩的色域圖範例")](wide-color-images/color03.png#lightbox)

## <a name="what-is-wide-color"></a>寬色域為何

之前涵蓋各種色彩的主題，討論都應該有目前的產業標準色彩，標準的 RGB 色彩空間 (sRGB) 的相關資訊。 它是最廣泛使用的色彩空間立即運算中，而且是適用於 iOS 和 macOS 的預設色彩空間。

SRGB 色彩空間具有下列屬性：

- 它根據 ITU-R BT.709 標準。
- 它有近似的 Gamma 2.2。
- 它代表典型的光源狀況 (D65)。

由於 sRGB 因此廣泛使用在產業中，開發人員可以進行一些假設，為指定的色彩會忠實表示它會顯示在任何裝置上。 不過，這不一定如此。 此外，還有數個不適合在 sRGB 色彩空間，因此，無法裡面代表的色彩。

比方說，許多紡織品的設計與使用許多的墨水和 dyes 落 sRGB 之外的執行緒。 人員遇到其日常生活中的許多產品也會建立亮、 生動範圍 sRGB 色彩空間之外的色彩。 最受矚目的 sRGB 無法顯示的色彩範例的一些事項落日、 autumn 分葉、 外來 flowers 和熱帶 waters 等性質。

具有已擷取未經處理格式的數位映像的使用者可能對他們的裝置，其中包含所有這類色彩資料，即使它無法正確表示 sRGB 色彩空間中，並因此無法正確顯示在畫面上的映像。

### <a name="the-display-p3-color-space"></a>Display P3 色彩空間

在 2015 中，Apple 會發行新產品 （iMac 和 iPad Pro 9.7"），提供新 Display P3 色彩空間來處理由 sRGB 色彩空間的問題。

[![](wide-color-images/color04.png "新的 Display P3 色彩空間")](wide-color-images/color04.png#lightbox)

Display P3 色彩空間具有下列屬性：

- 現代的硬體平台支援各種色彩空間。
- SMPTE DCI-P3 標準為基礎。 DCI P3 專為數位投影機，但已由 Apple，以支援監視修改。
- 它有近似的 Gamma 2.2。
- 它代表典型的光源狀況 (D65)。

根據 Apple，使用者會將其工作流程移到其行動裝置平台。 解決 sRGB 不只包括各種色彩顯示所需的專業行動裝置 (iPad 專業人員) 中所呈現的色彩呈現問題。 其中一個解決方案是升級 factory 校正，使每個個別的裝置已校正過在 factory 確保裝置，色彩顯示正確且一致。

另一個解決方案，是包含的 Apple 在 iOS 10 和 macOS Sierra 中都內建的完整的系統範圍的色彩管理。 

### <a name="the-extended-range-srgb-color-space"></a>延伸範圍 sRGB 色彩空間

新的 iOS 10 系統色彩管理，都必須考量的所有現有的 iOS 應用程式建立與微調的 sRGB。 其用意是要確保它不會影響這些現有的應用程式的其中一個色彩表示法或應用程式的效能。

若要處理這種情況下，Apple 已包含在 iOS 10 中的延伸範圍 sRGB 色彩空間 （和以及 macOS Sierra）。

延伸範圍 sRGB 色彩空間具有下列屬性：

- 具有所有相同的 sRGB 主要複本。
- 它有近似的 Gamma 2.2。
- 它代表典型的光源狀況 (D65)。
- 它支援負值，而且值大於一 (1)。

所允許的值小於零且大於一，的延伸範圍 sRGB 色彩空間不僅可讓現有的應用程式中 sRGB 效能折損或扭曲，但它不存在的色彩可讓以代表內可見的任何色彩的色彩空間範圍。 所有這些都是在保持同一個錨點為 sRGB 色彩空間來完成。

### <a name="extended-range-srgb-in-action"></a>作用中的延伸的範圍 sRGB

若要查看延伸範圍 sRGB 色彩空間中的零和一以外的值如何運作，請使用以下的範例顯示 P3 色彩空間中可用的最飽和紅色：

[![](wide-color-images/color05.png "延伸範圍 sRGB 色彩空間中的零和一以外的值如何運作")](wide-color-images/color05.png#lightbox)

在 Display P3，此色彩會表示為`[1.0, 0.0, 0.0]`與延伸範圍 sRGB 很`[1.358, -0.074, -0.012]`。 因為 sRGB 值是完全自主內 Display P3 和 Display P3 值版面配置設定 「 外部 」 的 sRGB 範圍。

允許從極端正前往極端的負數值的像素值的實體硬體，它可以顯示任何可見的範圍中可用的色彩，以及這些值可以用來表示延伸範圍 sRGB 色彩空間。

### <a name="device-pixel-formats"></a>裝置像素格式 

SRGB 色彩空間多半已標準化 8 位元每一色大部分是因為使用 8 位元的像素格式，足以描述 sRGB 色彩。 這不是完美的但夠好，並提供良好來顯示影像的記憶體及處理器用量之間進行取捨。

Display P3 可代表色彩的 sRGB 色彩空間之外的座標，因為它需要每個以正確具延伸範圍 sRGB 色彩空間的色彩的色頻的 16 位元。

## <a name="system-wide-wide-color-support"></a>支援的全系統的各種色彩

寬色域寬色域圖，在 iOS 10 和 macOS Sierra 的完整支援，Apple 已擴充來充分利用的延伸範圍 sRGB 色彩空間和 Display P3 下列架構：

- UIKit （適用於僅限 iOS)
- SceneKit
- Core Graphics
- ImageIO
- Core 映像
- WebKit
- SpriteKit
- Core Animation
- AppKit （適用於 macOS 只)

此外，顯示 Retina 顯示器延伸範圍 sRGB 色彩空間已增強的支援以及 Display P3。

寬色域受支援，而且可以用於下列應用程式內容類型：

- 包含在應用程式套件組合中的靜態影像資源。
- 文件和網路基礎映像資源。
- 進階的媒體，例如 Live 相片或以原始格式的映像。
- 3D 圖形著色器材質影像。

## <a name="solving-the-color-problem"></a>解決色彩的問題

應用程式中顯示的內容可能來自各種不同的色彩豐富來源。 此外，此內容可以顯示在廣泛的裝置，各有自己的色彩顯示功能的範圍。

IOS 10 應用程式橋接這兩個問題，使用新內建的系統色彩管理系統之間的差異。 此系統可確保，影像看起來相同上所有的 iOS 裝置，不論哪個色彩空間中編碼映像。

色彩管理是由每個有相關聯的色彩空間 （或色彩設定檔） 的映像開始。 這項資訊會在_色彩比對程序_會在輸出裝置的色彩比對中來源影像的色彩。 因為映像中的每個像素必須色彩比對，它可以會耗費大量時間，並放置在裝置的 CPU 負擔。

由於_色彩比對程序_，這個轉換可能造成潛在"失真"如果輸出裝置具有較小的色域圖比來源映像。

幸運的是，計算進入_色彩比對程序_可以輕鬆地進行硬體加速 （可能是 CPU 或 GPU） 和 Apple 可確保它會自動運作的基底的系統，例如 Quartz 2D 中建置支援ColorSync 和核心動畫。 如需正確標記的內容，不必編寫程式碼，才能利用這些功能。

色彩管理項目支援每個平台，如下所示：

- **macOS** -macOS 已自開始受管理的色彩。
- **iOS** -iOS 自便開始支援自動色彩管理 iOS 9.3 （和更新版本）。

### <a name="designing-for-wide-gamut"></a>寬色域圖的設計

Apple 會有下列的建議設計，並使用各種色彩、 iOS 和 macOS 應用程式中的廣泛映像內容：

- 只在進行中，使用寬色域圖內容感知應用程式，它們應該不會自動可在各處使用。
- 只能使用廣泛的內容位置逼真的色彩會增強使用者體驗。
- 在不需要針對現有的應用程式，變更為 P3 的所有內容。

Apple 的工具鏈可讓應用程式中支援各種色彩不是孤注一擲的情況下漸進式選用功能，支援廣泛的映像內容。

### <a name="upgrading-existing-content-to-wide-color"></a>寬色域來升級現有的內容

Apple 會有各種色彩升級現有的映像內容的下列建議：

- 不只是 「 指派 」 的 P3 設定檔影像編輯應用程式中的內容。 這樣將只會重新對應非預期的結果，例如自動縮放以符合新的空間，因此改變影像的色彩內容到新的色彩空間的現有色彩。
- 「 轉換 」 至 Display P3 設定檔使用影像編輯應用程式需要的映像內容。

### <a name="file-formats-and-color-profiles"></a>檔案格式和色彩設定檔

Apple 會有的檔案格式和應用程式的各種色彩映像內容中使用的色彩設定檔的下列建議：

- 用於 RGB 工作空間中的 「 Display P3 」 色彩設定檔。
- 使用 16 位元每個色彩通道模式。
- 使用 Late 2015 iMac （或更新版本），精確地預覽映像內容。
- 匯出為 16 位元 PNG 檔案，與內嵌的 「 Display P3"ICC 設定檔的影像資產。

> [!IMPORTANT]
> 使用**儲存以供 Web**或**匯出資產**最受歡迎的影像編輯軟體中找到的功能_則不會_寬色域映像，因為這些功能未運作更新為尚未支援必要的檔案格式規格。

### <a name="supporting-wide-color-with-asset-catalogs"></a>支援使用資產目錄寬色域

在 iOS 10 和 macOS Sierra，Apple 已擴充資產目錄用來包含和分類的應用程式套件組合中的靜態影像內容支援廣泛的色彩。

使用資產目錄可提供下列優點的應用程式：

- 它們提供靜態影像資產的最佳部署選項。
- 它們支援自動色彩修正。
- 它們支援自動的像素格式最佳化。
- 它們支援應用程式配量和應用程式精簡，以確保只有已相關取得內容傳遞給使用者的裝置。

Apple 對下列增強功能資產目錄寬色域支援：

- 它們支援 16 位元 （每個色頻） 來源內容。
- 它們支援顯示所有編製目錄的內容。 可以標記 sRGB 或 Display P3 色階的內容。

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

## <a name="colors-in-user-interfaces"></a>在使用者介面中的色彩

當使用使用者介面中的色彩，螢幕上的像素大部分都是純色。 此外，大部分的這些像素為單位不會來自映像的資產，但會繪製直接由應用程式 （或代表應用程式的作業系統）。

寬色域圖色彩 UI 層級使用時，可以呈現幾項挑戰：

- **通訊色彩**-在談論設計人員與開發人員通常沒有色彩_假設_sRGB 色彩空間有關。 讓色彩可能會做為傳達`rgb(128, 45, 56)`或`#FF0456`。 在廣泛的設計中，這些表示法不提供足夠的資訊來正確呈現指定的色彩，色彩空間的工作也必須包含。 Apple 建議使用`P3(128, 45, 56)`和`P3#FF0456`改。 
- **挑選色彩**-大多數受歡迎的映像編輯和設計會降低軟體從 sRGB 色彩空間相同的限制，但使用他們的內建的色彩選擇器。 設計工具應該確定它們是處於 「 Display P3"色彩空間，色彩選擇器中，使用寬色域設計時。
- **撰寫程式碼色彩**-這兩個`NSColor`(macOS) 和`UIColor`（iOS 和 tvOS） 有新的便利方法，來直接產生 P3 色彩，而且兩者都已擴充為支援的色彩中延伸範圍 sRGB 色彩空間以及。
- **儲存色彩**-應用程式的文件中的儲存寬色域圖色彩時，就應該特別注意。 其中每一色運作正常的 sRGB 色彩空間的 8 位元，16 位元每一色應該用於寬的色彩。 開發人員也必須以監看擔任色彩空間 （因為所有項目是傳統上只有 sRGB） 的執行個體。

## <a name="colors-on-the-web"></a>在網站上的色彩

寬色域 web pages，並支援各種色彩顯示裝置上使用時請務必小心。 如果已包含在網站的映像內容的所有已適當地標記，iOS 和 macOS 會自動色彩比對並正確地顯示它們。

媒體查詢也有可協助您解決 P3 和 sRGB 功能的裝置之間的資產：

```xml
<picture>
    <source srcset="monkey-p3.jpg" media="(color-gamut: p3)">
    <source srcset="monkey-rpg.jpg">
</picture>
```

Apple 也有可讓在擔任的 sRGB 空間以外的其他色彩空間中指定的 CSS WebKit 提案。

## <a name="rendering-off-screen-images-in-app"></a>轉譯應用程式中的幕外映像

根據所建立的應用程式的類型，它可能會允許使用者包括它們已經從網際網路收集或建立映像內容，直接在應用程式 （例如向量，例如繪圖應用程式） 的映像內容。

在這兩種情況下，應用程式可能會呈現所需的影像使用增強的功能新增至 iOS 和 macOS 的色彩寬螢幕外。

### <a name="drawing-wide-color-in-ios"></a>在 iOS 中繪製寬色域

之前討論過如何正確繪製在 iOS 10 中的 寬色域圖，看看下列常見 iOS 繪圖程式碼：

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

## <a name="summary"></a>總結

這篇文章已涵蓋寬的色彩和它的可能實作和使用的 Xamarin.iOS 或 Xamarin.Mac 應用程式內的方式。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)

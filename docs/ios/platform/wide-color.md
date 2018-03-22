---
title: "寬的色彩"
description: "本文涵蓋寬的色彩，以及如何使用它的 Xamarin.iOS 或 Xamarin.Mac 應用程式中。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 34f2e249c5474997b2e2e179285f455c425739cf
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="wide-color"></a>寬的色彩

_本文涵蓋寬的色彩，以及如何使用它的 Xamarin.iOS 或 Xamarin.Mac 應用程式中。_

iOS 10 及 macOS 利也可增強的擴充範圍像素格式和整個系統中包括架構，例如核心圖形、 Core 映像、 裸機和 AVFoundation 寬一系列色彩空間的支援。 藉由提供整個圖形堆疊此行為，是進一步降低與寬的色彩顯示裝置的支援。

## <a name="about-wide-color"></a>關於寬的色彩

如同前面所述，iOS 10 及 macOS 利也可增強的擴充範圍像素格式和整個系統，包括架構，例如核心圖形、 Core 映像、 裸機和 AVFoundation 寬一系列色彩空間的支援。 藉由提供整個圖形堆疊此行為，是進一步降低與寬的色彩顯示裝置的支援。

在 90's Apple 建立 ColorSync 來處理處理 mac 上的色彩 它們也有助於找到的 「 國際色彩協會 (ICC) 」，若要建立並升級的一組標準來處理在電腦上的色彩。 Apple 的使用 ICC 已納入 ColorSync，且它已內建 OS X （現在稱為 macOS） 的核心。

Apple 的硬體，例如依序顯示，新 P3 顯示和顯示 P3 色彩空間 （發行 iMac 2015 中） 與顯示技術也有和 TrueTone 顯示 iPad、 iPhone 7 後 iPhone 7 中加上。

與寬色彩 macOS 利也在與 iOS 10，Apple 正在變更 macOS 和 iOS 處理要充分利用這些新的顯示技術色彩的方式。

## <a name="core-color-concepts"></a>色彩的核心概念

下列的核心色彩概念需要在執行探究 macOS 和 iOS 中色彩之前涵蓋：

### <a name="color-space"></a>色彩空間

色彩空間是在其中色彩可以表示和比較的環境。 它可以是其色彩元件的濃度所定義的一到四個維度空間。 

[![](wide-color-images/color00.png "色彩空間")](wide-color-images/color00.png#lightbox)

### <a name="color-channels"></a>色彩色板

色彩元件可以也稱為色頻。 某些常見的表示法將 RGB 空格、 灰色空格、 CMYK 空格或裝置獨立空格。 

[![](wide-color-images/color02.png "色彩元件可以也稱為色彩頻道")](wide-color-images/color02.png#lightbox)

### <a name="color-primaries"></a>色彩混雜

色彩主要複本會提供用來比較和運算色彩座標系統。 色彩主要複本會通常落在最密集指定色彩可能會產生色彩通道內的版本。

[![](wide-color-images/color01.png "色彩主要複本會提供用來比較和運算色彩座標系統")](wide-color-images/color01.png#lightbox)

在上述表示 RGB 色彩空間，色彩的主要複本會位於何處`1.0`座標錨定 (例如`[1.0, 0.0, 0.0]`紅色)。

### <a name="color-gamut"></a>色域圖

色彩範圍是指所有可以定義為提供色彩空間內的個別色彩通道組合的色彩。

[![](wide-color-images/color03.png "色彩色域圖範例")](wide-color-images/color03.png#lightbox)

## <a name="what-is-wide-color"></a>什麼是寬的色彩

之前涵蓋寬的色彩的主題，討論應該有相關的目前的業界標準的色彩，標準的 RGB 色彩空間 (sRGB) 中。 這是最普遍使用的色彩空間今天計算且適用於 iOS 和 macOS 的預設色彩空間。

SRGB 色彩空間具有下列屬性：

- 它根據 ITU-R BT.709 標準。
- 它有 2.2 近似色差補正。
- 它代表一般光線條件 (D65)。

SRGB 因此廣泛使用在產業中，開發人員可以做出，因為指定的色彩會準確地表示它會顯示在任何裝置上。 不過，這可能不一定是大小寫。 此外，還有數個色彩不適合在 sRGB 色彩空間，因此，不能以表示它。

例如，許多紡織品的設計與使用許多墨水和 dyes 下降 sRGB 以外的執行緒。 也亮、 逼真的色彩範圍之外 sRGB 色彩空間會建立許多使用者遇到其每日的生命週期中的產品。 部分最令人信服的 sRGB 無法表示的色彩範例事項落日、 秋日分葉，縱使花和 tropical 水域等性質。

具有已擷取未經處理格式的數位映像的使用者在其包含所有此色彩的資料，即使它無法適當地表示 sRGB 色彩空間中，因此無法正確顯示在畫面上的裝置上可能映像。

### <a name="the-display-p3-color-space"></a>顯示 P3 色彩空間

2015，Apple 發行提供新顯示 P3 色彩空間來處理問題 sRGB 色彩空間所建立的新產品 （iMac 和 iPad Pro 9.7"）。

[![](wide-color-images/color04.png "新的顯示 P3 色彩空間")](wide-color-images/color04.png#lightbox)

顯示 P3 色彩空間具有下列屬性：

- 現代硬體平台支援廣泛的色彩空間。
- SMPTE DCI P3 標準為基礎。 DCI P3 是數位投影機，但已修改以支援監視 Apple。
- 它有 2.2 近似色差補正。
- 它代表一般光線條件 (D65)。

根據 Apple，使用者會將工作流程移到其行動裝置平台。 解決 sRGB 不只包括寬的色彩顯示所需的專業行動裝置 (iPad 專業人員) 中所呈現的色彩呈現問題。 其中一個解決方案是升級 factory 校正，使每個裝置已校正過在工廠確保裝置，色彩顯示正確且一致。

另一個解決方案是完整的全系統色彩管理 Apple 已內建 iOS 10，macOS 利也包含。 

### <a name="the-extended-range-srgb-color-space"></a>擴充範圍 sRGB 色彩空間

新的 iOS 10 全系統色彩管理必須針對所有現有的 iOS 應用程式針對建立與微調 sRGB 的帳戶。 它被為了確保它不會影響這些現有的應用程式的其中一個色彩表示法或應用程式效能。

若要處理這種情況，並包含 Apple 中擴充範圍 sRGB 色彩空間中 iOS 10 （及 macOS 利也一併）。

擴充範圍 sRGB 色彩空間具有下列屬性：

- 具有所有相同的主要複本會 sRGB。
- 它有 2.2 近似色差補正。
- 它代表一般光線條件 (D65)。
- 它支援負值，而且值大於一 (1)。

允許的值小於零或大於一個，現有的應用程式中 sRGB 耗用或失真，但它不存在的色彩不只允許色彩空間的擴充範圍 sRGB 允許來代表內可見的任何色彩的色彩空間範圍。 這些全部都是同時保留相同錨點做為 sRGB 色彩空間來完成。

### <a name="extended-range-srgb-in-action"></a>在動作中的擴充的範圍 sRGB

零，另一個之外的值擴充範圍 sRGB 色彩空間中的運作方式，請採取下列範例會顯示 P3 色彩空間中可用的最飽和紅色：

[![](wide-color-images/color05.png "零，另一個之外的值擴充範圍 sRGB 色彩空間中的運作方式")](wide-color-images/color05.png#lightbox)

在顯示 P3 這個色彩則表示為`[1.0, 0.0, 0.0]`在將擴充範圍 sRGB `[1.358, -0.074, -0.012]`。 因為 sRGB 值是完整自主內顯示 P3 和顯示 P3 值版面配置設定 「 外部 」 的 sRGB 範圍。

允許從極端正前往極端的負數值的像素值的實體硬體，它可以顯示可見範圍的任何色彩，這些值可以表示擴充範圍 sRGB 色彩空間中。

### <a name="device-pixel-formats"></a>裝置像素格式 

色彩空間已大致 sRGB 標準化使用 8 位元的像素格式，因為每個色彩通道的 8 位元大致足以描述 sRGB 中的色彩。 這不完整，但夠好，而且它提供良好的權衡取捨，來顯示影像的記憶體及處理器用量之間。

顯示 P3 可表示色彩座標 sRGB 色彩空間之外，因為它需要每個以正確擴充範圍 sRGB 色彩空間的色彩的色彩頻道的 16 位元。

## <a name="system-wide-wide-color-support"></a>支援的全系統寬色彩

若要完整支援廣泛的色彩寬的範圍內 iOS 10 和 macOS 利也，Apple 已經擴充，以利用完整的擴充範圍 sRGB 色彩空間與顯示 P3 下列架構：

- UIKit （適用於僅限 iOS)
- SceneKit
- 核心圖形
- ImageIO
- Core 映像
- WebKit
- SpriteKit
- 核心動畫
- AppKit （適用於僅 macOS)

此外，支援已擴充範圍 sRGB 色彩空間增強 Retina 顯示器和顯示 P3 會顯示。

寬色彩受支援，且可用於下列的應用程式內容類型：

- 包含在應用程式套件組合的靜態影像資源。
- 文件和網路架構影像資源。
- 進階的媒體，例如 Live 相片或原始格式的影像。
- 3D 圖形著色器紋理影像。

## <a name="solving-the-color-problem"></a>解決色彩問題

應用程式中顯示的內容可能來自各種不同的色彩豐富的來源。 此外，這個內容可以顯示在廣泛的裝置，各有自己的色彩顯示功能的範圍。

10 的 iOS 應用程式連接這兩個問題，使用新內建的全系統色彩管理系統之間的差異。 此系統可確保，影像看起來都一樣上所有的 iOS 裝置，不論哪一個色彩空間的映像已編碼中。

色彩管理開頭有相關聯的色彩空間 （或色彩設定檔） 的每個映像。 這項資訊用於_色彩比對程序_位置中之來源影像的色彩會對應到輸出裝置中的色彩。 因為映像中的每個像素必須色彩比對，它可以會耗費大量時間，並放在裝置的 CPU 負擔。

由於的_色彩比對程序_，這項轉換可以是潛在"失真"如果輸出裝置具有較小的一系列比來源映像。

幸運的是，計算移入_色彩比對程序_可以輕鬆地被硬體加速 （是使用 CPU 或 GPU 上） 和 Apple 可確保它是否運作自動的建置基底的系統，例如 Quartz 2D 支援ColorSync 和核心動畫。 如需正確加上標記的內容，任何程式碼，才能充分利用這些功能。

色彩管理已被支援的每個平台，如下所示：

- **macOS** -macOS 已經過色彩自開始管理。
- **iOS** -自 iOS 9.3 （含） 以後版本支援自動色彩管理 iOS。

### <a name="designing-for-wide-gamut"></a>針對寬的一系列設計

Apple 有下列的建議設計，而且使用寬色彩、 iOS 及 macOS 應用程式中的一系列寬映像內容：

- 只能使用一系列寬內容中進行其中它們不應該自動使用所有應用程式中有意義。
- 只能使用一系列寬內容逼真的色彩加強使用者體驗。
- 在不需要將變更現有的應用程式的所有內容 P3。

Apple 的工具鏈會寬色域圖映像內容支援漸進式選擇加入的因此應用程式中支援廣泛的色彩不是想到的狀況。

### <a name="upgrading-existing-content-to-wide-color"></a>升級現有的內容為寬的色彩

Apple 有寬的色彩要升級現有的映像內容的下列建議：

- 不只 「 指派 」 P3 設定檔中編輯應用程式的映像的內容。 這樣只會重新對應現有內容加入至新的色彩空間與非預期的結果，例如自動縮放要放入新的空間，因此改變影像的色彩的色彩。
- 「 轉換 」 為使用影像編輯應用程式的顯示 P3 設定檔需要的映像內容。

### <a name="file-formats-and-color-profiles"></a>檔案格式和色彩設定檔

Apple 有檔案格式和應用程式的色彩寬映像內容中使用的色彩設定檔的下列建議：

- 用於 RGB 工作空間中的 「 顯示 P3"色彩設定檔。
- 使用 16 位元每個色彩通道模式。
- 使用最遲 2015 iMac （或更新版本），精確地預覽映像內容。
- 將影像資產匯出為 16 位元與內嵌的 「 顯示 P3"ICC 設定檔的 PNG 檔案。

> [!IMPORTANT]
> 使用**儲存 web**或**匯出資產**功能位於最受歡迎的影像編輯軟體_則不會_寬的彩色影像，因為這些功能未運作更新為尚未支援必要的檔案格式規格。

### <a name="supporting-wide-color-with-asset-catalogs"></a>支援與資產目錄寬色彩

在 iOS 10 和 macOS 利也，Apple 擴充資產的目錄，用來包含及分類中的應用程式套件組合的靜態影像內容以支援廣泛的色彩。

使用資產目錄有下列優點的應用程式：

- 它們提供的靜態影像資產的最佳部署選項。
- 它們支援自動色彩修正。
- 它們支援自動像素格式最佳化。
- 它們支援應用程式配量和應用程式精簡可確保只有已相關取得內容傳遞給終端使用者的裝置。

Apple 已對下列增強功能資產目錄寬的色彩支援：

- 它們支援 16 位元 （每個色彩通道） 來源內容。
- 它們支援編目的內容，顯示一系列。 內容可以標記為 sRGB 或 P3 顯示色階。

開發人員可以在自己的應用程式中支援寬色彩內容的三個選項：

1. **不執行任何動作**-寬色彩內容應該只用於中的應用程式必須顯示逼真的色彩 （其中它們會強化使用者體驗） 的情況下，由於這項需求之外的內容應該保留為-是。 它會繼續在所有硬體的情況下正確呈現。
2. **升級現有的內容以顯示 P3** -這需要以新的升級 P3 格式檔案以取代其資產類別中的現有映像內容和標記它在這種情況在資產編輯器開發人員。 在建置時，會產生這些資產 sRGB 衍生映像。
3. **提供最佳化資產內容**-在此情況下，開發人員會提供 8 位元 sRGB 和每個影像資產目錄 （適當地標記資產編輯器） 中的 16 位元顯示 P3 願景。

### <a name="asset-catalog-deployment"></a>資產目錄部署

會發生下列情形時，開發人員提交包含寬色彩映像內容的資產目錄的應用程式市集的應用程式：

- 當應用程式部署至使用者時，應用程式的配量可確保只有適當的內容變數傳遞給使用者的裝置。
- 在裝置上支援寬度的色彩，沒有無裝載成本包括寬色彩內容，因為永遠不會出貨至裝置。
- `NSImage` 在 macOS 利也 （和更新版本） 會自動選取最佳的內容表示硬體的顯示。
- 顯示的內容時，或裝置硬體顯示特性變更將會自動更新。

### <a name="asset-catalog-storage"></a>資產目錄儲存體

資產目錄儲存體有下列的內容和應用程式的影響：

- 在建置時，Apple 會嘗試最佳化透過高品質影像轉換的映像內容的儲存。
- 16 位元用於每個色彩頻道寬色彩內容。
- 內容相依的像像壓縮用來降低交付項目內容的大小。 已加入新的 「 失真 「 壓縮以進一步最佳化內容的大小。

## <a name="colors-in-user-interfaces"></a>在使用者介面中的色彩

當使用使用者介面中的色彩，大部分的螢幕上的像素會處於純色。 此外，大部分的這些像素為單位不是來自影像資產，但會繪製直接由應用程式 （或代表應用程式的作業系統）。

寬的一系列色彩 UI 層級使用時，可呈現幾項挑戰：

- **通訊色彩**-當論及之間設計人員和開發人員通常沒有色彩_假設_sRGB 涉及色彩空間。 讓色彩可能會傳遞做為`rgb(128, 45, 56)`或`#FF0456`。 在寬的一系列設計中，這些表示沒有提供足夠的資訊來精確地表示指定的色彩，必須也包含使用色彩空間。 Apple 建議使用`P3(128, 45, 56)`和`P3#FF0456`改為。 
- **挑選色彩**-最受歡迎的映像編輯和設計軟體所使用的內建的色彩選擇器時苦 sRGB 色彩空間相同的限制。 在設計工具應該確保其為 「 顯示 P3"色彩空間中色彩選擇器中，使用寬色彩設計時。
- **撰寫程式碼色彩**-這兩個`NSColor`(macOS) 和`UIColor`(iOS & tvOS) 有新的便利方法，來直接產生 P3 色彩，而且兩者都已擴充為支援的色彩中擴充範圍 sRGB 色彩空間以及。
- **儲存色彩**-應該小心時儲存寬的一系列應用程式的文件中的色彩。 其中每個色彩頻道毫無 sRGB 色彩空間的 8 位元，16 位元，每個色彩通道應該用於寬的色彩。 開發人員也必須要監看的假設色彩空間 （因為一切傳統上只有 sRGB） 執行個體。

## <a name="colors-on-the-web"></a>在網站上的色彩

寬的色彩在網頁中支援廣泛的色彩顯示的裝置上使用時請務必小心。 如果已包含在網站的映像內容的所有已適當地標記，iOS 和 macOS 會自動色彩比對並正確地顯示它們。

媒體查詢也可用來協助解決 P3 和 sRGB 裝置之間的資產：

```xml
<picture>
    <source srcset="monkey-p3.jpg" media="(color-gamut: p3)">
    <source srcset="monkey-rpg.jpg">
</picture>
```

Apple 也有可讓在假設的 sRGB 空間以外的其他色彩空間中指定的 CSS WebKit 提案。

## <a name="rendering-off-screen-images-in-app"></a>呈現應用程式中的螢幕影像

根據正在建立的應用程式類型，它可能允許使用者為包含映像內容，他們已經從網際網路收集或建立 （例如向量，例如繪圖應用程式） 的應用程式內直接的映像內容。

在這兩種情況下，應用程式可以轉譯必要的圖像螢幕中使用增強的功能加入 iOS 及 macOS 寬的色彩。

### <a name="drawing-wide-color-in-ios"></a>在 iOS 中繪製寬的色彩

之前討論如何正確地在 iOS 10 中繪製寬的彩色影像時，看看下列常見 iOS 繪圖程式碼：

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

有問題需要解決的標準程式碼_之前_它可以用來繪製寬的彩色影像。 `UIGraphics.BeginImageContext (size)`方法用來啟動 iOS 影像繪製具有下列限制：

- 它無法建立超過每個色彩通道的 8 位元映像內容。
- 它不能代表擴充範圍 sRGB 色彩空間中的色彩。
- 它並沒有提供介面，以建立非 sRGB 色彩空間中的內容，因為低層級的 C 常式在背景中被呼叫的能力。

若要處理這些限制，並在 iOS 10 中繪製寬的彩色影像，改用下列程式碼：

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

新`UIGraphicsImageRenderer`類別會建立能夠處理擴充範圍 sRGB 色彩空間的新映像內容，並具有下列功能：

- 它完全是由預設的色彩。
- 擴充範圍 sRGB 色彩空間，以根據預設，它支援。
- 它以聰明的方式決定應該轉譯 sRGB 或擴充範圍 sRGB iOS 裝置上執行應用程式的功能為基礎的色彩空間中。
- 它完全並自動管理的映像內容 (`CGContext`) 存留期，因此開發人員不需要擔心呼叫開始與結束內容命令。
- 與之相容`UIGraphics.GetCurrentContext()`方法。

`CreateImage`方法`UIGraphicsImageRenderer`呼叫建立寬色彩映像並傳遞要繪製成的映像內容的完成處理常式類別。 所有的繪圖方式是在這個完成處理常式內，如下所示：

- `UIColor.FromDisplayP3`方法建立新的完全飽和紅色色彩寬色域圖顯示 P3 色彩空間中，它用來繪製矩形的前半。 
- 第二個矩形的下半部會繪製為一般 sRGB 完全飽和紅色進行比較。

### <a name="drawing-wide-color-in-macos"></a>在 macOS 中繪製寬的色彩

`NSImage`類別已展開於 macOS 利也支援廣泛的彩色影像繪圖。 例如: 

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

## <a name="rendering-on-screen-images-in-app"></a>呈現螢幕中的映像應用程式

螢幕上呈現寬的彩色影像，處理程序運作方式類似於繪製 macOS 與 iOS 以上所顯示的螢幕寬度色彩映像。

### <a name="rendering-on-screen-in-ios"></a>在 iOS 中將螢幕上呈現

當應用程式需要寬螢幕在 iOS 中的色彩中呈現映像時，覆寫`Draw`方法`UIView`如往常般有問題。 例如: 

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

使用 iOS 10 一樣`UIGraphicsImageRenderer`如上所示，以聰明的方式決定應該轉譯 sRGB 或擴充範圍 sRGB iOS 裝置上時所執行的應用程式的功能為基礎的色彩空間中的類別`Draw`方法呼叫。 此外，`UIImageView`已經過受管理 iOS 9.3 以及之後的色彩。

如果應用程式必須知道如何執行呈現上`UIView`或`UIViewController`，可以檢查新`DisplayGamut`屬性`UITraitCollection`類別。 這個值會`UIDisplayGamut`下列的列舉：

- P3
- Srgb
- 未指定

如果應用程式想要控制色彩空間是用來繪製影像時，它可以使用新`ContentsFormat`屬性`CALayer`來指定所要的色彩空間。 這個值可以是`CAContentsFormat`下列的列舉：

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>MacOS 的螢幕上呈現

當應用程式需要寬螢幕 macOS 的色彩中呈現映像時，覆寫`DrawRect`方法`NSView`如往常般有問題。 例如: 

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

同樣地，它以聰明的方式決定應該轉譯 sRGB 或擴充範圍 sRGB Mac 硬體上時所執行的應用程式的功能為基礎的色彩空間中`DrawRect`方法呼叫。

如果應用程式想要控制色彩空間是用來繪製影像時，它可以使用新`DepthLimit`屬性`NSWindow`類別，以指定所要的色彩空間。 這個值可以是`NSWindowDepth`下列的列舉：

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb

## <a name="summary"></a>總結

這篇文章已涵蓋寬的色彩和它的可能實作及 Xamarin.iOS 或 Xamarin.Mac 的應用程式內使用的方法。



## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)

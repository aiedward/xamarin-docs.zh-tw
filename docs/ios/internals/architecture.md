---
title: iOS 應用程式架構
description: 本檔以較低的層級描述 Xamarin，討論原生和 managed 程式碼的互動方式、AOT 編譯、選擇器、註冊機構、應用程式啟動和產生器。
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 7d59a295961c25ecfcc99bb54fdc188c957cf3ee
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291948"
---
# <a name="ios-app-architecture"></a>iOS 應用程式架構

Xamarin iOS 應用程式會在 Mono 執行環境中執行，並使用完整的時間（AOT）編譯，將C#程式碼編譯為 ARM 元件語言。 這會與[目標-C 運行](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)時間並存執行。 這兩個執行時間環境會在類似 UNIX 的核心上執行，特別是[XNU](https://en.wikipedia.org/wiki/XNU)，並將各種 api 公開給使用者程式碼，讓開發人員能夠存取基礎的原生或受控系統。

下圖顯示此架構的基本總覽：

[![](architecture-images/ios-arch-small.png "此圖顯示時間先行（AOT）編譯架構的基本總覽")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>原生和受控碼：說明

針對 Xamarin 進行開發時，通常會使用*原生和 managed*程式碼的詞彙。 [Managed 程式碼](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/)是[.NET Framework Common Language runtime](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx)所管理的程式碼，或在 Xamarin 的案例中： Mono 執行時間。 這就是我們所謂的中繼語言。

機器碼是以原生方式在特定平臺上執行的程式碼（例如，在 ARM 晶片上的目標 C 或甚至是 AOT 編譯器代碼）。 本指南將探討 AOT 如何將您的 managed 程式碼編譯為機器碼，並說明如何使用系結來充分利用 Apple 的 iOS Api，同時也可存取。NET 的 BCL 和複雜的語言，例如C#。

## <a name="aot"></a>AOT

當您編譯任何 Xamarin 平臺應用程式時， C# Mono （ F#或）編譯器將會執行，並C#將F#您的和程式碼編譯成 Microsoft 中繼語言（MSIL）。 如果您在模擬器上執行 Xamarin、Xamarin 應用程式，或甚至是 Xamarin iOS 應用程式， [.Net Common Language Runtime （CLR）](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx)會使用即時（JIT）編譯器來編譯 MSIL。 在執行時間，這會編譯成機器碼，這可以在應用程式的正確架構上執行。

不過，iOS 上有安全性限制，由 Apple 設定，不允許在裝置上執行動態產生的程式碼。
為了確保我們遵守這些安全通訊協定，Xamarin 會改為使用前方（AOT）編譯器來編譯 managed 程式碼。 這會產生原生 iOS 二進位檔，並選擇性地使用適用于裝置的 LLVM 進行優化，可部署在 Apple 的 ARM 型處理器上。 下面將說明如何搭配使用這種方式的粗略圖表：

[![](architecture-images/aot.png "如何搭配使用的粗略圖表")](architecture-images/aot-large.png#lightbox)

使用 AOT 有一些限制，在[限制](~/ios/internals/limitations.md)指南中有詳細說明。 它也透過縮短啟動時間和各種效能優化，提供許多 JIT 的改良功能

既然我們已經探討了程式碼如何從原始程式碼編譯成機器碼，讓我們來看看如何讓我們撰寫完全原生的 iOS 應用程式

## <a name="selectors"></a>選取器

有了 Xamarin，我們有兩個不同的生態系統（.NET 和 Apple），我們需要將它們結合在一起，以確保最終目標是順暢的使用者體驗。 我們在上一節看到了這兩個執行時間的通訊方式，而您可能已經聽過「系結」一詞，這可讓您在 Xamarin 中使用原生 iOS Api。 我們將在我們的[目標-C](~/cross-platform/macios/binding/overview.md)系結檔中深入說明系結，因此現在讓我們來探索 iOS 在幕後的運作方式。

首先，必須有一種方法可將目標-C 公開給C#，這是透過選取器來完成的。 「選取器」是傳送至物件或類別的訊息。 使用目標-C，這是透過[objc_msgSend](~/cross-platform/macios/binding/overview.md)函數來完成。
如需使用選取器的詳細資訊，請參閱[目標-C 選取器](~/ios/internals/objective-c-selectors.md)指南。 此外，也必須有方法可以將 managed 程式碼公開至目標-C，因為目標-C 並不知道 managed 程式碼的任何內容，因此更複雜。 為了解決這個情況，我們使用*註冊機構*。 下一節會更詳細地說明這些功能。

## <a name="registrars"></a>註冊機構

如先前所述，註冊機構是將 managed 程式碼公開至目標-C 的程式碼。 它會建立一個衍生自 NSObject 的每個 managed 類別清單來執行這項操作：

- 對於未包裝現有目標 c 類別的所有類別，它會建立新的目標 c 類別，其中的目標 c 成員會鏡像具有 [`Export`] 屬性的所有 managed 成員。

- 在每個目標– C 成員的執行中，會自動加入程式碼來呼叫鏡像的 managed 成員。

下列虛擬程式碼顯示如何完成此作業的範例：

**C#（Managed 程式碼）**

```csharp
 class MyViewController : UIViewController{
     [Export ("myFunc")]
     public void MyFunc ()
     {
     }
 }
```

**目標-C：**

```objectivec
@interface MyViewController : UIViewController { }

    -(void)myFunc;
@end

@implementation MyViewController {}

    -(void) myFunc
    {
        /* code to call the managed MyViewController.MyFunc method */
    }
@end

```

Managed 程式碼可以包含屬性， `[Register]`以及`[Export]`註冊機構用來知道物件需要公開至目標-C 的屬性。
當預設產生的名稱不適用時，屬性會用來指定所產生之目標C類別的名稱。`[Register]` 所有衍生自 NSObject 的類別都會自動向目標-C 註冊。
必要`[Export]`的屬性包含字串，這是在產生的目標-C 類別中使用的選取器。

有兩種類型的註冊機構用於 Xamarin. iOS –動態和靜態：


- **動態**登錄庫–動態註冊機構會在執行時間註冊元件中的所有類型。 它會使用由[目標 C 的執行時間 API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)所提供的函式來執行這項工作。 因此，動態註冊機構的啟動速度較慢，但組建時間較快。 這是 iOS 模擬器的預設值。 使用動態註冊機構時，原生函式（通常在 C 中）稱為 trampolines，可做為方法的執行。 它們在不同的架構之間有所不同。

- **靜態註冊機構**–靜態註冊機構會在組建期間產生目標 C 程式碼，然後將其編譯成靜態程式庫並連結至可執行檔。 這可加快啟動速度，但會在組建期間耗費較長的時間。 預設會針對裝置組建使用此方法。 靜態註冊機構也可以搭配 iOS 模擬器使用，方法是在`--registrar:static`專案的`mtouch`組建選項中傳遞做為屬性，如下所示：

    [![](architecture-images/image1.png "設定其他 mtouch 引數")](architecture-images/image1.png#lightbox)

如需有關 Xamarin 所使用之 iOS 類型登錄系統細節的詳細資訊，請參閱[類型註冊機構](~/ios/internals/registrar.md)指南。

## <a name="application-launch"></a>應用程式啟動

所有 Xamarin. iOS 可執行檔的進入點是由名`xamarin_main`為的函式所提供，該函式會初始化 mono。

視專案類型而定，會執行下列動作：

- 針對一般 iOS 和 tvOS 應用程式，會呼叫 Xamarin 應用程式所提供的 managed Main 方法。 這個 managed Main 方法接著會`UIApplication.Main`呼叫，這是目標-C 的進入點。 UIApplication 是目標 C 的`UIApplicationMain`方法的系結。
- 針對延伸模組，會呼叫 Apple `NSExtensionMain`程式庫`NSExtensionmain`所提供的原生函式（或適用于 WatchOS 擴充功能）。 由於這些專案是類別庫，而不是可執行檔專案，因此不會執行任何 managed Main 方法。

所有此啟動順序都會編譯成靜態程式庫，然後連結至您的最終可執行檔，讓您的應用程式知道如何開始使用。

此時，我們的應用程式已啟動，Mono 正在執行，我們在受控碼中，而且我們知道如何呼叫機器碼並回呼。 接下來我們要實際開始加入控制項，讓應用程式成為互動。


## <a name="generator"></a>Generator

Xamarin 會包含每個單一 iOS API 的定義。 您可以流覽[MaciOS github](https://github.com/xamarin/xamarin-macios/tree/master/src)存放庫中的任何一項。 這些定義包含具有屬性的介面，以及任何必要的方法和屬性。 例如，下列程式碼是用來定義 UIKit[命名空間](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327)中的 UIToolbar。 請注意，它是具有許多方法和屬性的介面：

```csharp
[BaseType (typeof (UIView))]
public interface UIToolbar : UIBarPositioning {
    [Export ("initWithFrame:")]
    IntPtr Constructor (CGRect frame);

    [Export ("barStyle")]
    UIBarStyle BarStyle { get; set; }

    [Export ("items", ArgumentSemantic.Copy)][NullAllowed]
    UIBarButtonItem [] Items { get; set; }

    [Export ("translucent", ArgumentSemantic.Assign)]
    bool Translucent { [Bind ("isTranslucent")] get; set; }

    // done manually so we can keep this "in sync" with 'Items' property
    //[Export ("setItems:animated:")][PostGet ("Items")]
    //void SetItems (UIBarButtonItem [] items, bool animated);

    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage ([NullAllowed] UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);

    ...
}
```

在 Xamarin 中呼叫[`btouch`](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs)的產生器會採用這些定義檔，並使用 .net 工具將[它們編譯成暫時的元件](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318)。 不過，此暫存元件無法用來呼叫目標-C 程式碼。 然後，產生器會讀取暫存元件， C#並產生可在執行時間使用的程式碼。
例如，如果您將隨機屬性新增至定義 .cs 檔案，它就不會顯示在輸出程式碼中。 產生器並不知道它，因此`btouch`不知道要在暫存元件中尋找它來輸出它。

建立了 Xamarin 後，mtouch 會將所有元件組合在一起。

在高階中，它會執行下列工作來達到此程度：

- 建立應用程式套件組合結構。
- 複製您的 managed 元件。
- 如果已啟用連結，請執行 managed 連結器，藉由將未使用的部分翻錄出來來優化您的元件。
- AOT 編譯。
- 建立原生可執行檔，它會輸出一系列連結至原生可執行檔的靜態程式庫（每個元件各一個），使原生可執行檔包含啟動器程式碼、註冊機構程式碼（如果是靜態）和所有來自 AOT 的輸出編譯器


如需連結器和其使用方式的詳細資訊，請參閱[連結器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>總結

本指南探討了 Xamarin iOS 應用程式的 AOT 編譯，以及探索到的 Xamarin 和其與目標的深入關係。

## <a name="related-links"></a>相關連結

- [限制](~/ios/internals/limitations.md)
- [繫結 Objective-C](~/cross-platform/macios/binding/overview.md)
- [目標-C 選取器](~/ios/internals/objective-c-selectors.md)
- [輸入註冊機構](~/ios/internals/registrar.md)
- [連結器](~/ios/deploy-test/linker.md)

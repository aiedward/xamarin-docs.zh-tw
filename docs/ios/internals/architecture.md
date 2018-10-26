---
title: iOS 應用程式架構
description: 本文件說明 Xamarin.iOS 在最低層級，討論如何在原生和 managed 程式碼互動，AOT 編譯、 選取器，註冊機構、 應用程式啟動和產生器。
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 106357e9442d51fdd31bb30b4f0342e2b59f67fd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118483"
---
# <a name="ios-app-architecture"></a>iOS 應用程式架構

Mono 執行環境中，執行 Xamarin.iOS 應用程式，並使用完整的預先 (AOT) 編譯來編譯C#ARM 組合語言的程式碼。 這會執行並排顯示以[Objective C 執行階段](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)。 這兩個執行階段環境上執行類似 UNIX 的核心，特別[XNU](https://en.wikipedia.org/wiki/XNU)，並公開至使用者程式碼，讓開發人員能夠存取基礎原生或 managed 系統的各種 Api。

下圖顯示此架構的基本概觀：

[ ![](architecture-images/ios-arch-small.png "下圖顯示預先 (AOT) 編譯架構的基本概觀")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>原生和 Managed 程式碼： 說明

適用於 Xamarin 開發時條款*原生和 managed*經常使用的程式碼。 [Managed 程式碼](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/)是已由其執行的程式碼[.NET Framework Common Language Runtime](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx)，或在 Xamarin 的案例中： Mono 執行階段。 這是我們所謂的中繼語言。

原生程式碼是將特定的平台 （例如，OBJECTIVE-C 或甚至 AOT 編譯程式碼，在 ARM 晶片上的） 的原生方式執行的程式碼。 本指南探索如何 AOT 編譯至機器碼、 managed 程式碼，並說明如何在 Xamarin.iOS 應用程式的運作方式，充分運用透過繫結，使用 Apple 的 iOS Api 同時也擁有存取權。NET 的 BCL 和複雜的語言，例如C#。

## <a name="aot"></a>AOT

當您編譯任何 Xamarin 平台應用程式，Mono C# (或F#) 編譯器會執行，而且將會編譯您C#和F#程式碼到 Microsoft Intermediate Language (MSIL)。 如果您在模擬器中，執行 Xamarin.Android、 Xamarin.Mac 應用程式或甚至是 Xamarin.iOS 應用程式[.NET Common Language Runtime (CLR)](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx)編譯使用 Just in Time (JIT) 編譯器的 MSIL。 在這會編譯成原生程式碼的執行階段，這可以在您的應用程式的正確架構上執行。

不過，還有在 iOS 上，Apple 不允許在裝置上的動態產生程式碼執行所設定的安全性限制。
若要確保，我們會遵守這些安全性通訊協定，Xamarin.iOS 會改用預先 (AOT) 編譯器來編譯 managed 程式碼。 這會產生原生 iOS 二進位，選擇性地針對可以部署在 Apple 的 ARM 處理器的裝置，以最佳化使用 LLVM。 這如何相互配合的概略圖表如下所示：

[![](architecture-images/aot.png "這如何相互配合概略圖表")](architecture-images/aot-large.png#lightbox)

使用 AOT 有幾項限制中詳述[限制](~/ios/internals/limitations.md)指南。 它也提供了許多改進功能透過 JIT 透過減少啟動時間，以及各種效能最佳化

現在我們已探索的程式碼如何從來源編譯原生程式碼，讓我們看探討以查看如何 Xamarin.iOS 可讓我們撰寫完整的原生的 iOS 應用程式

## <a name="selectors"></a>選取器

有了 Xamarin，我們必須要有兩個不同的生態系統，.NET 和 Apple，我們需要將一起變得越好，以確保最終目標是流暢的使用者體驗，簡化。 我們在前兩個執行階段通訊的方式，一節中討論過，您可能會非常適合已經聽過的詞彙 '繫結' 可讓原生的 iOS Api，以在 Xamarin 中使用。 繫結的深入說明我們[OBJECTIVE-C 繫結](~/cross-platform/macios/binding/overview.md)文件，所以現在讓我們來探索 iOS 幕後的運作方式。

首先，必須要有的方式來公開以 OBJECTIVE-C C#，其中會透過選取器。 選取器是一則訊息會傳送至物件或類別。 與 OBJECTIVE-C 這透過完成[objc_msgSend](~/cross-platform/macios/binding/overview.md)函式。
如需有關如何使用選取器的詳細資訊，請參閱[OBJECTIVE-C 選取器](~/ios/internals/objective-c-selectors.md)指南。 也必須是公開以 OBJECTIVE-C、 managed 程式碼因為的 Objective C 不了解 managed 程式碼，這是更複雜的方式。 若要解決這個問題，我們使用*註冊機構*。 這些會在下一節中詳細說明。

## <a name="registrars"></a>註冊機構

如先前所述，註冊機構是程式碼，會公開 managed 程式碼以 OBJECTIVE-C 它會藉由建立衍生自 NSObject 的每個 managed 類別的清單：

- 針對不會包裝現有的 OBJECTIVE-C 類別的所有類別，它會建立新的 Objective C 類別與鏡像的所有受管理的成員的 Objective C 成員 [`Export`] 屬性。

- 在每個目標 – C 成員實作中，程式碼會自動新增至呼叫的鏡像受管理的成員。

下列虛擬程式碼示範如何做到這點：

**C#（managed 程式碼）**

```csharp
 class MyViewController : UIViewController{
     [Export ("myFunc")]
     public void MyFunc ()
     {
     }
 }
```

**本節目標 c:**

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

Managed 程式碼可包含屬性，`[Register]`和`[Export]`，註冊機構會知道物件需要公開以 OBJECTIVE-C
`[Register]`屬性用來指定所產生的 Objective C 類別的名稱，如果預設產生的名稱不適合。 所有的類別衍生自 NSObject 自動向 OBJECTIVE-C
必要`[Export]`屬性包含字串，也就是使用所產生的 Objective C 類別中的選取器。

有兩種類型的註冊機構用於 Xamarin.iOS – 動態和靜態：


- **動態註冊機構**– 動態註冊機構會在執行階段組件中的所有類型的註冊。 其做法是使用所提供的函式[Objective C 的執行階段 API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)。 動態註冊機構，因此有速度較慢啟動，但更快建置時間。 這是 iOS 模擬器的預設值。 原生函式 （通常是在 C 中)，稱為 trampolines，會將數字做方法實作，當使用動態的註冊機構。 兩者的不同架構之間的差異。

- **靜態註冊機構**– 靜態註冊機構會 OBJECTIVE-C 程式碼產生期間建置，然後編譯成靜態程式庫並連結至可執行檔。 這允許較快的啟動，但所花費的時間在建置階段。 這預設會使用針對裝置組建。 靜態註冊機構也可搭配 iOS 模擬器藉由傳遞`--registrar:static`做為`mtouch`屬性在您的專案組建選項中，如下所示：

    [![](architecture-images/image1.png "設定其他 mtouch 引數")](architecture-images/image1.png#lightbox)

更多細節 iOS Xamarin.iOS 所使用的型別註冊系統的詳細資訊，請參閱[型別註冊機構](~/ios/internals/registrar.md)指南。

## <a name="application-launch"></a>啟動應用程式

所有 Xamarin.iOS 的可執行檔的進入點由呼叫的函式提供`xamarin_main`，這會初始化 mono。

根據專案類型，下列作法：

- 如需一般 iOS 和 tvOS 應用程式，稱為受管理的 Main 方法，Xamarin 應用程式所提供。 此管理 Main 方法接著會呼叫`UIApplication.Main`，這是 OBJECTIVE-C 的進入點 UIApplication.Main 是繫結 Objective c 的`UIApplicationMain`方法。
- 擴充功能，原生的函式 – `NSExtensionMain`或 (`NSExtensionmain` WatchOS 延伸模組) – 提供 Apple 所呼叫的程式庫。 由於這些專案是類別庫並不是可執行專案，有任何受管理的 Main 方法來執行。

這個啟動順序的所有會編譯成靜態程式庫，讓您的應用程式知道如何開始會接著連結至您的最終可執行檔。

我們的應用程式已啟動此時 Mono 執行，我們是以 managed 程式碼，我們知道如何呼叫原生程式碼，並回呼。 接下來我們要做為實際開始新增控制項，並讓應用程式更具互動性。


## <a name="generator"></a>Generator

Xamarin.iOS 將包含每個單一的 iOS API 定義。 您可以在瀏覽任一[MaciOS github 存放庫](https://github.com/xamarin/xamarin-macios/tree/master/src)。 這些定義會包含介面的屬性，以及任何必要的方法和屬性。 例如，下列程式碼是用來定義 UIKit UIToolbar[命名空間](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327)。 請注意，它含有一些方法和屬性的介面：

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

產生器，稱為[ `btouch` ](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) Xamarin.iOS，都會採用這些定義檔案，並使用.NET 工具[它們編譯成暫存組件](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318)。 不過，此暫存組件並不會使用呼叫 OBJECTIVE-C 程式碼。 產生器接著會讀取暫存組件，並產生C#可以在執行階段使用的程式碼。
這是為什麼，比方說，您隨機屬性新增至您定義的.cs 檔案時，如果它不會顯示在輸出的程式碼。 產生器並不知道，因此`btouch`還不知道要在其輸出的暫存組件中尋找它。

一旦建立 Xamarin.iOS.dll，mtouch 會連結相同的所有元件在一起。

概括而言，它的做法是執行下列工作：

-   建立應用程式套件組合結構。
-   複製您的 managed 組件中。
-   如果啟用連結時，執行受管理的連結器，以擷取出未使用的組件，以最佳化您的組件。
-   AOT 編譯。
-   建立原生的可執行檔會輸出一系列的靜態程式庫 （一個用於每個組件） 連結到原生可執行檔，以便啟動程式碼、 登錄器程式碼 （如果有靜態），以及 AOT 的所有輸出組成的原生可執行檔編譯器


如需詳細的連結器和使用方式的詳細資訊，請參閱[連結器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>總結

本指南討論過的 Xamarin.iOS 應用程式，並瀏覽的 Xamarin.iOS 和 OBJECTIVE-C 深入了解其關聯性的 AOT 編譯。

## <a name="related-links"></a>相關連結

- [限制](~/ios/internals/limitations.md)
- [繫結 Objective-C](~/cross-platform/macios/binding/overview.md)
- [OBJECTIVE-C 選取器](~/ios/internals/objective-c-selectors.md)
- [型別註冊機構](~/ios/internals/registrar.md)
- [連結器](~/ios/deploy-test/linker.md)

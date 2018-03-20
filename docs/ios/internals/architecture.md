---
title: "iOS 架構"
description: "瀏覽 Xamarin.iOS 在較低層級"
ms.topic: article
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: c54ea9a2f63853066413f2db9713882441399744
ms.sourcegitcommit: cc38757f56aab53bce200e40f873eb8d0e5393c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2018
---
# <a name="ios-architecture"></a>iOS 架構

Xamarin.iOS 應用程式內單聲道執行環境、 執行，並使用完整的前的時間 (AOT) 編譯來編譯 C# 程式碼以 ARM 組件語言。 這會執行的並行與[Objective C 執行階段](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)。 這兩個執行階段環境上執行 unix 核心特別[XNU](https://en.wikipedia.org/wiki/XNU)，並公開各種 Api，可讓開發人員能夠存取基礎原生或 managed 系統的使用者程式碼。

下圖顯示這個架構的基本概觀：

[ ![](architecture-images/ios-arch-small.png "這個圖表會顯示前的時間 (AOT) 編譯架構的基本概觀")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>原生和 Managed 程式碼： 的說明

Xamarin 開發時條款*原生和 managed*通常會使用程式碼。 [Managed 程式碼](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/)是由管理其執行的程式碼[.NET Framework Common Language Runtime](https://msdn.microsoft.com/en-us/library/8bs2ecf4(v=vs.110).aspx)，或在 Xamarin 的情況下： Mono 執行階段。 這是我們呼叫的中繼語言。

原生程式碼是將特定的平台 （例如，OBJECTIVE-C 或甚至 AOT 編譯程式碼，在 ARM 晶片上的） 的原生方式執行的程式碼。 本指南會探討如何 AOT 編譯原生程式碼，managed 程式碼，並說明如何 Xamarin.iOS 應用程式的運作方式，充分使用 Apple iOS 應用程式開發介面使用的繫結，同時也擁有存取權。網路的 BCL 和複雜的語言，例如 C#。


## <a name="aot"></a>AOT

當您編譯任何 Xamarin 平台應用程式時，Mono C# （或 F #） 編譯器會執行，而且會將 C# 和 F # 程式碼編譯成 Microsoft Intermediate Language (MSIL)。 如果您正在 Xamarin.Android、 Xamarin.Mac 應用程式或甚至 Xamarin.iOS 應用程式在模擬器中， [.NET Common Language Runtime (CLR)](https://msdn.microsoft.com/en-us/library/8bs2ecf4(v=vs.110).aspx)編譯 Time (JIT) 編譯器中使用正的 MSIL。 在這編譯成原生程式碼的執行階段，它可以您的應用程式的正確架構上執行。

不過，沒有在 iOS、 Apple，就不允許在裝置上動態產生的程式碼執行所設定的安全性限制。
若要確保我們遵守這些安全性通訊協定，Xamarin.iOS 改為使用前的時間 (AOT) 編譯器來編譯 managed 程式碼。 這會產生二進位，原生 iOS 的裝置，可以部署 Apple 的 arm 處理器選擇性地最佳化 LLVM。 下圖說明這如何搭配運用的概略圖表：

[![](architecture-images/aot.png "這如何搭配運用概略圖表")](architecture-images/aot-large.png#lightbox)

使用 AOT 有幾項限制，詳述於[限制](~/ios/internals/limitations.md)指南。 它也提供許多改進功能透過 JIT 透過減少啟動時間，以及各種效能最佳化

既然我們已瀏覽程式碼如何從來源編譯為原生程式碼，讓我們看實際上以查看如何 Xamarin.iOS 可讓我們來撰寫完整原生 iOS 應用程式

## <a name="selectors"></a>選取器

使用 Xamarin，我們有兩個不同的生態系統，.NET 和 Apple，我們需要將在一起，似乎是為簡化越好，以確保最終目的是流暢的使用者體驗。 我們已經看到上述兩個執行階段通訊的方式，區段中，您可能很好聽過的詞彙 '繫結' 可原生 iOS 應用程式開發介面，以用於 Xamarin。 繫結說明，請參閱深入探索我們[OBJECTIVE-C 繫結](~/cross-platform/macios/binding/overview.md)文件，因此現在讓我們來探索 iOS 實際上的運作方式。

首先，必須要為公開 OBJECTIVE-C C#，其作法是透過選取器的方式。 選取器是一則訊息會傳送至物件或類別。 Objective C 的做法是透過[objc_msgSend](~/cross-platform/macios/binding/overview.md)函式。
如需有關如何使用選取器的詳細資訊，請參閱[Objective C 的選取器](~/ios/internals/objective-c-selectors.md)指南。 也有公開 managed 程式碼以 OBJECTIVE-C，這是更複雜，因為用 OBJECTIVE-C 完全不了解 managed 程式碼的方式。 若要解決這個問題，我們使用*註冊機構*。 這些會在下一節中詳細說明。

## <a name="registrars"></a>註冊機構

如上面所述，在註冊機構是程式碼目標 c 該公開 managed 程式碼 它會建立衍生自 NSObject 每個 managed 類別的清單：

- 針對不會包裝現有的 OBJECTIVE-C 類別的所有類別，它必須建立新的 OBJECTIVE-C 類別與鏡像的所有 managed 的成員 Objective C 成員 [`Export`] 屬性。

- 在每個目標 – C 成員的實作中，程式碼會自動加入至呼叫的受管理的鏡像的成員。

下列虛擬程式碼顯示範例的執行方式：

**C# （Managed 程式碼）**

```csharp
 class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
```

**Objective-C:**

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

Managed 程式碼可包含屬性，`[Register]`和`[Export]`，知道需要公開到目標 c。 該物件用於註冊機構
`[Register]`屬性用來指定所產生的 OBJECTIVE-C 類別的名稱，以免產生的預設名稱就不適當。 所有的類別衍生自 NSObject 自動向目標 c。
所需`[Export]`屬性包含字串，這是用於產生 OBJECTIVE-C 類別中的選取器。

有兩種類型的註冊機構，用於 Xamarin.iOS – 動態和靜態：


- **動態註冊機構**– 動態註冊機構未在執行階段組件中的所有類型的註冊。 其做法是使用所提供的函式[Objective C 的執行階段 API](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/)。 動態註冊機構，因此有速度較慢啟動，但更快建置時間。 這是預設值為 iOS 模擬器。 原生函式 （通常是在 C 中)，稱為 trampolines，可用做為方法的實作使用動態註冊機構。 兩者的不同架構之間的差異。

- **靜態註冊機構**– 靜態註冊機構然後編譯成靜態程式庫並連結到可執行檔在建置期間產生 Objective C 程式碼。 這允許更快速啟動時，但所花費的時間建置時間期間。 這會使用預設針對裝置組建。 靜態註冊機構也可搭配 iOS 模擬器藉由傳遞`--registrar:static`為`mtouch`屬性在專案的組建選項，如下所示：

    [![](architecture-images/image1.png "設定其他 mtouch 引數")](architecture-images/image1.png#lightbox)

更多細節 iOS Xamarin.iOS 所使用的型別註冊系統的詳細資訊，請參閱[類型註冊機構](~/ios/internals/registrar.md)指南。

## <a name="application-launch"></a>啟動應用程式

所有 Xamarin.iOS 可執行檔的進入點所呼叫的函式提供`xamarin_main`，其中初始化單聲道。

根據專案類型，下列作法：

- 對於一般的 iOS 和 tvOS 應用程式，稱為受管理的 Main 方法，提供的 Xamarin 應用程式。 此管理 Main 方法接著會呼叫`UIApplication.Main`，這是目標 c 的進入點 UIApplication.Main 是 Objective c 的繫結`UIApplicationMain`方法。
- 針對擴充功能，原生函式中 –`NSExtensionMain`或 (`NSExtensionmain` WatchOS 延伸模組) – 提供 Apple 所呼叫的程式庫。 這些專案是類別庫，並且是可執行檔專案，因為沒有任何受管理的主要方法來執行。

所有此啟動程序會編譯成靜態程式庫，然後連結至您的最終可執行檔讓您的應用程式知道如何取得踏出。

此時我們的應用程式已啟動、 單聲道執行，我們會在 managed 程式碼，我們已經知道如何呼叫原生程式碼和回撥。 我們必須進行下一件事是實際開始新增控制項，並讓應用程式互動。


## <a name="generator"></a>Generator

Xamarin.iOS 包含每個單一的 iOS 應用程式開發介面的定義。 您可以在瀏覽這些[MaciOS github 儲存機制](https://github.com/xamarin/xamarin-macios/tree/master/src)。 這些定義包含屬性，與介面，以及任何必要的方法和屬性。 例如，下列程式碼是用來定義 UIKit UIToolbar[命名空間](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327)。 請注意，它含有一些方法和屬性的介面：

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

產生器，稱為[ `btouch` ](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) Xamarin.iOS，會將這些定義檔案，並使用.NET 工具[它們編譯至暫存組件](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318)。 不過，這個暫存組件不能呼叫 Objective C 程式碼。 然後產生器會讀取暫存組件，並會產生 C# 程式碼可以在執行階段使用。
這是為什麼，比方說，如果您將隨機屬性加入您定義的.cs 檔案時，它將不會出現在輸出的程式碼。 產生器並不知道，因此`btouch`並不知道要在其輸出的暫存組件中尋找它。

一旦建立 Xamarin.iOS.dll mtouch 將配套的所有元件起來。

在高層級，它的做法是執行下列工作：

-   建立應用程式資源存放區結構。
-   複製您的 managed 組件中。
-   如果連結已啟用，執行受管理的連結器，來最佳化您的組件擷取出未使用的組件。
-   AOT 編譯。
-   建立原生的可執行檔的輸出一系列的靜態程式庫 （一個用於每個組件） 都連結到原生可執行檔，以便啟動程式碼、 註冊機構程式碼 （如果有靜態） 及從 AOT 所有輸出組成原生可執行檔編譯器


如需詳細的連結器和使用方式的詳細資訊，請參閱[連結器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>總結

本指南討論了 AOT 編譯 Xamarin.iOS 應用程式，並瀏覽的 Xamarin.iOS 以及其 OBJECTIVE-C 深度的關聯性。

## <a name="related-links"></a>相關連結

- [限制](~/ios/internals/limitations.md)
- [繫結 Objective-C](~/cross-platform/macios/binding/overview.md)
- [Objective C 的選取器](~/ios/internals/objective-c-selectors.md)
- [類型的註冊機構](~/ios/internals/registrar.md)
- [連結器](~/ios/deploy-test/linker.md)

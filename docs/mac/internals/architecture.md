---
title: Xamarin. Mac 架構
description: 本指南會探索 Xamarin 和其在較低層級的目標-C 的關聯性。 其中說明編譯、選取器、註冊機構、應用程式啟動和產生器等概念。
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 04/12/2017
ms.openlocfilehash: 2c9bbd663257e937e35e062f03b4aa84813edb27
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287777"
---
# <a name="xamarinmac-architecture"></a>Xamarin. Mac 架構

_本指南會探索 Xamarin 和其在較低層級的目標-C 的關聯性。其中說明編譯、選取器、註冊機構、應用程式啟動和產生器等概念。_

## <a name="overview"></a>總覽

Xamarin 應用程式會在 Mono 執行環境中執行，並使用 Xamarin 的編譯器向下編譯成中繼語言（IL），這是在執行時間編譯為機器碼的即時（JIT）。 這會與目標-C 執行時間並存執行。 這兩個執行時間環境會在類似 UNIX 的核心上執行，特別是 XNU，並將各種 Api 公開給使用者程式碼，讓開發人員能夠存取基礎的原生或受控系統。

下圖顯示此架構的基本總覽：

[![此圖顯示架構的基本總覽](architecture-images/mac-arch.png "此圖顯示架構的基本總覽")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>原生和 managed 程式碼

針對 Xamarin 進行開發時，通常會使用*原生*和*managed*程式碼詞彙。 Managed 程式碼是 .NET Framework Common Language Runtime 所管理的程式碼，或在 Xamarin 的案例中： Mono 執行時間。

機器碼是以原生方式在特定平臺上執行的程式碼（例如，在 ARM 晶片上的目標 C 或甚至是 AOT 編譯器代碼）。 本指南將探討如何將受控碼編譯成機器碼，並說明 Xamarin 應用程式的運作方式、使用系結來完全使用 Apple 的 Mac Api，同時也能存取。NET 的 BCL 和複雜的語言，例如C#。

## <a name="requirements"></a>需求

使用 Xamarin.Mac 開發 macOS 應用程式需要下列項目：

- 執行 macOS Sierra （10.12）或更新版本的 Mac。
- 最新版的 Xcode （從[App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)安裝）
- 最新版本的 Xamarin. Mac 和 Visual Studio for Mac

執行使用 Xamarin.Mac 建立的 Mac 應用程式有下列系統需求：

- 執行 Mac OS X 10.7 或更新版本的 Mac。

## <a name="compilation"></a>編譯

當您編譯任何 Xamarin 平臺應用程式時， C# Mono （ F#或）編譯器將會執行，並C#將F#您的和程式碼編譯成 Microsoft 中繼語言（MSIL 或 IL）。 接著，Xamarin 會在運行*時間使用即時（JIT）* 編譯器來編譯機器碼，視需要允許在正確的架構上執行。

這與使用 AOT 編譯的 Xamarin 相反。 使用 AOT 編譯器時，這些元件和其中的所有方法都會在組建階段進行編譯。 使用 JIT 時，編譯只會視需要針對執行的方法進行。

在 Xamarin. Mac 應用程式中，Mono 通常內嵌于應用程式套件組合中（也稱為**內嵌 Mono**）。 使用傳統的 Xamarin. Mac API 時，應用程式可以改為使用**系統 Mono**，不過，Unified API 不支援這種方式。 系統 Mono 指的是已安裝在作業系統中的 Mono。 在應用程式啟動時，Xamarin. Mac 應用程式會使用此。

## <a name="selectors"></a>選取器

有了 Xamarin，我們有兩個不同的生態系統（.NET 和 Apple），我們需要將它們結合在一起，以確保最終目標是順暢的使用者體驗。 我們在上一節看到了這兩個執行時間的通訊方式，而您可能已經聽過「系結」一詞，這可讓您在 Xamarin 中使用原生 Mac Api。 系結會在[目標-C](~/mac/platform/binding.md)系結檔中深入說明，因此現在讓我們來探索 Xamarin 的運作方式。

首先，必須有一種方法可將目標-C 公開給C#，這是透過選取器來完成的。 「選取器」是傳送至物件或類別的訊息。 使用目標-C，這是透過[objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html)函數來完成。 如需使用選取器的詳細資訊，請參閱 iOS[目標-C 選取器](~/ios/internals/objective-c-selectors.md)指南。 此外，也必須有方法可以將 managed 程式碼公開至目標-C，因為目標-C 並不知道 managed 程式碼的任何內容，因此更複雜。 為了解決這個情況，我們使用[註冊機構](~/mac/internals/registrar.md)。 這會在下一節中更詳細地說明。

## <a name="registrar"></a>登錄器

如先前所述，註冊機構是將 managed 程式碼公開至目標-C 的程式碼。 它會建立一個衍生自 NSObject 的每個 managed 類別清單來執行這項操作：

- 對於未包裝現有目標-c 類別的所有類別，它會建立新的目標 c 類別，其中的目標 c 成員會鏡像擁有`[Export]`屬性的所有 managed 成員。
- 在每個目標– C 成員的執行中，會自動加入程式碼來呼叫鏡像的 managed 成員。

下列虛擬程式碼顯示如何完成此作業的範例：

**C#（managed 程式碼）：**

```csharp
class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
 ```

**目標-C （機器碼）：**

```objc
@interface MyViewController : UIViewController
 - (void)myFunc;
@end 

@implementation MyViewController
- (void)myFunc {
    // Code to call the managed C# MyFunc method in MyViewController
}
@end
```

Managed 程式碼可以包含屬性， `[Register]`以及`[Export]`註冊機構用來知道物件需要公開至目標-C 的屬性。 [Register] 屬性可用來指定所產生之目標 C 類別的名稱，以防預設產生的名稱不適用。 所有衍生自 NSObject 的類別都會自動向目標-C 註冊。 必要的 [Export] 屬性包含字串，這是在產生的目標-C 類別中使用的選取器。

Xamarin 中使用兩種類型的註冊機構-動態和靜態：

- 動態註冊機構–這是所有 Xamarin. Mac 組建的預設註冊機構。 動態註冊機構會在執行時間註冊元件中的所有類型。 它會使用由目標 C 的執行時間 API 所提供的函式來執行這項工作。 因此，動態註冊機構的啟動速度較慢，但組建時間較快。 使用動態註冊機構時，原生函式（通常在 C 中）稱為 trampolines，可做為方法的執行。 它們在不同的架構之間有所不同。
- 靜態註冊機構–靜態註冊機構會在組建期間產生目標 C 程式碼，然後將其編譯成靜態程式庫並連結至可執行檔。 這可加快啟動速度，但會在組建期間耗費較長的時間。

## <a name="application-launch"></a>應用程式啟動

根據內嵌或系統 Mono 的使用，Xamarin 的啟動邏輯會有所不同。 若要查看 Xamarin 應用程式啟動的程式碼和步驟，請參閱 xamarin-macios 公用存放庫中的[啟動頭](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h)檔。

## <a name="generator"></a>Generator

Xamarin 會包含每個 Mac API 的定義。 您可以流覽[MaciOS github](https://github.com/xamarin/xamarin-macios/tree/master/src)存放庫中的任何一項。 這些定義包含具有屬性的介面，以及任何必要的方法和屬性。 例如，下列程式碼是用來定義[AppKit 命名空間](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526)中的 NSBox。 請注意，它是具有許多方法和屬性的介面：

```csharp
[BaseType (typeof (NSView))]
public interface NSBox {

    …

    [Export ("borderRect")]
    CGRect BorderRect { get; }

    [Export ("titleRect")]
    CGRect TitleRect { get; }

    [Export ("titleCell")]
    NSObject TitleCell { get; }

    [Export ("sizeToFit")]
    void SizeToFit ();

    [Export ("contentViewMargins")]
    CGSize ContentViewMargins { get; set; }

    [Export ("setFrameFromContentFrame:")]
    void SetFrameFromContentFrame (CGRect contentFrame);

    …

}
```

在 Xamarin 中呼叫`bmac`的產生器會接受這些定義檔，並使用 .net 工具將它們編譯成暫時的元件。 不過，此暫存元件無法用來呼叫目標-C 程式碼。 然後，產生器會讀取暫存元件， C#並產生可在執行時間使用的程式碼。 例如，如果您將隨機屬性新增至定義 .cs 檔案，它就不會顯示在輸出程式碼中。 產生器並不知道它，因此`bmac`不知道要在暫存元件中尋找它來輸出它。

建立`mmp`了 Xamarin .dll 之後，封裝程式會將所有元件組合在一起。

在高階中，它會執行下列工作來達到此程度：

- 建立應用程式套件組合結構。
- 複製您的 managed 元件。
- 如果已啟用連結，請執行 managed 連結器，藉由移除未使用的元件來優化您的元件。
- 建立啟動器應用程式，並在與註冊機構程式碼（如果處於靜態模式）中連結。

然後，這會在使用者組建程式中執行，將使用者程式碼編譯成參考的 Xamarin. Mac 的元件，然後執行`mmp`以使其成為封裝

如需連結器和其使用方式的詳細資訊，請參閱 iOS[連結器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>總結

本指南探討了 Xamarin. Mac 應用程式的編譯，以及探索到的 Xamarin 和其與目標-C 的關係。

---
title: Xamarin.Mac 架構
description: 本指南會探索 Xamarin.Mac 和其關聯性以 OBJECTIVE-C 在較低層級。 它說明概念，例如編譯、 選取器、 註冊機構、 應用程式啟動和產生器。
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/12/2017
ms.openlocfilehash: d6d7557fed5ea0ca0719dcbddbda316340645320
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30785552"
---
# <a name="xamarinmac-architecture"></a>Xamarin.Mac 架構

_本指南會探索 Xamarin.Mac 和其關聯性以 OBJECTIVE-C 在較低層級。它說明概念，例如編譯、 選取器、 註冊機構、 應用程式啟動和產生器。_

## <a name="overview"></a>總覽

Xamarin.Mac 應用程式內單聲道執行環境、 執行，並使用 Xamarin 的編譯器來編譯到中繼語言 (IL)，就只需的時間 (JIT) 編譯為原生程式碼在執行階段。 這會執行的並行 Objective C 執行階段。 這兩個執行階段環境 unix 核心，特別是 XNU 之上執行，並公開各種 Api，可讓開發人員能夠存取基礎原生或 managed 系統的使用者程式碼。

下圖顯示這個架構的基本概觀：

[![顯示架構的基本概觀圖表](architecture-images/mac-arch.png "顯示架構的基本概觀圖表")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>原生和 managed 程式碼

Xamarin 開發時條款*原生*和*管理*通常會使用程式碼。 Managed 程式碼為其.NET Framework Common Language Runtime，或在 Xamarin 的情況下管理其執行的程式碼： Mono 執行階段。

原生程式碼是將特定的平台 （例如，OBJECTIVE-C 或甚至 AOT 編譯程式碼，在 ARM 晶片上的） 的原生方式執行的程式碼。 本指南會探討如何 managed 程式碼編譯為原生程式碼，並說明如何 Xamarin.Mac 應用程式的運作方式，充分使用 Apple 的 Mac 應用程式開發介面使用的繫結，同時也擁有存取權。網路的 BCL 和複雜的語言，例如 C#。

## <a name="requirements"></a>需求

使用 Xamarin.Mac 開發 macOS 應用程式需要下列項目：

- Mac 執行 macOS 利也 (10.12) 或更高。
- Xcode 的最新版本 (從安裝[App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- 最新版的 Xamarin.Mac 和 Visual Studio for Mac

執行使用 Xamarin.Mac 建立的 Mac 應用程式有下列系統需求：

- 執行 Mac OS X 10.7 或更高版本的 Mac。

## <a name="compilation"></a>編譯

當您編譯任何 Xamarin 平台應用程式時，Mono C# （或 F #） 編譯器會執行，並將您 C# 和 F # 程式碼編譯成 Microsoft Intermediate Language （MSIL 或 IL）。 接著會使用 Xamarin.Mac *Just in Time (JIT)* 編譯器來編譯原生程式碼，讓正確的架構上執行，視需要執行階段。

這是相較於使用 AOT 編譯 Xamarin.iOS。 使用 AOT 編譯器時，所有組件與其中的所有方法都會編譯在建置階段中。 使用 JIT 編譯發生狀況的方法所執行的需求。

使用 Xamarin.Mac 應用程式中，單聲道通常會內嵌到應用程式套件組合 (稱為**內嵌 Mono**)。 使用傳統 Xamarin.Mac API 時，應用程式可以改為使用**系統 Mono**，不過，這不支援在統一的 API。 系統 Mono 指的是已安裝在作業系統中的單聲道。 在應用程式啟動 Xamarin.Mac 應用程式將使用此。

## <a name="selectors"></a>選取器

使用 Xamarin，我們有兩個不同的生態系統，.NET 和 Apple，我們需要將在一起，似乎是為簡化越好，以確保最終目的是流暢的使用者體驗。 我們已經看到上述兩個執行階段通訊的方式，區段中，您可能很好聽過的詞彙 '繫結' 可原生 Mac Api 以供 Xamarin。 繫結會在中深入說明[OBJECTIVE-C 繫結文件](~/mac/platform/binding.md)，因此現在讓我們來探索 Xamarin.Mac 實際上的運作方式。

首先，必須要為公開 OBJECTIVE-C C#，其作法是透過選取器的方式。 選取器是一則訊息會傳送至物件或類別。 Objective C 的做法是透過[objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html)函式。 如需有關如何使用選取器的詳細資訊，請參閱 iOS [Objective C 的選取器](~/ios/internals/objective-c-selectors.md)指南。 也有公開 managed 程式碼以 OBJECTIVE-C，這是更複雜，因為用 OBJECTIVE-C 完全不了解 managed 程式碼的方式。 若要解決這個問題，我們使用[註冊機構](~/mac/internals/registrar.md)。 這在下一節中詳細說明。

## <a name="registrar"></a>登錄器

如上面所述，在註冊機構是程式碼目標 c 該公開 managed 程式碼 它會建立衍生自 NSObject 每個 managed 類別的清單：

- 針對不會包裝現有的 OBJECTIVE-C 類別的所有類別，它必須建立新的 OBJECTIVE-C 類別與鏡像的所有 managed 的成員 Objective C 成員`[Export]`屬性。
- 在每個目標 – C 成員的實作中，程式碼會自動加入至呼叫的受管理的鏡像的成員。

下列虛擬程式碼顯示範例的執行方式：

**C# （managed 程式碼）：**

```csharp
class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
 ```

**目標-C （原生程式碼）：**

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

Managed 程式碼可包含屬性，`[Register]`和`[Export]`，知道需要公開到目標 c。 該物件用於註冊機構 [註冊] 屬性用來指定所產生的 OBJECTIVE-C 類別的名稱，如果產生的預設名稱就不適當。 所有的類別衍生自 NSObject 自動向目標 c。 必要的 [匯出] 屬性包含字串，這是用於產生 OBJECTIVE-C 類別中的選取器。

有兩種類型的註冊機構，用於 Xamarin.Mac – 動態和靜態：

- 動態註冊機構 – 這是針對所有 Xamarin.Mac 組建的預設註冊機構。 動態註冊機構未在執行階段組件中的所有類型的註冊。 它會使用 Objective C 的執行階段 API 所提供的函式。 動態註冊機構，因此有速度較慢啟動，但更快建置時間。 原生函式 （通常是在 C 中)，稱為 trampolines，可用做為方法的實作使用動態註冊機構。 兩者的不同架構之間的差異。
- 靜態註冊機構 – 靜態註冊機構產生 Objective C 程式碼在建置期間，這是編譯成靜態程式庫，然後連結至可執行檔。 這允許更快速啟動時，但所花費的時間建置時間期間。

## <a name="application-launch"></a>啟動應用程式

Xamarin.Mac 啟動邏輯與會有所不同，取決於是否內嵌，或使用系統單聲道。 若要檢視程式碼和 Xamarin.Mac 應用程式啟動的步驟，請參閱[啟動標頭](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h)xamarin macios 公用儲存機制中的檔案。

## <a name="generator"></a>Generator

Xamarin.Mac 包含每個 Mac 應用程式開發介面的定義。 您可以在瀏覽這些[MaciOS github 儲存機制](https://github.com/xamarin/xamarin-macios/tree/master/src)。 這些定義包含屬性，與介面，以及任何必要的方法和屬性。 例如，下列程式碼是用來定義在 NSBox [AppKit 命名空間](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526)。 請注意，它含有一些方法和屬性的介面：

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

產生器，稱為`bmac`Xamarin.Mac，會將這些定義檔案，並將它們編譯至暫存組件中使用.NET 工具。 不過，這個暫存組件不能呼叫 Objective C 程式碼。 然後產生器會讀取暫存組件，並會產生 C# 程式碼可以在執行階段使用。 這是為什麼，比方說，如果您將隨機屬性加入您定義的.cs 檔案時，它將不會出現在輸出的程式碼。 產生器不了解，因此`bmac`並不知道要在其輸出的暫存組件中尋找它。

Xamarin.Mac.dll 建立之後，封裝程式`mmp`，同時將配套的所有元件。

在高層級，它的做法是執行下列工作：

- 建立應用程式資源存放區結構。
- 複製您的 managed 組件中。
- 如果連結已啟用，執行受管理的連結器，來最佳化您的組件移除未使用的組件。
- 建立啟動器應用程式，以及 registar 中的程式碼如果靜態模式討論啟動器程式碼中的連結。

這是那麼一部分使用者執行建立程序，使用者程式碼編譯成組件，該參考 Xamarin.Mac.dll 並執行`mmp`使其封裝

如需詳細的連結器和使用方式的詳細資訊，請參閱 iOS[連結器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>總結

本指南看 Xamarin.Mac 應用程式，並瀏覽的 Xamarin.Mac 和其關聯性目標 c 的編譯

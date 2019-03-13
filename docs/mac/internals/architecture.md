---
title: Xamarin.Mac 架構
description: 本指南會探討低層級的 objective-c Xamarin.Mac 和其關聯性。 它會說明概念，例如編譯中，選取器，註冊機構、 應用程式啟動和產生器。
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 04/12/2017
ms.openlocfilehash: 1ea38b527acaa89b9f25690de4e55664a7afd9e8
ms.sourcegitcommit: d09391c315336d36496880ef465a72b8974f2ac7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2018
ms.locfileid: "51579826"
---
# <a name="xamarinmac-architecture"></a>Xamarin.Mac 架構

_本指南會探討低層級的 objective-c Xamarin.Mac 和其關聯性。它會說明概念，例如編譯中，選取器，註冊機構、 應用程式啟動和產生器。_

## <a name="overview"></a>總覽

Xamarin.Mac 應用程式在 Mono 執行環境中，執行，並使用 Xamarin 的編譯器編譯到中繼語言 (IL)，並會恰好及時 (JIT) 編譯為原生程式碼在執行階段。 這會執行並排顯示與 OBJECTIVE-C 執行階段。 這兩個執行階段環境在類似 UNIX 的核心，特別是 XNU 之上執行，並公開 （expose） 可讓開發人員能夠存取基礎原生或 managed 系統的使用者程式碼的各種 Api。

下圖顯示此架構的基本概觀：

[![此圖顯示架構的基本概觀](architecture-images/mac-arch.png "此圖顯示架構的基本概觀")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>原生和 managed 程式碼

Xamarin 的套件，開發時條款*原生*並*受控*經常使用的程式碼。 Managed 程式碼是由.NET Framework Common Language Runtime，，或在 Xamarin 的情況下執行的程式碼： Mono 執行階段。

原生程式碼是將特定的平台 （例如，OBJECTIVE-C 或甚至 AOT 編譯程式碼，在 ARM 晶片上的） 的原生方式執行的程式碼。 本指南探索如何 managed 程式碼會編譯成原生程式碼，並說明如何 Xamarin.Mac 應用程式的運作方式，充分運用透過繫結，使用 Apple Mac Api 同時有存取權。NET 的 BCL 和複雜的語言，例如C#。

## <a name="requirements"></a>需求

使用 Xamarin.Mac 開發 macOS 應用程式需要下列項目：

- Mac 執行 macOS Sierra (10.12) 或更新版本。
- 最新版的 Xcode (從安裝[App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- Xamarin.Mac 和 Visual Studio for Mac 的最新版本

執行使用 Xamarin.Mac 建立的 Mac 應用程式有下列系統需求：

- 在執行 Mac OS X 10.7 或更新版本的 Mac。

## <a name="compilation"></a>編譯

當您編譯任何 Xamarin 平台應用程式，Mono C# (或F#) 編譯器會執行，而且將會編譯您C#和F#Microsoft Intermediate Language （MSIL 或 IL） 程式碼。 接著會使用 Xamarin.Mac *Just in Time (JIT)* 編譯器在編譯原生程式碼，使正確的架構上執行，視執行階段。

這是相較於使用 AOT 編譯的 Xamarin.iOS。 使用 AOT 編譯器時，會在建置階段編譯所有組件和其內的所有方法。 與 JIT 編譯會發生上執行的方法的需求。

使用 Xamarin.Mac 應用程式，Mono 通常會內嵌到應用程式套件組合 (稱為**內嵌的 Mono**)。 使用傳統的 Xamarin.Mac API 時，應用程式可以改用**系統 Mono**，不過，這不支援，在統一的 API。 系統 Mono 是指已安裝作業系統的 Mono。 應用程式啟動時，Xamarin.Mac 應用程式將使用此。

## <a name="selectors"></a>選取器

有了 Xamarin，我們必須要有兩個不同的生態系統，.NET 和 Apple，我們需要將一起變得越好，以確保最終目標是流暢的使用者體驗，簡化。 我們在前兩個執行階段通訊的方式，一節中討論過，您可能很聽過的詞彙 '繫結' 可讓原生 Mac Api 在 Xamarin 中使用。 繫結會說明中的深度[OBJECTIVE-C 繫結的文件](~/mac/platform/binding.md)，因此現在，讓我們瀏覽 Xamarin.Mac 幕後的運作方式。

首先，必須要有的方式來公開以 OBJECTIVE-C C#，其中會透過選取器。 選取器是一則訊息會傳送至物件或類別。 與 OBJECTIVE-C 這透過完成[objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html)函式。 如需有關如何使用選取器的詳細資訊，請參閱 iOS [OBJECTIVE-C 選取器](~/ios/internals/objective-c-selectors.md)指南。 也必須是公開以 OBJECTIVE-C、 managed 程式碼因為的 Objective C 不了解 managed 程式碼，這是更複雜的方式。 若要解決這個問題，我們使用[登錄器](~/mac/internals/registrar.md)。 這一節中詳細說明。

## <a name="registrar"></a>登錄器

如先前所述，註冊機構是程式碼，會公開 managed 程式碼以 OBJECTIVE-C 它會藉由建立衍生自 NSObject 的每個 managed 類別的清單：

- 針對不會包裝現有的 OBJECTIVE-C 類別的所有類別，它會建立新的 Objective C 類別與鏡像的所有受管理的成員的 Objective C 成員`[Export]`屬性。
- 在每個目標 – C 成員實作中，程式碼會自動新增至呼叫的鏡像受管理的成員。

下列虛擬程式碼示範如何做到這點：

**C#（managed 程式碼）：**

```csharp
class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
 ```

**Objective C （原生程式碼）：**

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

Managed 程式碼可包含屬性，`[Register]`和`[Export]`，註冊機構會知道物件需要公開以 OBJECTIVE-C [註冊] 屬性用來指定所產生的 Objective C 類別的名稱，如果預設產生的名稱不適合。 所有的類別衍生自 NSObject 自動向 OBJECTIVE-C 必要的 [匯出] 屬性包含字串，也就是使用所產生的 Objective C 類別中的選取器。

有兩種類型的註冊機構用於 Xamarin.Mac – 動態和靜態：

- 動態註冊機構 – 這是所有 Xamarin.Mac 組建的預設註冊機構。 動態註冊機構會在執行階段組件中的所有類型的註冊。 它會使用提供的 Objective C 的執行階段 API 函式。 動態註冊機構，因此有速度較慢啟動，但更快建置時間。 原生函式 （通常是在 C 中)，稱為 trampolines，會將數字做方法實作，當使用動態的註冊機構。 兩者的不同架構之間的差異。
- 靜態註冊機構 – 靜態註冊機構會在建置期間，然後編譯成靜態程式庫並連結至可執行檔，產生 OBJECTIVE-C 程式碼。 這允許較快的啟動，但所花費的時間在建置階段。

## <a name="application-launch"></a>啟動應用程式

Xamarin.Mac 啟動邏輯會隨是否內嵌，或使用系統 Mono。 若要檢視程式碼及啟動 Xamarin.Mac 應用程式的步驟，請參閱[啟動標頭](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h)xamarin macios 公用存放庫中的檔案。

## <a name="generator"></a>Generator

Xamarin.Mac 包含每一種 Mac api 的定義。 您可以在瀏覽任一[MaciOS github 存放庫](https://github.com/xamarin/xamarin-macios/tree/master/src)。 這些定義會包含介面的屬性，以及任何必要的方法和屬性。 比方說，下列程式碼用來定義在 NSBox [AppKit 命名空間](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526)。 請注意，它含有一些方法和屬性的介面：

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

產生器，稱為`bmac`Xamarin.Mac，都會採用這些定義檔案，並將它們編譯成暫存組件中使用.NET 工具。 不過，此暫存組件並不會使用呼叫 OBJECTIVE-C 程式碼。 產生器接著會讀取暫存組件，並產生C#可以在執行階段使用的程式碼。 這是為什麼，比方說，您隨機屬性新增至您定義的.cs 檔案時，如果它不會顯示在輸出的程式碼。 產生器不會知道它，因此`bmac`還不知道要在其輸出的暫存組件中尋找它。

Xamarin.Mac.dll 建立之後，封裝程式`mmp`，會一起聚集的所有元件。

概括而言，它的做法是執行下列工作：

- 建立應用程式套件組合結構。
- 複製您的 managed 組件中。
- 如果啟用連結時，執行受管理的連結器，以移除未使用的組件，以最佳化您的組件。
- 建立啟動器應用程式，在談到註冊機構中的程式碼如果靜態模式啟動程式程式碼連結。

這是以使用者的一部分執行建置程序，將使用者程式碼編譯成組件，該參考 Xamarin.Mac.dll 並執行，則`mmp`讓封裝

如需詳細的連結器和使用方式的詳細資訊，請參閱 iOS[連結器](~/ios/deploy-test/linker.md)指南。

## <a name="summary"></a>總結

本指南探討 Xamarin.Mac 應用程式，並瀏覽的 Xamarin.Mac 和 OBJECTIVE-C 其關聯性的編譯

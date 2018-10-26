---
title: Xamarin.Mac 的運作方式
description: 本文件說明 Xamarin.Mac 的內部運作。 特別是，它會尋找在建構函式、 記憶體管理、 預先編譯，並在註冊機構。
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/25/2017
ms.openlocfilehash: cd5371cde1dfcbe3cb1aea5dbdf8439816d66d95
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111313"
---
# <a name="how-xamarinmac-works"></a>Xamarin.Mac 的運作方式

大部分的情況，開發人員將永遠不必擔心內部"magic"的 Xamarin.Mac，不過，有項目在幕後的運作方式如何協助與這兩種解譯現有文件中的粗略了解C#功能濾鏡和偵錯在發生時的問題。

在 Xamarin.Mac 應用程式橋接兩個世界： 沒有包含原生類別的執行個體的 Objective C 執行階段 (`NSString`，`NSApplication`等等)，而且沒有C#包含的執行個體的執行階段 managed 類別 (`System.String``HttpClient`等等)。 這兩個世界，之間 Xamarin.Mac 建立的雙向橋接器讓應用程式可以呼叫方法 （選取器） 在 OBJECTIVE-C 中 (例如`NSApplication.Init`) 和 OBJECTIVE-C 可以呼叫應用程式的C#方法後 （例如上的應用程式委派的方法）。 一般情況下，OBJECTIVE-C 呼叫都會透明處理透過**P/Invokes**和 Xamarin 提供一些執行階段程式碼。

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>公開C#類別 / objective-c 方法

不過，對於 OBJECTIVE-C 呼叫的應用程式C#物件，他們需要某種程度的了解 OBJECTIVE-C 中公開。 這透過完成`Register`和`Export`屬性。 請使用以下範例：

```csharp
[Register ("MyClass")]
public class MyClass : NSObject
{
   [Export ("init")]
   public MyClass ()
   {
   }

   [Export ("run")]
   public void Run ()
   {
   }
}
```

在此範例中，OBJECTIVE-C 執行階段現在知道呼叫類別的相關`MyClass`與選取器呼叫`init`和`run`。

在大部分情況下，這是實作詳細資料，開發人員可以忽略，因為大部分的應用程式收到的回呼會透過覆寫方法`base`類別 (例如`AppDelegate`， `Delegates`， `DataSources`)，或在**動作**傳遞至 Api。 在所有這些情況下，`Export`屬性不是必要的C#程式碼。

## <a name="constructor-runthrough"></a>建構函式 runthrough

在許多情況下，開發人員必須公開 （expose） 應用程式的C#Objective C 執行階段，因此它可以具現化的位置，例如當類別建構 API 的呼叫將分鏡腳本或 XIB 檔案。 以下是五個最常見的建構函式用於 Xamarin.Mac 應用程式：

```csharp
// Called when created from unmanaged code
public CustomView (IntPtr handle) : base (handle)
{
   Initialize ();
}

// Called when created directly from a XIB file
[Export ("initWithCoder:")]
public CustomView (NSCoder coder) : base (coder)
{
   Initialize ();
}

// Called from C# to instance NSView with a Frame (initWithFrame)
public CustomView (CGRect frame) : base (frame)
{
}

// Called from C# to instance NSView without setting the frame (init)
public CustomView () : base ()
{
}

// This is a special case constructor that you call on a derived class when the derived called has an [Export] constructor.
// For example, if you call init on NSString then you don’t want to call init on NSObject.
public CustomView () : base (NSObjectFlag.Empty)
{
}
```

一般情況下，開發人員應該將保留`IntPtr`並`NSCoder`建構函式時建立某些類型，例如自訂所產生`NSViews`單獨。 如果 Xamarin.Mac 需要呼叫這些建構函式的其中一個以 OBJECTIVE-C 執行階段要求的回應，而且您已將它移除，原生程式碼內的應用程式會當機，並可能會難以找出確切的問題。

## <a name="memory-management-and-cycles"></a>記憶體管理和循環

Xamarin.mac 的記憶體管理是在許多方面非常類似於 Xamarin.iOS。 它也是一個複雜的主題，其中一個超出本文的範圍。 請閱讀[記憶體和效能最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)。

## <a name="ahead-of-time-compilation"></a>直接的編譯

一般而言，.NET 應用程式不會編譯至機器碼會在建置時，而它們會編譯為中繼層呼叫取得的 IL 程式碼_Just In Time_ (JIT) 編譯成機器碼，應用程式啟動時。

JIT 編譯 mono 執行階段所花費的時間，因為需要必要的機器碼，以產生的時間，此機器的程式碼就會達 20%，降低 Xamarin.Mac 應用程式的啟動作業。

由於在 iOS 上，Apple 所加諸的限制，JIT 編譯的 IL 程式碼不適用於 Xamarin.iOS。 如此一來，所有的 Xamarin.iOS 應用程式都填滿_Just-in-time 預先_組建循環期間 (AOT) 編譯成機器碼。

Xamarin.Mac 是新 AOT 的能力的 IL 程式碼在應用程式組建循環，就像 Xamarin.iOS 可以。 使用 Xamarin.Mac_混合式_AOT 編譯所需的機器碼中，大部分的方法，但可讓執行階段編譯所需的 trampolines 和彈性地繼續支援這些事件處理常式 （及其他使用案例，目前作用於 Xamarin.Mac）。

有兩個 AOT 可以幫助 Xamarin.Mac 應用程式的主要區域：

- **更好的 「 原生 」 的當機記錄檔**-如果 Xamarin.Mac 應用程式損毀時進行無效的 Cocoa Api 的呼叫，經常發生的原生程式碼 (例如傳送`null`不接受它的方法) 的原生損毀 JIT 的記錄檔畫面格很難進行分析。 因為 JIT 框架沒有偵錯資訊，將會有多行與十六進位的位移和在沒有頭緒。 AOT 會產生"real"具名的框架，而且更容易讀取追蹤。 這也表示 Xamarin.Mac 應用程式的互動就更好的原生工具這類**lldb**並**Instruments**。
- **更好的啟動時間效能**-針對大型的 Xamarin.Mac 應用程式，具有多個第二個啟動時間，JIT 編譯所有的程式碼可能需要很長的時間。 AOT 執行此工作。

### <a name="enabling-aot-compilation"></a>啟用 AOT 編譯

按兩下 Xamarin.Mac 中啟用 AOT**專案名稱**中**方案總管**，請巡覽至**Mac 組建**並新增`--aot:[options]`到**其他 mmp 引數：** 欄位 (其中`[options]`是一或多個選項來控制 AOT 型別，如下所示)。 例如: 

![加入其他 mmp 引數的 AOT](how-it-works-images/aot01.png "新增至 其他 mmp 引數的 AOT")

> [!IMPORTANT]
> 啟用 AOT 編譯會大幅增加建置時間，有時候幾分鐘的時間，但它可以改善應用程式啟動時間 20%的平均值。 如此一來，AOT 編譯應該只啟用**發行**Xamarin.Mac 應用程式的組建。

### <a name="aot-compilation-options"></a>Aot 編譯選項

有數個不同的選項時啟用 AOT 編譯的 Xamarin.Mac 應用程式上可調整：

- `none` -無 AOT 編譯。 這是預設設定。
- `all` -AOT 編譯 MonoBundle 中的每個組件。
- `core` -AOT 編譯`Xamarin.Mac`，`System`和`mscorlib`組件。
- `sdk` -AOT 編譯`Xamarin.Mac`和基底類別程式庫 (BCL) 組件。
- `|hybrid` -新增這其中一個以上的選項可讓混合式 AOT 可允許 IL 移除，但會導致再編譯時間。
- `+` -包含在單一 AOT 編譯。
- `-` -將單一檔案移除 AOT 編譯。

比方說，`--aot:all,-MyAssembly.dll`會啟用所有 MonoBundle 中的組件的 AOT 編譯_除了_`MyAssembly.dll`並`--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll`會啟用混合式、 程式碼 AOT 包含`MyOtherAssembly.dll`和排除`mscorlib.dll`.

## <a name="partial-static-registrar"></a>部分的靜態註冊機構

當開發完成變更，並加以測試之間的時間降至最低的 Xamarin.Mac 應用程式可能會變得重要擅開發。 策略模組化的程式碼基底，單元測試有助於縮短編譯時間，因為它們會減少應用程式將會需要耗費資源的完整重建的次數。

此外，新增至 Xamarin.Mac_部分的靜態註冊機構_（如首創的 Xamarin.iOS） 可大幅減少在 Xamarin.Mac 應用程式的啟動時間**偵錯**組態。 了解如何使用部分的靜態註冊機構可以夾出幾乎 5 倍的改善偵錯啟動將會需要進行的背景上註冊機構是什麼、 的差異在於靜態和動態、 之間和此 「 部分靜態 」 版本的功能。

### <a name="about-the-registrar"></a>關於在註冊機構

任何 Xamarin.Mac 應用程式會位於 Apple 和 OBJECTIVE-C 執行階段的 Cocoa 架構。 建置此 「 原生 world 」 與 「 受控 」 的世界之間的橋樑C#是 Xamarin.Mac 的主要責任。 此工作的一部分由內執行的註冊機構`NSApplication.Init ()`方法。 這是任何使用中 Xamarin.Mac 的 Cocoa Api 需要的其中一個原因`NSApplication.Init`第一次呼叫。

註冊機構的工作就是要通知的應用程式是否存在的 Objective C 執行階段C#類別，例如衍生自類別`NSApplicationDelegate`， `NSView`， `NSWindow`，和`NSObject`。 這需要應用程式中的所有類型，以判斷哪些項目註冊和報表的每個型別上的哪些元素的掃描。

這項掃描可**動態**，在啟動應用程式使用反映，或**靜態**，做為建置時間步驟。 當挑選註冊型別，開發人員應該注意下列幾點：

- 靜態註冊可大幅減少啟動時間，但可以明顯變慢的組建時間 （通常超過兩倍的偵錯組建時間）。 這是預設值**發行**組態則會建置。
- 動態註冊延遲，這項工作直到應用程式啟動，並略過程式碼產生，但此額外工作造成明顯的暫停 （至少兩秒） 中啟動應用程式。 這是特別明顯，在偵錯組態的組建，而其預設值來動態登錄其反映較慢。

部分的靜態註冊，在 Xamarin.iOS 8.13 首次引進，讓開發人員兩者之優點。 藉由預先計算的每個項目中的註冊資訊`Xamarin.Mac.dll`和傳送這項資訊搭配 Xamarin.Mac 的靜態程式庫 （也就只需要在建置時連結到），Microsoft 已移除大部分動態反映情況同時也不會影響建置時間的註冊機構。

### <a name="enabling-the-partial-static-registrar"></a>啟用部分的靜態註冊機構

部分的靜態註冊機構會啟用 Xamarin.Mac 中按兩下**專案名稱**中**方案總管**，請巡覽至**Mac 組建**並加入`--registrar:static`要**其他 mmp 引數：** 欄位。 例如: 

![將部分的靜態註冊機構新增至 其他 mmp 引數](how-it-works-images/psr01.png "將部分的靜態註冊機構新增至 其他 mmp 引數")

## <a name="additional-resources"></a>其他資源

以下是一些更詳細的說明的項目在內部運作的方式：

- [OBJECTIVE-C 選取器](~/ios/internals/objective-c-selectors.md)
- [登錄器](~/ios/internals/registrar.md)
- [適用於 iOS 和 OS X 的 Xamarin 統一 API](~/cross-platform/macios/unified/index.md)
- [Theading 基本概念](~/ios/app-fundamentals/threading.md)
- [委派、 通訊協定，以及事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [關於 `newrefcount`](~/ios/internals/newrefcount.md)


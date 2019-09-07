---
title: Xamarin.Mac 的運作方式
description: 本檔說明 Xamarin 的內部運作。 特別是，它會查看在編譯時間和註冊機構之前的「構造器」、「記憶體管理」。
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 05/25/2017
ms.openlocfilehash: 24ddd71fe1468edc70ec4d487dc2cb2dbd4da1b6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769816"
---
# <a name="how-xamarinmac-works"></a>Xamarin.Mac 的運作方式

在大部分的情況下，開發人員永遠都不需要擔心 Xamarin 的內部「魔術」，不過，若要深入瞭解專案的運作方式，可以使用C#鏡頭和調試功能來解讀現有的檔發生問題。

在 Xamarin. Mac 中，應用程式會橋接兩個領域：以目標 C 為基礎的執行時間包含原生類別（`NSString`、 `NSApplication`等）的實例，而且有C#執行時間包含 managed 類別（`System.String`、 `HttpClient`等）的實例。 在這兩個領域之間，Xamarin 會建立雙向橋接器，讓應用程式可以呼叫目標 c 中的方法（選取器）（例如`NSApplication.Init`），而目標-c 可以呼叫應用程式的C#方法（例如應用程式委派上的方法）。 一般來說，對目標-C 的呼叫會透過**P/invoke**和 Xamarin 提供的一些執行時間程式碼，以透明的方式處理。

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>將C#類別/方法公開至目標-C

不過，若要讓客觀呼叫應用程式的C#物件，必須以目標-c 能夠瞭解的方式來公開。 這會透過`Register`和`Export`屬性來完成。 請使用以下範例：

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

在此範例中，目標-C 執行時間現在會知道名`MyClass`為的類別，以及名`run` `init`為和的選取器。

在大部分情況下，這是開發人員可以忽略的執行詳細資料，因為應用程式接收到的大部分回呼，都是透過`base`類別上的覆`AppDelegate`寫方法`DataSources`（例如、 `Delegates`、）或**動作**來進行傳入 Api。 在所有這些情況下， `Export`程式C#代碼中都不需要屬性。

## <a name="constructor-runthrough"></a>流程示範的構造函式

在許多情況下，開發人員必須將應用程式的C#類別結構 API 公開給目標-C 執行時間，以便從像是在分鏡腳本或 XIB 檔中呼叫時的位置來具現化。 以下是 Xamarin. Mac 應用程式中使用的五個最常見的函式：

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

一般而言，開發人員應該保留建立一些`IntPtr`類型`NSCoder`時所產生的和函式，例如「自`NSViews`定義」。 如果 Xamarin 需要呼叫其中一個函式來回應目標-C 執行時間要求，而且您已將它移除，應用程式將會在機器碼內部損毀，而且可能很難以找出問題的確切原因。

## <a name="memory-management-and-cycles"></a>記憶體管理和週期

Xamarin 中的記憶體管理的方式與 Xamarin 非常類似。 這也是一個複雜的主題，但不在本檔的討論範圍內。 請閱讀[記憶體和效能最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)。

## <a name="ahead-of-time-compilation"></a>先行編譯

一般而言，.NET 應用程式在建立時不會向下編譯至機器碼，而是會編譯成稱為 IL 程式碼的中繼層，以在應用程式啟動時，_即時_（JIT）編譯至機器碼。

當需要產生必要的機器程式碼時，mono 執行時間使用 JIT 編譯此機器碼的時間可能會使 Xamarin. Mac 應用程式的啟動速度變慢最多 20%。

由於 Apple on iOS 所加諸的限制，所以在 Xamarin 中無法使用 IL 程式碼的 JIT 編譯。 如此一來，所有的 Xamarin iOS 應用程式都會在組建週期期間，將完整_的預先_（AOT）編譯成機器碼。

Xamarin 的新功能是能夠在應用程式組建週期中進行 IL 程式碼的 AOT，就像像是 Xamarin 一樣。 Xamarin 會使用_混合_式 AOT 方法來編譯大部分需要的機器程式碼，但允許執行時間編譯所需的 trampolines，以及可繼續支援反映的彈性。發出（以及其他目前處理的使用案例Xamarin. Mac）。

有兩個主要區域，AOT 可以協助 Xamarin. Mac 應用程式：

- **更好的「原生」損毀記錄**-如果 Xamarin. Mac 應用程式在機器碼中當機，這是對 Cocoa api 進行無效呼叫（例如`null` ，將傳送至不接受它的方法）時常見的情況，這是使用 JIT 框架的原生損毀記錄很容易分析。 由於 JIT 畫面格沒有偵錯工具資訊，因此會有多行具有十六進位位移，而且不會有任何線索。 AOT 會產生名為「real」的框架，而追蹤則更容易閱讀。 這也表示 Xamarin 應用程式可與原生工具（例如**lldb**和**儀器**）更好地互動。
- **更好的啟動時間效能**-對於大型 Xamarin. Mac 應用程式，如果有多秒的啟動時間，JIT 編譯所有程式碼可能需要相當長的時間。 AOT 會提前運作。

### <a name="enabling-aot-compilation"></a>啟用 AOT 編譯

在 Xamarin 中啟用 AOT，方法是按兩下 [**方案總管**中的**專案名稱**，流覽至 [Mac] [**組建**] `--aot:[options]` ，然後新增至 **其他 mmp 引數：** ] 欄位（其中`[options]`是一或多個控制 AOT 類型的選項，請參閱下文）。 例如：

![將 AOT 加入至其他 mmp 引數](how-it-works-images/aot01.png "將 AOT 加入至其他 mmp 引數")

> [!IMPORTANT]
> 啟用 AOT 編譯會大幅增加組建時間（有時最多幾分鐘），但它可以改善應用程式啟動時間，平均為 20%。 因此，應該只在 Xamarin. Mac 應用程式的**發行**組建上啟用 AOT 編譯。

### <a name="aot-compilation-options"></a>Aot 編譯選項

在 Xamarin. Mac 應用程式上啟用 AOT 編譯時，有幾個不同的選項可以調整：

- `none`-沒有 AOT 編譯。 這是預設設定。
- `all`-AOT 會編譯 MonoBundle 中的每個元件。
- `core`-AOT 會編譯`Xamarin.Mac`、 `System`和`mscorlib`元件。
- `sdk`-AOT 會編譯`Xamarin.Mac`和基類程式庫（BCL）元件。
- `|hybrid`-將此加入至上述其中一個選項會啟用混合式 AOT，允許 IL 去除，但會導致編譯時間較長。
- `+`-包含適用于 AOT 編譯的單一檔案。
- `-`-從 AOT 編譯中移除單一檔案。

例如`--aot:all,-MyAssembly.dll` ，會在 MonoBundle 中的所有元件上啟用 AOT 編譯，_但_ `MyAssembly.dll` `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll`會`mscorlib.dll`啟用混合式，程式碼 AOT 會包含`MyOtherAssembly.dll`和（不包括）。

## <a name="partial-static-registrar"></a>部分靜態註冊機構

開發 Xamarin. Mac 應用程式時，將完成變更和測試之間的時間降到最低，可能會變得很重要，以符合開發期限。 模組化程式碼基底和單元測試等策略有助於減少編譯時間，因為它們會減少應用程式需要昂貴的完整重建的次數。

此外，除了 xamarin 的新功能之外，_部分靜態註冊機構_（如 xamarin 的推出）可以大幅減少在**Debug**設定中的 xamarin 應用程式的啟動時間。 瞭解使用部分靜態註冊機構如何在 debug 啟動中排除幾乎5倍的改進，會對註冊機構的功能有一點的背景、靜態與動態之間的差異，以及此「部分靜態」版本的作用。

### <a name="about-the-registrar"></a>關於註冊機構

在任何 Xamarin 應用程式的幕後，都是來自 Apple 和目標-C 執行時間的 Cocoa 架構。 在此「原生世界」和「受管理的世界」之間建立C#橋樑是 Xamarin 的主要責任。 這項工作的一部分是由註冊機構（在方法內`NSApplication.Init ()`執行）所處理。 這是在 Xamarin. Mac `NSApplication.Init`中使用 Cocoa api 的其中一個原因，必須先呼叫。

註冊機構的工作是要通知目標-C 執行時間是否存在應用C#程式的類別（衍生自`NSApplicationDelegate`、、 `NSWindow`和`NSObject`等類別） `NSView`。 這需要掃描應用程式中的所有類型，以判斷需要註冊的專案，以及要報告之每個類型上的元素。

這項掃描可以在使用反映的應用程式啟動時**動態**完成，或以**靜態**方式在組建階段步驟中執行。 在挑選註冊類型時，開發人員應該注意下列事項：

- 靜態註冊可以大幅縮短啟動時間，但可能會大幅降低組建的時間（通常是超過兩次的 debug 組建時間）。 這會是**發行**設定組建的預設值。
- 動態登錄會延遲這項工作，直到應用程式啟動和略過程式碼產生為止，但這項額外的工作可能會在應用程式啟動時產生明顯的暫停（至少兩秒鐘）。 這在 debug 設定組建中特別明顯，預設為動態登錄，而且其反映速度較慢。

部分靜態註冊，第一次在 Xamarin. iOS 8.13 中引進，為開發人員提供這兩個選項的最佳選擇。 藉由在靜態程式庫中預先計算每個`Xamarin.Mac.dll`專案的註冊資訊，並將這項資訊傳送至 Xamarin. Mac （這只需要在組建階段連結到），Microsoft 已移除大部分動態的反映時間註冊機構不會影響組建時間。

### <a name="enabling-the-partial-static-registrar"></a>啟用部分靜態註冊機構

在 Xamarin 中啟用部分靜態註冊機構，方法是按兩下 **方案總管**中的**專案名稱**，流覽至 Mac **組建** `--registrar:static` ，然後新增至 **其他 mmp 引數：**  欄位。 例如：

![將部分靜態註冊機構新增至其他 mmp 引數](how-it-works-images/psr01.png "將部分靜態註冊機構新增至其他 mmp 引數")

## <a name="additional-resources"></a>其他資源

以下是一些如何在內部運作的詳細說明：

- [目標-C 選取器](~/ios/internals/objective-c-selectors.md)
- [登錄器](~/ios/internals/registrar.md)
- [適用于 iOS 和 OS X 的 Xamarin Unified API](~/cross-platform/macios/unified/index.md)
- [Theading 基本概念](~/ios/app-fundamentals/threading.md)
- [委派、通訊協定和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [有關`newrefcount`](~/ios/internals/newrefcount.md)

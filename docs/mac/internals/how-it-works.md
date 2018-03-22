---
title: "Xamarin.Mac 的運作方式"
description: "本文件說明 Xamarin.Mac 的內部工作。 特別是，它會查看建構函式、 預先時間編譯的記憶體管理和註冊機構。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/25/2017
ms.openlocfilehash: a1dbff32b113bd1c3a6b2058a34c73977c59c9e5
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="how-xamarinmac-works"></a>Xamarin.Mac 的運作方式

大部分的情況下，開發人員不必擔心內部"magic"的 Xamarin.Mac，不過，有的項目實際上的運作方式如何協助解譯與 C# 功能濾鏡的現有文件和偵錯問題中概略了解時系統發生。

Xamarin.Mac，在應用程式等兩個領域： 沒有包含原生類別的執行個體 Objective C 執行階段 (`NSString`，`NSApplication`等等)，而且沒有包含 managed 類別的執行個體的 C# 執行階段 (`System.String`， `HttpClient`等等)。 這些兩者之間 Xamarin.Mac 建立雙向橋接器讓應用程式可以呼叫 Objective C 中的方法 （選取器） (例如`NSApplication.Init`) 及 OBJECTIVE-C 可以呼叫應用程式的 C# 方法回 （例如上的應用程式委派的方法）。 一般情況下，Objective C 的呼叫都會透明處理透過**P/Invokes**和 Xamarin 提供一些執行階段程式碼。

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>將 C# 類別公開 / Objective C 的方法

不過，OBJECTIVE-C 適用的回呼至應用程式的 C# 物件，它們需要公開可了解 OBJECTIVE-C 的方式。 這是透過`Register`和`Export`屬性。 請使用以下範例：

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

在此範例中，Objective C 執行階段現在會了解類別，稱為`MyClass`與呼叫選取器`init`和`run`。

在大部分情況下，這是實作詳細資料，開發人員可以忽略，因為大部分應用程式收到的回呼會透過覆寫方法上`base`類別 (例如`AppDelegate`， `Delegates`， `DataSources`) 或在**動作**傳遞至應用程式開發介面。 在所有這些情況下，`Export`屬性時，不需要在 C# 程式碼。

## <a name="constructor-runthrough"></a>建構函式流程示範

在許多情況下，開發人員必須公開應用程式的 C# 類別建構 API Objective C 執行階段，以便它可以具現化的位置例如分鏡腳本或 XIB 檔案中呼叫時。 以下是五個最常見的建構函式 Xamarin.Mac 應用程式中使用：

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

一般情況下，開發人員應保留`IntPtr`和`NSCoder`建立某些類型，例如自訂時，所產生的建構函式`NSViews`單獨。 如果 Xamarin.Mac 需要呼叫這些建構函式的其中一個 Objective C 執行階段要求的回應，而且您將它移除，原生程式碼內的應用程式將會損毀，可能會難以找出完全問題。

## <a name="memory-management-and-cycles"></a>記憶體管理和循環

Xamarin.Mac 中的記憶體管理是在許多方面與 Xamarin.iOS 非常類似。 它也是一個複雜的主題，其中一個超出本文的範圍。 請閱讀[記憶體和效能最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)。

## <a name="ahead-of-time-compilation"></a>繼續時間編譯的

一般而言，會在建置時不.NET 應用程式編譯到機器碼，而是會編譯呼叫 IL 程式碼，可取得中繼層_時間恰好_應用程式啟動時 (JIT) 編譯成機器碼。

JIT 編譯單聲道的執行階段所花費的時間此機器的程式碼可能會降低 Xamarin.Mac 應用程式的啟動最高達 20%，所需的必要的電腦程式碼會產生時間。

由於在 iOS 上的 Apple 所加諸的限制，JIT 編譯的 IL 程式碼不適用於 Xamarin.iOS。 如此一來，所有 Xamarin.iOS 應用程式都都填滿_Ahead 的時間_(AOT) 建置循環期間編譯成機器碼。

新 Xamarin.Mac 是 AOT 能力 IL 程式碼在應用程式建置週期中，就像 Xamarin.iOS 可以。 使用 Xamarin.Mac_混合式_AOT 方法進行編譯所需的電腦程式碼中，大部分的但可讓執行階段編譯所需的 trampolines 和彈性地繼續支援這些事件處理常式 （及其他使用案例的目前在工作 Xamarin.Mac）。

有兩個 AOT 可以幫助 Xamarin.Mac 應用程式的主要區域：

- **更好的 「 原生 」 的當機記錄檔**-如果 Xamarin.Mac 應用程式當機狀況一般發生時進行 Cocoa Api 的無效呼叫的原生程式碼 (例如傳送`null`成不接受的方法)、 原生 JIT 的記錄檔損毀框架很難進行分析。 由於 JIT 框架沒有偵錯資訊，將會具有十六進位的位移的多行，在沒有線索。 AOT 會產生 「 實際 」 具名的框架，而且更容易讀取追蹤。 這也表示 Xamarin.Mac 應用程式會更好與互動的原生工具例如**lldb**和**儀器**。
- **更好的啟動時間效能**-針對大型 Xamarin.Mac 應用程式，使用多個第二個啟動時間，JIT 編譯程式碼的所有可能需要很長的時間。 AOT 執行此工作。

### <a name="enabling-aot-compilation"></a>啟用 AOT 編譯

AOT Xamarin.Mac 中啟用按兩下**專案名稱**中**方案總管 中**、 瀏覽至**Mac 建置**並加入`--aot:[options]`至**其他 mmp 引數：**欄位 (其中`[options]`為一或多個選項控制 AOT 類型，如下所示)。 例如: 

![加入其他 mmp 引數的 AOT](how-it-works-images/aot01.png "加入 AOT 其他 mmp 引數")

> [!IMPORTANT]
> 啟用 AOT 編譯大幅增加建置時，有時長達數分鐘，但它可以改善應用程式啟動時間是平均的 20%。 如此一來，AOT 編譯，才應該啟用上**發行**Xamarin.Mac 應用程式的組建。

### <a name="aot-compilation-options"></a>Aot 編譯選項

有數個不同的選項時啟用 AOT 編譯 Xamarin.Mac 應用程式上的可調整：

- `none` -沒有 AOT 編譯。 這是預設設定。
- `all` -AOT 編譯 MonoBundle 中的每個組件。
- `core` -AOT 編譯`Xamarin.Mac`，`System`和`mscorlib`組件。
- `sdk` -AOT 編譯`Xamarin.Mac`和基底類別程式庫 (BCL) 組件。
- `|hybrid` -新增上述選項的其中一個這可讓混合式 AOT 允許 IL 條狀配置，但會導致再對進行編譯時間。
- `+` -包含 AOT 編譯為單一。
- `-` -將單一檔案移除 AOT 編譯。

例如，`--aot:all,-MyAssembly.dll`會啟用所有 MonoBundle 中的組件上的 AOT 編譯_除了_`MyAssembly.dll`和`--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll`會啟用混合式，程式碼 AOT 包含`MyOtherAssembly.dll`並排除`mscorlib.dll`.

## <a name="partial-static-registrar"></a>部分的靜態註冊機構

當開發 Xamarin.Mac 應用程式，最小化完成變更並加以測試之間的時間會變得重要會議開發期限。 策略的模組程式碼基底及單元測試可協助減少編譯時間，它們會減少應用程式將會需要耗費資源的完整重建的次數。

此外，新增至 Xamarin.Mac，_部分的靜態註冊機構_（如 pioneered 由 Xamarin.iOS) 可以大幅減少在 Xamarin.Mac 應用程式的啟動時間**偵錯**組態。 了解如何使用部分的靜態註冊機構可以夾出幾乎 5 倍中啟動偵錯將進行的註冊機構的是靜態和動態、 之間有何差異以及"部分的靜態 「 本版用途的背景。

### <a name="about-the-registrar"></a>有關在註冊機構

任何 Xamarin.Mac 實際上應用程式必須從 Apple 和 Objective C 執行階段 Cocoa 架構。 建置此"原生 world"和"managed 的 world"的 C# 之間的橋樑是 Xamarin.Mac 的主要責任。 此工作的一部分由執行內的註冊機構`NSApplication.Init ()`方法。 這是任何使用中 Xamarin.Mac Cocoa Api 需要的一個原因`NSApplication.Init`第一次呼叫。

註冊機構的作業是通知 Objective C 執行階段的應用程式的 C# 類別，例如衍生自類別存在`NSApplicationDelegate`， `NSView`， `NSWindow`，和`NSObject`。 這需要應用程式中的所有類型，以判斷需要註冊和哪些項目至報表的每個類型的掃描。

這項掃描要完成可**動態**，在反映中，應用程式啟動或**靜態**，做為建置時間步驟。 當挑選註冊型別，開發人員應該注意下列幾點：

- 靜態註冊可大幅減少啟動時間，但可以明顯變慢組建的時間 （通常會超過 double 偵錯建置時間）。 這將會成為預設**發行**組建組態。
- 動態註冊延遲此工作啟動應用程式，並略過產生程式碼，但此額外的工作可以建立明顯暫停 （至少兩秒） 中啟動應用程式。 這是在偵錯組態的組建，特別明顯預設為動態登錄和其 reflection 相當慢。

部分的靜態註冊、 Xamarin.iOS 8.13 中首度引進，讓開發人員最棒的是這兩個選項。 藉由預先計算的每個項目中的註冊資訊`Xamarin.Mac.dll`和傳送此資訊與 Xamarin.Mac 中的靜態程式庫 （只需要在建置期間，與連結），Microsoft 已經移除大部分的動態反映時間同時也不影響建置時間的註冊機構。

### <a name="enabling-the-partial-static-registrar"></a>啟用部分的靜態註冊機構

部分的靜態註冊機構 Xamarin.Mac 中啟用按兩下**專案名稱**中**方案總管 中**、 瀏覽至**Mac 建置**並加入`--registrar:static`至**其他 mmp 引數：**欄位。 例如: 

![加入其他 mmp 引數，部分的靜態註冊機構](how-it-works-images/psr01.png "部分的靜態註冊機構加入其他 mmp 引數")

## <a name="additional-resources"></a>其他資源

以下是一些更詳細的說明的項目在內部運作的方式：

- [Objective C 的選取器](~/ios/internals/objective-c-selectors.md)
- [登錄器](~/ios/internals/registrar.md)
- [適用於 iOS 和 OS X 的 Xamarin 統一的 API](~/cross-platform/macios/unified/index.md)
- [Theading 基本概念](~/ios/app-fundamentals/threading.md)
- [委派、 通訊協定和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [關於 `newrefcount`](~/ios/internals/newrefcount.md)


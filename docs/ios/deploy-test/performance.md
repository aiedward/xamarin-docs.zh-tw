---
title: Xamarin.iOS 效能
description: 本文件說明在 Xamarin.iOS 應用程式中用來改善效能和記憶體使用量的技術。
ms.prod: xamarin
ms.assetid: 02b1f628-52d9-49de-8479-f2696546ca3f
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/29/2016
ms.openlocfilehash: 06d413b2bf07df38f78e93af027de2c7dc0badcc
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436775"
---
# <a name="xamarinios-performance"></a>Xamarin.iOS 效能

不佳的應用程式效能會以許多方式表現。 它可能會讓應用程式看起來沒有回應、造成捲動緩慢，以及減少電池壽命。 不過，最佳化效能不僅僅只牽涉到實作有效率的程式碼而已。 同時也必須考量使用者對於應用程式效能的體驗。 例如，確保作業能在不封鎖使用者執行其他活動的情況下執行，將可以協助改善使用者體驗。

本文件說明在 Xamarin.iOS 應用程式中用來改善效能和記憶體使用量的技術。

> [!NOTE]
>  在閱讀本文之前，您應該先閱讀[跨平台效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)，其中討論了非平台的特定技術來改善記憶體使用量和使用 Xamarin 平台建置之應用程式的效能。

## <a name="avoid-strong-circular-references"></a>避免強式循環參考

在某些情況下，可能會建立強式參考循環，這會防止記憶體回收行程回收物件的記憶體。 例如，假設衍生的子類別（ [`NSObject`](xref:Foundation.NSObject) 例如繼承自的類別） [`UIView`](xref:UIKit.UIView) 新增至 `NSObject` 衍生的容器，並從目標 C 強式參考，如下列程式碼範例所示：

```csharp
class Container : UIView
{
    public void Poke ()
    {
    // Call this method to poke this object
    }
}

class MyView : UIView
{
    Container parent;
    public MyView (Container parent)
    {
        this.parent = parent;
    }

    void PokeParent ()
    {
        parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

當此程式碼建立 `Container` 執行個體時，C# 物件將有 Objective-C 物件的強式參考。 同樣地，`MyView` 執行個體也會有 Objective-C 物件的強式參考。

此外，呼叫 `container.AddSubview` 將會增加非受控 `MyView` 執行個體上的參考計數。 當發生這種情況時，Xamarin.iOS 執行階段會建立 `GCHandle` 執行個體以保留受控程式碼中的 `MyView` 運作，因為無法保證任何受控物件會保留其參考。 從 managed 程式碼的觀點來看， `MyView` 物件會在呼叫不是的情況下回收 [`AddSubview`](xref:UIKit.UIView.AddSubview(UIKit.UIView)) `GCHandle` 。

非受控 `MyView` 物件會有指向受控物件的 `GCHandle`，稱為*強式連結*。 受控物件將包含對 `Container` 執行個體的參考。 接著，`Container` 執行個體會有對 `MyView` 物件的受控參考。

在包含之物件保留其容器連結的情況下，有幾個選項可用來處理循環參考：

- 手動中斷循環，方法是將容器連結設定為 `null`。
- 手動從容器移除所包含的物件。
- 呼叫物件上的 `Dispose`。
- 避免循環參考保留容器的弱式參考。 如需弱式參考的相關詳細資訊。

### <a name="using-weakreferences"></a>使用 WeakReferences

避免循環的一種方式是從子項到父項的弱式參考。 例如，上述程式碼可以寫成這樣：

```csharp
class Container : UIView
{
    public void Poke ()
    {
        // Call this method to poke this object
    }
}

class MyView : UIView
{
    WeakReference<Container> weakParent;
    public MyView (Container parent)
    {
        this.weakParent = new WeakReference<Container> (parent);
    }

    void PokeParent ()
    {
        if (weakParent.TryGetTarget (out var parent))
            parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

在此，所包含的物件不讓父項保持運作。 但是，父項透過對 `container.AddSubView` 的呼叫保持子項運作。

這也會發生在使用委派或資料來源模式的 iOS Api 中，其中對等類別包含執行。例如，在設定 [`Delegate`](xref:UIKit.UITableView.Delegate*)
屬性或 [`DataSource`](xref:UIKit.UITableView.DataSource*)
在 [`UITableView`](xref:UIKit.UITableView) 類別中。

如果只為了執行通訊協定而建立的類別，例如 [`IUITableViewDataSource`](xref:UIKit.IUITableViewDataSource) ，您可以直接在類別中執行介面，並覆寫方法，並將屬性指派給，而不是建立子類別 `DataSource` `this` 。

#### <a name="weak-attribute"></a>弱式屬性

[Xamarin.iOS 11.10](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_11/xamarin.ios_11.10.md#WeakAttribute) 引入 `[Weak]` 屬性。 例如 `WeakReference <T>`，`[Weak]` 可用來中斷[強式循環參考](#avoid-strong-circular-references)，但使用更少的程式碼。

請考慮下列使用 `WeakReference <T>` 的程式碼：

```csharp
public class MyFooDelegate : FooDelegate {
    WeakReference<MyViewController> controller;
    public MyFooDelegate (MyViewController ctrl) => controller = new WeakReference<MyViewController> (ctrl);
    public void CallDoSomething ()
    {
        MyViewController ctrl;
        if (controller.TryGetTarget (out ctrl)) {
            ctrl.DoSomething ();
        }
    }
}
```

使用 `[Weak]` 的對等程式碼更為精簡：

```csharp
public class MyFooDelegate : FooDelegate {
    [Weak] MyViewController controller;
    public MyFooDelegate (MyViewController ctrl) => controller = ctrl;
    public void CallDoSomething () => controller.DoSomething ();
}
```

以下是另一個在[委派](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html)模式內容中使用 `[Weak]` 的範例：

```csharp
public class MyViewController : UIViewController
{
    WKWebView webView;

    protected MyViewController (IntPtr handle) : base (handle) { }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        webView = new WKWebView (View.Bounds, new WKWebViewConfiguration ());
        webView.UIDelegate = new UIDelegate (this);
        View.AddSubview (webView);
    }
}

public class UIDelegate : WKUIDelegate
{
    [Weak] MyViewController controller;

    public UIDelegate (MyViewController ctrl) => controller = ctrl;

    public override void RunJavaScriptAlertPanel (WKWebView webView, string message, WKFrameInfo frame, Action completionHandler)
    {
        var msg = $"Hello from: {controller.Title}";
        var alertController = UIAlertController.Create (null, msg, UIAlertControllerStyle.Alert);
        alertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
        controller.PresentViewController (alertController, true, null);
        completionHandler ();
    }
}
```

### <a name="disposing-of-objects-with-strong-references"></a>處置包含強式參考的物件

如果存在強式參考且難以移除該相依性，請使 `Dispose` 方法清除父代指標。

針對容器，覆寫 `Dispose` 方法以移除包含的物件，如下列程式碼範例所示：

```csharp
class MyContainer : UIView
{
    public override void Dispose ()
    {
        // Brute force, remove everything
        foreach (var view in Subviews)
        {
              view.RemoveFromSuperview ();
        }
        base.Dispose ();
    }
}
```

針對保留其父代強式參考的子物件，請清除 `Dispose` 實作中對父代的參考：

```csharp
class MyChild : UIView
{
    MyContainer container;
    public MyChild (MyContainer container)
    {
        this.container = container;
    }
    public override void Dispose ()
    {
        container = null;
    }
}
```

如需有關釋放強式參考的詳細資訊，請參閱[釋放 IDisposable 資源](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable)。
此部落格文章中也有很棒的探討：[Xamarin.iOS、記憶體回收行程與我](https://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me) \(英文\)。

### <a name="more-information"></a>詳細資訊

如需詳細資訊，請參閱 Cocoa With Love 上的[避免保留循環的規則](https://www.cocoawithlove.com/2009/07/rules-to-avoid-retain-cycles.html) \(英文\)、StackOverflow 上的[這是 MonoTouch GC 中的錯誤嗎](https://stackoverflow.com/questions/13058521/is-this-a-bug-in-monotouch-gc) \(英文\)，以及 StackOverflow 上的[為什麼 MonoTouch GC 無法終止 refcount > 1 的受控物件？](https://stackoverflow.com/questions/13064669/why-cant-monotouch-gc-kill-managed-objects-with-refcount-1) \(英文\)。

## <a name="optimize-table-views"></a>最佳化資料表檢視

使用者預期實例會有順暢的滾動和快速載入時間 [`UITableView`](xref:UIKit.UITableView) 。 不過，當資料格包含深層巢狀檢視階層，或資料格包含複雜配置時，捲動效能可能會受到影響。 不過，有可用來避免 `UITableView` 效能不佳的技巧：

- 重複使用資料格。 如需詳細資訊，請參閱[重複使用資料格](#reuse-cells)。
- 減少子檢視數目。
- 快取擷取自 Web 服務的資料格內容。
- 快取任何資料列的高度 (如果不相同)。
- 讓資料格和任何其他檢視不透明。
- 避免影像縮放和漸層。

這些技術統稱可協助保持 [`UITableView`](xref:UIKit.UITableView) 實例順暢地滾動。

### <a name="reuse-cells"></a>重複使用資料格

當您在中顯示數百個數據列時 [`UITableView`](xref:UIKit.UITableView) ，可能會浪費記憶體來建立數百個 [`UITableViewCell`](xref:UIKit.UITableViewCell) 物件（當畫面上只顯示少量的物件時）。 反之，僅有畫面上看見的資料格可以載入記憶體，且**內容**載入至這些重複使用的資料格中。 這可防止具現化數百個額外的物件，進而節省時間與記憶體。

因此，當資料格從畫面消失時，其檢視可以放置在佇列中以供重複使用，如下列程式碼範例所示：

```csharp
class MyTableSource : UITableViewSource
{
    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        // iOS will create a cell automatically if one isn't available in the reuse pool
        var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

        // Perform required cell actions
        return cell;
    }
}
```

當使用者滾動時，會 [`UITableView`](xref:UIKit.UITableView) 呼叫覆 `GetCell` 寫來要求新的視圖顯示。 然後，此覆寫會呼叫 [`DequeueReusableCell`](xref:UIKit.UITableView.DequeueReusableCell(Foundation.NSString)) 方法，而且如果資料格可供重複使用，則會傳回。

如需詳細資訊，請參閱[使用資料填入資料表](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)中的[資料格重複使用](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)。

## <a name="use-opaque-views"></a>使用不透明檢視

確定任何未定義透明度的視圖都會 [`Opaque`](xref:UIKit.UIView.Opaque) 設定其屬性。 這可確保檢視由繪製系統以最佳方式呈現。 當視圖內嵌于或複雜動畫的一部分時，這一點特別重要 [`UIScrollView`](xref:UIKit.UIScrollView) 。 否則繪製系統會合成檢視與其他內容，這可能會明顯影響效能。

## <a name="avoid-fat-xibs"></a>避免 FAT XIB

雖然 XIB 已大部分由分鏡腳本取代，仍有一些情況會使用 XIB。 當 XIB 載入記憶體時，它的所有內容都會載入到記憶體中，包括任何影像。 如果 XIB 包含並非立即要使用的檢視，則會浪費記憶體。 因此，當使用 XIB 時，請確定每個檢視控制器都只有一個 XIB，可能的話，將檢視控制器的檢視階層架構分為個別 XIB。

## <a name="optimize-image-resources"></a>最佳化影像資源

影像是一些應用程式所使用成本最高的資源，且經常以高解析度擷取。 因此，在中顯示來自應用程式配套的影像時 [`UIImageView`](xref:UIKit.UIImageView) ，請確定影像和 `UIImageView` 大小都相同。 在執行時間調整影像可能是昂貴的作業，尤其 `UIImageView` 是當內嵌在中時 [`UIScrollView`](xref:UIKit.UIScrollView) 。

如需詳細資訊，請參閱[跨平台效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)指南中的[最佳化影像資源](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)。

## <a name="test-on-devices"></a>在裝置上進行測試

盡早在實體裝置上開始部署及測試應用程式。 模擬器無法完全符合裝置的行為和限制，因此務必盡早在實際裝置上進行測試。

特別是模擬器並不以任何方式模擬實體裝置的記憶體或 CPU 限制。

## <a name="synchronize-animations-with-the-display-refresh"></a>使用顯示重新整理來同步處理動畫

遊戲通常具有緊密迴圈，以執行遊戲邏輯和更新畫面。 常見的畫面播放速率，範圍從每秒 30 畫面格數到每秒 60 畫面格數。 有些開發人員會覺得他們每秒應該盡可能更新畫面，結合他們的遊戲模擬與畫面更新，且可能會想要超越每秒 60 畫面格數。

不過，顯示伺服器執行畫面更新的上限為每秒 60 次。 因此，嘗試透過比此限制更快的速度更新畫面，可能會造成畫面撕裂和微間斷的情形。 所以最好先將程式碼結構化，這樣一來畫面更新才能與顯示更新同步處理。 您可以使用類別來達成此目的 [`CoreAnimation.CADisplayLink`](xref:CoreAnimation.CADisplayLink) ，這是一種計時器，適用于在每秒60畫面格執行的視覺效果和遊戲。

## <a name="avoid-core-animation-transparency"></a>避免核心動畫透明度

避免核心動畫透明度可改善點陣圖合成 (Compositing) 效能。 一般情況下，盡可能避免透明的圖層與套用模糊效果的框線。

## <a name="avoid-code-generation"></a>避免程式碼產生

必須避免使用 `System.Reflection.Emit` 或 *Dynamic Language Runtime* 以動態方式產生程式碼，因為 iOS 核心會防止動態程式碼執行。

## <a name="summary"></a>摘要

本文章會說明與討論用來增加以 Xamarin.iOS 建置之應用程式效能的技巧。 這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。

## <a name="related-links"></a>相關連結

- [跨平臺效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)
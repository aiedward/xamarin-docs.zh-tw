---
title: Xamarin Designer for iOS 中的自訂控制項
description: Xamarin Designer for iOS 支援轉譯您的專案中建立的自訂控制項, 或從「Xamarin 元件存放區」之類的外部來源參考。
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 3f36ef7541ea00e2a08733c9d47d252477da787a
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227069"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Xamarin Designer for iOS 中的自訂控制項

_Xamarin Designer for iOS 支援轉譯您的專案中建立的自訂控制項, 或從「Xamarin 元件存放區」之類的外部來源參考。_

Xamarin Designer for iOS 是一種功能強大的工具, 可讓您視覺化應用程式的使用者介面, 並為大部分的 iOS 視圖和視圖控制器提供 WYSIWYG 編輯支援。 您的應用程式可能也會包含自訂控制項, 以擴充內建于 iOS 中的控制項。 如果這些自訂控制項是以幾個指導方針寫成, 則 iOS 設計工具也可以轉譯它們, 以提供更豐富的編輯體驗。 本檔將探討這些指導方針。

## <a name="requirements"></a>需求

符合下列所有需求的控制項將會在設計介面上呈現:

1. 它是[UIView](xref:UIKit.UIView)或[UIViewController](xref:UIKit.UIViewController)的直接或間接子類別。 其他[NSObject](xref:Foundation.NSObject)子類別會顯示為設計介面上的圖示。
2. 它有[RegisterAttribute](xref:Foundation.RegisterAttribute)可將它公開給目標-C。
3. 它具有[必要的 IntPtr 函數](~/ios/internals/api-design/index.md)。
4. 它會執行[IComponent](xref:System.ComponentModel.IComponent)介面, 或將[DesignTimeVisibleAttribute](xref:System.ComponentModel.DesignTimeVisibleAttribute)設定為 True。

當程式碼中定義的控制項符合上述需求時, 就會出現在設計工具中, 當其包含的專案是針對模擬器進行編譯時。 根據預設, 所有自訂控制項都會出現在 [**工具箱**] 的 [**自訂群組件**] 區段中。 不過, [CategoryAttribute](xref:System.ComponentModel.CategoryAttribute)可以套用至自訂控制項的類別, 以指定不同的區段。

設計工具不支援載入協力廠商的目標 C 程式庫。

## <a name="custom-properties"></a>自訂內容

如果符合下列條件, 自訂控制項所宣告的屬性就會出現在 [屬性] 面板中:

1. 屬性具有公用 getter 和 setter。
1. 屬性的[ExportAttribute](xref:Foundation.ExportAttribute)和[BrowsableAttribute](xref:System.ComponentModel.BrowsableAttribute)設定為 True。
1. 屬性類型為數數值型別、列舉類型、字串、bool、 [SizeF](xref:System.Drawing.SizeF)、 [UIColor](xref:UIKit.UIColor)或[UIImage](xref:UIKit.UIImage)。 這份支援的類型清單可能會在未來擴充。


屬性也可以使用[DisplayNameAttribute](xref:System.ComponentModel.DisplayNameAttribute)裝飾, 以指定在 [屬性] 面板中顯示的標籤。

## <a name="initialization"></a>初始化

針對`UIViewController`子類別, 您應該使用[ViewDidLoad](xref:UIKit.UIViewController.ViewDidLoad)方法來執行與您在設計工具中建立之瀏覽器相依的程式碼。

對於`UIView`和其他`NSObject`子類別, [AwakeFromNib](xref:Foundation.NSObject.AwakeFromNib)方法是在從配置檔案載入自訂控制項之後, 為其執行初始化的建議位置。 這是因為在執行控制項的函式時, 將不會設定在屬性面板中設定的任何自訂屬性, 但會在`AwakeFromNib`呼叫之前設定它們:


```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        // Initialize the view here.
    }
}
```

如果控制項也是設計為直接從程式碼建立, 您可能會想要建立具有一般初始化程式碼的方法, 如下所示:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
    }
}
```

## <a name="property-initialization-and-awakefromnib"></a>屬性初始化和 AwakeFromNib

請注意, 在自訂群組件中初始化可設計屬性的時機和位置, 以確保不會覆寫 iOS 設計工具內已設定的值。 例如, 請採用下列程式碼:

```csharp
[Register ("CustomView"), DesignTimeVisible (true)]
public class CustomView : UIView {

    [Export ("Counter"), Browsable (true)]
    public int Counter {get; set;}

    public CustomView (IntPtr handle) : base (handle) { }

    public CustomView ()
    {
        // Called when created from code.
        Initialize ();
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        Initialize ();
    }

    void Initialize ()
    {
        // Common initialization code here.
        Counter = 0;
    }
}
```

元件會公開可由開發人員在 iOS 設計工具中設定的屬性。`Counter` `CustomView` 不過, 不論在設計工具內設定的值為何, `Counter`屬性的值一律會是零 (0)。 原因如下：

- 的實例`CustomControl`會從分鏡腳本檔案中放大。
- 在 iOS 設計工具中修改的任何屬性 (例如, 將的值`Counter`設定為二 (2)) 都是如此。
- 會執行`Initialize`方法, 並對元件的方法進行呼叫。 `AwakeFromNib`
- 在`Initialize` `Counter`屬性值中, 將會重設為零 (0)。


若要修正上述情況, 請在其他`Counter`地方初始化屬性 (例如在元件的函式中), 或不要`AwakeFromNib`覆寫方法`Initialize` , 並在元件不需要進一步初始化的範圍之外, 呼叫目前正由其函式處理。

## <a name="design-mode"></a>設計模式

在設計介面上, 自訂控制項必須遵守幾個限制:

- 應用程式套件組合資源無法在設計模式中使用。 透過[UIImage 方法](xref:UIKit.UIImage)載入時, 可以使用影像。
- 非同步作業 (例如 web 要求) 不應該在設計模式中執行。 設計介面不支援動畫或任何其他非同步更新至控制項的 UI。


自訂控制項可以執行[IComponent](xref:System.ComponentModel.IComponent) , 並使用[DesignMode](xref:System.ComponentModel.ISite.DesignMode)屬性來檢查它是否位於設計介面上。 在此範例中, 標籤會在設計介面上顯示「設計模式」, 並在執行時間顯示「執行時間」:

```csharp
[Register ("DesignerAwareLabel")]
public class DesignerAwareLabel : UILabel, IComponent {

    #region IComponent implementation

    public ISite Site { get; set; }
    public event EventHandler Disposed;

    #endregion

    public DesignerAwareLabel (IntPtr handle) : base (handle) { }

    public override void AwakeFromNib ()
    {
        if (Site != null && Site.DesignMode)
            Text = "Design Mode";
        else
            Text = "Runtime";
    }
}
```

在嘗試存取其任何`Site`成員`null`之前, 您應該一律檢查的屬性。 如果`Site` 為`null`, 則可以放心假設控制項不是在設計工具中執行。
在設計模式中`Site` , 會在控制項的「函式」執行之後和`AwakeFromNib`呼叫之前設定。

## <a name="debugging"></a>偵錯

符合上述需求的控制項將會顯示在 [工具箱] 中, 並在介面上呈現。
如果未轉譯控制項, 請檢查控制項中的 bug 或其相依性的其中之一。

設計介面通常可以攔截個別控制項所擲回的例外狀況, 同時繼續呈現其他控制項。 錯誤的控制項會取代成紅色的預留位置, 而您可以按一下驚嘆號圖示來查看例外狀況追蹤:

 ![](ios-designable-controls-overview-images/exception-box.png "做為紅色預留位置和例外狀況詳細資料的錯誤控制項")

如果控制項有可用的偵錯工具符號, 追蹤就會有檔案名和行號。
按兩下堆疊追蹤中的一行, 會跳至原始程式碼中的那一行。

如果設計工具無法隔離錯誤的控制項, 則會在設計介面的頂端顯示警告訊息:

 ![](ios-designable-controls-overview-images/info-bar.png "設計介面頂端的警告訊息")

從設計介面修正或移除錯誤的控制項時, 將會繼續完整轉譯。

## <a name="summary"></a>總結

本文介紹在 iOS 設計工具中建立和應用自訂控制項的方式。 首先, 它會說明控制項必須符合才能在設計介面上轉譯, 並在屬性面板中公開自訂屬性的需求。 然後, 它會查看控制項的程式碼後置初始化和 DesignMode 屬性。 最後, 它會說明當擲回例外狀況時所發生的情況, 以及如何解決此問題。

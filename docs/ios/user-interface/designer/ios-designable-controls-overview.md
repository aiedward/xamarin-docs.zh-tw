---
title: Xamarin Designer for iOS 中的自訂控制項
description: Xamarin Designer for iOS 支援轉譯在您的專案中建立的自訂控制項，或從像是 Xamarin 元件存放區的外部來源參考。
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 593f03588d691071d4a231d9ed788391530d5608
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439419"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>Xamarin Designer for iOS 中的自訂控制項

_Xamarin Designer for iOS 支援轉譯在您的專案中建立的自訂控制項，或從像是 Xamarin 元件存放區的外部來源參考。_

> [!WARNING]
> IOS 設計工具將開始在 Visual Studio 2019 16.8 版和 Visual Studio 2019 for Mac 8.8 版中推出。
> 建立 iOS 使用者介面的建議方式是直接在執行 Xcode 的 Mac 上執行。 如需詳細資訊，請參閱 [使用 Xcode 設計使用者介面](../storyboards/index.md)。 

Xamarin Designer for iOS 是一種功能強大的工具，可將應用程式的使用者介面視覺化，並針對大部分的 iOS 視圖和視圖控制器提供 WYSIWYG 編輯支援。 您的應用程式也可能包含可延伸內建至 iOS 的自訂控制項。 如果在撰寫這些自訂控制項時，有一些指導方針，也可以由 iOS 設計工具來呈現，以提供更豐富的編輯體驗。 本檔將探討這些指導方針。

## <a name="requirements"></a>規格需求

符合下列所有需求的控制項將會在設計介面上呈現：

1. 它是  [UIView](xref:UIKit.UIView) 或  [UIViewController](xref:UIKit.UIViewController)的直接或間接子類別。 其他 [NSObject](xref:Foundation.NSObject) 子類別會顯示為設計介面上的圖示。
2. 它有  [RegisterAttribute](xref:Foundation.RegisterAttribute) 可將它公開至目標 C。
3. 它有  [必要的 IntPtr 函數](~/ios/internals/api-design/index.md)。
4. 它會實 [IComponent](xref:System.ComponentModel.IComponent) 介面，或將 [DesignTimeVisibleAttribute](xref:System.ComponentModel.DesignTimeVisibleAttribute) 設定為 True。

在程式碼中所定義符合上述需求的控制項，會在其包含的專案為模擬器進行編譯時出現在設計工具中。 根據預設，所有自訂控制項都會出現在 [**工具箱**] 的 [**自訂群組件**] 區段中。 不過， [CategoryAttribute](xref:System.ComponentModel.CategoryAttribute) 可以套用至自訂控制項的類別，以指定不同的區段。

設計工具不支援載入協力廠商的目標 C 程式庫。

## <a name="custom-properties"></a>自訂屬性

如果符合下列條件，自訂控制項所宣告的屬性將會出現在 [屬性] 面板中：

1. 屬性具有公用 getter 和 setter。
1. 屬性的  [ExportAttribute](xref:Foundation.ExportAttribute) 和  [BrowsableAttribute](xref:System.ComponentModel.BrowsableAttribute) 設定為 True。
1. 屬性類型為數數值型別、列舉類型、字串、bool、 [SizeF](xref:System.Drawing.SizeF)、 [UIColor](xref:UIKit.UIColor)或 [UIImage](xref:UIKit.UIImage)。 未來可能會擴充此支援類型清單。

屬性也可以使用 [DisplayNameAttribute](xref:System.ComponentModel.DisplayNameAttribute) 裝飾，以指定顯示在屬性面板中的標籤。

## <a name="initialization"></a>初始化

針對子 `UIViewController` 類別，您應該針對相依于您在設計工具中建立之視圖的程式碼使用 [ViewDidLoad](xref:UIKit.UIViewController.ViewDidLoad) 方法。

針對 `UIView` 和其他子 `NSObject` 類別， [AwakeFromNib](xref:Foundation.NSObject.AwakeFromNib) 方法是在從配置檔案載入自訂控制項之後，執行自訂控制項初始化的建議位置。 這是因為在執行控制項的函式時，將不會設定屬性面板中設定的任何自訂屬性，但是在呼叫之前會先設定它們 `AwakeFromNib` ：

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

如果控制項也設計為直接從程式碼建立，您可能會想要建立具有一般初始化程式碼的方法，如下所示：

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

請小心在自訂群組件中將可設計的屬性初始化的時機和位置，不要覆寫在 iOS 設計工具內設定的值。 例如，請採用下列程式碼：

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

`CustomView`元件 `Counter` 會公開可由開發人員在 iOS 設計工具內設定的屬性。 不過，無論在設計工具內設定的值為何，屬性的值 `Counter` 一律會是零 (0) 。 原因如下：

- 的實例是從分鏡腳本檔案  `CustomControl` 放大的。
- 在 iOS 設計工具中修改的任何屬性都會設定 (例如，將的值設定  `Counter` 為兩個 (2) ，例如) 。
- `AwakeFromNib`方法會執行，並對元件的方法進行呼叫 `Initialize` 。
- 在  `Initialize` 屬性值內，  `Counter` 會將屬性的值重設為零 (0) 。

若要修正上述狀況，請 `Counter` 在其他地方初始化屬性 (例如在元件的函式中) 或不覆寫 `AwakeFromNib` 方法，並呼叫 `Initialize` 元件是否不需要在目前由其函式處理的範圍之外進行任何初始化。

## <a name="design-mode"></a>設計模式

在設計介面上，自訂控制項必須遵守一些限制：

- 應用程式套件組合資源無法在設計模式中使用。 透過  [UIImage 方法](xref:UIKit.UIImage) 載入時，可以使用映射。
- 非同步作業（例如 web 要求）不應該在設計模式中執行。 設計介面不支援動畫或任何其他非同步更新至控制項的 UI。

自訂控制項可以執行 [IComponent](xref:System.ComponentModel.IComponent) ，並使用 [DesignMode](xref:System.ComponentModel.ISite.DesignMode) 屬性來檢查它是否在設計介面上。 在此範例中，標籤會在設計介面上顯示「設計模式」，並在執行時間顯示「執行時間」：

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

在 `Site` `null` 嘗試存取其任何成員之前，您應該一律先檢查屬性是否存在。 如果 `Site` 為 `null` ，則假設控制項不是在設計工具中執行，就是安全的。
在設計模式中，將會在 `Site` 控制項的函式已執行，而且在呼叫之前設定 `AwakeFromNib` 。

## <a name="debugging"></a>偵錯

符合上述需求的控制項將會顯示在 [工具箱] 中，並呈現在介面上。
如果未轉譯控制項，請檢查控制項或其中一個相依性的 bug。

設計介面通常可以捕捉個別控制項擲回的例外狀況，同時繼續呈現其他控制項。 錯誤的控制項會以紅色預留位置取代，而且您可以按一下驚嘆號圖示來查看例外狀況追蹤：

 ![做為紅色預留位置和例外狀況詳細資料的錯誤控制項](ios-designable-controls-overview-images/exception-box.png)

如果控制項可使用 debug 符號，追蹤將會有檔案名和行號。
按兩下堆疊追蹤中的行，將會跳到原始程式碼中的那一行。

如果設計工具無法隔離錯誤的控制項，設計介面頂端會出現一則警告訊息：

 ![設計介面頂端的警告訊息](ios-designable-controls-overview-images/info-bar.png)

當修正錯誤的控制項或從設計介面中移除時，就會繼續進行完整轉譯。

## <a name="summary"></a>摘要

本文介紹如何在 iOS 設計工具中建立和應用自訂控制項。 首先，它會說明控制項必須符合才能在設計介面上呈現的需求，並在屬性面板中公開自訂屬性。 接著，它會查看控制項和 DesignMode 屬性的程式碼後端初始化。 最後，它會描述擲回例外狀況時所發生的狀況，以及如何解決此問題。

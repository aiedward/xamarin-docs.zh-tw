---
title: IOS 的 Xamarin 設計工具中的自訂控制項
description: 適用於 iOS 的 Xamarin 設計工具支援在專案中建立，或從外部來源，例如 Xamarin 元件存放區參考轉譯自訂控制項。
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 00bf7290d5f7165feb5b67cd91c15a96b7d3eaf8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118366"
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>IOS 的 Xamarin 設計工具中的自訂控制項

_適用於 iOS 的 Xamarin 設計工具支援在專案中建立，或從外部來源，例如 Xamarin 元件存放區參考轉譯自訂控制項。_

適用於 iOS 的 Xamarin 設計工具是功能強大的工具，以視覺化方式呈現應用程式的使用者介面，並提供 WYSIWYG 編輯大部分 iOS 檢視和檢視控制器的支援。 您的應用程式也可能包含自訂控制項來擴充內建在 iOS 的項目。 如果記住的一些指導方針，撰寫這些自訂控制項，也依 iOS 設計工具中，提供更豐富的編輯體驗呈現它們。 本文件探討這些指導方針。

## <a name="requirements"></a>需求

在設計介面上會轉譯符合下列所有需求的控制項：

1.  它是直接或間接子類別的[UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)或是[UIViewController](https://developer.xamarin.com/api/type/UIKit.UIView/Controller)。 其他[NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)子類別會顯示為設計介面上的圖示。
2.  它有[RegisterAttribute](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/)公開以 OBJECTIVE-C
3.  它有[必要的 IntPtr 建構函式](~/ios/internals/api-design/index.md)。
4.  它可能會實作[IComponent](xref:System.ComponentModel.IComponent)介面，或已[DesignTimeVisibleAttribute](xref:System.ComponentModel.DesignTimeVisibleAttribute)設為 True。

程式碼中定義的控制項符合上述需求，會出現在設計工具中，其包含的專案編譯模擬器時。 根據預設，所有的自訂控制項將會出現在**自訂元件**一節**工具箱**。 不過[CategoryAttribute](xref:System.ComponentModel.CategoryAttribute)可以套用至自訂控制項的類別，以指定不同的區段。

設計工具不支援載入協力廠商 Objective C 程式庫。

## <a name="custom-properties"></a>自訂內容

自訂控制項所宣告的屬性會出現在 [屬性] 面板中，如果符合下列條件：

1.  屬性會有公用 getter 和 setter。
1.  屬性具有[exportattribute 標記](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/)，以及[BrowsableAttribute](xref:System.ComponentModel.BrowsableAttribute)設為 True。
1.  屬性型別是數值類型、 列舉型別、 string、 bool、 [SizeF](xref:System.Drawing.SizeF)， [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/)，或[UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/)。 這份支援的類型可能會在未來擴充。


屬性也可以使用裝飾[DisplayNameAttribute](xref:System.ComponentModel.DisplayNameAttribute)來指定其顯示在 [屬性] 面板的標籤。

## <a name="initialization"></a>初始化

針對`UIViewController`子類別，您應該使用[ViewDidLoad](https://developer.xamarin.com/api/member/UIKit.UIViewController.ViewDidLoad/)取決於您在設計工具中建立的檢視的程式碼的方法。

針對`UIView`和其他`NSObject`子類別， [AwakeFromNib](https://developer.xamarin.com/api/member/Foundation.NSObject.AwakeFromNib/)方法是執行您的自訂控制項的初始化，載入從版面配置檔之後的建議的位置。 這是因為當執行控制項的建構函式時，但它們將會設定之前，將不會設定在 [屬性] 面板中設定的任何自訂屬性`AwakeFromNib`稱為：


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

如果控制項也可直接從程式碼建立，您可能想要建立具有通用的初始化程式碼，就像這樣的方法：

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

## <a name="property-initialization-and-awakefromnib"></a>屬性初始設定和 AwakeFromNib

應該小心在何時及如何初始化可設計的屬性，使用自訂的元件，並不會覆寫已在 iOS 設計工具內設定的值。 例如，執行下列程式碼：

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

`CustomView`元件會公開`Counter`iOS 設計工具內的開發人員可以設定的屬性。 不過，無論何種值會設定在設計工具中，值`Counter`屬性一律會是零 (0)。 原因如下：

-  執行個體`CustomControl`擴大從分鏡腳本檔案。
-  在 iOS 設計工具中修改任何屬性都設定 (例如設定的值`Counter`到兩個 （2)，例如)。
-  `AwakeFromNib`方法會執行，並呼叫到元件的`Initialize`方法。
-  內部`Initialize`的值`Counter`屬性則正在重設為零 (0)。


若要修正上述的情況下，初始化`Counter`屬性在其他地方 （例如元件的建構函式） 或不覆寫`AwakeFromNib`方法，並呼叫`Initialize`如果元件需要外部功能沒有進一步的初始化正在處理由其建構函式。

## <a name="design-mode"></a>設計模式

在設計介面上的自訂控制項必須遵守一些限制：

-  無法在設計模式中使用應用程式套件組合的資源。 映像可供透過載入時[UIImage 方法](https://developer.xamarin.com/api/type/UIKit.UIImage/%2fM)。
-  不應在設計模式中執行非同步作業，例如 web 要求。 不支援在設計介面，動畫或任何其他非同步更新控制項的 ui。


可以實作自訂控制項[IComponent](xref:System.ComponentModel.IComponent)並用[設計模式](xref:System.ComponentModel.ISite.DesignMode)來檢查它是否在設計介面上的屬性。 在此範例中，標籤會顯示 「 設計模式 」 的設計介面和 「 執行階段 」 在執行階段：

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
        if (Site != null &amp;&amp; Site.DesignMode)
            Text = "Design Mode";
        else
            Text = "Runtime";
    }
}
```

您應該一律檢查`Site`屬性`null`後再嘗試存取它的任何成員。 如果`Site`是`null`，安全地假設控制項不會執行設計工具中。
在設計模式中，`Site`將會設定控制項的建構函式執行之後，以及之前`AwakeFromNib`呼叫。

## <a name="debugging"></a>偵錯

將工具箱中所顯示的控制項符合上述需求，並將其介面上呈現。
如果控制項呈現時，請檢查控制項或其中一個相依性中的錯誤。

在設計介面通常可以攔截例外狀況擲回的個別控制項，同時可繼續在其他控制項。 錯誤的控制項取代紅色的預留位置，而您可以檢視例外狀況追蹤驚嘆號圖示即可：

 ![](ios-designable-controls-overview-images/exception-box.png "錯誤的控制項為紅色的預留位置和例外狀況詳細資料")

如果偵錯符號可供控制，追蹤就會有檔案名稱和行號。 按一下堆疊追蹤中的資料行的雙精度浮點數會跳至原始程式碼中該程式行。

如果設計工具無法找出故障的控制項，一則警告訊息會出現在設計介面的頂端：

 ![](ios-designable-controls-overview-images/info-bar.png "在設計介面頂端的警告訊息")

錯誤的控制項為固定長度或移除從設計介面，將恢復完整呈現。

## <a name="summary"></a>總結

本文將介紹建立和自訂控制項，在 iOS 設計工具中的應用程式。 第一次，它會描述控制項必須符合在設計介面上呈現，並將其公開屬性 面板中的自訂屬性的需求。 它接著會探討程式碼後置-初始化的控制項和設計模式屬性。 最後說明會發生什麼事時擲回例外狀況，以及如何解決此問題。
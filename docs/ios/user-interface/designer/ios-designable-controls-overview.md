---
title: IOS 的 Xamarin 設計工具中的自訂控制項
description: 適用於 iOS Xamarin 設計工具支援您的專案中建立或從外部來源，例如 Xamarin 元件存放區參考轉譯自訂控制項。
ms.prod: xamarin
ms.assetid: D8F07D63-B006-4050-9D1B-AC6FCDA71B99
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 113fab2fd0d1a055d566606885cefbafe3185529
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="custom-controls-in-the-xamarin-designer-for-ios"></a>IOS 的 Xamarin 設計工具中的自訂控制項

_適用於 iOS Xamarin 設計工具支援您的專案中建立或從外部來源，例如 Xamarin 元件存放區參考轉譯自訂控制項。_

Xamarin 設計工具，適用於 iOS 視覺化應用程式的使用者介面的強大工具，提供 WYSIWYG 編輯的大多數 iOS 檢視和檢視控制器的支援。 您的應用程式可能也包含擴充內建 iOS 適用的自訂控制項。 如果記住的一些指導方針，撰寫這些自訂控制項，可以也呈現 ios 設計工具中，提供更豐富的編輯體驗。 這份文件會探討這些指導方針。

## <a name="requirements"></a>需求

符合下列所有需求的控制項將設計介面上呈現：

1.  它是直接或間接子類別的[UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)或[UIViewController](https://developer.xamarin.com/api/type/UIKit.UIView/Controller)。 其他[NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)子類別會顯示為設計介面上的圖示。
2.  它有[RegisterAttribute](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/)公開到目標 c。
3.  它有[必要的 IntPtr 建構函式](~/ios/internals/api-design/index.md)。
4.  它可以是實作[IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/)介面或具有[DesignTimeVisibleAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DesignTimeVisibleAttribute/)設為 True。

其包含的專案編譯模擬器時，會出現在設計工具中符合上述需求的程式碼中定義控制項。 根據預設，所有的自訂控制項將會出現在**自訂元件**區段**工具箱**。 不過， [CategoryAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.CategoryAttribute/)可以套用至自訂控制項的類別，以指定不同的區段。

在設計工具不支援載入協力廠商 Objective C 程式庫。

## <a name="custom-properties"></a>自訂內容

自訂控制項所宣告的屬性會出現在 [屬性] 面板中，如果符合下列條件：

1.  屬性有公用的 getter 和 setter。
1.  此屬性有[exportattribute 標記的型](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/)以及[BrowsableAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.BrowsableAttribute/)設為 True。
1.  屬性類型就是數值類型、 列舉類型、 string、 bool、 [SizeF](https://developer.xamarin.com/api/type/System.Drawing.SizeF/)， [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/)，或[UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/)。 在未來可能會展開這份支援的型別。


屬性也可以使用裝飾[DisplayNameAttribute](https://developer.xamarin.com/api/type/System.ComponentModel.DisplayNameAttribute/)指定會為其顯示在 [屬性] 面板的標籤。

## <a name="initialization"></a>初始化

如`UIViewController`子類別中，您應該使用[ViewDidLoad](https://developer.xamarin.com/api/member/UIKit.UIViewController.ViewDidLoad/)取決於您在設計工具中建立的檢視的程式碼的方法。

如`UIView`和其他`NSObject`子類別， [AwakeFromNib](https://developer.xamarin.com/api/member/Foundation.NSObject.AwakeFromNib/)方法是在載入時將它從配置檔後，執行初始化自訂控制項的建議的位置。 這是因為當控制項的建構函式會執行，但它之前將設定這些欄位，將不會設定在 [屬性] 面板中設定任何自訂屬性`AwakeFromNib`稱為：


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

如果要直接從程式碼建立也設計控制項，您可能想要建立具有通用的初始化程式碼，就像這樣的方法：

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

## <a name="property-initialization-and-awakefromnib"></a>屬性的初始化和 AwakeFromNib

應該小心在何時及如何初始化自訂元件，而不會覆寫值已設定內部 iOS 設計工具中設計的屬性。 例如，執行下列程式碼：

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

`CustomView`元件公開`Counter`內 iOS 設計工具開發人員可以設定的屬性。 不過，不論在設計工具中，值的設定了什麼價值`Counter`屬性一定會是零 (0)。 原因如下：

-  執行個體`CustomControl`膨脹從分鏡腳本檔案。
-  設定 iOS 設計工具中修改任何屬性 (例如設定的值`Counter`至二 （2)，例如)。
-  `AwakeFromNib`執行方法，並呼叫該元件的`Initialize`方法。
-  內部`Initialize`值`Counter`屬性會被重設為零 (0)。


若要修正上述情況，可能是初始化`Counter`屬性其他地方 （例如元件的建構函式） 或不覆寫`AwakeFromNib`方法，並呼叫`Initialize`如果元件不需要什麼以外任何進一步的初始化正在處理由其建構函式。

## <a name="design-mode"></a>設計模式

自訂控制項的設計介面上，必須遵守一些限制：

-  無法在設計模式中使用應用程式資源存放區資源。 映像可供透過載入時[UIImage 方法](https://developer.xamarin.com/api/type/UIKit.UIImage/%2fM)。
-  非同步作業，例如 web 要求，不應該在設計模式中執行。 在設計介面不支援動畫或任何其他非同步更新控制項的 ui。


自訂控制項可以實作[IComponent](https://developer.xamarin.com/api/type/System.ComponentModel.IComponent/)並用[設計模式](https://developer.xamarin.com/api/property/System.ComponentModel.ISite.DesignMode/)來檢查它是否在設計介面上的屬性。 在此範例中，標籤會顯示 [設計模式] 在設計介面及其"Runtime"在執行階段：

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

請務必檢查`Site`屬性`null`後再嘗試存取它的任何成員。 如果`Site`是`null`，安全地假設控制項不以設計工具中執行。
在設計模式中，`Site`將控制項的建構函式執行完畢之後再`AwakeFromNib`呼叫。

## <a name="debugging"></a>偵錯

將工具箱中所顯示控制項符合上述需求，並將其介面上呈現。
如果不呈現控制項，請檢查控制項或其中一個相依性中的 bug。

在設計介面通常可以攔截例外狀況擲回的個別控制項，同時繼續呈現其他控制項。 故障的控制項就會以紅色的預留位置，取代，而且可以驚嘆號圖示即可檢視例外狀況追蹤：

 ![](ios-designable-controls-overview-images/exception-box.png "錯誤為紅色的預留位置和例外狀況詳細資料控制項")

如果偵錯符號可用控制項，追蹤將會有檔案名稱和行號。 按兩下堆疊追蹤中的資料行會跳到原始程式碼中的線條。

如果設計工具無法找出故障的控制項，警告訊息會出現在設計介面的頂端：

 ![](ios-designable-controls-overview-images/info-bar.png "在設計介面上方的警告訊息")

故障的控制項是固定或移除從設計介面時，將會繼續完整呈現。

## <a name="summary"></a>總結

建立與 iOS 設計工具中的自訂控制項的應用程式，導入了這份文件。 第一次，它會描述控制項必須符合在設計介面上呈現，並將其公開屬性面板中的自訂屬性的需求。 它然後看過程式碼後置-初始化控制項的設計模式的屬性。 最後描述會發生什麼事時擲回例外狀況，以及如何解決此問題。
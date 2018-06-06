---
title: 建立程式碼中 Xamarin.iOS iOS 使用者介面
description: 本文件說明如何使用程式碼建置 Xamarin.iOS 應用程式的使用者介面。 其中也會討論檢視控制器，建置檢視階層時，處理旋轉，等等。
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/03/2018
ms.openlocfilehash: 5e8abc2cea2e2ca8abfada8bc85379d93d183768
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784630"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>建立程式碼中 Xamarin.iOS iOS 使用者介面

IOS 應用程式的使用者介面就像 storefront – 應用程式通常會取得一個視窗，但它可以充滿視窗需要在它的許多物件，並根據應用程式想要顯示，則可以變更的物件和排列方式。 此案例中的物件 (使用者所看見的內容) 稱為「檢視」。 若要建置的應用程式中的單一畫面，檢視會彼此交互堆疊在內容檢視階層中，與階層由單一檢視控制器管理。 具有多個畫面的應用程式會有多個內容檢視階層，每個都有它自己的檢視控制器，而應用程式會將檢視放置於視窗中，以根據使用者所在畫面建立不同的內容檢視階層。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

下圖說明視窗、檢視、子檢視及檢視控制器之間的關聯性，以在裝置畫面中顯示使用者介面： 

[![](ios-code-only-images/image9.png "此圖說明 視窗、 檢視、 Subviews 及檢視控制器之間的關聯性")](ios-code-only-images/image9.png#lightbox)

這些檢視階層可以使用建構[Xamarin 設計工具，適用於 iOS](~/ios/user-interface/designer/index.md)在 Visual Studio 中，不過最好是有基本了解如何使用完全以程式碼。 本文逐步解說一些基本的點，才能啟動和執行僅限程式碼使用者介面開發人員。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

下圖說明視窗、檢視、子檢視及檢視控制器之間的關聯性，以在裝置畫面中顯示使用者介面： 

[![](ios-code-only-images/image9.png "此圖說明 視窗、 檢視、 Subviews 及檢視控制器之間的關聯性")](ios-code-only-images/image9.png#lightbox)

這些檢視階層可以使用建構[Xamarin 設計工具，適用於 iOS](~/ios/user-interface/designer/index.md)在 Visual Studio for Mac，不過最好是有基本了解如何在程式碼中完全處理。 本文逐步解說一些基本的點，才能啟動和執行僅限程式碼使用者介面開發人員。

-----

## <a name="creating-a-code-only-project"></a>建立僅限程式碼專案

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="ios-blank-project-template"></a>iOS 空白專案範本

首先，建立 iOS 專案在 Visual Studio 使用**檔案 > 新的專案 > Visual C# > iPhone （& s) iPad > iOS 應用程式 (Xamarin)** 專案中，如下所示：

[![新增專案 對話方塊](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

然後選取**空白應用程式**專案範本：

[![[選取範本] 對話方塊](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

空白專案範本會將 4 個檔案加入專案：

[![專案檔](ios-code-only-images/empty-project.w157-sml.png "專案檔")](ios-code-only-images/empty-project.w157.png#lightbox)


1. **D** -包含`UIApplicationDelegate`子類別， `AppDelegate` ，用來處理從 iOS 應用程式事件。 應用程式視窗中建立`AppDelegate`的`FinishedLaunching`方法。
1. **Main.cs** -包含應用程式，指定類別的進入點的`AppDelegate`。
1. **Info.plist** -內容清單檔案，其中包含應用程式組態資訊。
1. **Entitlements.plist** – 內容清單檔案，其中包含的功能和應用程式的權限的相關資訊。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="ios-templates"></a>iOS 範本


Visual Studio for Mac 不提供空白的範本。 所有範本都隨附的分鏡腳本支援 Apple 建議做為主要方式來建立的 UI。 不過，它可能會完全建立您的 UI 程式碼。 

下列步驟會引導您完成移除應用程式中的分鏡腳本： 


1. 使用的單一檢視應用程式範本建立新的 iOS 專案：
    
    [![](ios-code-only-images/single-view-app.png "使用單一檢視應用程式範本")](ios-code-only-images/single-view-app.png#lightbox)

1. 刪除`Main.Storyboard`和`ViewController.cs`檔案。 請勿**不**刪除`LaunchScreen.Storyboard`。 應該刪除檢視控制器，因為它是建立在分鏡腳本檢視控制器背後的程式碼：
1. 請務必選取**刪除**從快顯的對話方塊：
    
    [![](ios-code-only-images/delete.png "從快顯對話方塊中選取 刪除")](ios-code-only-images/delete.png#lightbox)

1. 在 Info.plist 中刪除內部資訊**部署資訊 > 主要介面**選項：
    
    [![](ios-code-only-images/main-interface.png "刪除內部的主要介面選項的資訊")](ios-code-only-images/main-interface.png#lightbox)

1. 最後，加入下列程式碼加入您`FinishedLaunching`AppDelegate 類別中的方法：
        
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            // create a new window instance based on the screen size
            window = new UIWindow(UIScreen.MainScreen.Bounds);

            // make the window visible
            window.MakeKeyAndVisible();

            return true;
        }

已加入的程式碼`FinishedLaunching`在步驟 5 以上版本，方法是建立您的 iOS 應用程式的視窗所需的程式碼的最小數量。


-----



建置 iOS 應用程式使用[MVC 模式](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#Model_View_Controller)。 視窗的根檢視控制站來建立應用程式會顯示第一個畫面。 請參閱[Hello，iOS 多重畫面](~/ios/get-started/hello-ios-multiscreen/index.md)引導如需有關 MVC 模式本身。

實作`AppDelegate`新增的範本建立應用程式 視窗中，它那里只有一個每個 iOS 應用程式，並會顯示為下列程式碼：

```csharp
public class AppDelegate : UIApplicationDelegate
{
    public override UIWindow Window
            {
                get;
                set;
            }

    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        window.MakeKeyAndVisible();

        return true;
    }
}
```

如果您要執行此應用程式，現在，您可能會收到指出擲回例外狀況`Application windows are expected to have a root view controller at the end of application launch`。 讓我們加入控制器，並將其應用程式的根檢視控制站。

## <a name="adding-a-controller"></a>新增控制器

您的應用程式可以包含許多檢視控制站，但它必須有一個根檢視控制站，以控制所有檢視控制站。  新增至視窗的控制站，藉由建立`UIViewController`執行個體，並將它設定為`window.RootViewController`屬性：

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;

        Window.RootViewController = controller;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

每個控制站具有相關聯的檢視，也就是從存取`View`屬性。 上述程式碼變更的檢視`BackgroundColor`屬性`UIColor.LightGray`，因此它會顯示，如下所示：

 [![](ios-code-only-images/image1.png "檢視的背景是可見的淺灰色")](ios-code-only-images/image1.png#lightbox)

我們無法設定任何`UIViewController`子類別為`RootViewController`以這種方式，包括從 UIKit 以及我們可以撰寫自己的控制站。 例如，下列程式碼加入`UINavigationController`為`RootViewController`:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;
        controller.Title = "My Controller";

        var navController = new UINavigationController(controller);

        Window.RootViewController = navController;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

這會產生如下所示的巢狀瀏覽控制器內的控制站：

 [![](ios-code-only-images/image2.png "導覽控制站內變成巢狀控制站")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>建立檢視控制站

既然我們已經看到如何將控制站指定為`RootViewController`的視窗中，我們來看看如何在程式碼中建立的自訂檢視控制站。

加入新的類別，名為`CustomViewController`如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "新增名為 CustomViewController 類別")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](ios-code-only-images/new-file.png "新增名為 CustomViewController 類別")](ios-code-only-images/new-file.png#lightbox)

-----

此類別必須繼承自`UIViewController`，這是在`UIKit`命名空間，如下所示：

```csharp
using System;
using UIKit;

namespace CodeOnlyDemo
{
    class CustomViewController : UIViewController
    {
    }
}
```

<a name="Initializing_the_View"/>

## <a name="initializing-the-view"></a>初始化檢視

`UIViewController` 包含方法呼叫`ViewDidLoad`，而呼叫時檢視控制器第一次載入記憶體。 這是適當的位置，以進行檢視，例如，設定其內容的初始化。

例如，下列程式碼加入按鈕和餂鈕蒢推播至瀏覽堆疊新檢視控制站的事件處理常式：

```csharp
using System;
using CoreGraphics;
using UIKit;

namespace CodyOnlyDemo
{
    public class CustomViewController : UIViewController
    {
        public CustomViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            View.BackgroundColor = UIColor.White;
            Title = "My Custom View Controller";

            var btn = UIButton.FromType (UIButtonType.System);
            btn.Frame = new CGRect (20, 200, 280, 44);
            btn.SetTitle ("Click Me", UIControlState.Normal);

            var user = new UIViewController ();
            user.View.BackgroundColor = UIColor.Magenta;

            btn.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (user, true);
            };

            View.AddSubview (btn);

        }
    }
}
```

若要載入此應用程式中的控制站，並示範簡單的導覽，建立的新執行個體`CustomViewController`。 建立新的瀏覽控制器、 傳遞檢視的控制器執行個體以及將新的瀏覽控制站設定為視窗的`RootViewController`中`AppDelegate`與之前：

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

現在應用程式載入，`CustomViewController`載入內導覽控制站：

 [![](ios-code-only-images/customvc.png "CustomViewController 載入內導覽控制站")](ios-code-only-images/customvc.png#lightbox)
 
按一下按鈕，將_發送_新檢視控制站至瀏覽堆疊：

[![](ios-code-only-images/customvca.png "在巡覽堆疊推入新的檢視控制器")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>建立檢視階層架構

在上述範例中，我們已開始將按鈕加入至檢視控制器程式碼中建立使用者介面。

iOS 使用者介面是由檢視階層所組成。 額外的檢視，例如標籤、 按鈕、 滑桿 」 等會加入為 subviews 的某些父檢視。

例如，讓我們來編輯`CustomViewController`來建立讓使用者可以輸入使用者名稱和密碼的登入畫面。 螢幕將會包含兩個文字欄位和按鈕。

### <a name="adding-the-text-fields"></a>加入文字欄位

首先，移除已加入按鈕和事件處理常式[初始化檢視](#Initializing_the_View)> 一節。 

新增使用者名稱的控制項是建立並初始化`UITextField`，然後將它加入至檢視階層中，如下所示：

```csharp
class CustomViewController : UIViewController
{
    UITextField usernameField;

    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        View.BackgroundColor = UIColor.Gray;

        nfloat h = 31.0f;
        nfloat w = View.Bounds.Width;

        usernameField = new UITextField
        {
            Placeholder = "Enter your username",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 82, w - 20, h)
        };

        View.AddSubview(usernameField);
    }
}
```

當我們建立`UITextField`，我們設定`Frame`屬性來定義其位置和大小。 在 iOS 中的 0，0 的座標是左上方的 + x 右邊和 + y 下的。 設定之後`Frame`連同一些其他屬性，我們會呼叫`View.AddSubview`新增`UITextField`檢視階層架構。 這可讓`usernameField`的子檢視`UIView`執行個體`View`屬性參考。 子檢視會加入具有高於父檢視，讓它出現在螢幕上的父檢視前面疊置順序。

應用程式與`UITextField`包含如下所示：

 [![](ios-code-only-images/image4.png "包含 UITextField 應用程式")](ios-code-only-images/image4.png#lightbox)

我們可以加入`UITextField`密碼以類似的方式，不過此時我們設定`SecureTextEntry`屬性設定為 true，如下所示：

```csharp
public class CustomViewController : UIViewController
{
    UITextField usernameField, passwordField;
    public override void ViewDidLoad()
    {
       // keep the code the username UITextField
        passwordField = new UITextField
        {
            Placeholder = "Enter your password",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 114, w - 20, h),
            SecureTextEntry = true
        };

      View.AddSubview(usernameField); 
      View.AddSubview(passwordField);
   }
}

```

設定`SecureTextEntry = true`隱藏在文字方塊中輸入`UITextField`使用者如下所示：

 [![](ios-code-only-images/image4a.png "設定 SecureTextEntry true 會隱藏使用者所輸入的文字")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>加入按鈕

接下來，我們要加入按鈕讓使用者可以提交的使用者名稱和密碼。 按鈕就會像任何其他控制項的檢視階層架構來加入它做為引數傳遞給父檢視`AddSubview`方法一次。

下列程式碼加入按鈕和註冊事件處理常式`TouchUpInside`事件：

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

這個之後，登入畫面現在看起來如下所示：

 [![](ios-code-only-images/image5.png "登入畫面")](ios-code-only-images/image5.png#lightbox)

不同於在舊版的 iOS、 預設按鈕的背景是透明的。 變更按鈕的`BackgroundColor`屬性變更此：

```csharp
submitButton.BackgroundColor = UIColor.White;
```

這將導致方形按鈕，而非一般的捨入邊的按鈕。 若要取得圓角的邊緣，請使用下列程式碼片段：

```csharp
submitButton.Layer.CornerRadius = 5f;
```

這些變更，檢視看起來像這樣：

[![](ios-code-only-images/image6.png "在檢視執行的範例")](ios-code-only-images/image6.png#lightbox)
 
## <a name="adding-multiple-views-to-the-view-hierarchy"></a>將多個檢視加入至檢視階層架構

iOS 提供加入檢視階層架構中的多個檢視，使用設備`AddSubviews`。

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton }); 
```

## <a name="adding-button-functionality"></a>加入按鈕的功能

當按一下按鈕時，您的使用者將預期發生。 例如，警示會顯示或執行瀏覽至另一個螢幕。 

讓我們加入一些程式碼來推送巡覽堆疊中的第二個檢視控制站。

首先，建立第二個檢視控制器：

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

然後，將功能加入至`TouchUpInside`事件：

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

瀏覽如下所示：

[![](ios-code-only-images/navigation.png "瀏覽如下所示此圖表")](ios-code-only-images/navigation.png#lightbox)

請注意，根據預設，當您使用瀏覽控制站，iOS 應用程式提供導覽列和上一頁按鈕，以讓您向後移動堆疊。

## <a name="iterating-through-the-view-hierarchy"></a>逐一查看的檢視階層架構

它可逐一查看子檢視階層以及挑出任何特定的檢視。 例如，若要尋找每個`UIButton`，並為該按鈕不同`BackgroundColor`，可以使用下列程式碼片段

```csharp
foreach(var subview in View.Subviews)
{
    if (subview is UIButton)
    {
         var btn = subview as UIButton;
         btn.BackgroundColor = UIColor.Green;
    }
}
```

這點，不過將無法運作如果正在逐一查看的檢視是`UIView`為所有檢視將都回來為`UIView`本身為父檢視中加入的物件繼承`UIView`。

## <a name="handling-rotation"></a>處理旋轉

如果使用者旋轉裝置變成橫向，控制項不調整大小適當，如下列螢幕擷取畫面所示：

 [![](ios-code-only-images/image7.png "如果使用者旋轉裝置變成橫向，控制項不調整大小適當地")](ios-code-only-images/image7.png#lightbox)

若要修正此問題的一個方法是設定`AutoresizingMask`上每個檢視的屬性。 在此情況下我們想要的水平縮放控制項，我們會設定每個`AutoresizingMask`。 下列範例是針對`usernameField`，但相同會想要套用至檢視階層中每個小工具。

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

現在我們旋轉的裝置或模擬器，當所有項目會自動縮放以填滿額外的空間，如下所示：

 [![](ios-code-only-images/image8.png "所有控制項都伸展以填滿額外的空間")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>建立自訂檢視

除了使用屬於 UIKit 的控制項，也可以使用自訂檢視。 可以建立自訂檢視繼承自`UIView`和覆寫`Draw`。 讓我們來建立自訂檢視，並將它加入至檢視階層，以示範。

### <a name="inheriting-from-uiview"></a>繼承自 UIView

我們要做的第一件事就是建立自訂檢視的類別。 我們會執行這項操作使用**類別**範本在 Visual Studio 中加入名為空類別`CircleView`。 基底類別應設定為`UIView`，我們請記得這是在`UIKit`命名空間。 我們還需要`System.Drawing`以及命名空間。 在各種`System.*`命名空間將不會使用在此範例中，所以您可以自由將它們移除。

此類別看起來應該像這樣：

```csharp
using System;

namespace CodeOnlyDemo
{
    class CircleView : UIView
    {
    }
}
```

### <a name="drawing-in-a-uiview"></a>UIView 中繪圖

每個`UIView`具有`Draw`需要繪製時，由系統呼叫的方法。 `Draw` 應該永遠不會直接呼叫。 它是由系統呼叫期間執行的迴圈處理。 第一次執行迴圈之後新增的檢視階層架構中，以檢視其`Draw`方法呼叫。 後續呼叫`Draw`檢視標示為需要繪製呼叫時，會發生`SetNeedsDisplay`或`SetNeedsDisplayInRect`檢視上。

我們可以將繪製程式碼加入我們檢視藉由新增這類程式碼內覆寫`Draw`方法如下所示：

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    //get graphics context
    using (var g = UIGraphics.GetCurrentContext())
    {
        // set up drawing attributes
        g.SetLineWidth(10.0f);
        UIColor.Green.SetFill();
        UIColor.Blue.SetStroke();

        // create geometry
        var path = new CGPath();
        path.AddArc(Bounds.GetMidX(), Bounds.GetMidY(), 50f, 0, 2.0f * (float)Math.PI, true);

        // add geometry to graphics context and draw
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);
    }
}
```

因為`CircleView`是`UIView`，我們也可以設定`UIView`以及屬性。 例如，我們可以設定`BackgroundColor`建構函式：

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

若要使用`CircleView`剛才所建立，我們可以加入它的子檢視當做檢視階層架構中現有的控制站，以`UILabels`和`UIButton`更早版本，或者我們可以將它載入新的控制站的檢視。 就來看看後者。

### <a name="loading-a-view"></a>載入檢視

 `UIViewController` 具有名為方法`LoadView`，而若要建立其檢視控制器所呼叫。 這是建立檢視，並將它指派給控制站的適當位置`View`屬性。

首先，我們需要控制器，因此建立新的空類別，名為`CircleController`。

在`CircleController`加入下列程式碼以設定`View`至`CircleView`(您不應該呼叫`base`實作您的覆寫):

```csharp
using UIKit;

namespace CodeOnlyDemo
{
    class CircleController : UIViewController
    {
        CircleView view;

        public override void LoadView()
        {
            view = new CircleView();
            View = view;
        }
    }
}
```

最後，我們需要在執行階段呈現控制器。 讓我們來執行這項操作上稍早、，如下所示，我們加入的 [提交] 按鈕加入事件處理常式來：

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

現在，當我們執行應用程式，並點選 [提交] 按鈕，就會顯示為一個圓圈新的檢視：

 [![](ios-code-only-images/circles.png "會顯示新檢視的圓圈")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>建立啟動螢幕

A[啟動螢幕](~/ios/app-fundamentals/images-icons/launch-screens.md)時，您的應用程式啟動，以便顯示給使用者，則回應會顯示。 載入您的應用程式時，會顯示啟動畫面，因為它無法建立在程式碼中為應用程式仍在載入到記憶體中。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

當您建立專案在 Visual Studio 中，啟動螢幕為您的.xib 檔案，可以在表單中提供的 iOS**資源**在專案內的資料夾。 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

當您的 Mac，啟動螢幕為您的分鏡腳本檔案形式提供，Visual Studio 中建立的 iOS 專案。 

-----

這可以編輯 double，則在其上按一下，然後 iOS 設計工具中開啟它。

Apple 建議.xib 或分鏡腳本檔案的用途為目標 iOS 8 應用程式，或更新版本中，當您啟動 iOS 設計工具中的其中一個檔案時，您將使用大小類別和自動配置來調整版面配置，因此看起來不錯，並正確地顯示所有裝置大小。 靜態啟動映像可以使用除了.xib 或分鏡腳本，允許以較早的版本為目標的應用程式的支援。

如需有關建立啟動螢幕的詳細資訊，請參閱下列文件：

- [建立使用.xib 啟動螢幕](https://developer.xamarin.com/recipes/ios/general/templates/launchscreen-xib/)
- [管理與分鏡腳本的啟動螢幕](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> 從 iOS 9，開始 Apple 建議分鏡腳本應該當做建立啟動螢幕的主要方法。

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>建立啟動映像前 ios 8 的應用程式

靜態影像可以使用除了.xib 或分鏡腳本啟動螢幕，如果您的應用程式以若是 iOS 8 之前的版本為目標。 

Info.plist 檔案，或在您的應用程式 （適用於 iOS 7) 的資產目錄，可以設定此靜態影像。 您必須為應用程式可能會在每個裝置大小 320 x 480、 640 x 960 （640 x 1136） 提供不同的映像。 如需有關啟動螢幕大小的詳細資訊，請檢視[啟動畫面影像](~/ios/app-fundamentals/images-icons/launch-screens.md)指南。

> [!IMPORTANT]
> 如果您的應用程式有沒有啟動螢幕，您可能會發現它並不完全符合螢幕。 如果這種情況，您應該確定至少，包含名為 640 x 1136 映像`Default-568@2x.png`程式 Info.plist 至。 



## <a name="summary"></a>總結

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

本文將討論如何開發透過程式設計方式在 Visual Studio 中的 iOS 應用程式。 我們探討如何建置的專案，從空白專案範本，討論如何建立及加入至視窗的 [根] 檢視控制站。 然後，我們會示範如何使用從 UIKit 控制項來建立檢視階層架構中控制站來開發應用程式畫面。 接下來我們檢查的方式，讓檢視配置適當地在不同的方向和我們可了解如何建立自訂檢視透過子類別化`UIView`、 以及如何載入的控制器內的檢視。 最後，我們會探索如何將應用程式的啟動螢幕。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

本文將討論如何開發 iOS 應用程式以程式設計方式在 Visual Studio for mac。 我們探討如何建置的專案，從單一檢視範本，討論如何建立及加入至視窗的 [根] 檢視控制站。 然後，我們會示範如何使用從 UIKit 控制項來建立檢視階層架構中控制站來開發應用程式畫面。 接下來我們檢查的方式，讓檢視配置適當地在不同的方向和我們可了解如何建立自訂檢視透過子類別化`UIView`、 以及如何載入的控制器內的檢視。 最後，我們會探索如何將應用程式的啟動螢幕。

-----




## <a name="related-links"></a>相關連結

- [SimpleLogin （範例）](https://developer.xamarin.com/samples/monotouch/SimpleLogin)

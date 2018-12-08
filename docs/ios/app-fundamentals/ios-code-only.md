---
title: 在 Xamarin.iOS 中的程式碼建立 iOS 使用者介面
description: 本文件說明如何使用程式碼建置 Xamarin.iOS 應用程式的使用者介面。 它討論檢視控制器，建置檢視階層中，處理旋轉，等等。
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 2fa554264578ec626567ef7d28377ac80bde21d3
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53060170"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>在 Xamarin.iOS 中的程式碼建立 iOS 使用者介面

IOS 應用程式的使用者介面就像一個店面： 應用程式通常會取得一個視窗中，但它可以填滿視窗與許多物件它需要以及根據的應用程式，則可以變更的物件和排列方式想要顯示為。 此案例中的物件 (使用者所看見的內容) 稱為「檢視」。 若要建置單一畫面的應用程式中，檢視會彼此交互堆疊在內容檢視階層中，與階層由單一檢視控制器管理。 具有多個畫面的應用程式會有多個內容檢視階層，每個都有它自己的檢視控制器，而應用程式會將檢視放置於視窗中，以根據使用者所在畫面建立不同的內容檢視階層。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

下圖說明視窗、檢視、子檢視及檢視控制器之間的關聯性，以在裝置畫面中顯示使用者介面： 

[![](ios-code-only-images/image9.png "此圖說明視窗、 檢視、 子檢視和檢視控制器之間的關聯性")](ios-code-only-images/image9.png#lightbox)

這些檢視階層可以使用來建構[適用於 iOS 的 Xamarin 設計師](~/ios/user-interface/designer/index.md)在 Visual Studio 中，不過最好有基本的了解如何完全以程式碼。 這篇文章會逐步一些基本的點，以啟動及執行僅限程式碼使用者介面開發。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

下圖說明視窗、檢視、子檢視及檢視控制器之間的關聯性，以在裝置畫面中顯示使用者介面： 

[![](ios-code-only-images/image9.png "此圖說明視窗、 檢視、 子檢視和檢視控制器之間的關聯性")](ios-code-only-images/image9.png#lightbox)

這些檢視階層可以使用來建構[適用於 iOS 的 Xamarin 設計師](~/ios/user-interface/designer/index.md)在 Visual Studio for Mac，不過最好有基本的了解如何完全以程式碼。 這篇文章會逐步一些基本的點，以啟動及執行僅限程式碼使用者介面開發。

-----

## <a name="creating-a-code-only-project"></a>建立僅限程式碼的專案

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>iOS 的空白專案範本

首先，建立 iOS 專案，在 Visual Studio 中使用**檔案 > 新增專案 > Visual C# > iPhone & iPad > iOS 應用程式 (Xamarin)** 專案，如下所示：

[![新增專案 對話方塊](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

然後選取**空白應用程式**專案範本：

[![[選取範本] 對話方塊](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

空白專案範本會加入專案中的 4 個檔案：

[![專案檔](ios-code-only-images/empty-project.w157-sml.png "專案檔")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** -包含`UIApplicationDelegate`子類別， `AppDelegate` ，這用來處理從 iOS 應用程式事件。 在中建立應用程式視窗`AppDelegate`的`FinishedLaunching`方法。
1. **Main.cs** -包含指定的類別的應用程式的進入點的`AppDelegate`。
1. **Info.plist** -包含應用程式的組態資訊的屬性清單檔案。
1. **Entitlements.plist** – 包含的功能和應用程式的權限的相關資訊的屬性清單檔案。


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="ios-templates"></a>iOS 範本


Visual Studio for Mac 不提供空白的範本。 所有範本都隨附支援分鏡腳本，Apple 建議做為建立 UI 的主要方式。 不過，就可以完全在程式碼中建立您的 UI。 

下列步驟會引導您完成應用程式中移除分鏡腳本： 


1. 您可以使用單一檢視應用程式範本來建立新的 iOS 專案：

    [![](ios-code-only-images/single-view-app.png "使用單一檢視應用程式範本")](ios-code-only-images/single-view-app.png#lightbox)

1. 刪除`Main.Storyboard`和`ViewController.cs`檔案。 請勿**未**刪除`LaunchScreen.Storyboard`。 檢視控制器應該刪除，因為它是在分鏡腳本中建立檢視控制器的程式碼後置：
1. 請務必選取**刪除**從快顯對話方塊中：

    [![](ios-code-only-images/delete.png "從快顯對話方塊中選取 刪除")](ios-code-only-images/delete.png#lightbox)

1. 在 Info.plist 中，刪除內部資訊**部署資訊 > 主要介面**選項：

    [![](ios-code-only-images/main-interface.png "刪除的主要介面選項內的資訊")](ios-code-only-images/main-interface.png#lightbox)

1. 最後，新增下列程式碼，以您`FinishedLaunching`AppDelegate 類別中的方法：

        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            // create a new window instance based on the screen size
            window = new UIWindow(UIScreen.MainScreen.Bounds);

            // make the window visible
            window.MakeKeyAndVisible();

            return true;
        }

已新增至程式碼`FinishedLaunching`上述，步驟 5 中的方法是建立您的 iOS 應用程式的視窗所需的程式碼的最小數量。


-----

使用建置 iOS 應用程式[MVC 模式](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc)。 從視窗的根檢視控制器建立應用程式會顯示第一個畫面。 請參閱[Hello，iOS 多重畫面](~/ios/get-started/hello-ios-multiscreen/index.md)引導的更多有關 MVC 模式本身。

實作`AppDelegate`新增的範本建立應用程式 視窗中，其中只有一個每個 iOS 應用程式，並使其可見的下列程式碼：

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

如果您要執行此應用程式，現在，您可能會發生的例外狀況訊息，指出`Application windows are expected to have a root view controller at the end of application launch`。 讓我們加入一個控制器，並將其應用程式的根檢視控制器。

## <a name="adding-a-controller"></a>新增控制器

您的應用程式可以包含許多檢視控制器，但它需要有一個根檢視控制器來控制所有檢視控制器。  將控制器新增至 視窗中，藉由建立`UIViewController`執行個體，並將它設定為`window.RootViewController`屬性：

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

每個控制站有相關聯的檢視，因此可從`View`屬性。 上述程式碼變更的檢視`BackgroundColor`屬性設`UIColor.LightGray`，讓它將會顯示，如下所示：

 [![](ios-code-only-images/image1.png "檢視的背景是可見的淺灰色")](ios-code-only-images/image1.png#lightbox)

我們無法設定任何`UIViewController`子類別為`RootViewController`以這種方式，包括從 UIKit，以及我們自己寫的控制站。 例如，下列程式碼加入`UINavigationController`做為`RootViewController`:

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

這會產生巢狀瀏覽控制器，如下所示的控制站：

 [![](ios-code-only-images/image2.png "瀏覽控制器內的巢狀控制站")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>建立檢視控制器

既然我們已了解如何新增做為控制器`RootViewController`的視窗中，我們來看看如何在程式碼中建立自訂檢視控制器。

加入新的類別，名為`CustomViewController`，如下所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "新增名為 CustomViewController 類別")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](ios-code-only-images/new-file.png "新增名為 CustomViewController 類別")](ios-code-only-images/new-file.png#lightbox)

-----

類別應該繼承自`UIViewController`，這是在`UIKit`命名空間，如所示：

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

## <a name="initializing-the-view"></a>初始化檢視

`UIViewController` 包含呼叫的方法`ViewDidLoad`檢視控制器第一次載入記憶體時呼叫。 這是適當的位置，以進行檢視，例如設定其屬性的初始化。

例如，下列程式碼所加入的按鈕和推送新的檢視控制器，到導覽堆疊上，按下按鈕時的事件處理常式：

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

若要載入此應用程式中的控制站，並示範簡單的巡覽，建立的新執行個體`CustomViewController`。 建立新的瀏覽控制器、 傳入檢視的控制器執行個體並設定新的瀏覽控制器視窗`RootViewController`在`AppDelegate`和以前一樣：

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

現在應用程式載入，`CustomViewController`內瀏覽控制器載入：

 [![](ios-code-only-images/customvc.png "在瀏覽控制器載入 CustomViewController")](ios-code-only-images/customvc.png#lightbox)
 
按一下按鈕，將_推播_到導覽堆疊上新的檢視控制器：

[![](ios-code-only-images/customvca.png "新的檢視控制器推送至導覽堆疊")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>建置檢視階層

在上述範例中，我們開始建立程式碼中的使用者介面，藉由將檢視控制器中的按鈕。

iOS 使用者介面是由檢視階層所組成。 額外的檢視，例如標籤、 按鈕、 滑桿 」 等會新增為子檢視的某些父檢視。

例如，讓我們編輯`CustomViewController`建立登入畫面，其中使用者可以輸入使用者名稱和密碼。 畫面將會包含兩個文字欄位和一個按鈕。

### <a name="adding-the-text-fields"></a>新增在文字欄位

首先，移除已加入的按鈕和事件處理常式[初始化檢視](#Initializing_the_View)一節。 

新增使用者名稱的控制項，藉由建立並初始化`UITextField`然後將它們新增至檢視階層，如下所示：

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

當我們建立`UITextField`，我們將設定`Frame`屬性來定義其位置和大小。 在 iOS 中的 0，0 的座標是右邊的左上方的 + x 和 + y 下的。 設定後`Frame`幾個其他屬性，我們會呼叫`View.AddSubview`以新增`UITextField`至檢視階層。 這可讓`usernameField`的子檢視`UIView`執行個體`View`屬性參考。 子檢視會新增具有會高於父檢視，使其出現在螢幕上的父檢視前面疊置順序。

應用程式與`UITextField`包含如下所示：

 [![](ios-code-only-images/image4.png "包含 UITextField 應用程式")](ios-code-only-images/image4.png#lightbox)

我們可以加上`UITextField`針對類似的方式中的密碼，只是這次我們設定`SecureTextEntry`屬性設定為 true，如下所示：

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

設定`SecureTextEntry = true`會隱藏在文字方塊中輸入`UITextField`使用者，如下所示：

 [![](ios-code-only-images/image4a.png "設定 SecureTextEntry true 會隱藏使用者所輸入的文字")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>加入按鈕

接下來，我們將新增按鈕，讓使用者可以提交的使用者名稱和密碼。 [] 按鈕加入至檢視階層，如同任何其他的控制項，做為引數傳遞給父檢視的`AddSubview`方法一次。

下列程式碼會將按鈕新增和註冊事件處理常式`TouchUpInside`事件：

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

上述作業時，登入畫面現在看起來如下所示：

 [![](ios-code-only-images/image5.png "登入畫面")](ios-code-only-images/image5.png#lightbox)

不同於在舊版的 iOS、 預設按鈕的背景是透明的。 變更按鈕的`BackgroundColor`屬性變更此：

```csharp
submitButton.BackgroundColor = UIColor.White;
```

這會導致方形按鈕，而不是一般的圓角邊的按鈕。 若要取得的圓角的邊緣，使用下列程式碼片段：

```csharp
submitButton.Layer.CornerRadius = 5f;
```

經過這些變更，檢視看起來像這樣：

[![](ios-code-only-images/image6.png "檢視執行的範例")](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>將多個檢視加入至檢視階層

iOS，來提供要加入的檢視階層中的多個檢視，使用設備`AddSubviews`。

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton }); 
```

## <a name="adding-button-functionality"></a>加入按鈕的功能

按一下按鈕時，您的使用者會預期發生某件事。 比方說，會顯示警示，或瀏覽至另一個螢幕執行。

讓我們新增一些程式碼來將第二個檢視控制器推送到導覽堆疊上。

首先，建立第二個檢視控制器：

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

然後，將功能新增至`TouchUpInside`事件：

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

瀏覽如下所示：

[![](ios-code-only-images/navigation.png "此圖表將說明導覽")](ios-code-only-images/navigation.png#lightbox)

請注意，根據預設，當您使用 巡覽控制器，iOS 應用程式提供瀏覽列和上一頁按鈕，以讓您向後移動堆疊。

## <a name="iterating-through-the-view-hierarchy"></a>逐一查看的檢視階層

可以逐一查看子檢視階層並挑出任何特定的檢視。 例如，若要尋找每個`UIButton`並提供該按鈕不同`BackgroundColor`，可以使用下列程式碼片段

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

不過將無法運作進行逐一查看的檢視是否`UIView`為所有的檢視會回復成正在`UIView`物件加入至父檢視本身繼承`UIView`。

## <a name="handling-rotation"></a>處理旋轉

如果在使用者旋轉裝置為橫向，控制項不調整大小，如下列螢幕擷取畫面所示：

[![](ios-code-only-images/image7.png "如果在使用者旋轉裝置為橫向，控制項不調整大小適當")](ios-code-only-images/image7.png#lightbox)

若要修正此問題的一種方法是設定`AutoresizingMask`上每個檢視的屬性。 在此情況下我們想要的控制項要水平延展因此我們會將每個`AutoresizingMask`。 下列範例是針對`usernameField`，但相同必須套用至檢視階層中每個小工具。

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

現在當我們在旋轉裝置或模擬器時，所有項目會自動縮放以填滿額外的空間，如下所示：

[![](ios-code-only-images/image8.png "所有控制項都伸展以填滿的額外空間")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>建立自訂檢視

除了使用屬於 UIKit 控制項，也可以使用自訂的檢視。 可以建立自訂檢視，藉由繼承自`UIView`，並覆寫`Draw`。 讓我們來建立自訂檢視，並將它新增至檢視階層，來示範。

### <a name="inheriting-from-uiview"></a>繼承自 UIView

我們要做的第一件事是建立自訂檢視的類別。 我們這樣使用**類別**以新增空的類別名為 Visual Studio 中的範本`CircleView`。 基底類別應設定為`UIView`，我們還記得這是在`UIKit`命名空間。 我們也需要`System.Drawing`以及命名空間。 其他各種`System.*`命名空間將不會使用在此範例中，不妨將它們移除。

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

每隔`UIView`具有`Draw`需要繪製時，會將由系統呼叫的方法。 `Draw` 應該永遠不會直接呼叫。 執行的迴圈處理期間，它會呼叫系統。 第一次執行迴圈時檢視加入至檢視階層之後, 它`Draw`呼叫方法。 後續呼叫`Draw`檢視會標示為需要繪製呼叫時，會發生`SetNeedsDisplay`或`SetNeedsDisplayInRect`檢視上。

我們也可以新增內部覆寫這類程式碼到我們的檢視新增的繪圖程式碼`Draw`方法，如下所示：

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

由於`CircleView`已`UIView`，我們也可以設定`UIView`屬性。 比方說，我們可以設定`BackgroundColor`建構函式：

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

若要使用`CircleView`我們剛剛建立，我們可以將它加入為子檢視來檢視階層架構中現有的控制站，以`UILabels`和`UIButton`更早版本，或者我們可以將它載入新的控制器的檢視。 讓我們採用後者。

### <a name="loading-a-view"></a>載入檢視

 `UIViewController` 有一個名為方法`LoadView`，而若要建立其檢視控制器所呼叫。 這是建立檢視，並將它指派給控制器的適當位置`View`屬性。

首先，我們需要一個控制站，因此建立新的空類別，名為`CircleController`。

在`CircleController`新增下列程式碼，以設定`View`要`CircleView`(您不應該呼叫`base`覆寫中的實作):

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

最後，我們需要在執行階段呈現控制器。 讓我們執行這項操作上我們新增稍早，如下所示的 [提交] 按鈕新增事件處理常式：

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

現在，當我們執行應用程式，並點選 [提交] 按鈕，就會顯示圓形的新檢視：

[![](ios-code-only-images/circles.png "顯示圓形的新檢視")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>建立啟動畫面

A[啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)做為顯示給您的使用者是回應式的方式啟動您的應用程式時，會顯示。 正在載入您的應用程式時，會顯示啟動畫面，因為它無法建立程式碼中為應用程式仍在載入到記憶體。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

當您在 Visual Studio 中建立 iOS 專案時，為您.xib 檔案，可以在中找到的形式提供啟動螢幕**資源**專案內的資料夾。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

當您建立的 iOS 專案在 Visual Studio for Mac 時，就會為您的分鏡腳本檔案形式提供啟動螢幕。

-----

這可以編輯 double，則按一下它，並在 iOS 設計工具中開啟。

Apple 建議.xib 或分鏡腳本檔案，用於以 iOS 8 為目標的應用程式，或更新版本中，當您啟動 iOS 設計工具中的任一檔案時，您將使用大小類別和自動版面配置來調整您的版面配置，使其看起來沒問題，並正確地顯示所有裝置大小。 靜態啟動映像可以使用除了.xib 或分鏡腳本，允許以較早的版本為目標的應用程式的支援。

如需有關如何建立啟動螢幕的詳細資訊，請參閱下列文件：

- [建立使用.xib 的啟動螢幕](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [管理分鏡腳本與啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> 從 iOS 9，Apple 建議分鏡腳本應該用作建立啟動螢幕的主要方法。

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>建立啟動映像前 ios 8 的應用程式

靜態影像可以使用除了.xib 或分鏡腳本啟動畫面，如果您的應用程式的目標 iOS 8 之前的版本。 

在 Info.plist 檔案中，或為您的應用程式中的 （適用於 iOS 7) 的資產目錄，則可以設定此靜態映像。 您必須為每個裝置大小 （320x480，640 x 960，640 x 1136） 上執行您的應用程式，可能會提供不同的映像。 如需有關啟動螢幕大小的詳細資訊，請檢視[啟動螢幕影像](~/ios/app-fundamentals/images-icons/launch-screens.md)指南。

> [!IMPORTANT]
> 如果您的應用程式不有任何啟動畫面，您可能會注意到，它不完全符合螢幕大小。 如果發生這種情況，您應該確定至少，包含名為 640 x 1136 映像`Default-568@2x.png`至您的 Info.plist。 

## <a name="summary"></a>總結

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

這篇文章討論如何開發以程式設計方式在 Visual Studio 中的 iOS 應用程式。 我們已經了解如何建置專案，以從空白專案範本，討論如何建立並加入至視窗的根檢視控制器。 然後，我們會示範如何使用從 UIKit 控制項來建立控制器內的檢視階層，開發應用程式畫面。 接下來我們檢查如何讓檢視版面配置適當地在不同的方向和我們了解如何建立自訂檢視，子類別化`UIView`、 以及如何將控制器內的檢視。 最後，我們會探討如何將啟動畫面新增至應用程式。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

這篇文章討論如何開發 iOS 應用程式以程式設計方式在 Visual Studio for mac。 我們已經了解如何建置專案，以從單一檢視範本，討論如何建立並加入至視窗的根檢視控制器。 然後，我們會示範如何使用從 UIKit 控制項來建立控制器內的檢視階層，開發應用程式畫面。 接下來我們檢查如何讓檢視版面配置適當地在不同的方向和我們了解如何建立自訂檢視，子類別化`UIView`、 以及如何將控制器內的檢視。 最後，我們會探討如何將啟動畫面新增至應用程式。

-----

## <a name="related-links"></a>相關連結

- [SimpleLogin （範例）](https://developer.xamarin.com/samples/monotouch/SimpleLogin)

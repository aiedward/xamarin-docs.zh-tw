---
title: 在 Xamarin 的程式碼中建立 iOS 使用者介面
description: 本檔說明如何使用程式碼來建立適用于 Xamarin iOS 應用程式的使用者介面。 它會討論視圖控制器、建立視圖階層、處理旋轉等等。
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: 7b6852485fed6cc14c9f9b2e1a303b7c2e576da9
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433584"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>在 Xamarin 的程式碼中建立 iOS 使用者介面

IOS 應用程式的使用者介面就像店面–應用程式通常會取得一個視窗，但它可以在視窗中填入所需數量的物件，而且可以根據應用程式要顯示的內容來變更物件和相片順序。 此案例中的物件 (使用者所看見的內容) 稱為「檢視」。 為了在應用程式中建置單一畫面，檢視會在「內容檢視階層」中彼此堆疊，並由單一檢視控制器管理該階層。 具有多個畫面的應用程式會有多個內容檢視階層，每個都有它自己的檢視控制器，而應用程式會將檢視放置於視窗中，以根據使用者所在畫面建立不同的內容檢視階層。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

下圖說明視窗、檢視、子檢視及檢視控制器之間的關聯性，以在裝置畫面中顯示使用者介面：

[![下圖說明視窗、Views、子檢視和 View Controller 之間的關聯性](ios-code-only-images/image9.png)](ios-code-only-images/image9.png#lightbox)

您可以使用 Visual Studio 中的 [Xamarin Designer for iOS](~/ios/user-interface/designer/index.md) 來建立這些視圖階層，不過最好先瞭解如何在程式碼中工作。 本文將逐步解說一些基本要點，以啟動並執行僅限程式碼的使用者介面開發。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

下圖說明視窗、檢視、子檢視及檢視控制器之間的關聯性，以在裝置畫面中顯示使用者介面：

[![下圖說明視窗、Views、子檢視和 View Controller 之間的關聯性](ios-code-only-images/image9.png)](ios-code-only-images/image9.png#lightbox)

您可以使用 Visual Studio for Mac 中的 [Xamarin Designer for iOS](~/ios/user-interface/designer/index.md) 來建立這些視圖階層，不過最好先瞭解如何在程式碼中工作。 本文將逐步解說一些基本要點，以啟動並執行僅限程式碼的使用者介面開發。

-----

## <a name="creating-a-code-only-project"></a>建立僅限程式碼的專案

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>iOS 空白專案範本

首先，使用檔案 > 新專案，在 Visual Studio 中建立 iOS 專案 **> Visual c # > iPhone & iPad > IOS 應用程式 (Xamarin) ** 專案，如下所示：

[![[新增專案] 對話方塊](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

然後選取 [ **空白應用程式** ] 專案範本：

[![選取範本對話方塊](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

空白專案範本會將4個檔案新增至專案：

[![專案檔](ios-code-only-images/empty-project.w157-sml.png "專案檔")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** -包含子  `UIApplicationDelegate` 類別，  `AppDelegate` 用來處理來自 iOS 的應用程式事件。 應用程式視窗會建立在的 `AppDelegate`  `FinishedLaunching` 方法中。
1. **Main.cs** -包含應用程式的進入點，此應用程式會指定的類別  `AppDelegate` 。
1. **Plist** -包含應用程式設定資訊的屬性清單檔。
1. **Plist** –屬性清單檔案，其中包含應用程式功能和許可權的相關資訊。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="ios-templates"></a>iOS 範本

Visual Studio for Mac 不提供空白的範本。 所有範本都隨附分鏡腳本支援，Apple 建議您以這種方式建立 UI。 不過，您可以完全以程式碼建立您的 UI。

下列步驟將引導您從應用程式移除分鏡腳本：

1. 使用「單一視圖」應用程式範本來建立新的 iOS 專案：

    [![使用單一視圖應用程式範本](ios-code-only-images/single-view-app.png)](ios-code-only-images/single-view-app.png#lightbox)

1. 刪除 `Main.Storyboard` 和檔案 `ViewController.cs` 。 請勿 **刪除** `LaunchScreen.Storyboard` 。 視圖控制器應該刪除，因為它是在腳本中建立之 view controller 的程式碼後置於：
1. 請務必從快顯對話方塊中選取 [ **刪除** ]：

    [![從快顯對話方塊中選取 [刪除]](ios-code-only-images/delete.png)](ios-code-only-images/delete.png#lightbox)

1. 在 plist 中，刪除 [ **部署資訊] > 主要介面** 選項中的資訊：

    [![刪除主要介面選項內的資訊](ios-code-only-images/main-interface.png)](ios-code-only-images/main-interface.png#lightbox)

1. 最後，將下列程式碼新增至 `FinishedLaunching` AppDelegate 類別中的方法：

    ```csharp
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
    ```

在 `FinishedLaunching` 上述步驟5中新增至方法的程式碼，是為您的 iOS 應用程式建立視窗所需的最少程式碼數量。

-----

iOS 應用程式是使用 [MVC 模式](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc)建立。 應用程式所顯示的第一個畫面是從視窗的根視圖控制器建立。 如需 MVC 模式本身的詳細資訊，請參閱 [Hello，IOS 多重畫面](~/ios/get-started/hello-ios-multiscreen/index.md) 指南。

`AppDelegate`範本所加入之的執行會建立應用程式視窗，其中每個 iOS 應用程式只會有一個應用程式，並使用下列程式碼來顯示它：

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
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

如果您現在執行這個應用程式，您可能會收到一則擲回的例外狀況，指出 `Application windows are expected to have a root view controller at the end of application launch` 。 讓我們新增控制器，並將其設為應用程式的根視圖控制器。

## <a name="adding-a-controller"></a>新增控制器

您的應用程式可以包含許多視圖控制器，但需要有一個根視圖控制器來控制所有視圖控制器。  藉由建立 `UIViewController` 實例並將它設定為屬性，將控制器加入至視窗 `Window.RootViewController` ：

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

每個控制器都有相關聯的視圖，可從 `View` 屬性存取。 上述程式碼會將 view 的 `BackgroundColor` 屬性變更為 `UIColor.LightGray` 可見，如下所示：

 [![視圖的背景是可見的淺灰色](ios-code-only-images/image1.png)](ios-code-only-images/image1.png#lightbox)

我們 `UIViewController` 也可以用這種方式將任何子類別設定為 `RootViewController` ，包括來自 UIKit 的控制器，以及我們自己撰寫的控制器。 例如，下列程式碼會將新增 `UINavigationController` 為 `RootViewController` ：

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

這會產生內嵌在流覽控制器內的控制器，如下所示：

 [![內嵌在流覽控制器內的控制器](ios-code-only-images/image2.png)](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>建立視圖控制器

現在我們已瞭解如何新增控制器作為 `RootViewController` 視窗的，讓我們來看看如何在程式碼中建立自訂視圖控制器。

新增名為的新類別 `CustomViewController` ，如下所示：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![加入名為 CustomViewController 的新類別](ios-code-only-images/customviewcontroller.w157-sml.png)](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![加入名為 CustomViewController 的新類別](ios-code-only-images/new-file.png)](ios-code-only-images/new-file.png#lightbox)

-----

類別應該繼承自 `UIViewController` 命名空間中的， `UIKit` 如下所示：

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

## <a name="initializing-the-view"></a>初始化視圖

`UIViewController` 包含名為的方法 `ViewDidLoad` ，此方法會在視圖控制器第一次載入至記憶體時呼叫。 這是可讓您初始化視圖的適當位置，例如設定其屬性。

例如，下列程式碼會在按下按鈕時，新增一個按鈕和一個事件處理常式，以將新的視圖控制器推送至導覽堆疊：

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

若要在您的應用程式中載入此控制器，並示範簡單的導覽，請建立的新實例 `CustomViewController` 。 建立新的流覽控制器，並傳入您的 view controller 實例，並將新的流覽控制器設定為中的視窗， `RootViewController` `AppDelegate` 如同之前一樣：

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

現在，當應用程式載入時， `CustomViewController` 會在流覽控制器內載入：

 [![CustomViewController 會載入流覽控制器內部](ios-code-only-images/customvc.png)](ios-code-only-images/customvc.png#lightbox)

按一下該按鈕，就會將新的視圖控制器 _推送_ 至導覽堆疊：

[![推送至導覽堆疊的新 View 控制器](ios-code-only-images/customvca.png)](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>建立視圖階層

在上述範例中，我們已開始在程式碼中建立使用者介面，方法是將按鈕新增至 View Controller。

iOS 使用者介面由一個 view 階層組成。 其他的視圖（例如標籤、按鈕、滑杆等）會新增為某些父視圖的子檢視。

例如，讓我們編輯 `CustomViewController` 以建立登入畫面，讓使用者可以在其中輸入使用者名稱和密碼。 畫面將包含兩個文字欄位和一個按鈕。

### <a name="adding-the-text-fields"></a>加入文字欄位

首先，移除在 [ [初始化視圖](#initializing-the-view) ] 區段中加入的按鈕和事件處理常式。

藉由建立和初始化 `UITextField` ，然後將它新增至 view 階層，來新增使用者名稱的控制項，如下所示：

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

當我們建立時 `UITextField` ，我們會設定 `Frame` 屬性來定義其位置和大小。 在 iOS 中的0，0座標是在左上方，右邊 + x 向下 + y。 在設定 `Frame` 和一些其他屬性之後，我們會呼叫， `View.AddSubview` 將加入 `UITextField` 至 view 階層。 這會使 `usernameField` `UIView` 屬性參考之實例的子視圖 `View` 。 加入子視圖時的迭置順序高於其父視圖，因此它會出現在螢幕上父視圖的前方。

包含包含的應用程式如下 `UITextField` 所示：

 [![包含 UITextField 的應用程式](ios-code-only-images/image4.png)](ios-code-only-images/image4.png#lightbox)

我們可以 `UITextField` 用類似的方式加入密碼，但這次我們會將 `SecureTextEntry` 屬性設定為 true，如下所示：

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

設定會 `SecureTextEntry = true` 隱藏使用者輸入的文字 `UITextField` ，如下所示：

 [![設定 SecureTextEntry true 會隱藏使用者輸入的文字](ios-code-only-images/image4a.png)](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>新增按鈕

接下來，我們將新增一個按鈕，讓使用者可以提交使用者名稱和密碼。 按鈕會新增至 view 階層，就像任何其他控制項一樣，方法是將它當作引數傳遞給父視圖的 `AddSubview` 方法。

下列程式碼會新增按鈕並註冊事件的事件處理常式 `TouchUpInside` ：

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

有了這項功能，登入畫面現在就會出現，如下所示：

 [![登入畫面](ios-code-only-images/image5.png)](ios-code-only-images/image5.png#lightbox)

不同于舊版的 iOS，預設按鈕背景是透明的。 變更按鈕的 `BackgroundColor` 屬性會變更：

```csharp
submitButton.BackgroundColor = UIColor.White;
```

這會產生一個方形按鈕，而不是一般的圓角邊按鈕。 若要取得圓角邊緣，請使用下列程式碼片段：

```csharp
submitButton.Layer.CornerRadius = 5f;
```

有了這些變更之後，視圖看起來會像這樣：

[![此視圖的範例執行](ios-code-only-images/image6.png)](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>將多個視圖新增至視圖階層

iOS 提供了一項功能，可讓您使用將多個視圖新增至視圖階層 `AddSubviews` 。

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>新增按鈕功能

當您按一下按鈕時，您的使用者會預期會發生一些狀況。 例如，系統會顯示警示，或執行流覽至另一個畫面。

讓我們新增一些程式碼，以將第二個 view 控制器推送至導覽堆疊上。

首先，建立第二個 view 控制器：

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

然後，將功能新增至 `TouchUpInside` 事件：

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

導覽如下所示：

[![此圖表說明導覽](ios-code-only-images/navigation.png)](ios-code-only-images/navigation.png#lightbox)

請注意，根據預設，當您使用流覽控制器時，iOS 會為應用程式提供導覽列和 [上一頁] 按鈕，讓您可以在堆疊中移回。

## <a name="iterating-through-the-view-hierarchy"></a>逐一查看視圖階層

您可以逐一查看子視圖階層，並挑選任何特定的觀點。 例如，若要尋找每個 `UIButton` 按鈕並為其提供不同的 `BackgroundColor` ，可使用下列程式碼片段

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

但是，如果要進行反覆運算的視圖是，則無法使用，因為 `UIView` 所有視圖都會回到，因為 `UIView` 加入父視圖本身的物件會繼承 `UIView` 。

## <a name="handling-rotation"></a>處理旋轉

如果使用者將裝置旋轉為橫向，則控制項不會適當地調整大小，如下列螢幕擷取畫面所示：

[![如果使用者將裝置旋轉為橫向，控制項就不會適當地調整大小](ios-code-only-images/image7.png)](ios-code-only-images/image7.png#lightbox)

修正此問題的方法之一是 `AutoresizingMask` 在每個視圖上設定屬性。 在此情況下，我們希望控制項水準延展，所以我們會設定每個控制項 `AutoresizingMask` 。 下列範例適用于 `usernameField` ，但相同的範例必須套用至 view 階層中的每個小工具。

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

現在，當我們旋轉裝置或模擬器時，所有專案都會延伸以填滿額外的空間，如下所示：

[![所有控制項都伸展以填滿額外的空間](ios-code-only-images/image8.png)](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>建立自訂視圖

除了使用屬於 UIKit 一部分的控制項之外，也可以使用自訂的視圖。 自訂視圖可透過繼承自和覆 `UIView` 寫來建立 `Draw` 。 讓我們建立自訂視圖，並將其新增至要示範的視圖階層。

### <a name="inheriting-from-uiview"></a>從 UIView 繼承

我們需要做的第一件事，就是建立自訂視圖的類別。 我們會使用 Visual Studio 中的 **類別** 樣板來新增名為的空白類別，以進行這項作業 `CircleView` 。 基類應設定為 `UIView` ，我們回想在 `UIKit` 命名空間中。 我們也需要 `System.Drawing` 命名空間。 在 `System.*` 此範例中，不會使用其他的不同命名空間，因此您可以隨意移除它們。

該類別看起來應該如下所示：

```csharp
using System;

namespace CodeOnlyDemo
{
    class CircleView : UIView
    {
    }
}
```

### <a name="drawing-in-a-uiview"></a>在 UIView 中繪圖

每個 `UIView` 都有一個 `Draw` 系統在需要繪製時所呼叫的方法。 `Draw` 永遠不應該直接呼叫。 它是由系統在執行迴圈處理期間呼叫。 第一次在將 view 新增至 view 階層之後，執行迴圈的第一次， `Draw` 會呼叫其方法。 `Draw`當視圖被標示為需要透過呼叫 `SetNeedsDisplay` 或在視圖上繪製時，會發生後續的呼叫 `SetNeedsDisplayInRect` 。

我們可以在覆寫的方法內新增此類程式碼，以將繪圖程式碼新增至我們的觀點， `Draw` 如下所示：

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

由於 `CircleView` 是 `UIView` ，我們也可以設定 `UIView` 屬性。 例如，我們可以在函式 `BackgroundColor` 中設定：

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

若要使用 `CircleView` 剛才建立的，我們可以將它以子視圖的形式新增至現有控制器中的 view 階層，如同我們先前所做的一樣， `UILabels` `UIButton` 也可以將它載入為新控制器的視圖。 讓我們來進行後者。

### <a name="loading-a-view"></a>載入視圖

 `UIViewController` 具有一個名為 `LoadView` 的方法，此方法會由控制器呼叫，以建立其視圖。 這是建立視圖並將其指派給控制器屬性的適當位置 `View` 。

首先，我們需要一個控制器，因此請建立一個名為的新空類別 `CircleController` 。

在 `CircleController` [加入下列程式碼] 中，將設定 `View` 為 `CircleView` (您不應該 `base` 在覆寫) 中呼叫實作為：

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

最後，我們需要在執行時間呈現控制器。 讓我們在稍早新增的提交按鈕上新增事件處理常式，如下所示：

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

現在，當我們執行應用程式並按一下 [提交] 按鈕時，會顯示具有圓形的新視圖：

[![顯示具有圓形的新視圖](ios-code-only-images/circles.png)](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>建立啟動畫面

當您的應用程式啟動時，會顯示 [啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md) ，以向您的使用者顯示其回應性。 由於啟動畫面會在應用程式載入時顯示，因此無法在程式碼中建立，因為應用程式仍會載入記憶體中。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

當您在 Visual Studio 中建立 iOS 專案時，系統會以 xib 檔案的形式為您提供啟動畫面，您可以在專案內的 [ **Resources** ] 資料夾中找到此檔案。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

當您在 Visual Studio for Mac 中建立 iOS 專案時，會以分鏡腳本檔案的形式為您提供啟動畫面。

-----

您可以按兩下，然後在 iOS 設計工具中開啟它來編輯。

Apple 建議將 xib 或分鏡腳本檔案用於以 iOS 8 或更新版本為目標的應用程式，當您在 iOS 設計工具中啟動任一檔案時，您將會使用大小類別和自動設定來調整版面配置，使其看起來正確，並針對所有裝置大小正確地顯示。 除了 xib 或分鏡腳本之外，還可以使用靜態啟動映射來允許以較早版本為目標的應用程式支援。

如需有關建立啟動畫面的詳細資訊，請參閱下列檔：

- [使用 xib 建立啟動畫面](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [使用分鏡腳本管理啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> 從 iOS 9 開始，Apple 建議您使用分鏡腳本作為建立啟動畫面的主要方法。

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>建立 iOS 前8個應用程式的啟動映射

如果您的應用程式是以 iOS 8 之前的版本為目標，則除了 xib 或分鏡腳本啟動畫面之外，還可以使用靜態映射。

您可以在 plist 檔案中設定此靜態映射，也可以在應用程式中以 iOS 7) 的資產目錄 (來設定。 您必須為每個裝置大小提供個別的映射 (您的應用程式可能會在其上執行的320x480、640x960、640x1136) 。 如需啟動畫面大小的詳細資訊，請參閱 [啟動畫面影像](~/ios/app-fundamentals/images-icons/launch-screens.md) 指南。

> [!IMPORTANT]
> 如果您的應用程式沒有啟動畫面，您可能會注意到它未完全符合畫面。 如果是這種情況，您應該務必至少包含一個名為 plist 的640x1136 映射 `Default-568@2x.png` 。

## <a name="summary"></a>摘要

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

本文討論如何在 Visual Studio 中以程式設計方式開發 iOS 應用程式。 我們已探討如何從空白專案範本建立專案，並討論如何建立根視圖控制器並將其加入至視窗。 接著，我們會示範如何使用 UIKit 中的控制項來建立控制器內的 view 階層，以開發應用程式畫面。 接下來，我們將探討如何以不同的方向適當地配置視圖，並瞭解如何藉由子類別化來建立自訂視圖 `UIView` ，以及如何在控制器中載入此視圖。 最後，我們探討了如何將啟動畫面新增至應用程式。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

本文討論如何在 Visual Studio for Mac 中以程式設計方式開發 iOS 應用程式。 我們已探討如何從單一視圖範本建立專案，並討論如何建立和新增根視圖控制器至視窗。 接著，我們會示範如何使用 UIKit 中的控制項來建立控制器內的 view 階層，以開發應用程式畫面。 接下來，我們將探討如何以不同的方向適當地配置視圖，並瞭解如何藉由子類別化來建立自訂視圖 `UIView` ，以及如何在控制器中載入此視圖。 最後，我們探討了如何將啟動畫面新增至應用程式。

-----

## <a name="related-links"></a>相關連結

- [SimpleLogin (範例) ](/samples/xamarin/ios-samples/simplelogin)
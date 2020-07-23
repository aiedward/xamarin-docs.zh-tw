---
title: 在 Xamarin 的程式碼中建立 iOS 使用者介面
description: 本檔說明如何使用程式碼來建立 Xamarin iOS 應用程式的使用者介面。 其中討論視圖控制器、建立視圖階層、處理旋轉等。
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: 42d90175b6ddc005aa34b9fb7ae558741e4a4642
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930633"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>在 Xamarin 的程式碼中建立 iOS 使用者介面

IOS 應用程式的使用者介面就像店面，應用程式通常會取得一個視窗，但它可以在視窗中填入所需的物件數目，而且可以根據應用程式所要顯示的內容來變更物件和相片順序。 此案例中的物件 (使用者所看見的內容) 稱為「檢視」。 為了在應用程式中建置單一畫面，檢視會在「內容檢視階層」中彼此堆疊，並由單一檢視控制器管理該階層。 具有多個畫面的應用程式會有多個內容檢視階層，每個都有它自己的檢視控制器，而應用程式會將檢視放置於視窗中，以根據使用者所在畫面建立不同的內容檢視階層。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

下圖說明視窗、檢視、子檢視及檢視控制器之間的關聯性，以在裝置畫面中顯示使用者介面：

[![下圖說明視窗、Views、子檢視和 View Controller 之間的關聯性](ios-code-only-images/image9.png)](ios-code-only-images/image9.png#lightbox)

您可以使用 Visual Studio 中的[Xamarin Designer for iOS](~/ios/user-interface/designer/index.md)來建立這些視圖階層架構，但對於如何在程式碼中完全瞭解，是很好的。 本文逐步解說一些基本重點，讓您能夠以僅限程式碼的使用者介面開發來啟動並執行。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

下圖說明視窗、檢視、子檢視及檢視控制器之間的關聯性，以在裝置畫面中顯示使用者介面：

[![下圖說明視窗、Views、子檢視和 View Controller 之間的關聯性](ios-code-only-images/image9.png)](ios-code-only-images/image9.png#lightbox)

您可以使用 Visual Studio for Mac 中的[Xamarin Designer for iOS](~/ios/user-interface/designer/index.md)來建立這些視圖階層架構，但對於如何在程式碼中完全瞭解，是很好的。 本文逐步解說一些基本重點，讓您能夠以僅限程式碼的使用者介面開發來啟動並執行。

-----

## <a name="creating-a-code-only-project"></a>建立僅限程式碼的專案

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>iOS 空白專案範本

首先，使用檔案 **> 新專案 > Visual c # > iPhone & iPad > IOS 應用程式（Xamarin）** 專案，在 Visual Studio 中建立 iOS 專案，如下所示：

[![[新增專案] 對話方塊](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

然後選取 [**空白應用程式**] 專案範本：

[![選取範本對話方塊](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

[空白專案] 範本會將4個檔案新增至專案：

[![專案檔](ios-code-only-images/empty-project.w157-sml.png "專案檔")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** -包含子 `UIApplicationDelegate` 類別， `AppDelegate` 用來處理 iOS 的應用程式事件。 應用程式視窗會建立在 `AppDelegate` 的 `FinishedLaunching` 方法中。
1. **Main.cs** -包含應用程式的進入點，它會指定的類別 `AppDelegate` 。
1. **Plist** -包含應用程式設定資訊的屬性清單檔案。
1. **Plist** –屬性清單檔案，其中包含應用程式的功能和許可權的相關資訊。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="ios-templates"></a>iOS 範本

Visual Studio for Mac 未提供空的範本。 所有範本都隨附分鏡腳本支援，Apple 建議這種方式來建立 UI。 不過，您可以完全以程式碼建立您的 UI。

下列步驟會引導您從應用程式移除分鏡腳本：

1. 使用單一 View 應用程式範本來建立新的 iOS 專案：

    [![使用單一視圖應用程式範本](ios-code-only-images/single-view-app.png)](ios-code-only-images/single-view-app.png#lightbox)

1. 刪除 `Main.Storyboard` 和檔案 `ViewController.cs` 。 請勿**刪除** `LaunchScreen.Storyboard` 。 應刪除視圖控制器，因為它是在分鏡腳本中建立之 view Controller 的程式碼後置：
1. 請務必從快顯對話方塊中選取 [**刪除**]：

    [![從快顯對話方塊中選取 [刪除]](ios-code-only-images/delete.png)](ios-code-only-images/delete.png#lightbox)

1. 在 plist 中，刪除 [**部署資訊] > [主要介面**] 選項中的資訊：

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

在 `FinishedLaunching` 上述步驟5中新增至方法的程式碼，是為您的 iOS 應用程式建立視窗時所需的最小程式代碼數量。

-----

iOS 應用程式是使用[MVC 模式](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc)所建立。 應用程式所顯示的第一個畫面是從視窗的根視圖控制器建立的。 如需 MVC 模式本身的詳細資訊，請參閱[Hello，IOS 多重畫面](~/ios/get-started/hello-ios-multiscreen/index.md)guide。

`AppDelegate`範本所新增之的執行會建立應用程式視窗，其中只有每個 iOS 應用程式都有一個，並可使用下列程式碼顯示：

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

如果您現在要執行此應用程式，您可能會收到擲回的例外狀況，指出 `Application windows are expected to have a root view controller at the end of application launch` 。 讓我們新增一個控制器，並將它設為應用程式的根視圖控制器。

## <a name="adding-a-controller"></a>新增控制器

您的應用程式可以包含許多視圖控制器，但它需要有一個根視圖控制器來控制所有的視圖控制器。  藉由建立 `UIViewController` 實例並將其設定為屬性，將控制器新增至視窗 `Window.RootViewController` ：

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

每個控制器都有相關聯的視圖，可從 `View` 屬性存取。 上述程式碼會將視圖的 `BackgroundColor` 屬性變更為， `UIColor.LightGray` 使其可見，如下所示：

 [![](ios-code-only-images/image1.png "The View's background is a visible light gray")](ios-code-only-images/image1.png#lightbox)

我們 `UIViewController` 也可以用這種方式來設定任何子類別 `RootViewController` ，包括來自 UIKit 的控制器，以及我們自己撰寫的控制項。 例如，下列程式碼會將新增 `UINavigationController` 為 `RootViewController` ：

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

 [![在流覽控制器內嵌套的控制器](ios-code-only-images/image2.png)](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>建立視圖控制器

現在，我們已瞭解如何將控制器新增為視窗的 `RootViewController` ，讓我們來瞭解如何在程式碼中建立自訂視圖控制器。

新增名為的新類別 `CustomViewController` ，如下所示：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![新增名為 CustomViewController 的類別](ios-code-only-images/customviewcontroller.w157-sml.png)](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![新增名為 CustomViewController 的類別](ios-code-only-images/new-file.png)](ios-code-only-images/new-file.png#lightbox)

-----

類別應該繼承自 `UIViewController` ，其位於 `UIKit` 命名空間中，如下所示：

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

`UIViewController`包含名為 `ViewDidLoad` 的方法，它會在第一次將 View controller 載入至記憶體時呼叫。 這是執行視圖初始化的適當位置，例如設定其屬性。

例如，下列程式碼會在按下按鈕時，加入一個按鈕和一個事件處理常式，以將新的 View Controller 推送至導覽堆疊：

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

若要在您的應用程式中載入此控制器，並示範簡單的導覽，請建立的新實例 `CustomViewController` 。 建立新的流覽控制器，傳入您的 view controller 實例，並將新的流覽控制器設定為中的視窗， `RootViewController` `AppDelegate` 如同之前：

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

現在，當應用程式載入時， `CustomViewController` 會載入至流覽控制器內：

 [![CustomViewController 會載入至流覽控制器內](ios-code-only-images/customvc.png)](ios-code-only-images/customvc.png#lightbox)

按一下按鈕，就會將新的視圖控制器_推送_至流覽堆疊：

[![已推送至導覽堆疊的新視圖控制器](ios-code-only-images/customvca.png)](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>建立視圖階層

在上述範例中，我們已開始在程式碼中建立使用者介面，方法是將按鈕新增至視圖控制器。

iOS 使用者介面是由一個視圖階層所組成。 其他視圖（例如標籤、按鈕、滑杆等等）會新增為部分父視圖的子檢視。

例如，讓我們編輯 `CustomViewController` 以建立登入畫面，使用者可以在其中輸入使用者名稱和密碼。 畫面會包含兩個文字欄位和一個按鈕。

### <a name="adding-the-text-fields"></a>加入文字欄位

首先，移除 [[初始化視圖](#initializing-the-view)] 區段中新增的按鈕和事件處理常式。 

藉由建立和初始化，然後將它加入至 view 階層，以加入使用者名稱的控制項 `UITextField` ，如下所示：

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

當我們建立時 `UITextField` ，我們會設定 `Frame` 屬性來定義它的位置和大小。 在 iOS 中0，0座標位於左上方，其中 + x 向右和 + y 向下。 將設定為 `Frame` 以及其他幾個屬性後，我們會呼叫， `View.AddSubview` 將加入 `UITextField` 至視圖階層。 這會建立 `usernameField` 屬性所參考之實例的子視圖 `UIView` `View` 。 子視圖會以高於其父系視圖的迭置順序加入，因此它會出現在螢幕上父視圖的前方。

包含的應用程式 `UITextField` 如下所示：

 [![包含 UITextField 的應用程式](ios-code-only-images/image4.png)](ios-code-only-images/image4.png#lightbox)

我們可以 `UITextField` 使用類似的方式新增的密碼，只有這次我們將 `SecureTextEntry` 屬性設定為 true，如下所示：

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

[設定] 會 `SecureTextEntry = true` 隱藏使用者在中輸入的文字，如下 `UITextField` 所示：

 [![設定 SecureTextEntry true 會隱藏使用者輸入的文字。](ios-code-only-images/image4a.png)](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>新增按鈕

接下來，我們將新增按鈕，讓使用者可以提交使用者名稱和密碼。 按鈕會加入至視圖階層中，就像任何其他控制項一樣，其方式是將它當做引數傳遞給父視圖的 `AddSubview` 方法。

下列程式碼會新增按鈕，並註冊事件的事件處理常式 `TouchUpInside` ：

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

這麼做之後，登入畫面現在會出現，如下所示：

 [![登入畫面](ios-code-only-images/image5.png)](ios-code-only-images/image5.png#lightbox)

與舊版 iOS 不同的是，預設按鈕背景是透明的。 改變按鈕的 `BackgroundColor` 屬性會變更下列內容：

```csharp
submitButton.BackgroundColor = UIColor.White;
```

這會導致方形按鈕，而不是一般的圓角邊按鈕。 若要取得圓角邊緣，請使用下列程式碼片段：

```csharp
submitButton.Layer.CornerRadius = 5f;
```

有了這些變更，視圖看起來會像這樣：

[![執行視圖的範例](ios-code-only-images/image6.png)](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>將多個視圖加入至視圖階層

iOS 提供一個功能，可使用將多個視圖加入至視圖階層 `AddSubviews` 。

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>新增按鈕功能

按一下按鈕時，您的使用者會預期會發生一些問題。 例如，會顯示警示，或執行流覽至另一個畫面。

讓我們新增一些程式碼，將第二個 view 控制器推送到導覽堆疊上。

首先，建立第二個 view controller：

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

流覽如下所示：

[![此圖表中說明此導覽](ios-code-only-images/navigation.png)](ios-code-only-images/navigation.png#lightbox)

請注意，根據預設，當您使用 [流覽控制器] 時，iOS 會為應用程式提供一個導覽列和 [上一頁] 按鈕，讓您能夠在堆疊中往前移動。

## <a name="iterating-through-the-view-hierarchy"></a>逐一查看視圖階層

您可以逐一查看子視圖階層，並挑選任何特定的視圖。 例如，若要尋找每個， `UIButton` 並為該按鈕提供不同的 `BackgroundColor` ，可以使用下列程式碼片段

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

不過，如果要反復查看的視圖是，就不會有 `UIView` 任何作用，因為所有的視圖都會回到，因為 `UIView` 加入父代視圖的物件本身會繼承 `UIView` 。

## <a name="handling-rotation"></a>處理旋轉

如果使用者將裝置旋轉為橫向，控制項不會適當地調整大小，如下列螢幕擷取畫面所示：

[![如果使用者將裝置旋轉為橫向，控制項就不會適當地調整大小](ios-code-only-images/image7.png)](ios-code-only-images/image7.png#lightbox)

修正此問題的方法之一，是 `AutoresizingMask` 在每個視圖上設定屬性。 在此情況下，我們想要讓控制項水準延展，因此我們會進行設定 `AutoresizingMask` 。 下列範例適用于 `usernameField` ，但相同的動作必須套用至 view 階層中的每個小工具。

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

現在當我們旋轉裝置或模擬器時，所有專案都會自動縮放以填滿額外的空間，如下所示：

[![所有控制項都會伸展以填滿額外的空間](ios-code-only-images/image8.png)](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>建立自訂視圖

除了使用 UIKit 中的控制項，也可以使用自訂的視圖。 自訂視圖可以藉由繼承自和覆寫來建立 `UIView` `Draw` 。 讓我們建立自訂視圖，並將其加入至要示範的視圖階層。

### <a name="inheriting-from-uiview"></a>繼承自 UIView

我們要做的第一件事，就是建立自訂視圖的類別。 我們會使用 Visual Studio 中的**類別**範本來新增名為的空類別，以執行這項操作 `CircleView` 。 基類應設定為 `UIView` ，我們記得在 `UIKit` 命名空間中。 同時也需要 `System.Drawing` 命名空間。 在 `System.*` 此範例中不會使用其他各種命名空間，因此您可以隨意移除它們。

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

每個 `UIView` 都有一個 `Draw` 方法，會在需要繪製時由系統呼叫。 `Draw`永遠不應直接呼叫。 系統會在執行迴圈處理期間呼叫它。 第一次在視圖加入至視圖階層之後執行迴圈時， `Draw` 會呼叫其方法。 `Draw`當此視圖標示為需要在視圖上呼叫或來繪製時，就會發生後續呼叫 `SetNeedsDisplay` `SetNeedsDisplayInRect` 。

我們可以藉由在覆寫的方法內加入這類程式碼，將繪製程式碼加入至我們的觀點，如下 `Draw` 所示

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

因為 `CircleView` 是 `UIView` ，所以我們也可以設定 `UIView` 屬性。 例如，我們可以 `BackgroundColor` 在此函式中設定：

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

若要使用 `CircleView` 剛才建立的，我們可以將它當做子視圖加入至現有控制器中的 view 階層，如同我們在和較舊版本中所做的一樣 `UILabels` `UIButton` ，或者我們可以將它當做新控制器的視圖來載入。 讓我們來執行第二個動作。

### <a name="loading-a-view"></a>載入視圖

 `UIViewController`具有名為 `LoadView` 的方法，控制器會呼叫它來建立其視圖。 這是建立視圖並將它指派給控制器屬性的適當位置 `View` 。

首先，我們需要一個控制器，因此請建立名為的新空白類別 `CircleController` 。

在中， `CircleController` 新增下列程式碼以將設定 `View` 為 `CircleView` （您不應該 `base` 在覆寫中呼叫實作為）：

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

最後，我們需要在執行時間呈現控制器。 讓我們在稍早新增的 [提交] 按鈕上新增事件處理常式，如下所示：

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

現在，當我們執行應用程式並按下 [提交] 按鈕時，會顯示含有圓形的新視圖：

[![隨即顯示含有圓形的新視圖](ios-code-only-images/circles.png)](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>建立啟動畫面

當您的應用程式啟動時，就會顯示[啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)，以向您的使用者顯示其回應性的方式。 因為當您的應用程式載入時，會顯示啟動畫面，所以無法在程式碼中建立，因為仍在將應用程式載入記憶體中。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

當您在 Visual Studio 中建立 iOS 專案時，會以 xib 檔案的形式提供啟動畫面，您可以在專案內的**Resources**資料夾中找到該檔案。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

當您在 Visual Studio for Mac 中建立 iOS 專案時，會以分鏡腳本檔案的形式為您提供啟動畫面。

-----

您可以按兩下它，然後在 iOS 設計工具中開啟它來編輯。

Apple 建議將 xib 或分鏡腳本檔案用於以 iOS 8 或更新版本為目標的應用程式，當您在 iOS 設計工具中啟動任一檔案時，您會使用大小類別和自動設定來調整您的配置，讓它看起來良好，並針對所有裝置大小正確顯示。 除了 xib 或分鏡腳本之外，還可以使用靜態啟動映射，以允許以較早版本為目標的應用程式支援。

如需建立啟動畫面的詳細資訊，請參閱下列檔：

- [使用 xib 建立啟動畫面](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [使用分鏡腳本管理啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> 從 iOS 9 開始，Apple 建議使用腳本做為建立啟動畫面的主要方法。

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>建立預先 iOS 8 應用程式的啟動映射

如果您的應用程式是以 iOS 8 之前的版本為目標，則除了 xib 或分鏡腳本啟動畫面之外，還可以使用靜態映射。

此靜態映射可以在 plist 檔案中設定，或在您的應用程式中做為資產目錄（適用于 iOS 7）。 您必須為您的應用程式可執行檔每個裝置大小（320x480、640x960、640x1136）提供個別的映射。 如需啟動畫面大小的詳細資訊，請參閱[啟動畫面影像](~/ios/app-fundamentals/images-icons/launch-screens.md)指南。

> [!IMPORTANT]
> 如果您的應用程式沒有啟動畫面，您可能會注意到它並不完全符合畫面。 如果是這種情況，您應該確定至少包含名為的640x1136 影像 `Default-568@2x.png` 至您的 Info. plist。

## <a name="summary"></a>總結

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

本文討論如何在 Visual Studio 中以程式設計方式開發 iOS 應用程式。 我們已探討如何從空白的專案範本建立專案，並討論如何建立根視圖控制器並將其加入至視窗。 接著，我們會示範如何使用 UIKit 中的控制項，在控制器內建立視圖階層以開發應用程式畫面。 接下來，我們探討了如何讓 views 以不同的方向適當地進行版面配置，並瞭解如何透過子類別化來建立自訂的視圖 `UIView` ，以及如何在控制器中載入此視圖。 最後，我們探討了如何將啟動畫面新增至應用程式。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

本文討論如何在 Visual Studio for Mac 中以程式設計方式開發 iOS 應用程式。 我們已探討如何從單一視圖範本建立專案，並討論如何建立根視圖控制器並將其加入至視窗。 接著，我們會示範如何使用 UIKit 中的控制項，在控制器內建立視圖階層以開發應用程式畫面。 接下來，我們探討了如何讓 views 以不同的方向適當地進行版面配置，並瞭解如何透過子類別化來建立自訂的視圖 `UIView` ，以及如何在控制器中載入此視圖。 最後，我們探討了如何將啟動畫面新增至應用程式。

-----

## <a name="related-links"></a>相關連結

- [SimpleLogin （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/simplelogin)

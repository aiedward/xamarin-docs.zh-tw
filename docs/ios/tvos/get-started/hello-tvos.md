---
title: Hello，tvOS 快速入門手冊
description: 本指南會逐步解說如何建立您的第一個 tvOS 應用程式及其開發工具鏈。 它也介紹了 Xamarin 設計工具，它會將 UI 控制項公開給程式碼，並說明如何建立、執行和測試 tvOS 應用程式。
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 30fcf586a280688834e1ae9af61630c2611964a5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281825"
---
# <a name="hello-tvos-quick-start-guide"></a>Hello，tvOS 快速入門手冊

_本指南會逐步解說如何建立您的第一個 tvOS 應用程式及其開發工具鏈。它也介紹了 Xamarin 設計工具，它會將 UI 控制項公開給程式碼，並說明如何建立、執行和測試 tvOS 應用程式。_

Apple 已發行第5代的 Apple TV （Apple TV 4K，其執行 tvOS 11）。

Apple TV 平臺開放給開發人員使用，讓他們能夠建立豐富的沉浸式應用程式，並透過 Apple 電視的內建 App Store 發行。

如果您熟悉 Xamarin iOS 開發，您應該會發現轉換到 tvOS 相當簡單。 大部分的 Api 和功能都相同，不過，許多常見的 Api 無法使用（例如 WebKit）。 此外，使用 Siri 遠端時，會造成在以觸控式 iOS 裝置中不存在的一些設計挑戰。

本指南將提供在 Xamarin 應用程式中使用 tvOS 的簡介。 如需 tvOS 的詳細資訊，請參閱 Apple 的[ready For APPLE TV 4k](https://developer.apple.com/tvos/)檔。

## <a name="overview"></a>總覽

TvOS 可讓您使用在*Swift* （或*目標-C*） C#和*Xcode*中進行開發時所使用的相同 OS X 程式庫和介面控制項，在和 .NET 中開發完全原生的 Apple TV 應用程式。

此外，由於 tvOS 應用程式是以C#和 .net 撰寫的，因此通用的後端程式碼可以與 Xamarin、Xamarin、Android 和 xamarin 應用程式共用;全都是在每個平臺上提供原生體驗。

本文將為您介紹使用 tvOS 和 Visual Studio 建立 Apple TV 應用程式所需的重要概念，方法是逐步解說建立基本的**Hello，tvOS**應用程式來計算按鈕按下的次數：

[![](hello-tvos-images/run05.png "範例應用程式執行")](hello-tvos-images/run05.png#lightbox)

我們將涵蓋下列概念：

- **Visual Studio for Mac** – Visual Studio for Mac 簡介，以及如何使用它來建立 tvOS 應用程式。
- **TvOS 應用程式的剖析**– tvOS 應用程式包含的內容。
- **建立使用者介面**–如何使用 Xamarin Designer for iOS 來建立使用者介面。
- **部署和測試**–如何在 tvOS 模擬器和 real tvOS 硬體上執行和測試您的應用程式。

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 中啟動新的 tvOS 應用程式

如上所述，我們將建立名`Hello-tvOS`為的 Apple 電視應用程式，將單一按鈕和標籤新增至主畫面。 按一下按鈕時，標籤將會顯示已按下的次數。

若要開始，讓我們執行下列動作：

1. 啟動 Visual Studio for Mac：

    [![](hello-tvos-images/setup01.png "Visual Studio for Mac")](hello-tvos-images/setup01.png#lightbox)
2. 按一下畫面左上角的 [**新增方案 ...** ] 連結，以開啟 [**新增專案**] 對話方塊。
3. 選取 [ **tvOS**  > **應用程式** > **單一視圖應用程式**]，然後按 [**下一步]** 按鈕：

    [![](hello-tvos-images/setup02.png "選取單一視圖應用程式")](hello-tvos-images/setup02.png#lightbox)
4. 輸入`Hello, tvOS`作為 [**應用程式名稱**]，輸入您的**組織識別碼**，然後按 [**下一步]** 按鈕：

    [![](hello-tvos-images/setup04.png "輸入 Hello，tvOS")](hello-tvos-images/setup04.png#lightbox)
5. 針對`Hello_tvOS` [**專案名稱**] 輸入，然後按一下 [**建立**] 按鈕：

    [![](hello-tvos-images/setup03.png "輸入 HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio for Mac 會建立新的 tvOS 應用程式，並顯示新增至您應用程式解決方案的預設檔案：

 [![](hello-tvos-images/project01.png "預設檔案視圖")](hello-tvos-images/project01.png#lightbox)

Visual Studio for Mac 使用**方案**和**專案**，與 Visual Studio 的方式完全相同。 方案是一個容器，可保存一或多個專案；專案則可包含應用程式，並支援程式庫、測試應用程式等。在此情況下，Visual Studio for Mac 已為您建立方案和應用程式專案。

如有需要，您可以建立一或多個包含通用共用程式碼的程式碼程式庫專案。 這些程式庫專案可由應用程式專案取用，或與其他 tvOS 應用程式專案（或以程式碼類型為基礎的 Xamarin. Android 和 Xamarin）共用，就像您建立標準的 .NET 應用程式一樣。

## <a name="anatomy-of-a-xamarintvos-app"></a>TvOS 應用程式的剖析

如果您很熟悉 iOS 程式設計，您會發現這裡有很多相似之處。 事實上，tvOS 9 是 iOS 9 的子集，因此有許多概念會在此相互交叉。

讓我們看一下專案中的檔案：

- `Main.cs` – 這包含應用程式的主要進入點。 當應用程式啟動時，這包含執行的第一個類別與方法。
- `AppDelegate.cs`–此檔案包含主要的應用程式類別，負責接聽作業系統的事件。
- `Info.plist`–此檔案包含應用程式屬性，例如應用程式名稱、圖示等。
- `ViewController.cs`–這是代表主視窗並控制其生命週期的類別。
- `ViewController.designer.cs`–此檔案包含配管程式碼，可協助您與主畫面的使用者介面整合。
- `Main.storyboard`–主視窗的 UI。 此檔案可由 Xamarin Designer for iOS 建立及維護。

在下列各節中，我們將快速查看其中一些檔案。 我們稍後會更詳細地探索這些專案，但最好先瞭解其基本概念。

### <a name="maincs"></a>Main.cs

檔案包含靜態`Main`方法，它會建立新的 tvOS 應用程式實例，並傳遞將處理 OS 事件的類別名稱，在我們的`AppDelegate`案例中是類別： `Main.cs`

```csharp
using UIKit;

namespace Hello_tvOS
{
    public class Application
    {
        // This is the main entry point of the application.
        static void Main (string[] args)
        {
            // if you want to use a different Application Delegate class from "AppDelegate"
            // you can specify it here.
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

檔案包含我們`AppDelegate`的類別，其負責建立視窗及接聽 OS 事件： `AppDelegate.cs`

```csharp
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    // The UIApplicationDelegate for the application. This class is responsible for launching the
    // User Interface of the application, as well as listening (and optionally responding) to application events from iOS.
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        // class-level declarations

        public override UIWindow Window {
            get;
            set;
        }

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Override point for customization after application launch.
            // If not required for your application you can safely delete this method

            return true;
        }

        public override void OnResignActivation (UIApplication application)
        {
            // Invoked when the application is about to move from active to inactive state.
            // This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message)
            // or when the user quits the application and it begins the transition to the background state.
            // Games should use this method to pause the game.
        }

        public override void DidEnterBackground (UIApplication application)
        {
            // Use this method to release shared resources, save user data, invalidate timers and store the application state.
            // If your application supports background execution this method is called instead of WillTerminate when the user quits.
        }

        public override void WillEnterForeground (UIApplication application)
        {
            // Called as part of the transition from background to active state.
            // Here you can undo many of the changes made on entering the background.
        }

        public override void OnActivated (UIApplication application)
        {
            // Restart any tasks that were paused (or not yet started) while the application was inactive.
            // If the application was previously in the background, optionally refresh the user interface.
        }

        public override void WillTerminate (UIApplication application)
        {
            // Called when the application is about to terminate. Save data, if needed. See also DidEnterBackground.
        }
    }
}
```

除非您先前已建立 iOS 應用程式，否則可能不熟悉這段程式碼，但這是相當簡單的方式。 讓我們來檢查重要的程式程式碼。

首先，我們來看一下類別層級的變數宣告：

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

`Window`屬性會提供主視窗的存取權。 tvOS 會使用所謂的「*模型視圖控制器*」（MVC）模式。 一般來說，針對您建立的每個視窗（以及 windows 內的其他許多專案），都有一個控制器負責視窗的生命週期，例如顯示它、在其中加入新的視圖（控制項）等等。

接下來，我們有`FinishedLaunching`方法。 這個方法會在應用程式具現化之後執行，而且它會負責實際建立應用程式視窗，並開始在其中顯示視圖的進程。 因為我們的應用程式會使用分鏡腳本來定義其 UI，所以這裡不需要任何額外程式碼。

範本中提供了許多其他方法，例如`DidEnterBackground`和。 `WillEnterForeground` 如果應用程式事件未在您的應用程式中使用，則可以安全地移除。

### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController`類別是主視窗的控制器。 這表示它會負責主視窗的生命週期。 我們稍後將詳細檢查這一點，現在就讓我們快速看一下：

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
    }
}
```

#### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

主視窗類別的設計工具檔案目前是空的，但在使用 iOS 設計工具建立使用者介面時，Visual Studio for Mac 會自動填入此檔案：

```csharp
using Foundation;

namespace HellotvOS
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

我們通常不會關心設計工具檔案，因為它們只是由 Visual Studio for Mac 自動管理，而只是提供必要的配管程式碼，以允許存取我們新增至應用程式中任何視窗或視圖的控制項。

既然我們已建立 tvOS 應用程式，並對其元件有基本瞭解，讓我們來看看如何建立 UI。

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>建立使用者介面

您不需要使用 Xamarin Designer for iOS 來建立 tvOS 應用程式的使用者介面，可以直接從C#程式碼建立 UI，但這已超出本文的範圍。 為了簡單起見，我們將使用 iOS 設計工具，在本教學課程的其餘部分建立 UI。

若要開始建立您的 UI，讓我們在  `Main.storyboard` **方案總管**中按兩下該檔案，在 iOS 設計工具中開啟它進行編輯：

[![](hello-tvos-images/designer01.png "方案總管中的 Main.storyboard 檔案")](hello-tvos-images/designer01.png#lightbox)

這應該會啟動設計工具，如下所示：

[![](hello-tvos-images/designer02.png "設計工具")](hello-tvos-images/designer02.png#lightbox)

如需 iOS 設計工具及其運作方式的詳細資訊，請參閱[Xamarin Designer for iOS 指南簡介](~/ios/user-interface/designer/introduction.md)。

我們現在可以開始將控制項新增至 tvOS 應用程式的設計介面。

請執行下列動作：

1. 找出 [**工具箱**]，其應該位於設計介面的右邊：

    [![](hello-tvos-images/designer03.png "工具箱")](hello-tvos-images/designer03.png#lightbox)

    如果您在這裡找不到它，請流覽以觀看 **> pad > 工具箱**來觀看。
2. 將**標籤**從 [**工具箱**] 拖曳至設計介面：

    [![](hello-tvos-images/designer04.png "從 [工具箱] 拖曳標籤")](hello-tvos-images/designer04.png#lightbox)
3. 按一下**屬性 pad**中的 [**標題**] 屬性，將按鈕的標題變更為`Hello, tvOS` ，並將**字型大小**設為128：

    [![](hello-tvos-images/designer05.png "將標題設定為 Hello，tvOS，並將字型大小設為128")](hello-tvos-images/designer05.png#lightbox)
4. 調整標籤的大小，讓所有單字都可見，並將它放在靠近視窗頂端的位置：

    [![](hello-tvos-images/designer06.png "調整大小並置中標籤")](hello-tvos-images/designer06.png#lightbox)
5. 標籤現在必須限制為其位置，如此才會顯示為預期。 不論螢幕大小為何。 若要這樣做，請按一下標籤，直到出現*T 形狀控點*為止：

    [![](hello-tvos-images/designer07.png "T 形狀控點")](hello-tvos-images/designer07.png#lightbox)
6. 若要水準限制標籤，請選取中間方形並將它拖曳到垂直虛線：

    [![](hello-tvos-images/designer08.png "選取中央正方形")](hello-tvos-images/designer08zoom.png#lightbox)

     標籤應該會變成橙色。
7. 選取標籤頂端的 T 控點，並將它拖曳至視窗的上邊緣：

    [![](hello-tvos-images/designer09.png "將控點拖曳至視窗的上邊緣")](hello-tvos-images/designer09.png#lightbox)
8. 接下來，依序按一下 [寬度] 和 [高度]*骨骼控點*：

    [![](hello-tvos-images/designer10.png "寬度和高度的骨骼控點")](hello-tvos-images/designer10.png#lightbox)

     按一下每個*骨骼控制碼*時，分別選取 [寬度] 和 [高度] 來設定固定維度。
9. 完成時，您的條件約束看起來應該類似于 Properties pad 的 [配置] 索引標籤中：

    [![](hello-tvos-images/designer11.png "範例條件約束")](hello-tvos-images/designer11.png#lightbox)
10. 拖曳 **按鈕** 從 **工具箱** 並將它放在標籤底下。
11. 按一下**屬性 pad**中的 [**標題**] 屬性，將按鈕的標題變更為`Click Me`：

    [![](hello-tvos-images/designer12.png "將按鈕標題變更為按一下我")](hello-tvos-images/designer12.png#lightbox)
12. 重複上述的步驟5到8，以限制 [tvOS] 視窗中的按鈕。 不過，請將其拖曳至標籤的底部，而不是將 T-控制碼拖曳至視窗頂端（如步驟 #7）：

    [![](hello-tvos-images/designer14.png "限制按鈕")](hello-tvos-images/designer14.png#lightbox)
13. 拖曳按鈕底下的另一個標籤，將其大小調整為與第一個標籤相同的寬度，並將其**對齊**設定為 [**置**中]：

    [![](hello-tvos-images/designer15.png "拖曳按鈕底下的另一個標籤，將其大小調整為與第一個標籤相同的寬度，並將其對齊方式設定為 [置中]")](hello-tvos-images/designer15.png#lightbox)
14. 如同第一個標籤和按鈕，將此標籤設為置中並釘選到位置和大小：

    [![](hello-tvos-images/designer16.png "將標籤釘選到位置和大小")](hello-tvos-images/designer16.png#lightbox)
15. 將您的變更儲存至使用者介面。

當您調整大小和移動控制項時，您應該已經注意到設計工具會提供以[APPLE TV 人類介面指導方針](https://developer.apple.com/tvos/human-interface-guidelines/)為基礎的實用貼齊提示。 這些指導方針可協助您建立高品質的應用程式，對 Apple 電視使用者具有熟悉的外觀與風格。

如果您查看 [**檔大綱**] 區段，請注意如何顯示構成使用者介面之元素的版面配置和階層：

[![](hello-tvos-images/designer17.png "檔大綱區段")](hello-tvos-images/designer17.png#lightbox)

您可以從這裡選取要編輯的專案，或視需要拖曳來重新排列 UI 元素。 例如，如果 UI 專案是由另一個元素所涵蓋，您可以將它拖曳到清單底部，使其成為視窗的最上層專案。

既然我們已建立使用者介面，我們必須公開 UI 專案，tvOS 才能在程式碼中C#存取它們並與其互動。

### <a name="accessing-the-controls-in-the-code-behind"></a>存取程式碼後置中的控制項

有兩種主要方式可從程式碼存取您在 iOS 設計工具中新增的控制項：

- 在控制項上建立事件處理常式。
- 提供控制項的名稱，讓我們稍後可以參考它。

當加入其中一項時， `ViewController.designer.cs`將會更新內的部分類別以反映變更。 這可讓您接著存取 View Controller 中的控制項。

### <a name="creating-an-event-handler"></a>建立事件處理常式

在此範例應用程式中，當按下按鈕時，我們會想要進行_一些_動作，因此必須將事件處理常式新增至按鈕上的特定事件。 若要進行此設定，請執行下列動作：

1. 在 Xamarin iOS 設計工具中，選取 [視圖控制器] 上的 [] 按鈕。
2. 在 Properties pad 中，選取 [**事件**] 索引標籤：

    [![](hello-tvos-images/event1.png "[事件] 索引標籤")](hello-tvos-images/event1.png#lightbox)
3. 找出 TouchUpInside 事件，並為它提供名為`Clicked`的事件處理常式：

    [![](hello-tvos-images/event2.png "TouchUpInside 事件")](hello-tvos-images/event2.png#lightbox)
4. 當您按下**enter**時，將會開啟**ViewController**，並在程式碼中建議事件處理常式的位置。 使用鍵盤上的方向鍵來設定位置：

    [![](hello-tvos-images/event3.png "設定位置")](hello-tvos-images/event3.png#lightbox)
5. 這會建立部分方法，如下所示：

    [![](hello-tvos-images/event4.png "部分方法")](hello-tvos-images/event4.png#lightbox)

我們現在已準備好開始新增一些程式碼，讓按鈕能夠運作。

### <a name="naming-a-control"></a>命名控制項

按一下按鈕時，標籤應該會根據點擊次數來更新。 若要這麼做，我們必須在程式碼中存取標籤。 這是藉由提供名稱來完成。 請執行下列動作：

1. 開啟腳本，然後選取視圖控制器底部的標籤。
2. 在 Properties pad 中，選取 [ **Widget** ] 索引標籤：

    [![](hello-tvos-images/name1.png "選取 [Widget] 索引標籤")](hello-tvos-images/name1.png#lightbox)
3. 在 [身分**識別 > 名稱**] 底下，新增`ClickedLabel`：

    [![](hello-tvos-images/name2.png "設定 ClickedLabel")](hello-tvos-images/name2.png#lightbox)

我們現在已準備好開始更新標籤！

### <a name="how-controls-are-accessed"></a>如何存取控制項

如果`ViewController.designer.cs`您在**方案總管**中選取，您就可以查看`ClickedLabel`標籤和`Clicked`事件處理常式如何對應至C#中的**輸出**和**動作**：

[![](hello-tvos-images/accesscontrol.png "輸出和動作")](hello-tvos-images/accesscontrol.png#lightbox)

您可能也會注意`ViewController.designer.cs`到，是部分類別，因此 Visual Studio for Mac 不需要進行修改`ViewController.cs` ，這會覆寫對類別所做的任何變更。

以這種方式公開 UI 元素，可讓您在 View Controller 中存取這些專案。

您通常不需要`ViewController.designer.cs`自行開啟，只是為了教育目的而在此呈現。

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>撰寫程式碼

藉由建立使用者介面，並透過**輸出**和**動作**將其 UI 專案公開給程式碼，我們終於準備好撰寫程式碼來提供程式功能。

在我們的應用程式中，每次按一下第一個按鈕時，我們就會更新標籤，以顯示按鈕按了幾次。 若要完成這項操作，我們需要`ViewController.cs`在  **Solution Pad**中按兩下檔案以進行編輯：

[![](hello-tvos-images/code01.png "Solution Pad")](hello-tvos-images/code01.png#lightbox)

首先，我們需要在`ViewController`類別中建立類別層級變數，以追蹤發生的點擊次數。 編輯類別定義，使它看起來如下所示：

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

接下來，在相同的類別`ViewController`（）中，我們需要覆寫**ViewDidLoad**方法並新增一些程式碼，以設定標籤的初始訊息：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

`ViewDidLoad`我們需要使用，而不是其他方法`Initialize`（例如）， `ViewDidLoad`因為在`.storyboard` OS 已載入並從檔案具現化使用者介面時，*會呼叫。* 如果我們嘗試在檔案完全載入並具現`.storyboard`化之前存取標籤控制項，我們會`NullReferenceException`收到錯誤，因為 label 控制項尚未建立。

接下來，我們需要新增程式碼，以回應使用者按一下按鈕。 將下列內容新增至我們建立的部分類別：

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

每當使用者按一下按鈕時，就會呼叫此程式碼。

一切就緒之後，我們就可以開始建立和測試我們的 tvOS 應用程式。

## <a name="testing-the-application"></a>測試應用程式

現在可以建立並執行我們的應用程式，以確保它如預期般執行。 我們可以在一個步驟中建立並執行全部，或者可以建立它，而不需要執行它。

當我們建立應用程式時，我們可以選擇想要的組建種類：

- **Debug** – debug 組建會編譯成具有額外中繼資料的 ' ' （應用程式）檔案，讓我們能夠在應用程式執行時，進行 debug。
- **發行**-發行組建也會建立 ' ' 檔案，但不包含偵錯工具資訊，因此較小且執行速度更快。  

您可以從 [Visual Studio for Mac] 畫面左上角的設定選取**器**中，選取組建的類型：

[![](hello-tvos-images/run01.png "選取組建的類型")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>建置應用程式

在我們的案例中，我們只想要一個 debug 組建，因此請確定已選取 [ **debug** ]。 讓我們先按**則是⌘ B**來建立應用程式，或從 [**建立**] 功能表選擇 [**全部組建**]。

如果沒有任何錯誤，您會在 Visual Studio for Mac 的狀態列中看到 [**組建成功**] 訊息。 如果發生錯誤，請檢查您的專案，並確定您已正確地遵循步驟。 一開始請先確認您的程式碼（在 Xcode 和 Visual Studio for Mac 中）是否符合教學課程中的程式碼。

### <a name="running-the-application"></a>執行應用程式

若要執行應用程式，我們有三個選項：

- 按下 **⌘+Enter**。
- 從 [執行] 功能表中選擇 [偵錯]。
- 按一下 Visual Studio for Mac 工具列中的 [播放] 按鈕 (在 [方案總管] 上方)。

應用程式將會建立（如果尚未建立），啟動進入 debug 模式，tvOS 模擬器將會啟動，且應用程式將會啟動，並顯示其主要介面視窗：

[![範例應用程式主畫面](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

從 [**硬體**] 功能表選取 [**顯示 Apple TV 遠端**]，讓您可以控制模擬器。

[![](hello-tvos-images/run04.png "選取 [顯示 Apple TV 遠端]")](hello-tvos-images/run04.png#lightbox)

使用模擬器的遠端，如果您按下按鈕幾次，標籤應該會更新為下列計數：

[![](hello-tvos-images/run05.png "具有已更新計數的標籤")](hello-tvos-images/run05.png#lightbox)

恭喜您！ 這裡涵蓋了許多基礎，但如果您遵循本教學課程的開始到完成，您現在應該已充分瞭解 tvOS 應用程式的元件，以及用來建立它們的工具。

## <a name="where-to-next"></a>下一步是什麼？

開發 Apple TV 應用程式有幾項挑戰，因為使用者與介面之間的連線中斷（不在使用者手中的房間內），而且 tvOS 的限制會放在應用程式大小和儲存體上。

因此，我們強烈建議您先閱讀下列檔，然後再進入 tvOS 應用程式的設計：

- [TvOS 9 簡介](~/ios/tvos/platform/tvos9.md)-這篇文章介紹 tvOS 9 中適用于 Xamarin. tvOS 開發人員的所有新的和已修改的 api 和功能。
- 使用[導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)– Xamarin. tvOS 應用程式的使用者不會與 iOS 直接互動，因為它會在裝置的螢幕上使用影像，而是透過 Siri 遠端從房間中間接地進行。 本文涵蓋焦點的概念，以及如何使用它來處理 tvOS 應用程式使用者介面中的導覽。
- [Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)–使用者將與 Apple 電視和 tvOS 應用程式互動的主要方式是透過內含的 Siri 遠端。 如果您的應用程式是遊戲，您可以選擇性地建立協力廠商的支援，也就是您應用程式中的 iOS （MFI）藍牙遊戲控制器。 本文涵蓋支援 tvOS 應用程式中新的 Siri 遠端和藍牙遊戲控制器。
- [資源和資料儲存](~/ios/tvos/app-fundamentals/resources-data-storage.md)–不同于 iOS 裝置，新的 Apple TV 並不提供 tvOS 應用程式的持續性本機儲存體。 因此，如果您的 tvOS 應用程式需要保存資訊（例如使用者喜好設定），它必須從 iCloud 儲存和取出該資料。 本文涵蓋在 tvOS 應用程式中使用資源和持續性資料儲存。
- 使用[圖示和](~/ios/tvos/app-fundamentals/icons-images.md)影像-建立迷人圖示和圖像是為 Apple 電視應用程式開發沉浸式使用者體驗的重要部分。 本指南將說明建立和包含 tvOS 應用程式所需的圖形資產所需的步驟。
- [使用者介面](~/ios/tvos/user-interface/index.md)–一般使用者體驗（UX）涵蓋範圍，包括使用者介面（UI）控制項，在使用 tvOS 時，請使用 Xcode 的 INTERFACE BUILDER 和 UX 設計原則。
- [部署和測試](~/ios/tvos/deploy-test/index.md)–本節涵蓋用來測試應用程式以及如何散發的主題。 這裡的主題包含一些工具，例如用來進行偵錯工具、部署至測試人員，以及如何將應用程式發行至 Apple TV App Store 等。

如果您遇到任何使用 tvOS 的問題，請參閱我們的[疑難排解](~/ios/tvos/troubleshooting.md)檔，以取得已知問題和解決方案的清單。

## <a name="summary"></a>總結

本文提供的快速入門可讓您藉由建立簡單的 Hello，tvOS 應用程式，來開發使用 Visual Studio for Mac 的 tvOS 應用程式。 其中涵蓋了 tvOS 裝置布建、介面建立、程式碼以在 tvOS 模擬器上進行 tvOS 和測試的基本概念。


## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [使用 Xamarin 建立適用于 tvOS 的應用程式（影片）](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)

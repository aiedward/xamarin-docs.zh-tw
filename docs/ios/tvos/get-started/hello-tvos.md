---
title: Hello, tvOS 快速入門指南
description: 本指南將逐步解說如何建立您的第一個 tvOS 應用程式及其開發工具鏈。 它也引進了 Xamarin 設計工具，此設計工具會將 UI 控制項公開給程式碼，並說明如何建立、執行及測試 tvOS 應用程式。
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: beaa26e0d028fb614baf76ff52d05b80584f14d4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435256"
---
# <a name="hello-tvos-quick-start-guide"></a>Hello, tvOS 快速入門指南

_本指南將逐步解說如何建立您的第一個 tvOS 應用程式及其開發工具鏈。它也引進了 Xamarin 設計工具，此設計工具會將 UI 控制項公開給程式碼，並說明如何建立、執行及測試 tvOS 應用程式。_

Apple 已發行第5代的 Apple TV，也就是執行 tvOS 11 的 apple tv 4K。

Apple TV 平臺開放給開發人員使用，讓他們可以建立豐富、沉浸式的應用程式，並透過 Apple TV 的內建 App Store 來發行這些應用程式。

如果您熟悉 Xamarin 的開發，您應該會發現轉換成 tvOS 相當簡單。 大部分的 Api 和功能都是相同的，但許多常見的 Api 都無法使用 (例如 WebKit) 。 此外，使用 Siri 遠端時，會帶來一些在以觸控式螢幕為基礎的 iOS 裝置上不存在的設計挑戰。

本指南將提供在 Xamarin 應用程式中使用 tvOS 的簡介。 如需 tvOS 的詳細資訊，請參閱 Apple 的「 [準備取得 APPLE TV 4k](https://developer.apple.com/tvos/) 」檔。

## <a name="overview"></a>概觀

TvOS 可讓您使用 c # 和 .NET 開發完全原生的 Apple TV 應用程式，其方式是使用在 *Swift* (或 *目標-C*) 和 *Xcode*中進行開發時使用的相同 OS X 程式庫和介面控制項。

此外，由於 tvOS 應用程式是以 c # 和 .NET 撰寫的，因此通用的後端程式碼可以與 Xamarin、Xamarin 和 Xamarin 應用程式共用;在每個平臺上提供原生體驗。

本文將為您介紹使用 tvOS 和 Visual Studio 建立 Apple TV 應用程式所需的重要概念，方法是逐步解說建立基本的 **Hello tvOS** 應用程式，以計算按鈕的點擊次數：

[![範例應用程式執行](hello-tvos-images/run05.png)](hello-tvos-images/run05.png#lightbox)

我們將涵蓋下列概念：

- **Visual Studio for Mac**  – Visual Studio for Mac 的簡介，以及如何使用它建立 Xamarin. tvOS 應用程式。
- **Xamarin. TvOS 應用程式** 的結構– tvOS 應用程式所組成的。
- **建立消費者介面** –如何使用 Xamarin Designer for iOS 來建立使用者介面。
- **部署和測試** –如何在 tvOS 模擬器和實際 tvOS 硬體上執行和測試您的應用程式。

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 中啟動新的 Xamarin. tvOS 應用程式

如上所述，我們將建立名為的 Apple TV 應用程式 `Hello-tvOS` ，將單一按鈕和標籤新增至主畫面。 按一下按鈕時，標籤將會顯示已按下的次數。

首先，讓我們執行下列作業：

1. 啟動 Visual Studio for Mac：

    [![Visual Studio for Mac](hello-tvos-images/setup01.png)](hello-tvos-images/setup01.png#lightbox)
2. 按一下畫面左上角的 [ **新增方案 ...** ] 連結，以開啟 [ **新增專案** ] 對話方塊。
3. 選取**tvOS**  >  **應用程式**  >  **單一視圖應用程式**，然後按 [**下一步]** 按鈕：

    [![選取單一視圖應用程式](hello-tvos-images/setup02.png)](hello-tvos-images/setup02.png#lightbox)
4. 輸入 `Hello, tvOS` **應用程式名稱**、輸入您的 **組織識別碼** ，然後按 [ **下一步]** 按鈕：

    [![輸入 Hello，tvOS](hello-tvos-images/setup04.png)](hello-tvos-images/setup04.png#lightbox)
5. 輸入 `Hello_tvOS` **專案名稱** ，然後按一下 [ **建立** ] 按鈕：

    [![輸入 HellotvOS](hello-tvos-images/setup03.png)](hello-tvos-images/setup03.png#lightbox)

Visual Studio for Mac 將會建立新的 tvOS 應用程式，並顯示新增至您應用程式解決方案的預設檔案：

 [![預設檔案視圖](hello-tvos-images/project01.png)](hello-tvos-images/project01.png#lightbox)

Visual Studio for Mac 使用 **方案** 和 **專案**的方式，與 Visual Studio 的方式完全相同。 解決方案是可保存一或多個專案的容器;專案可以包含應用程式、支援程式庫、測試應用程式等。在此情況下，Visual Studio for Mac 已為您建立方案和應用程式專案。

如果您想要的話，您可以建立一個或多個包含通用共用程式碼的程式碼程式庫專案。 應用程式專案可以使用這些程式庫專案，或與其他 tvOS 應用程式專案共用 (或 Xamarin、Xamarin 和 Xamarin （根據程式碼) 的類型），就像您建立標準 .NET 應用程式一樣。

## <a name="anatomy-of-a-xamarintvos-app"></a>Xamarin. tvOS 應用程式的剖析

如果您很熟悉 iOS 程式設計，您會發現這裡有許多相似之處。 事實上，tvOS 9 是 iOS 9 的子集，所以許多概念都在這裡。

讓我們看看專案中的檔案：

- `Main.cs` – 這包含應用程式的主要進入點。 當應用程式啟動時，這包含執行的第一個類別與方法。
- `AppDelegate.cs` –這個檔案包含主要的應用程式類別，負責從作業系統接聽事件。
- `Info.plist` –這個檔案包含應用程式屬性，例如應用程式名稱、圖示等。
- `ViewController.cs` –這是代表主視窗並控制其生命週期的類別。
- `ViewController.designer.cs` –這個檔案包含的程式碼可協助您整合主畫面的使用者介面。
- `Main.storyboard` –主視窗的 UI。 此檔案可由 Xamarin Designer for iOS 建立和維護。

在下列各節中，我們將快速查看其中一些檔案。 稍後我們將更詳細地探索它們，但現在瞭解其基本概念是不錯的主意。

### <a name="maincs"></a>Main.cs

檔案 `Main.cs` 包含靜態方法， `Main` 此方法會建立新的 tvOS 應用程式實例，並傳遞將處理 OS 事件的類別名稱，在我們的案例中為 `AppDelegate` 類別：

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

檔案 `AppDelegate.cs` 包含我們的 `AppDelegate` 類別，負責建立視窗並接聽 OS 事件：

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

除非您先前已建立 iOS 應用程式，否則這段程式碼可能不陌生，但其實相當簡單。 讓我們來檢查重要的行。

首先，讓我們看看類別層級的變數宣告：

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

`Window`屬性提供主視窗的存取權。 tvOS 會使用所謂的 *模型視圖控制器* (MVC) 模式。 一般來說，對於您建立的每個視窗 (以及 windows) 內的其他許多專案，都有一個控制器負責視窗的生命週期，例如顯示它、新增 (控制項) 的新視圖等等。

接下來，我們有 `FinishedLaunching` 方法。 這個方法會在應用程式具現化後執行，而且會負責實際建立應用程式視窗，以及開始在其中顯示視圖的進程。 因為我們的應用程式會使用分鏡腳本來定義其 UI，所以此處不需要額外的程式碼。

範本中提供許多其他方法，例如 `DidEnterBackground` 和 `WillEnterForeground` 。 如果應用程式事件未在應用程式中使用，則可以安全地移除這些應用程式。

### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController`類別是我們主視窗的控制器。 這表示它負責主視窗的生命週期。 我們稍後會詳細探討這一點，現在讓我們來看看它：

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

主視窗類別的設計工具檔案目前是空的，但在使用 iOS 設計工具建立消費者介面時，Visual Studio for Mac 會自動將它填入：

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

我們通常不在意設計工具檔案，因為它們只會由 Visual Studio for Mac 自動管理，並且提供必要的配管程式碼，以允許存取我們新增至應用程式中任何視窗或視圖的控制項。

既然我們已建立 tvOS 應用程式，並對其元件有基本的瞭解，讓我們來看看如何建立 UI。

<a name="Creating-the-User-Interface"></a>

## <a name="creating-the-user-interface"></a>建立使用者介面

您不需要使用 Xamarin Designer for iOS 來建立 tvOS 應用程式的消費者介面，您可以直接從 c # 程式碼建立 UI，但這已超出本文的範圍。 為了簡單起見，我們將使用 iOS 設計工具，在本教學課程的其餘部分建立 UI。

若要開始建立您的 UI，讓我們按兩下 `Main.storyboard` **方案總管** 中的檔案，將它開啟，以在 iOS 設計工具中進行編輯：

[![[方案總管] 中的 Main.storyboard 檔案](hello-tvos-images/designer01.png)](hello-tvos-images/designer01.png#lightbox)

這應該會啟動設計工具，看起來如下所示：

[![設計工具](hello-tvos-images/designer02.png)](hello-tvos-images/designer02.png#lightbox)

如需 iOS 設計工具和其運作方式的詳細資訊，請參閱 [Xamarin Designer for iOS 指南簡介](~/ios/user-interface/designer/introduction.md) 。

我們現在可以開始將控制項新增至 tvOS 應用程式的設計介面。

執行下列動作：

1. 找出設計介面右邊的 [ **工具箱**]：

    [![工具箱](hello-tvos-images/designer03.png)](hello-tvos-images/designer03.png#lightbox)

    如果您在這裡找不到它，請流覽以 **查看 > pad > 工具箱** 來查看。
2. 將 **標籤** 從 [ **工具箱** ] 拖曳至設計介面：

    [![從 [工具箱] 拖曳標籤](hello-tvos-images/designer04.png)](hello-tvos-images/designer04.png#lightbox)
3. 按一下**屬性板**中的 [**標題**] 屬性，然後將按鈕的標題變更為 `Hello, tvOS` ，並將**字型大小**設為128：

    [![將標題設定為 Hello、tvOS，並將字型大小設為128](hello-tvos-images/designer05.png)](hello-tvos-images/designer05.png#lightbox)
4. 調整標籤的大小，以便顯示所有文字，並將它放在靠近視窗頂端的中央：

    [![調整標籤大小並置中](hello-tvos-images/designer06.png)](hello-tvos-images/designer06.png#lightbox)
5. 標籤現在必須限制為其位置，使其看起來如預期。 無論螢幕大小。 若要這樣做，請按一下標籤，直到顯示 *T 形控點* 為止：

    [![T 形把手](hello-tvos-images/designer07.png)](hello-tvos-images/designer07.png#lightbox)
6. 若要水準限制標籤，請選取中央正方形，然後將它拖曳至垂直虛線：

    [![選取中央正方形](hello-tvos-images/designer08.png)](hello-tvos-images/designer08zoom.png#lightbox)

     標籤應該會變成橙色。
7. 選取標籤頂端的 T 控點，並將它拖曳至視窗的上邊緣：

    [![將控點拖曳至視窗的上邊緣](hello-tvos-images/designer09.png)](hello-tvos-images/designer09.png#lightbox)
8. 接著，依序按一下 [寬度] 和 [高度] *骨骼控點* ，如下所示：

    [![寬度和高度的骨骼處理](hello-tvos-images/designer10.png)](hello-tvos-images/designer10.png#lightbox)

     按一下每個 *骨骼控制碼* 時，請分別選取 [寬度] 和 [高度] 來設定固定的維度。
9. 完成時，您的條件約束看起來應該會類似 [屬性] 面板的 [配置] 索引標籤中：

    [![範例條件約束](hello-tvos-images/designer11.png)](hello-tvos-images/designer11.png#lightbox)
10. 從 [**工具箱**] 拖曳**按鈕**，並將它放在標籤底下。
11. 按一下**屬性板**中的 [**標題**] 屬性，然後將按鈕的標題變更為 `Click Me` ：

    [![將按鈕標題變更為 Click Me](hello-tvos-images/designer12.png)](hello-tvos-images/designer12.png#lightbox)
12. 重複上述步驟5到8，以限制 tvOS 視窗中的按鈕。 不過，您不需要將 T 控制碼拖曳至視窗的頂端 (如同在步驟 #7) 中，將它拖曳到標籤底部：

    [![限制按鈕](hello-tvos-images/designer14.png)](hello-tvos-images/designer14.png#lightbox)
13. 拖曳按鈕下的另一個標籤，將其大小調整為與第一個標籤相同的寬度，並將其 **對齊方式** 設定為 [ **置**中]：

    [![拖曳按鈕下的另一個標籤，將其大小調整為與第一個標籤相同的寬度，並將其對齊方式設定為 [置中]](hello-tvos-images/designer15.png)](hello-tvos-images/designer15.png#lightbox)
14. 如同第一個標籤和按鈕，將此標籤設定為置中，並將其釘選到位置和大小：

    [![將標籤釘選到位置和大小](hello-tvos-images/designer16.png)](hello-tvos-images/designer16.png#lightbox)
15. 將您的變更儲存至消費者介面。

當您調整控制項的大小時，您應該注意到設計工具會為您提供以 [APPLE TV 人體介面指導方針](https://developer.apple.com/tvos/human-interface-guidelines/)為基礎的輔助貼齊提示。 這些指導方針可協助您建立高品質的應用程式，以熟悉 Apple TV 使用者的外觀和風格。

如果您查看 [ **檔大綱** ] 區段，請注意組成使用者介面之元素的版面配置和階層會如何顯示：

[![[檔大綱] 區段](hello-tvos-images/designer17.png)](hello-tvos-images/designer17.png#lightbox)

您可以從這裡選取要編輯的專案，或視需要拖曳以重新排列 UI 元素。 例如，如果 UI 元素是由另一個專案所涵蓋，您可以將它拖曳到清單底部，使其成為視窗中最上層的專案。

既然我們已建立消費者介面，我們必須公開 UI 專案，tvOS 才能在 c # 程式碼中存取這些專案並與其互動。

### <a name="accessing-the-controls-in-the-code-behind"></a>存取程式碼後置於中的控制項

從程式碼存取您在 iOS 設計工具中新增的控制項有兩種主要方式：

- 在控制項上建立事件處理常式。
- 為控制項命名，讓我們稍後可以參考該控制項。

當加入其中一種時，中的部分類別 `ViewController.designer.cs` 將會更新以反映變更。 這可讓您存取 View Controller 中的控制項。

### <a name="creating-an-event-handler"></a>建立事件處理常式

在此範例應用程式中，當您按一下按鈕時，我們想要發生 _某些_ 情況，因此必須將事件處理常式新增至按鈕上的特定事件。 若要進行此設定，請執行下列動作：

1. 在 Xamarin iOS 設計工具中，選取視圖控制器上的按鈕。
2. 在 [屬性] 面板中，選取 [ **事件** ] 索引標籤：

    [![[事件] 索引標籤](hello-tvos-images/event1.png)](hello-tvos-images/event1.png#lightbox)
3. 找出 TouchUpInside 事件，並為它指定一個名為的事件處理常式 `Clicked` ：

    [![TouchUpInside 事件](hello-tvos-images/event2.png)](hello-tvos-images/event2.png#lightbox)
4. 當您按 **enter**鍵時， **ViewController**會開啟，建議您在程式碼中的事件處理常式位置。 使用鍵盤上的方向鍵來設定位置：

    [![設定位置](hello-tvos-images/event3.png)](hello-tvos-images/event3.png#lightbox)
5. 這會建立部分方法，如下所示：

    [![部分方法](hello-tvos-images/event4.png)](hello-tvos-images/event4.png#lightbox)

我們現在已準備好開始新增一些程式碼，讓按鈕可以運作。

### <a name="naming-a-control"></a>命名控制項

按一下按鈕時，標籤應該會根據點擊次數進行更新。 若要這樣做，我們必須存取程式碼中的標籤。 這是藉由提供名稱來完成。 執行下列動作：

1. 開啟分鏡腳本，然後選取視圖控制器底部的標籤。
2. 在 [屬性] 面板中，選取 [ **Widget** ] 索引標籤：

    [![選取 [Widget] 索引標籤](hello-tvos-images/name1.png)](hello-tvos-images/name1.png#lightbox)
3. 在 [ **識別 > 名稱**] 下，新增 `ClickedLabel` ：

    [![設定 >clickedlabel](hello-tvos-images/name2.png)](hello-tvos-images/name2.png#lightbox)

我們現在已準備好開始更新標籤！

### <a name="how-controls-are-accessed"></a>如何存取控制項

如果您 `ViewController.designer.cs` 在 **方案總管** 中選取，您將能夠看到 `ClickedLabel` 標籤和 `Clicked` 事件處理常式如何對應到 c # 中的 **輸出** 和 **動作** ：

[![輸出和動作](hello-tvos-images/accesscontrol.png)](hello-tvos-images/accesscontrol.png#lightbox)

您也可能會注意到這 `ViewController.designer.cs` 是部分類別，因此 Visual Studio for Mac 不需要修改 `ViewController.cs` 會覆寫對類別所做的任何變更。

以這種方式公開 UI 專案，可讓您在「View 控制器」中存取這些元素。

您通常不需要 `ViewController.designer.cs` 自行開啟，它只會提供給教育用途。

<a name="Writing-the-Code"></a>

## <a name="writing-the-code"></a>撰寫程式碼

藉由我們的消費者介面建立，並透過 **輸出** 和 **動作**將其 UI 元素公開到程式碼，我們終於可以撰寫程式碼來提供程式功能。

在我們的應用程式中，每次按一下第一個按鈕時，就會更新標籤，以顯示按鈕的點擊次數。 若要完成這項工作，我們必須 `ViewController.cs` 在 **Solution Pad**中按兩下檔案，以開啟檔案進行編輯：

[![Solution Pad](hello-tvos-images/code01.png)](hello-tvos-images/code01.png#lightbox)

首先，我們需要在類別中建立類別層級的變數， `ViewController` 以追蹤已發生的點擊次數。 編輯類別定義，使它看起來如下所示：

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

接下來，在相同類別 (`ViewController`) 中，我們需要覆寫 **ViewDidLoad** 方法，並加入一些程式碼來設定標籤的初始訊息：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

我們需要使用 `ViewDidLoad` ，而不是另一個方法（例如 `Initialize` ），因為 `ViewDidLoad` 會在作業系統載入並具現化來自檔案的消費者介面 *之後* 呼叫 `.storyboard` 。 如果我們嘗試在檔案完全載入並具現化之前存取標籤控制項 `.storyboard` ，我們會收到錯誤， `NullReferenceException` 因為尚未建立標籤控制項。

接下來，我們需要新增程式碼來回應使用者按一下按鈕。 將下列內容新增至我們所建立的部分類別：

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

當使用者按一下按鈕時，就會呼叫此程式碼。

一切就緒之後，我們就可以開始建立及測試 tvOS 應用程式。

## <a name="testing-the-application"></a>測試應用程式

現在可以建立並執行我們的應用程式，以確保它如預期般執行。 我們可以在一個步驟中建立並執行全部，也可以在不執行的情況下建立。

每次建立應用程式時，我們都可以選擇我們想要的組建種類：

- **Debug** – debug 組建會編譯成「 (應用程式) 檔案，其中包含額外的中繼資料，可讓我們在應用程式執行時，對發生的情況進行偵錯工具。
- **發行** -發行組建也會建立 ' ' 檔案，但不包含 debug 資訊，因此它較小，執行速度更快。  

您可以從 Visual Studio for Mac 畫面左上角的設定選取 **器** ，選取組建的類型：

[![選取組建的類型](hello-tvos-images/run01.png)](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>建置應用程式

在我們的案例中，我們只想要有一個 debug 組建，因此請確定已選取 [ **debug** ]。 讓我們先藉由按 **⌘ B**來建立應用程式，或從 [ **組建** ] 功能表選擇 [ **全部組建**]。

如果沒有任何錯誤，您將會在 Visual Studio for Mac 的狀態列中看到 **組建成功** 訊息。 如果發生錯誤，請檢查您的專案，並確定您已正確遵循這些步驟。 先確認您的程式碼 (在 Xcode 和 Visual Studio for Mac) 符合教學課程中的程式碼。

### <a name="running-the-application"></a>執行應用程式

若要執行應用程式，我們有三個選項：

- 按 **⌘ + enter**。
- 從 [執行]**** 功能表中選擇 [偵錯]****。
- 按一下 Visual Studio for Mac 工具列中的 [播放]**** 按鈕 (在 [方案總管]**** 上方)。

應用程式將會建立 (（如果尚未建立）) ，在「偵測模式」中啟動，tvOS 模擬器將會啟動，而應用程式會啟動並顯示其主要介面視窗：

[![範例應用程式主畫面](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

從 [ **硬體** ] 功能表中，選取 [ **顯示 Apple TV Remote** ]，讓您可以控制模擬器。

[![選取 [顯示 Apple TV 遠端]](hello-tvos-images/run04.png)](hello-tvos-images/run04.png#lightbox)

使用模擬器的遠端時，如果您按一下按鈕幾次，就應該以計數來更新標籤：

[![具有已更新計數的標籤](hello-tvos-images/run05.png)](hello-tvos-images/run05.png#lightbox)

恭喜！ 我們在這裡討論過很多，但如果您已遵循本教學課程，請立即瞭解 tvOS 應用程式的元件，以及用來建立它們的工具。

## <a name="where-to-next"></a>接下來該怎麼做？

開發 Apple TV 應用程式會帶來幾項挑戰，因為使用者與介面之間的中斷連線 (它不在使用者的手) 中，而限制 tvOS 在應用程式大小和儲存空間上。

因此，我們強烈建議您先閱讀下列檔，然後再進入 Xamarin. tvOS 應用程式的設計：

- [TvOS 9 簡介](~/ios/tvos/platform/tvos9.md) -本文將介紹適用于 Xamarin tvOS 開發人員的 tvOS 9 中所有新的和修改過的 api 和功能。
- 使用[導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)：您的 tvOS 應用程式的使用者將不會像 iOS 一樣直接與其介面互動，因為它們會在裝置的螢幕上點出影像，但會使用 Siri 遠端在房間間間接地進行。 本文涵蓋焦點的概念，以及如何用它來處理 Xamarin. tvOS 應用程式消費者介面中的導覽。
- [Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md) -使用者將與 Apple TV 和您的 tvOS 應用程式互動的主要方式是透過包含的 Siri 遠端。 如果您的應用程式是一項遊戲，您可以選擇性地為您的應用程式中的 iOS (MFI) 藍牙遊戲控制器建立協力廠商支援。 本文說明如何在您的 tvOS 應用程式中支援新的 Siri 遠端和藍牙遊戲控制器。
- [資源和資料存放區](~/ios/tvos/app-fundamentals/resources-data-storage.md) （不同于 iOS 裝置）新的 Apple TV 不會為 tvOS apps 提供持續的本機儲存體。 如此一來，如果您的 tvOS 應用程式需要保存 (的資訊，例如使用者喜好設定) 它必須儲存並從 iCloud 取得該資料。 本文涵蓋在 tvOS 應用程式中使用資源和持續性資料存放區。
- 使用[圖示和](~/ios/tvos/app-fundamentals/icons-images.md)影像–建立迷人圖示和影像是為 Apple TV 應用程式開發沉浸式使用者體驗的重要部分。 本指南將涵蓋為您的 tvOS 應用程式建立和包含必要圖形資產所需的步驟。
- [消費者介面](~/ios/tvos/user-interface/index.md) –一般使用者體驗 (UX) 涵蓋範圍（包括消費者介面 (UI) 控制項）在使用 tvOS 時，請使用 Xcode 的 INTERFACE BUILDER 和 UX 設計原則。
- [部署和測試](~/ios/tvos/deploy-test/index.md) -本節涵蓋用來測試應用程式的主題，以及散發該應用程式的方式。 這裡的主題包括用於偵錯工具的工具、部署至測試人員，以及如何將應用程式發佈至 Apple TV App Store。

如果您在使用 tvOS 時遇到任何問題，請參閱我們的 [疑難排解](~/ios/tvos/troubleshooting.md) 檔，以取得已知問題和解決方案的清單。

## <a name="summary"></a>摘要

本文提供快速入門，以透過建立簡單的 Hello tvOS 應用程式，使用 Visual Studio for Mac 開發 tvOS 應用程式。 其中涵蓋了 tvOS 裝置布建、建立介面、在 tvOS 模擬器上 tvOS 和測試程式碼的基本概念。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [使用 Xamarin 建立適用于 tvOS 的應用程式 (影片) ](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
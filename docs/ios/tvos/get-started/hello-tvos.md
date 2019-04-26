---
title: Hello，tvOS 快速入門指南
description: 本指南逐步引導您建立第一個 Xamarin.tvOS 應用程式和其開發工具鏈。 它也會介紹 Xamarin 設計工具中，會公開 UI 控制項的程式碼，並說明如何建置、 執行和測試 Xamarin.tvOS 應用程式。
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 02/02/2018
ms.openlocfilehash: 8c8bf3f86091f49633913b37ef5108ddbae6d276
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60951461"
---
# <a name="hello-tvos-quick-start-guide"></a>Hello，tvOS 快速入門指南

_本指南逐步引導您建立第一個 Xamarin.tvOS 應用程式和其開發工具鏈。它也會介紹 Xamarin 設計工具中，會公開 UI 控制項的程式碼，並說明如何建置、 執行和測試 Xamarin.tvOS 應用程式。_

Apple 已發行 Apple TV、 Apple TV 4k、 執行 tvOS 11 第 5 個層的代。

開放給開發人員，讓他們能夠建立豐富的沉浸式應用程式並將它們發行至 Apple TV 的內建應用程式市集的 Apple TV 平台。

如果您已熟悉使用 Xamarin.iOS 開發，您應該會發現 tvOS 轉為相當簡單。 大部分的 Api 和功能都相同，不過，許多常見的 Api （例如，WebKit) 無法使用。 此外，使用與 Siri 遠端會造成不存在於基礎的觸控式螢幕的 iOS 裝置的一些設計挑戰。

本指南將提供使用 tvOS 的 Xamarin 應用程式中的簡介。 如需有關 tvOS 的詳細資訊，請參閱 Apple[準備迎接 Apple TV 4k](https://developer.apple.com/tvos/)文件。

## <a name="overview"></a>總覽

Xamarin.tvOS 可讓您開發中的完全原生 Apple TV 應用程式C#並使用相同的 OS X 程式庫和介面控制項中開發時所使用的.NET *Swift* (或*OBJECTIVE-C*) 和*Xcode*。

此外，自 Xamarin.tvOS 應用程式以C#和.NET 中，常見後, 端程式碼可以與 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 的應用程式，共用同時在每個平台上提供原生體驗。

這篇文章將為您介紹建立使用 Xamarin.tvOS 和 Visual Studio 逐步引導您建立基本的程序透過 Apple TV 應用程式所需的重要概念**Hello，tvOS**應用程式來計算次數按鈕具有已按下：

[![](hello-tvos-images/run05.png "執行範例應用程式")](hello-tvos-images/run05.png#lightbox)

我們將討論下列概念：

-  **Visual Studio for Mac** – Visual Studio for Mac，以及如何使用它建立 Xamarin.tvOS 應用程式的簡介。
-  **Xamarin.tvOS 應用程式的結構**– 什麼 Xamarin.tvOS 應用程式所組成。
-  **建立使用者介面**– 如何使用適用於 iOS 的 Xamarin 設計工具來建立使用者介面。
-  **部署和測試**– 如何執行和 tvOS 模擬器中，並在實際的 tvOS 的硬體上測試您的應用程式。

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>For Mac，然後在 Visual Studio 中啟動新的 Xamarin.tvOS 應用程式

如上所述，我們將建立 Apple TV 應用程式呼叫`Hello-tvOS`，將單一按鈕和標籤加入主畫面。 按一下按鈕時，標籤將會顯示已按下的次數。

若要開始，讓我們執行下列作業：

1. 啟動 Visual Studio for Mac：

    [![](hello-tvos-images/setup01.png "Visual Studio for Mac")](hello-tvos-images/setup01.png#lightbox)
2. 按一下**新方案...** 連結來開啟螢幕的左上角**新的專案** 對話方塊。
3. 選取 [ **tvOS** > **應用程式** > **單一檢視應用程式**然後按一下**下一步]** 按鈕：

    [![](hello-tvos-images/setup02.png "選取 單一檢視應用程式")](hello-tvos-images/setup02.png#lightbox)
4. 請輸入`Hello, tvOS`for**應用程式名稱**，輸入您**組織識別碼**，按一下 [**下一步]** 按鈕：

    [![](hello-tvos-images/setup04.png "輸入 Hello，tvOS")](hello-tvos-images/setup04.png#lightbox)
5. 請輸入`Hello_tvOS`for**專案名稱**，按一下 **建立**按鈕：

    [![](hello-tvos-images/setup03.png "輸入 HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio for Mac 將會建立新的 Xamarin.tvOS 應用程式，並顯示加入至您的應用程式方案的預設檔案：

 [![](hello-tvos-images/project01.png "預設的 [檔案] 檢視")](hello-tvos-images/project01.png#lightbox)

Visual Studio for Mac 使用**解決方案**並**專案**，Visual Studio 會執行完全相同方式。 方案是一個容器，可保存一或多個專案；專案則可包含應用程式，並支援程式庫、測試應用程式等。在此情況下，Visual Studio for Mac 已為您建立方案和應用程式專案。

如果您想要您可以建立一或多個程式碼包含通用的共用程式碼的程式庫專案。 這些程式庫專案可以由應用程式專案，或與 Xamarin.tvOS 應用程式的其他專案共用 （或 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 根據程式碼的類型），就像您一樣如果您正在建置標準的.NET 應用程式。

## <a name="anatomy-of-a-xamarintvos-app"></a>Xamarin.tvOS 應用程式的結構

如果您熟悉 iOS 程式設計，您會發現這裡有相似處很多。 事實上，tvOS 9 是 iOS 9 的子集，因此在這裡跨許多概念。

讓我們看看專案中的檔案：

-   `Main.cs` – 這包含應用程式的主要進入點。 當應用程式啟動時，這包含執行的第一個類別與方法。
-   `AppDelegate.cs` – 這個檔案包含負責從作業系統接聽事件的主應用程式類別。
-   `Info.plist` – 這個檔案包含應用程式屬性，例如應用程式名稱、 圖示等。
-   `ViewController.cs` – 這是表示主視窗和控制項的生命週期的類別。
-   `ViewController.designer.cs` – 這個檔案包含可協助您整合主畫面使用者介面的配管程式碼。
-  `Main.storyboard` – 在主視窗的 UI。 這個檔案可以是所建立和維護適用於 iOS 的 Xamarin 設計工具。

在下列章節中，我們將看過某些這類檔案。 我們將更新版本中，更詳細地探討它們，但它是個不錯的主意，現在了解其基本概念。

### <a name="maincs"></a>Main.cs

`Main.cs`檔案包含靜態`Main`方法會建立新的 Xamarin.tvOS 應用程式執行個體，並傳遞類別的名稱，將處理 OS 事件，在我們的案例中是`AppDelegate`類別：

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

`AppDelegate.cs`檔案包含我們`AppDelegate`類別，這是負責建立視窗和接聽 OS 事件：

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

此程式碼是可能不熟悉，除非您已經建置 iOS 應用程式前，但它就相當簡單。 讓我們檢查的重要程式行。

首先，讓我們看看在類別層級的變數宣告：

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

`Window`屬性可存取主視窗。 tvOS 使用所謂*模型檢視控制器*(MVC) 模式。 一般而言，針對您所建立的每一個視窗 （以及視窗內的許多項目），是一個控制站，也就是負責視窗的生命週期，例如顯示，加入新的檢視 （控制項） 等。

接下來，我們必須`FinishedLaunching`方法。 在具現化應用程式，並負責實際建立應用程式視窗，並在其中顯示檢視的程序開始後，就會執行此方法。 因為我們的應用程式會使用分鏡腳本，以定義其 UI，沒有額外的程式碼需要。 這裡

有許多這類範本中提供其他方法`DidEnterBackground`和`WillEnterForeground`。 這些是可以安全地移除如果未在您的應用程式中使用的應用程式事件。

### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController`類別是主視窗的控制站。 也就是說，它負責主視窗的生命週期。 我們這詳細檢查的更新版本，現在讓我們只看快速瀏覽它：

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

主視窗類別的設計工具檔案目前是空的但它會自動填入由 Visual Studio for Mac 中，我們會使用 「 iOS 設計工具建立使用者介面：

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

我們不通常涉及設計工具檔案，因為它們只由 Visual Studio for Mac 自動管理，並只是提供 允許存取控制項，我們將新增至任何視窗，或在我們的應用程式中檢視的必要的連接程式碼。

既然我們已經建立 Xamarin.tvOS 應用程式，我們有基本的了解它的元件，讓我們看看建立 UI。

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>建立使用者介面

您不必使用適用於 iOS 的 Xamarin 設計工具建立 Xamarin.tvOS 應用程式的使用者介面，可以建立 UI，直接從C#程式碼，但已超出本文的範圍。 為了簡單起見，我們將使用 iOS 設計工具來建立我們的 UI，本教學課程的其餘部分。

若要開始建立您的 UI，讓我們連按兩下`Main.storyboard`檔案中**方案總管 中**開啟以在 iOS 設計工具中編輯：

[![](hello-tvos-images/designer01.png "方案總管中的 Main.storyboard 檔案")](hello-tvos-images/designer01.png#lightbox)

這應該啟動設計工具，並如下所示：

[![](hello-tvos-images/designer02.png "設計工具")](hello-tvos-images/designer02.png#lightbox)

如需 iOS 設計工具的詳細資訊和其運作方式，請參閱[適用於 iOS 的 Xamarin 設計工具簡介](~/ios/user-interface/designer/introduction.md)指南。

我們現在可以開始將控制項加入至我們的 Xamarin.tvOS 應用程式的設計介面。

請執行下列動作：

1. 找出**工具箱**，它應該是右邊的設計介面：

    [![](hello-tvos-images/designer03.png "[工具箱]")](hello-tvos-images/designer03.png#lightbox)

    如果您無法在此找到它，請瀏覽至**檢視 > 板 > 工具箱**來檢視它。
2. 拖曳**標籤**從**工具箱**至設計介面：

    [![](hello-tvos-images/designer04.png "從 [工具箱] 拖曳一個標籤")](hello-tvos-images/designer04.png#lightbox)
3. 按一下**標題**中的屬性**屬性 pad**和按鈕的標題變更為 `Hello, tvOS`並設定**字型大小**128 到：

    [![](hello-tvos-images/designer05.png "設定 Hello，tvOS 的標題，並將字型大小設為 128")](hello-tvos-images/designer05.png#lightbox)
4. 調整標籤大小，讓所有文字會顯示，並將它置中靠近視窗頂端：

    [![](hello-tvos-images/designer06.png "調整大小，並將標籤置")](hello-tvos-images/designer06.png#lightbox)
5. 標籤現在必須限制為它的位置，使它看起來如預期般。 不論螢幕大小。 若要這樣做，請按一下直到標籤*T 圖形控點*出現：

    [![](hello-tvos-images/designer07.png "T 圖形控點")](hello-tvos-images/designer07.png#lightbox)
6. 若要以水平方式限制標籤，選取中央的矩形，並將它拖曳至垂直虛線：

    [![](hello-tvos-images/designer08.png "選取中央的矩形")](hello-tvos-images/designer08zoom.png#lightbox)

     標籤應該變成橘色。
7. 選取頂端的標籤中，T 控點，並將它拖曳至視窗的頂端：

    [![](hello-tvos-images/designer09.png "視窗的頂端拖曳控點")](hello-tvos-images/designer09.png#lightbox)
8. 接下來，按一下 寬度然後高度*骨控制代碼*如下所示：

    [![](hello-tvos-images/designer10.png "寬度和高度骨控點")](hello-tvos-images/designer10.png#lightbox)

     當每個*骨控制代碼*是按下，選取 Width 和 Height 分別設定固定的維度。
9. 完成時，您的條件約束看起來應該類似於 [屬性] 面板的 [配置] 索引標籤中：

    [![](hello-tvos-images/designer11.png "條件約束範例")](hello-tvos-images/designer11.png#lightbox)
8. 拖曳** 按鈕**從**工具箱**並將它放在標籤底下。
9. 按一下 **標題**中的屬性**屬性輸入板**和按鈕的標題變更為  `Click Me`:

    [![](hello-tvos-images/designer12.png "變更按一下 我的按鈕標題")](hello-tvos-images/designer12.png#lightbox)
10. 重複步驟 5 到 8 上述限制 tvOS 視窗中的按鈕。 不過，而不是將 T 控點拖曳到視窗頂端的 （如步驟 7） 中，將它拖曳到標籤的底部：

    [![](hello-tvos-images/designer14.png "限制 [] 按鈕")](hello-tvos-images/designer14.png#lightbox)
11. 拖曳按鈕下方的另一個標籤，其會作為第一個標籤，並將相同的寬度大小及其**對齊**要**中心**:

    [![](hello-tvos-images/designer15.png "拖曳按鈕下方的另一個標籤，其會與第一個標籤等寬，並將其對齊設定為中心的大小")](hello-tvos-images/designer15.png#lightbox)
12. 如同第一個標籤和按鈕，設定此標籤置中與將它釘選到位置和大小：

    [![](hello-tvos-images/designer16.png "將標籤釘選到位置和大小")](hello-tvos-images/designer16.png#lightbox)
13. 將變更儲存至使用者介面。

當您已調整大小並移動控制項，您應該已經注意到，設計工具可讓您很有幫助的貼齊提示為基礎[Apple TV 人性化介面指導方針](https://developer.apple.com/tvos/human-interface-guidelines/)。 這些指導方針可協助您建立高品質應用程式具有 Apple TV 使用者熟悉外觀和感覺。

如果您查看**文件大綱**區段中，注意 版面配置和構成使用者介面項目階層架構如何顯示：

[![](hello-tvos-images/designer17.png "[文件大綱] 區段")](hello-tvos-images/designer17.png#lightbox)

從這裡您可以選取要編輯或拖曳來重新排列 UI 元素，如有需要的項目。 比方說，如果 UI 項目已被另一個項目範圍內，您無法將它拖曳讓視窗上的最上層項目清單的底部。

既然我們有我們建立的使用者介面，我們必須公開 UI 項目，以便 Xamarin.tvOS 可以存取並與其互動C#程式碼。

### <a name="accessing-the-controls-in-the-code-behind"></a>存取程式碼後置中的控制項

有兩種主要的方式來存取您已新增 iOS 設計工具，從程式碼中的控制項：

* 在控制項上建立事件處理常式。
* 提供控制項的名稱，以便我們可以稍後參考它。

任一種加入時，部分類別內`ViewController.designer.cs`將會更新以反映所做的變更。 這可讓您存取檢視控制器中的控制項。

### <a name="creating-an-event-handler"></a>建立事件處理常式

在此範例應用程式中，按一下按鈕時，我們想_項目_發生，因此必須要加入至按鈕上的特定事件的事件處理常式。 若要設定此功能，執行下列作業：

1. 在 Xamarin iOS 設計工具中，選取 檢視控制器上的按鈕。
2. 在 屬性 面板中，選取**事件** 索引標籤：

    [![](hello-tvos-images/event1.png "[事件] 索引標籤")](hello-tvos-images/event1.png#lightbox)
3. 找出 TouchUpInside 事件，並為它提供事件處理常式命名為`Clicked`:

    [![](hello-tvos-images/event2.png "TouchUpInside 事件")](hello-tvos-images/event2.png#lightbox)
4. 當您按下**Enter**，則**ViewController**.cs 檔案將會開啟，建議您在程式碼中的事件處理常式的位置。 使用鍵盤上的方向鍵，將位置設定：

    [![](hello-tvos-images/event3.png "設定位置")](hello-tvos-images/event3.png#lightbox)
5. 這會建立部分的方法，如下所示：

    [![](hello-tvos-images/event4.png "部分方法")](hello-tvos-images/event4.png#lightbox)

我們現在已準備好開始新增一些程式碼，讓函式的按鈕。

### <a name="naming-a-control"></a>命名控制項

按一下按鈕時，應該更新標籤的點擊次數為基礎。 若要這樣做，我們將會需要存取程式碼中的標籤。 這是提供一個名稱。 請執行下列動作：

1. 開啟分鏡腳本，並選取底部的 檢視控制器的標籤。
2. 在 屬性 面板中，選取**Widget**  索引標籤：

    [![](hello-tvos-images/name1.png "選取 [小工具] 索引標籤")](hello-tvos-images/name1.png#lightbox)
3. 底下**身分識別 > 名稱**，新增`ClickedLabel`:

    [![](hello-tvos-images/name2.png "設定 ClickedLabel")](hello-tvos-images/name2.png#lightbox)

我們現在已準備好開始更新標籤 ！

### <a name="how-controls-are-accessed"></a>存取控制的方式

如果您選取`ViewController.designer.cs`中**方案總管 中**您將能夠看到如何`ClickedLabel`標籤和`Clicked`事件處理常式已對應至**插座**並**動作**在C#:

[![](hello-tvos-images/accesscontrol.png "輸出和動作")](hello-tvos-images/accesscontrol.png#lightbox)

您可能也注意`ViewController.designer.cs`是部分類別中，以便 Visual Studio for Mac 不需修改`ViewController.cs`這樣會覆寫我們對該類別的任何變更。

公開 UI 項目，如此一來，可讓您在檢視控制器進行存取。

您通常會永遠不需要開啟`ViewController.designer.cs`，它此處所提供教育之用。

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>撰寫程式碼

我們建立的使用者介面和其 UI 元素公開到程式碼，透過**插座**並**動作**，終於可以準備撰寫程式碼，以提供的程式功能。

在我們的應用程式中，每次按一下第一個按鈕時，我們會更新我們的標籤顯示按下按鈕的次數。 若要達成此目的，我們需要開啟`ViewController.cs`檔案進行編輯，按兩下**Solution Pad**:

[![](hello-tvos-images/code01.png "Solution Pad")](hello-tvos-images/code01.png#lightbox)

首先，我們需要建立一個類別層級變數，在我們`ViewController`類別來追蹤已按下的數目。 編輯類別定義，使它看起來如下所示：

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

接下來，在相同的類別 (`ViewController`)，我們需要覆寫**ViewDidLoad**方法，並新增一些程式碼來設定我們的標籤的初始訊息：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

我們需要使用`ViewDidLoad `，而不是另一個方法，例如`Initialize`，因為`ViewDidLoad `稱為*之後*OS 已經載入並具現化使用者介面與`.storyboard`檔案。 如果我們嘗試存取標籤控制項之前`.storyboard`檔案完全載入並具現化，我們會收到`NullReferenceException`錯誤因為標籤控制項不會尚未建立。

接下來，我們需要加入程式碼以回應使用者按下按鈕。 將下列加入至部分類別，我們建立：

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

使用者按一下按鈕時，會呼叫此程式碼。

所需的一切就緒，我們現在已準備好建置和測試我們的 Xamarin.tvOS 應用程式。

## <a name="testing-the-application"></a>測試應用程式

它就可以建置並執行我們的應用程式，以確定它如預期般執行。 可用來建置及執行全都放在一個步驟中，或我們可以建置它，而不執行它。

每當我們建置的應用程式時，我們可以選擇我們想要的組建種類：

-   **偵錯**– 偵錯組建會編譯成 ' （應用程式） 檔案，使用額外的中繼資料，讓我們來偵錯應用程式執行時，會發生什麼問題。
-   **釋放**– 發行組建也會建立 ' 檔案，但它並不包含偵錯的詳細資訊，因此後者較小且執行速度更快。  

您可以選取從組建的類型**組態選取器**左上角的 Visual Studio for Mac 畫面：

[![](hello-tvos-images/run01.png "選取組建的類型")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>建置應用程式

在我們的案例中，我們只想要偵錯組建，讓我們確定**偵錯**已選取。 我們將建置我們的應用程式第一次按下**⌘ b**，或從**建置**功能表上，選擇 **全部建置**。

如果沒有任何錯誤，您會看到**建置成功**在 Visual Studio for Mac 的狀態列上的訊息。 如果發生錯誤，檢閱您的專案，並確定已正確遵循這些步驟。 開始確認您的程式碼 （包括在 Xcode 和 Visual Studio for Mac 中） 在本教學課程中，要比對程式碼。

### <a name="running-the-application"></a>執行應用程式

若要執行應用程式，我們有三個選項：

-  按下 **⌘+Enter**。
-  從 [執行] 功能表中選擇 [偵錯]。
-  按一下 Visual Studio for Mac 工具列中的 [播放] 按鈕 (在 [方案總管] 上方)。

（如果它尚未建置的話），將建置的應用程式，將會啟動偵錯模式，tvOS 模擬器中的啟動和應用程式會啟動，並顯示它的主要介面視窗：

[![範例應用程式主畫面](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

從**硬體**功能表中，選取**顯示 Apple TV 遠端**讓您能夠控制模擬器。

[![](hello-tvos-images/run04.png "選取 Show Apple TV 遠端")](hello-tvos-images/run04.png#lightbox)

使用模擬器的遠端，如果您按一下按鈕幾次標籤應該更新為計數：

[![](hello-tvos-images/run05.png "已更新的計數與標籤")](hello-tvos-images/run05.png#lightbox)

恭喜您！ 我們涵蓋範圍甚廣，但如果您遵循本教學課程中從開始到完成時，您現在應該有充分的了解 Xamarin.tvOS 應用程式，以及用來建立這些工具的元件。

## <a name="where-to-next"></a>下一步 在何處？

開發 Apple TV 應用程式帶來一些挑戰，因為使用者介面 （它是不在使用者的手房間對面） 與限制 tvOS 之間中斷連線會放在應用程式大小和儲存體。

如此一來，我們強烈建議，您的閱讀下列文件才能跳入 Xamarin.tvOS 應用程式的設計：

- [TvOS 9 簡介](~/ios/tvos/platform/tvos9.md)– 這篇文章介紹的所有新的及修改 Api 和 tvOS 9 中可用的功能適用於 Xamarin.tvOS 開發人員。
- [使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)– Xamarin.tvOS 應用程式的使用者不會互動的介面直接做為 ios，他們點選映像，在裝置的畫面上，而間接地是從使用 Siri 遠端的空間。 本文章涵蓋的概念，焦點，以及如何用它來處理 Xamarin.tvOS 應用程式的使用者介面中的導覽。
- [Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)– 使用者將 Apple TV 中，與 Xamarin.tvOS 程式互動，是透過包含 Siri 遠端的主要方式。 如果您的應用程式是遊戲，您可以選擇性地建置的第 3 個合作對象，做為支援 iOS (MFI) 藍芽遊戲控制器您應用程式中。 本文章涵蓋 Xamarin.tvOS 應用程式中支援新 Siri 遠端和藍牙的遊戲控制器。
- [資源和資料存放區](~/ios/tvos/app-fundamentals/resources-data-storage.md)– 不同於 iOS 裝置，新的 Apple TV 不提供持續性的本機儲存體的 tvOS 應用程式。 如此一來，如果 Xamarin.tvOS 應用程式需要保有資訊 （例如使用者喜好設定），它必須儲存及擷取該資料從 iCloud。 本文章涵蓋資源與 Xamarin.tvOS 應用程式中的永續性資料儲存的工作。
- [使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)– 建立目眩的圖示和影像是開發您的 Apple TV 應用程式的擬真的使用者體驗的重要部分。 本指南將說明如何建立並將包含所需的圖形資產 Xamarin.tvOS 應用程式所需的步驟。
- [使用者介面](~/ios/tvos/user-interface/index.md)– 一般使用者體驗 (UX) 涵蓋範圍包括使用者介面 (UI) 控制項、 使用 Xcode 的 Interface Builder 和 UX 設計原則與 xamarin.tvos 搭配使用時。
- [部署和測試](~/ios/tvos/deploy-test/index.md)– 本章節包含用來測試應用程式，以及如何將它散發的主題。 此處的主題包括偵錯、 測試人員，以及如何在應用程式發行至 Apple TV App Store 的部署工具等項目。

如果您遇到使用與 xamarin.tvos 搭配使用的任何問題，請參閱我們[疑難排解](~/ios/tvos/troubleshooting.md)文件的清單上的已知問題與解決方案。

## <a name="summary"></a>總結

這篇文章提供快速開始使用開發應用程式 tvOS 的 Visual Studio for Mac 中建立簡單的 Hello，tvOS 應用程式。 它涵蓋 tvOS 裝置佈建，介面建立、 tvOS 的程式碼撰寫和 tvOS 模擬器上測試基本的概念。


## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [建置使用 Xamarin （影片） 的 tvOS 應用程式](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)

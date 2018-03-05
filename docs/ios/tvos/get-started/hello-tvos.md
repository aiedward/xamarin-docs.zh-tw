---
title: "Hello，tvOS 快速入門指南"
description: "本指南逐步引導您建立第一個 Xamarin.tvOS 應用程式和其開發工具鏈。 也引進了 Xamarin 設計工具中，它會公開 UI 控制項加入至程式碼中，並說明如何建置、 執行及測試 Xamarin.tvOS 應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/02/2018
ms.openlocfilehash: fe58aa8ffb74a9b6e937be5a7f1dde0432794405
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="hello-tvos-quick-start-guide"></a>Hello，tvOS 快速入門指南

_本指南逐步引導您建立第一個 Xamarin.tvOS 應用程式和其開發工具鏈。也引進了 Xamarin 設計工具中，它會公開 UI 控制項加入至程式碼中，並說明如何建置、 執行及測試 Xamarin.tvOS 應用程式。_

Apple 已發行 Apple TV，Apple TV 4 K 執行 tvOS 11 第 5 個的產生。

Apple TV 平台會開啟開發人員，讓它們來建立豐富、 沈浸式應用程式，並釋放它們透過 Apple 電視遙控器的內建應用程式市集。

如果您熟悉 Xamarin.iOS 開發，您應該會發現 tvOS 轉為相當簡單。 大部分的應用程式開發介面和功能都相同，不過，許多常見的 Api （例如，WebKit) 無法使用。 此外，使用與 Siri 遠端會帶來一些設計挑戰不存在於基礎的觸控螢幕的 iOS 裝置。

本指南將會使用 tvOS 中的 Xamarin 應用程式的簡介。 如需有關 tvOS 的詳細資訊，請參閱 Apple[備妥供 Apple TV 4k](https://developer.apple.com/tvos/)文件。

## <a name="overview"></a>總覽

Xamarin.tvOS 可讓您可以在 C# 和.NET 使用相同的 OS X 程式庫和中開發時使用的介面控制項的完整原生 Apple TV 應用程式的開發*Swift* (或*OBJECTIVE-C*) 和*Xcode*。

此外，由於 Xamarin.tvOS 應用程式以 C# 和.NET 撰寫，常見後, 端程式碼可以與共用 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 應用程式。同時在每個平台上傳遞原生的體驗。

本文將介紹建立帶您逐步建立基本的程序使用 Xamarin.tvOS 和 Visual Studio Apple TV 應用程式所需的重要概念**Hello，tvOS**計算次數的應用程式在按鈕的已按下：

[ ![](hello-tvos-images/run05.png "執行範例應用程式")](hello-tvos-images/run05.png)

我們將說明下列概念：

-  **Visual Studio for Mac** – Visual Studio for Mac 以及如何使用它建立 Xamarin.tvOS 應用程式的簡介。
-  **Xamarin.tvOS 應用程式的結構**– 什麼 Xamarin.tvOS 應用程式所組成。
-  **建立使用者介面**– 如何使用 Xamarin 適用於 iOS 的設計工具來建立使用者介面。
-  **部署和測試**– 如何執行和測試您的應用程式在 tvOS 模擬器和實際 tvOS 硬體上。

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>在 Visual Studio 中啟動新的 Xamarin.tvOS 應用程式，適用於 Mac

如前所述，我們將建立 Apple TV 應用程式呼叫`Hello-tvOS`的單一按鈕和標籤加入主畫面。 按一下按鈕時，標籤將會顯示已按下的次數。

若要開始，我們執行下列動作：

1. 啟動 Visual Studio for Mac：

    [ ![](hello-tvos-images/setup01.png "Visual Studio for Mac")](hello-tvos-images/setup01.png)
2. 按一下**新方案...**連結來開啟螢幕左上角**新專案** 對話方塊。
3. 選取**tvOS** > **應用程式** > **單一檢視應用程式**按一下**下一步**按鈕：

    [ ![](hello-tvos-images/setup02.png "選取單一檢視應用程式")](hello-tvos-images/setup02.png)
4. 輸入`Hello, tvOS`如**應用程式名稱**，輸入您**組織識別碼**按一下**下一步**按鈕：

    [ ![](hello-tvos-images/setup04.png "輸入 Hello，tvOS")](hello-tvos-images/setup04.png)
5. 輸入`Hello_tvOS`如**專案名稱**按一下**建立**按鈕：

    [ ![](hello-tvos-images/setup03.png "輸入 HellotvOS")](hello-tvos-images/setup03.png)

適用於 Mac 的 visual Studio 會建立新的 Xamarin.tvOS 應用程式，並顯示的預設檔案，加入至您的應用程式的方案：

 [ ![](hello-tvos-images/project01.png "檢視的預設檔案")](hello-tvos-images/project01.png)

Visual Studio for Mac 使用**解決方案**和**專案**，Visual Studio 會執行完全相同方式。 方案是一個容器，可保存一或多個專案；專案則可包含應用程式，並支援程式庫、測試應用程式等。在此情況下，Visual Studio for Mac 已為您建立方案和應用程式專案。

如果您想要您可以建立一或多個程式碼程式庫專案可包含通用的共用程式碼。 這些程式庫專案可以由應用程式專案或其他 Xamarin.tvOS 應用程式專案共用且 （或 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 基礎的程式碼類型），如同您在建置標準的.NET 應用程式時。

## <a name="anatomy-of-a-xamarintvos-app"></a>Xamarin.tvOS 應用程式的結構

如果您已熟悉 iOS 程式設計，您會發現以下的相似處很多。 事實上，tvOS 9 是 iOS 9 的子集，因此在這裡相交的概念很多。

讓我們看一下專案中的檔案：

-   `Main.cs` – 這包含應用程式的主要進入點。 當應用程式啟動時，這包含執行的第一個類別與方法。
-   `AppDelegate.cs` – 這個檔案包含會負責接聽事件從作業系統的主應用程式類別。
-   `Info.plist` – 這個檔案包含應用程式屬性，例如應用程式名稱、 圖示、 等等。
-   `ViewController.cs` – 這是代表主視窗，並控制它的生命週期的類別。
-   `ViewController.designer.cs` – 這個檔案包含可協助您整合主畫面使用者介面的基礎程式碼。
-  `Main.storyboard` – 在主要視窗的 UI。 這個檔案可以是所建立和維護適用於 iOS Xamarin 設計工具。

在下列章節中，我們將透過這當中有些檔案快速瀏覽。 我們將更新版本中，詳細探討它們，但最好現在了解其基本概念。

### <a name="maincs"></a>Main.cs

`Main.cs`檔案包含靜態`Main`方法以建立新的 Xamarin.tvOS 應用程式執行個體和傳遞類別的名稱將會處理 OS 事件，在我們的案例中是`AppDelegate`類別：

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

`AppDelegate.cs`檔案包含我們`AppDelegate`類別，這是負責建立我們視窗和接聽 OS 事件：

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

除非您建立 iOS 應用程式之前，但它就相當簡單，是可能不熟悉此程式碼。 讓我們來檢查行重要。

首先，讓我們看看類別層級變數宣告：

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

`Window`屬性可存取主視窗。 tvOS 使用所謂*模型檢視控制器*(MVC) 模式。 一般而言，針對您所建立的每一個視窗 （以及許多其他 windows 內的項目），是在控制站會負責視窗的生命週期，例如顯示，將新的檢視 （控制項） 加入至它，依此類推。

接下來，我們有`FinishedLaunching`方法。 這個方法會執行之後已產生應用程式，而且它會負責實際建立應用程式視窗，並開始在其中顯示檢視的程序。 因為我們的應用程式會以定義其 UI 使用分鏡腳本，沒有額外的程式碼需要。 這裡

有許多這類提供範本中的其他方法`DidEnterBackground`和`WillEnterForeground`。 這些是可以安全地移除如果未在應用程式中使用的應用程式事件。

### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController`類別是主視窗的控制站。 也就是說，它會負責在主視窗的生命週期。 我們這詳細檢查的更新版本，現在就讓我們快速查看它：

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

主視窗類別設計工具的檔案是空的但是它會自動填入由 Visual Studio for Mac 當我們使用 iOS 設計工具建立我們的使用者介面：

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

我們不通常著重於設計工具的檔案，因為這些元件只會自動管理由 Visual Studio for Mac，只是提供必要的基礎程式碼，讓我們將加入至任何視窗或在我們的應用程式中檢視的存取控制項。

現在，我們建立了我們 Xamarin.tvOS 的應用程式，並有基本了解它的元件，讓我們看看建立 UI。

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>建立使用者介面

您不必使用適用於 iOS 的 Xamarin 設計工具來建立 Xamarin.tvOS 應用程式的使用者介面，您可以直接從 C# 程式碼建立 UI，但這已超出本文的範圍。 為了簡單起見，我們將使用 iOS 設計工具來建立我們本教學課程的其餘的 UI。

若要開始建立您的 UI，讓我們按兩下`Main.storyboard`檔案**方案總管 中**開啟 iOS 設計工具中進行編輯：

[ ![](hello-tvos-images/designer01.png "[方案總管] 中的 Main.storyboard 檔案")](hello-tvos-images/designer01.png)

這應該啟動設計工具，並如下所示：

[ ![](hello-tvos-images/designer02.png "在設計工具")](hello-tvos-images/designer02.png)

如需 iOS 設計工具的詳細資訊和其運作方式，請參閱[Xamarin 設計工具，適用於 iOS 簡介](~/ios/user-interface/designer/introduction.md)指南。

我們現在可以開始將控制項加入至我們的 Xamarin.tvOS 應用程式的設計介面。

請執行下列動作：

1. 找出**工具箱**，應該要在設計介面右邊：

    [![](hello-tvos-images/designer03.png "[工具箱]")](hello-tvos-images/designer03.png)

    如果您無法在此找到，瀏覽至**檢視 > 填補 > 工具箱**來檢視它。
2. 拖曳**標籤**從**工具箱**至設計介面：

    [ ![](hello-tvos-images/designer04.png "從 [工具箱] 拖曳標籤")](hello-tvos-images/designer04.png)
3. 按一下**標題**屬性**屬性板**和按鈕的標題變更為 `Hello, tvOS`並設定**字型大小**128:

    [ ![](hello-tvos-images/designer05.png "將標題 Hello，tvOS 設定，並將字型大小設為 128")](hello-tvos-images/designer05.png)
4. 調整標籤的大小，讓所有文字會顯示，並將其置中靠近視窗頂端：

    [ ![](hello-tvos-images/designer06.png "調整大小，並將標籤置")](hello-tvos-images/designer06.png)
5. 標籤現在必須限制為它的位置，便會如預期般。 不論螢幕大小。 若要這樣做，請按一下直到標籤*T 圖形控點*出現：

    [ ![](hello-tvos-images/designer07.png "T 圖形的控點")](hello-tvos-images/designer07.png)
6. 若要以水平方式限制標籤，選取中央的矩形並將它拖曳至垂直虛線：

    [ ![](hello-tvos-images/designer08.png "選取中央的矩形")](hello-tvos-images/designer08zoom.png)

     標籤應該變成橙色。
7. 選取頂端的標籤，T 控點，並將它拖曳到視窗的頂端：

    [ ![](hello-tvos-images/designer09.png "拖曳到視窗頂端的控點")](hello-tvos-images/designer09.png)
8. 接下來，寬度按一下，然後按一下高度*骨控制代碼*如下所示：

    [ ![](hello-tvos-images/designer10.png "寬度和高度骨控點")](hello-tvos-images/designer10.png)

     當每個*骨控制代碼*是按一下，選取寬度和高度分別設定固定的維度。
9. 完成時，您的條件約束看起來應該類似於屬性輸入板的 [配置] 索引標籤：

    [ ![](hello-tvos-images/designer11.png "條件約束範例")](hello-tvos-images/designer11.png)
8. 拖曳**按鈕**從**工具箱**並將它放在標籤底下。
9. 按一下**標題**屬性**屬性板**和按鈕的標題變更為  `Click Me`:

    [ ![](hello-tvos-images/designer12.png "按鈕將標題變更為 Click Me")](hello-tvos-images/designer12.png)
10. 重複步驟 5 到 8 上述限制 tvOS 視窗中的按鈕。 不過，而不是將 T 控點拖曳到視窗頂端的 （如同步驟 #7），將它拖曳至標籤的下方：

    [ ![](hello-tvos-images/designer14.png "限制 [] 按鈕")](hello-tvos-images/designer14.png)
11. 拖曳按鈕之下的另一個標籤，大小就是相同的寬度，作為第一個標籤，並將其**對齊**至**Center**:

    [ ![](hello-tvos-images/designer15.png "拖曳按鈕之下的另一個標籤，將其第一個標籤的寬度相同並設定其對齊為中心的大小")](hello-tvos-images/designer15.png)
12. 如同第一個標籤和按鈕，設定此標籤置中，並將其釘選到位置和大小：

    [ ![](hello-tvos-images/designer16.png "釘選到位置和大小的標籤")](hello-tvos-images/designer16.png)
13. 將您的變更儲存至使用者介面。

當您調整大小並移動控制項周圍，您應該會注意到，設計工具能讓您很有幫助的嵌入式管理單元提示為基礎的[Apple TV 人性化介面指導方針](https://developer.apple.com/tvos/human-interface-guidelines/)。 這些指導方針將協助您建立高品質應用程式將會有 Apple TV 使用者熟悉外觀與風格。

若您查看**文件大綱**區段中，注意配置和組成我們的使用者介面項目階層中的顯示方式：

[ ![](hello-tvos-images/designer17.png "文件大綱區段")](hello-tvos-images/designer17.png)

從這裡您可以選取要編輯或重新排列 UI 項目，必要時拖曳項目。 比方說，如果 UI 項目已被另一個項目範圍內，您無法將它拖曳以容易視窗上的最上層項目清單的底部。

現在，我們已經我們建立的使用者介面時，我們需要公開 UI 項目，讓 Xamarin.tvOS 可以存取和 C# 程式碼與其互動。

### <a name="accessing-the-controls-in-the-code-behind"></a>存取程式碼後置中的控制項

有兩種主要的方式存取您在 iOS 設計工具，從程式碼中加入的控制項：

* 在控制項上建立事件處理常式。
* 為控制項指定的名稱，讓我們可以稍後參考它。

其中一個加入時，部分類別內`ViewController.designer.cs`將更新以反映變更。 這可讓您存取檢視控制器中的控制項。

### <a name="creating-an-event-handler"></a>建立事件處理常式

在此範例應用程式中，按一下按鈕時我們想_東西_發生，因此必須加入至按鈕上的特定事件的事件處理常式。 若要設定此項目，執行下列作業：

1. 在 Xamarin iOS 設計工具中，選取 檢視控制器上的按鈕。
2. 在屬性輸入板中，選取**事件** 索引標籤：

    [![](hello-tvos-images/event1.png "[事件] 索引標籤")](hello-tvos-images/event1.png)
3. 找出 TouchUpInside 事件，並為它提供名為事件處理常式`Clicked`:

    [![](hello-tvos-images/event2.png "TouchUpInside 事件")](hello-tvos-images/event2.png)
4. 當您按**Enter**、 **ViewController**.cs 檔案將會開啟，建議您在程式碼中的事件處理常式的位置。 使用鍵盤上的方向鍵來設定的位置：

    [![](hello-tvos-images/event3.png "設定的位置")](hello-tvos-images/event3.png)
5. 這會建立部分的方法，如下所示：

    [![](hello-tvos-images/event4.png "部分方法")](hello-tvos-images/event4.png)

我們現在已準備好開始加入一些程式碼來允許函式的按鈕。

### <a name="naming-a-control"></a>命名控制項

按一下按鈕時，應該更新標籤的點選次數為基礎。 若要這樣做，我們將會需要存取程式碼中的標籤。 這是提供一個名稱。 請執行下列動作：

1. 開啟分鏡腳本，並選取底部的 檢視控制器的標籤。
2. 在屬性輸入板中，選取**Widget**  索引標籤：

    [![](hello-tvos-images/name1.png "選取 [小工具] 索引標籤")](hello-tvos-images/name1.png)
3. 在下**識別 > 名稱**，新增`ClickedLabel`:

    [![](hello-tvos-images/name2.png "設定 ClickedLabel")](hello-tvos-images/name2.png)

我們現在已準備好開始更新標籤 ！

### <a name="how-controls-are-accessed"></a>存取控制項的方式

如果您選取`ViewController.designer.cs`中**方案總管 中**您將能夠看到如何`ClickedLabel`標籤和`Clicked`事件處理常式已對應至**插座**和**動作**C# 中：

[ ![](hello-tvos-images/accesscontrol.png "插座和動作")](hello-tvos-images/accesscontrol.png)

您可能也注意`ViewController.designer.cs`是部分類別，如此不需要修改 Visual Studio for Mac`ViewController.cs`這會覆寫，我們對類別的任何變更。

公開的 UI 項目，如此一來，可讓您檢視控制器中存取它們。

您通常會永遠不需要開啟`ViewController.designer.cs`自己，它已介紹教學之用。

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>撰寫程式碼

與我們建立的使用者介面公開給程式碼透過及其 UI 項目**插座**和**動作**，我們最後準備要撰寫的程式碼來提供深度的程式功能。

在我們的應用程式中，每次按一下第一個按鈕時，我們會更新我們的標籤，以顯示在按一下按鈕的次數。 若要達成此目的，我們必須開啟`ViewController.cs`檔案進行編輯按兩下**方案板**:

[ ![](hello-tvos-images/code01.png "方案填補")](hello-tvos-images/code01.png)

首先，我們需要建立一個類別層級變數中的我們`ViewController`類別來追蹤所有發生次數。 編輯類別定義，使它看起來如下所示：

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

接下來，在相同的類別 (`ViewController`)，我們需要覆寫**ViewDidLoad**方法並加入一些程式碼以設定初始訊息我們標籤：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

我們需要使用`ViewDidLoad `，而不是另一個方法，例如`Initialize`，因為`ViewDidLoad `稱為*之後*OS 已經載入並具現化使用者介面與`.storyboard`檔案。 如果我們嘗試存取 label 控制項之前`.storyboard`檔案已經完全載入並具現化，則會得到`NullReferenceException`錯誤因為會尚未建立 label 控制項。

接下來，我們需要加入程式碼以回應使用者按下按鈕。 將下列加入至部分類別，我們建立：

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

每當使用者按一下我們按鈕會呼叫此程式碼。

與任何進行中的項目，現在我們準備建置及測試 Xamarin.tvOS 應用程式。

## <a name="testing-the-application"></a>測試應用程式

它是建置與執行我們的應用程式，以確定其如預期般執行。 我們可以建置並執行所有在一個步驟中，或我們可以建置它，而不執行它。

每當我們建置的應用程式時，我們可以選擇何種建置我們想要：

-   **偵錯**– 偵錯組建會編譯成 ' 使用額外中繼資料，讓我們為偵錯應用程式執行時，會發生什麼動作 （應用程式） 檔案。
-   **釋放**– 也會建立發行組建 ' 檔案，但它不包含偵錯資訊，因此後者較小且執行速度更快。  

您可以選取的組建的型別**組態選取器**左上角的 Visual Studio for Mac 螢幕：

[ ![](hello-tvos-images/run01.png "選取組建的類型")](hello-tvos-images/run01.png)

### <a name="building-the-application"></a>建置應用程式

在我們的案例中，我們只想要偵錯組建，讓我們確定**偵錯**已選取。 我們來建置應用程式第一次下**⌘B**，或從**建置**功能表上，選擇**建置所有**。

如果沒有任何錯誤，您會看到**建置成功**Mac 的狀態列上的 Visual Studio 中的訊息。 如果發生錯誤，檢閱您的專案，並確定已正確遵循這些步驟。 先確認您的程式碼 （兩者在 Xcode 中，然後在 Visual Studio for Mac），符合教學課程中的程式碼。

### <a name="running-the-application"></a>執行應用程式

若要執行應用程式，我們有三個選項：

-  按下 **⌘+Enter**。
-  從 [執行] 功能表中選擇 [偵錯]。
-  按一下 Visual Studio for Mac 工具列中的 [播放] 按鈕 (在 [方案總管] 上方)。

（如果它尚未被已建立），將會建立應用程式開始在偵錯模式中，tvOS 模擬器會啟動，應用程式會啟動並顯示它的主要介面視窗：

[ ![範例應用程式主畫面](hello-tvos-images/run03.png)](hello-tvos-images/run03.png)

從**硬體**功能表中選取**顯示 Apple TV 遠端**讓您能夠控制模擬器。

[ ![](hello-tvos-images/run04.png "選取顯示 Apple TV 遠端")](hello-tvos-images/run04.png)

使用模擬器的遠端，如果您按一下按鈕的標籤幾次應該更新計數：

[ ![](hello-tvos-images/run05.png "包含更新計數的標籤")](hello-tvos-images/run05.png)

恭喜您！ 我們探討大量地在這裡，但如果您遵循本教學課程中從開始到完成時，您現在應該有紮 Xamarin.tvOS 應用程式，以及用來建立它們的工具的元件。

## <a name="where-to-next"></a>下一步 在何處？

開發 Apple TV 應用程式提供，因為使用者介面 （它是不在使用者的手遙控器輕鬆） 與限制 tvOS 之間中斷連線的一些挑戰放置於應用程式大小和儲存體。

如此一來，我們強烈建議，您的閱讀下列文件再 Xamarin.tvOS 應用程式的設計開始：

- [簡介 tvOS 9](~/ios/tvos/platform/tvos9.md) – 這篇文章導入 Xamarin.tvOS 開發人員的所有新的及修改應用程式開發介面和 tvOS 9 中可用的功能。
- [使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)– Xamarin.tvOS 應用程式的使用者不會互動的介面直接做為使用 iOS 其中他們點選映像的裝置 畫面中，但從透過間接使用 Siri 遠端聊天室。 本文涵蓋的概念焦點時，如何使用它來處理 Xamarin.tvOS 應用程式的使用者介面中導覽。
- [Siri 遠端和藍芽控制器](~/ios/tvos/platform/remote-bluetooth.md)– 使用者互動的 Apple TV 和 Xamarin.tvOS 應用程式，是透過包含 Siri 遠端的主要方式。 如果您的應用程式的遊戲，您可以選擇性地建置中的第 3 個合作對象，做為支援 iOS (MFI) 藍芽遊戲控制器您應用程式中。 本文涵蓋 Xamarin.tvOS 應用程式中支援的新 Siri 遠端和藍芽遊戲控制器。
- [資源和資料存放區](~/ios/tvos/app-fundamentals/resources-data-storage.md)– 不同於 iOS 裝置，新的 Apple TV 不提供持續的本機儲存體 tvOS 應用程式。 如此一來，如果 Xamarin.tvOS 應用程式需要保存的資訊 （例如使用者喜好設定） 它必須儲存，而且從 iCloud 擷取該資料。 本文件涵蓋使用資源和永續性資料儲存在 Xamarin.tvOS 應用程式。
- [使用圖示和映像](~/ios/tvos/app-fundamentals/icons-images.md)– 建立幻象圖示和圖像是開發應用程式 Apple TV 沈浸式使用者經驗很重要的一部分。 本指南將說明如何建立並包含必要的圖形資產 Xamarin.tvOS 應用程式所需的步驟。
- [使用者介面](~/ios/tvos/user-interface/index.md)– 一般使用者經驗 (UX) 涵蓋範圍包括使用者介面 (UI) 控制項，使用 Xcode 的介面產生器及 UX 設計原則使用 Xamarin.tvOS 時。
- [部署和測試](~/ios/tvos/deploy-test/index.md)– 本章節涵蓋用來測試應用程式，以及如何將它散發的主題。 此處的主題包含用來偵錯，測試人員，以及如何發佈至 Apple TV 應用程式市集應用程式部署工具等。

如果您執行使用 Xamarin.tvOS 所發生的任何問題，請參閱我們[疑難排解](~/ios/tvos/troubleshooting.md)清單上的文件了解問題和解決方案。

## <a name="summary"></a>總結

這篇文章提供快速開始使用開發應用程式 tvos Visual Studio for Mac 藉由建立簡單的 Hello，tvOS 應用程式。 它涵蓋 tvOS 裝置佈建，介面建立、 撰寫程式碼 tvos 和 tvOS 模擬器上測試基本的概念。


## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [建置 tvOS （影片） 的 xamarin 應用程式](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)

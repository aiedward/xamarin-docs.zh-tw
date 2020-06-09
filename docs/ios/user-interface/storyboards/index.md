---
title: Xamarin 中的分鏡腳本簡介
description: 本檔提供 Xamarin 中的分鏡腳本簡介。 其中描述如何使用分鏡腳本來定義使用者介面、segue，以及如何使用 iOS 設計工具來編輯分鏡腳本檔案。
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 0eead476fe7842ac326b61771776a83c7a35461c
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567381"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Xamarin 中的分鏡腳本簡介

在本指南中，我們將說明什麼是分鏡腳本，並檢查一些主要元件（例如 Segue）。 我們將探討如何建立和使用分鏡腳本，以及他們對開發人員有哪些好處。

在由 Apple 引進分鏡腳本檔案格式做為 iOS 應用程式 UI 的視覺標記法之前，開發人員會為每個 view controller 建立 XIB 檔案，並以手動方式設計每個視圖之間的導覽。  使用分鏡腳本可讓開發人員在設計介面上同時定義視圖控制器和導覽，並提供應用程式使用者介面的 WYSIWYG 編輯。

您可以使用 Xamarin iOS 設計工具來建立、開啟和編輯腳本。 本指南也將逐步解說如何使用設計工具來建立您的分鏡腳本，同時使用 c # 來設計導覽的程式。

## <a name="requirements"></a>規格需求

分鏡腳本可以搭配 Xcode、Visual Studio for Mac 中的 iOS 設計工具，以及已安裝 Xamarin 工作負載的 Visual Studio 2019 來使用。

## <a name="what-is-a-storyboard"></a>什麼是分鏡腳本？

分鏡腳本是應用程式中所有畫面的視覺標記法。 它包含一系列場景，其中每個場景都代表一個*視圖控制器*和其*Views*。 這些視圖可能包含物件和[控制項](~/ios/user-interface/controls/index.md)，可讓您的使用者與您的應用程式互動。 這組視圖和控制項（或*子檢視*）稱為「*內容視圖*」階層。 場景是透過 segue 物件進行連線，這代表視圖控制器之間的轉換。 這通常是藉由在初始視圖的物件與連接視圖之間建立 segue 來達成。 設計介面上的關聯性如下圖所示：

 [![](images/storyboardsview.png "The relationships on the design surface are illustrated in this image")](images/storyboardsview.png#lightbox)

如圖所示，分鏡腳本會配置已呈現內容的每個場景，並說明它們之間的連接。  此時值得注意的是，當我們討論 iPhone 上的場景時，可以安全地假設分鏡腳本上的一個*場景*等於裝置上的一個內容*畫面*。 不過，使用 iPad 時，可以一次顯示多個場景–例如，使用 Popover view 控制器。

使用分鏡腳本來建立應用程式的 UI 有許多優點，特別是在使用 Xamarin 時。 首先，它是 UI 的視覺標記法，因為所有物件（包括[自訂控制項](~/ios/user-interface/designer/ios-designable-controls-overview.md)）都是在設計階段呈現。
這表示在建立或部署您的應用程式之前，您可以將其外觀和流程視覺化。 例如，取得上述影像。 我們可以從設計介面快速查看有多少場景、每個觀點的版面配置，以及所有專案的關聯方式。 這就是讓分鏡腳本變得強大的功能。

事件可透過分鏡腳本更容易管理，特別是在使用 iOS 設計工具時。 大部分的 UI 控制項都會有 Properties Pad 中可能的事件清單。 您可以在這裡新增事件處理常式，並在視圖控制器類別的部分方法中完成。

腳本的內容會儲存為 XML 檔案。 在組建階段，任何檔案 `.storyboard` 都會編譯成二進位檔案，稱為 nibs。 在執行時間，這些 nibs 會初始化並具現化，以建立新的視圖。

## <a name="segues"></a>Segue

*Segue*（或*Segue 物件*）會在 iOS 開發中用來代表場景之間的轉換。 若要建立 segue，請按住**Ctrl**鍵，然後從一個場景中按一下滑鼠拖曳至另一個場景。 當我們拖曳滑鼠時，會出現藍色接頭，指出 segue 會在下圖中顯示的位置：

 [![](images/createsegue.png "A blue connector appears, indicating where the segue will lead as demonstrated in this image")](images/createsegue.png#lightbox)

當滑鼠開啟時，將會出現一個功能表，讓我們選擇 segue 的動作。 看起來可能會類似下列影像：

**IOS 之前8和大小的類別**：

[![](images/segue1.png "The Action Segue dropdown without Size Classes")](images/segue1.png#lightbox)

**當使用大小類別和彈性 Segue 時**：

[![](images/16new.png "The Action Segue dropdown with Size Classes")](images/16new.png#lightbox)

> [!IMPORTANT]
> 如果您使用 VMWare 作為 Windows 虛擬機器，依預設，Ctrl + 按一下 [對應] 滑鼠_右鍵_。 若要建立 Segue，請透過**喜好**設定鍵盤編輯您的鍵盤喜好設定  >  **& 滑鼠**  >  **按鍵快捷方式**並重新對應**次要按鈕**，如下所示：
>
> [![](images/image22.png "Keyboard and Mouse preference settings")](images/image22.png#lightbox)
>
> 您現在應該能夠在您的視圖控制器之間，以正常方式加入 segue。

轉換有不同的類型，每個都能控制如何向使用者呈現新的 view controller，以及如何與腳本中的其他視圖控制器互動。 如下所述。 您也可以將 segue 物件子類別化，以執行自訂轉換：

- **顯示/推送**–推播 segue 會將視圖控制器新增至導覽堆疊。 它假設源自推播的 view controller 屬於與要新增至堆疊的視圖控制器相同的流覽控制器。 這會執行與相同的工作 `pushViewController` ，而且通常會在畫面上的資料有某種關聯性時使用。 使用推播 segue 可讓您有更好的導覽列，其中包含將 [上一頁] 按鈕和標題新增至堆疊上的每個視圖，讓您能夠在視圖階層中向下切入導覽。
- **Modal**強制回應：強制回應 segue 會在專案中的任何兩個視圖控制器之間建立關聯性，並顯示動畫轉換的選項。 當您進入視圖時，子視圖控制器將會完全遮蔽父系視圖控制器。 不同于推播 segue，這會為我們新增 [上一頁] 按鈕;使用強制回應 segue 時，必須使用強制回應，才能 `DismissViewController` 回到先前的視圖控制器。
- **Custom** –任何自訂 segue 都可以建立為的子類別 `UIStoryboardSegue` 。
- 回溯-回溯 segue 可以用來透過推送或強制回應 segue 進行**流覽（例如**，藉由關閉以模式呈現的視圖控制器）。 除此之外，您不只可以回溯到一個，而是一系列的推送和強制回應 segue，並使用單一回溯動作在流覽階層中返回多個步驟。 若要瞭解如何在 iOS 中使用回溯 segue，請閱讀[建立回溯 segue](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue)配方。
- **Segue** – segue segue 會指出包含初始 view controller 的場景，因此使用者會先看到該控制項。 其以 segue 表示，如下所示：  

    [![](images/sourcelesssegue.png "A sourceless segue")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>適應性 Segue 類型

 iOS 8 引進了[大小類別](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes)，讓 iOS 分鏡腳本檔案能夠使用所有可用的螢幕大小，讓開發人員可以為所有 iOS 裝置建立一個 UI。 根據預設，所有新的 Xamarin iOS 應用程式都會使用大小類別。 若要使用較舊專案的大小類別，請參閱[整合](~/ios/user-interface/storyboards/unified-storyboards.md)的分鏡腳本簡介指南。

任何使用大小類別的應用程式也會使用新的調適型[*segue*](~/ios/user-interface/storyboards/unified-storyboards.md)。 使用大小類別時，請記住，我們不會直接指定使用 iPhone 或 iPad 的天氣。 換句話說，我們要建立一個總是看起來相同的 UI，而不論它必須使用多少實際資產。 調適型 Segue 的工作方式，是藉由判斷環境，以及判斷呈現內容的最佳方式。 調適型 Segue 如下所示：

[![](images/adaptivesegue.png "The Adaptive Segues dropdown")](images/adaptivesegue.png#lightbox)

|Segue|描述|
|--- |--- |
|顯示|這非常類似于推播 segue，但會將畫面的內容納入考慮。|
|顯示詳細資料|如果應用程式顯示主版和詳細資料檢視（例如，在 iPad 的分割視圖控制器中），內容將會取代詳細資料檢視。 如果應用程式只顯示主要或詳細資料，內容將會取代視圖控制器堆疊的頂端。|
|簡報|這類似于強制回應 segue，可讓您選取簡報和轉換樣式。|
|Popover 簡報|這會以 popover 的形式呈現內容|

### <a name="transferring-data-with-segues"></a>使用 Segue 傳輸資料

Segue 的優點不會以轉換結束。 它們也可以用來管理檢視控制器之間的資料傳輸。 這是藉由覆寫 `PrepareForSegue` 初始視圖控制器上的方法，並自行處理資料來達成。 觸發 segue 時（例如，按下按鈕），應用程式將會呼叫這個方法，讓您有機會在進行任何導覽*之前*準備新的視圖控制器。 下面的程式碼從[Phoneword](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)範例中示範：

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue,
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController
                                  as CallHistoryController;

    if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
}
```

在此範例中， `PrepareForSegue` 當使用者觸發 segue 時，會呼叫方法。 首先，我們必須建立「接收」視圖控制器的實例，並將它設定為 segue 的目的地視圖控制器。 這是由下列程式程式碼完成：

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

方法現在能夠在上設定屬性 `DestinationViewController` 。 在此範例中，我們藉由將名為的清單傳遞 `PhoneNumbers` 至 `CallHistoryController` ，並將它指派給相同名稱的物件，來利用這個方法：

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

轉換完成後，使用者會看到 `CallHistoryController` 具有已填入清單的。

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>將分鏡腳本新增至非分鏡腳本專案

有時，您可能需要將分鏡腳本新增至先前的非分鏡腳本檔案。 在 Visual Studio for Mac 中執行此動作之後，您可以遵循下列步驟來簡化這項作業：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 流覽至檔案 **> 新檔案 > iOS >** 腳本，以建立新的分鏡腳本檔案，如下所示：

    [![](images/new-storyboard-xs.png "The new file dialog")](images/new-storyboard-xs.png#lightbox)

2. 將您的分鏡腳本名稱新增至**plist**的**主要介面**區段，如下所示：

    [![](images/infoplist.png "The Info.plist editor")](images/infoplist.png#lightbox)

    這相當於在 `FinishedLaunching` 應用程式委派內的方法中具現化初始視圖控制器。 設定此選項後，應用程式會具現化視窗（如下所示）、載入主要的分鏡腳本，並將腳本的初始視圖控制器（segue Segue 旁邊的）實例指派為 `RootViewController` 視窗的屬性，然後讓視窗顯示在螢幕上。

3. 在中 `AppDelegate` ，覆寫預設 `Window` 方法，並使用下列程式碼來執行視窗屬性：

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 以滑鼠右鍵按一下專案以建立新的分鏡腳本檔案，**將 > 新檔案 > iOS > 空白**分鏡腳本，如下所示：

    [![](images/new-storyboard-vs.png "The new item dialog")](images/new-storyboard-vs.png#lightbox)

2. 將您的分鏡腳本名稱新增至 iOS 應用程式的**主要介面**區段，如下所示：

    [![](images/ios-app.png "The Info.plist editor")](images/ios-app.png#lightbox)

    這相當於在 `FinishedLaunching` 應用程式委派內的方法中具現化初始視圖控制器。 設定此選項後，應用程式會具現化視窗（如下所示）、載入主要的分鏡腳本，並將腳本的初始視圖控制器（segue Segue 旁邊的）實例指派為 `RootViewController` 視窗的屬性，然後讓視窗顯示在螢幕上。

3. 在中 `AppDelegate` ，覆寫預設 `Window` 方法，並使用下列程式碼來執行視窗屬性：

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

-----

## <a name="creating-a-storyboard-with-xcode"></a>使用 Xcode 建立分鏡腳本

您可以使用 Xcode 來建立及修改分鏡腳本，以用於以 Visual Studio for Mac 開發的 iOS 應用程式。

分鏡腳本會完全取代專案中的個別 XIB 檔案，不過，腳本中的個別 view controller 仍然可以使用來具現化 `Storyboard.InstantiateViewController` 。

有時候應用程式會有特殊需求，無法使用設計工具所提供的內建分鏡腳本轉換來處理。 例如，如果我們建立的應用程式會從相同的按鈕啟動不同的畫面，則根據應用程式的目前狀態而定，我們可能會想要手動具現化視圖控制器，並自行編寫轉換。

下列螢幕擷取畫面顯示在設計介面上的兩個 view controller，它們之間沒有 segue。 下一節將逐步解說如何在程式碼中設定該轉換。

1. 將_空白的 iPhone_分鏡腳本新增至現有的專案專案：

    [![](images/add-storyboard2.png "Adding storyboard")](images/add-storyboard2.png#lightbox)

2. 以滑鼠右鍵按一下分鏡腳本檔案，然後選取 [**開啟方式] > Xcode Interface Builder**在 Xcode 中開啟它。

    *如果您想要依預設使用 Xcode 介面產生器，您可以在 [**專案 > iOS**] 底下的 [Visual Studio for Mac 喜好設定] 中選擇它：*

![](images/set-preferred-designer-tool.png "Selecting the preferred designer tool")

3. 在 Xcode 中，開啟 [程式庫] （**透過 View > Show library**或*Shift + Command + L*）來顯示可加入至分鏡腳本的物件清單。 將 `Navigation Controller` 物件從清單拖曳至分鏡腳本，以將加入至分鏡腳本。 根據預設， `Navigation Controller` 會提供兩個畫面，右側的畫面是以較 `TableViewController` 簡單的視圖取代，因此可以藉由按一下此視圖並按下 Delete 鍵來移除。

    [![](images/add-navigation-controller.png "Adding a NavigationController from the Library")](images/add-navigation-controller.png#lightbox)

4. 此視圖控制器會有自己的自訂類別，而且它也需要自己的分鏡腳本識別碼。 當您按一下這個新加入的視圖上方的方塊時，將會有三個圖示，最左邊的圖示代表視圖的視圖控制器。 藉由選取此圖示，您可以在右窗格的 [身分識別] 索引標籤上設定 [類別] 和 [識別碼] 值。請將這些值設定為 `MainViewController` ，並確定檢查 `Use Storyboard ID` 。

    [![](images/identity-panel.png "Setting the MainViewController in the identity panel")](images/identity-panel.png#lightbox)

5. 再次使用程式庫，將 View Controller 拖曳到畫面上。 這會設定為根視圖控制器。 按住 Control 鍵，按一下左側的 [流覽控制器] 並拖曳至右邊的新加入視圖控制器，然後按一下功能表中的 [*根視圖控制器*]。

    [![](images/add-view-controller.png "Adding a NavigationController from the Library and setting the MainViewController as a Root View Controller")](images/add-view-controller.png#lightbox)

6. 此應用程式將會流覽至另一個視圖，因此，請將一個以上的視圖加入至分鏡腳本，就像之前一樣。 我們會將此稱為 `PinkViewController` ，並以與相同的方式來設定這些值 `MainViewController` 。

    [![](images/add-additional-view-controller.png "Adding an additional View Controller")](images/add-additional-view-controller.png#lightbox)

7. 由於 View Controller 將會有粉紅色的背景，因此可以使用 [屬性] 面板中的下拉式清單來設定該屬性 `Background` 。

    [![](images/set-pink-background.png "Adding an additional View Controller")](images/set-pink-background.png#lightbox)

8. 因為我們想要 `MainViewController` 流覽至，所以 `PinkViewController` 前者需要一個按鈕來與互動。 使用程式庫時，我們可以將按鈕新增至 `MainViewController` 。

    [![](images/add-button.png "Adding a Button to the MainViewController")](images/add-button.png#lightbox)

分鏡腳本已完成，但如果我們現在部署專案，將會出現空白畫面。 這是因為我們仍然需要告訴 IDE 使用我們的分鏡腳本，並將根視圖控制器設定為第一個視圖。 一般來說，這可以透過我們的專案選項來完成，如上所示。 不過在此範例中，我們會將下列內容新增至**AppDelegate**，以在程式碼中達到相同的結果：

```csharp
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    public static UIStoryboard Storyboard = UIStoryboard.FromName ("MainStoryboard", null);
    public static UIViewController initialViewController;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);

        initialViewController = Storyboard.InstantiateInitialViewController () as UIViewController;

        window.RootViewController = initialViewController;
        window.AddSubview(initialViewController.View);
        window.MakeKeyAndVisible ();
        return true;
    }
}
```

這是很多程式碼，但不熟悉幾行。 首先，我們會傳入分鏡腳本的名稱**mainstoryboard.storyboard**，以向**AppDelegate**註冊腳本。 接下來，我們會告訴應用程式在分鏡腳本中呼叫，以從分鏡腳本將初始視圖控制器具現化 `InstantiateInitialViewController` ，並將該視圖控制器設定為應用程式的根視圖控制器。 這個方法會決定使用者看到的第一個畫面，並建立該 View Controller 的新實例。

請注意，在 [解決方案] 窗格中，IDE 已建立 `MainViewcontroller.cs` 類別，而 `corresponding designer.cs` 當我們將類別名稱新增至步驟4中的 Properties Pad 時，會出現此情況。 我們可以看到這個類別建立了一個包含基類的特殊函式：

```csharp
public MainViewController (IntPtr handle) : base (handle)
{
}
```

使用 Xcode 建立分鏡腳本時，IDE 會自動將[[Register]](xref:Foundation.RegisterAttribute)屬性加入至類別的頂端 `designer.cs` ，並傳入字串識別碼，這與上一個步驟中指定的分鏡腳本 ID 相同。 這會將 c # 連結至分鏡腳本中的相關場景。

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
    public MainViewController (IntPtr handle) : base (handle)
    {
    }
    //...
}
```

如需註冊類別和方法的詳細資訊，請參閱[類型註冊機構](https://docs.microsoft.com/xamarin/ios/internals/registrar)檔。

此類別的最後一個步驟是將按鈕和轉換成粉紅色的視圖控制器。 我們會從分鏡腳本將具現化 `PinkViewController` ; 然後，我們會使用來設計推播 segue `PushViewController` ，如下列範例程式碼所示：

```csharp
public partial class MainViewController : UIViewController
{
    UIViewController pinkViewController;

    public MainViewController (IntPtr handle) : base (handle)
    {
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        this.Initialize ();
    }

    public void Initialize()
    {
        //Instantiating View Controller with Storyboard ID 'PinkViewController'
        pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //When we push the button, we will push the pinkViewController onto our current Navigation Stack
        PinkButton.TouchUpInside += (o, e) =&gt;
        {
            this.NavigationController.PushViewController (pinkViewController, true);
        };
    }
}
```

執行應用程式會產生2個畫面的應用程式：

![](images/finishedstoryboard.png "Sample app run screens")

## <a name="conditional-segues"></a>條件式 Segue

通常，從一個 view 控制器移到下一個，會視特定條件而定。 例如，如果我們建立一個簡單的登入畫面，只有在使用者名稱和密碼經過驗證之後，才會想要移到下*一個畫面。*

在下一個範例中，我們會在上述範例中新增密碼欄位。 只有當使用者輸入正確的密碼時，才能夠存取*PinkViewController* ，否則會顯示錯誤。

開始之前，請遵循上述步驟1–8。 在這些步驟中，我們會建立分鏡腳本、開始建立 UI，以及告訴我們的應用程式委派在 RootViewController 時要使用哪個視圖控制器。

1. 現在，讓我們來建立 UI，並新增列出的其他視圖， `MainViewController` 讓它看起來像下面的螢幕擷取畫面：

    - UITextField
        - 名稱： PasswordTextField
        - 預留位置： ' 輸入密碼密碼 '
    - UILabel
        - 文字：「錯誤：密碼錯誤。 您不應通過！ '
        - 色彩：紅色
        - 對齊：置中
        - 行數：2
        - 已核取 [隱藏] 核取方塊    

    [![](images/passwordvc.png "Center Lines")](images/passwordvc.png#lightbox)

2. 在 [移至粉紅色] 按鈕和 [視圖控制器] 之間建立 Segue，方法是 Ctrl-從*PinkButton*拖曳至*PinkViewController*，然後選取 [在滑鼠上**推送**]。

3. 按一下 Segue，並指定其*識別碼* `SegueToPink` ：

    [![](images/namesegue.png "Click on the Segue and give it the Identifier SegueToPink")](images/namesegue.png#lightbox)  

4. 最後，將下列 ShouldPerformSegue 方法新增至 `MainViewController` 類別：

    ```csharp
    public override bool ShouldPerformSegue (string segueIdentifier, NSObject sender)
    {

        if(segueIdentifier == "SegueToPink"){
            if (PasswordTextField.Text == "password") {
                PasswordTextField.ResignFirstResponder ();
                return true;
            }
            else{
                ErrorLabel.Hidden = false;
                return false;
            }
        }
        return base.ShouldPerformSegue (segueIdentifier, sender);
    }
    ```

在此程式碼中，我們已將 segueIdentifier 與我們的 segue 比對 `SegueToPink` ，因此我們可以在此案例中測試條件; 有效的密碼。 如果我們的條件傳回 `true` ，Segue 將會執行，而且會顯示 `PinkViewController` 。 如果為 `false` ，則不會顯示新的視圖控制器。

我們可以藉由檢查 ShouldPerformSegue 方法的 segueIdentifier 引數，將此方法套用到此視圖控制器上的任何 Segue。 在此情況下，我們只有一個 Segue 識別碼– `SegueToPink` 。

如需實用範例，請參閱分鏡腳本[範例](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard)中的條件式解決方案。

<a name="Using-Storyboard-References"></a>

## <a name="using-storyboard-references"></a>使用分鏡腳本參考

「分鏡腳本參考」可讓您進行大型、複雜的分鏡腳本設計，並將其分成較小的腳本，以從原始參考，進而移除複雜度，並讓產生的個別分鏡腳本更容易設計和維護。

此外，分鏡腳本參考可以將_錨點_提供給同一個分鏡腳本中的另一個場景，或不同的場景。

<a name="Referencing-an-External-Storyboard"></a>

### <a name="referencing-an-external-storyboard"></a>參考外部分鏡腳本

若要加入外部分鏡腳本的參考，請執行下列動作：

1. 在 [**方案總管**中，以滑鼠右鍵按一下專案名稱，然後**選取 [**  >  **新增檔案 ...**  >  ]。**iOS**  > 分鏡**腳本。** 輸入新分鏡腳本的**名稱**，然後按一下 [**新增**] 按鈕：

    [![](images/ref01.png "The New File Dialog")](images/ref01.png#lightbox)

2. 以您平常的方式設計新腳本的版面配置，並儲存您的變更：

    [![](images/ref02.png "The layout of the new scene")](images/ref02.png#lightbox)

3. 在 iOS 設計工具中，開啟您要加入參考的分鏡腳本。

4. 從 [**工具箱**] 將 [分鏡腳本**參考**] 拖曳至 [Design Surface：

    [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)

5. 在 [**屬性] Explorer**的 [ **Widget** ] 索引標籤中，選取您在上面**建立的分**鏡腳本名稱：

    [![](images/ref04.png "The Widget tab")](images/ref04.png#lightbox)

6. 在現有的場景上，以滑鼠右鍵按一下 UI Widget （例如按鈕），並建立新的 Segue 至您剛才建立的分鏡腳本**參考**：

    [![](images/ref05.png "Creating a segue")](images/ref05.png#lightbox)

7. 從快顯功能表選取 [**顯示**] 以完成 Segue：

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox)

8. 將您的變更儲存至分鏡腳本。

當應用程式執行時，如果使用者按一下您建立 Segue 的 UI 元素，則會顯示分鏡腳本參考中所指定之外部分鏡腳本的初始視圖控制器。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard"></a>

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>參考外部分鏡腳本中的特定場景

若要將參考新增至外部分鏡腳本（而不是初始視圖控制器）的特定場景，請執行下列動作：

1. 在 [**方案總管**中，按兩下外部分鏡腳本，將其開啟以進行編輯。

2. 加入新的場景，並以平常的方式設計它的版面配置：

    [![](images/ref07.png "The new scene layout")](images/ref07.png#lightbox)

3. 在 [**屬性] Explorer**的 [ **Widget** ] 索引標籤中，輸入新場景之 View 控制器的分鏡腳本**識別碼**：

    [![](images/ref08.png "Enter a Storyboard ID for the new Scenes View Controller")](images/ref08.png#lightbox)

4. 在 iOS 設計工具中，開啟您要加入參考的分鏡腳本。

5. 從 [**工具箱**] 將 [分鏡腳本**參考**] 拖曳至 [Design Surface：

    [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)

6. 在 [**屬性] Explorer**的 [ **Widget** ] 索引標籤中，選取腳本的名稱 **，以及您**先前建立之場景的**參考識別碼**（分鏡腳本識別碼）：

    [![](images/ref09.png "The Widget tab ")](images/ref09.png#lightbox)

7. 在現有的場景上，以滑鼠右鍵按一下 UI Widget （例如按鈕），並建立新的 Segue 至您剛才建立的分鏡腳本**參考**：

    [![](images/ref10.png "Creating a segue")](images/ref10.png#lightbox)

8. 從快顯功能表選取 [**顯示**] 以完成 Segue：

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox)

9. 將您的變更儲存至分鏡腳本。

當應用程式執行時，如果使用者按一下您建立 Segue 時所用的 UI 元素，就會顯示具有指定分鏡腳本參考中所指定之分鏡腳本**識別碼**的場景。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard"></a>

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>參考相同分鏡腳本中的特定場景

若要在相同的分鏡腳本中加入特定場景的參考，請執行下列動作：

1. 在 [**方案總管**中，按兩下分鏡腳本，將其開啟以進行編輯。

2. 加入新的場景，並以平常的方式設計它的版面配置：

    [![](images/ref11.png "The new scene layout")](images/ref11.png#lightbox)

3. 在 [**屬性] Explorer**的 [ **Widget** ] 索引標籤中，輸入新場景之 View 控制器的分鏡腳本**識別碼**：

    [![](images/ref12.png "The Widget tab")](images/ref12.png#lightbox)

4. 從 [**工具箱**] 將 [分鏡腳本**參考**] 拖曳至 [Design Surface：

   [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)

5. 在 [**屬性] 瀏覽器**的 [ **Widget** ] 索引標籤中，選取您先前建立之場景的 [**參考識別碼**（分鏡腳本識別碼）]：

    [![](images/ref13.png "The Widget tab")](images/ref13.png#lightbox)

6. 在現有的場景上，以滑鼠右鍵按一下 UI Widget （例如按鈕），並建立新的 Segue 至您剛才建立的分鏡腳本**參考**：

    [![](images/ref14.png "Creating a segue")](images/ref14.png#lightbox)

7. 從快顯功能表選取 [**顯示**] 以完成 Segue：

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox)

8. 將您的變更儲存至分鏡腳本。

當應用程式執行時，如果使用者按一下您建立 Segue 的 UI 元素，則會顯示在分鏡腳本參考中所指定的相同分鏡腳本中具有指定分鏡腳本**識別碼**的場景。

## <a name="summary"></a>總結

本文介紹分鏡腳本的概念，以及它們在 iOS 應用程式開發方面的好處。 其中討論場景、視圖控制器、視圖和視圖階層，以及場景如何與不同類型的 Segue 連結在一起。  此外，它也會從分鏡腳本手動探索具現化視圖控制器，並建立條件式 Segue。

## <a name="related-links"></a>相關連結

- [手動分鏡腳本（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard/)
- [IOS 設計工具簡介](~/ios/user-interface/designer/introduction.md)
- [轉換成分鏡腳本](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [UIStoryboard 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)

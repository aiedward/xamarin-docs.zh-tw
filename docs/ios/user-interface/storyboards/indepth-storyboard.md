---
title: Xamarin 中的分鏡腳本
description: 本檔提供適用于 Xamarin 的分鏡腳本簡介。 它說明如何使用腳本來定義使用者介面，以及 segue。
ms.prod: xamarin
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 37bdbfdc82946aec069325ff29e012f33b12cb13
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602394"
---
# <a name="storyboards-in-xamarinios"></a>Xamarin 中的分鏡腳本

在本指南中，我們將說明什麼是分鏡腳本，並檢查一些重要元件，例如 Segue。 我們將探討如何建立和使用分鏡腳本，以及它們對開發人員有何好處。

在 Apple 將分鏡腳本檔案格式導入為 iOS 應用程式 UI 的視覺標記法之前，開發人員為每個 view 控制器建立了 XIB 檔，並以手動方式在每個視圖之間進行導覽。  使用分鏡腳本可讓開發人員在設計介面上定義視圖控制器以及兩者之間的導覽，並提供應用程式使用者介面的 WYSIWYG 編輯。

您可以使用 Visual Studio for Mac 建立並開啟分鏡腳本。 本指南也將逐步解說如何使用 Xcode 介面產生器，在使用 c # 來設計流覽程式時建立分鏡腳本。

## <a name="requirements"></a>規格需求

分鏡腳本可以與 Xcode 搭配使用，並在 Visual Studio for Mac 上的 Xamarin 專案內啟動。

## <a name="what-is-a-storyboard"></a>什麼是分鏡腳本？

分鏡腳本是應用程式中所有畫面的視覺標記法。 它包含一系列的場景，每個場景都代表一個 *視圖控制器* 及其 *視圖*。 這些視圖可能包含物件和 [控制項](~/ios/user-interface/controls/index.md) ，可讓您的使用者與您的應用程式互動。 這個 (或 *子檢視*) 的視圖和控制項集合稱為「 *內容視圖* 階層」。 場景是透過 segue 物件來連接，這代表視圖控制器之間的轉換。 這通常是藉由在初始視圖的物件與連接視圖之間建立 segue 來達成。 設計介面上的關聯性如下圖所示：

 [![此圖說明設計介面上的關聯性。](images/storyboardsview.png)](images/storyboardsview.png#lightbox)

如圖所示，腳本會使用已經轉譯的內容來配置每一個場景，並說明兩者之間的連接。 當我們談到 iPhone 上的場景時，可以安全地假設腳本上的一個 *場景* 等於裝置上的一個 *畫面* 內容。 不過，有了 iPad，就可以一次顯示多個場景，例如使用 Popover view 控制器。

使用分鏡腳本來建立應用程式的 UI 有許多優點，特別是在使用 Xamarin 時。 首先，它是 UI 的視覺標記法，因為所有物件（包括 [自訂控制項](../designer/ios-designable-controls-overview.md) ）都會在設計階段呈現。
這表示在建立或部署您的應用程式之前，您可以將其外觀和流程視覺化。 例如，取得上一個影像。 我們可以從設計介面快速查看有多少場景、每個視圖的版面配置，以及一切相關的方式。 這就是讓分鏡腳本的強大功能。

您也可以使用分鏡腳本來管理事件。  大部分的 UI 控制項在 Properties Pad 中都有可能事件的清單。 您可以在此加入事件處理常式，並在視圖控制器類別的部分方法中完成。

分鏡腳本的內容會儲存為 XML 檔案。 在組建階段，任何檔案 `.storyboard` 都會編譯成二進位檔，稱為 nibs。 在執行時間，這些 nibs 會初始化並具現化，以建立新的視圖。

## <a name="segues"></a>Segue

*Segue* 或 *Segue 物件* 會在 iOS 開發中用來代表場景之間的轉換。 若要建立 segue，請按住 **Ctrl** 鍵，然後按一下並從一個場景拖曳到另一個場景。 當您拖曳滑鼠時，會出現藍色的接點，指出 segue 將會出現的位置。 如下圖所示：

 [![藍色的連接器隨即出現，指出 segue 將在此呈現的位置，如下圖所示](images/createsegue.png)](images/createsegue.png#lightbox)

在滑鼠上，會出現一個功能表，讓您選擇 segue 的動作。 它看起來可能類似下列影像：

**IOS 8 和大小類別**：

[![沒有大小類別的動作 Segue 下拉式清單](images/segue1.png)](images/segue1.png#lightbox)

**使用大小類別和適應性 Segue 時**：

[![具有大小類別的動作 Segue 下拉式清單](images/16new.png)](images/16new.png#lightbox)

> [!IMPORTANT]
> 如果您在 Windows 虛擬機器上使用 VMWare，依預設，Ctrl + 按一下會對應為滑鼠 _右鍵_ 按鈕。 若要建立 Segue，請透過 **喜好** 設定  >  **鍵盤 & 滑鼠** 按鍵快速鍵來編輯按鍵盤喜好設定，並重新對應  >   **次要按鈕**，如下所示：
>
> [![鍵盤和滑鼠喜好設定](images/image22.png)](images/image22.png#lightbox)
>
> 您現在應該可以在您的視圖控制器之間新增 segue。

轉換有不同的類型，每個類型都可控制如何向使用者呈現新的視圖控制器，以及如何與腳本中的其他視圖控制器互動。 以下將說明這些情況。 您也可以子類別化 segue 物件來執行自訂轉換：

- **顯示/推送** –推送 segue 會將 view 控制器新增至導覽堆疊。 它會假設源自于推播的視圖控制器是與要新增至堆疊的視圖控制器相同之流覽控制器的一部分。 這會執行相同的工作  `pushViewController` ，而且通常會在螢幕上的資料有某些關聯性時使用。 使用推播 segue 可讓您擁有具有 [上一頁] 按鈕和標題的導覽列，並新增至堆疊上的每個視圖，讓您可以向下切入流覽階層。
- 強制 **回應-強制** 回應 segue 會在專案中的任何兩個視圖控制器之間建立關聯性，並可選擇顯示動畫轉換。 當您進入 view 時，子視圖控制器將會完全混淆父視圖控制器。 不同于推播 segue （為我們新增 [上一頁] 按鈕），您必須在使用強制回應 segue 時使用，才能 `DismissViewController` 回到上一個視圖控制器。
- **自訂** –任何自訂 segue 都可以建立為的子類別 `UIStoryboardSegue` 。
- 回溯 **–回溯** segue 可用來透過推播或強制回應 segue （例如，藉由關閉以強制回應方式顯示的視圖控制器）進行流覽。 此外，您不只可以透過一系列的推播和強制回應 segue，也可以回溯流覽階層中的多個步驟，並使用單一回溯動作。 若要瞭解如何使用 iOS 中的回溯 segue，請閱讀  [建立回溯 segue](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) 配方。
- **Segue** – segue segue 表示包含初始 view 控制器的場景，因此使用者會先看到該控制項。 它是以如下所示的 segue 來表示：

    [![Segue segue](images/sourcelesssegue.png)](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>適應性 Segue 類型

 iOS 8 引進了 [大小類別](../storyboards/unified-storyboards.md#size-classes) ，可讓 iOS 分鏡腳本檔案使用所有可用的螢幕大小，讓開發人員能夠為所有 iOS 裝置建立一個 UI。 根據預設，所有新的 Xamarin iOS 應用程式都會使用大小類別。 若要從較舊的專案使用大小類別，請參閱整合式分鏡腳本指南的 [簡介](~/ios/user-interface/storyboards/unified-storyboards.md) 。

使用大小類別的任何應用程式也會使用新的調適型 [*segue*](unified-storyboards.md)。 使用大小類別時，請記住，您不會直接指定您使用的是 iPhone 或 iPad。 換句話說，您要建立一個永遠看起來相同的使用者介面，而不管它有多少真實資產需要使用。 調適型 Segue 的運作方式是判斷環境，並決定最適合呈現內容的方式。 適應性 Segue 如下所示：

[![自 Segue 下拉式清單](images/adaptivesegue.png)](images/adaptivesegue.png#lightbox)

|Segue|Description|
|--- |--- |
|顯示|這非常類似于推播 segue，但會將畫面的內容列入考慮。|
|顯示詳細資料|如果應用程式顯示主要和詳細資料檢視 (例如，在 iPad) 的分割視圖控制器中，內容將會取代詳細資料檢視。 如果應用程式只顯示主要或詳細資料，內容將會取代視圖控制器堆疊的頂端。|
|簡報|這類似于強制回應 segue，可讓您選擇簡報和轉換樣式。|
|Popover 簡報|這會以 popover 的形式呈現內容。|

### <a name="transferring-data-with-segues"></a>使用 Segue 傳輸資料

Segue 的優點不會以轉換做為結尾。 它們也可以用來管理檢視控制器之間的資料傳輸。 這是藉由覆寫 `PrepareForSegue` 初始視圖控制器上的方法並自行處理資料來達成。 觸發 segue 時（例如，在按下按鈕時），應用程式會呼叫這個方法，讓您有機會在進行任何導覽 *之前* 準備新的視圖控制器。 [Phoneword](/samples/xamarin/ios-samples/hello-ios)範例中的下列程式碼會示範：

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

在此範例中， `PrepareForSegue` 當使用者觸發 segue 時，就會呼叫方法。 您必須先建立「接收」視圖控制器的實例，並將其設定為 segue 的目的地視圖控制器。 這會透過下列程式程式碼來完成：

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

方法現在可以在上設定屬性 `DestinationViewController` 。 這個範例會將名 `PhoneNumbers` 為的清單傳遞給，並將 `CallHistoryController` 其指派給相同名稱的物件，以充分利用這項功能：

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

轉換完成後，使用者將會看到 `CallHistoryController` 包含已填入清單的。

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>將分鏡腳本新增至非分鏡腳本專案

有時，您可能需要將分鏡腳本新增至先前的非分鏡腳本檔案。 您可以遵循下列步驟，以簡化 Visual Studio for Mac 中的程式：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 流覽至檔案 **> 新的檔案 > iOS >** 腳本，以建立新的分鏡腳本檔案。

    [![[新增檔案] 對話方塊](images/new-storyboard-xs.png)](images/new-storyboard-xs.png#lightbox)

2. 將您的分鏡腳本名稱新增至 **plist** 的 **主要介面** 區段。

    [![Plist 編輯器](images/infoplist.png)](images/infoplist.png#lightbox)

    這等同于在 `FinishedLaunching` 應用程式委派內的方法中具現化初始視圖控制器。 設定此選項之後，應用程式會具現化視窗 (請參閱下一個步驟) 、載入主要分鏡腳本，然後指派腳本初始視圖控制器的實例 (segue Segue) 的實例，做為 `RootViewController` 視窗的屬性。 然後讓視窗顯示在畫面上。

3. 在中 `AppDelegate` ，使用下列程式碼覆寫預設 `Window` 方法，以執行視窗屬性：

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 以滑鼠右鍵按一下專案來建立新的分鏡腳本檔案，以 **將 > 新檔案 > iOS > 空白** 分鏡腳本。

    [![[新增專案] 對話方塊](images/new-storyboard-vs.png)](images/new-storyboard-vs.png#lightbox)

2. 將您的分鏡腳本名稱新增至 iOS 應用程式的 **主要介面** 區段。

    [![Plist 編輯器](images/ios-app.png)](images/ios-app.png#lightbox)

    這等同于在 `FinishedLaunching` 應用程式委派內的方法中具現化初始視圖控制器。 設定此選項之後，應用程式會具現化視窗 (請參閱下一個步驟) 、載入主要分鏡腳本，然後指派腳本初始視圖控制器的實例 (segue Segue) 的實例，做為 `RootViewController` 視窗的屬性。 然後讓視窗顯示在畫面上。

3. 在中 `AppDelegate` ，使用下列程式碼覆寫預設 `Window` 方法，以執行視窗屬性：

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

-----

## <a name="creating-a-storyboard-with-xcode"></a>使用 Xcode 建立分鏡腳本

您可以使用 Xcode 來建立和修改分鏡腳本，以便在使用 Visual Studio for Mac 開發的 iOS 應用程式中使用。

分鏡腳本會完全取代專案中的個別 XIB 檔案，不過，分鏡腳本中的個別視圖控制器仍可使用具現化 `Storyboard.InstantiateViewController` 。

有時候應用程式會有特殊需求，無法使用設計工具提供的內建分鏡腳本轉換來處理。 例如，如果您要建立的應用程式會從相同的按鈕啟動不同的螢幕，視應用程式的目前狀態而定，您可能會想要手動具現化視圖控制器，並自行編寫轉換的程式。

下列螢幕擷取畫面顯示設計介面上的兩個 view 控制器，其之間沒有任何 segue。 下一節會逐步解說如何在程式碼中設定轉換。

1. 將 _空白的 iPhone_ 分鏡腳本新增至現有的專案專案：

    [![加入分鏡腳本](images/add-storyboard2.png)](images/add-storyboard2.png#lightbox)

2. 按兩下分鏡腳本檔案或按一下滑鼠右鍵，然後選取 [ **開啟方式] > Xcode 介面** 產生器，以在 Xcode 的介面產生器中開啟它。

3. 在 Xcode 中，透過 **View > Show library** 或 **Shift + Command + L**) 來開啟程式庫 (，以顯示可加入至腳本的物件清單。 將 `Navigation Controller` 物件從清單拖曳至腳本，以將加入至腳本。 根據預設， `Navigation Controller` 將會提供兩個畫面。 右邊的畫面是 `TableViewController` 您將以較簡單的觀點來取代的畫面，因此可以藉由按一下視圖並按下 Delete 鍵來移除。

    [![從程式庫新增 NavigationController](images/add-navigation-controller.png)](images/add-navigation-controller.png#lightbox)

4. 此 view 控制器將會有自己的自訂類別，而且它也需要自己的分鏡腳本識別碼。 當您按一下這個新加入的視圖上方的方塊時，有三個圖示，最左邊的代表視圖的視圖控制器。 藉由選取此圖示，您就可以在右窗格的 [識別] 索引標籤上設定 [類別] 和 [識別碼] 值。將這些值設定為 `MainViewController` ，並確定檢查 `Use Storyboard ID` 。

    [![在身分識別面板中設定 MainViewController](images/identity-panel.png)](images/identity-panel.png#lightbox)

5. 再次使用程式庫，將 View Controller 控制項拖曳到畫面上。 這會設定為 [根視圖控制器]。 按住 **ctrl** 鍵，然後在左側的導覽控制器中按一下並拖曳至右側新增的 View controller，然後選取功能表中的 [ **根視圖控制器** ]。

    [![從程式庫新增 NavigationController，並將 MainViewController 設定為根視圖控制器](images/add-view-controller.png)](images/add-view-controller.png#lightbox)

6. 此應用程式會流覽至另一個視圖，因此請將另一個視圖新增至腳本，就像之前一樣。 呼叫它 `PinkViewController` ，並使用與相同的方式來設定這些值 `MainViewController` 。

    [![螢幕擷取畫面顯示具有三個視圖的分鏡腳本。](images/add-additional-view-controller.png)](images/add-additional-view-controller.png#lightbox)

7. 因為 view 控制器將會有粉紅色的背景，請使用的旁邊的下拉式清單，在 [屬性] 面板中設定該屬性 `Background` 。

    [![螢幕擷取畫面顯示上一個步驟中的分鏡腳本，並將最右邊的畫面變更為粉紅色背景。](images/set-pink-background.png)](images/set-pink-background.png#lightbox)

8. 因為我們想要 `MainViewController` 流覽至，所以 `PinkViewController` 前者需要一個按鈕來與互動。 使用程式庫將按鈕新增至 `MainViewController` 。

    [![將按鈕新增至 MainViewController](images/add-button.png)](images/add-button.png#lightbox)

腳本已完成，但如果您現在部署專案，則會出現空白畫面。 這是因為您仍然需要告知 IDE 使用分鏡腳本，並設定根視圖控制器做為第一個視圖。 一般來說，這可以透過專案選項來完成，如先前所示。 不過，在此範例中，我們會將下列程式碼新增至 **AppDelegate** ，以達成相同的結果：

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

這是很多程式碼，但不熟悉幾行。 首先，藉由傳遞腳本的名稱 **MainStoryboard**，向 **AppDelegate** 註冊分鏡腳本。 接下來，您會在腳本上呼叫，告訴應用程式從分鏡腳本將初始視圖控制器具現化 `InstantiateInitialViewController` ，並將該 view 控制器設定為應用程式的根視圖控制器。 這個方法會決定使用者看到的第一個畫面，並建立該 View 控制器的新實例。

請注意，在 [方案] 窗格中， `MainViewcontroller.cs` `*.designer.cs` 當您將類別名稱新增至步驟4中的 [屬性] 面板時，IDE 會建立類別和其對應的檔案。 這個類別建立了包含基類的特殊函式：

```csharp
public MainViewController (IntPtr handle) : base (handle)
{
}
```

使用 Xcode 建立分鏡腳本時，IDE 會自動將 [[Register]](xref:Foundation.RegisterAttribute) 屬性加入至類別的頂端 `*.designer.cs` ，並傳入與上一個步驟中指定的分鏡腳本識別碼相同的字串識別碼。 這會將 c # 連結到分鏡腳本中的相關場景。

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

如需註冊類別和方法的詳細資訊，請參閱 [類型註冊機構](../../internals/registrar.md)。

此類別中的最後一個步驟是連接按鈕，並將轉換成粉紅色視圖控制器。 您將從分鏡腳本中具現化， `PinkViewController` 然後您會使用來撰寫推播 segue `PushViewController` ，如下列範例程式碼所示：

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

![範例應用程式執行畫面](images/finishedstoryboard.png)

## <a name="conditional-segues"></a>條件式 Segue

通常，從某個 view 控制器移至下一個控制器時，會相依于特定的條件。 例如，如果我們要建立簡單的登入畫面，只要使用者名稱和密碼經過驗證，就會想要移至下 *一個畫面。*

在下一個範例中，我們會將密碼欄位新增至先前的範例。 如果使用者輸入正確的密碼，使用者將只能存取 *PinkViewController* ，否則將會顯示錯誤。

開始之前，請遵循先前的步驟1–8。 在這些步驟中，我們會建立分鏡腳本，開始建立 UI，並告訴我們的應用程式委派要使用哪個 view controller 作為 RootViewController。

1. 現在，讓我們來建立 UI，並將列出的其他視圖新增至， `MainViewController` 使其看起來像下面的螢幕擷取畫面所示：

    - UITextField
        - 名稱： PasswordTextField
        - 預留位置：「輸入秘密密碼」
    - UILabel
        - 文字： ' 錯誤：密碼錯誤。 您不應通過！」
        - 色彩：紅色
        - 對齊方式：置中
        - 行：2
        - 已核取 [隱藏] 核取方塊    

    [![中間行](images/passwordvc.png)](images/passwordvc.png#lightbox)

2. 在 [移至粉紅色] 按鈕和 [view controller] 之間建立 Segue，方法是從 *PinkButton* 拖曳至 *PinkViewController*，然後選取 [在滑鼠上 **按下** ]。

3. 按一下 [Segue]，並為其指定 *識別碼* `SegueToPink` ：

    [![按一下 Segue 並提供其識別碼 SegueToPink](images/namesegue.png)](images/namesegue.png#lightbox)  

4. 最後，將下列 `ShouldPerformSegue` 方法新增至 `MainViewController` 類別：

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

在此程式碼中，我們已將 segueIdentifier 與我們的 `SegueToPink` segue 相符，因此我們可以測試條件，在此案例中是有效的密碼。 如果我們的條件傳回 `true` ，Segue 將會執行並顯示 `PinkViewController` 。 若為 `false` ，則不會顯示新的視圖控制器。

我們可以藉由將 segueIdentifier 引數檢查至 ShouldPerformSegue 方法，將此方法套用到此 view controller 上的任何 Segue。 在此情況下，我們只有一個 Segue 識別碼– `SegueToPink` 。

Refer to the Storyboards.Conditional solution in the [Manual Storyboards sample](/samples/xamarin/ios-samples/manualstoryboard) for a working example.

## <a name="summary"></a>摘要

本文介紹分鏡腳本的概念，以及它們如何在 iOS 應用程式的開發方面有所説明。 其中討論場景、view controller、views 和 view 階層，以及如何將場景與不同類型的 Segue 連結在一起。  它也會探索從分鏡腳本手動具現化視圖控制器，並建立條件式 Segue。

## <a name="related-links"></a>相關連結

- [手動分鏡腳本 (範例) ](/samples/xamarin/ios-samples/manualstoryboard/)
- [IOS Designer 簡介](../designer/introduction.md)
- [轉換成分鏡腳本](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [UIStoryboard 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)

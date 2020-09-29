---
title: Xamarin 中的索引標籤列和索引標籤列控制器
description: 本檔說明 iOS 索引標籤列控制器，以及如何搭配使用這些選項與 Xamarin。 它會示範如何設定 UITabBarController、使用影像、設定徽章值、處理事件等等。
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 9bd048239c404c0eb3309fdc74b26bcb94db4740
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434122"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Xamarin 中的索引標籤列和索引標籤列控制器

在 iOS 中使用索引標籤式應用程式來支援使用者介面，讓您不需要特定的順序即可存取多個畫面。 透過此 `UITabBarController` 類別，應用程式可以輕鬆地包含對這類多重畫面案例的支援。 `UITabBarController` 負責進行多重畫面管理，讓應用程式開發人員能夠專注于每個畫面的詳細資料。

通常，索引標籤式應用程式是以 `UITabBarController` 主視窗的成為建立 `RootViewController` 。 不過，有一點額外的程式碼，索引標籤式應用程式也可以連續使用於一些其他的初始畫面，例如，應用程式首先顯示登入畫面的案例，後面接著索引標籤式介面。

此頁面會討論這兩種案例：當索引標籤位於應用程式視圖階層的根目錄時，也會在非 `RootViewController` 案例中。

## <a name="introducing-uitabbarcontroller"></a>UITabBarController 簡介

支援下列各項的索引標籤 `UITabBarController` 式應用程式開發：

- 允許將多個控制器新增至其中。
- 透過類別提供索引標籤式使用者介面，  `UITabBar` 可讓使用者在控制器與其視圖之間切換。

控制器會透過 `UITabBarController` 其 `ViewControllers` 屬性（陣列）新增至 `UIViewController` 。 `UITabBarController`本身會處理載入適當的控制器，並根據所選索引標籤來呈現其觀點。

索引標籤是類別的實例 `UITabBarItem` ，包含在 `UITabBar` 實例中。 每個 `UITabBar` 實例都可以透過 `TabBarItem` 每個索引標籤中的控制器屬性來存取。

為了瞭解如何使用，讓我們逐步解說如何 `UITabBarController` 建立簡單的應用程式來使用一個。

## <a name="tabbed-application-walkthrough"></a>索引標籤應用程式逐步解說

在這個逐步解說中，我們將建立下列應用程式：

[![範例索引標籤式應用程式](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

雖然 Visual Studio for Mac 中已經有索引標籤式應用程式範本，但在此範例中，這些指示可從空的專案運作，以進一步瞭解應用程式的結構。

### <a name="creating-the-application"></a>建立應用程式

從建立新的應用程式開始。

在 Visual Studio for Mac 中選取 [ **> 新的 > 解決方案** ] 功能表項目，然後選取 **IOS > 應用程式 > 空白專案** 範本， `TabbedApplication` 為專案命名，如下所示：

[![選取空的專案範本](creating-tabbed-applications-images/newsolution1.png)](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![將專案命名為 TabbedApplication](creating-tabbed-applications-images/newsolution2.png)](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>新增 UITabBarController

接下來，選取 [檔案] **> [新增** 檔案]，然後選擇 [ **一般：空白類別** ] 範本，以新增空的類別。 為檔案命名， `TabController` 如下所示：

[![新增 TabController 類別](creating-tabbed-applications-images/02-newclass.png)](creating-tabbed-applications-images/02-newclass.png#lightbox)

`TabController`類別將包含 `UITabBarController` 將管理陣列的的實 `UIViewControllers` 。 當使用者選取索引標籤時， `UITabBarController` 會負責呈現適當視圖控制器的視圖。

若要執行， `UITabBarController` 我們必須執行下列動作：

1. 將的基底類別設定  `TabController` 為  `UITabBarController` 。
1. 建立  `UIViewController` 要加入的實例  `TabController` 。
1. 將  `UIViewController` 實例加入至指派給屬性的陣列  `ViewControllers`  `TabController` 。

將下列程式碼加入至 `TabController` 類別，以完成下列步驟：

```csharp
using System;
using UIKit;

namespace TabbedApplication {
    public class TabController : UITabBarController {

        UIViewController tab1, tab2, tab3;

        public TabController ()
        {
            tab1 = new UIViewController();
            tab1.Title = "Green";
            tab1.View.BackgroundColor = UIColor.Green;

            tab2 = new UIViewController();
            tab2.Title = "Orange";
            tab2.View.BackgroundColor = UIColor.Orange;

            tab3 = new UIViewController();
            tab3.Title = "Red";
            tab3.View.BackgroundColor = UIColor.Red;

            var tabs = new UIViewController[] {
                tab1, tab2, tab3
            };

            ViewControllers = tabs;
        }
    }
}
```

請注意，針對每個 `UIViewController` 實例，我們會設定的 `Title` 屬性 `UIViewController` 。 當控制器加入至時 `UITabBarController` ， `UITabBarController` 會讀取 `Title` 每個控制器的，並將其顯示在相關索引標籤的標籤上，如下所示：

[![範例應用程式執行](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>將 TabController 設定為 RootViewController

控制器放在索引標籤中的順序，會對應至將它們新增至陣列的順序 `ViewControllers` 。

若要將 `UITabController` 載入為第一個畫面，我們必須將它設為視窗的 `RootViewController` ，如下列程式碼所示 `AppDelegate` ：

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TabController tabController;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);

        tabController = new TabController ();
        window.RootViewController = tabController;

        window.MakeKeyAndVisible ();

        return true;
    }
}
```

如果現在執行應用程式， `UITabBarController` 將會載入預設選取的第一個索引標籤。 選取任何其他索引標籤會導致顯示相關聯的控制器視圖，如下所 `UITabBarController,` 示，使用者已選取第二個索引標籤：

![顯示的第二個索引標籤](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>修改 TabBarItems

現在我們已有執行中的索引標籤應用程式，讓我們修改 `TabBarItem` 來變更顯示的影像和文字，以及將徽章新增至其中一個索引標籤。

#### <a name="setting-a-system-item"></a>設定系統專案

首先，讓我們設定第一個索引標籤，以使用系統專案。 在的函式中 `TabController` ，移除為實例設定控制器的行， `Title` 並以 `tab1` 下列程式碼取代它來設定控制器的 `TabBarItem` 屬性：

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

使用建立時 `UITabBarItem` `UITabBarSystemItem` ，iOS 會自動提供標題和影像，如下列螢幕擷取畫面所示，在第一個索引卷**Favorites**標上顯示 [我的最愛] 圖示和標題：

 ![具有星號圖示的第一個索引標籤](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>設定影像

除了使用系統專案之外，的標題和影像 `UITabBarItem` 也可以設定為自訂值。 例如，變更設定控制器屬性的程式碼，如下所示 `TabBarItem` `tab2` ：

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

上述程式碼假設已將名為的影像 `second.png` 新增至專案 (的根目錄或 **資源** 目錄) 。 若要支援所有的螢幕密度，您需要三個映射，如下所示：

![新增至專案的影像](creating-tabbed-applications-images/tabbedimages7new.png)

如需正確維度的指引，請參閱[Apple 自訂圖示頁面](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons/)的 [索引標籤列**圖示大小]** 區段。 建議的大小會根據影像的樣式而有所不同， (圓形、正方形、寬或高) 。

`Image`屬性只需要設定為**second.png**檔案名，而 iOS 會在需要時自動載入較高的解析度檔案。 您可以在使用 [影像](~/ios/app-fundamentals/images-icons/index.md) 指南中閱讀更多相關資訊。 依預設，索引標籤列專案為灰色，並在選取時使用藍色淡色。

#### <a name="overriding-the-title"></a>覆寫標題

當 `Title` 直接在上設定屬性時 `TabBarItem` ，它會覆寫控制器本身上設定的任何值 `Title` 。

這個螢幕擷取畫面中的第二個 (中間) 索引標籤會顯示自訂標題和影像：

![具有正方形圖示的第二個索引標籤](creating-tabbed-applications-images/05-customtab-sml.png)

#### <a name="setting-the-badge-value"></a>設定徽章值

索引標籤也可以顯示徽章。 例如，新增下列程式程式碼，在第三個索引標籤上設定徽章：

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

執行這項動作會在索引標籤的左上角加上字串 "Hi" 的紅色標籤，如下所示：

![使用 Hi 徽章的第二個索引標籤](creating-tabbed-applications-images/06-badge-sml.png)

徽章通常用來顯示未讀取的數位指示、新專案。 若要移除徽章，請將設定 `BadgeValue` 為 null，如下所示：

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>非 RootViewController 案例中的索引標籤

在上述範例中，我們示範了如何在 `UITabBarController` 其為視窗的時使用 `RootViewController` 。 在此範例中，我們將探討如何在 `UITabBarController` 不使用時使用，並顯示如何使用分鏡腳本來 `RootViewController` 建立。

### <a name="initial-screen-example"></a>初始畫面範例

在此案例中，會從不是的控制器載入初始畫面 `UITabBarController` 。 當使用者按下按鈕與螢幕互動時，相同的視圖控制器將會載入至 `UITabBarController` ，然後向使用者顯示。 下列螢幕擷取畫面顯示此應用程式流程：

[![此螢幕擷取畫面顯示應用程式流程](creating-tabbed-applications-images/inital-screen-application.png)](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

讓我們為此範例啟動新的應用程式。 同樣地，我們將使用 **iPhone > 應用程式 > 空白專案 (c # ) ** 範本，這次為專案命名 `InitialScreenDemo` 。

在此範例中，會使用分鏡腳本來配置視圖控制器。 若要加入分鏡腳本：

- 以滑鼠右鍵按一下專案名稱，然後選取 [ **加入 > 新**檔案]。

- 當 [新增檔案] 對話方塊出現時，流覽至 **iOS > 空白 iPhone**分鏡腳本。

讓我們呼叫這個新的腳本 **MainStoryboard** ，如下所示：

[![將 MainStoryboard 檔案新增至專案](creating-tabbed-applications-images/new-file-dialog.png)](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

將分鏡腳本新增至先前的非分鏡腳本檔案時，有幾個要注意的重要步驟，請見分鏡腳本指南的 [簡介](~/ios/user-interface/storyboards/index.md) 。 這些警告是：

1. 將您的分鏡腳本名稱新增至的 **主要介面** 區段 `Info.plist` ：

    [![將主要介面設定為 MainStoryboard](creating-tabbed-applications-images/project-options.png)](creating-tabbed-applications-images/project-options.png#lightbox)
1. 在中 `App Delegate` ，使用下列程式碼覆寫視窗方法：

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

在此範例中，我們需要三個 View 控制器。 一個名為的 `ViewController1` ，將會用來做為初始 View 控制器和第一個索引標籤。另二名為 `ViewController2` 和 `ViewController3` ，分別會在第二和第三個索引標籤中使用。

按兩下 [MainStoryboard]，開啟設計工具，然後將三個視圖控制器拖曳至設計介面。 我們希望每一個 View 控制器都有自己的類別對應到上述的名稱，因此，在 **Identity > 類別**下，輸入其名稱，如下列螢幕擷取畫面所示：

[![將類別設定為 ViewController1](creating-tabbed-applications-images/class-name.png)](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio for Mac 會自動產生所需的類別和設計工具檔案，您可以在 Solution Pad 中看到，如下所示：

[![專案中自動產生的檔案](creating-tabbed-applications-images/solution-pad2.png)](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>建立 UI

接下來，我們將使用 Xamarin iOS 設計工具，為每個 ViewController 的視圖建立簡單的使用者介面。

我們想要 `Label` `Button` 從右手邊的 **工具箱** 將 a 和 a 拖曳至 ViewController1。 接下來，我們將使用 Properties Pad 來編輯控制項的名稱和文字，如下所示：

- **標籤**： `Text`  =  **一個**
- **按鈕**： `Title`  =  **使用者採取一些初始動作**

我們將在事件中控制按鈕的可見度 `TouchUpInside` ，而我們需要在程式碼後接中參考它。 讓我們使用 Properties Pad 中的 **名稱**來識別它 `aButton` ，如下列螢幕擷取畫面所示：

[![將 Properties Pad 中的名稱設定為 aButton](creating-tabbed-applications-images/abutton-properties.png)](creating-tabbed-applications-images/abutton-properties.png#lightbox)

您的 Design Surface 現在看起來應該類似下列螢幕擷取畫面：

[![您的 Design Surface 現在看起來應該類似此螢幕擷取畫面](creating-tabbed-applications-images/design-surface1.png)](creating-tabbed-applications-images/design-surface1.png#lightbox)

讓我們將更多詳細資料新增至 `ViewController2` 和 `ViewController3` ，方法是將標籤新增至每個標籤，並分別將文字變更為 ' 2 ' 和 ' 3 '。 這會對使用者顯示我們查看的索引標籤/觀點。

#### <a name="wiring-up-the-button"></a>連接按鈕

`ViewController1`當應用程式第一次啟動時，我們就會載入。 當使用者按下按鈕時，就會隱藏按鈕，並 `UITabBarController` 在第一個索引標籤中載入含有 `ViewController1` 實例的。

當使用者發行時 `aButton` ，我們想要觸發 TouchUpInside 事件。 讓我們選取按鈕，然後在 [屬性] 面板的 [ **事件]** 索引標籤中，宣告事件處理常式– `InitialActionCompleted` -因此可以在程式碼中參考它。 下列螢幕擷取畫面說明這一點：

[![當使用者放開 aButton 時，觸發 TouchUpInside 事件](creating-tabbed-applications-images/event-handler.png)](creating-tabbed-applications-images/event-handler.png#lightbox)

我們現在需要告知 View 控制器在事件引發時隱藏按鈕 `InitialActionCompleted` 。 在中 `ViewController1` ，新增下列部分方法：

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

儲存檔案，然後執行應用程式。 我們應該會看到一個出現畫面，而按鈕會在觸控時消失。

#### <a name="adding-the-tab-bar-controller"></a>加入索引標籤列控制器

我們現在會如預期般運作。 接下來，我們想要將它加入至 `UITabBarController` ，以及 Views 2 和3。 讓我們在設計工具中開啟分鏡腳本。

在 [ **工具箱**] 中，于 [控制器 & 物件] 下搜尋索引標籤列 **控制器** ，並將其拖曳至 Design Surface。 如您在下列螢幕擷取畫面中所見，索引標籤列控制器沒有 UI，因此預設會顯示兩個 View 控制器：

[![將索引標籤列控制器加入至版面配置](creating-tabbed-applications-images/tabbarcontroller.png)](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

選取底部的黑色列，然後按下 [刪除]，以刪除這些新的視圖控制器。

在我們的腳本中，我們可以使用 Segue 來處理 TabBarController 和我們的視圖控制器之間的轉換。 在與初始視圖互動之後，我們想要將它載入至向使用者呈現的 TabBarController 中。 讓我們在設計工具中進行這項設定。

按**Ctrl 鍵**，然後從按鈕**拖曳**至 TabBarController。 在滑鼠上，會出現內容功能表。 我們想要使用強制回應 segue。

若要設定每個索引標籤，請按 **Ctrl 鍵** ，從 TabBarController 到每個 View 控制器的順序，從1到3，然後從內容功能表中選取 [關聯性 **]** 索引標籤，如下所示：

[![選取索引標籤關聯性](creating-tabbed-applications-images/context-menu.png)](creating-tabbed-applications-images/context-menu.png#lightbox)

您的腳本看起來應該類似下列螢幕擷取畫面：

[![腳本應類似此螢幕擷取畫面](creating-tabbed-applications-images/segue-layout.png)](creating-tabbed-applications-images/segue-layout.png#lightbox)

如果我們按一下其中一個索引標籤列專案，然後流覽 [屬性] 面板，您就可以看到許多不同的選項，如下所示：

[![在屬性瀏覽器中設定索引標籤選項](creating-tabbed-applications-images/properties-panel.png)](creating-tabbed-applications-images/properties-panel.png#lightbox)

我們可以使用此專案來編輯某些屬性，例如徽章、標題和 iOS 識別碼等等。

如果我們立即儲存和執行應用程式，我們會發現當 ViewController1 實例載入 TabBarController 時，按鈕會重新出現。 讓我們藉由查看目前的視圖是否有父視圖控制器來修正此問題。 如果有的話，我們知道我們是在 TabBarController 內，因此按鈕應該是隱藏的。 讓我們將下列程式碼新增至 ViewController1 類別：

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

當應用程式執行時，當使用者按下第一個畫面上的按鈕時，會載入 UITabBarController，並將第一個畫面的視圖放在第一個索引標籤中，如下所示：

[![範例應用程式輸出](creating-tabbed-applications-images/first-view-sml.png)](creating-tabbed-applications-images/first-view.png#lightbox)

## <a name="summary"></a>摘要

本文涵蓋如何 `UITabBarController` 在應用程式中使用。 我們逐步解說如何在每個索引標籤中載入控制器，以及如何在標題、影像和徽章等索引標籤上設定屬性。 接著，我們使用分鏡腳本來進行檢查， `UITabBarController` 當它不是視窗的時，如何在執行時間載入 `RootViewController` 。

## <a name="related-links"></a>相關連結

- [建立索引標籤式應用程式 (範例) ](/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [UITabBarController 類別參考](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
---
title: Xamarin 中的索引標籤列和索引標籤列控制器
description: 本檔說明 iOS 索引標籤列控制器，以及如何搭配使用它們與 Xamarin。 它會示範如何設定 UITabBarController、使用影像、設定徽章值、處理事件等等。
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: ad4682e9a3d4de2565bee54ffa159fd739572e24
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663300"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Xamarin 中的索引標籤列和索引標籤列控制器

在 iOS 中使用索引標籤式應用程式來支援使用者介面，而不需要特定順序即可存取多個畫面。 透過 `UITabBarController` 類別，應用程式可以輕鬆地包含對這類多重畫面案例的支援。 `UITabBarController` 會負責多畫面管理，讓應用程式開發人員能夠專注于每個畫面的詳細資料。

索引標籤式應用程式通常會以 `UITabBarController` 為主視窗的 `RootViewController` 來建立。 不過，有一些額外的程式碼，索引標籤式應用程式也可以連續用於一些其他的初始畫面，例如應用程式第一次呈現登入畫面的案例，接著是索引標籤式介面。

此頁面討論兩種案例：當索引標籤位於應用程式視圖階層的根目錄，同時也在非`RootViewController` 案例中。

## <a name="introducing-uitabbarcontroller"></a>UITabBarController 簡介

`UITabBarController` 支援下列各項的索引標籤式應用程式開發：

- 允許將多個控制器新增至其中。
- 透過 `UITabBar` 類別提供索引標籤式使用者介面，讓使用者可以在控制器與其視圖之間切換。 

控制器會透過其 `ViewControllers` 屬性（也就是 `UIViewController` 陣列）新增至 `UITabBarController`。 `UITabBarController` 本身會處理載入適當的控制器，並根據選取的索引標籤呈現其 view。

索引標籤是包含在 `UITabBar` 實例中 `UITabBarItem` 類別的實例。 每個 `UITabBar` 實例都可以透過每個索引標籤中控制器的 `TabBarItem` 屬性來存取。

若要瞭解如何使用 `UITabBarController`，讓我們逐步解說如何建立使用一個的簡單應用程式。

## <a name="tabbed-application-walkthrough"></a>索引標籤應用程式逐步解說

在此逐步解說中，我們將建立下列應用程式：

[![範例索引標籤式應用程式](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

雖然 Visual Studio for Mac 中已有索引標籤式應用程式範本，但在此範例中，這些指示會從空白專案中運作，以進一步瞭解應用程式的結構。

### <a name="creating-the-application"></a>建立應用程式

一開始先建立新的應用程式。

在 Visual Studio for Mac 中選取 **> 新 > 方案** 功能表項目的檔案，然後選取**IOS > 應用程式 > 空白專案**範本，將專案命名為 `TabbedApplication`，如下所示：

[![](creating-tabbed-applications-images/newsolution1.png "Select the Empty Project template")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Name the project TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>新增 UITabBarController

接下來，選取 [檔案] **> [新增**檔案]，然後選擇 [**一般：空白類別**] 範本，以新增空的類別。 將檔案命名為 `TabController` 如下所示：

[![](creating-tabbed-applications-images/02-newclass.png "Add the TabController class")](creating-tabbed-applications-images/02-newclass.png#lightbox)

`TabController` 類別將包含將管理 `UIViewControllers`陣列之 `UITabBarController` 的執行。 當使用者選取索引標籤時，`UITabBarController` 將會負責呈現適當視圖控制器的視圖。

若要執行 `UITabBarController` 我們需要執行下列動作：

1. 將 `TabController` 的基底類別設定為 `UITabBarController`。 
1. 建立要新增至 `TabController` 的 `UIViewController` 實例。 
1. 將 `UIViewController` 實例新增至指派給 `TabController` 之 `ViewControllers` 屬性的陣列。 

將下列程式碼新增至 `TabController` 類別，以達成這些步驟：

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

請注意，針對每個 `UIViewController` 實例，我們都會設定 `UIViewController`的 `Title` 屬性。 當控制器新增至 `UITabBarController`時，`UITabBarController` 會讀取每個控制器的 `Title`，並將其顯示在相關聯的索引標籤上，如下所示：

[![](creating-tabbed-applications-images/00-app.png "The sample app run")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>將 TabController 設定為 RootViewController

控制器放在索引標籤中的順序，會對應到新增至 `ViewControllers` 陣列的順序。

若要讓 `UITabController` 載入為第一個畫面，我們必須將它設為視窗的 `RootViewController`，如下列 `AppDelegate`的程式碼所示：

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

如果現在執行應用程式，`UITabBarController` 將會以預設選取的第一個索引標籤來載入。 選取任何其他索引標籤會導致 `UITabBarController,` 顯示相關聯的控制器視圖，如下所示，使用者已選取第二個索引標籤：

![顯示的第二個索引標籤](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>修改 TabBarItems

現在，我們已有一個執行中的索引標籤應用程式，讓我們修改 `TabBarItem` 以變更顯示的影像和文字，以及將徽章新增至其中一個索引標籤。

#### <a name="setting-a-system-item"></a>設定系統專案

首先，讓我們將第一個索引標籤設定為使用系統專案。 在 `TabController`的函式中，移除設定 `tab1` 實例之控制器 `Title` 的程式列，並以下列程式碼取代它，以設定控制器的 `TabBarItem` 屬性：

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

使用 `UITabBarSystemItem`建立 `UITabBarItem` 時，iOS 會自動提供標題和影像，如下列螢幕擷取畫面所示，顯示第一個索引標籤上**的 [我的最**愛] 圖示和標題：

 ![具有星號圖示的第一個索引標籤](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>設定影像

除了使用系統專案之外，`UITabBarItem` 的標題和影像也可以設定為自訂值。 例如，變更程式碼，以設定名為 `tab2` 之控制器的 `TabBarItem` 屬性，如下所示：

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

上述程式碼假設已將名為 `second.png` 的映射新增至專案的根目錄（或**Resources**目錄）。 若要支援所有的螢幕密度，您需要三個影像，如下所示：

![加入至專案的影像](creating-tabbed-applications-images/tabbedimages7new.png)

如需正確維度的指引，請參閱[Apple 自訂圖示頁面](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons/)的索引標籤**欄圖示大小**區段。 建議的大小會根據影像的樣式（圓形、正方形、寬或高）而有所不同。

`Image` 屬性只需要設定為**第二個 .png**檔案名，iOS 會在需要時自動載入較高的解析度檔案。 您可以在[使用影像](~/ios/app-fundamentals/images-icons/index.md)指南中閱讀更多相關資訊。 依預設，索引標籤列專案為灰色，選取時會加上藍色色調。

#### <a name="overriding-the-title"></a>覆寫標題

直接在 `TabBarItem`上設定 `Title` 屬性時，它會覆寫在控制器本身上為 `Title` 設定的任何值。

這個螢幕擷取畫面中的第二個（中間）索引標籤會顯示自訂標題和影像：

![具有方形圖示的第二個索引標籤](creating-tabbed-applications-images/05-customtab-sml.png)

#### <a name="setting-the-badge-value"></a>設定徽章值

[索引標籤] 也可以顯示徽章。 例如，新增下列程式程式碼，以在第三個索引標籤上設定徽章：

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

執行此動作會在索引標籤的左上角顯示含有 "Hi" 字串的紅色標籤，如下所示：

![第二個索引標籤，加上最高徽章](creating-tabbed-applications-images/06-badge-sml.png)

徽章通常用來顯示數位指示 [未讀取]、[新專案]。 若要移除徽章，請將 `BadgeValue` 設定為 null，如下所示：

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>非 RootViewController 案例中的索引標籤

在上述範例中，我們示範了當 `UITabBarController` 是視窗 `RootViewController` 時如何使用它。 在此範例中，我們將探討如何使用不是 `RootViewController` 的 `UITabBarController`，並顯示如何使用分鏡腳本建立此功能。

### <a name="initial-screen-example"></a>初始畫面範例

在此案例中，初始畫面會從不是 `UITabBarController`的控制器載入。 當使用者藉由按下按鈕來與畫面互動時，會將相同的視圖控制器載入 `UITabBarController`，然後向使用者顯示。 下列螢幕擷取畫面顯示此應用程式流程：

[![](creating-tabbed-applications-images/inital-screen-application.png "This screenshot shows the application flow")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

讓我們針對此範例啟動新的應用程式。 同樣地，我們將使用**iPhone > 應用程式 > 空的C#專案（）** 範本，這次將專案命名為 `InitialScreenDemo`。

在此範例中，會使用分鏡腳本來配置視圖控制器。 若要新增分鏡腳本：

- 以滑鼠右鍵按一下專案名稱，然後選取 [**加入 > 新增**檔案]。

- 當 [新增檔案] 對話方塊出現時，流覽至**iOS > 空白的 iPhone**分鏡腳本。

讓我們來呼叫這個新的分鏡腳本**mainstoryboard.storyboard** ，如下所示： 

[![](creating-tabbed-applications-images/new-file-dialog.png "Add a MainStoryboard file to the project")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

將分鏡腳本新增至先前的非分鏡腳本檔案時，有幾個要注意的重要步驟，如腳本[簡介](~/ios/user-interface/storyboards/index.md)指南所述。 這些是：

1. 將您的分鏡腳本名稱新增至 `Info.plist`的**主要介面**區段中：

    [![](creating-tabbed-applications-images/project-options.png "Set the Main Interface to MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. 在您的 `App Delegate`中，使用下列程式碼覆寫 Window 方法：

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

在此範例中，我們將需要三個 View 控制器。 其中一個名稱為 `ViewController1`，將用來做為初始視圖控制器和第一個索引標籤。另兩個名為 `ViewController2` 和 `ViewController3`，分別用於第二個和第三個索引標籤。

按兩下 [Mainstoryboard.storyboard] 檔案，然後將三個 View 控制器拖曳至設計介面，以開啟設計工具。 我們希望每個 View controller 都有自己的類別，並對應到上述的名稱，因此，請在 [身分**識別 > 類別**] 下輸入其名稱，如下列螢幕擷取畫面所示：

[![](creating-tabbed-applications-images/class-name.png "Set the Class to ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio for Mac 會自動產生所需的類別和設計工具檔案，這可以在 Solution Pad 中看到，如下所示：

[![](creating-tabbed-applications-images/solution-pad2.png "Auto-generated files in the project")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>建立 UI

接下來，我們將使用 Xamarin iOS 設計工具，為每個 ViewController 的觀點建立簡單的使用者介面。

我們想要從右手邊的 [**工具箱**] 拖曳 [`Label`] 和 [`Button` 至 ViewController1]。 接下來，我們將使用 Properties Pad 來編輯控制項的名稱和文字，如下所示：

- **標籤**： `Text` = **一個**
- **按鈕**： `Title` = **使用者採取一些初始動作**

我們將在 `TouchUpInside` 事件中控制按鈕的可見度，而我們需要在程式碼後置中參考它。 讓我們使用 Properties Pad 中的**名稱**`aButton` 加以識別，如下列螢幕擷取畫面所示：

[![](creating-tabbed-applications-images/abutton-properties.png "Set the Name to aButton in the Properties Pad")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

您的 Design Surface 現在看起來應該類似以下的螢幕擷取畫面：

[![](creating-tabbed-applications-images/design-surface1.png "Your Design Surface should now look similar to this screenshot")](creating-tabbed-applications-images/design-surface1.png#lightbox)

讓我們新增更多詳細資料來 `ViewController2` 和 `ViewController3`，方法是將標籤新增至每個，並分別將文字變更為 ' 2 ' 和 ' 3 '。 這會向使用者顯示我們要查看的索引標籤/視圖。

#### <a name="wiring-up-the-button"></a>向上連接按鈕

我們即將在應用程式第一次啟動時載入 `ViewController1`。 當使用者按下按鈕時，我們會隱藏按鈕，並在第一個索引標籤中載入具有 `ViewController1` 實例的 `UITabBarController`。

當使用者釋放 `aButton`時，我們想要觸發 TouchUpInside 事件。 讓我們選取 [] 按鈕，然後在 [Properties] pad 的 [**事件]** 索引標籤中，宣告事件處理常式– `InitialActionCompleted` –因此可以在程式碼中參考它。 下列螢幕擷取畫面將說明這一點：

[![](creating-tabbed-applications-images/event-handler.png "When the user releases the aButton, trigger a TouchUpInside event")](creating-tabbed-applications-images/event-handler.png#lightbox)

我們現在需要告訴 View Controller 在事件引發 `InitialActionCompleted`時隱藏按鈕。 在 `ViewController1`中，新增下列部分方法：

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

儲存檔案，然後執行應用程式。 我們應該會看到畫面一出現，而且按鈕會在碰觸時消失。

#### <a name="adding-the-tab-bar-controller"></a>新增索引標籤列控制器

我們的初始視圖會如預期般運作。 接下來，我們想要將它新增至 `UITabBarController`，以及 Views 2 和3。 讓我們在設計工具中開啟腳本。

在 [**工具箱**] 中，搜尋 [控制器] & [物件] 底下的索引標籤列控制器，並將其拖曳至 [Design Surface **]** 。 如您在下面的螢幕擷取畫面中所見，索引標籤列控制器不會有 UI，因此預設會帶入兩個視圖控制器：

[![](creating-tabbed-applications-images/tabbarcontroller.png "Adding a Tab Bar Controller to the layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

選取底部的黑色列，然後按下 [刪除]，即可刪除這些新的視圖控制器。

在我們的腳本中，我們可以使用 Segue 來處理 TabBarController 與我們的視圖控制器之間的轉換。 與初始視圖互動之後，我們想要將它載入至呈現給使用者的 TabBarController。 讓我們在設計工具中進行這項設定。

**按 Ctrl-按一下**並從 [] 按鈕**拖曳**至 [TabBarController]。 當滑鼠開啟時，將會出現內容功能表。 我們想要使用強制回應 segue。 

若要設定每個索引標籤，請從 TabBarController 中依序**按一下**每個 View controller （從一到三），然後從內容功能表中選取 [關聯性 **]** 索引標籤，如下所示：

[![](creating-tabbed-applications-images/context-menu.png "Select the Tab Relationship")](creating-tabbed-applications-images/context-menu.png#lightbox)

您的分鏡腳本應該類似下列螢幕擷取畫面：

[![](creating-tabbed-applications-images/segue-layout.png "The Storyboard should resemble this screenshot")](creating-tabbed-applications-images/segue-layout.png#lightbox)

如果我們按一下其中一個索引標籤列專案，並流覽 [屬性] 面板，您可以看到許多不同的選項，如下所示：

[![](creating-tabbed-applications-images/properties-panel.png "Setting the tab options in the Properties Explorer")](creating-tabbed-applications-images/properties-panel.png#lightbox)

我們可以使用這個來編輯特定的屬性，例如徽章、標題和 iOS[識別碼](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html)，還有其他內容

如果我們立即儲存並執行應用程式，我們會發現當 ViewController1 實例載入 TabBarController 時，按鈕會重新出現。 讓我們來修正此問題，方法是檢查目前的視圖是否有父視圖控制器。 如果有的話，我們知道我們在 TabBarController 內，因此應該隱藏按鈕。 讓我們將下列程式碼新增至 ViewController1 類別：

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

當應用程式執行，而且使用者在第一個畫面上按下按鈕時，就會載入 UITabBarController，而第一個畫面的視圖會放在第一個索引標籤中，如下所示：

[![範例應用程式輸出](creating-tabbed-applications-images/first-view-sml.png)](creating-tabbed-applications-images/first-view.png#lightbox)

## <a name="summary"></a>總結

本文涵蓋如何在應用程式中使用 `UITabBarController`。 我們逐步解說如何將控制器載入每個索引標籤，以及如何設定索引標籤（例如標題、影像和徽章）的屬性。 然後我們會使用分鏡腳本來進行檢查，當它不是視窗的 `RootViewController` 時，如何在執行時間載入 `UITabBarController`。

## <a name="related-links"></a>相關連結

- [建立索引標籤式應用程式（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/creatingtabbedapplications)
- [影像 .zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [UITabBarController 類別參考](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)

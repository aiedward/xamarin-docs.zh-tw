---
title: 索引標籤列和在 Xamarin.iOS 中的索引標籤列控制器
description: 本文件說明 iOS 索引標籤列控制器，以及如何使用它們搭配 Xamarin.iOS。 它會示範如何設定 UITabBarController、 使用映像、 設定徽章值、 事件、 使用和更多功能。
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: e4ab46994ed25daaef95a709e4f9df94f3a21cd0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114674"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>索引標籤列和在 Xamarin.iOS 中的索引標籤列控制器

索引標籤式應用程式可在 iOS 中支援使用者介面可以存取多個畫面的位置中沒有特定順序。 透過`UITabBarController`類別，應用程式可以輕鬆地包含這類多重畫面案例的支援。 `UITabBarController` 會處理多重畫面的管理，讓應用程式開發人員將焦點放在每個畫面的詳細資料。

通常，索引標籤式應用程式以建置`UITabBarController`正在`RootViewController`主視窗。 不過，額外的程式碼稍微，索引標籤式應用程式也可用連續至某些其他初始的畫面，例如應用程式第一次顯示登入畫面，後面接著索引標籤式介面的案例。

我們將檢驗透過簡單的應用程式的逐步解說中使用此索引標籤。 然後，我們將探討如何使用索引標籤中的非`RootViewController`案例。

## <a name="introducing-uitabbarcontroller"></a>簡介 UITabBarController

`UITabBarController`支援索引標籤式應用程式開發，由下列：

-  允許多個控制站新增至它。
-  提供索引標籤式的使用者介面，透過`UITabBar`類別，以允許使用者控制站和及其檢視之間切換。 


控制站新增至`UITabBarController`透過其`ViewControllers`屬性，這是`UIViewController`陣列。 `UITabBarController`本身會處理載入適當的控制站，並呈現其根據選取的索引標籤的檢視。

索引標籤是的執行個體`UITabBarItem`類別，其包含在`UITabBar`執行個體。 每個`UITabBar`執行個體是可透過存取`TabBarItem`屬性的每個索引標籤中的控制站。

若要了解如何使用`UITabBarController`，讓我們逐步解說建置簡單的應用程式使用其中一個。

## <a name="tabbed-application-walkthrough"></a>標籤列應用程式逐步解說

此逐步解說中，我們要建立下列應用程式：

[![](creating-tabbed-applications-images/00-app.png "範例索引標籤式應用程式")](creating-tabbed-applications-images/00-app.png#lightbox)

雖然已經有索引標籤式應用程式範本適用於 Visual Studio for Mac，在此範例中，我們從空的專案，以取得進一步了解如何建構應用程式的工作。

 <a name="Creating_the_Application" />


### <a name="creating-the-application"></a>建立應用程式

現在就開始建立新的應用程式。

選取 **檔案 > 新增 > 解決方案**功能表項目，在 Visual Studio for Mac，然後選取**iOS > 應用程式 > 空白專案**範本，將專案命名為`TabbedApplication`，如下所示：

[![](creating-tabbed-applications-images/newsolution1.png "選取 空專案範本")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "將專案命名為 TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)



### <a name="adding-the-uitabbarcontroller"></a>新增 UITabBarController

接下來，新增 藉由選取 空類別**檔案 > 新的檔案**，然後選擇**一般： 空的類別**範本。 將檔案命名為`TabController`，如下所示：

[![](creating-tabbed-applications-images/02-newclass.png "加入 TabController 類別")](creating-tabbed-applications-images/02-newclass.png#lightbox)

`TabController`類別將包含實作`UITabBarController`，將管理的陣列`UIViewControllers`。 當使用者選取索引標籤上，`UITabBarController`會負責呈現適當的檢視控制器的檢視。

若要實作`UITabBarController`我們需要執行下列動作：

1.  設定的基底類別`TabController`至`UITabBarController`。 
1.  建立`UIViewController`執行個體加入至`TabController`。 
1.  新增`UIViewController`指派給陣列的執行個體`ViewControllers`屬性`TabController`。 


將下列程式碼加入`TabController`類別，以達到下列步驟：

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

請注意，每個`UIViewController`執行個體，我們設定`Title`屬性`UIViewController`。 當控制器加入至`UITabBarController`，則`UITabBarController`將讀取`Title`每個控制站並顯示在 [關聯] 索引標籤的標籤，如下所示：

[![](creating-tabbed-applications-images/00-app.png "執行範例應用程式")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>設定 RootViewController TabController

控制器會放在索引標籤的順序對應至它們加入的順序`ViewControllers`陣列。

若要取得`UITabController`若要載入的第一個畫面，我們需要使其視窗`RootViewController`，如下列程式碼所示`AppDelegate`:

```csharp
[Register ("AppDelegate")]
        public partial class AppDelegate : UIApplicationDelegate
        {
                UIWindow window;
                TabController tabController;

                public override bool FinishedLaunching (UIApplication app, NSDictionary options)
                {
                        window = new UIWindow (UIScreen.MainScreen.Bounds);

                        var tabController = new TabController ();
                        window.RootViewController = tabController;

                        window.MakeKeyAndVisible ();
            
                        return true;
                }
        }
```

如果我們執行應用程式現在`UITabBarController`便會載入預設選取第一個索引標籤。 選取 [其他] 索引標籤的任何相關聯的控制器中的結果檢視所顯示`UITabBarController,`終端使用者已在其中選取第二個索引標籤，如下所示：

[![](creating-tabbed-applications-images/03-secondtab.png "顯示第二個索引標籤")](creating-tabbed-applications-images/03-secondtab.png#lightbox)

 <a name="Modifying_TabBarItems" />


### <a name="modifying-tabbaritems"></a>修改 TabBarItems

現在，我們會執行的應用程式索引標籤上，讓我們修改`TabBarItem`變更影像和文字顯示，以及其中一個索引標籤新增徽章。

 <a name="Setting_a_System_Item" />


#### <a name="setting-a-system-item"></a>設定系統 項目

首先，讓我們設定第一個索引標籤，使用 [系統] 項目。 中的建構函式`TabController`，移除設定控制器的一行`Title`for`tab1`執行個體，並取代為下列程式碼，將控制器的`TabBarItem`屬性：

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

建立時`UITabBarItem`使用`UITabBarSystemItem`、 標題 和 映像會自動提供 iOS 所顯示以下的螢幕擷取畫面所示**我的最愛**圖示，然後在第一個索引標籤上的標題：

 ![](creating-tabbed-applications-images/04a-tabimage.png "具有星號圖示的第一個索引標籤")

 <a name="Setting_the_Title_and_Image" />


#### <a name="setting-the-title-and-image"></a>設定 標題 和 映像

除了使用 [系統] 項目、 標題和映像的`UITabBarItem`可以設定為自訂的值。 例如，變更設定的程式碼`TabBarItem`屬性的控制器命名為`tab2`，如下所示：

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

上述程式碼會假設名為映像`second.png`已新增至專案的根目錄，在 Visual Studio for mac。 我們實際上已新增至我們的專案，以涵蓋所有的裝置解析度，如下所示的三個映像：

 [![](creating-tabbed-applications-images/tabbedimages7new.png "映像加入至專案")](creating-tabbed-applications-images/tabbedimages7new.png#lightbox)

索引標籤影像應該是具有一般的解析度，高解析度的 60 和 90 x 90 適用於 iPhone 6 x 60 的透明度 30x30 png 再加上解析。 在我們的程式碼中，我們只需要載入名為的檔案`second.png`，iOS 會自動載入高解析度的 Retina 顯示器的裝置上。 您可以閱讀更多中了解[處理映像](~/ios/app-fundamentals/images-icons/index.md)輔助線。 預設索引標籤列項目會以灰色，搭配選取藍色濃淡。

**注意**

上述映像也可以新增至**資源**目錄，也就是特殊的目錄，其內容將自動複製到應用程式套件組合的根目錄：

[![](creating-tabbed-applications-images/tabbedapplication8.png "為資源的映像")](creating-tabbed-applications-images/tabbedapplication8.png#lightbox)

此外，當我們設定`Title`屬性直接依據`TabBarItem`，則會覆寫為任何值`Title`本身的控制站上。

當我們執行應用程式現在時，第二個索引標籤會顯示我們的自訂標題和映像，如下所示：

[![](creating-tabbed-applications-images/05-customtab.png "第二個索引標籤的方形圖示")](creating-tabbed-applications-images/05-customtab.png#lightbox)

 <a name="Setting_the_Badge_Value" />


#### <a name="setting-the-badge-value"></a>將徽章值

索引標籤也會顯示徽章。 例如，加入下列程式碼來設定第三個索引標籤上的徽章：

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

執行這會導致索引標籤，如下所示的左上角的"Hi"字串的紅色標籤：

[![](creating-tabbed-applications-images/06-badge.png "具有 Hi 徽章的第二個索引標籤")](creating-tabbed-applications-images/06-badge.png#lightbox)

徽章通常用來顯示數字表示未讀郵件，新的項目。 若要移除的徽章，設定`BadgeValue`為 null，如下所示：

```csharp
tab3.TabBarItem.BadgeValue = null;
```

 <a name="Tabs_in_Non-RootViewController_Scenarios" />


## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>在非 RootViewController 案例中的索引標籤

在上述範例中，我們示範了如何運用`UITabBarController`時`RootViewController`的視窗。 在此範例中，我們會檢驗如何使用`UITabBarController`不是`RootViewController`並顯示此項目建立使用分鏡腳本。

 <a name="Initial_Screen_Example" />


### <a name="initial-screen-example"></a>初始畫面範例

此案例中，初始畫面則是從控制器不是載入`UITabBarController`。 當使用者互動與螢幕點選按鈕時，會載入回相同的檢視控制器`UITabBarController`，然後提供給使用者。 下列螢幕擷取畫面顯示應用程式流程：

[![](creating-tabbed-applications-images/inital-screen-application.png "此螢幕擷取畫面顯示應用程式流程")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

讓我們開始新的應用程式，此範例中。 同樣地，我們將使用**iPhone > 應用程式 > 空白專案 (C#)** 範本，此時命名專案`InitialScreenDemo`。


在此範例中，我們必須保留我們的檢視控制器分鏡腳本。 若要新增分鏡腳本：

- 以滑鼠右鍵按一下專案名稱，然後選取**新增 > 新的檔案**。

- 新的檔案對話方塊出現時，瀏覽至**iOS > 空白 iPhone 分鏡腳本**。

讓我們來呼叫此新分鏡腳本**MainStoryboard** ，如下所示： 

[![](creating-tabbed-applications-images/new-file-dialog.png "MainStoryboard 檔案加入專案")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

有幾個重要的步驟，請注意，當加入分鏡腳本先前非腳本檔案，其中涵蓋[分鏡腳本簡介](~/ios/user-interface/storyboards/index.md)指南。 這些是：

 
1. 新增名稱至分鏡腳本**主要介面**一節`Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "主要介面設 MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. 在您`App Delegate`，覆寫視窗方法中的，為下列程式碼：

    ```csharp
    public override UIWindow Window {
      get;
      set;
    }
    ```

我們將此範例需要三個檢視控制器。 一個名為`ViewController1`，將使用我們的初始檢視控制器，並在第一個索引標籤中。其他兩個名為`ViewController2`和`ViewController3`，用來與第二個和第三個索引標籤中分別。

按兩下 MainStoryboard.storyboard 檔案中，開啟設計工具，並拖曳到設計介面的三個檢視控制器。 我們想要每個擁有自己的類別名稱，對應這些檢視控制器，請在**身分識別 > 類別**，輸入名稱，如以下螢幕擷取畫面所示：

[![](creating-tabbed-applications-images/class-name.png "設 ViewController1 類別")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio for Mac 會自動產生的類別和設計工具所需的檔案，您可以在 Solution Pad 中，如下所示：

[![](creating-tabbed-applications-images/solution-pad2.png "在專案中自動產生的檔案")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

 <a name="Creating_the_UI" />


#### <a name="creating-the-ui"></a>建立 UI

接下來，我們將建立簡單的使用者介面的 ViewController 檢視每個使用 Xamarin iOS 設計工具。

我們想要拖曳`Label`並`Button`到從 ViewController1**工具箱**在右手邊。 接下來我們將使用 Properties Pad 編輯名稱與文字的控制項如下：

-  **標籤**: `Text`  = **其中一個**
-  **按鈕**: `Title`  = **使用者採取一些初始動作**


我們會控制在按鈕的可見性`TouchUpInside`事件，我們需要在程式碼後置中參考它。 讓我們找出與其**名稱**`aButton`中 [屬性] 面板，如下列螢幕擷取畫面所示：

[![](creating-tabbed-applications-images/abutton-properties.png "將名稱設定為在 Properties Pad aButton")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

您的設計介面現在看起來應該類似下面的螢幕擷取畫面：

[![](creating-tabbed-applications-images/design-surface1.png "您的設計介面現在看起來應該類似此螢幕擷取畫面")](creating-tabbed-applications-images/design-surface1.png#lightbox)

讓我們新增至更詳細`ViewController2`和`ViewController3`、 將標籤新增至每一個，並分別將文字變更為 '2' 和 '第 3'。 這會反白顯示給使用者，我們看看哪一個索引標籤/檢視表。

#### <a name="wiring-up-the-button"></a>連接按鈕

我們要載入`ViewController1`第一次啟動應用程式。 當使用者點選按鈕時，我們會隱藏按鈕，並載入`UITabBarController`與`ViewController1`第一個索引標籤中的執行個體。

當使用者放開`aButton`，我們想要觸發 TouchUpInside 事件。 讓我們選取 [[] 按鈕，然後在**事件] 索引標籤**的 [屬性] 面板中，宣告的事件處理常式 – `InitialActionCompleted` – 讓它可以參考程式碼中。 以下螢幕擷取畫面所示：

[![](creating-tabbed-applications-images/event-handler.png "當使用者放開 aButton 時，觸發程序 TouchUpInside 事件")](creating-tabbed-applications-images/event-handler.png#lightbox)

我們現在需要告知事件引發時，隱藏按鈕的檢視控制器`InitialActionCompleted`。 在  `ViewController1`，新增下列部分方法：

```csharp
partial void InitialActionCompleted (UIButton sender)
    {
      aButton.Hidden = true;  
    }
```

儲存檔案，並執行應用程式。 我們應該會看到一個出現的畫面和消失在 [修改] 按鈕。

#### <a name="adding-the-tab-bar-controller"></a>加入索引標籤列控制器

我們現在有我們如預期般運作的初始檢視。 接下來，我們想將它加入至 `UITabBarController` 以及檢視 2 和 3。 讓我們開啟分鏡腳本設計工具中。

在**工具箱**，搜尋**索引標籤列控制器**控制器物件之下和這拖曳至設計介面。 您可以看到以下螢幕擷取畫面中，索引標籤列控制器是無 UI，並因此將兩個檢視控制器與其預設：

[![](creating-tabbed-applications-images/tabbarcontroller.png "將索引標籤列控制器加入至版面配置")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

選取底部的黑色列，然後按下 delete 鍵刪除這些新的檢視控制器。

在我們的分鏡腳本中，我們可以使用 Segue 處理 TabBarController 與我們的檢視控制器之間的轉換。 之後的初始檢視與互動時，我們想要將其載入至 TabBarController 呈現給使用者。 讓我們設定此功能在設計工具中。

**按一下 Ctrl 以** 和 **拖曳** 從 [TabBarController] 按鈕。 在滑鼠向上上會出現內容功能表。 我們想要使用強制回應的 segue。 
 
若要設定每個索引標籤中， **Ctrl + 按一下**從我們的檢視控制器在順序中的每個從一到三個，然後選取關聯性 TabBarController ** 索引標籤**從內容功能表中，如下所示：

[![](creating-tabbed-applications-images/context-menu.png "選取索引標籤關聯性")](creating-tabbed-applications-images/context-menu.png#lightbox)

分鏡腳本應該類似以下螢幕擷取畫面：

[![](creating-tabbed-applications-images/segue-layout.png "分鏡腳本應該類似這個螢幕擷取畫面")](creating-tabbed-applications-images/segue-layout.png#lightbox)

如果我們按一下其中一個索引標籤列項目，並瀏覽 [屬性] 面板，您可以看到許多不同的選項，如下所示：

[![](creating-tabbed-applications-images/properties-panel.png "在 [屬性總管] 中設定的索引標籤選項")](creating-tabbed-applications-images/properties-panel.png#lightbox)

我們可以使用此選項來編輯某些屬性，例如徽章、 標題和 iOS[識別碼](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html)，其他項目

如果我們儲存，並執行應用程式現在，我們會發現按鈕在 ViewController1 執行個體載入至 TabBarController 時隨即再度出現。 讓我們修正此問題是查看目前的檢視是否具有父檢視控制器。 若是如此，我們知道我們正在 TabBarController，並因此應隱藏按鈕。 讓我們將下列程式碼加入 ViewController1 類別：

```csharp
public override void ViewDidLoad ()
{
     if (ParentViewController != null){
       aButton.Hidden = true;
     }

}
```

當在應用程式執行和的使用者點選的按鈕，在第一個畫面上，UITabBarController 載入時，使用從放在第一個索引標籤，如下所示的第一個畫面的檢視：

[![](creating-tabbed-applications-images/first-view.png "範例應用程式輸出")](creating-tabbed-applications-images/first-view.png#lightbox)

<!--Save the files and run the application:

[![](creating-tabbed-applications-images/inital-screen-application.png "Save the files and run the application")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)-->

## <a name="summary"></a>總結

本文涵蓋如何使用`UITabBarController`應用程式中。 我們逐步解說如何載入每個索引標籤中的控制站，以及如何設定屬性索引標籤上，這類的標題、 影像和標誌。 我們再檢查，使用分鏡腳本，如何載入`UITabBarController`在執行階段時`RootViewController`的視窗。


## <a name="related-links"></a>相關連結

- [建立索引標籤式應用程式 （範例）](https://developer.xamarin.com/samples/monotouch/CreatingTabbedApplications/)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [UITabBarController 類別參考](http://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)

---
title: 索引標籤列和索引標籤列 Xamarin.iOS 中的控制站
description: 本文件說明 iOS 索引標籤列的控制站，以及如何使用 Xamarin.iOS。 它會示範如何設定 UITabBarController、 使用映像時，設定徽章值、 工作與事件，等等。
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: d8b096774e60ec0e0b69e109fa5da53c25e66d25
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789754"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>索引標籤列和索引標籤列 Xamarin.iOS 中的控制站

在 iOS 中使用索引標籤式的應用程式以支援使用者介面，其中可以存取多個螢幕不依特定順序。 透過`UITabBarController`類別，應用程式可以輕鬆地包含這類多重畫面案例的支援。 `UITabBarController` 會負責多螢幕管理，允許應用程式開發人員專注於每個畫面的詳細資料。

通常，索引標籤式的應用程式會建立含有`UITabBarController`正在`RootViewController`主視窗。 不過，進行一些額外的程式碼，以索引標籤式的應用程式也可用連續某些其他初始畫面，例如，應用程式第一次呈現登入畫面，後面接著索引標籤式介面的案例。

我們將檢驗使用這裡 索引標籤，透過簡單的應用程式的逐步解說的工作。 然後，我們將探討如何使用索引標籤中的非`RootViewController`案例。

## <a name="introducing-uitabbarcontroller"></a>介紹 UITabBarController

`UITabBarController`支援索引標籤式由下列的應用程式開發：

-  允許多個要加入至它的控制站。
-  提供索引標籤式的使用者介面，透過`UITabBar`類別，以允許使用者控制站和及其檢視之間切換。 


控制站新增至`UITabBarController`透過其`ViewControllers`屬性，這是`UIViewController`陣列。 `UITabBarController`本身會處理載入適當的控制站和呈現其根據選取的索引標籤的檢視。

索引標籤是的執行個體`UITabBarItem`類別，包含在`UITabBar`執行個體。 每個`UITabBar`執行個體是透過可存取`TabBarItem`屬性的每個索引標籤中的控制站。

若要了解，了解如何使用`UITabBarController`，讓我們逐步解說建立簡單的應用程式使用其中一個。

## <a name="tabbed-application-walkthrough"></a>索引標籤式的應用程式逐步解說

此逐步解說中，我們要建立下列應用程式：

[![](creating-tabbed-applications-images/00-app.png "範例索引標籤式的應用程式")](creating-tabbed-applications-images/00-app.png#lightbox)

雖然有索引標籤式的應用程式範本中已經提供 Visual Studio for Mac，此範例中，我們將從空白的專案，以取得進一步了解如何建構應用程式的工作。

 <a name="Creating_the_Application" />


### <a name="creating-the-application"></a>建立應用程式

讓我們開始建立新的應用程式。

選取**檔案 > 新增 > 方案**用於 Mac 和選取的 Visual Studio 中的功能表項目**iOS > 應用程式 > 空專案**範本，將專案`TabbedApplication`，如下所示：

[![](creating-tabbed-applications-images/newsolution1.png "選取空白專案範本")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "將專案 TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)



### <a name="adding-the-uitabbarcontroller"></a>加入 UITabBarController

接下來，加入空的類別，方式是選取**檔案 > 新的檔案**，然後選擇**一般： 空的類別**範本。 將檔案命名`TabController`如下所示：

[![](creating-tabbed-applications-images/02-newclass.png "加入 TabController 類別")](creating-tabbed-applications-images/02-newclass.png#lightbox)

`TabController`類別所包含的實作`UITabBarController`可用於將管理的陣列`UIViewControllers`。 當使用者選取索引標籤上，`UITabBarController`會負責的呈現適當的檢視控制站的檢視。

若要實作`UITabBarController`我們需要執行下列動作：

1.  設定的基底類別`TabController`至`UITabBarController`。 
1.  建立`UIViewController`執行個體加入至`TabController`。 
1.  新增`UIViewController`指派給陣列的執行個體`ViewControllers`屬性`TabController`。 


將下列程式碼加入`TabController`類別來達成下列步驟：

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

請注意，對於每個`UIViewController`執行個體，我們設定`Title`屬性`UIViewController`。 當控制器加入至`UITabBarController`、`UITabBarController`會讀取`Title`每個控制站，並顯示相關聯的索引標籤上，如下所示：

[![](creating-tabbed-applications-images/00-app.png "執行範例應用程式")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>設定為 RootViewController TabController

控制器會放在索引標籤的順序對應的順序就會加入至此`ViewControllers`陣列。

若要取得`UITabController`載入做為第一個螢幕，我們需要使其視窗的`RootViewController`的下列程式碼所示， `AppDelegate`:

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

當我們執行應用程式現在，`UITabBarController`便會載入預設選取第一個索引標籤。 選取任何其他索引標籤相關聯的控制器會導致檢視所呈現`UITabBarController,`使用者已在其中選取第二個索引標籤，如下所示：

[![](creating-tabbed-applications-images/03-secondtab.png "顯示在第二個索引標籤")](creating-tabbed-applications-images/03-secondtab.png#lightbox)

 <a name="Modifying_TabBarItems" />


### <a name="modifying-tabbaritems"></a>修改 TabBarItems

現在，我們會執行的應用程式索引標籤上，讓我們來修改`TabBarItem`來變更所顯示的文字和影像，以及將徽章新增至其中一個索引標籤。

 <a name="Setting_a_System_Item" />


#### <a name="setting-a-system-item"></a>設定系統項目

首先，我們設定第一個索引標籤，使用系統項目。 建構函式中`TabController`，移除設定控制器的一行`Title`如`tab1`執行個體，並取代為下列的程式碼以設定控制器的`TabBarItem`屬性：

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

建立時`UITabBarItem`使用`UITabBarSystemItem`，標題和映像會自動提供 iOS 所顯示以下螢幕擷取畫面所示**我的最愛**圖示和標題的第一個索引標籤上：

 ![](creating-tabbed-applications-images/04a-tabimage.png "第一個索引標籤，以星形圖示")

 <a name="Setting_the_Title_and_Image" />


#### <a name="setting-the-title-and-image"></a>設定標題和映像

除了使用 [系統] 項目、 標題和映像的`UITabBarItem`可以設定自訂值。 例如，變更設定的程式碼`TabBarItem`屬性的具名的控制站`tab2`，如下所示：

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

上述程式碼會假設名為映像`second.png`已加入至 Visual Studio 中專案的根目錄 for mac。 我們實際上已新增三個映像至受測專案，以涵蓋所有裝置解析度，如下所示：

 [![](creating-tabbed-applications-images/tabbedimages7new.png "新增至專案的映像")](creating-tabbed-applications-images/tabbedimages7new.png#lightbox)

索引標籤影像應該是一般的解決方法，為高解析度的 60 和 90 x 90 適用於 iPhone 6 x 60 透明度與 30 x 30 個 png 再加上解析。 在我們的程式碼中，我們只需要載入名為的檔案`second.png`，iOS 會自動載入高解析度 Retina 顯示器的裝置上。 閱讀更多相關內容位於[處理映像](~/ios/app-fundamentals/images-icons/index.md)輔助線。 預設索引標籤列項目會以灰色顯示，以藍色濃淡時選取。

**注意**

上述的映像也可以加入至**資源**其內容將會自動複製到應用程式套件的根目錄是特殊目錄的目錄：

[![](creating-tabbed-applications-images/tabbedapplication8.png "為資源映像")](creating-tabbed-applications-images/tabbedapplication8.png#lightbox)

此外，當我們將`Title`屬性直接依據`TabBarItem`，它就會覆寫設定的任何值`Title`本身的控制站上。

當我們執行應用程式現在時，第二個索引標籤會顯示我們的自訂標題和映像如下所示：

[![](creating-tabbed-applications-images/05-customtab.png "第二個索引標籤，以正方形的圖示")](creating-tabbed-applications-images/05-customtab.png#lightbox)

 <a name="Setting_the_Badge_Value" />


#### <a name="setting-the-badge-value"></a>將徽章值

索引標籤也可以顯示的徽章。 例如，加入程式碼以設定徽章第三個索引標籤上的下列一行：

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

執行這個結果中包含的索引標籤，如下所示的左上角的"Hi"字串的紅色標籤：

[![](creating-tabbed-applications-images/06-badge.png "具有 Hi 徽章的第二個索引標籤")](creating-tabbed-applications-images/06-badge.png#lightbox)

徽章通常用於顯示數字指示未讀郵件，新的項目。 若要移除徽章，設定`BadgeValue`為 null，如下所示：

```csharp
tab3.TabBarItem.BadgeValue = null;
```

 <a name="Tabs_in_Non-RootViewController_Scenarios" />


## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>在非 RootViewController 案例中的索引標籤

在上述範例中，我們也示範了如何使用`UITabBarController`時`RootViewController`的視窗。 在此範例中我們將檢驗如何使用`UITabBarController`時不`RootViewController`並顯示如何建立使用分鏡腳本。

 <a name="Initial_Screen_Example" />


### <a name="initial-screen-example"></a>初始畫面範例

此案例中，初始畫面上載入的控制站，不是`UITabBarController`。 當使用者互動與螢幕藉由點選按鈕時，都會載入至相同檢視控制器`UITabBarController`，接著會呈現給使用者。 下列螢幕擷取畫面顯示應用程式流程：

[![](creating-tabbed-applications-images/inital-screen-application.png "這個螢幕擷取畫面顯示應用程式流程")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

讓我們開始新的應用程式，此範例中。 同樣地，我們將使用**iPhone > 應用程式 > 空專案 (C#)** 範本，此時命名專案`InitialScreenDemo`。


在此範例中我們需要儲存我們檢視控制器分鏡腳本。 若要新增分鏡腳本：

- 以滑鼠右鍵按一下專案名稱，然後選取**新增 > 新的檔案**。

- 新的檔案對話方塊出現時，瀏覽至**iOS > 空白 iPhone 分鏡腳本**。

讓我們來呼叫此新分鏡腳本**MainStoryboard** ，如下所示： 

[![](creating-tabbed-applications-images/new-file-dialog.png "MainStoryboard 檔案加入專案")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

有幾個重要的步驟，請注意，當加入分鏡腳本先前非腳本檔案，將涵蓋的[簡介分鏡腳本](~/ios/user-interface/storyboards/index.md)指南。 這些是：

 
1. 加入您的分鏡腳本名稱**主要介面**區段`Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "主要介面設 MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. 在您`App Delegate`，覆寫視窗方法，以下列程式碼：

    ```csharp
    public override UIWindow Window {
      get;
      set;
    }
    ```

我們即將在此範例中需要三個檢視的控制器。 一個名為`ViewController1`，將使用我們的初始檢視控制器，並在第一個索引標籤。其他兩個，名為`ViewController2`和`ViewController3`，用來在第二個和第三個索引標籤分別。

開啟設計工具 MainStoryboard.storyboard 檔案中，按兩下和拖曳到設計介面的三個檢視控制器。 我們想要每個有自己的類別對應至名稱上述項目，這些檢視控制站，請在下**識別 > 類別**，輸入名稱，如以下螢幕擷取畫面所示：

[![](creating-tabbed-applications-images/class-name.png "將類別設 ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

適用於 Mac 的 visual Studio 會自動產生的類別和設計工具所需的檔案，這可以看到在方案板，如下所示：

[![](creating-tabbed-applications-images/solution-pad2.png "專案中自動產生的檔案")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

 <a name="Creating_the_UI" />


#### <a name="creating-the-ui"></a>建立 UI

接下來，我們將建立簡單的使用者介面的 ViewController 檢視每個使用 Xamarin iOS 設計工具。

我們想要拖曳`Label`和`Button`到從 ViewController1**工具箱**右邊。 接下來我們將使用屬性填補來編輯名稱和下列控制項的文字：

-  **標籤**: `Text`  = **其中一個**
-  **按鈕**: `Title`  = **使用者採取某些初始的動作**


我們會控制在我們按鈕是否可見`TouchUpInside`事件，所以我們需要在後面的程式碼中參考它。 讓我們識別與**名稱**`aButton`屬性輸入板，如下列螢幕擷取畫面所示：

[![](creating-tabbed-applications-images/abutton-properties.png "將名稱設定為 aButton 屬性輸入板中")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

設計介面現在看起來類似下面的螢幕擷取畫面：

[![](creating-tabbed-applications-images/design-surface1.png "設計介面現在看起來應該類似這個螢幕擷取畫面")](creating-tabbed-applications-images/design-surface1.png#lightbox)

讓我們加入更詳細的資訊來`ViewController2`和`ViewController3`、 將標籤新增至每個，並分別將文字變更為 '兩個' 和 'Three'。 這會反白顯示給使用者我們查看哪一個索引標籤/檢視表。

#### <a name="wiring-up-the-button"></a>連接按鈕

我們將載入`ViewController1`第一次啟動應用程式。 當使用者點選該按鈕時，我們會隱藏按鈕，並載入`UITabBarController`與`ViewController1`第一個索引標籤中的執行個體。

當使用者放開`aButton`，我們想要觸發 TouchUpInside 事件。 讓我們來選取 按鈕，然後在**事件 索引標籤**的屬性輸入板中，宣告此事件處理常式 – `InitialActionCompleted` – 讓它可以參考程式碼中。 下列螢幕擷取畫面所示：

[![](creating-tabbed-applications-images/event-handler.png "當使用者放開 aButton 時，觸發 TouchUpInside 事件")](creating-tabbed-applications-images/event-handler.png#lightbox)

我們現在需要讓檢視在控制器事件引發時，隱藏按鈕`InitialActionCompleted`。 在`ViewController1`，加入下列的部分方法：

```csharp
partial void InitialActionCompleted (UIButton sender)
    {
      aButton.Hidden = true;  
    }
```

儲存檔案，並執行應用程式。 我們應該會看到一個出現的畫面和修改在消失 按鈕。

#### <a name="adding-the-tab-bar-controller"></a>加入索引標籤列控制器

我們現在有我們如預期般運作的初始檢視。 接下來，我們想將它加入至 `UITabBarController` 以及檢視 2 和 3。 讓我們來開啟分鏡腳本設計工具中。

在**工具箱**，搜尋** 索引標籤列控制器**控制站和物件下這拖曳到設計介面上。 您可以看到下面的螢幕擷取畫面中，索引標籤列控制器是無 UI，並因此使兩個檢視控制站與其預設：

[![](creating-tabbed-applications-images/tabbarcontroller.png "加入至配置的索引標籤列控制器")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

選取 黑色列底部，然後按下 delete 鍵刪除這些新的檢視控制站。

在我們的分鏡腳本，我們可以使用 Segues 處理 TabBarController 和我們檢視控制站之間的轉換。 之後互動初始的檢視，我們想要將其載入至 TabBarController 呈現給使用者。 讓我們設定此項目在設計工具中。

**按一下 Ctrl 以** 和 **拖曳** 從 [TabBarController] 按鈕。 滑鼠向上上會出現內容功能表。 我們想要使用強制回應 segue。 
 
若要設定每個索引標籤中， **Ctrl** TabBarController 我們檢視中控制站的順序的每一到三個，並選取關聯性從** 索引標籤**從內容功能表中，如下所示：

[![](creating-tabbed-applications-images/context-menu.png "選取索引標籤關聯性")](creating-tabbed-applications-images/context-menu.png#lightbox)

將分鏡腳本應該類似下面的螢幕擷取畫面：

[![](creating-tabbed-applications-images/segue-layout.png "分鏡腳本應該類似這個螢幕擷取畫面")](creating-tabbed-applications-images/segue-layout.png#lightbox)

如果我們按一下其中一個索引標籤列項目，並瀏覽屬性 面板，您可以看到有多種不同的選項，如下所示：

[![](creating-tabbed-applications-images/properties-panel.png "在 [屬性總管] 中設定索引標籤選項")](creating-tabbed-applications-images/properties-panel.png#lightbox)

我們可以用這個來編輯某些屬性，例如徽章、 標題和 iOS[識別碼](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html)，和其他項目

如果我們儲存，並執行應用程式現在，我們會發現按鈕在 ViewController1 執行個體載入至 TabBarController 時隨即再度出現。 讓我們來修正此問題，檢查目前的檢視是否具有父代檢視控制器。 若是如此，我們知道我們正在 TabBarController，並因此應隱藏 按鈕。 讓我們加入下列程式碼 ViewController1 類別：

```csharp
public override void ViewDidLoad ()
{
     if (ParentViewController != null){
       aButton.Hidden = true;
     }

}
```

當應用程式執行，而使用者按下按鈕時的第一個畫面 UITabBarController 載入時，從第一個放置在第一個索引標籤，如下所示的畫面檢視：

[![](creating-tabbed-applications-images/first-view.png "範例應用程式輸出")](creating-tabbed-applications-images/first-view.png#lightbox)

<!--Save the files and run the application:

[![](creating-tabbed-applications-images/inital-screen-application.png "Save the files and run the application")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)-->

## <a name="summary"></a>總結

本文涵蓋如何使用`UITabBarController`應用程式中。 我們逐步介紹如何將載入每個索引標籤中的控制站，以及如何設定屬性索引標籤上，這類的標題、 影像和徽章。 我們再檢查，使用分鏡腳本，如何載入`UITabBarController`在執行階段時，就會`RootViewController`的視窗。


## <a name="related-links"></a>相關連結

- [建立索引標籤式的應用程式 （範例）](https://developer.xamarin.com/samples/monotouch/CreatingTabbedApplications/)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [UITabBarController 類別參考](http://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)

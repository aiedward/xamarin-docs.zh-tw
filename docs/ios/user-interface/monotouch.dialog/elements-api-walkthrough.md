---
title: 使用 Elements API 建立 Xamarin iOS 應用程式
description: 本文是根據 [MonoTouch 簡介] 對話方塊文章中所呈現的資訊來建立。 它會提供逐步解說，說明如何使用 MonoTouch （MT）。D） Elements API，快速開始使用 MT 來建立應用程式。D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 2258b2e8451f896aff59c89478833976ef7086e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002373"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>使用 Elements API 建立 Xamarin iOS 應用程式

_本文是根據 [MonoTouch 簡介] 對話方塊文章中所呈現的資訊來建立。它會提供逐步解說，說明如何使用 MonoTouch （MT）。D） Elements API，快速開始使用 MT 來建立應用程式。D._

在本逐步解說中，我們將使用 MT。D Elements API，用來建立顯示工作清單之應用程式的主版詳細資料樣式。 當使用者選取導覽列中的 [ **+** ] 按鈕時，就會將新的資料列加入至工作的資料表。 選取資料列將會流覽至 [詳細資料] 畫面，讓我們更新工作描述和到期日，如下所示：

[![](elements-api-walkthrough-images/01-task-list-app.png "Selecting the row will navigate to the detail screen that allows us to update the task description and the due date")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

## <a name="setting-up-mtd"></a>設定 MT。D

MT.D 與 Xamarin 一起散發。 若要使用它，請以滑鼠右鍵按一下 Visual Studio 2017 或 Visual Studio for Mac 中之 Xamarin 專案的 [**參考**] 節點，然後新增**MonoTouch**元件的參考。 然後，視需要在您的原始程式碼中加入 `using MonoTouch.Dialog` 語句。

## <a name="elements-api-walkthrough"></a>Elements API 逐步解說

在[MonoTouch 的簡介對話](~/ios/user-interface/monotouch.dialog/index.md)文章中，我們對 MT 的不同部分有深刻的瞭解。D. 讓我們使用 Elements API 將它們全部整合到一個應用程式中。

## <a name="setting-up-the-multi-screen-application"></a>設定多畫面應用程式

若要啟動畫面建立程式，MonoTouch 會建立 `DialogViewController`，然後加入 `RootElement`。

若要使用 MonoTouch 建立多畫面應用程式，我們需要：

1. 建立 `UINavigationController.`
1. 建立 `DialogViewController.`
1. 新增 `DialogViewController` 做為 `UINavigationController.` 的根目錄 
1. 將 `RootElement` 新增至 `DialogViewController.`
1. 將 `Sections` 和 `Elements` 新增至 `RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>使用 UINavigationController

若要建立導覽樣式應用程式，我們必須建立 `UINavigationController`，然後將它新增為 `AppDelegate``FinishedLaunching` 方法中的 `RootViewController`。 為了讓 `UINavigationController` 與 MonoTouch 搭配使用，我們將 `DialogViewController` 新增至 `UINavigationController`，如下所示：

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    _window = new UIWindow (UIScreen.MainScreen.Bounds);
            
    _rootElement = new RootElement ("To Do List"){new Section ()};

    // code to create screens with MT.D will go here …

    _rootVC = new DialogViewController (_rootElement);
    _nav = new UINavigationController (_rootVC);
    _window.RootViewController = _nav;
    _window.MakeKeyAndVisible ();
            
    return true;
}
```

上述程式碼會建立 `RootElement` 的實例，並將它傳遞至 `DialogViewController`。 `DialogViewController` 在其階層的頂端一律會有 `RootElement`。 在此範例中，會使用「待辦事項清單」字串來建立 `RootElement`，以作為流覽控制器導覽列中的標題。 此時，執行應用程式會顯示如下所示的畫面：

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Running the application will present the screen shown here")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

讓我們來看看如何使用 MonoTouch 的階層式 `Sections` 和 `Elements` 來新增更多螢幕。

### <a name="creating-the-dialog-screens"></a>建立對話方塊畫面

`DialogViewController` 是 MonoTouch 的 `UITableViewController` 子類別，會使用來新增畫面。 MonoTouch 會藉由將 `RootElement` 新增至 `DialogViewController`來建立畫面，如前文所述。 `RootElement` 可以有代表資料表區段的 `Section` 實例。
這些區段是由元素、其他區段或甚至其他 `RootElements`所組成。 藉由 `RootElements`的嵌套，MonoTouch 會自動建立導覽樣式應用程式，如下所示。

### <a name="using-dialogviewcontroller"></a>使用 DialogViewController

`DialogViewController`（成為 `UITableViewController` 子類別）的 `UITableView` 是其 view。 在此範例中，我們想要在每次按下 [ **+** ] 按鈕時，將專案加入至資料表。 因為 `DialogViewController` 已新增至 `UINavigationController`，所以我們可以使用 `NavigationItem`的 `RightBarButton` 屬性來新增 [ **+** ] 按鈕，如下所示：

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

當我們稍早建立 `RootElement` 時，我們會將單一 `Section` 實例傳遞給它，讓我們可以加入專案，因為使用者會按下 [ **+** ] 按鈕。 我們可以使用下列程式碼，在按鈕的事件處理常式中完成這項操作：

```csharp
_addButton.Clicked += (sender, e) => {                
    ++n;
                
    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
    var taskElement = new RootElement (task.Name) {
        new Section () {
            new EntryElement (task.Name, "Enter task description", task.Description)
        },
        new Section () {
            new DateElement ("Due Date", task.DueDate)
        }
    };
    _rootElement [0].Add (taskElement);
};
```

這段程式碼會在每次按下按鈕時，建立新的 `Task` 物件。 以下顯示 `Task` 類別的簡單執行：

```csharp
public class Task
{   
    public Task ()
    {
    }
      
    public string Name { get; set; }
        
    public string Description { get; set; }

    public DateTime DueDate { get; set; }
}
```

工作的 `Name` 屬性會用來建立 `RootElement`的標題，以及名為 `n` 的計數器變數，這會針對每個新工作遞增。 MonoTouch 會將元素轉換成每個 `taskElement` 新增時加入至 `TableView` 的資料列。

## <a name="presenting-and-managing-dialog-screens"></a>呈現和管理對話方塊畫面

我們使用了 `RootElement`，因此 MonoTouch 會自動為每個工作的詳細資料建立新的畫面，並在選取資料列時加以導覽。

工作詳細資料畫面本身包含兩個區段：每個區段都包含單一元素。 第一個專案是從 `EntryElement` 建立，以便為工作的 `Description` 屬性提供可編輯的資料列。 選取元素時，會顯示文字編輯的鍵盤，如下所示：

 [![](elements-api-walkthrough-images/03-create-task.png "When the element is selected, a keyboard for text editing is presented as shown")](elements-api-walkthrough-images/03-create-task.png#lightbox)

第二個區段包含 `DateElement`，可讓我們管理工作的 `DueDate` 屬性。 選取日期會自動載入日期選擇器，如下所示：

 [![](elements-api-walkthrough-images/04-date-picker.png "Selecting the date automatically loads a date picker as")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

在 `EntryElement` 和 `DateElement` 案例中（或 MonoTouch 中的任何資料輸入元素），都會自動保留對值所做的任何變更。 我們可以藉由編輯日期來示範，然後在根畫面和各種工作詳細資料之間來回導覽，其中會保留詳細資料畫面中的值。

## <a name="summary"></a>總結

本文提供的逐步解說示範如何使用 MonoTouch 元素 API。 其中涵蓋了使用 MT 建立多畫面應用程式的基本步驟。D，包括如何使用 `DialogViewController`，以及如何新增專案和區段來建立畫面。 此外，它也示範了如何使用 MT。D 與 `UINavigationController`搭配使用。

## <a name="related-links"></a>相關連結

- [MTDWalkthrough （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdwalkthrough)
- [MonoTouch 簡介](~/ios/user-interface/monotouch.dialog/index.md)
- [反映 API 逐步解說](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [JSON 元素逐步解說](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 應用程式](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 類別參考](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)

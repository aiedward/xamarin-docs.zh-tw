---
title: 建立 Xamarin.iOS 應用程式使用元素 API
description: 這篇文章以 MonoTouch 對話方塊文章簡介所述的資訊時。 它會呈現的逐步解說，示範如何使用 MonoTouch.Dialog (MTD） 項目 API 可以快速地開始建置 MT 的應用程式D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 14711f9cc2c34d72765e28db158379bc2a26849b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61201061"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>建立 Xamarin.iOS 應用程式使用元素 API

_這篇文章以 MonoTouch 對話方塊文章簡介所述的資訊時。它會呈現的逐步解說，示範如何使用 MonoTouch.Dialog (MTD） 項目 API 可以快速地開始建置 MT 的應用程式D._

在本逐步解說中，我們將使用 mt。若要建立主版詳細資料的樣式來顯示工作清單應用程式 D 項目 API。 當使用者選取<span class="ui"> + </span>按鈕在導覽列中，新的資料列會加入至工作表。 選取的資料列會巡覽至詳細資料畫面，可讓我們將更新的工作描述和到期日，如下所示：

 [![](elements-api-walkthrough-images/01-task-list-app.png "選取的資料列會巡覽至詳細資料畫面，可讓我們將更新的工作描述和到期日")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 ## <a name="setting-up-mtd"></a>MT 的設定D

MTD Xamarin.iOS 一起散發。 若要使用它，以滑鼠右鍵按一下**參考**節點的 Xamarin.iOS 專案在 Visual Studio 2017 或 Visual Studio for Mac，並加入參考**MonoTouch.Dialog 1**組件。 然後，新增`using MonoTouch.Dialog`視您在來源中的陳述式的程式碼。

## <a name="elements-api-walkthrough"></a>項目 API 逐步解說

在  [MonoTouch 對話方塊簡介](~/ios/user-interface/monotouch.dialog/index.md)文章中，我們便可獲得具備深厚的 MT 的不同部分D. 若要將它們一起放入應用程式，讓我們使用元素 API。

## <a name="setting-up-the-multi-screen-application"></a>設定多重畫面應用程式

若要開始檢測建立程序，建立 MonoTouch.Dialog `DialogViewController`，然後加入`RootElement`。

若要建立 MonoTouch.Dialog 多重畫面應用程式，我們需要：

1.  建立 `UINavigationController.`
1.  建立 `DialogViewController.`
1.  新增`DialogViewController`作為根目錄  `UINavigationController.` 
1.  新增`RootElement`至  `DialogViewController.`
1.  新增`Sections`和`Elements`至  `RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>使用 UINavigationController

若要建立導覽樣式的應用程式，我們需要建立`UINavigationController`，然後將它做為新增`RootViewController`中`FinishedLaunching`方法`AppDelegate`。 若要讓`UINavigationController`使用 MonoTouch.Dialog，我們將新增`DialogViewController`來`UINavigationController`，如下所示：

```csharp
public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
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

上述程式碼建立的執行個體`RootElement`，並將傳遞到`DialogViewController`。 `DialogViewController`一律會有`RootElement`及其階層的頂端。 在此範例中，`RootElement`建立字串 「 待辦事項清單，"作為巡覽控制器的導覽列中的項目。 此時，執行應用程式會造成畫面如下所示：

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "執行應用程式將會顯示如下所示的畫面")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

我們來看看如何使用 MonoTouch.Dialog 的階層式結構`Sections`和`Elements`新增更多畫面。

### <a name="creating-the-dialog-screens"></a>建立對話方塊畫面

A`DialogViewController`是`UITableViewController`MonoTouch.Dialog 用來將畫面新增子類別。 MonoTouch.Dialog 建立畫面加上`RootElement`至`DialogViewController`，如我們所見上方。 `RootElement`可以有`Section`執行個體，表示資料表的區段。
下列各組成項目、 其他區段，或甚至是其他`RootElements`。 巢狀結構由`RootElements`，MonoTouch.Dialog 會自動建立導覽樣式的應用程式，如接下來，我們所見。

### <a name="using-dialogviewcontroller"></a>使用 DialogViewController

`DialogViewController`，正在`UITableViewController`子類別，有`UITableView`作為它的檢視。 在此範例中，我們想要將項目加入至資料表每次<span class="ui"> + </span>點選按鈕時。 由於`DialogViewController`已新增至`UINavigationController`，我們可以使用`NavigationItem`的`RightBarButton`屬性，即可加入<span class="ui"> + </span>按鈕，如下所示：

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

我們在建立時`RootElement`更早版本，我們傳遞給它的單一`Section`執行個體，以便我們可以新增項目<span class="ui"> + </span>使用者點選按鈕時。 若要在事件完成按鈕處理常式，我們可以使用下列程式碼：

```csharp
_addButton.Clicked += (sender, e) => {
                
        ++n;
                
        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
        var taskElement = new RootElement (task.Name){
                new Section () {
                        new EntryElement (task.Name, 
                                "Enter task description", task.Description)
                },
                new Section () {
                        new DateElement ("Due Date", task.DueDate)
                }
        };
        _rootElement [0].Add (taskElement);
};
```

此程式碼會建立新`Task`物件每次點選按鈕時。 下圖顯示簡單的實作`Task`類別：

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

工作的`Name`屬性用來建立`RootElement`的標題，以及名為計數器變數`n`，就會遞增每個新的工作。 MonoTouch.Dialog 會變成加入的資料列中的項目`TableView`時每個`taskElement`加入。

## <a name="presenting-and-managing-dialog-screens"></a>呈現和管理對話方塊畫面

我們使用`RootElement`以便 MonoTouch.Dialog 會自動建立一個新的畫面，如每個工作的詳細資訊並選取一個資料列時，瀏覽至它。

工作詳細資料畫面本身是由兩個區段所組成下列各節包含單一項目。 從建立第一個項目`EntryElement`提供工作的可編輯的資料列`Description`屬性。 選取的項目時，文字編輯鍵盤會如下所示：

 [![](elements-api-walkthrough-images/03-create-task.png "文字編輯鍵盤選取項目時，會顯示所示")](elements-api-walkthrough-images/03-create-task.png#lightbox)

第二個區段包含`DateElement`，可讓我們管理工作的`DueDate`屬性。 選取的日期會自動載入日期選擇器，如所示：

 [![](elements-api-walkthrough-images/04-date-picker.png "選取的日期會自動載入做為日期選擇器")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

在這兩`EntryElement`和`DateElement`情況下 （或任何輸入資料中的項目 MonoTouch.Dialog），會自動保留為值的任何變更。 我們可以示範此編輯日期，然後 [根] 畫面和各種不同的工作詳細資料，其中保留的詳細資料畫面中的值之間來回巡覽。

## <a name="summary"></a>總結

這篇文章所呈現的逐步解說，示範了如何使用 MonoTouch.Dialog 項目 API。 它涵蓋建立 MT 的多重畫面應用程式的基本步驟D，包括如何使用`DialogViewController`以及如何新增項目和區段，來建立螢幕。 此外，它示範了如何使用 MT搭配 D `UINavigationController`。

## <a name="related-links"></a>相關連結

- [MTDWalkthrough （範例）](https://developer.xamarin.com/samples/MTDWalkthrough/)
- [螢幕錄製影片-Miguel de Icaza 建立 iOS 登入畫面 MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [螢幕錄製影片-輕鬆地建立 iOS 使用者介面與 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [MonoTouch.Dialog 簡介](~/ios/user-interface/monotouch.dialog/index.md)
- [反映 API 逐步解說](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [JSON 元素的逐步解說](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [在 Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 應用程式](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 類別參考](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)

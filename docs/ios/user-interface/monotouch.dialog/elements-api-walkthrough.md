---
title: "逐步解說-建立使用項目 API 的應用程式"
description: "本文章根據 MonoTouch 對話方塊文章簡介 > 中所呈現的資訊。 它會呈現的逐步解說，示範如何使用 MonoTouch.Dialog （機動D） 項目 API 來快速地開始建置機動的應用程式D."
ms.topic: article
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 19e20015d1872cbaea21dd8b8e5431981e463c33
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough---creating-an-application-using-the-elements-api"></a>逐步解說-建立使用項目 API 的應用程式

_本文章根據 MonoTouch 對話方塊文章簡介 > 中所呈現的資訊。它會呈現的逐步解說，示範如何使用 MonoTouch.Dialog （機動D） 項目 API 來快速地開始建置機動的應用程式D._

在此逐步解說中，我們將使用機動D 項目來建立主從式樣式顯示工作清單中的應用程式的 API。 當使用者選取<span class="ui"> + </span>按鈕在導覽列中，新的資料列會加入至工作表。 選取的資料列會巡覽至 [詳細資料] 畫面，讓我們為更新的工作描述和到期日，如下所示：

 [ ![](elements-api-walkthrough-images/01-task-list-app.png "選取的資料列會導覽至詳細資料畫面，讓我們為更新的工作描述和到期日")](elements-api-walkthrough-images/01-task-list-app.png)

 <a name="Elements_API_Walkthrough" />


## <a name="elements-api-walkthrough"></a>逐步解說中應用程式開發介面項目

在[簡介 MonoTouch 對話方塊](~/ios/user-interface/monotouch.dialog/index.md)我們獲得充分了解機動的不同部分的文件：D. 讓我們使用項目的應用程式開發介面來將它們一起放入應用程式。

 <a name="Setting_up_the_Multi-Screen_Application" />


## <a name="setting-up-the-multi-screen-application"></a>設定多重畫面應用程式

若要啟動螢幕的建立程序，建立 MonoTouch.Dialog `DialogViewController`，然後將`RootElement`。

若要建立 MonoTouch.Dialog 多螢幕應用程式，我們需要：

1.  建立  `UINavigationController.`
1.  建立  `DialogViewController.`
1.  新增`DialogViewController`作為根目錄  `UINavigationController.` 
1.  新增`RootElement`至  `DialogViewController.`
1.  新增`Sections`和`Elements`至  `RootElement.` 


 <a name="Using_A_UINavigationController" />


### <a name="using-a-uinavigationcontroller"></a>使用 UINavigationController

若要建立導覽樣式應用程式，我們需要建立`UINavigationController`，並將它做為`RootViewController`中`FinishedLaunching`方法`AppDelegate`。 若要讓`UINavigationController`搭配 MonoTouch.Dialog，我們將新增`DialogViewController`來`UINavigationController`如下所示：

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

上述程式碼建立的執行個體`RootElement`並將資料傳遞到`DialogViewController`。 `DialogViewController`必定`RootElement`在其階層的頂端。 在此範例中，`RootElement`會透過 「 待辦清單"會做為導覽控制站的導覽列中的標題的字串。 此時，執行應用程式會呈現畫面如下所示：

 [ ![](elements-api-walkthrough-images/02-to-do-list-screen-.png "執行應用程式將會顯示如下所示的畫面")](elements-api-walkthrough-images/02-to-do-list-screen-.png)

我們來看看如何使用 MonoTouch.Dialog 的階層式結構`Sections`和`Elements`新增更多的螢幕。

 <a name="Creating_the_Dialog_Screens" />


### <a name="creating-the-dialog-screens"></a>建立對話方塊畫面

A`DialogViewController`是`UITableViewController`MonoTouch.Dialog 用來將螢幕加入子類別。 MonoTouch.Dialog 建立加入螢幕`RootElement`至`DialogViewController`，如我們所見的上方。 `RootElement`可以有`Section`執行個體，表示資料表的區段。
區段的項目時，會啟動，其他區段，或甚至其他`RootElements`。 巢狀結構`RootElements`，MonoTouch.Dialog，自動建立導覽樣式的應用程式，接下來，我們會看到。

 <a name="Using_DialogViewController" />


### <a name="using-dialogviewcontroller"></a>使用 DialogViewController

`DialogViewController`，正在`UITableViewController`子類別，具有`UITableView`做為它的檢視。 在此範例中，我們想要將項目加入至資料表每次<span class="ui"> + </span>所點選按鈕。 因為`DialogViewController`已新增至`UINavigationController`，我們可以使用`NavigationItem`的`RightBarButton`屬性將<span class="ui"> + </span>按鈕，如下所示：

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

當我們建立`RootElement`更早版本，我們傳遞單一`Section`執行個體，讓我們無法將項目為<span class="ui"> + </span>由使用者所點選按鈕。 我們可以使用下列程式碼來完成此事件中的按鈕處理常式：

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

此程式碼會建立新`Task`物件所點選按鈕的每一次。 下列範例示範簡單的實作`Task`類別：

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

 []()

工作的`Name`屬性用來建立`RootElement`的標題，以及名為的計數器變數`n`，就會遞增每個新的工作。 MonoTouch.Dialog 將元素轉換成資料列加入至`TableView`時每個`taskElement`加入。

 <a name="Presenting_and_Managing_Dialog_Screens" />


## <a name="presenting-and-managing-dialog-screens"></a>展示和管理對話方塊畫面

我們使用`RootElement`以便 MonoTouch.Dialog 會自動建立每個工作的詳細資料的新畫面並選取一個資料列時，瀏覽至它。

工作詳細資料畫面本身組成兩個區段。下列各節包含單一項目。 第一個項目從建立`EntryElement`提供工作的可編輯的資料列`Description`屬性。 選取的項目時，文字編輯鍵盤會如下所示：

 [ ![](elements-api-walkthrough-images/03-create-task.png "顯示選取項目時，呈現鍵盤文字編輯")](elements-api-walkthrough-images/03-create-task.png)

第二節包含`DateElement`，讓我們能夠管理的工作`DueDate`屬性。 選取日期，會自動載入日期選擇器，如所示：

 [ ![](elements-api-walkthrough-images/04-date-picker.png "選取的日期會自動載入做為日期選擇器")](elements-api-walkthrough-images/04-date-picker.png)

在這兩`EntryElement`和`DateElement`案例 （或任何資料輸入中的項目 MonoTouch.Dialog），會自動保留為值的任何變更。 我們可以編輯的日期，然後巡覽根畫面和各種工作詳細資料，其中保留詳細資料畫面中的值之間來回進行示範。

 <a name="Summary" />


## <a name="summary"></a>總結

本文提供的逐步解說，示範了如何使用 MonoTouch.Dialog 項目的應用程式開發介面。 它涵蓋使用機動建立多重畫面應用程式的基本步驟D，包括如何使用`DialogViewController`以及如何新增項目和區段建立的螢幕。 此外，它示範了如何使用機動搭配 D `UINavigationController`。


## <a name="related-links"></a>相關連結

- [MTDWalkthrough （範例）](https://developer.xamarin.com/samples/MTDWalkthrough/)
- [錄影畫面-Miguel de Icaza iOS 登入畫面以建立 MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [錄影畫面-輕鬆地建立 iOS 使用者介面與 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [MonoTouch.Dialog 簡介](~/ios/user-interface/monotouch.dialog/index.md)
- [反映 API 逐步解說](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [逐步解說中 JSON 項目](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 應用程式](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 類別參考](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)

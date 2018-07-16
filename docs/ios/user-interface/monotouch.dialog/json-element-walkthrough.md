---
title: 若要建立使用者介面在 Xamarin.iOS 中使用 JSON
description: MonoTouch.Dialog (MTD） 包含支援動態 UI 透過 JSON 資料的產生。 在本教學課程中，我們將逐步解說如何使用 JSONElement 建立使用者介面，從所包含的應用程式，或從遠端的 Url 載入的 JSON。
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 94cef78bb7eedc03192071f17af765ebb702e260
ms.sourcegitcommit: cb80df345795989528e9df78eea8a5b45d45f308
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/14/2018
ms.locfileid: "39038491"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>若要建立的使用者介面在 Xamarin.iOS 中使用 JSON

_MonoTouch.Dialog (MTD） 包含支援動態 UI 透過 JSON 資料的產生。在本教學課程中，我們將逐步解說如何使用 JSONElement 建立使用者介面，從所包含的應用程式，或從遠端的 Url 載入的 JSON。_

MTD 支援在 JSON 中宣告的建立使用者介面。 當使用 JSON，MT 宣告的項目D 將相關聯的項目為您自動建立。 JSON 可以載入從本機檔案，剖析`JsonObject`執行個體或甚至是遠端的 Url。

MTD 支援時，可以使用元素 API 中使用 JSON 功能的完整範圍。 比方說，下列螢幕擷取畫面中的應用程式的完全宣告使用 JSON 中：

[![](json-element-walkthrough-images/01-load-from-file.png "例如，此螢幕擷取畫面中的應用程式完全宣告使用 JSON") ](json-element-walkthrough-images/01-load-from-file.png#lightbox) [ ![ ](json-element-walkthrough-images/01-load-from-file.png "比方說，此螢幕擷取畫面中的應用程式完全使用宣告JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

從範例重新瀏覽[項目 API 逐步解說](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)教學課程中，顯示如何加入 工作詳細資料螢幕使用 JSON。

## <a name="setting-up-mtd"></a>MT 的設定D

MTD Xamarin.iOS 一起散發。 若要使用它，以滑鼠右鍵按一下**參考**節點的 Xamarin.iOS 專案在 Visual Studio 2017 或 Visual Studio for Mac，並加入參考**MonoTouch.Dialog 1**組件。 然後，新增`using MonoTouch.Dialog`視您在來源中的陳述式的程式碼。

## <a name="json-walkthrough"></a>JSON 逐步解說

本逐步解說的範例可讓您建立的工作。 第一個畫面上選取一項工作時，詳細資料畫面會顯示所示：

 [![](json-element-walkthrough-images/03-task-list.png "如所示，第一個畫面上選取一項工作時，會顯示詳細資料畫面")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>建立 JSON

針對此範例中，我們會從名為專案中的檔案載入 JSON `task.json`。 MTD 會預期要符合鏡像處理項目 API 語法的 JSON。 就像使用元素 API 從程式碼，當使用 JSON 時，我們宣告區段，並在這些章節中，我們新增項目。 若要宣告區段和 JSON 中的項目，我們使用的字串 「 區段 」 和 「 項目 」 分別做為索引鍵。 對於每個項目中，相關聯的項目類型設定使用`type`索引鍵。 每個項目屬性是設定索引鍵屬性名稱。

例如，下列的 JSON 描述的區段和工作詳細資料的項目：

```csharp
{
    "title": "Task",
    "sections": [
        {
          "elements" : [
            {
                "id" : "task-description",
                "type": "entry",
                "placeholder": "Enter task description"
            },
            {
                "id" : "task-duedate",
                "type": "date",
                "caption": "Due Date",
                "value": "00:00"
            }
         ]
        }
    ]
  }
```

請注意上述的 JSON 包含每個項目的 id。 任何項目可以包含一個識別碼，來參考它在執行階段。 我們會看到如何使用此名稱稍後當我們示範如何將程式碼中的 JSON。

## <a name="loading-the-json-in-code"></a>正在載入程式碼中的 JSON

一旦已定義的 JSON，我們需要將它載入 MT使用 D`JsonElement`類別。 假設使用前面所建立的 JSON 檔案已經加入到專案中名稱 sample.json 並指定在建置動作載入的內容為`JsonElement`很簡單，只要呼叫下列程式碼行：

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

由於我們要將它新增隨每次建立工作時，我們可以修改先前的項目 API 範例中，如下所示所按的按鈕：

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>在執行階段存取的項目

您應該記得我們將新增識別碼至這兩個項目，當我們在 JSON 檔案中宣告。 我們可以使用 id 屬性來存取在執行階段來修改其屬性，在程式碼中的每個項目。 例如，下列程式碼參考的項目和日期的項目來設定從工作物件的值：

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        taskElement.Caption = task.Name;

        var description = taskElement ["task-description"] as EntryElement;

        if (description != null) {
                description.Caption = task.Name;
                description.Value = task.Description;       
        }

        var duedate = taskElement ["task-duedate"] as DateElement;

        if (duedate != null) {                
                duedate.DateValue = task.DueDate;
        }
        _rootElement [0].Add (taskElement);
};
```

## <a name="loading-json-from-a-url"></a>從 url 載入 JSON

MTD 也支援以動態方式載入 JSON 從外部 Url 只是將 Url 傳遞至建構函式的`JsonElement`。 MTD 將會展開宣告在 JSON 中依需求為您在畫面之間瀏覽的階層。 例如，請考慮下面類似的 JSON 檔案位於本機 web 伺服器的根目錄：

```csharp
{
    "type": "root",
    "title": "home",
    "sections": [
       {
         "header": "Nested view!",
         "elements": [
           {
             "type": "boolean",
             "caption": "Just a boolean",
             "id": "first-boolean",
             "value": false
           },
           {
             "type": "string",
             "caption": "Welcome to the nested controller"
           }
         ]
       }
     ]
}
```

我們可以載入此使用`JsonElement`如下列程式碼所示：

```csharp
_rootElement = new RootElement ("Json Example"){
        new Section (""){ new JsonElement ("Load from url",
                "http://localhost/sample.json")
        }
};
```

在執行階段，檔案將會擷取和剖析 MT當使用者導覽至第二個檢視，如以下螢幕擷取畫面所示的 D:

 [![](json-element-walkthrough-images/04-json-web-example.png "檔案會擷取並剖析 MT當使用者導覽到第二個檢視的 D")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>總結

這篇文章說明如何建立使用 MT 介面從 JSON D。 它示範了如何將檔案與應用程式，以及從遠端的 Url 中包含的 JSON。 它也會示範如何存取 JSON 中所述，在執行階段的項目。

## <a name="related-links"></a>相關連結

- [MTDJsonDemo （範例）](https://developer.xamarin.com/samples/MTDJsonDemo/)
- [螢幕錄製影片-Miguel de Icaza 建立 iOS 登入畫面 MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [螢幕錄製影片-輕鬆地建立 iOS 使用者介面與 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [MonoTouch.Dialog 簡介](~/ios/user-interface/monotouch.dialog/index.md)
- [項目 API 逐步解說](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [反映 API 逐步解說](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [在 Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 應用程式](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 類別參考](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)

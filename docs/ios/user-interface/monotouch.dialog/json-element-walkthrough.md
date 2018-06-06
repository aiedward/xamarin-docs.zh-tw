---
title: 使用 JSON Xamarin.iOS 中建立使用者介面
description: MonoTouch.Dialog （機動D） 包含支援 JSON 資料透過動態 UI 產生。 在本教學課程中，我們將逐步解說如何使用 JSONElement 隨附於應用程式，或從遠端 Url 載入 JSON 從建立使用者介面。
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: f9ba2cce1650260aa889e8282c091012ef8bbddc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790649"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>使用 JSON Xamarin.iOS 中建立使用者介面

_MonoTouch.Dialog （機動D） 包含支援 JSON 資料透過動態 UI 產生。在本教學課程中，我們將逐步解說如何使用 JSONElement 隨附於應用程式，或從遠端 Url 載入 JSON 從建立使用者介面。_

周D 支援在 JSON 中宣告的建立使用者介面。 當使用 JSON，機動宣告的項目D 將相關項目為您自動建立。 JSON 可以載入從本機檔案，請剖析`JsonObject`執行個體或遠端 Url。

周D 支援當使用 JSON 時，項目 API 中可用功能的完整範圍。 例如，下列螢幕擷取畫面中的應用程式的完全宣告使用 JSON 中：

[![](json-element-walkthrough-images/01-load-from-file.png "例如，下列螢幕擷取畫面中的應用程式完全宣告使用 JSON") ](json-element-walkthrough-images/01-load-from-file.png#lightbox) [ ![ ](json-element-walkthrough-images/01-load-from-file.png "比方說，此螢幕擷取畫面中的應用程式完全使用宣告JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

讓我們重新瀏覽的範例中[元素 API 逐步解說](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)教學課程中，顯示如何加入 工作詳細資料螢幕使用 JSON。

## <a name="json-walkthrough"></a>JSON 逐步解說

本逐步解說的範例可讓您建立的工作。 工作的第一個畫面選取時，詳細資料螢幕會看見所示：

 [![](json-element-walkthrough-images/03-task-list.png "如所示，工作的第一個畫面選取時，就會呈現詳細資料畫面")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>建立 JSON

針對此範例中，我們會從名為專案中的檔案載入 JSON `task.json`。 周D 預期要符合鏡像處理項目 API 語法的 JSON。 就像使用項目 API 從程式碼，當使用 JSON 時，我們會宣告區段，這些區段內我們新增項目。 若要宣告區段和 JSON 中的項目，我們使用的字串 「 區段 」 和 「 項目 」 分別做為索引鍵。 對於每個項目，相關聯的項目類型透過設定`type`索引鍵。 每個項目屬性是設定索引鍵屬性名稱。

例如，下列 JSON 描述區段和工作詳細資料的項目：

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

請注意上述的 JSON 包含每個項目的 id。 任何項目可以包含的識別碼，來參考它在執行階段。 我們會看到這個用法稍後當我們示範如何載入程式碼中的 JSON。

 <a name="Loading_the_JSON_in_Code" />


## <a name="loading-the-json-in-code"></a>載入程式碼中的 JSON

一旦已定義的 JSON，我們需要將其載入至機動使用 D`JsonElement`類別。 假設我們在上方建立的 JSON 檔案已加入至專案的名稱 sample.json 並指定建置動作的內容，載入`JsonElement`很簡單，呼叫下列程式碼行：

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

因為我們會增加這視每次會建立一個工作，所以我們可以修改 button 已按下從先前的項目 API 範例，如下所示：

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        _rootElement [0].Add (taskElement);
};
```

 <a name="Accessing_Elements_at_Runtime" />


## <a name="accessing-elements-at-runtime"></a>存取在執行階段的項目

還記得我們識別碼時加入至兩個元素，我們在 JSON 檔案中宣告它們。 我們可以使用識別碼屬性來存取在執行階段來修改其屬性，在程式碼中的每個項目。 例如，下列程式碼參考的項目和日期的項目從工作物件設定的值：

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

 <a name="Loading_JSON_from_a_Url" />


## <a name="loading-json-from-a-url"></a>從 Url 載入 JSON

周D 也支援以動態方式載入外部 Url 從 JSON 只將 Url 傳遞至建構函式`JsonElement`。 周D 會展開宣告在 JSON 中依需求為您瀏覽螢幕之間的階層。 例如，請考慮下面的例如 JSON 檔案位於根目錄中的本機 web 伺服器：

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

在執行階段，檔案會擷取並剖析機動當使用者巡覽至第二個檢視，如以下螢幕擷取畫面所示的 D:

 [![](json-element-walkthrough-images/04-json-web-example.png "檔案會擷取並剖析機動當使用者巡覽至第二個檢視的 D")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>總結

本文說明如何建立使用機動介面從 JSON D。 它示範了如何載入 JSON 檔案與應用程式，以及從遠端 Url 中。 它也示範了如何存取在執行階段在 JSON 中所述的項目。


## <a name="related-links"></a>相關連結

- [MTDJsonDemo （範例）](https://developer.xamarin.com/samples/MTDJsonDemo/)
- [錄影畫面-Miguel de Icaza iOS 登入畫面以建立 MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [錄影畫面-輕鬆地建立 iOS 使用者介面與 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [MonoTouch.Dialog 簡介](~/ios/user-interface/monotouch.dialog/index.md)
- [逐步解說中應用程式開發介面項目](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [反映 API 逐步解說](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 應用程式](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 類別參考](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)

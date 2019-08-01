---
title: 在 Xamarin 中使用 JSON 建立使用者介面
description: MonoTouch。對話方塊 (MT。D) 包含透過 JSON 資料產生動態 UI 的支援。 在本教學課程中, 我們將逐步解說如何使用 JSONElement 來建立 JSON 中的使用者介面, 其包含在應用程式中, 或從遠端 Url 載入。
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 64b35588afa06a6a8f4a206f10827409f9de316f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645225"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>在 Xamarin 中使用 JSON 建立使用者介面

_MonoTouch。對話方塊 (MT。D) 包含透過 JSON 資料產生動態 UI 的支援。在本教學課程中, 我們將逐步解說如何使用 JSONElement 來建立 JSON 中的使用者介面, 其包含在應用程式中, 或從遠端 Url 載入。_

MT.D 支援建立以 JSON 宣告的使用者介面。 使用 JSON、MT 宣告元素時。D 會自動為您建立相關聯的元素。 您可以從本機檔案、剖析的`JsonObject`實例或甚至是遠端 Url 來載入 JSON。

MT.D 支援在使用 JSON 時, 元素 API 中可用的完整功能範圍。 例如, 下列螢幕擷取畫面中的應用程式是使用 JSON 來完全宣告:

[![](json-element-walkthrough-images/01-load-from-file.png "例如，此螢幕擷取畫面中的應用程式完全宣告使用 JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox) [ ![](json-element-walkthrough-images/01-load-from-file.png "比方說，此螢幕擷取畫面中的應用程式完全使用宣告JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

讓我們來回顧[ELEMENTS API 逐步](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)解說教學課程中的範例, 示範如何使用 JSON 加入工作詳細資料畫面。

## <a name="setting-up-mtd"></a>設定 MT。D

MT.D 與 Xamarin 一起散發。 若要使用它, 請以滑鼠右鍵按一下 Visual Studio 2017 或 Visual Studio for Mac 中之 Xamarin 專案的 [**參考**] 節點, 然後新增**MonoTouch**元件的參考。 然後, 視`using MonoTouch.Dialog`需要在您的原始程式碼中新增語句。

## <a name="json-walkthrough"></a>JSON 逐步解說

此逐步解說的範例可讓您建立工作。 當第一個畫面上選取了工作時, 會顯示詳細資料畫面, 如下所示:

 [![](json-element-walkthrough-images/03-task-list.png "當第一個畫面上選取了工作時, 會顯示詳細資料畫面, 如下所示")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>建立 JSON

在此範例中, 我們會從名為`task.json`的專案中的檔案載入 JSON。 MT.D 預期 JSON 必須符合會反映元素 API 的語法。 就像使用程式碼中的 Elements API 一樣, 使用 JSON 時, 我們會宣告區段, 並在這些區段中新增元素。 為了在 JSON 中宣告區段和專案, 我們分別使用字串 "sections" 和 "elements" 作為索引鍵。 針對每個專案, 會使用`type`索引鍵來設定相關聯的元素類型。 每個其他專案屬性都是以屬性名稱做為索引鍵來設定。

例如, 下列 JSON 描述工作詳細資料的區段和元素:

```json
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

請注意, 上述 JSON 包含每個元素的識別碼。 任何元素都可以包含識別碼, 以便在執行時間參考它。 當我們示範如何在程式碼中載入 JSON 時, 我們會看到這種情況的使用方式。

## <a name="loading-the-json-in-code"></a>在程式碼中載入 JSON

定義 JSON 之後, 我們需要將它載入至 MT。使用類別的`JsonElement` D。 假設我們先前建立的 JSON 檔案已新增至名稱為 sample 的專案, 並指定內容的建立動作, `JsonElement`則載入會像呼叫下列程式碼一樣簡單:

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

因為我們會在每次建立工作時依需求新增此專案, 所以我們可以從先前的 Elements API 範例中修改按一下的按鈕, 如下所示:

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>在執行時間存取元素

回想一下, 我們已在 JSON 檔案中宣告這兩個專案時, 將識別碼新增至這兩個元素。 我們可以使用 id 屬性來存取執行時間中的每個專案, 以在程式碼中修改其屬性。 例如, 下列程式碼會參考 entry 和 date 元素, 以設定 task 物件的值:

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

MT.D 也支援透過直接將 Url 傳遞至`JsonElement`的函式, 從外部 URL 動態載入 JSON。 MT.當您在畫面之間流覽時, D 會視需要擴充 JSON 中所宣告的階層。 例如, 假設有一個 JSON 檔案, 如下所示, 位於本機 web 伺服器的根目錄:

```json
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

我們可以使用`JsonElement`來載入此, 如下列程式碼所示:

```csharp
_rootElement = new RootElement ("Json Example") {
    new Section ("") {
        new JsonElement ("Load from url", "http://localhost/sample.json")
    }
};
```

在執行時間, 檔案將會由 MT 抓取和剖析。D 當使用者流覽至第二個視圖時, 如下列螢幕擷取畫面所示:

 [![](json-element-walkthrough-images/04-json-web-example.png "檔案將會由 MT 抓取和剖析。D 當使用者流覽至第二個視圖時")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>總結

本文說明如何使用 MT 來建立 using 介面。D from JSON。 它示範如何使用應用程式以及從遠端 Url 載入包含在檔案中的 JSON。 它也示範如何在執行時間存取 JSON 中所述的元素。

## <a name="related-links"></a>相關連結

- [MTDJsonDemo (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdjsondemo)
- [MonoTouch 簡介](~/ios/user-interface/monotouch.dialog/index.md)
- [Elements API 逐步解說](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [反映 API 逐步解說](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 應用程式](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 類別參考](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)

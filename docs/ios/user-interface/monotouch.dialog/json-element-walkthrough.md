---
title: 使用 JSON 在 Xamarin 中建立消費者介面
description: Monotouch.dll。對話方塊 (MT。D) 包含透過 JSON 資料產生動態 UI 的支援。 在本教學課程中，我們將逐步解說如何使用 JSONElement 來建立 JSON 中的使用者介面，該介面可包含在應用程式中，或是從遠端 Url 載入。
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: c19cf373a89798703224574fe13724dfbade33bb
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436684"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>使用 JSON 在 Xamarin 中建立使用者介面

_Monotouch.dll。對話方塊 (MT。D) 包含透過 JSON 資料產生動態 UI 的支援。在本教學課程中，我們將逐步解說如何使用 JSONElement 來建立 JSON 中的使用者介面，該介面可包含在應用程式中，或是從遠端 Url 載入。_

MT.D 支援建立以 JSON 宣告的使用者介面。 當元素是使用 JSON （MT）宣告時。D 將會自動為您建立相關聯的元素。 您可以從本機檔案、剖析的 `JsonObject` 實例或甚至是遠端 Url 來載入 JSON。

MT.D 支援在使用 JSON 時，可在元素 API 中使用的完整功能範圍。 例如，下列螢幕擷取畫面中的應用程式會使用 JSON 完整宣告：

[ ![ 例如，此螢幕擷取畫面中的應用程式是使用 json 完整](json-element-walkthrough-images/01-load-from-file.png)](json-element-walkthrough-images/01-load-from-file.png#lightbox)宣告的[ ![ ，例如，此螢幕擷取畫面中的應用程式是使用 json 完整宣告的。](json-element-walkthrough-images/01-load-from-file.png)](json-element-walkthrough-images/01-load-from-file.png#lightbox)

讓我們從 [ELEMENTS API 逐步](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) 解說教學課程中回顧範例，示範如何使用 JSON 加入工作詳細資料畫面。

## <a name="setting-up-mtd"></a>設定 MT。D

MT.D 與 Xamarin 一起散發。 若要使用它，請在 Visual Studio 2017 或 Visual Studio for Mac 中，以滑鼠右鍵按一下 Xamarin 專案的 [ **參考** ] 節點，然後加入 monotouch.dll 的參考 **。對話方塊-1** 元件。 然後， `using MonoTouch.Dialog` 視需要在您的原始程式碼中加入語句。

## <a name="json-walkthrough"></a>JSON 逐步解說

本逐步解說的範例可讓您建立工作。 在第一個畫面上選取工作時，會顯示詳細資料畫面，如下所示：

 [![在第一個畫面上選取工作時，會顯示詳細資料畫面，如下所示](json-element-walkthrough-images/03-task-list.png)](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>建立 JSON

在此範例中，我們會從名為的專案中的檔案載入 JSON `task.json` 。 MT.D 預期 JSON 必須符合可反映元素 API 的語法。 就像使用程式碼中的 Elements API 一樣，在使用 JSON 時，我們會宣告區段，而在這些區段中，我們會加入元素。 為了在 JSON 中宣告區段和元素，我們會分別使用字串 "sections" 和 "elements" 作為索引鍵。 針對每個專案，會使用索引鍵來設定相關聯的元素類型 `type` 。 每個 [其他元素] 屬性都設定為屬性名稱做為索引鍵。

例如，下列 JSON 描述工作詳細資料的區段和元素：

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

請注意，上述 JSON 包含每個元素的識別碼。 任何專案都可以包含識別碼，以便在執行時間參考它。 我們將在示範如何在程式碼中載入 JSON 時，看到這種使用方式。

## <a name="loading-the-json-in-code"></a>在程式碼中載入 JSON

一旦定義 JSON 之後，我們必須將它載入至 MT。使用類別的 D `JsonElement` 。 假設我們在上面建立的 JSON 檔案已新增至名稱 sample.js的專案，並在指定內容的組建動作時，載入， `JsonElement` 就像呼叫下面這行程式碼一樣簡單：

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

因為我們會在每次建立工作時依需求新增此專案，所以我們可以依照下列方式修改先前的元素 API 範例中所按的按鈕：

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>在執行時間存取元素

回想一下，我們在 JSON 檔案中宣告時，已將識別碼新增至兩個元素。 我們可以在執行時間使用 id 屬性來存取每個專案，以在程式碼中修改其屬性。 例如，下列程式碼會參考 [專案] 和 [日期] 元素，以設定工作物件中的值：

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

MT.D 也支援從外部 Url 動態載入 JSON，只要將 Url 傳遞給的函式即可 `JsonElement` 。 MT.當您在畫面之間流覽時，D 會視需要展開 JSON 中宣告的階層。 例如，假設有一個 JSON 檔案，例如位於本機網頁伺服器根目錄的檔案：

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

我們可以使用將它載入， `JsonElement` 如下列程式碼所示：

```csharp
_rootElement = new RootElement ("Json Example") {
    new Section ("") {
        new JsonElement ("Load from url", "http://localhost/sample.json")
    }
};
```

在執行時間，檔案將會由 MT 抓取和剖析。D 當使用者流覽至第二個視圖時，如下列螢幕擷取畫面所示：

 [![檔案將由 MT 抓取和剖析。當使用者流覽至第二個視圖時的 D](json-element-walkthrough-images/04-json-web-example.png)](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>摘要

本文說明如何使用 MT 建立 using 介面。D。 它會示範如何使用應用程式以及從遠端 Url 載入包含在檔案中的 JSON。 文中也會示範如何在執行時間存取 JSON 中所述的元素。

## <a name="related-links"></a>相關連結

- [MTDJsonDemo (範例) ](/samples/xamarin/ios-samples/mtdjsondemo)
- [Monotouch.dll 簡介。對話方塊](~/ios/user-interface/monotouch.dialog/index.md)
- [Elements API 逐步解說](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [反映 API 逐步解說](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Github 上的 Monotouch.dll 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 應用程式](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 類別參考](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
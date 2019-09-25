---
title: Xamarin. Forms Web 服務簡介
description: 本指南提供示範如何與不同 web 服務通訊的 Xamarin 範例應用程式的逐步解說。 雖然每個 web 服務使用個別的範例應用程式，其功能上很類似，並共用通用類別。
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: bbeab6a6ab0d4a9d0e3a962240317fc0d54f9e25
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "68656647"
---
# <a name="xamarinforms-web-services-introduction"></a>Xamarin. Forms Web 服務簡介

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_本主題提供逐步解說示範如何與不同的 web 服務通訊的 Xamarin.Forms 範例應用程式。雖然每個 web 服務使用個別的範例應用程式，其功能上很類似，並共用通用類別。_

如下所述的範例待辦事項清單應用程式用來示範如何存取不同類型的 web 服務後端使用 Xamarin.Forms。 它提供下列功能：

- 檢視工作清單。
- 新增、 編輯和刪除工作。
- 將工作的狀態，設為 [完成]。
- 將工作的 [名稱] 和 [附註] 欄位。

在所有情況下，這些工作會儲存在後端中透過 web 服務存取。

啟動應用程式時，會顯示頁面，列出 web 服務，從擷取的任何工作，並可讓使用者建立新的工作。 在工作上按一下 瀏覽至第二個頁面，其中工作可以編輯、 儲存、 刪除和語音應用程式。 最終的應用程式如下所示：

![](introduction-images/app-example-1.png "待辦事項應用程式-第一頁")
![](introduction-images/app-example-2.png "待辦事項應用程式-第二個頁面")

本指南中的每個主題提供的下載連結*不同*示範特定類型的 web 服務後端的應用程式版本。 下載每個 web 服務樣式的相關網頁的相關的範例程式碼。

## <a name="understand-the-application-anatomy"></a>瞭解應用程式的剖析

每個範例應用程式的共用程式碼專案都包含三個主要資料夾：

|資料夾|用途|
|--- |--- |
|資料|包含類別和介面，可用來管理資料的項目，並與 web 服務進行通訊。 這包括最少`TodoItemManager`類別，其會透過屬性公開`App`類別以叫用 web 服務作業。|
|模型|包含資料模型類別，應用程式。 這包括最少`TodoItem`類別，建立模型資料的應用程式所使用的單一項目。 資料夾也可以包含任何其他的類別，用於模型的使用者資料。|
|檢視|包含應用程式的網頁。 這通常是組成`TodoListPage`和`TodoItemPage`類別，以及任何其他類別，用來進行驗證。|

每個應用程式的共用程式碼專案也包含一些重要的檔案：

|檔案|用途|
|--- |--- |
|Constants.cs|`Constants`指定應用程式用來與 web 服務通訊的任何常數的類別。 這些常數會需要更新以存取您個人的後端服務建立之提供者上。|
|ITextToSpeech.cs|`ITextToSpeech`介面，它會指定`Speak`必須由任何實作類別提供方法。|
|Todo.cs|`App`負責具現化兩個的第一頁會顯示每個平台上的應用程式的類別和`TodoItemManager`用來叫用 web 服務作業的類別。|

### <a name="view-pages"></a>View 頁面

大部分的範例應用程式中包含至少兩個頁面：

- **TodoListPage** – 此頁面會顯示一份`TodoItem`執行個體和勾號圖示如果`TodoItem.Done`屬性是`true`。 按一下某個項目會巡覽至`TodoItemPage`。 此外，建立新的項目上即可 *+* 符號。
- **TodoItemPage** – 此頁面會顯示所選的詳細資料`TodoItem`，並讓它可以編輯、 儲存、 刪除及語音。

此外，一些範例應用程式包含用來管理使用者的驗證程序的其他頁面。

### <a name="model-the-data"></a>建立資料模型

每個範例應用程式使用`TodoItem`類別來建立模型的資料，會顯示，並且傳送至儲存體的 web 服務。 下列程式碼範例顯示 `TodoItem` 類別：

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

`ID`屬性用來唯一識別每個`TodoItem`執行個體，並為每個 web 服務用來識別要更新或刪除的資料。

### <a name="invoke-web-service-operations"></a>叫用 web 服務作業

透過存取 web 服務作業`TodoItemManager`類別和類別的執行個體可以透過存取`App.TodoManager`屬性。 `TodoItemManager`類別提供下列方法來叫用 web 服務作業：

- **GetTasksAsync** – 此方法用來填入`ListView`控制`TodoListPage`使用`TodoItem`從 web 服務擷取執行個體。
- **SaveTaskAsync** – 此方法用來建立或更新`TodoItem`web 服務的執行個體。
- **DeleteTaskAsync** – 此方法用來刪除`TodoItem`web 服務的執行個體。

此外，一些範例應用程式包含額外的方法，在`TodoItemManager`類別，用來管理使用者的驗證程序。

而不是直接叫用 web 服務作業`TodoItemManager`方法叫用會插入之相依類別方法`TodoItemManager`建構函式。 例如，一個範例應用程式會插入`RestService`類別到`TodoItemManager`建構函式，以提供使用 REST Api 來存取資料的實作。

## <a name="related-links"></a>相關連結

- [.ASMX （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

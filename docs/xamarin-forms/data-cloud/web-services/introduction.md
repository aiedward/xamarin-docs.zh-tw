---
title: Xamarin. Forms Web 服務簡介
description: 本指南提供示範如何與不同 web 服務通訊的 Xamarin 範例應用程式的逐步解說。 雖然每個 web 服務都使用個別的範例應用程式，但它們的功能類似，而且共用一般類別。
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: bbeab6a6ab0d4a9d0e3a962240317fc0d54f9e25
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656647"
---
# <a name="xamarinforms-web-services-introduction"></a>Xamarin. Forms Web 服務簡介

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_本主題提供 Xamarin 範例應用程式的逐步解說，以示範如何與不同的 web 服務通訊。雖然每個 web 服務都使用個別的範例應用程式，但它們的功能類似，而且共用一般類別。_

下面所述的範例待辦事項清單應用程式，是用來示範如何以 Xamarin 來存取不同類型的 web 服務後端。 它提供下列功能：

- 查看工作清單。
- 新增、編輯和刪除工作。
- 將工作的狀態設定為「完成」。
- 朗讀工作的 [名稱] 和 [附注] 欄位。

在所有情況下，這些工作會儲存在透過 web 服務存取的後端中。

當應用程式啟動時，會顯示一個頁面，列出從 web 服務取得的任何工作，並允許使用者建立新的工作。 按一下工作會將應用程式流覽至第二頁，讓您可以編輯、儲存、刪除和讀出工作。 最終的應用程式如下所示：

![](introduction-images/app-example-1.png "Todo application - first page")
![](introduction-images/app-example-2.png "Todo application - second page")

本指南中的每個主題都會提供*另*一個應用程式版本的下載連結，以示範特定類型的 web 服務後端。 在與每個 web 服務樣式相關的頁面上下載相關的範例程式碼。

## <a name="understand-the-application-anatomy"></a>瞭解應用程式的剖析

每個範例應用程式的共用程式碼專案都包含三個主要資料夾：

|資料夾|用途|
|--- |--- |
|資料|包含用來管理資料項目和與 web 服務通訊的類別和介面。 其中至少會包含 `TodoItemManager` 類別，這會透過 `App` 類別中的屬性公開，以叫用 web 服務作業。|
|模型|包含應用程式的資料模型類別。 這至少包含 `TodoItem` 類別，這會為應用程式所使用的單一資料項目目建立模型。 資料夾也可以包含用來建立使用者資料模型的任何其他類別。|
|檢視|包含應用程式的頁面。 這通常是由 `TodoListPage` 和 `TodoItemPage` 類別，以及用於驗證的任何其他類別所組成。|

每個應用程式的共用程式碼專案也包含一些重要的檔案：

|檔案|用途|
|--- |--- |
|Constants.cs|@No__t_0 類別，指定應用程式用來與 web 服務進行通訊的任何常數。 這些常數需要更新，才能存取在提供者上建立的個人後端服務。|
|ITextToSpeech.cs|@No__t_0 介面，指定 `Speak` 方法必須由任何實作為類別所提供。|
|Todo.cs|負責具現化每個平臺上應用程式將顯示之第一頁的 `App` 類別，以及用來叫用 web 服務作業的 `TodoItemManager` 類別。|

### <a name="view-pages"></a>View 頁面

大部分的範例應用程式都包含至少兩個頁面：

- **TodoListPage** –此頁面會顯示 `TodoItem` 實例清單，以及 `TodoItem.Done` 屬性 `true` 時的刻度圖示。 按一下專案會流覽至 [`TodoItemPage`]。 此外，您可以按一下 [ *+* ] 符號來建立新的專案。
- **TodoItemPage** –此頁面會顯示所選 `TodoItem` 的詳細資料，並可供編輯、儲存、刪除和讀出。

此外，某些範例應用程式包含用來管理使用者驗證進程的其他頁面。

### <a name="model-the-data"></a>建立資料模型

每個範例應用程式都會使用 `TodoItem` 類別來建立顯示的資料模型，並將其傳送至 web 服務以供儲存。 下列程式碼範例顯示 `TodoItem` 類別：

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

@No__t_0 屬性是用來唯一識別每個 `TodoItem` 實例，而每個 web 服務會使用此屬性來識別要更新或刪除的資料。

### <a name="invoke-web-service-operations"></a>叫用 web 服務作業

Web 服務作業是透過 `TodoItemManager` 類別來存取，而且可以透過 `App.TodoManager` 屬性來存取類別的實例。 @No__t_0 類別提供下列方法來叫用 web 服務作業：

- **GetTasksAsync** –這個方法是用來在 `TodoListPage` 上填入 `ListView` 控制項，其中包含從 web 服務取得的 `TodoItem` 實例。
- **SaveTaskAsync** –這個方法用來建立或更新 web 服務上的 `TodoItem` 實例。
- **DeleteTaskAsync** –這個方法用來刪除 web 服務上的 `TodoItem` 實例。

此外，某些範例應用程式包含 `TodoItemManager` 類別中的其他方法，可用來管理使用者驗證程式。

@No__t_0 方法不會直接叫用 web 服務作業，而是會在插入 `TodoItemManager` 的函式的相依類別上叫用方法。 例如，其中一個範例應用程式會將 `RestService` 類別插入 `TodoItemManager` 的函式，以提供使用 REST Api 來存取資料的執行。

## <a name="related-links"></a>相關連結

- [.ASMX （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

---
title: Xamarin.Forms Web 服務簡介
description: 本指南提供 Xamarin.Forms 範例應用程式的逐步解說，示範如何與不同的 web 服務進行通訊。 雖然每個 web 服務都使用不同的範例應用程式，但它們的功能類似，而且共用一般類別。
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ebff259519776718f41ab0fcc0a7abb0c692dac6
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563415"
---
# <a name="no-locxamarinforms-web-services-introduction"></a>Xamarin.Forms Web 服務簡介

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_本主題提供 Xamarin.Forms 範例應用程式的逐步解說，示範如何與不同的 web 服務進行通訊。雖然每個 web 服務都使用不同的範例應用程式，但它們的功能類似，而且共用一般類別。_

以下所述的「待辦事項清單」應用程式是用來示範如何使用後端來存取不同類型的 web 服務 Xamarin.Forms 。 它提供下列功能：

- 查看工作的清單。
- 新增、編輯和刪除工作。
- 將工作的狀態設定為 [完成]。
- 說出工作的 [名稱] 和 [附注] 欄位。

在所有情況下，工作都會儲存在透過 web 服務存取的後端。

當應用程式啟動時，會顯示一個頁面，列出從 web 服務取出的任何工作，並允許使用者建立新的工作。 按一下工作可將應用程式流覽至第二頁，讓您可以編輯、儲存、刪除和讀出工作。 最終的應用程式如下所示：

![Todo 應用程式-第一個頁面 ](introduction-images/app-example-1.png)
 ![ 待辦事項應用程式第二頁](introduction-images/app-example-2.png)

本指南中的每個主題都會提供 *另* 一版應用程式的下載連結，以示範特定類型的 web 服務後端。 在與每個 web 服務樣式相關的頁面上，下載相關的範例程式碼。

## <a name="understand-the-application-anatomy"></a>瞭解應用程式剖析

每個範例應用程式的共用程式碼專案都是由三個主要資料夾組成：

|資料夾|目的|
|--- |--- |
|資料|包含用來管理資料項目和與 web 服務通訊的類別和介面。 其中至少包含 `TodoItemManager` 類別，這個類別是透過類別中的屬性公開，以叫用 `App` web 服務作業。|
|模型|包含應用程式的資料模型類別。 其中至少包含 `TodoItem` 類別，此類別會建立應用程式所使用的單一資料項目目模型。 資料夾也可以包含用來建立使用者資料模型的任何其他類別。|
|檢視|包含應用程式的頁面。 這通常是由 `TodoListPage` 和 `TodoItemPage` 類別，以及用於驗證的任何其他類別所組成。|

每個應用程式的共用程式碼專案也包含許多重要的檔案：

|檔案|目的|
|--- |--- |
|Constants.cs|`Constants`類別，指定應用程式用來與 web 服務進行通訊的任何常數。 這些常數需要更新以存取您在提供者上建立的個人後端服務。|
|ITextToSpeech.cs|`ITextToSpeech`介面，指定 `Speak` 方法必須由任何執行中的類別提供。|
|Todo.cs|此 `App` 類別負責具現化每個平臺上應用程式將顯示的第一個頁面，以及 `TodoItemManager` 用來叫用 web 服務作業的類別。|

### <a name="view-pages"></a>視圖頁面

大部分的範例應用程式都包含至少兩個頁面：

- **TodoListPage** –此頁面會顯示實例的清單 `TodoItem` ，而如果屬性為，則會顯示刻度圖示 `TodoItem.Done` `true` 。 按一下專案，即可流覽至 `TodoItemPage` 。 此外，您可以按一下符號來建立新專案 *+* 。
- **TodoItemPage** –此頁面會顯示所選的詳細資料，並可讓您進行 `TodoItem` 編輯、儲存、刪除和讀出。

此外，某些範例應用程式包含用來管理使用者驗證程式的其他頁面。

### <a name="model-the-data"></a>將資料模型化

每個範例應用程式 `TodoItem` 都會使用類別來建立資料的模型，並將其傳送至 web 服務以供儲存。 下列程式碼範例顯示 `TodoItem` 類別：

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

`ID`屬性可用來唯一識別每個 `TodoItem` 實例，而且每個 web 服務會使用此屬性來識別要更新或刪除的資料。

### <a name="invoke-web-service-operations"></a>叫用 web 服務作業

Web 服務作業是透過類別來存取 `TodoItemManager` ，而且可以透過屬性來存取類別的實例 `App.TodoManager` 。 `TodoItemManager`類別提供下列方法來叫用 web 服務作業：

- **GetTasksAsync** –這個方法是用來在 `ListView` 上填入控制項， `TodoListPage` 以及 `TodoItem` 從 web 服務抓取的實例。
- **SaveTaskAsync** –此方法是用來建立或更新 `TodoItem` web 服務上的實例。
- **DeleteTaskAsync** –此方法用來刪除 `TodoItem` web 服務上的實例。

此外，某些範例應用程式包含類別中的其他方法 `TodoItemManager` ，可用來管理使用者驗證程式。

`TodoItemManager`方法會在插入至函式的相依類別上叫用方法，而不是直接叫用 web 服務作業 `TodoItemManager` 。 例如，一個範例應用程式會將類別插入至函式， `RestService` `TodoItemManager` 以提供使用 REST api 來存取資料的執行。

## <a name="related-links"></a>相關連結

- [.ASMX (範例) ](/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (範例) ](/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (範例) ](/samples/xamarin/xamarin-forms-samples/webservices-todorest)
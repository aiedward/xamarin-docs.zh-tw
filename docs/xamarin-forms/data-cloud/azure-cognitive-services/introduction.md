---
title: Xamarin.Forms和 Azure 認知服務簡介
description: 本文提供範例應用程式的簡介，示範如何叫用一些 Microsoft 認知服務 Api。
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7c2a63c40d1006c7d83c9dde871e17d4b194bdca
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84129710"
---
# <a name="xamarinforms-and-azure-cognitive-services-introduction"></a>Xamarin.Forms和 Azure 認知服務簡介

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft 認知服務是一組可供開發人員使用的 Api、Sdk 和服務，藉由新增臉部辨識、語音辨識和語言理解等功能，讓其應用程式更聰明。本文提供範例應用程式的簡介，示範如何叫用一些 Microsoft 認知服務 Api。_

## <a name="overview"></a>概觀

隨附的範例是待辦事項清單應用程式，可提供下列功能：

- 查看工作清單。
- 透過螢幕小鍵盤新增和編輯工作，或使用 Microsoft Speech API 來執行語音辨識。
- 使用 Bing 拼寫檢查 API 檢查工作的拼寫。 如需詳細資訊，請參閱[使用 Bing 拼寫檢查 API 的拼寫檢查](spell-check.md)。
- 使用翻譯工具 API，將工作從英文轉譯為德文。 如需詳細資訊，請參閱[使用翻譯工具 API 的文字翻譯](text-translation.md)。
- 刪除工作。
- 將工作的狀態設定為「完成」。
- 使用臉部 API 為應用程式評分表情辨識。 如需詳細資訊，請參閱[使用臉部 API 的表情](emotion-recognition.md)辨識。

> [!WARNING]
> Bing 語音 API 已取代為 Azure 語音服務。 如需 Azure 語音服務專用的範例，請參閱語音[識別與語音服務 API](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md)。

工作會儲存在本機 SQLite 資料庫中。 如需使用本機 SQLite 資料庫的詳細資訊，請參閱使用[本機資料庫](~/xamarin-forms/data-cloud/data/databases.md)。

`TodoListPage`當應用程式啟動時，就會顯示。 此頁面會顯示儲存在本機資料庫中的任何工作清單，並可讓使用者建立新的工作或為應用程式評分：

![](introduction-images/sample-application-1.png "TodoListPage")

您可以按一下 [] 按鈕來建立新專案 *+* ，以流覽至 `TodoItemPage` 。 藉由選取工作，也可以流覽至此頁面：

![](introduction-images/sample-application-2.png "TodoItemPage")

`TodoItemPage`允許建立、編輯、拼寫檢查、轉譯、儲存和刪除工作。 語音辨識可以用來建立或編輯工作。 這可以藉由按下 [麥克風] 按鈕開始錄製，然後按第二次按下相同的按鈕停止錄製（這會將記錄傳送至 Bing 語音辨識 API）來達成此目的。

按一下上的 [smilies] 按鈕 `TodoListPage` ，即可導覽至 `RateAppPage` ，其可用來對臉部運算式的影像執行表情辨識：

![](introduction-images/sample-application-3.png "RateAppPage")

`RateAppPage`可讓使用者拍攝臉部的相片，並將其提交至臉部 API，並顯示所傳回的表情。

## <a name="understand-the-application-anatomy"></a>瞭解應用程式的剖析

範例應用程式的共用程式碼專案包含五個主要資料夾：

|資料夾|目的|
|--- |--- |
|模型|包含應用程式的資料模型類別。 這包括 `TodoItem` 類別，它會建立應用程式所使用的單一資料項目目模型。 此資料夾也包含用來建立不同 Microsoft 認知服務 Api 所傳回之 JSON 回應模型的類別。|
|儲存機制|包含 `ITodoItemRepository` `TodoItemRepository` 用來執行資料庫作業的介面和類別。|
|服務|包含用來存取不同 Microsoft 認知服務 Api 的介面和類別，以及 `DependencyService` 類別用來尋找在平臺專案中執行介面之類別的介面。|
|Utils|包含 `Timer` 類別， `AuthenticationService` 類別用來每隔9分鐘更新 JWT 存取權杖。|
|檢視|包含應用程式的頁面。|

共用程式碼專案也包含一些重要的檔案：

|檔案|目的|
|--- |--- |
|Constants.cs|`Constants`類別，指定用來叫用的 Microsoft 認知服務 api 的 API 金鑰和端點。 API 金鑰常數需要進行更新，才能存取不同的認知服務 Api。|
|App.xaml.cs|`App`類別負責具現化每個平臺上的應用程式將顯示的第一頁，以及 `TodoManager` 用來叫用資料庫作業的類別。|

### <a name="nuget-packages"></a>NuGet 套件

範例應用程式會使用下列 NuGet 套件：

- `Newtonsoft.Json`-提供適用于 .NET 的 JSON 架構。
- `PCLStorage`-提供一組跨平臺本機檔案 IO Api。
- `sqlite-net-pcl`–提供 SQLite 資料庫儲存體。
- `Xam.Plugin.Media`-提供跨平臺相片拍攝和挑選 Api。

此外，這些 NuGet 套件也會安裝自己的相依性。

### <a name="model-the-data"></a>將資料模型化

範例應用程式會使用 `TodoItem` 類別，將顯示並儲存在本機 SQLite 資料庫中的資料建立模型。 下列程式碼範例顯示 `TodoItem` 類別：

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

`ID`屬性可用來唯一識別每個 `TodoItem` 實例，並以 SQLite 屬性裝飾，讓屬性成為資料庫中的自動遞增主鍵。

### <a name="invoke-database-operations"></a>叫用資料庫作業

`TodoItemRepository`類別會執行資料庫作業，而類別的實例則可透過 `App.TodoManager` 屬性存取。 `TodoItemRepository`類別提供下列方法來叫用資料庫作業：

- **GetAllItemsAsync** –從本機 SQLite 資料庫中取出所有專案。
- **GetItemAsync** –從本機 SQLite 資料庫中抓取指定的專案。
- **SaveItemAsync** –建立或更新本機 SQLite 資料庫中的專案。
- **DeleteItemAsync** –從本機 SQLite 資料庫中刪除指定的專案。

### <a name="platform-project-implementations"></a>平臺專案的實現

`Services`共用程式碼專案中的資料夾包含 `IFileHelper` 和 `IAudioRecorderService` 介面，類別會使用這些介面 `DependencyService` 來尋找在平臺專案中執行介面的類別。

`IFileHelper`介面是由 `FileHelper` 每個平臺專案中的類別所執行。 這個類別是由單一方法所組成，它會傳回 `GetLocalFilePath` 用來儲存 SQLite 資料庫的本機檔案路徑。

`IAudioRecorderService`介面是由 `AudioRecorderService` 每個平臺專案中的類別所執行。 這個類別是由 `StartRecording` 、 `StopRecording` 和支援的方法所組成，其使用平臺 api 從裝置的麥克風錄製音訊，並將其儲存為 wav 檔案。 在 iOS 上，會 `AudioRecorderService` 使用 `AVFoundation` API 來記錄音訊。 在 Android 上，會 `AudioRecordService` 使用 `AudioRecord` API 來記錄音訊。 在通用 Windows 平臺（UWP）上，會 `AudioRecorderService` 使用 `AudioGraph` API 來錄製音訊。

### <a name="invoke-cognitive-services"></a>叫用認知服務

範例應用程式會叫用下列 Microsoft 認知服務：

- Microsoft Speech API。 如需詳細資訊，請參閱[使用 Microsoft SPEECH API 的語音辨識](speech-recognition.md)。
- Bing 拼寫檢查 API。 如需詳細資訊，請參閱[使用 Bing 拼寫檢查 API 的拼寫檢查](spell-check.md)。
- 轉譯 API。 如需詳細資訊，請參閱[使用翻譯工具 API 的文字翻譯](text-translation.md)。
- 臉部 API。 如需詳細資訊，請參閱[使用臉部 API 的表情](emotion-recognition.md)辨識。

## <a name="related-links"></a>相關連結

- [語音辨識與語音服務 API](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md)
- [Microsoft 認知服務檔](https://www.microsoft.com/cognitive-services/documentation)
- [Todo 認知服務（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

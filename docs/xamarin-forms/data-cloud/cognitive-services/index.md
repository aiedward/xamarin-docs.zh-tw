---
title: 使用認知服務加入智慧
description: 本文將示範如何叫用一些 Microsoft 認知服務 Api 的範例應用程式的簡介。
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 0c09063b55a14f9f22feb91d2a6f9d3f9417ecee
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330842"
---
# <a name="adding-intelligence-with-cognitive-services"></a>使用認知服務加入智慧

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_Microsoft 認知服務是一組 Api、 Sdk 和適用於開發人員可以藉由新增功能，例如臉部辨識、 語音辨識和語言理解，讓他們的應用程式更有智慧服務。本文將示範如何叫用一些 Microsoft 認知服務 Api 的範例應用程式的簡介。_

## <a name="overview"></a>總覽

隨附的範例是待辦事項清單應用程式所提供的功能：

- 檢視工作清單。
- 新增和編輯透過螢幕小鍵盤，或藉由執行 Microsoft Speech API 的語音辨識的工作。 如需有關如何執行語音辨識的詳細資訊，請參閱[使用 Microsoft Speech API 的語音辨識](speech-recognition.md)。
- 拼字檢查工作使用 Bing 拼字檢查 API。 如需詳細資訊，請參閱 <<c0> [ 拼字檢查使用 Bing 拼字檢查 API](spell-check.md)。
- 轉譯為德文使用 Translator API 從英文工作。 如需詳細資訊，請參閱 <<c0> [ 使用 Translator API 的文字翻譯](text-translation.md)。
- 刪除工作。
- 將工作的狀態，設為 [完成]。
- 透過表情辨識，使用臉部 API 應用程式的評分。 如需詳細資訊，請參閱 <<c0> [ 表情辨識使用人臉識別 API](emotion-recognition.md)。

工作會儲存在本機 SQLite 資料庫。 如需使用本機 SQLite 資料庫的詳細資訊，請參閱 <<c0> [ 使用本機資料庫](~/xamarin-forms/app-fundamentals/databases.md)。

`TodoListPage`應用程式啟動時顯示。 此頁面會顯示一份儲存在本機資料庫中，任何工作，並允許使用者建立新的工作，或應用程式評分：

![](images/sample-application-1.png "TodoListPage")

先建立新的項目，請按一下 *+* 按鈕，巡覽至`TodoItemPage`。 此頁面也可以巡覽至選取的工作：

![](images/sample-application-2.png "TodoItemPage")

`TodoItemPage`轉譯、 儲存，並刪除，可讓工作建立、 編輯、 拼字檢查。 語音辨識可用來建立或編輯工作。 做法是藉由按下 [麥克風] 按鈕，開始錄製，並按下按鈕相同，第二次，若要停止錄製，其會將記錄傳送至 Bing 語音辨識 API。

按一下 [smilies] 按鈕`TodoListPage`瀏覽至`RateAppPage`，用以執行表情辨識臉部運算式映像上：

![](images/sample-application-3.png "RateAppPage")

`RateAppPage`可讓使用者拍照他們的臉部，並顯示傳回表情與提交給人臉識別 API。

## <a name="understanding-the-application-anatomy"></a>了解應用程式結構

範例應用程式的可攜式類別庫 (PCL) 專案包含五個主要資料夾：

|資料夾|用途|
|--- |--- |
|模型|包含資料模型類別，應用程式。 這包括`TodoItem`類別，建立模型資料的應用程式所使用的單一項目。 此資料夾也包含用來從不同的 Microsoft 認知服務 Api 所傳回的模型 JSON 回應的類別。|
|存放庫|包含`ITodoItemRepository`介面和`TodoItemRepository`類別，用來執行資料庫作業。|
|服務|包含介面和類別，用來存取不同 Microsoft 認知服務 Api，以及所使用的介面`DependencyService`類別，以找出在平台專案中實作之介面的類別。|
|公用程式|包含`Timer`類別，這將由`AuthenticationService`每隔 9 分鐘更新的 JWT 存取權杖的類別。|
|檢視|包含應用程式的網頁。|

PCL 專案也包含一些重要的檔案：

|檔案|用途|
|--- |--- |
|Constants.cs|`Constants`類別，適用於 Microsoft 認知服務 Api 叫用指定的 API 金鑰和端點。 API 金鑰常數需要更新存取不同的認知服務 Api。|
|App.xaml.cs|`App`類別負責具現化兩個的第一頁會顯示每個平台上的應用程式和`TodoManager`用來叫用的資料庫作業的類別。|

### <a name="nuget-packages"></a>NuGet 封裝

範例應用程式會使用下列的 NuGet 套件：

- `Newtonsoft.Json` – 提供適用於.NET 的 JSON 架構。
- `PCLStorage` -提供一組的跨平台本機檔案 IO Api。
- `sqlite-net-pcl` – 提供 SQLite 資料庫儲存體。
- `Xam.Plugin.Media` – 提供跨平台相片採用，並挑選 Api。

此外，這些 NuGet 套件也會安裝它們自己的相依性。

### <a name="modeling-the-data"></a>將資料模型化

範例應用程式使用`TodoItem`類別來建立模型的資料，會顯示並儲存在本機 SQLite 資料庫。 下列程式碼範例顯示 `TodoItem` 類別：

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

`ID`屬性用來唯一識別每個`TodoItem`執行個體，並將屬性設為自動遞增的主索引鍵在資料庫中的 SQLite 屬性裝飾。

### <a name="invoking-database-operations"></a>叫用的資料庫作業

`TodoItemRepository`類別會實作資料庫作業，而您可以透過類別的執行個體`App.TodoManager`屬性。 `TodoItemRepository`類別提供下列方法來叫用的資料庫作業：

- **GetAllItemsAsync** – 從本機 SQLite 資料庫中擷取所有項目。
- **GetItemAsync** – 從本機 SQLite 資料庫中擷取指定的項目。
- **SaveItemAsync** – 建立或更新本機 SQLite 資料庫中的項目。
- **DeleteItemAsync** – 從本機 SQLite 資料庫中刪除指定的項目。

### <a name="platform-project-implementations"></a>平台專案的實作

`Services` PCL 專案的資料夾中包含`IFileHelper`並`IAudioRecorderService`介面，可供`DependencyService`類別，以找出在平台專案中實作之介面的類別。

`IFileHelper`介面由實作`FileHelper`每個平台專案中的類別。 此類別包含單一方法`GetLocalFilePath`，它會傳回本機檔案路徑來儲存 SQLite 資料庫。

`IAudioRecorderService`介面由實作`AudioRecorderService`每個平台專案中的類別。 此類別包含`StartRecording`， `StopRecording`，和支援的方法，可以使用平台 Api 從裝置的麥克風錄製音訊，並將它儲存為 wav 檔案。 在 iOS 上，`AudioRecorderService`使用`AVFoundation`錄製音訊的 API。 在 Android 上，`AudioRecordService`使用`AudioRecord`錄製音訊的 API。 在通用 Windows 平台 (UWP)，`AudioRecorderService`使用`AudioGraph`錄製音訊的 API。

### <a name="invoking-cognitive-services"></a>叫用的認知服務

範例應用程式會叫用下列的 Microsoft 認知服務：

- Microsoft Speech API。 如需詳細資訊，請參閱 <<c0> [ 使用 Microsoft Speech API 的語音辨識](speech-recognition.md)。
- Bing 拼字檢查 API。 如需詳細資訊，請參閱 <<c0> [ 拼字檢查使用 Bing 拼字檢查 API](spell-check.md)。
- 轉換 API。 如需詳細資訊，請參閱 <<c0> [ 使用 Translator API 的文字翻譯](text-translation.md)。
- 人臉識別 API。 如需詳細資訊，請參閱 <<c0> [ 表情辨識使用人臉識別 API](emotion-recognition.md)。

## <a name="related-links"></a>相關連結

- [Microsoft 認知服務文件](https://www.microsoft.com/cognitive-services/documentation)
- [待辦事項 Cognitive Services （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

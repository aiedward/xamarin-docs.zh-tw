---
title: "使用認知的服務加入智慧"
description: "Microsoft 認知服務是一組應用程式開發介面、 Sdk 和藉由新增功能，例如臉部辨識、 語音辨識和語言的了解，使其應用程式更聰明的開發人員可使用的服務。 本文章提供範例應用程式，示範如何叫用一些 Microsoft 認知服務 Api 的簡介。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: c309fb6936296dc181e499c91770ab8891121e9c
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="adding-intelligence-with-cognitive-services"></a>使用認知的服務加入智慧

_Microsoft 認知服務是一組應用程式開發介面、 Sdk 和藉由新增功能，例如臉部辨識、 語音辨識和語言的了解，使其應用程式更聰明的開發人員可使用的服務。本文章提供範例應用程式，示範如何叫用一些 Microsoft 認知服務 Api 的簡介。_

## <a name="overview"></a>總覽

隨附的範例是 todo 清單應用程式提供功能給：

- 檢視工作的清單。
- 新增和編輯工作透過螢幕小鍵盤，或是執行與 Bing 語音 API 的語音辨識。 如需執行語音辨識的詳細資訊，請參閱[語音辨識使用 Bing 語音 API](speech-recognition.md)。
- 拼字檢查工作使用 Bing 拼字檢查 API。 如需詳細資訊，請參閱[拼字檢查使用 Bing 拼字檢查 API](spell-check.md)。
- 轉譯為 「 德文使用轉譯器 API 從英文工作。 如需詳細資訊，請參閱[文字轉譯使用轉譯器 API](text-translation.md)。
- 刪除工作。
- 設定工作的狀態 設定為 完成。
- 率情緒辨識，使用情緒 API 的應用程式。 如需詳細資訊，請參閱[情緒辨識使用情緒 API](emotion-recognition.md)。

工作會儲存在本機的 SQLite 資料庫。 如需使用本機的 SQLite 資料庫的詳細資訊，請參閱[使用本機資料庫](~/xamarin-forms/app-fundamentals/databases.md)。

`TodoListPage`應用程式啟動時顯示。 此頁面會顯示儲存在本機資料庫中，任何工作的清單，並可讓使用者建立新的工作或評等應用程式：

![](images/sample-application-1.png "TodoListPage")

您可以建立新的項目上按一下 *+* 按鈕，巡覽至`TodoItemPage`。 此頁面也可以巡覽至所選取工作：

![](images/sample-application-2.png "TodoItemPage")

`TodoItemPage`允許工作建立、 編輯、 拼字檢查，翻譯、 儲存，以及刪除。 語音辨識可用來建立或編輯工作。 這被達成藉由按下 [麥克風] 按鈕來開始記錄，並按下按鈕相同，第二次，若要停止錄製，這將記錄傳送至 Bing 語音辨識 API。

按一下 smilies 按鈕`TodoListPage`瀏覽至`RateAppPage`，用來在臉部運算式的映像上執行情緒辨識：

![](images/sample-application-3.png "RateAppPage")

`RateAppPage`可讓使用者為其表面，提交給情緒 API，以傳回要顯示的表情。

## <a name="understanding-the-application-anatomy"></a>了解應用程式結構

範例應用程式的可攜式類別程式庫 (PCL) 專案包含五個主要的資料夾：

|資料夾|用途|
|--- |--- |
|模型|包含應用程式的資料模型類別。 這包括`TodoItem`類別，為模型使用的應用程式資料的單一項目。 此資料夾也包含用來從不同的 Microsoft 認知服務 Api 傳回的模型 JSON 回應的類別。|
|儲存機制|包含`ITodoItemRepository`介面和`TodoItemRepository`用來執行資料庫作業的類別。|
|服務|包含介面和類別，用來存取不同 Microsoft 認知服務 Api，以及使用的介面`DependencyService`類別，以找出在平台專案中實作介面的類別。|
|公用程式|包含`Timer`類別，由`AuthenticationService`類別，以更新 JWT 存取權杖每隔 9 分鐘。|
|檢視|包含應用程式的網頁。|

PCL 專案也包含一些重要的檔案：

|檔案|用途|
|--- |--- |
|Constants.cs|`Constants`類別，適用於 Microsoft 認知服務 Api 叫用指定的 API 金鑰和端點。 API 金鑰常數需要更新以存取不同的認知服務 Api。|
|App.xaml.cs|`App`類別會負責這兩個的第一頁會顯示每個平台上的應用程式具現化和`TodoManager`用來叫用的資料庫作業的類別。|

### <a name="nuget-packages"></a>NuGet 封裝

範例應用程式會使用下列 NuGet 套件：

- `Microsoft.Net.Http` – 提供`HttpClient`類別進行透過 HTTP 要求。
- `Newtonsoft.Json` – 提供 JSON framework for.NET。
- `Microsoft.ProjectOxford.Emotion` – 存取情緒 API 的用戶端程式庫。
- `PCLStorage` -提供一組的跨平台的本機檔案 IO 的應用程式開發介面。
- `sqlite-net-pcl` – 提供 SQLite 資料庫儲存體。
- `Xam.Plugin.Media` – 提供跨平台相片函式和挑選應用程式開發介面。

此外，這些 NuGet 封裝也會安裝它們自己的相依性。

### <a name="modeling-the-data"></a>將資料模型化

範例應用程式使用`TodoItem`類別來建立模型的資料，會顯示並儲存在本機的 SQLite 資料庫。 下列程式碼範例顯示 `TodoItem` 類別：

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

`ID`屬性用來唯一識別每個`TodoItem`執行個體，並以將屬性設為自動遞增主索引鍵在資料庫中的 SQLite 屬性裝飾。

### <a name="invoking-database-operations"></a>叫用的資料庫作業

`TodoItemRepository`類別實作資料庫作業，而且可以透過存取類別的執行個體`App.TodoManager`屬性。 `TodoItemRepository`類別會提供下列方法來叫用的資料庫作業：

- **GetAllItemsAsync** – 擷取所有的項目從本機 SQLite 資料庫。
- **GetItemAsync** – 從本機 SQLite 資料庫中擷取指定的項目。
- **SaveItemAsync** – 建立或更新本機 SQLite 資料庫中的項目。
- **DeleteItemAsync** – 從本機 SQLite 資料庫中刪除指定的項目。

### <a name="platform-project-implementations"></a>平台專案實作

`Services` PCL 專案中的資料夾包含`IFileHelper`和`IAudioRecorderService`介面所使用的`DependencyService`類別，以找出在平台專案中實作介面的類別。

`IFileHelper`介面由實作`FileHelper`每個平台專案中的類別。 此類別包含單一方法`GetLocalFilePath`，它會傳回用於儲存 SQLite 資料庫的本機檔案路徑。

`IAudioRecorderService`介面由實作`AudioRecorderService`每個平台專案中的類別。 此類別包含`StartRecording`， `StopRecording`，和支援的方法，使用裝置的麥克風錄製音訊，平台應用程式開發介面，並將它儲存為 wav 檔案。 在 iOS、`AudioRecorderService`使用`AVFoundation`錄製音訊的 API。 在 Android 上，`AudioRecordService`使用`AudioRecord`錄製音訊的 API。 在通用 Windows 平台 (UWP)，`AudioRecorderService`使用`AudioGraph`錄製音訊的 API。

### <a name="invoking-cognitive-services"></a>叫用認知的服務

範例應用程式會叫用下列 Microsoft 認知服務：

- Bing 語音 API。 如需詳細資訊，請參閱[語音辨識使用 Bing 語音 API](speech-recognition.md)。
- Bing 拼字檢查應用程式開發介面。 如需詳細資訊，請參閱[拼字檢查使用 Bing 拼字檢查 API](spell-check.md)。
- 轉換 API。 如需詳細資訊，請參閱[文字轉譯使用轉譯器 API](text-translation.md)。
- 情緒應用程式開發介面。 如需詳細資訊，請參閱[情緒辨識使用情緒 API](emotion-recognition.md)。


## <a name="related-links"></a>相關連結

- [Microsoft 認知的服務文件](https://www.microsoft.com/cognitive-services/documentation)
- [Todo 認知服務 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

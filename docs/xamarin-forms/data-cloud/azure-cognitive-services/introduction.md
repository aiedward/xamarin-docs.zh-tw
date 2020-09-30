---
title: Xamarin.Forms 和 Azure 認知服務簡介
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
ms.openlocfilehash: f40220a29611bb365dc77eeb5e85445a0df19fd6
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563246"
---
# <a name="no-locxamarinforms-and-azure-cognitive-services-introduction"></a>Xamarin.Forms 和 Azure 認知服務簡介

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft 認知服務是一組 Api、Sdk 和服務，可供開發人員使用，藉由新增臉部辨識、語音辨識和語言理解等功能，讓其應用程式更具智慧。本文提供範例應用程式的簡介，示範如何叫用一些 Microsoft 認知服務 Api。_

## <a name="overview"></a>概觀

隨附的範例是待辦事項清單應用程式，可提供下列功能：

- 查看工作的清單。
- 透過軟鍵盤新增和編輯工作，或使用 Microsoft 語音 API 來執行語音辨識。
- 使用 Bing 拼寫檢查 API 來拼寫檢查工作。 如需詳細資訊，請參閱 [使用 Bing 拼寫檢查 API 的拼寫檢查](spell-check.md)。
- 使用 Translator API 將工作從英文翻譯為德文。 如需詳細資訊，請參閱 [使用 TRANSLATOR API 的文字翻譯](text-translation.md)。
- 刪除工作。
- 將工作的狀態設定為 [完成]。
- 使用臉部 API，以表情辨識來為應用程式評分。 如需詳細資訊，請參閱 [使用臉部 API 的表情](emotion-recognition.md)辨識。

> [!WARNING]
> Bing 語音 API 已被取代為 Azure 語音服務。 如需 Azure 語音服務專屬的範例，請參閱語音 [服務 API 的語音辨識](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md)。

工作會儲存在本機 SQLite 資料庫中。 如需使用本機 SQLite 資料庫的詳細資訊，請參閱使用 [本機資料庫](~/xamarin-forms/data-cloud/data/databases.md)。

`TodoListPage`當應用程式啟動時，就會顯示。 此頁面會顯示儲存在本機資料庫中之任何工作的清單，並可讓使用者建立新的工作或為應用程式評分：

![TodoListPage](introduction-images/sample-application-1.png)

您可以按一下 *+* 流覽至的按鈕來建立新專案 `TodoItemPage` 。 您也可以選取工作來流覽此頁面：

![TodoItemPage](introduction-images/sample-application-2.png)

`TodoItemPage`允許建立、編輯、拼寫檢查、轉譯、儲存和刪除工作。 您可以使用「語音辨識」來建立或編輯工作。 這是藉由按下麥克風按鈕來開始錄製，然後按下相同的按鈕來停止錄製，以將記錄傳送至 Bing 語音辨識 API。

按一下中的 [smilies] 按鈕，即可在臉部 `TodoListPage` `RateAppPage` 運算式的影像上，用來執行表情辨識：

![RateAppPage](introduction-images/sample-application-3.png)

`RateAppPage`可讓使用者拍攝臉部的相片，並將其提交至臉部 API，並顯示所傳回的表情。

## <a name="understand-the-application-anatomy"></a>瞭解應用程式剖析

範例應用程式的共用程式碼專案是由五個主要資料夾組成：

|資料夾|目的|
|--- |--- |
|模型|包含應用程式的資料模型類別。 這包括 `TodoItem` 類別，其會建立應用程式所使用的單一資料項目目模型。 此資料夾也包含類別，用來建立從不同 Microsoft 認知服務 Api 傳回的 JSON 回應模型。|
|儲存機制|包含 `ITodoItemRepository` `TodoItemRepository` 用來執行資料庫作業的介面和類別。|
|服務|包含用來存取不同 Microsoft 認知服務 Api 的介面和類別，以及 `DependencyService` 類別用來在平臺專案中找出執行介面之類別的介面。|
|公用程式|包含 `Timer` 類別，類別會使用這個類別 `AuthenticationService` 每隔9分鐘更新一次 JWT 存取權杖。|
|檢視|包含應用程式的頁面。|

共用程式碼專案也包含一些重要的檔案：

|檔案|目的|
|--- |--- |
|Constants.cs|類別，指定所叫用之 `Constants` Microsoft 認知服務 api 的 API 金鑰和端點。 API 金鑰常數需要更新以存取不同的認知服務 Api。|
|App.xaml.cs|`App`類別負責具現化每個平臺上應用程式將顯示的第一個頁面，以及 `TodoManager` 用來叫用資料庫作業的類別。|

### <a name="nuget-packages"></a>NuGet 套件

範例應用程式會使用下列 NuGet 套件：

- `Newtonsoft.Json` –提供適用于 .NET 的 JSON 架構。
- `PCLStorage` –提供一組跨平臺本機檔案 IO Api。
- `sqlite-net-pcl` –提供 SQLite 資料庫儲存體。
- `Xam.Plugin.Media` –提供跨平臺相片拍攝和挑選 Api。

此外，這些 NuGet 套件也會安裝自己的相依性。

### <a name="model-the-data"></a>將資料模型化

範例應用程式會使用 `TodoItem` 類別來建立顯示並儲存在本機 SQLite 資料庫中的資料模型。 下列程式碼範例顯示 `TodoItem` 類別：

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

`ID`屬性可用來唯一識別每個 `TodoItem` 實例，並以 SQLite 屬性裝飾，讓屬性成為資料庫中自動遞增的主鍵。

### <a name="invoke-database-operations"></a>叫用資料庫作業

`TodoItemRepository`類別會執行資料庫作業，而類別的實例可透過屬性來存取 `App.TodoManager` 。 `TodoItemRepository`類別提供下列方法來叫用資料庫作業：

- **GetAllItemsAsync** –抓取本機 SQLite 資料庫中的所有專案。
- **GetItemAsync** –從本機 SQLite 資料庫抓取指定的專案。
- **SaveItemAsync** –在本機 SQLite 資料庫中建立或更新專案。
- **DeleteItemAsync** –從本機 SQLite 資料庫中刪除指定的專案。

### <a name="platform-project-implementations"></a>平臺專案實現

`Services`共用程式碼專案中的資料夾包含 `IFileHelper` 和 `IAudioRecorderService` 介面，這些類別會用 `DependencyService` 來尋找在平臺專案中執行介面的類別。

`IFileHelper`介面是由 `FileHelper` 每個平臺專案中的類別所執行。 這個類別是由單一方法所組成，它會傳回 `GetLocalFilePath` 儲存 SQLite 資料庫的本機檔案路徑。

`IAudioRecorderService`介面是由 `AudioRecorderService` 每個平臺專案中的類別所執行。 這個類別包含 `StartRecording` 、 `StopRecording` 和支援的方法，這些方法使用平臺 api 來記錄裝置麥克風的音訊，並將其儲存為 wav 檔案。 在 iOS 上，會 `AudioRecorderService` 使用 `AVFoundation` API 來錄製音訊。 在 Android 上，會 `AudioRecordService` 使用 `AudioRecord` API 來錄製音訊。 在通用 Windows 平臺 (UWP) 上，會 `AudioRecorderService` 使用 `AudioGraph` API 來錄製音訊。

### <a name="invoke-cognitive-services"></a>叫用認知服務

範例應用程式會叫用下列 Microsoft 認知服務：

- Microsoft 語音 API。 如需詳細資訊，請參閱 [使用 Microsoft SPEECH API 的語音辨識](speech-recognition.md)。
- Bing 拼寫檢查 API。 如需詳細資訊，請參閱 [使用 Bing 拼寫檢查 API 的拼寫檢查](spell-check.md)。
- 轉譯 API。 如需詳細資訊，請參閱 [使用 TRANSLATOR API 的文字翻譯](text-translation.md)。
- 臉部 API。 如需詳細資訊，請參閱 [使用臉部 API 的表情](emotion-recognition.md)辨識。

## <a name="related-links"></a>相關連結

- [使用語音服務 API 進行語音辨識](~/xamarin-forms/data-cloud/azure-cognitive-services/speech-recognition.md)
- [Microsoft 認知服務檔](https://www.microsoft.com/cognitive-services/documentation)
- [Todo 認知服務 (範例) ](/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
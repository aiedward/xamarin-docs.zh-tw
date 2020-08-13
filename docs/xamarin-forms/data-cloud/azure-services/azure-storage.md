---
title: 儲存和存取 Azure 儲存體中的資料 Xamarin.Forms
description: Azure 儲存體是可擴充的雲端儲存體解決方案，可用於儲存非結構化和結構化資料。 本文說明如何使用，將 Xamarin.Forms 文字和二進位資料儲存在 Azure 儲存體中，以及如何存取資料。
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d7b97f21e5184a445cfac85dc06a7da0e1a6a4c5
ms.sourcegitcommit: f7fe46c0236a7130b63a33d9d1670d5111582dd2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186209"
---
# <a name="store-and-access-data-in-azure-storage-from-no-locxamarinforms"></a>儲存和存取 Azure 儲存體中的資料 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)

_Azure 儲存體是可擴充的雲端儲存體解決方案，可用於儲存非結構化和結構化資料。本文示範如何使用，將 Xamarin.Forms 文字和二進位資料儲存在 Azure 儲存體中，以及如何存取資料。_

Azure 儲存體提供四種儲存體服務：

- Blob 儲存體。 Blob 可以是文字或二進位資料，例如備份、虛擬機器、媒體檔案或檔。
- 表格儲存體是 NoSQL 索引鍵-屬性存放區。
- 佇列儲存體是一種訊息服務，可處理工作流程和雲端服務之間的通訊。
- 檔案儲存體會使用 SMB 通訊協定來提供共用存放裝置。

儲存體帳戶分為兩種類型：

- 一般用途的儲存體帳戶可讓您從單一帳戶存取 Azure 儲存體服務。
- Blob 儲存體帳戶是專門用來儲存 blob 的儲存體帳戶。 當您只需要儲存 blob 資料時，建議使用此帳戶類型。

本文和隨附的範例應用程式會示範如何將影像和文字檔上傳至 blob 儲存體，並下載這些檔案。 此外，它也會示範如何從 blob 儲存體中抓取檔案清單，以及如何刪除檔案。

如需 Azure 儲存體的詳細資訊，請參閱 [儲存體簡介](https://azure.microsoft.com/documentation/articles/storage-introduction/)。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

## <a name="introduction-to-blob-storage"></a>Blob 儲存體簡介

Blob 儲存體是由三個元件所組成，如下圖所示：

![Blob 儲存體概念](azure-storage-images/blob-storage.png)

Azure 儲存體的所有存取都是透過儲存體帳戶。 儲存體帳戶可以包含無限數量的容器，而容器可以儲存不限數目的 blob，最高可達儲存體帳戶的容量限制。

Blob 是任何類型和大小的檔案。 Azure 儲存體支援三種不同的 blob 類型：

- 區塊 blob 已針對串流和儲存雲端物件進行優化，而且是儲存備份、媒體檔案、檔等的理想選擇。區塊 blob 的大小最高可 195 Gb。
- 附加 blob 類似于區塊 blob，但已針對附加作業（例如記錄）進行優化。 附加 blob 的大小最多可 195 Gb。
- 分頁 blob 已針對頻繁的讀取/寫入作業進行優化，通常用於儲存虛擬機器及其磁片。 分頁 blob 的大小上限為1Tb。

> [!NOTE]
> 請注意，blob 儲存體帳戶支援區塊和附加 blob，而非分頁 blob。

Blob 會上傳至 Azure 儲存體，並以位元組資料流程的形式從 Azure 儲存體下載。 因此，檔案必須先轉換成位元組資料流程，然後再上傳，並在下載後轉換回其原始表示。

儲存在 Azure 儲存體中的每個物件都有唯一的 URL 位址。 儲存體帳戶名稱會形成該位址的子域，而子域和功能變數名稱的組合會形成儲存體帳戶的 *端點* 。 例如，如果您的儲存體帳戶名為 *mystorageaccount*，則儲存體帳戶的預設 blob 端點會是 `https://mystorageaccount.blob.core.windows.net` 。

用以存取儲存體帳戶中某物件的 URL，可藉由在端點後附加該物件在儲存體帳戶中的位置來建置。 例如，blob 位址的格式會是 `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob` 。

## <a name="setup"></a>安裝程式

將 Azure 儲存體帳戶整合到應用程式的流程如下所示 Xamarin.Forms ：

1. 建立儲存體帳戶。 如需詳細資訊，請參閱[建立儲存體帳戶](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account)。
1. 將 [Azure 儲存體用戶端程式庫](https://www.nuget.org/packages/WindowsAzure.Storage/) 新增至 Xamarin.Forms 應用程式。
1. 設定儲存體連接字串。 如需詳細資訊，請參閱 [連接到 Azure 儲存體](#connecting-to-azure-storage)。
1. 將 `using` 和命名空間的指示詞加入 `Microsoft.WindowsAzure.Storage` `Microsoft.WindowsAzure.Storage.Blob` 至將會存取 Azure 儲存體的類別。

## <a name="connecting-to-azure-storage"></a>連線到 Azure 儲存體

針對儲存體帳戶資源提出的每個要求都必須經過驗證。 雖然可以將 blob 設定為支援匿名驗證，但應用程式可以使用兩種主要方法來向儲存體帳戶進行驗證：

- 共用金鑰。 此方法會使用 Azure 儲存體帳戶名稱和帳戶金鑰來存取儲存體服務。 儲存體帳戶會在建立時指派兩個可用於共用金鑰驗證的私密金鑰。
- 共用存取簽章。 這是可以附加到 URL 的權杖，可讓您在有效的期間內，使用其指定的許可權來委派儲存體資源的存取權。

您可以指定連接字串，其中包含從應用程式存取 Azure 儲存體資源所需的驗證資訊。 此外，您可以將連接字串設定為從 Visual Studio 連接到 Azure 儲存體模擬器。

> [!NOTE]
> Azure 儲存體支援連接字串中的 HTTP 和 HTTPS。 不過，建議使用 HTTPS。

### <a name="connecting-to-the-azure-storage-emulator"></a>連接到 Azure 儲存體模擬器

Azure 儲存體模擬器提供了模擬 Azure blob、佇列和表格服務的本機環境，以供開發之用。

下列連接字串應該用來連接到 Azure 儲存體模擬器：

```csharp
UseDevelopmentStorage=true
```

如需有關 Azure 儲存體模擬器的詳細資訊，請參閱 [使用 azure 儲存體模擬器進行開發和測試](https://azure.microsoft.com/documentation/articles/storage-use-emulator/)。

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>使用共用金鑰連接到 Azure 儲存體

下列連接字串格式應該用來連接到具有共用金鑰的 Azure 儲存體：

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` 應取代為您的儲存體帳戶名稱，並 `myAccountKey` 應取代為您的兩個帳戶存取金鑰的其中一個。

> [!NOTE]
> 使用共用金鑰驗證時，您的帳戶名稱和帳戶金鑰會散發給每個使用您應用程式的人員，這會提供儲存體帳戶的完整讀取/寫入存取權。 因此，使用共用金鑰驗證僅供測試之用，而且永遠不會將金鑰散發給其他使用者。

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>使用共用存取簽章連接到 Azure 儲存體

下列連接字串格式應該用來連接到具有 SAS 的 Azure 儲存體：

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` 應取代為 blob 端點的 URL，並 `mySharedAccessSignature` 應取代為您的 SAS。 SAS 會提供通訊協定、服務端點，以及用來存取資源的認證。

> [!NOTE]
> 建議針對生產環境應用程式使用 SAS 驗證。 不過，在生產應用程式中，應該視需要從後端服務取出 SAS，而不是與應用程式配套。

如需共用存取簽章的詳細資訊，請參閱 [使用共用存取簽章 (SAS) ](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。

## <a name="creating-a-container"></a>建立容器

`GetContainer`方法是用來抓取命名容器的參考，然後用來從容器中取出 blob，或將 blob 新增至容器。 下列程式碼範例示範 `GetContainer` 方法：

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

`CloudStorageAccount.Parse`方法會剖析連接字串，並傳回 `CloudStorageAccount` 代表儲存體帳戶的實例。 `CloudBlobClient`接著，方法會建立用來取得容器和 blob 的實例 `CreateCloudBlobClient` 。 `GetContainerReference`方法會先將指定的容器當做 `CloudBlobContainer` 實例來抓取，然後再傳回給呼叫方法。 在此範例中，容器名稱是 `ContainerType` 列舉值，轉換成小寫字串。

> [!NOTE]
> 容器名稱必須是小寫，而且必須以字母或數位開頭。 此外，它們只能包含字母、數位和虛線字元，且長度必須介於3到63個字元之間。

`GetContainer`會叫用方法，如下所示：

```csharp
var container = GetContainer(containerType);
```

`CloudBlobContainer`然後，實例可以用來建立容器（如果尚未存在）：

```csharp
await container.CreateIfNotExistsAsync();
```

根據預設，新建立的容器是私用的。 這表示必須指定儲存體存取金鑰，才能從容器中取出 blob。 如需將容器內的 blob 設為公用的詳細資訊，請參閱 [建立容器](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container)。

## <a name="uploading-data-to-a-container"></a>將資料上傳至容器

`UploadFileAsync`方法是用來將位元組資料串流上傳至 blob 儲存體，如下列程式碼範例所示：

```csharp
public static async Task<string> UploadFileAsync(ContainerType containerType, Stream stream)
{
  var container = GetContainer(containerType);
  await container.CreateIfNotExistsAsync();

  var name = Guid.NewGuid().ToString();
  var fileBlob = container.GetBlockBlobReference(name);
  await fileBlob.UploadFromStreamAsync(stream);

  return name;
}
```

在抓取容器參考之後，方法會建立容器（如果尚未存在的話）。 接著會建立新的，做為 `Guid` 唯一的 blob 名稱，並將 blob 區塊參考抓取為 `CloudBlockBlob` 實例。 然後使用方法將資料串流上傳至 blob `UploadFromStreamAsync` ，如果 blob 不存在，則會加以建立，如果存在，則會加以覆寫。

您必須先將檔案轉換成位元組資料流程，才能使用此方法將檔案上傳到 blob 儲存體。 這會在下列程式碼範例中示範：

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

`text`資料會轉換成位元組陣列，然後包裝為傳遞至方法的資料流程 `UploadFileAsync` 。

## <a name="downloading-data-from-a-container"></a>從容器下載資料

`GetFileAsync`方法是用來從 Azure 儲存體下載 blob 資料，如下列程式碼範例所示：

```csharp
public static async Task<byte[]> GetFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);

  var blob = container.GetBlobReference(name);
  if (await blob.ExistsAsync())
  {
    await blob.FetchAttributesAsync();
    byte[] blobBytes = new byte[blob.Properties.Length];

    await blob.DownloadToByteArrayAsync(blobBytes, 0);
    return blobBytes;
  }
  return null;
}
```

在取得容器參考之後，方法會抓取所儲存資料的 blob 參考。 如果 blob 存在，則方法會抓取其屬性 `FetchAttributesAsync` 。 系統會建立正確大小的位元組陣列，並下載 blob 做為傳回給呼叫方法的位元組陣列。

下載 blob 位元組資料之後，必須將它轉換成其原始表示。 這會在下列程式碼範例中示範：

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

在 `GetFileAsync` 轉換回 UTF8 編碼的字串之前，會先從方法 Azure 儲存體取得位元組陣列。

## <a name="listing-data-in-a-container"></a>列出容器中的資料

`GetFilesListAsync`方法是用來抓取儲存在容器中的 blob 清單，如下列程式碼範例所示：

```csharp
public static async Task<IList<string>> GetFilesListAsync(ContainerType containerType)
{
  var container = GetContainer(containerType);

  var allBlobsList = new List<string>();
  BlobContinuationToken token = null;

  do
  {
    var result = await container.ListBlobsSegmentedAsync(token);
    if (result.Results.Count() > 0)
    {
      var blobs = result.Results.Cast<CloudBlockBlob>().Select(b => b.Name);
      allBlobsList.AddRange(blobs);
    }
    token = result.ContinuationToken;
  } while (token != null);

  return allBlobsList;
}
```

在取得容器參考之後，方法會使用容器的 `ListBlobsSegmentedAsync` 方法來抓取容器內 blob 的參考。 `ListBlobsSegmentedAsync`當實例不是時，會列舉方法所傳回的結果 `BlobContinuationToken` `null` 。 每個 blob 會從傳回的轉換成， `IListBlobItem` `CloudBlockBlob` 以便在將 `Name` 其值新增至集合之前，存取 blob 的屬性 `allBlobsList` 。 一旦 `BlobContinuationToken` 實例為，就會 `null` 傳回最後一個 blob 名稱，而執行則會結束迴圈。

## <a name="deleting-data-from-a-container"></a>刪除容器中的資料

`DeleteFileAsync`方法是用來從容器中刪除 blob，如下列程式碼範例所示：

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

在取得容器參考之後，方法會抓取所指定 blob 的 blob 參考。 接著，會使用方法刪除 blob `DeleteIfExistsAsync` 。

## <a name="related-links"></a>相關連結

- [Azure 儲存體 (範例) ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)
- [儲存體簡介](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [如何使用 Xamarin 的 Blob 儲存體](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [使用共用存取簽章 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Windows Azure 儲存體 (NuGet) ](https://www.nuget.org/packages/WindowsAzure.Storage/)

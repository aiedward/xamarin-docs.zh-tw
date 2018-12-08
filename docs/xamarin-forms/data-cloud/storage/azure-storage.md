---
title: 儲存和存取 Azure 儲存體中的資料
description: Azure 儲存體是可以用來儲存非結構化及結構化資料的可調整的雲端儲存體解決方案。 這篇文章會示範如何使用 Xamarin.Forms 來將文字和二進位資料儲存在 Azure 儲存體，以及如何存取資料。
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 1f920eb36eab3e451b20aa91734f00cee5ba6485
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059216"
---
# <a name="storing-and-accessing-data-in-azure-storage"></a>儲存和存取 Azure 儲存體中的資料

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)

_Azure 儲存體是可以用來儲存非結構化及結構化資料的可調整的雲端儲存體解決方案。這篇文章會示範如何使用 Xamarin.Forms 來將文字和二進位資料儲存在 Azure 儲存體，以及如何存取資料。_

## <a name="overview"></a>總覽

Azure 儲存體提供四個儲存體服務：

- Blob 儲存體。 Blob 可以是文字或二進位資料，例如備份、 虛擬機器、 媒體檔案或文件。
- 資料表儲存體是 NoSQL 索引鍵屬性存放區。
- 佇列儲存體是工作流程處理和雲端服務之間通訊的傳訊服務。
- 檔案儲存體提供共用儲存體使用 SMB 通訊協定。

有兩種類型的儲存體帳戶：

- 一般用途儲存體帳戶會提供單一帳戶從 Azure 儲存體服務的存取。
- Blob 儲存體帳戶是專門的儲存體帳戶用於儲存 blob。 您只需要儲存 blob 資料時，建議使用此帳戶類型。

本文章中，和隨附的範例應用程式，示範如何上傳的影像和文字檔案到 blob 儲存體，並下載它們。 此外，它也示範從 blob 儲存體，擷取一份檔案及刪除檔案。

如需有關 Azure 儲存體的詳細資訊，請參閱 <<c0> [ 儲存體簡介](https://azure.microsoft.com/documentation/articles/storage-introduction/)。

## <a name="introduction-to-blob-storage"></a>Blob 儲存體簡介

Blob 儲存體包含三個元件，如下圖所示：

![](azure-storage-images/blob-storage.png "Blob 儲存體概念")

Azure 儲存體的所有存取都都會透過儲存體帳戶。 儲存體帳戶可以包含無限的數量的容器和容器可以儲存無限的數量的 blob 儲存體帳戶容量上限。

Blob 是任何類型和大小的檔案。 Azure 儲存體支援三種不同的 blob 類型：

- 區塊 blob 已針對串流和儲存雲端物件，和最佳化是不錯的選擇儲存備份媒體檔案、 文件等等。區塊 blob 可達 195 Gb 的大小。
- 附加 blob 是區塊 blob 類似，但最適合用於附加作業，例如記錄。 附加 blob 可達 195 Gb 的大小。
- 分頁 blob 適用於頻繁的讀取/寫入作業，通常用於儲存虛擬機器和其磁碟。 分頁 blob 可以多達 1tb 的大小。

> [!NOTE]
> 請注意，blob 儲存體帳戶支援區塊和附加 blob，但不是分頁 blob。

將 blob 上傳至 Azure 儲存體，並從 Azure 儲存體、 下載為位元組資料流。 因此，檔案必須轉換成之前上傳，並轉換的回其原始的表示法，下載後的位元組資料流。

Azure 儲存體中儲存的每個物件具有唯一的 URL 位址。 儲存體帳戶名稱會形成該位址和子網域和網域名稱形式的組合的子網域*端點*儲存體帳戶。 例如，如果您的儲存體帳戶命名為*mystorageaccount*，儲存體帳戶的預設 blob 端點是`https://mystorageaccount.blob.core.windows.net`。

存取儲存體帳戶中的物件的 URL 是藉由附加至端點的儲存體帳戶中物件的位置。 例如，blob 位址將會有格式`https://mystorageaccount.blob.core.windows.net/mycontainer/myblob`。

## <a name="setup"></a>安裝程式

整合 Xamarin.Forms 應用程式中的 Azure 儲存體帳戶的程序如下所示：

1. 建立儲存體帳戶。 如需詳細資訊，請參閱 <<c0> [ 建立儲存體帳戶](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account)。
1. 新增[Azure Storage Client Library](https://www.nuget.org/packages/WindowsAzure.Storage/) Xamarin.Forms 應用程式。
1. 設定儲存體連接字串。 如需詳細資訊，請參閱 <<c0> [ 連接到 Azure 儲存體](#connecting)。
1. 新增`using`指示詞`Microsoft.WindowsAzure.Storage`和`Microsoft.WindowsAzure.Storage.Blob`命名空間，以將存取 Azure 儲存體的類別。

> [!NOTE]
> 這個範例會使用共用存取專案，而 Azure 儲存體用戶端程式庫現在也支援從可攜式類別庫 (PCL) 專案正在使用。

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>連接到 Azure 儲存體

對儲存體帳戶資源的每個要求必須經過驗證。 雖然 blob 可以設定為支援匿名驗證，有兩個應用程式可用來驗證的儲存體帳戶的主要方法：

- 共用的金鑰。 這個方法會使用 Azure 儲存體帳戶名稱和帳戶金鑰來存取儲存體服務。 儲存體帳戶會指派可用的共用金鑰驗證的建立兩個私用的索引鍵。
- 共用的存取簽章。 這是您可以附加至 URL，可讓您對儲存體資源的委派的存取權杖，具有權限它，會為指定的是有效的時間。

包含從應用程式存取 Azure 儲存體資源所需的驗證資訊指定連接字串。 此外，可以從 Visual Studio 連線到 Azure 儲存體模擬器設定連接字串。

> [!NOTE]
> Azure 儲存體支援 HTTP 和 HTTPS，連接字串中。 不過，建議使用 HTTPS。

### <a name="connecting-to-the-azure-storage-emulator"></a>連線到 Azure 儲存體模擬器

Azure 儲存體模擬器提供模擬 Azure blob、 佇列和表格服務進行開發本機環境。

下列連接字串應該用來連線到 Azure 儲存體模擬器中：

```csharp
UseDevelopmentStorage=true
```

如需有關 Azure 儲存體模擬器的詳細資訊，請參閱 <<c0> [ 使用 Azure 儲存體模擬器進行開發和測試](https://azure.microsoft.com/documentation/articles/storage-use-emulator/)。

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>連接到 Azure 儲存體，使用共用的金鑰

下列連接字串格式應該用於連線到 Azure 儲存體，使用共用金鑰：

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` 用來取代您的儲存體帳戶的名稱和`myAccountKey`應該取代為兩個帳戶存取金鑰。

> [!NOTE]
> 當使用共用金鑰驗證，您的帳戶名稱和帳戶金鑰將會分送給每個會使用您的應用程式，這將會提供完整的讀取/寫入存取權的儲存體帳戶的個人。 因此，基於測試目的，使用共用金鑰驗證，而且永遠不會散發給其他使用者的金鑰。

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>連接到 Azure 儲存體中使用共用存取簽章

下列連接字串格式應該用於連線到 Azure 儲存體 SAS:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` 應該取代為您的 blob 端點的 URL 和`mySharedAccessSignature`應該取代為您的 SAS。 SAS 提供通訊協定、 服務端點，以及認證來存取資源。

> [!NOTE]
> SAS 驗證被建議用於生產應用程式。 不過，在實際執行的應用程式應該擷取 SAS 從後端服務隨，而不是所隨附的應用程式。

如需有關共用存取簽章的詳細資訊，請參閱 <<c0> [ 使用共用存取簽章 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。

## <a name="creating-a-container"></a>建立容器

`GetContainer`方法用來擷取具名的容器，可用來擷取容器的 blob，或將 blob 新增至容器的參考。 下列程式碼範例示範`GetContainer`方法：

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

`CloudStorageAccount.Parse`方法會剖析連接字串，並傳回`CloudStorageAccount`代表儲存體帳戶的執行個體。 A`CloudBlobClient`執行個體，這用來擷取容器和 blob，然後由`CreateCloudBlobClient`方法。 `GetContainerReference`方法會擷取指定的容器，做為`CloudBlobContainer`執行個體，再傳回至呼叫的方法。 在此範例中，容器名稱是`ContainerType`列舉值，轉換成小寫的字串。

> [!NOTE]
> 容器名稱必須是小寫，並且必須以字母或數字開頭。 此外，它們只能包含字母、 數字和虛線字元，並必須介於 3 到 63 個字元之間。

`GetContainer`叫用方法時，如下所示：

```csharp
var container = GetContainer(containerType);
```

`CloudBlobContainer`執行個體可以再用來建立容器，如果不存在：

```csharp
await container.CreateIfNotExistsAsync();
```

根據預設，新建立的容器是私用。 這表示必須指定儲存體存取金鑰，才能從容器擷取 blob。 進行中的容器的公用 blob 的相關資訊，請參閱[建立容器](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container)。

## <a name="uploading-data-to-a-container"></a>將資料上傳至容器

`UploadFileAsync`方法用來上傳至 blob 儲存體，位元組資料流，並在下列程式碼範例所示：

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

擷取後為容器參考，此方法會建立容器，如果不存在。 新`Guid`接著會建立做為唯一的 blob 名稱，和 blob 的區塊參考會擷取為`CloudBlockBlob`執行個體。 資料流然後上傳至 blob 使用`UploadFromStreamAsync`方法，它會建立 blob，如果它尚未存在，或存在則覆寫。

檔案可以上傳至 blob 儲存體中使用這個方法之前，它必須先轉換為位元組資料流。 下列程式碼範例所示：

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

`text`資料會轉換成位元組陣列，然後再包裝為資料流傳遞至`UploadFileAsync`方法。

## <a name="downloading-data-from-a-container"></a>從容器下載資料

`GetFileAsync`方法用來從 Azure 儲存體、 下載 blob 資料，以及下列的程式碼範例所示：

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

在擷取之後為容器參考，方法會擷取 blob 參考的預存的資料。 如果 blob 存在，其屬性藉由擷取`FetchAttributesAsync`方法。 建立正確的大小的位元組陣列時，和 blob 會下載為位元組陣列傳回至呼叫的方法。

下載的 blob 位元組的資料之後, 它必須轉換成其原始的表示法。 下列程式碼範例所示：

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

從 Azure 儲存體中，擷取的位元組陣列`GetFileAsync`方法，它會轉換回 UTF8 編碼的字串。

## <a name="listing-data-in-a-container"></a>列出容器中的資料

`GetFilesListAsync`方法用來擷取儲存在容器中，blob 的清單和下列程式碼範例所示：

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

在擷取之後為容器參考，此方法會使用容器的`ListBlobsSegmentedAsync`方法來擷取容器中 blob 的參考。 所傳回的結果`ListBlobsSegmentedAsync`方法會列舉雖然`BlobContinuationToken`執行個體不是`null`。 每個 blob 會轉型從傳回`IListBlobItem`要`CloudBlockBlob`順序存取`Name`屬性的 blob，值後，才會加入至`allBlobsList`集合。 一次`BlobContinuationToken`執行個體是`null`、 已傳回的最後一個 blob 名稱，而且會執行結束迴圈。

## <a name="deleting-data-from-a-container"></a>從容器中刪除資料

`DeleteFileAsync`方法用來刪除 blob 容器，從和下列程式碼範例所示：

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

在擷取之後為容器參考，方法會擷取指定的 blob 的 blob 參考。 Blob 刪除與`DeleteIfExistsAsync`方法。

## <a name="summary"></a>總結

這篇文章會示範如何使用 Xamarin.Forms 來將文字和二進位資料儲存在 Azure 儲存體，以及如何存取資料。 Azure 儲存體是用於儲存非結構化及結構化資料的可調整的雲端儲存體解決方案。


## <a name="related-links"></a>相關連結

- [Azure 儲存體 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)
- [儲存體簡介](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [如何使用 Xamarin 的 Blob 儲存體](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [使用共用的存取簽章 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Windows Azure 儲存體](https://www.nuget.org/packages/WindowsAzure.Storage/)

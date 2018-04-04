---
title: 儲存和存取 Azure 儲存體中的資料
description: Azure 儲存體是可以用來儲存非結構化，和結構化資料的可擴充式雲端存放裝置解決方案。 本文示範如何使用 Xamarin.Forms，將文字和二進位資料儲存在 Azure 儲存體，以及如何存取資料。
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 63afeec81eff350b034e8dd3a13da52801937826
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="storing-and-accessing-data-in-azure-storage"></a>儲存和存取 Azure 儲存體中的資料

_Azure 儲存體是可以用來儲存非結構化，和結構化資料的可擴充式雲端存放裝置解決方案。本文示範如何使用 Xamarin.Forms，將文字和二進位資料儲存在 Azure 儲存體，以及如何存取資料。_

## <a name="overview"></a>總覽

Azure 儲存體提供四個儲存體服務：

- Blob 儲存體。 Blob 可以是文字或二進位資料，例如備份、 虛擬機器、 媒體檔案或文件。
- 資料表儲存體是 NoSQL 索引鍵屬性存放區。
- 佇列儲存體是工作流程處理和雲端服務之間通訊的訊息服務。
- 檔案存放裝置提供共用存放裝置使用 SMB 通訊協定。

有兩種類型的儲存體帳戶：

- 一般用途儲存體帳戶提供從單一帳戶存取 Azure 儲存體服務。
- Blob 儲存體帳戶是用於儲存 blob 專門的儲存體帳戶。 當您只需要儲存 blob 資料時，建議您使用此帳戶類型。

本文中，以及隨附的範例應用程式，示範 blob 儲存體，並下載它們上傳的影像和文字檔案。 此外，它也示範從 blob 儲存體，擷取的檔案清單和刪除檔案。

如需有關 Azure 儲存體的詳細資訊，請參閱[儲存體簡介](https://azure.microsoft.com/documentation/articles/storage-introduction/)。

## <a name="introduction-to-blob-storage"></a>Blob 儲存體簡介

Blob 儲存體包含三個元件，如下圖所示：

![](azure-storage-images/blob-storage.png "Blob 儲存體概念")

Azure 儲存體的所有存取都是透過儲存體帳戶。 儲存體帳戶可以包含無限的數量的容器和容器可以儲存不限的數目的 blob 儲存體帳戶的容量上限為止。

Blob 是任何類型和大小的檔案。 Azure 儲存體支援三種不同的 blob 類型：

- 區塊 blob 會最佳化，供串流處理和儲存雲端物件，而且儲存備份媒體檔案、 文件等的理想選擇。區塊 blob 可以是 195 Gb 的大小。
- 附加 blob 類似於區塊 blob，但會針對最佳化附加作業，例如記錄。 附加 blob 可以是 195 Gb 的大小。
- 分頁 blob 適用於頻繁的讀取/寫入作業，通常會用來儲存虛擬機器和其磁碟。 分頁 blob 可以是 1 Tb 的大小。

> [!NOTE]
> 請注意支援區塊 blob 儲存體帳戶，而且附加 blob，但是未分頁 blob。

Blob 上傳至 Azure 儲存體，並從 Azure 儲存體，下載的位元組資料流的形式。 因此，檔案必須轉換成之前上傳，並轉換的回其原始的表示法，在下載後的位元組資料流。

儲存在 Azure 儲存體的每個物件具有唯一的 URL 位址。 儲存體帳戶名稱會形成該位址和子網域和網域名稱表單的組合的子網域*端點*儲存體帳戶。 例如，如果您的儲存體帳戶命名為*mystorageaccount*，儲存體帳戶的預設 blob 端點是`https://mystorageaccount.blob.core.windows.net`。

建置的 URL 來存取儲存體帳戶中的物件附加至端點的儲存體帳戶中的物件的位置。 例如，blob 位址將會有格式`https://mystorageaccount.blob.core.windows.net/mycontainer/myblob`。

## <a name="setup"></a>安裝程式

將 Azure 儲存體帳戶整合至 Xamarin.Forms 應用程式的程序如下所示：

1. 建立儲存體帳戶。 如需詳細資訊，請參閱[建立儲存體帳戶](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account)。
1. 新增[Azure 儲存體用戶端程式庫](https://www.nuget.org/packages/WindowsAzure.Storage/)Xamarin.Forms 應用程式。
1. 設定儲存體連接字串。 如需詳細資訊，請參閱[連接到 Azure 儲存體](#connecting)。
1. 新增`using`指示詞`Microsoft.WindowsAzure.Storage`和`Microsoft.WindowsAzure.Storage.Blob`命名空間和類別，以便存取 Azure 儲存體。

> [!NOTE]
> 雖然這個範例會使用共用存取專案，Azure 儲存體用戶端程式庫現在也支援從可攜式類別程式庫 (PCL) 專案正在使用。

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>連接到 Azure 儲存體

對儲存體帳戶資源提出的每個要求必須進行驗證。 雖然 blob 可以設定為支援匿名驗證，但是會有兩個應用程式可用來驗證儲存體帳戶的主要方法：

- 共用的金鑰。 這個方法會使用 Azure 儲存體帳戶名稱和帳戶金鑰來存取儲存體服務。 儲存體帳戶會指派可用的共用金鑰驗證的建立兩個私用的索引鍵。
- 共用的存取簽章。 這是可以附加至的 URL，可讓您儲存體資源的委派的存取權杖，權限它，會為指定的是有效的時間。

包含從應用程式存取 Azure 儲存體資源所需的驗證資訊指定連接字串。 此外，可以從 Visual Studio 連接到 Azure 儲存體模擬器設定連接字串。

> [!NOTE]
> Azure 儲存體支援 HTTP 和 HTTPS 連接字串中。 不過，建議使用 HTTPS。

### <a name="connecting-to-the-azure-storage-emulator"></a>連接到 Azure 儲存體模擬器

Azure 儲存體模擬器提供模擬 Azure blob、 佇列和表格服務，供開發應用程式的本機環境。

下列連接字串應該用來連接到 Azure 儲存體模擬器：

```csharp
UseDevelopmentStorage=true
```

如需有關 Azure 儲存體模擬器的詳細資訊，請參閱[使用 Azure 儲存體模擬器進行開發和測試](https://azure.microsoft.com/documentation/articles/storage-use-emulator/)。

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>連接到 Azure 儲存體使用共用的金鑰

下列連接字串格式應該用來連接到 Azure 儲存體使用共用金鑰：

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` 用來取代的儲存體帳戶名稱和`myAccountKey`應該以其中兩個帳戶存取金鑰取代。

> [!NOTE]
> 當使用共用金鑰驗證，您的帳戶名稱和帳戶金鑰會散發到每個人使用應用程式，將會提供完整的讀取/寫入存取儲存體帳戶。 因此，基於測試目的，使用共用金鑰驗證，而且永遠不會發佈到其他使用者。

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>連接到 Azure 儲存體使用共用存取簽章

下列連接字串格式應該用來連接到 Azure 儲存體與 SAS:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` 應該取代成您的 blob 端點的 URL 和`mySharedAccessSignature`應該取代成您的 SAS。 SAS 提供通訊協定、 服務端點，以及存取資源的認證。

> [!NOTE]
> SAS 驗證建議用於實際執行應用程式。 不過，在實際執行的應用程式應該擷取 SAS 從後端服務指定，而不是要隨同應用程式。

如需有關共用存取簽章的詳細資訊，請參閱[使用共用存取簽章 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。

## <a name="creating-a-container"></a>建立容器

`GetContainer`方法用來擷取具名的容器，可以接著用來從容器擷取 blob 或新增 blob 容器的參考。 下列程式碼範例示範`GetContainer`方法：

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

`CloudStorageAccount.Parse`方法剖析連接字串，並傳回`CloudStorageAccount`代表儲存體帳戶的執行個體。 A`CloudBlobClient`執行個體，這用來擷取容器和 blob，然後由`CreateCloudBlobClient`方法。 `GetContainerReference`方法會擷取指定的容器做為`CloudBlobContainer`執行個體，再傳回至呼叫的方法。 在此範例中，容器名稱是`ContainerType`列舉值，轉換為小寫的字串。

> [!NOTE]
> 容器名稱必須是小寫，而且必須以字母或數字開頭。 此外，它們只能包含字母、 數字和虛線字元，而且必須是介於 3 到 63 個字元之間。

`GetContainer`叫用方法時，如下所示：

```csharp
var container = GetContainer(containerType);
```

`CloudBlobContainer`然後可以使用執行個體不存在時建立容器：

```csharp
await container.CreateIfNotExistsAsync();
```

根據預設，新建立的容器是私用。 這表示必須指定儲存體存取金鑰，才能從容器擷取 blob。 如需建立公用容器中的 blob 資訊，請參閱[建立容器](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container)。

## <a name="uploading-data-to-a-container"></a>將資料上傳到容器

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

擷取後為容器參考，此方法會建立容器，如果不存在。 新`Guid`接著會建立做為唯一 blob 名稱，並以擷取 blob 的區塊參考`CloudBlockBlob`執行個體。 中的資料流然後上傳至 blob 使用`UploadFromStreamAsync`方法，如果它不存在，會覆寫它存在的話，會建立 blob。

檔案可上傳至 blob 儲存體使用這個方法之前，它必須先轉換成位元組資料流。 下列程式碼範例所示：

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

`text`資料會轉換成位元組陣列，然後再包裝為資料流傳遞至`UploadFileAsync`方法。

## <a name="downloading-data-from-a-container"></a>從容器下載資料

`GetFileAsync`方法為用於來下載 blob 資料從 Azure 儲存體，並在下列程式碼範例所示：

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

擷取後為容器參考，方法會擷取儲存的資料的 blob 參考。 如果 blob 存在，其屬性藉由擷取`FetchAttributesAsync`方法。 建立的正確大小的位元組陣列，以及 blob 時會下載的位元組陣列傳回至呼叫的方法。

下載的 blob 位元組資料之後，它必須轉換成其原始的表示法。 下列程式碼範例所示：

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

從 Azure 儲存體，所擷取的位元組陣列`GetFileAsync`方法之前將它轉換回成 UTF8 編碼的字串。

## <a name="listing-data-in-a-container"></a>列出容器中的資料

`GetFilesListAsync`方法用來擷取 blob 儲存容器中的清單，且下列程式碼範例所示：

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

擷取後為容器參考，此方法會使用容器的`ListBlobsSegmentedAsync`方法來擷取容器中 blob 的參考。 所傳回的結果`ListBlobsSegmentedAsync`方法會列舉時`BlobContinuationToken`執行個體不是`null`。 每一個 blob 轉換從傳回`IListBlobItem`至`CloudBlockBlob`順序存取`Name`屬性的值之前的 blob 會加入至`allBlobsList`集合。 一次`BlobContinuationToken`執行個體是`null`、 已傳回 blob 姓氏，以及執行結束迴圈。

## <a name="deleting-data-from-a-container"></a>刪除容器中的資料

`DeleteFileAsync`方法用來從容器中，刪除 blob 和下列程式碼範例所示：

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

擷取後為容器參考，方法會擷取指定的 blob 的 blob 參考。 Blob 會隨之然後刪除`DeleteIfExistsAsync`方法。

## <a name="summary"></a>總結

本文示範如何使用 Xamarin.Forms，將文字和二進位資料儲存在 Azure 儲存體，以及如何存取資料。 Azure 儲存體是可以用來儲存非結構化，和結構化資料的可擴充式雲端存放裝置解決方案。


## <a name="related-links"></a>相關連結

- [Azure 儲存體 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)
- [儲存體簡介](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [如何使用 Xamarin 從 Blob 儲存體](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [使用共用的存取簽章 (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Windows Azure 儲存體](https://www.nuget.org/packages/WindowsAzure.Storage/)

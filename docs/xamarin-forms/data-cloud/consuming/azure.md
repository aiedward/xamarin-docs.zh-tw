---
title: 使用 Azure 行動應用程式
description: 只適用於使用 Node.js 後端的 Azure 行動應用程式，這篇文章說明如何查詢、 插入、 更新和刪除儲存在 Azure 行動應用程式執行個體中資料表的資料。
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 3cf27e48fe23b9a0f035689e55a72fcc706ab266
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241327"
---
# <a name="consuming-an-azure-mobile-app"></a>使用 Azure 行動應用程式

_Azure 行動應用程式可讓您與裝載在 Azure 應用程式服務中，以支援行動裝置的驗證、 離線同步處理，及推播通知的可擴充後端開發應用程式。只適用於使用 Node.js 後端的 Azure 行動應用程式，這篇文章說明如何查詢、 插入、 更新和刪除儲存在 Azure 行動應用程式執行個體中資料表的資料。_

如需如何建立可供 Xamarin.Forms Azure 行動應用程式執行個體資訊，請參閱[建立 Xamarin.Forms 應用程式](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)。 之後請遵循下列指示，可下載的範例應用程式可以設定為使用 Azure 行動應用程式執行個體，藉由設定`Constants.ApplicationURL`的 Azure 行動應用程式執行個體的 url。 然後，執行範例應用程式時它會連線到 Azure 行動應用程式執行個體，如下列螢幕擷取畫面所示：

![](azure-images/portal.png "範例應用程式")

Azure 行動應用程式的存取都是透過[Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)，並從 Azure Xamarin.Forms 範例應用程式的所有連線都是透過 HTTPS。

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性 (ATS) 會強制執行安全連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，因此可以防止意外洩漏機密資訊。 由於 AT 預設會在 ios 9 建置的應用程式啟用時，所有連線將都會受限於 AT 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以超出選擇 AT`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-an-azure-mobile-app-instance"></a>使用 Azure 行動應用程式執行個體

[Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)提供`MobileServiceClient`類別，可由 Xamarin.Forms 應用程式來存取 Azure 行動應用程式的執行個體，如下列程式碼範例所示：

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

當`MobileServiceClient`建立執行個體，必須指定應用程式 URL，以識別 Azure 行動應用程式執行個體。 這個值可以從行動裝置的應用程式中的儀表板取得[Microsoft Azure 入口網站](https://portal.azure.com/)。

若要參考`TodoItem`必須取得 Azure 行動應用程式執行個體中儲存的資料表，才能在該資料表上執行作業。 這藉由呼叫`GetTable`方法`MobileServiceClient`執行個體，它會傳回`IMobileServiceTable<TodoItem>`參考。

### <a name="querying-data"></a>查詢資料

資料表的內容可以藉由呼叫擷取`IMobileServiceTable.ToEnumerableAsync`方法，以非同步方式評估查詢，並傳回結果。 資料也可以藉由篩選伺服器端`Where`查詢中的子句。 `Where`子句會套用資料列篩選至資料表中，針對查詢述詞，如下列程式碼範例所示：

```csharp
public async Task<ObservableCollection<TodoItem>> GetTodoItemsAsync (bool syncItems = false)
{
  ...
  IEnumerable<TodoItem> items = await todoTable
              .Where (todoItem => !todoItem.Done)
              .ToEnumerableAsync ();

  return new ObservableCollection<TodoItem> (items);
}
```

此查詢會傳回所有項目從`TodoItem`資料表其`Done`屬性等於`false`。 查詢結果然後置於`ObservableCollection`供顯示。

### <a name="inserting-data"></a>插入資料

插入資料時的 Azure 行動應用程式執行個體中，將會自動會產生新的資料行中所需的資料表，提供 Azure 行動應用程式執行個體中啟用動態結構描述。 `IMobileServiceTable.InsertAsync`方法用來將新的資料列插入至指定的資料表，如下列程式碼範例所示：

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

在建立時的插入要求，資料傳遞至 Azure 行動應用程式執行個體中必須沒有指定的識別碼。 如果插入要求包含識別碼`MobileServiceInvalidOperationException`就會擲回。

之後`InsertAsync`方法完成、 Azure 行動應用程式執行個體中資料的識別碼將會填入`TodoItem`Xamarin.Forms 應用程式中的執行個體。

### <a name="updating-data"></a>更新資料

在更新的 Azure 行動應用程式執行個體中的資料時，將會自動會產生新的資料行中所需的資料表，提供 Azure 行動應用程式執行個體中啟用動態結構描述。 `IMobileServiceTable.UpdateAsync`方法用來更新現有的資料，以新的資訊，如下列程式碼範例所示：

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

更新要求時，必須指定的 ID，以供 Azure 行動應用程式執行個體識別要更新的資料。 這個識別碼值儲存在`TodoItem.ID`屬性。 如果更新要求不包含 ID 沒有被更新，以判斷資料的 Azure 行動應用程式執行個體方法，因此`MobileServiceInvalidOperationException`就會擲回。

### <a name="deleting-data"></a>刪除資料

`IMobileServiceTable.DeleteAsync`方法用來從 Azure 行動應用程式資料表中，刪除資料，如下列程式碼範例所示：

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

提出刪除要求時，必須指定的 ID，以供 Azure 行動應用程式 sinstance 識別要刪除的資料。 這個識別碼值儲存在`TodoItem.ID`屬性。 刪除要求不包含 ID，如果沒有任何要刪除的 Azure 行動應用程式執行個體，來判斷資料的方式，因此`MobileServiceInvalidOperationException`就會擲回。

## <a name="summary"></a>總結

本文說明如何使用[Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)來查詢、 插入、 更新和刪除儲存在 Azure 行動應用程式執行個體中的資料表中的資料。 SDK 提供`MobileServiceClient`Xamarin.Forms 應用程式用來存取 Azure 行動應用程式執行個體的類別。


## <a name="related-links"></a>相關連結

- [TodoAzure （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [建立 Xamarin.Forms 應用程式](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)

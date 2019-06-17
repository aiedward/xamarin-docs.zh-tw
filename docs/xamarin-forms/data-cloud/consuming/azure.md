---
title: 使用 Azure 行動應用程式
description: 本文章中，也就是只適用於 Azure Mobile Apps 使用 Node.js 後端，說明如何查詢、 插入、 更新和刪除儲存在 Azure Mobile Apps 執行個體中資料表的資料。
ms.prod: xamarin
ms.assetid: 2B3EFD0A-2922-437D-B151-4B4DE46E2095
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 8bd77ec4975fae3cc7245c5adc2b5ef18568b9e1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61328374"
---
# <a name="consuming-an-azure-mobile-app"></a>使用 Azure 行動應用程式

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)

_Azure Mobile Apps 可讓您開發可調整的後端裝載在 Azure App Service 中支援的行動驗證、 離線同步處理，以及推播通知與應用程式。本文章中，也就是只適用於 Azure Mobile Apps 使用 Node.js 後端，說明如何查詢、 插入、 更新和刪除儲存在 Azure Mobile Apps 執行個體中資料表的資料。_

> [!NOTE]
> 從開始於 6 月 30 日，所有新的 Azure 行動應用程式將會建立使用 TLS 1.2 預設。 此外，也建議您現有的 Azure 行動應用程式重新設定為使用 TLS 1.2。 如需如何強制執行在 Azure 行動應用程式的 TLS 1.2，請參閱[強制使用 TLS 版本](/azure/app-service/app-service-web-tutorial-custom-ssl#enforce-tls-versions)。 如需如何設定為使用 TLS 1.2 的 Xamarin 專案的資訊，請參閱[傳輸層安全性 (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)。

如需如何建立可供 Xamarin.Forms Azure Mobile Apps 執行個體的資訊，請參閱[建立 Xamarin.Forms 應用程式](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)。 之後遵循下列指示，可下載的範例應用程式可以設定為使用 Azure Mobile Apps 執行個體，藉由設定`Constants.ApplicationURL`的 Azure Mobile Apps 執行個體的 url。 然後，執行範例應用程式時它會連線到 Azure Mobile Apps 執行個體，如下列螢幕擷取畫面所示：

![](azure-images/portal.png "範例應用程式")

Azure Mobile Apps 的存取是透過[Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)，並從 Xamarin.Forms 範例應用程式至 Azure 的所有連線都透過 HTTPS 都進行。

> [!NOTE]
> 在 iOS 9 和更新版本中，App Transport Security (ATS) 會強制執行安全的連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，藉此防止意外洩漏機密資訊。 針對 iOS 9 所建置的應用程式中的預設會啟用 ATS，因為所有連線將會都受限於 ATS 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以共選擇 ATS`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的達成**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-an-azure-mobile-app-instance"></a>使用 Azure 行動應用程式執行個體

[Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)提供`MobileServiceClient`類別，可由 Xamarin.Forms 應用程式來存取 Azure Mobile Apps 的執行個體，如下列程式碼範例所示：

```csharp
IMobileServiceTable<TodoItem> todoTable;
MobileServiceClient client;

public TodoItemManager ()
{
  client = new MobileServiceClient (Constants.ApplicationURL);
  todoTable = client.GetTable<TodoItem> ();
}
```

當`MobileServiceClient`建立執行個體，必須指定應用程式 URL，以識別 Azure Mobile Apps 執行個體。 此值可從行動裝置的應用程式中的儀表板來取得[Microsoft Azure 入口網站](https://portal.azure.com/)。

參考`TodoItem`必須取得資料表儲存在 Azure Mobile Apps 執行個體，才能在該資料表上執行作業。 這藉由呼叫來達成`GetTable`方法`MobileServiceClient`執行個體，以傳回`IMobileServiceTable<TodoItem>`參考。

### <a name="querying-data"></a>查詢資料

可以擷取資料表的內容呼叫`IMobileServiceTable.ToEnumerableAsync`方法，以非同步方式評估查詢，並傳回結果。 資料也可以篩選伺服器端包含`Where`查詢子句中的。 `Where`子句套用資料列篩選來針對資料表中，查詢述詞，如下列程式碼範例所示：

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

此查詢會傳回所有項目`TodoItem`資料表的`Done`屬性等於`false`。 查詢結果接著會放在`ObservableCollection`顯示。

### <a name="inserting-data"></a>插入資料

自動會產生新的資料行時將資料插入 Azure Mobile Apps 執行個體，在資料表中視需要，提供在 Azure Mobile Apps 執行個體中啟用該動態結構描述。 `IMobileServiceTable.InsertAsync`方法用來將新的資料列插入至指定的資料表，如下列程式碼範例所示：

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.InsertAsync (item);
  ...
}
```

插入要求時，傳遞至 Azure Mobile Apps 執行個體資料中必須沒有指定的識別碼。 如果插入要求中包含識別碼`MobileServiceInvalidOperationException`就會擲回。

在後`InsertAsync`方法完成後，將 Azure Mobile Apps 執行個體中資料的識別碼將會填入`TodoItem`Xamarin.Forms 應用程式中的執行個體。

### <a name="updating-data"></a>更新資料

在更新 Azure 行動應用程式執行個體中的資料時，將會自動會產生新的資料行中所需的資料表，提供在 Azure Mobile Apps 執行個體中啟用該動態結構描述。 `IMobileServiceTable.UpdateAsync`方法用來更新現有的資料，以新的資訊，如下列程式碼範例所示：

```csharp
public async Task SaveTaskAsync (TodoItem item)
{
  ...
  await todoTable.UpdateAsync (item);
  ...
}
```

更新要求時，就必須指定 ID，以供 Azure Mobile Apps 執行個體識別要更新的資料。 這個識別碼值會儲存在`TodoItem.ID`屬性。 如果更新要求未包含識別碼沒有更新，以判斷資料的 Azure Mobile Apps 執行個體方法，因此`MobileServiceInvalidOperationException`就會擲回。

### <a name="deleting-data"></a>刪除資料

`IMobileServiceTable.DeleteAsync`方法用來從 Azure Mobile Apps 資料表中，刪除資料，如下列程式碼範例所示：

```csharp
public async Task DeleteTaskAsync (TodoItem item)
{
  ...
  await todoTable.DeleteAsync(item);
  ...
}
```

提出刪除要求時，就必須指定 ID，以供 Azure 行動應用程式 sinstance 識別要刪除的資料。 這個識別碼值會儲存在`TodoItem.ID`屬性。 如果 delete 要求未包含執行識別碼，沒有要刪除，來判斷資料的 Azure Mobile Apps 執行個體方法，因此`MobileServiceInvalidOperationException`就會擲回。

## <a name="summary"></a>總結

這篇文章說明如何使用[Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)來查詢、 插入、 更新和刪除儲存在 Azure 行動應用程式執行個體中的資料表中的資料。 SDK 提供`MobileServiceClient`供 Xamarin.Forms 應用程式用來存取 Azure Mobile Apps 執行個體的類別。


## <a name="related-links"></a>相關連結

- [TodoAzure （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure/)
- [建立 Xamarin.Forms 應用程式](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started/)
- [Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)

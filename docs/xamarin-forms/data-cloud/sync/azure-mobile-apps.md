---
title: 使用 Azure Mobile Apps 的離線資料同步處理
description: 這篇文章說明如何將離線同步處理功能新增至 Xamarin.Forms 應用程式使用 Azure Mobile Apps 後端。
ms.prod: xamarin
ms.assetid: DBB343B0-2709-4C20-A669-5522B9956D9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2017
ms.openlocfilehash: 6080b4dc152558d6f532399cee7424670c588c28
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61319567"
---
# <a name="synchronizing-offline-data-with-azure-mobile-apps"></a>使用 Azure Mobile Apps 的離線資料同步處理

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)

_離線同步處理可讓使用者互動與行動應用程式、 檢視、 加入或修改資料，即使在沒有網路連線。變更會儲存在本機資料庫中，並變更該裝置上線後，可以與 Azure Mobile Apps 執行個體同步處理。這篇文章說明如何將離線同步處理功能新增至 Xamarin.Forms 應用程式。_

## <a name="overview"></a>總覽

[Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)提供`IMobileServiceTable`介面，代表可以儲存在 Azure Mobile Apps 執行個體中的資料表執行的作業。 這些作業會直接連接到 Azure Mobile Apps 執行個體，而且如果行動裝置沒有網路連線將會失敗。

若要支援離線同步處理，支援 Azure Mobile Client SDK*同步處理資料表*，這由提供`IMobileServiceSyncTable`介面。 這個介面會提供相同的建立、 讀取、 更新、 刪除 (CRUD) 作業，做為`IMobileServiceTable`介面，但作業讀取或寫入至本機存放區。 以新的資料從 Azure Mobile Apps 的執行個體未填入本機存放區，直到呼叫*提取*資料。 同樣地，資料傳送到 Azure Mobile Apps 執行個體的呼叫之前*推播*本機變更。

離線同步處理也支援在 Azure Mobile Apps 的執行個體，並自訂衝突解決時相同的資料錄已變更，這兩個本機存放區中，並偵測衝突。 在本機存放區，或在 Azure Mobile Apps 執行個體中，可以被處理衝突。

如需離線同步處理的詳細資訊，請參閱[在 Azure Mobile Apps 的離線資料同步處理](/azure/app-service-mobile/app-service-mobile-offline-data-sync/)並[啟用 Xamarin.Forms 行動應用程式的離線同步處理](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-offline-data/)。

## <a name="setup"></a>安裝程式

整合 Xamarin.Forms 應用程式與 Azure Mobile Apps 執行個體之間的離線同步處理程序如下所示：

1. 建立 Azure Mobile Apps 執行個體。 如需詳細資訊，請參閱 <<c0> [ 使用 Azure Mobile Apps](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 新增[Microsoft.Azure.Mobile.Client.SQLiteStore](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/) Xamarin.Forms 方案中的所有專案的 NuGet 套件。
1. （選擇性）啟用 Azure 行動應用程式執行個體和 Xamarin.Forms 應用程式中的驗證。 如需詳細資訊，請參閱 <<c0> [ 驗證的使用者，使用 Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md)。

下節提供使用 Microsoft.Azure.Mobile.Client.SQLiteStore NuGet 套件的其他安裝指示來設定通用 Windows 平台 (UWP) 專案。 不需要額外設定，才能在 iOS 和 Android 上使用 Microsoft.Azure.Mobile.Client.SQLiteStore NuGet 封裝。

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要使用 SQLite 通用 Windows 平台 (UWP) 上，請遵循下列步驟：

1. 安裝[適用於通用 Windows 平台的 SQLite](http://sqlite.org/2016/sqlite-uwp-3120200.vsix)開發環境中的 Visual Studio 擴充功能。
1. 在 Visual Studio 中的 UWP 專案，以滑鼠右鍵按一下**參考 > 加入參考**，瀏覽至**擴充功能**，並新增**適用於通用 Windows 平台的 SQLite**和**通用 Windows 平台應用程式的 visual c + + 2015年執行階段**UWP 專案的擴充功能。

## <a name="initializing-the-local-store"></a>初始化本機存放區

在執行任何同步處理資料表作業之前，必須先初始化本機存放區。 做法是在可攜式類別庫 (PCL) 專案中的 Xamarin.Forms 方案：

```csharp
using Microsoft.WindowsAzure.MobileServices;
using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
using Microsoft.WindowsAzure.MobileServices.Sync;

namespace TodoAzure
{
    public partial class TodoItemManager
    {
        static TodoItemManager defaultInstance = new TodoItemManager();
        IMobileServiceClient client;
        IMobileServiceSyncTable<TodoItem> todoTable;

        private TodoItemManager()
        {
            this.client = new MobileServiceClient(Constants.ApplicationURL);
            var store = new MobileServiceSQLiteStore("localstore.db");
            store.DefineTable<TodoItem>();
            this.client.SyncContext.InitializeAsync(store);
            this.todoTable = client.GetSyncTable<TodoItem>();
        }
        ...
  }
}
```

建立新的本機 SQLite 資料庫`MobileServiceSQLiteStore`類別，前提是它不存在。 然後，`DefineTable<T>`方法會建立在本機存放區中的欄位相符的資料表`TodoItem`類型，前提是它不存在。

A*同步處理內容*聯`MobileServiceClient`執行個體，並進行同步處理資料表追蹤變更。 同步處理內容會維護記錄的建立、 更新和刪除 (CUD) 作業的排序的清單，該佇列會傳送至 Azure Mobile Apps 執行個體更新版本。 `IMobileServiceSyncContext.InitializeAsync()`方法用來同步處理內容相關聯的本機存放區。

`todoTable`欄位是`IMobileServiceSyncTable`，因此所有 CRUD 作業都使用本機存放區。

## <a name="performing-synchronization"></a>執行同步處理

Azure Mobile Apps 與同步處理本機存放區執行個體時`SyncAsync`叫用的方法：

```csharp
public async Task SyncAsync()
{
  ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

  try
  {
    await this.client.SyncContext.PushAsync();

    // The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
    // Use a different query name for each unique query in your program.
    await this.todoTable.PullAsync("allTodoItems", this.todoTable.CreateQuery());
  }
  catch (MobileServicePushFailedException exc)
  {
    if (exc.PushResult != null)
    {
      syncErrors = exc.PushResult.Errors;
    }
  }

  // Simple error/conflict handling.
  if (syncErrors != null)
  {
    foreach (var error in syncErrors)
    {
      if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
      {
        // Update failed, revert to server's copy
        await error.CancelAndUpdateItemAsync(error.Result);
      }
      else
      {
        // Discard local change
        await error.CancelAndDiscardItemAsync();
      }

      Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
    }
  }
}
```

`IMobileServiceSyncTable.PushAsync`方法同步處理內容，而不是特定的資料表上運作，並傳送一次推送之後的所有 CUD 變更。

執行提取`IMobileServiceSyncTable.PullAsync`單一資料表上的方法。 第一個參數`PullAsync`方法是只適用於行動裝置的查詢名稱。 提供非 null 的查詢名稱會導致 Azure Mobile Client SDK 執行*增量同步處理*，其中每次提取作業會傳回結果，最新`updatedAt`從結果中的時間戳記會儲存在本機系統資料表。 後續的提取作業只擷取的記錄在該時間戳記之後。 或者，*完整同步處理*可藉由傳遞`null`做為查詢名稱，這會導致正在擷取每個提取作業的所有記錄。 下列任何同步處理作業，接收的資料插入本機存放區。

> [!NOTE]
> 如果針對具有擱置中本機更新的資料表執行提取時，提取會先同步處理內容上執行推播。 這會將已排入佇列的變更和新的資料從 Azure Mobile Apps 的執行個體之間的衝突降到最低。

`SyncAsync`方法也會包含這兩個本機存放區以及 Azure Mobile Apps 執行個體中，已變更同一筆記錄時處理衝突的基本實作。 當衝突是在本機存放區和 Azure Mobile Apps 執行個體中，已更新資料時`SyncAsync`方法會更新儲存在 Azure Mobile Apps 執行個體中的資料從本機存放區中的資料。 任何其他的衝突發生時，`SyncAsync`方法會捨棄本機變更。 這個方法會處理此案例從 Azure Mobile Apps 的執行個體已刪除的資料所在的本機變更。

在生產環境應用程式中，開發人員應該撰寫自訂`IMobileServiceSyncHandler`衝突處理實作適合他們的使用案例。 如需詳細資訊，請參閱 <<c0> [ 使用來解決衝突的開放式並行存取](https://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/#optimisticconcurrency)在 Azure 入口網站，並[深入探討在受管理的用戶端 SDK 中的離線支援上](https://blogs.msdn.microsoft.com/azuremobile/2014/04/07/deep-dive-on-the-offline-support-in-the-managed-client-sdk/)MSDN 部落格上。

## <a name="purging-data"></a>清除資料

具有的資料，就可以清除本機存放區中的資料表`IMobileServiceSyncTable.PurgeAsync`方法。 這個方法支援的案例，例如移除不再需要應用程式的過時資料。 例如，範例應用程式只會顯示`TodoItem`未完成的執行個體。 因此，已完成的項目不再需要儲存在本機。 清除已完成的項目，從本機存放區即可，如下所示：

```csharp
await todoTable.PurgeAsync(todoTable.Where(item => item.Done));
```

呼叫`PurgeAsync`也會觸發推送作業。 因此，會標示為完成本機的任何項目會從本機存放區移除前傳送至 Azure Mobile Apps 執行個體。 不過，如果有作業擱置與 Azure Mobile Apps 執行個體的同步處理，，會擲回 purge`InvalidOperationException`除非`force`參數設為`true`。 另一種方法是檢查`IMobileServiceSyncContext.PendingOperations`屬性，會傳回暫止的作業還沒有已推送至 Azure Mobile Apps 執行個體，並只執行清除作業，如果屬性為零的數目。

> [!NOTE]
> 叫用`PurgeAsync`具有`force`參數設為`true`將會遺失任何暫止的變更。

## <a name="initiating-synchronization"></a>初始同步處理

在範例應用程式`SyncAsync`方法會叫用透過`TodoList.OnAppearing`方法：

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  // Set syncItems to true to synchronize the data on startup when running in offline mode
  await RefreshItems(true, syncItems: true);
}
```

這表示應用程式會嘗試啟動時，與 Azure Mobile Apps 執行個體同步處理。

此外，同步處理可以啟動的 iOS 和 Android 中使用重新整理的資料，清單和 Windows 平台上使用的提取**同步**使用者介面上的按鈕。 如需詳細資訊，請參閱 <<c0> [ 提取以重新整理](~/xamarin-forms/user-interface/listview/interactivity.md#Pull_to_Refresh)。

## <a name="summary"></a>總結

這篇文章說明如何將離線同步處理功能新增至 Xamarin.Forms 應用程式。 離線同步處理可讓使用者互動與行動應用程式、 檢視、 加入或修改資料，即使在沒有網路連線。 變更會儲存在本機資料庫中，並變更該裝置上線後，可以與 Azure Mobile Apps 執行個體同步處理。


## <a name="related-links"></a>相關連結

- [TodoAzureAuthOfflineSync （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)
- [使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)
- [使用 Azure 行動應用程式驗證使用者](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)

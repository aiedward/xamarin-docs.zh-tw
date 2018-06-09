---
title: 同步處理離線資料與 Azure 行動應用程式
description: 本文說明如何將離線同步處理功能加入至使用 Azure 行動應用程式後端的 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: DBB343B0-2709-4C20-A669-5522B9956D9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2017
ms.openlocfilehash: e8b0eeeb4f0033fccd7a61b4acb286bb8457e6c2
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243593"
---
# <a name="synchronizing-offline-data-with-azure-mobile-apps"></a>同步處理離線資料與 Azure 行動應用程式

_離線同步處理可讓使用者互動與行動應用程式、 檢視、 加入或修改資料，即使在沒有網路連線。變更會儲存在本機資料庫中，並變更裝置在線上時，一旦可以同步處理與 Azure 行動應用程式執行個體。本文說明如何加入 Xamarin.Forms 應用程式中離線的同步處理功能。_

## <a name="overview"></a>總覽

[Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)提供`IMobileServiceTable`介面，代表可以儲存在 Azure 行動應用程式執行個體中的資料表執行的作業。 這些作業會直接連接到 Azure 行動應用程式執行個體，而且如果行動裝置沒有網路連線將會失敗。

若要支援離線同步處理，Azure 行動用戶端 SDK 支援*同步資料表*，這由提供`IMobileServiceSyncTable`介面。 這個介面會提供相同的建立、 讀取、 更新、 刪除 (CRUD) 作業，作為`IMobileServiceTable`介面，但作業讀取或寫入本機存放區。 本機存放區不會填入 Azure 行動應用程式執行個體的新資料之前呼叫*提取*資料。 同樣地，未傳送資料到 Azure 行動應用程式執行個體的呼叫之前*發送*本機變更。

離線同步處理也支援在 Azure 行動應用程式執行個體，並進行自訂衝突解決這兩個在本機存放區，並變更同一筆記錄時偵測衝突。 在本機存放區，或在 Azure 行動應用程式執行個體可以處理衝突。

如需離線同步處理的詳細資訊，請參閱[Azure 行動應用程式中的離線資料同步](/azure/app-service-mobile/app-service-mobile-offline-data-sync/)和[啟用 Xamarin.Forms 行動應用程式離線同步](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-offline-data/)。

## <a name="setup"></a>安裝程式

整合離線 Xamarin.Forms 應用程式與 Azure 行動應用程式執行個體之間的同步處理程序如下所示：

1. 建立 Azure 行動應用程式執行個體。 如需詳細資訊，請參閱[取用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)。
1. 新增[Microsoft.Azure.Mobile.Client.SQLiteStore](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/) NuGet 封裝加入 Xamarin.Forms 方案中的所有專案。
1. （選擇性）啟用 Azure 行動應用程式執行個體和 Xamarin.Forms 應用程式中的驗證。 如需詳細資訊，請參閱[驗證 Azure 行動應用程式的使用者](~/xamarin-forms/data-cloud/authentication/azure.md)。

下節提供使用 Microsoft.Azure.Mobile.Client.SQLiteStore NuGet 封裝設定通用 Windows 平台 (UWP) 專案的其他安裝指示。 不需要額外設定，才能在 iOS 和 Android 使用 Microsoft.Azure.Mobile.Client.SQLiteStore NuGet 封裝。

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要使用 SQLite 通用 Windows 平台 (UWP) 上，請遵循下列步驟：

1. 安裝[SQLite 對通用 Windows 平台](http://sqlite.org/2016/sqlite-uwp-3120200.vsix)開發環境中的 Visual Studio 擴充功能。
1. 在 UWP 專案中 Visual Studio 中，以滑鼠右鍵按一下**參考 > 加入參考**，瀏覽至**延伸**並加入**SQLite 對通用 Windows 平台**和**通用 Windows 平台應用程式的 visual c + + 2015年執行階段**UWP 專案的擴充功能。

## <a name="initializing-the-local-store"></a>初始化本機存放區

可以執行任何同步處理的資料表作業之前，必須先初始化本機存放區。 這被達成可攜式類別程式庫 (PCL) 專案中的 Xamarin.Forms 方案：

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

建立新的本機 SQLite 資料庫`MobileServiceSQLiteStore`類別，前提是它不存在。 然後，`DefineTable<T>`方法中的欄位比對本機存放區中建立資料表`TodoItem`類型，前提是它不存在。

A*同步處理內容*聯`MobileServiceClient`執行個體，而且與同步處理資料表所做的追蹤變更。 同步處理內容會維護的佇列會建立、 更新和刪除 (CUD) 作業的已排序的清單，將會傳送至 Azure 行動應用程式執行個體更新版本。 `IMobileServiceSyncContext.InitializeAsync()`方法用來將本機存放區與同步處理內容產生關聯。

`todoTable`欄位是`IMobileServiceSyncTable`，因此所有 CRUD 作業都使用本機存放區。

## <a name="performing-synchronization"></a>執行同步處理

與 Azure 行動應用程式同步處理本機存放區執行個體時`SyncAsync`叫用方法時：

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

`IMobileServiceSyncTable.PushAsync`方法運作的同步處理內容，而非特定的資料表，並將所有 CUD 變更都傳送自最後一個推入。

由執行提取`IMobileServiceSyncTable.PullAsync`單一資料表上的方法。 第一個參數`PullAsync`方法是使用僅在行動裝置的查詢名稱。 提供非 null 查詢名稱導致 Azure 行動用戶端 SDK 執行*增量同步處理*，其中每次提取作業會傳回結果，最新版`updatedAt`從結果的時間戳記會儲存在本機系統資料表。 後續的提取作業，然後只擷取記錄的時間戳記之後。 或者，*完整同步處理*可藉由傳遞`null`為查詢名稱時，這會導致在每個提取作業擷取的所有記錄。 下列任何同步處理作業時，收到的資料會插入至本機存放區。

> [!NOTE]
> 如果針對具有暫止的本機更新的資料表執行提取時，提取將第一次同步處理內容上執行推入。 這會將已排入佇列的變更和新的資料從 Azure 行動應用程式執行個體之間的衝突降到最低。

`SyncAsync`方法也會包含這兩個在本機存放區及 Azure 行動應用程式執行個體中變更同一筆記錄時，處理衝突的基本實作。 當衝突是在本機存放區和 Azure 行動應用程式執行個體中，已更新資料`SyncAsync`方法會更新儲存在 Azure 行動應用程式執行個體中的資料從本機存放區中的資料。 任何其他衝突發生時，`SyncAsync`方法會捨棄本機變更。 這個方法會處理此案例會從 Azure 行動應用程式執行個體中刪除的資料所在的本機變更。

在實際執行應用程式開發人員應該撰寫自訂`IMobileServiceSyncHandler`適用於其使用案例的衝突處理實作。 如需詳細資訊，請參閱[使用開放式並行存取衝突解決](https://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/#optimisticconcurrency)在 Azure 入口網站和[深層深入了解上的受管理的用戶端 SDK 中的離線支援](https://blogs.msdn.microsoft.com/azuremobile/2014/04/07/deep-dive-on-the-offline-support-in-the-managed-client-sdk/)MSDN 部落格上。

## <a name="purging-data"></a>清除資料

本機存放區中的資料表可以具有的資料清除`IMobileServiceSyncTable.PurgeAsync`方法。 這個方法支援的案例，例如移除應用程式已不再需要的過時資料。 例如，範例應用程式只會顯示`TodoItem`未完成的執行個體。 因此，完成的項目不再需要儲存在本機。 清除已完成的項目，從本機存放區即可達成，如下所示：

```csharp
await todoTable.PurgeAsync(todoTable.Where(item => item.Done));
```

呼叫`PurgeAsync`也會觸發推入作業。 因此，會標示為完成本機的任何項目會從本機存放區移除之前傳送至 Azure 行動應用程式執行個體。 不過，如果有暫止與 Azure 行動應用程式執行個體的同步處理的作業，，將會擲回清除`InvalidOperationException`除非`force`參數設定為`true`。 替代的策略是檢查`IMobileServiceSyncContext.PendingOperations`屬性，它會傳回暫止的作業尚未推入到 Azure 行動應用程式執行個體，才執行清除的屬性為零的數目。

> [!NOTE]
> 叫用`PurgeAsync`與`force`參數設定為`true`將會遺失任何暫止的變更。

## <a name="initiating-synchronization"></a>初始同步處理

範例應用程式、`SyncAsync`透過叫用方法`TodoList.OnAppearing`方法：

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  // Set syncItems to true to synchronize the data on startup when running in offline mode
  await RefreshItems(true, syncItems: true);
}
```

這表示應用程式會嘗試在啟動時，與 Azure 行動應用程式執行個體同步處理。

此外，同步處理就能啟動 iOS 和 Android 使用提取的資料，清單和 Windows 平台上使用 重新整理**同步**使用者介面上的按鈕。 如需詳細資訊，請參閱[提取到重新整理](~/xamarin-forms/user-interface/listview/interactivity.md#Pull_to_Refresh)。

## <a name="summary"></a>總結

本文說明如何加入 Xamarin.Forms 應用程式中離線的同步處理功能。 離線同步處理可讓使用者互動與行動應用程式、 檢視、 加入或修改資料，即使在沒有網路連線。 變更會儲存在本機資料庫中，並變更裝置在線上時，一旦可以同步處理與 Azure 行動應用程式執行個體。


## <a name="related-links"></a>相關連結

- [TodoAzureAuthOfflineSync （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthOfflineSync/)
- [使用 Azure 行動應用程式](~/xamarin-forms/data-cloud/consuming/azure.md)
- [驗證使用者與 Azure 行動應用程式](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Azure 行動用戶端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)

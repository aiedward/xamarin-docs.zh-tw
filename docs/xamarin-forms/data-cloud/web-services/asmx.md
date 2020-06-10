---
標題：「使用 ASP.NET Web 服務（.ASMX）」描述：「本文示範如何從應用程式取用 .ASMX SOAP 服務」 Xamarin.Forms 。
assetid： D5533964-5528-4D35-9C2B-FAFB632472AC ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：04/02/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="consume-an-aspnet-web-service-asmx"></a>使用 ASP.NET Web 服務 (ASMX)

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)

_.ASMX 提供建立 web 服務的功能，可使用簡單的物件存取通訊協定（SOAP）來傳送訊息。SOAP 是一種與平臺無關且與語言無關的通訊協定，可用於建立和存取 web 服務。.ASMX 服務的取用者不需要知道用來執行服務的平臺、物件模型或程式設計語言等任何專案。他們只需要瞭解如何傳送和接收 SOAP 訊息。本文示範如何從應用程式取用 .ASMX SOAP 服務 Xamarin.Forms 。_

SOAP 訊息是包含下列元素的 XML 檔：

- 名為*封套*的根項目，會將 XML 檔識別為 SOAP 訊息。
- 選擇性的*標頭*元素，其中包含應用程式特定的資訊，例如驗證資料。 如果*標頭*元素存在，它必須是*信封*元素的第一個子項目。
- 必要的*Body*專案，其中包含用於收件者的 SOAP 訊息。
- *選擇性的錯誤元素*，用來表示錯誤訊息。 如果*錯誤*元素存在，它必須是*Body*元素的子項目。

SOAP 可以透過許多傳輸通訊協定來運作，包括 HTTP、SMTP、TCP 和 UDP。 不過，.ASMX 服務只能透過 HTTP 操作。 Xamarin 平臺支援透過 HTTP 的標準 SOAP 1.1 實作為，這包括對許多標準 .ASMX 服務設定的支援。

這個範例包含在實體或模擬裝置上執行的行動應用程式，以及提供方法來取得、新增、編輯和刪除資料的一種 .ASMX 服務。 當行動應用程式執行時，它們會連接到本機裝載的 .ASMX 服務，如下列螢幕擷取畫面所示：

![](asmx-images/portal.png "Sample Application")

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性（ATS）會強制執行網際網路資源（例如應用程式的後端伺服器）與應用程式之間的安全連線，藉此防止意外洩漏機密資訊。 由於預設會在針對 iOS 9 建立的應用程式中啟用 ATS，因此所有連線都將受限於 ATS 安全性需求。 如果連線不符合這些需求，則會失敗並產生例外狀況。
> 如果無法 `HTTPS` 針對網際網路資源使用通訊協定和安全通訊，可以退出宣告 ATS。 這可以藉由更新應用程式的**plist**檔案來達成。 如需詳細資訊，請參閱[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consume-the-web-service"></a>取用 Web 服務

.ASMX 服務提供下列作業：

|作業|描述|參數|
|--- |--- |--- |
|GetTodoItems|取得待辦事項的清單|
|CreateTodoItem|建立新的待辦事項專案|XML 序列化 TodoItem|
|EditTodoItem|更新待辦事項|XML 序列化 TodoItem|
|DeleteTodoItem|刪除待辦事項|XML 序列化 TodoItem|

如需應用程式中所使用之資料模型的詳細資訊，請參閱[模型化資料](~/xamarin-forms/data-cloud/web-services/introduction.md)。

## <a name="create-the-todoservice-proxy"></a>建立 TodoService proxy

Proxy 類別（稱為 `TodoService` ）會擴充 `SoapHttpClientProtocol` 並提供方法，以透過 HTTP 與 .asmx 服務進行通訊。 Proxy 的產生方式是將 web 參考加入至 Visual Studio 2019 或 Visual Studio 2017 中的每個平臺特定專案。 Web 參考會針對服務的 Web 服務描述語言（WSDL）檔中所定義的每個動作，產生方法和事件。

例如， `GetTodoItems` 服務動作會在 proxy 中產生 `GetTodoItemsAsync` 方法和 `GetTodoItemsCompleted` 事件。 產生的方法具有 void 傳回型別，並在 `GetTodoItems` 父類別上叫用動作 `SoapHttpClientProtocol` 。 當叫用的方法收到來自服務的回應時，它會引發 `GetTodoItemsCompleted` 事件，並在事件的屬性中提供回應資料 `Result` 。

## <a name="create-the-isoapservice-implementation"></a>建立 ISoapService 的執行

若要啟用共用的跨平臺專案來使用服務，此範例會定義 `ISoapService` 介面，其遵循[c # 中的工作非同步程式設計模型](/dotnet/csharp/programming-guide/concepts/async/)。 每個平臺都會執行， `ISoapService` 以公開平臺特定的 proxy。 此範例會使用 `TaskCompletionSource` 物件，將 proxy 公開為工作非同步介面。 如需使用的詳細資訊， `TaskCompletionSource` 請參閱下列各節中的每個動作類型的執行方式。

範例 `SoapService` ：

1. 將具現化為 `TodoService` 類別層級實例
1. 建立名為的集合 `Items` 以儲存 `TodoItem` 物件
1. 為選擇性屬性指定自訂端點，其 `Url` 位於`TodoService`

```csharp
public class SoapService : ISoapService
{
    ASMXService.TodoService todoService;
    public List<TodoItem> Items { get; private set; } = new List<TodoItem>();

    public SoapService ()
    {
        todoService = new ASMXService.TodoService ();
        todoService.Url = Constants.SoapUrl;
        ...
    }
}
```

### <a name="create-data-transfer-objects"></a>建立資料傳輸物件

範例應用程式會使用 `TodoItem` 類別來建立資料模型。 若要 `TodoItem` 在 web 服務中儲存專案，必須先將它轉換成 proxy 產生的 `TodoItem` 類型。 這是由方法來完成 `ToASMXServiceTodoItem` ，如下列程式碼範例所示：

```csharp
ASMXService.TodoItem ToASMXServiceTodoItem (TodoItem item)
{
    return new ASMXService.TodoItem {
        ID = item.ID,
        Name = item.Name,
        Notes = item.Notes,
        Done = item.Done
    };
}
```

這個方法會建立新的 `ASMService.TodoItem` 實例，並將每個屬性設定為實例中的相同屬性 `TodoItem` 。

同樣地，從 web 服務抓取資料時，它必須從 proxy 產生的類型轉換成 `TodoItem` `TodoItem` 實例。 這會透過方法來完成 `FromASMXServiceTodoItem` ，如下列程式碼範例所示：

```csharp
static TodoItem FromASMXServiceTodoItem (ASMXService.TodoItem item)
{
    return new TodoItem {
        ID = item.ID,
        Name = item.Name,
        Notes = item.Notes,
        Done = item.Done
    };
}
```

這個方法會從 proxy 產生的型別 `TodoItem` 中抓取資料，並將它設定在新建立的 `TodoItem` 實例中。

### <a name="retrieve-data"></a>抓取資料

`ISoapService`介面預期方法會傳回 `RefreshDataAsync` `Task` 具有專案集合的。 不過，此 `TodoService.GetTodoItemsAsync` 方法會傳回 void。 若要滿足介面模式，您必須呼叫 `GetTodoItemsAsync` ，等待 `GetTodoItemsCompleted` 事件引發，並填入集合。 這可讓您將有效的集合傳回到 UI。

下列範例會建立新的 `TaskCompletionSource` ，並在方法中開始非同步呼叫 `RefreshDataAsync` ，並等候所 `Task` 提供的 `TaskCompletionSource` 。 叫用 `TodoService_GetTodoItemsCompleted` 事件處理常式時，它會填入 `Items` 集合，並更新 `TaskCompletionSource` ：

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> getRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.GetTodoItemsCompleted += TodoService_GetTodoItemsCompleted;
    }

    public async Task<List<TodoItem>> RefreshDataAsync()
    {
        getRequestComplete = new TaskCompletionSource<bool>();
        todoService.GetTodoItemsAsync();
        await getRequestComplete.Task;
        return Items;
    }

    private void TodoService_GetTodoItemsCompleted(object sender, ASMXService.GetTodoItemsCompletedEventArgs e)
    {
        try
        {
            getRequestComplete = getRequestComplete ?? new TaskCompletionSource<bool>();

            Items = new List<TodoItem>();
            foreach (var item in e.Result)
            {
                Items.Add(FromASMXServiceTodoItem(item));
            }
            getRequestComplete?.TrySetResult(true);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(@"\t\tERROR {0}", ex.Message);
        }
    }

    ...
}
```

如需詳細資訊，請參閱[非同步程式設計模型](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm)和[TPL 和傳統 .NET Framework 非同步程式設計](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming)。

### <a name="create-or-edit-data"></a>建立或編輯資料

當您建立或編輯資料時，您必須執行 `ISoapService.SaveTodoItemAsync` 方法。 這個方法會偵測是否 `TodoItem` 為新的或已更新的專案，並在物件上呼叫適當的方法 `todoService` 。 `CreateTodoItemCompleted`和 `EditTodoItemCompleted` 事件處理常式也應該實作為，讓您知道何時 `todoService` 收到來自 .asmx 服務的回應（這些都可以合併成單一處理程式，因為它們會執行相同的作業）。 下列範例示範介面和事件處理常式的執行，以及 `TaskCompletionSource` 用來以非同步方式操作的物件：

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> saveRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.CreateTodoItemCompleted += TodoService_SaveTodoItemCompleted;
        todoService.EditTodoItemCompleted += TodoService_SaveTodoItemCompleted;
    }

    public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
    {
        try
        {
            var todoItem = ToASMXServiceTodoItem(item);
            saveRequestComplete = new TaskCompletionSource<bool>();
            if (isNewItem)
            {
                todoService.CreateTodoItemAsync(todoItem);
            }
            else
            {
                todoService.EditTodoItemAsync(todoItem);
            }
            await saveRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_SaveTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        saveRequestComplete?.TrySetResult(true);
    }

    ...
}
```

### <a name="delete-data"></a>刪除資料

刪除資料需要類似的執行方式。 定義 `TaskCompletionSource` 、執行事件處理常式和 `ISoapService.DeleteTodoItemAsync` 方法：

```csharp
public class SoapService : ISoapService
{
    TaskCompletionSource<bool> deleteRequestComplete = null;
    ...

    public SoapService()
    {
        ...
        todoService.DeleteTodoItemCompleted += TodoService_DeleteTodoItemCompleted;
    }

    public async Task DeleteTodoItemAsync (string id)
    {
        try
        {
            deleteRequestComplete = new TaskCompletionSource<bool>();
            todoService.DeleteTodoItemAsync(id);
            await deleteRequestComplete.Task;
        }
        catch (SoapException se)
        {
            Debug.WriteLine("\t\t{0}", se.Message);
        }
        catch (Exception ex)
        {
            Debug.WriteLine("\t\tERROR {0}", ex.Message);
        }
    }

    private void TodoService_DeleteTodoItemCompleted(object sender, System.ComponentModel.AsyncCompletedEventArgs e)
    {
        deleteRequestComplete?.TrySetResult(true);
    }

    ...
}
```

## <a name="test-the-web-service"></a>測試 Web 服務

使用本機裝載的服務來測試實體或模擬裝置，需要有自訂的 IIS 設定、端點位址和防火牆規則。 如需如何設定環境以進行測試的詳細資訊，請參閱設定[IIS Express 的遠端存取](wcf.md#configure-remote-access-to-iis-express)。 測試 WCF 和 .ASMX 的唯一差異在於 TodoService 的通訊埠編號。

## <a name="related-links"></a>相關連結

- [TodoASMX （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)

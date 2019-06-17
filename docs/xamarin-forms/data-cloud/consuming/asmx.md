---
title: 使用 ASP.NET Web 服務 (ASMX)
description: 這篇文章會示範如何使用 Xamarin.Forms 應用程式從 ASMX SOAP 服務。
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
ms.openlocfilehash: 71fb2b4742a66a560541febc9dbe87aed503da4c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61328631"
---
# <a name="consuming-an-aspnet-web-service-asmx"></a>使用 ASP.NET Web 服務 (ASMX)

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)

_ASMX 讓您能夠建立使用簡易物件存取通訊協定 (SOAP) 來傳送訊息的 web 服務。SOAP 是平台和語言無關的通訊協定，來建置，並存取 web 服務。ASMX 服務的取用者不需要知道任何關於平台、 物件模型或用來實作服務的程式設計語言。它們只需要了解如何傳送和接收 SOAP 訊息。這篇文章會示範如何使用 Xamarin.Forms 應用程式從 ASMX SOAP 服務。_

SOAP 訊息是 XML 文件包含下列項目：

- 名為根項目*信封*可識別為 SOAP 訊息的 XML 文件。
- 選擇性*標頭*元素，其中包含應用程式特定資訊，例如驗證資料。 如果*標頭*項目存在於它必須是第一個子項目*信封*項目。
- 必要*主體*元素，其中包含適用於收件者的 SOAP 訊息。
- 選擇性*錯誤*項目，用來指出錯誤訊息。 如果*錯誤*項目已存在，它必須是子項目*主體*項目。

SOAP 可透過不同的傳輸通訊協定，包括 HTTP、 SMTP、 TCP 和 UDP 操作。 不過，ASMX 服務僅透過 HTTP 操作。 Xamarin 平台支援標準的 SOAP 1.1 實作透過 HTTP，而這包括支援許多標準的 ASMX 服務組態。

此範例包含實體或模擬裝置執行的行動應用程式和 ASMX 服務，可提供方法來取得、 新增、 編輯和刪除資料。 當行動裝置應用程式執行時，它們連線到在本機裝載 ASMX 服務如下列螢幕擷取畫面所示：

![](asmx-images/portal.png "範例應用程式")

> [!NOTE]
> 在 iOS 9 和更新版本中，App Transport Security (ATS) 會強制執行安全的連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，藉此防止意外洩漏機密資訊。 針對 iOS 9 所建置的應用程式中的預設會啟用 ATS，因為所有連線將會都受限於 ATS 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以共選擇 ATS`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的達成**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consume-the-web-service"></a>取用 web 服務

ASMX 服務提供下列作業：

|運算|描述|參數|
|--- |--- |--- |
|GetTodoItems|取得待辦事項的清單|
|CreateTodoItem|建立新的待辦事項項目|XML 序列化 TodoItem|
|EditTodoItem|更新待辦事項|XML 序列化 TodoItem|
|DeleteTodoItem|刪除待辦事項|XML 序列化 TodoItem|

如需有關使用應用程式中的資料模型的詳細資訊，請參閱[將資料模型化](~/xamarin-forms/data-cloud/walkthrough.md)。

## <a name="create-the-todoservice-proxy"></a>建立 TodoService proxy

Proxy 類別，稱為`TodoService`，可擴充`SoapHttpClientProtocol`，並提供與 ASMX 服務透過 HTTP 進行通訊的方法。 加入 Visual Studio 2019 或 Visual Studio 2017 中的每個平台特定專案的 web 參考時，會產生 proxy。 Web 參考會產生服務的 Web 服務描述語言 (WSDL) 文件中定義每個動作的方法與事件。

例如，`GetTodoItems`服務動作會導致`GetTodoItemsAsync`方法和`GetTodoItemsCompleted`在 proxy 中的事件。 產生的方法具有 void 傳回類型，並叫用`GetTodoItems`父代上的動作`SoapHttpClientProtocol`類別。 當叫用的方法從服務收到回應時，它就會引發`GetTodoItemsCompleted`事件，並提供回應內的資料事件的`Result`屬性。

## <a name="create-the-isoapservice-implementation"></a>建立 ISoapService 實作

若要啟用共用的跨平台的專案，才能使用服務，此範例會定義`ISoapService`介面，其遵循[工作非同步程式設計模型C# ](/dotnet/csharp/programming-guide/concepts/async/)。 每個平台實作`ISoapService`公開平台特定的 proxy。 此範例會使用`TaskCompletionSource`物件來公開為工作的非同步介面的 proxy。 有關使用`TaskCompletionSource`位於下列各節中的每個動作類型實作。

此範例`SoapService`:

1. 具現化`TodoService`為類別層級的執行個體
1. 建立集合，稱為`Items`來儲存`TodoItem`物件
1. 指定選擇性的自訂端點`Url`屬性 `TodoService`

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

範例應用程式使用`TodoItem`模型資料的類別。 若要儲存`TodoItem`它必須先轉換成產生的 proxy web 服務中的項目`TodoItem`型別。 這可以藉由`ToASMXServiceTodoItem`方法，如下列程式碼範例所示：

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

這個方法會建立新`ASMService.TodoItem`執行個體，並將每個屬性設定為相同的屬性，從`TodoItem`執行個體。

同樣地，從 web 服務擷取資料時，必須將它從轉換產生的 proxy`TodoItem`輸入至`TodoItem`執行個體。 這可完成`FromASMXServiceTodoItem`方法，如下列程式碼範例所示：

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

這個方法會擷取資料，從產生的 proxy`TodoItem`輸入，並將它設定在新建立`TodoItem`執行個體。

### <a name="retrieve-data"></a>擷取資料

`ISoapService`介面必須有`RefreshDataAsync`方法來傳回`Task`與項目集合。 不過，`TodoService.GetTodoItemsAsync`方法會傳回 void。 若要滿足介面模式，您必須呼叫`GetTodoItemsAsync`，等候`GetTodoItemsCompleted`事件引發，並填入集合。 這可讓您返回 UI 中的有效的集合。

下列範例會建立新`TaskCompletionSource`，開始非同步呼叫，在`RefreshDataAsync`方法，並等候`Task`所提供`TaskCompletionSource`。 當`TodoService_GetTodoItemsCompleted`會叫用事件處理常式，它會填入`Items`集合和更新`TaskCompletionSource`:

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

如需詳細資訊，請參閱 <<c0> [ 非同步程式設計模型](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm)並[TPL 和傳統.NET Framework 非同步程式設計](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming)。

### <a name="create-or-edit-data"></a>建立或編輯資料

當您建立或編輯資料時，您必須實作`ISoapService.SaveTodoItemAsync`方法。 這個方法會偵測是否`TodoItem`是新的或更新的項目，並呼叫適當的方法上`todoService`物件。 `CreateTodoItemCompleted`並`EditTodoItemCompleted`事件處理常式應該也會實作，因此您知道何時`todoService`收到來自 ASMX 服務 （這些可以合併成單一的處理常式因為它們執行相同的作業） 的回應。 下列範例示範介面和事件處理常式實作，以及`TaskCompletionSource`物件，用來以非同步方式運作：

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

刪除資料需要類似的實作。 定義`TaskCompletionSource`，實作事件處理常式，而`ISoapService.DeleteTodoItemAsync`方法：

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

## <a name="test-the-web-service"></a>測試 web 服務

測試與在本機裝載服務的實體或模擬裝置時，需要自訂的 IIS 組態、 端點位址和防火牆規則要備妥。 如需有關如何設定用於測試環境的詳細資訊，請參閱 <<c0> [ 設定為 IIS Express 的遠端存取](wcf.md#configure-remote-access-to-iis-express)。 測試 WCF 與 asmx 運用的唯一差異是 TodoService 的通訊埠編號。

## <a name="related-links"></a>相關連結

- [TodoASMX （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)

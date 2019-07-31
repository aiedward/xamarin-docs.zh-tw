---
title: 使用 ASP.NET Web 服務 (.ASMX)
description: 這篇文章會示範如何使用 Xamarin.Forms 應用程式從 ASMX SOAP 服務。
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2019
ms.openlocfilehash: 124cffaf827ebeb8109f3cd4ac4dfd2701e43f9c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656649"
---
# <a name="consume-an-aspnet-web-service-asmx"></a>使用 ASP.NET Web 服務 (.ASMX)

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)

_ASMX 讓您能夠建立使用簡易物件存取通訊協定 (SOAP) 來傳送訊息的 web 服務。SOAP 是平台和語言無關的通訊協定，來建置，並存取 web 服務。ASMX 服務的取用者不需要知道任何關於平台、 物件模型或用來實作服務的程式設計語言。它們只需要了解如何傳送和接收 SOAP 訊息。這篇文章會示範如何使用 Xamarin.Forms 應用程式從 ASMX SOAP 服務。_

SOAP 訊息是 XML 文件包含下列項目：

- 名為根項目*信封*可識別為 SOAP 訊息的 XML 文件。
- 選擇性*標頭*元素，其中包含應用程式特定資訊，例如驗證資料。 如果*標頭*項目存在於它必須是第一個子項目*信封*項目。
- 必要*主體*元素，其中包含適用於收件者的 SOAP 訊息。
- 選擇性*錯誤*項目，用來指出錯誤訊息。 如果*錯誤*項目已存在，它必須是子項目*主體*項目。

SOAP 可透過不同的傳輸通訊協定，包括 HTTP、 SMTP、 TCP 和 UDP 操作。 不過，ASMX 服務僅透過 HTTP 操作。 Xamarin 平台支援標準的 SOAP 1.1 實作透過 HTTP，而這包括支援許多標準的 ASMX 服務組態。

這個範例包含在實體或模擬裝置上執行的行動應用程式, 以及提供方法來取得、新增、編輯和刪除資料的一種 .ASMX 服務。 當行動應用程式執行時, 它們會連接到本機裝載的 .ASMX 服務, 如下列螢幕擷取畫面所示:

![](asmx-images/portal.png "範例應用程式")

> [!NOTE]
> 在 iOS 9 和更新版本中，App Transport Security (ATS) 會強制執行安全的連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，藉此防止意外洩漏機密資訊。 針對 iOS 9 所建置的應用程式中的預設會啟用 ATS，因為所有連線將會都受限於 ATS 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以共選擇 ATS`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的達成**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consume-the-web-service"></a>使用 web 服務

ASMX 服務提供下列作業：

|運算|描述|參數|
|--- |--- |--- |
|GetTodoItems|取得待辦事項的清單|
|CreateTodoItem|建立新的待辦事項項目|XML 序列化 TodoItem|
|EditTodoItem|更新待辦事項|XML 序列化 TodoItem|
|DeleteTodoItem|刪除待辦事項|XML 序列化 TodoItem|

如需有關使用應用程式中的資料模型的詳細資訊，請參閱[將資料模型化](~/xamarin-forms/data-cloud/web-services/introduction.md)。

## <a name="create-the-todoservice-proxy"></a>建立 TodoService proxy

Proxy 類別 (稱為`TodoService`) 會擴充`SoapHttpClientProtocol`並提供方法, 以透過 HTTP 與 .asmx 服務進行通訊。 Proxy 的產生方式是將 web 參考加入至 Visual Studio 2019 或 Visual Studio 2017 中的每個平臺特定專案。 Web 參考會針對服務的 Web 服務描述語言 (WSDL) 檔中所定義的每個動作, 產生方法和事件。

例如, `GetTodoItems`服務動作會`GetTodoItemsAsync`在 proxy 中產生方法和`GetTodoItemsCompleted`事件。 產生的方法具有 void 傳回型別, 並`GetTodoItems`在父`SoapHttpClientProtocol`類別上叫用動作。 當叫用的方法收到來自服務的回應時, 它會`GetTodoItemsCompleted`引發事件, 並在事件的`Result`屬性中提供回應資料。

## <a name="create-the-isoapservice-implementation"></a>建立 ISoapService 的執行

為了讓共用的跨平臺專案能夠與服務搭配使用, 此範例會定義`ISoapService`介面, 其遵循中[ C#的工作非同步程式設計模型](/dotnet/csharp/programming-guide/concepts/async/)。 每個平臺`ISoapService`都會執行, 以公開平臺特定的 proxy。 此範例會`TaskCompletionSource`使用物件, 將 proxy 公開為工作非同步介面。 如需使用`TaskCompletionSource`的詳細資訊, 請參閱下列各節中的每個動作類型的執行方式。

範例`SoapService`:

1. 將`TodoService`具現化為類別層級實例
1. 建立名`Items`為的集合以`TodoItem`儲存物件
1. 為選擇性`Url`屬性指定自訂端點, 其位於`TodoService`

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

這個方法會建立新`ASMService.TodoItem`的實例, 並將每個屬性設定為`TodoItem`實例中的相同屬性。

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

這個方法會從 proxy 產生`TodoItem`的型別中抓取資料, 並將它設定在新建立`TodoItem`的實例中。

### <a name="retrieve-data"></a>取出資料

介面預期方法會傳回具有專案集合的。 `Task` `ISoapService` `RefreshDataAsync` 不過, 此`TodoService.GetTodoItemsAsync`方法會傳回 void。 若要滿足介面模式, 您必須呼叫`GetTodoItemsAsync`, 等待`GetTodoItemsCompleted`事件引發, 並填入集合。 這可讓您將有效的集合傳回到 UI。

下列`TaskCompletionSource`範例會建立新的, 並`RefreshDataAsync`在方法中開始非同步呼叫, 並`TaskCompletionSource`等候所`Task`提供的。 叫用`Items` `TaskCompletionSource`事件處理常式時, 它會填入集合, 並更新: `TodoService_GetTodoItemsCompleted`

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

如需詳細資訊, 請參閱[非同步程式設計模型](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm)和[TPL 和傳統 .NET Framework 非同步程式設計](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming)。

### <a name="create-or-edit-data"></a>建立或編輯資料

當您建立或編輯資料時, 您必須執行`ISoapService.SaveTodoItemAsync`方法。 這個方法`TodoItem`會偵測是否為新的或已更新的專案, 並`todoService`在物件上呼叫適當的方法。 和事件處理常式也應該實作為, `todoService`讓您知道何時收到來自 .asmx 服務的回應 (這些都可以合併成單一處理程式, 因為它們會執行相同的作業)。 `EditTodoItemCompleted` `CreateTodoItemCompleted` 下列範例示範介面和事件處理常式的執行, 以及用來以`TaskCompletionSource`非同步方式操作的物件:

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

刪除資料需要類似的執行方式。 定義、執行事件處理常式`ISoapService.DeleteTodoItemAsync`和方法: `TaskCompletionSource`

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

使用本機裝載的服務來測試實體或模擬裝置, 需要有自訂的 IIS 設定、端點位址和防火牆規則。 如需如何設定環境以進行測試的詳細資訊, 請參閱設定[IIS Express 的遠端存取](wcf.md#configure-remote-access-to-iis-express)。 測試 WCF 和 .ASMX 的唯一差異在於 TodoService 的通訊埠編號。

## <a name="related-links"></a>相關連結

- [TodoASMX （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [IAsyncResult](https://docs.microsoft.com/dotnet/api/system.iasyncresult)

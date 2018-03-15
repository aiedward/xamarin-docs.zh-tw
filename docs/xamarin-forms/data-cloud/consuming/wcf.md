---
title: "使用 Windows Communication Foundation (WCF) Web 服務"
description: "WCF 是 Microsoft 的統一的架構建置服務導向應用程式。 它可讓開發人員建置安全、 可靠、 交易，以及可互通的分散式應用程式。 這篇文章會示範如何使用 Xamarin.Forms 應用程式從 WCF 簡易物件存取通訊協定 (SOAP) 服務。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: a02627d5e5b40fe9e7b2fdbd3a6995a2d96fbf03
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="consuming-a-windows-communication-foundation-wcf-web-service"></a>使用 Windows Communication Foundation (WCF) Web 服務

_WCF 是 Microsoft 的統一的架構建置服務導向應用程式。它可讓開發人員建置安全、 可靠、 交易，以及可互通的分散式應用程式。這篇文章會示範如何使用 Xamarin.Forms 應用程式從 WCF 簡易物件存取通訊協定 (SOAP) 服務。_

WCF 說明各種不同的合約，其中包含下列服務：

- **資料合約**– 定義會促使內訊息的內容的資料結構。
- **訊息合約**– 撰寫從現有的資料合約的訊息。
- **錯誤合約**– 允許指定自訂 SOAP 錯誤。
- **服務合約**– 指定服務支援的作業和訊息所需的互動與每個作業。 它們也會指定可以與每個服務上的作業相關聯的任何自訂的錯誤行為。

ASP.NET Web 服務 (ASMX) 和 WCF 之間的差異，但請務必了解 WCF 支援相同的功能，提供 ASMX – 透過 HTTP SOAP 訊息。 如需使用 ASMX 服務的詳細資訊，請參閱[取用 ASP.NET Web 服務 (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)。

一般情況下，Xamarin 平台都支援相同隨附 Silverlight 執行階段的 WCF 用戶端子集。 這包括 WCF 的最常見編碼和通訊協定實作，透過 HTTP 的文字編碼 SOAP 訊息傳輸通訊協定使用`BasicHttpBinding`類別。 此外，WCF 支援需要使用工具僅適用於 Windows 的環境，以產生 proxy。

WCF 服務的設定指示可以找到範例應用程式隨附的讀我檔案。 不過，執行範例應用程式時它將會連接到 Xamarin 裝載的 WCF 服務可提供唯讀存取資料，如下列螢幕擷取畫面所示：

![](wcf-images/portal.png "範例應用程式")

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性 (ATS) 會強制執行安全連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，因此可以防止意外洩漏機密資訊。 由於 AT 預設會在 ios 9 建置的應用程式啟用時，所有連線將都會受限於 AT 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以超出選擇 AT`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服務

WCF 服務提供下列作業：

|運算|描述|參數|
|--- |--- |--- |
|GetTodoItems|取得待辦項目清單|
|CreateTodoItem|建立新的待辦項目|XML 序列化 TodoItem|
|EditTodoItem|更新 待辦項目|XML 序列化 TodoItem|
|DeleteTodoItem|刪除待辦項目|XML 序列化 TodoItem|

如需使用應用程式中的資料模型的詳細資訊，請參閱[將資料模型化](~/xamarin-forms/data-cloud/walkthrough.md)。

> [!NOTE]
> 範例應用程式使用 Xamarin 裝載的 WCF 服務，提供唯讀存取 web 服務。 因此，建立、 更新和刪除資料的作業不會改變應用程式中使用的資料。 不過，可裝載的 ASMX 服務版本，可以在**TodoWCFService**隨附的範例應用程式中的資料夾。 這一版可裝載 WCF 服務允許完整的建立、 更新、 讀取和刪除資料的存取權。

A *proxy*取用 WCF 服務，允許應用程式連接到服務，必須產生。 Proxy 的建構方式取用的服務中繼資料定義的方法和相關聯的服務組態。 產生 web 服務的 Web 服務描述語言 (WSDL) 文件的形式公開此中繼資料。 在 Visual Studio 2017 使用 Microsoft WCF Web 服務參考提供者，將 web 服務的服務參考新增至.NET 標準程式庫，您可以建立 proxy。 建立使用 Microsoft WCF Web 服務參考提供者，在 Visual Studio 2017 proxy 的替代方式是使用 ServiceModel Metadata Utility Tool (svcutil.exe)。 如需詳細資訊，請參閱[ServiceModel Metadata Utility Tool (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/)。

產生的 proxy 類別會提供方法來使用 web 服務使用非同步程式設計模型 (APM) 的設計模式。 在此模式中，非同步作業會實作為兩個方法，名為*BeginOperationName*和*EndOperationName*，其開始和結束非同步作業。

*BeginOperationName*方法開始非同步作業，並傳回該物件會實作`IAsyncResult`介面。 在呼叫*BeginOperationName*，應用程式可以繼續呼叫的執行緒上執行指令，而非同步作業會在執行緒集區執行緒。

每次呼叫*BeginOperationName*，應用程式也應該呼叫*EndOperationName*取得作業的結果。 傳回值*EndOperationName*同步 web 服務方法所傳回的類型相同。 例如，`EndGetTodoItems`方法傳回的集合`TodoItem`執行個體。 *EndOperationName*方法還包括`IAsyncResult`應該設定為對應的呼叫所傳回的執行個體的參數*BeginOperationName*方法。

工作平行程式庫 (TPL) 可以簡化耗用 APM begin/end 方法組，它會封裝在相同的非同步作業的程序`Task`物件。 此封裝由多個多載`TaskFactory.FromAsync`方法。

如需 APM 詳細資訊，請參閱[非同步程式設計模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)和[TPL 和傳統.NET Framework 非同步程式設計](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)MSDN 上。

### <a name="creating-the-todoserviceclient-object"></a>建立 TodoServiceClient 物件

產生的 proxy 類別會提供`TodoServiceClient`類別，用來與 WCF 服務透過 HTTP 通訊。 它提供非同步作業，從 URI 識別服務執行個體叫用 web 服務方法的功能。 如需有關非同步作業的詳細資訊，請參閱[非同步支援概觀](~/cross-platform/platform/async.md)。

`TodoServiceClient`執行個體宣告類別層級，讓物件存留的只要在應用程式需要能夠取用 WCF 服務，如下列程式碼範例所示：

```csharp
public class SoapService : ISoapService
{
  ITodoService todoService;
  ...

  public SoapService ()
  {
    todoService = new TodoServiceClient (
      new BasicHttpBinding (),
      new EndpointAddress (Constants.SoapUrl));
  }
  ...
}
```

`TodoServiceClient`執行個體都設定繫結資訊以及端點位址。 繫結用來指定傳輸、 編碼和通訊協定詳細資料所需的應用程式和服務來與對方進行通訊。 `BasicHttpBinding`指定，會透過 HTTP 傳輸通訊協定傳送文字編碼 SOAP 訊息。 指定端點位址可讓應用程式連接到不同的執行個體的 WCF 服務，前提是有多個已發行的執行個體。

如需有關如何設定服務參考的詳細資訊，請參閱[設定服務參考](~/cross-platform/data-cloud/web-services/index.md#wcf)。

### <a name="creating-data-transfer-objects"></a>建立資料傳輸物件

範例應用程式使用`TodoItem`模型資料的類別。 若要儲存`TodoItem`它必須先轉換成產生的 proxy web 服務中的項目`TodoItem`型別。 這會透過`ToWCFServiceTodoItem`方法，如下列程式碼範例所示：

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

這個方法只會建立新`TodoWCFService.TodoItem`執行個體，並將每個屬性設定為相同的屬性，從`TodoItem`執行個體。

同樣地，從 web 服務擷取資料時，必須將它從轉換產生的 proxy`TodoItem`類型`TodoItem`執行個體。 這是與`FromWCFServiceTodoItem`方法，如下列程式碼範例所示：

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
{
  return new TodoItem {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

這個方法只會擷取資料從產生的 proxy`TodoItem`輸入，然後將它設定在新建立`TodoItem`執行個體。

### <a name="retrieving-data"></a>擷取資料

`TodoServiceClient.BeginGetTodoItems`和`TodoServiceClient.EndGetTodoItems`方法用於呼叫`GetTodoItems`web 服務所提供的作業。 這些非同步方法，都會封裝在`Task`物件，如下列程式碼範例所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

`Task.Factory.FromAsync`方法會建立`Task`執行`TodoServiceClient.EndGetTodoItems`方法一次`TodoServiceClient.BeginGetTodoItems`方法完成時，與`null`指出沒有資料會傳遞至參數`BeginGetTodoItems`委派。 最後，值`TaskCreationOptions`列舉值會指定應建立和執行工作的預設行為。

`TodoServiceClient.EndGetTodoItems`方法會傳回`ObservableCollection`的`TodoWCFService.TodoItem`執行個體，然後轉換為`List`的`TodoItem`顯示執行個體。

### <a name="creating-data"></a>建立資料

`TodoServiceClient.BeginCreateTodoItem`和`TodoServiceClient.EndCreateTodoItem`方法用於呼叫`CreateTodoItem`web 服務所提供的作業。 這些非同步方法，都會封裝在`Task`物件，如下列程式碼範例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法會建立`Task`執行`TodoServiceClient.EndCreateTodoItem`方法一次`TodoServiceClient.BeginCreateTodoItem`方法完成時，與`todoItem`參數所傳入資料`BeginCreateTodoItem`指定委派`TodoItem` web 服務所建立。 最後，值`TaskCreationOptions`列舉值會指定應建立和執行工作的預設行為。

Web 服務擲回`FaultException`時建立`TodoItem`，這由應用程式。

### <a name="updating-data"></a>更新資料

`TodoServiceClient.BeginEditTodoItem`和`TodoServiceClient.EndEditTodoItem`方法用於呼叫`EditTodoItem`web 服務所提供的作業。 這些非同步方法，都會封裝在`Task`物件，如下列程式碼範例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法會建立`Task`執行`TodoServiceClient.EndEditTodoItem`方法一次`TodoServiceClient.BeginCreateTodoItem`方法完成時，與`todoItem`參數所傳入資料`BeginEditTodoItem`指定委派`TodoItem`更新的 web 服務。 最後，值`TaskCreationOptions`列舉值會指定應建立和執行工作的預設行為。

Web 服務擲回`FaultException`如果它無法找出或更新`TodoItem`，這由應用程式。

### <a name="deleting-data"></a>刪除資料

`TodoServiceClient.BeginDeleteTodoItem`和`TodoServiceClient.EndDeleteTodoItem`方法用於呼叫`DeleteTodoItem`web 服務所提供的作業。 這些非同步方法，都會封裝在`Task`物件，如下列程式碼範例所示：

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  ...
  await Task.Factory.FromAsync (
    todoService.BeginDeleteTodoItem,
    todoService.EndDeleteTodoItem,
    id,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法會建立`Task`執行`TodoServiceClient.EndDeleteTodoItem`方法一次`TodoServiceClient.BeginDeleteTodoItem`方法完成時，與`id`參數所傳入資料`BeginDeleteTodoItem`指定委派`TodoItem` web 服務所要刪除。 最後，值`TaskCreationOptions`列舉值會指定應建立和執行工作的預設行為。

Web 服務擲回`FaultException`如果它無法找出或刪除`TodoItem`，這由應用程式。

## <a name="summary"></a>總結

本文示範如何使用 Xamarin.Forms 應用程式從 WCF SOAP 服務。 一般情況下，Xamarin 平台都支援相同隨附 Silverlight 執行階段的 WCF 用戶端子集。 這包括 WCF 的最常見編碼和通訊協定實作，透過 HTTP 的文字編碼 SOAP 訊息傳輸通訊協定使用`BasicHttpBinding`類別。 此外，WCF 支援需要使用工具僅適用於 Windows 的環境，以產生 proxy。


## <a name="related-links"></a>相關連結

- [TodoWCF （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)

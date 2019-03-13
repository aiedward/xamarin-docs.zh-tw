---
title: 使用 ASP.NET Web 服務 (ASMX)
description: 這篇文章會示範如何使用 Xamarin.Forms 應用程式從 ASMX SOAP 服務。
ms.prod: xamarin
ms.assetid: D5533964-5528-4D35-9C2B-FAFB632472AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 1fa2fee36619a2a443d84b861b2473a1f616ed0e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055137"
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

讀我檔案所附的範例應用程式中，可以找到的 ASMX 服務設定說明。 不過，當執行範例應用程式時，它會連線到 Xamarin 裝載 ASMX 服務，可提供唯讀存取資料，如下列螢幕擷取畫面所示：

![](asmx-images/portal.png "範例應用程式")

> [!NOTE]
> 在 iOS 9 和更新版本中，App Transport Security (ATS) 會強制執行安全的連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，藉此防止意外洩漏機密資訊。 針對 iOS 9 所建置的應用程式中的預設會啟用 ATS，因為所有連線將會都受限於 ATS 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以共選擇 ATS`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的達成**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consuming-the-web-service"></a>使用 Web 服務

ASMX 服務提供下列作業：

|運算|描述|參數|
|--- |--- |--- |
|GetTodoItems|取得待辦事項的清單|
|CreateTodoItem|建立新的待辦事項項目|XML 序列化 TodoItem|
|EditTodoItem|更新待辦事項|XML 序列化 TodoItem|
|DeleteTodoItem|刪除待辦事項|XML 序列化 TodoItem|

如需有關使用應用程式中的資料模型的詳細資訊，請參閱[將資料模型化](~/xamarin-forms/data-cloud/walkthrough.md)。

> [!NOTE]
> 範例應用程式會取用提供唯讀存取 web 服務的 Xamarin 裝載 ASMX 服務。 因此，建立、 更新和刪除資料的作業並不會更改應用程式中使用的資料。 不過，ASMX 服務的裝載版本，可以在**TodoASMXService**隨附的範例應用程式中的資料夾。 本版可裝載 ASMX 服務允許完整的建立、 更新、 讀取和刪除資料的存取權。

A *proxy*必須產生使用 ASMX 服務，可讓應用程式連接至服務。 Proxy 的建構方式取用的服務中繼資料定義的方法和相關聯的服務組態。 此中繼資料會產生 web 服務的 Web 服務描述語言 (WSDL) 文件的形式公開。 建置平台專屬專案中加入 web 參考 web 服務 proxy。

產生的 proxy 類別會提供適用於使用 web 服務使用非同步程式設計模型 (APM) 設計模式的方法。 在此模式中非同步作業會實作為兩個方法，名為*BeginOperationName*並*EndOperationName*，其開始和結束非同步作業。

*BeginOperationName*方法開始非同步作業，並傳回該物件會實作`IAsyncResult`介面。 之後呼叫*BeginOperationName*，應用程式可以繼續呼叫的執行緒上執行指令，而非同步作業會在執行緒集區執行緒。

每次呼叫*BeginOperationName*，應用程式也應該呼叫*EndOperationName*取得作業的結果。 傳回值*EndOperationName*同步 web 服務方法所傳回的類型相同。 例如，`EndGetTodoItems`方法傳回的集合`TodoItem`執行個體。 *EndOperationName*方法還包括`IAsyncResult`應該設定為對應呼叫所傳回的執行個體的參數*BeginOperationName*方法。

Task Parallel Library (TPL) 可以簡化使用 APM begin/end 方法組，藉由將封裝中相同的非同步作業的程序`Task`物件。 此封裝由多個多載提供`TaskFactory.FromAsync`方法。

如需 APM 的詳細資訊請參閱 <<c0> [ 非同步程式設計模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)並[TPL 和傳統.NET Framework 非同步程式設計](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)MSDN 上。

### <a name="creating-the-todoservice-object"></a>建立 TodoService 物件

產生的 proxy 類別會提供`TodoService`類別，用來透過 HTTP 與 ASMX 服務通訊。 它會提供功能來叫用 web 服務方法，如非同步作業，從 URI 所識別的服務執行個體。 如需有關非同步作業的詳細資訊，請參閱 <<c0> [ 非同步支援概觀](~/cross-platform/platform/async.md)。

`TodoService` ，讓物件存留的只要應用程式需要耗用 ASMX 服務，如下列程式碼範例所示，執行個體在類別層級宣告：

```csharp
public class SoapService : ISoapService
{
  ASMXService.TodoService asmxService;
  ...

  public SoapService ()
  {
    asmxService = new ASMXService.TodoService (Constants.SoapUrl);
  }
  ...
}
```

`TodoService`建構函式會採用選擇性的字串參數來指定 ASMX 服務執行個體的 URL。 這可讓應用程式連接到不同的服務執行個體 ASMX，前提是有多個已發行的執行個體。

### <a name="creating-data-transfer-objects"></a>建立資料傳輸物件

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

這個方法只會建立新`ASMService.TodoItem`執行個體，並將每個屬性設定為相同的屬性，從`TodoItem`執行個體。

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

這個方法只擷取資料，從產生的 proxy`TodoItem`輸入，並將它設定在新建立`TodoItem`執行個體。

### <a name="retrieving-data"></a>擷取資料

`TodoService.BeginGetTodoItems`並`TodoService.EndGetTodoItems`方法來呼叫`GetTodoItems`web 服務所提供的作業。 這些非同步方法，都會封裝在`Task`物件，如下列程式碼範例所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems) {
    Items.Add (FromASMXServiceTodoItem (item));
  }
  ...
}
```

`Task.Factory.FromAsync`方法會建立`Task`執行`TodoService.EndGetTodoItems`方法一次`TodoService.BeginGetTodoItems`方法完成時，使用`null`參數，指出正在將資料傳遞至`BeginGetTodoItems`委派。 最後，windows 7`TaskCreationOptions`列舉會指定應建立和執行工作的預設行為。

`TodoService.EndGetTodoItems`方法傳回的陣列`ASMXService.TodoItem`執行個體，然後轉換成`List`的`TodoItem`顯示的執行個體。

### <a name="creating-data"></a>建立資料

`TodoService.BeginCreateTodoItem`並`TodoService.EndCreateTodoItem`方法來呼叫`CreateTodoItem`web 服務所提供的作業。 這些非同步方法，都會封裝在`Task`物件，如下列程式碼範例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法會建立`Task`執行`TodoService.EndCreateTodoItem`方法一次`TodoService.BeginCreateTodoItem`方法完成時，使用`todoItem`參數所傳入資料`BeginCreateTodoItem`委派指定`TodoItem`建立 web 服務。 最後，windows 7`TaskCreationOptions`列舉會指定應建立和執行工作的預設行為。

Web 服務擲回`SoapException`如果無法建立`TodoItem`，這由應用程式。

### <a name="updating-data"></a>更新資料

`TodoService.BeginEditTodoItem`並`TodoService.EndEditTodoItem`方法來呼叫`EditTodoItem`web 服務所提供的作業。 這些非同步方法，都會封裝在`Task`物件，如下列程式碼範例所示：

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToASMXServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

`Task.Factory.FromAsync`方法會建立`Task`執行`TodoService.EndEditTodoItem`方法一次`TodoService.BeginCreateTodoItem`方法完成時，使用`todoItem`參數所傳入資料`BeginEditTodoItem`委派指定`TodoItem`更新的 web 服務。 最後，windows 7`TaskCreationOptions`列舉會指定應建立和執行工作的預設行為。

Web 服務擲回`SoapException`找出或更新時`TodoItem`，這由應用程式。

### <a name="deleting-data"></a>刪除資料

`TodoService.BeginDeleteTodoItem`並`TodoService.EndDeleteTodoItem`方法來呼叫`DeleteTodoItem`web 服務所提供的作業。 這些非同步方法，都會封裝在`Task`物件，如下列程式碼範例所示：

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

`Task.Factory.FromAsync`方法會建立`Task`執行`TodoService.EndDeleteTodoItem`方法一次`TodoService.BeginDeleteTodoItem`方法完成時，使用`id`參數所傳入資料`BeginDeleteTodoItem`委派指定`TodoItem`要刪除 web 服務。 最後，windows 7`TaskCreationOptions`列舉會指定應建立和執行工作的預設行為。

Web 服務擲回`SoapException`找出或刪除時`TodoItem`，這由應用程式。

## <a name="summary"></a>總結

這篇文章會示範如何使用 Xamarin.Forms 應用程式的 ASMX web 服務。 ASMX 讓您能夠建置傳送訊息透過 HTTP 使用 SOAP 的 web 服務。 ASMX 服務的取用者不需要知道任何關於平台、 物件模型或用來實作服務的程式設計語言。 它們只需要了解如何傳送和接收 SOAP 訊息。


## <a name="related-links"></a>相關連結

- [TodoASMX （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX/)
- [IAsyncResult](https://msdn.microsoft.com/library/system.iasyncresult(v=vs.110).aspx)

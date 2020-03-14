---
title: 使用 Windows Communication Foundation （WCF） Web 服務
description: 本文示範如何從 Xamarin 應用程式使用 WCF 簡單物件存取通訊協定（SOAP）服務。
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/28/2019
ms.openlocfilehash: 28cb1573262b63cc2b0ccad9f468fe36c682718d
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305217"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>使用 Windows Communication Foundation （WCF） Web 服務

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)

_WCF 是 Microsoft 用來建立服務導向應用程式的統一架構。它可以讓開發人員建立安全、可靠、交易和互通的分散式應用程式。本文示範如何從 Xamarin 應用程式使用 WCF 簡單物件存取通訊協定（SOAP）服務。_

WCF 描述具有各種不同合約的服務，包括：

- **資料合約**–定義形成訊息內內容基礎的資料結構。
- **訊息合約**–撰寫來自現有資料合約的訊息。
- **錯誤合約**-允許指定自訂 SOAP 錯誤。
- **服務合約**–指定服務支援的作業，以及與每項操作互動所需的訊息。 它們也會指定可與每個服務上的作業相關聯的任何自訂錯誤行為。

ASP.NET Web 服務（.ASMX）和 WCF 之間有差異，但 WCF 支援的功能與 .ASMX 所提供的相同，也就是透過 HTTP 的 SOAP 訊息。 如需使用 .ASMX 服務的詳細資訊，請參閱[使用 ASP.NET Web 服務（.asmx）](~/xamarin-forms/data-cloud/web-services/asmx.md)。

> [!IMPORTANT]
> WCF 的 Xamarin 平臺支援是透過 HTTP/HTTPS 使用 `BasicHttpBinding` 類別，限制為文字編碼的 SOAP 訊息。
>
> WCF 支援需要使用僅在 Windows 環境中提供的工具來產生 proxy 並裝載 TodoWCFService。 建立和測試 iOS 應用程式需要在 Windows 電腦或 Azure web 服務上部署 TodoWCFService。
>
> Xamarin Forms 原生應用程式通常會與 .NET Standard 類別庫共用程式碼。 不過，.NET Core 目前不支援 WCF，因此共用的專案必須是舊版的可移植類別庫。 如需 .NET Core 中 WCF 支援的詳細資訊，請參閱[為伺服器應用程式選擇 .Net core 和 .NET Framework](/dotnet/standard/choosing-core-framework-server)。

範例應用程式解決方案包含可在本機執行的 WCF 服務，如下列螢幕擷取畫面所示：

![](wcf-images/portal.png "Sample Application")

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性（ATS）會強制執行網際網路資源（例如應用程式的後端伺服器）與應用程式之間的安全連線，藉此防止意外洩漏機密資訊。 由於預設會在針對 iOS 9 建立的應用程式中啟用 ATS，因此所有連線都將受限於 ATS 安全性需求。 如果連線不符合這些需求，則會失敗並產生例外狀況。
>
> 如果無法使用網際網路資源的 `HTTPS` 通訊協定和安全通訊，可以退出宣告 ATS。 這可以藉由更新應用程式的**plist**檔案來達成。 如需詳細資訊，請參閱[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="consume-the-web-service"></a>取用 Web 服務

WCF 服務會提供下列作業：

|作業|描述|參數|
|--- |--- |--- |
|GetTodoItems|取得待辦事項的清單|
|CreateTodoItem|建立新的待辦事項專案|XML 序列化 TodoItem|
|EditTodoItem|更新待辦事項|XML 序列化 TodoItem|
|DeleteTodoItem|刪除待辦事項|XML 序列化 TodoItem|

如需應用程式中所使用之資料模型的詳細資訊，請參閱[模型化資料](~/xamarin-forms/data-cloud/web-services/introduction.md)。

必須產生*proxy*才能使用 WCF 服務，這可讓應用程式連接到服務。 Proxy 是藉由使用定義方法和相關聯服務設定的服務中繼資料所構成。 此中繼資料是以 web 服務所產生的 Web 服務描述語言（WSDL）檔的形式公開。 您可以使用 Visual Studio 2017 中的 Microsoft WCF Web Service Reference Provider 來建立 proxy，將 Web 服務的服務參考新增至 .NET Standard 程式庫。 使用 Visual Studio 2017 中的 Microsoft WCF Web Service Reference Provider 來建立 proxy 的替代方法是使用 [System.servicemodel 中繼資料公用程式] 工具（svcutil .exe）。 如需詳細資訊，請參閱[System.servicemodel 中繼資料公用程式工具（Svcutil .exe）](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/)。

產生的 proxy 類別提供取用使用非同步程式設計模型（APM）設計模式之 web 服務的方法。 在此模式中，非同步作業會實作為兩個名為*BeginOperationName*和*EndOperationName*的方法，它會開始和結束非同步作業。

*BeginOperationName*方法會開始非同步作業，並傳回可執行 `IAsyncResult` 介面的物件。 呼叫*BeginOperationName*之後，應用程式可以繼續在呼叫執行緒上執行指令，而非同步作業會線上程集區執行緒上進行。

對於*BeginOperationName*的每個呼叫，應用程式也應該呼叫*EndOperationName*來取得作業的結果。 *EndOperationName*的傳回值與同步 web 服務方法所傳回的類型相同。 例如，`EndGetTodoItems` 方法會傳回 `TodoItem` 實例的集合。 *EndOperationName*方法也包含 `IAsyncResult` 參數，應設定為*BeginOperationName*方法的對應呼叫所傳回的實例。

工作平行程式庫（TPL）可以將非同步作業封裝在相同的 `Task` 物件中，藉此簡化使用 APM begin/end 方法組的程式。 此封裝是由 `TaskFactory.FromAsync` 方法的多個多載所提供。

如需有關 APM 的詳細資訊，請參閱 MSDN 上的[非同步程式設計模型](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx)和[TPL 和傳統 .NET Framework 非同步程式設計](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx)。

### <a name="create-the-todoserviceclient-object"></a>建立 TodoServiceClient 物件

產生的 proxy 類別會提供 `TodoServiceClient` 類別，這是用來透過 HTTP 與 WCF 服務進行通訊。 它提供從 URI 識別的服務實例叫用 web 服務方法做為非同步作業的功能。 如需非同步作業的詳細資訊，請參閱[非同步支援總覽](~/cross-platform/platform/async.md)。

`TodoServiceClient` 實例是在類別層級宣告，因此，只要應用程式需要使用 WCF 服務，物件就會存在，如下列程式碼範例所示：

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

`TodoServiceClient` 實例會使用系結資訊和端點位址進行設定。 系結是用來指定應用程式和服務彼此通訊所需的傳輸、編碼和通訊協定詳細資料。 `BasicHttpBinding` 指定將透過 HTTP 傳輸通訊協定傳送文字編碼的 SOAP 訊息。 指定端點位址可讓應用程式連接到 WCF 服務的不同實例，前提是有多個已發行的實例。

如需設定服務參考的詳細資訊，請參閱設定[服務參考](~/cross-platform/data-cloud/web-services/index.md#wcf)。

### <a name="create-data-transfer-objects"></a>建立資料傳輸物件

範例應用程式會使用 `TodoItem` 類別來建立資料模型。 若要在 web 服務中儲存 `TodoItem` 專案，必須先將它轉換成產生的 proxy `TodoItem` 類型。 這是由 `ToWCFServiceTodoItem` 方法來完成，如下列程式碼範例所示：

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

這個方法只會建立新的 `TodoWCFService.TodoItem` 實例，並將每個屬性設定為來自 `TodoItem` 實例的相同屬性。

同樣地，從 web 服務抓取資料時，它必須從產生的 proxy `TodoItem` 型別轉換成 `TodoItem` 實例。 這是使用 `FromWCFServiceTodoItem` 方法來完成，如下列程式碼範例所示：

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
{
  return new TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

這個方法只會從產生的 proxy `TodoItem` 類型抓取資料，並將它設定為新建立的 `TodoItem` 實例。

### <a name="retrieve-data"></a>取出資料

`TodoServiceClient.BeginGetTodoItems` 和 `TodoServiceClient.EndGetTodoItems` 方法是用來呼叫 web 服務所提供的 `GetTodoItems` 作業。 這些非同步方法會封裝在 `Task` 物件中，如下列程式碼範例所示：

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems)
  {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

`Task.Factory.FromAsync` 方法會建立 `Task`，以在 `TodoServiceClient.BeginGetTodoItems` 方法完成時執行 `TodoServiceClient.EndGetTodoItems` 方法，並使用 `null` 參數指出不會將任何資料傳遞至 `BeginGetTodoItems` 委派。 最後，`TaskCreationOptions` 列舉的值指定應該使用建立和執行工作的預設行為。

`TodoServiceClient.EndGetTodoItems` 方法會傳回 `TodoWCFService.TodoItem` 實例的 `ObservableCollection`，然後轉換成 `TodoItem` 實例的 `List` 以供顯示。

### <a name="create-data"></a>建立資料

`TodoServiceClient.BeginCreateTodoItem` 和 `TodoServiceClient.EndCreateTodoItem` 方法是用來呼叫 web 服務所提供的 `CreateTodoItem` 作業。 這些非同步方法會封裝在 `Task` 物件中，如下列程式碼範例所示：

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

`Task.Factory.FromAsync` 方法會建立一個 `Task`，在 `TodoServiceClient.BeginCreateTodoItem` 方法完成後執行 `TodoServiceClient.EndCreateTodoItem` 方法，其中 `todoItem` 參數是傳入 `BeginCreateTodoItem` 委派的資料，用以指定 web 服務所要建立的 `TodoItem`。 最後，`TaskCreationOptions` 列舉的值指定應該使用建立和執行工作的預設行為。

如果 web 服務無法建立 `TodoItem`（由應用程式處理），則會擲回 `FaultException`。

### <a name="update-data"></a>更新資料

`TodoServiceClient.BeginEditTodoItem` 和 `TodoServiceClient.EndEditTodoItem` 方法是用來呼叫 web 服務所提供的 `EditTodoItem` 作業。 這些非同步方法會封裝在 `Task` 物件中，如下列程式碼範例所示：

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

`Task.Factory.FromAsync` 方法會建立一個 `Task`，在 `TodoServiceClient.BeginCreateTodoItem` 方法完成後執行 `TodoServiceClient.EndEditTodoItem` 方法，其中 `todoItem` 參數是傳入 `BeginEditTodoItem` 委派的資料，用以指定 web 服務要更新的 `TodoItem`。 最後，`TaskCreationOptions` 列舉的值指定應該使用建立和執行工作的預設行為。

如果 web 服務找不到或無法更新應用程式所處理的 `TodoItem`，就會擲回 `FaultException`。

### <a name="delete-data"></a>刪除資料

`TodoServiceClient.BeginDeleteTodoItem` 和 `TodoServiceClient.EndDeleteTodoItem` 方法是用來呼叫 web 服務所提供的 `DeleteTodoItem` 作業。 這些非同步方法會封裝在 `Task` 物件中，如下列程式碼範例所示：

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

`Task.Factory.FromAsync` 方法會建立一個 `Task`，在 `TodoServiceClient.BeginDeleteTodoItem` 方法完成後執行 `TodoServiceClient.EndDeleteTodoItem` 方法，其中 `id` 參數是傳入 `BeginDeleteTodoItem` 委派的資料，以指定要由 web 服務刪除的 `TodoItem`。 最後，`TaskCreationOptions` 列舉的值指定應該使用建立和執行工作的預設行為。

如果 web 服務找不到或無法刪除由應用程式處理的 `TodoItem`，就會擲回 `FaultException`。

## <a name="configure-remote-access-to-iis-express"></a>設定遠端存取 IIS Express
在 Visual Studio 2017 或 Visual Studio 2019 中，您應該能夠在不進行其他設定的電腦上測試 UWP 應用程式。 測試 Android 和 iOS 用戶端可能需要本節中的其他步驟。 如需詳細資訊，請參閱[從 IOS 模擬器和 Android 模擬器連接到本機 Web 服務](~/cross-platform/deploy-test/connect-to-local-web-services.md)。

根據預設，IIS Express 只會回應 `localhost`的要求。 遠端裝置（例如 Android 裝置、iPhone 或甚至是模擬器）將無法存取您的本機 WCF 服務。 您將需要知道您在區域網路上的 Windows 10 工作站 IP 位址。 基於此範例的目的，假設您的工作站具有 `192.168.1.143`的 IP 位址。 下列步驟說明如何設定 Windows 10 和 IIS Express 以接受遠端連線，並從實體或虛擬裝置連線至服務：

1. **將例外狀況新增至 Windows 防火牆**。 您必須透過 Windows 防火牆開啟埠，子網中的應用程式才能用來與 WCF 服務進行通訊。 建立輸入規則，以在防火牆中開啟埠49393。 從系統管理命令提示字元中，執行下列命令：

    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **設定 IIS Express 以接受遠端連線**。 您可以在 **[方案目錄]\.vs\config\applicationhost.config**編輯 IIS Express 的設定檔，以設定 IIS Express。尋找名稱為 `TodoWCFService`的 `site` 元素。 看起來應該類似下列 XML：

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
        </bindings>
    </site>
    ```

    您將需要新增兩個 `binding` 元素，以開啟埠49393至外部流量和 Android 模擬器。 系結會使用 `[IP address]:[port]:[hostname]` 格式，以指定 IIS Express 回應要求的方式。 外部要求將具有必須指定為 `binding`的主機名稱。 將下列 XML 新增至 `bindings` 元素，並將 IP 位址取代為您自己的 IP 位址：

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    變更之後，`bindings` 元素應如下所示：

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
            <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
            <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
        </bindings>
    </site>
    ```

    >[!IMPORTANT]
    >根據預設，基於安全性理由，IIS Express 不會接受來自外部來源的連接。 若要啟用遠端裝置的連接，您必須以系統管理許可權執行 IIS Express。 若要這麼做，最簡單的方法是使用系統管理許可權來執行 Visual Studio 2017。 這會在執行 TodoWCFService 時，以系統管理許可權啟動 IIS Express。

    完成這些步驟之後，您應該能夠執行 TodoWCFService，並從子網的其他裝置進行連線。 您可以藉由執行應用程式並造訪 `http://localhost:49393/TodoService.svc`來進行測試。 如果您在流覽該 URL 時收到不正確的**要求**錯誤，您的 `bindings` 在 IIS Express 設定中可能不正確（要求會到達 IIS Express 但遭到拒絕）。 如果您收到不同的錯誤，可能是您的應用程式未執行，或您的防火牆設定不正確。

    若要讓 IIS Express 繼續執行並提供服務，請關閉 **專案屬性 > Web > 調試**程式 中的 **編輯後繼續** 選項。

1. **自訂用來存取服務的端點裝置**。 此步驟牽涉到設定在實體或模擬裝置上執行的用戶端應用程式，以存取 WCF 服務。

    Android 模擬器會利用內部 proxy，防止模擬器直接存取主機電腦的 `localhost` 位址。 相反地，模擬器上的位址 `10.0.2.2` 會透過內部 proxy 路由傳送至主機電腦上的 `localhost`。 這些 proxy 要求將會 `127.0.0.1` 做為要求標頭中的主機名稱，這就是您在上述步驟中為此主機名稱建立 IIS Express 系結的原因。

    IOS 模擬器會在 Mac 組建主機上執行，即使您使用[適用于 Windows 的遠端 iOS](~/tools/ios-simulator/index.md)模擬器也一樣。 來自模擬器的網路要求會在區域網路上將您的工作站 IP 做為主機名稱（在此範例中為 `192.168.1.143`，但您的實際 IP 位址可能會不同）。 這就是您在上述步驟中為此主機名稱建立 IIS Express 系結的原因。

    請確定 TodoWCF （便攜）專案中**Constants.cs**檔案中的 `SoapUrl` 屬性具有您的網路的正確值：

    ```csharp
    public static string SoapUrl
    {
        get
        {
            var defaultUrl = "http://localhost:49393/TodoService.svc";

            if (Device.RuntimePlatform == Device.Android)
            {
                defaultUrl = "http://10.0.2.2:49393/TodoService.svc";
            }
            else if (Device.RuntimePlatform == Device.iOS)
            {
                defaultUrl = "http://192.168.1.143:49393/TodoService.svc";
            }

            return defaultUrl;
        }
    }
    ```

    使用適當的端點設定**Constants.cs**之後，您應該能夠從實體或虛擬裝置連接到在 Windows 10 工作站上執行的 TodoWCFService。

## <a name="related-links"></a>相關連結

- [TodoWCF （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [How to：建立 Windows Communication Foundation 用戶端](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [System.servicemodel 中繼資料公用程式工具（svcutil .exe）](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)

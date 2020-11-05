---
title: Azure SignalR Service Xamarin.Forms
description: 開始使用 Azure SignalR Service 並 Azure Functions Xamarin.Forms
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12e1576ab2e5f7a90f4e1355d03e8f9c5c487e6c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374416"
---
# <a name="azure-signalr-service-with-no-locxamarinforms"></a>Azure SignalR Service Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/webservices-azuresignalr/)

ASP.NET Core SignalR 是一種應用程式模型，可簡化將即時通訊加入至應用程式的流程。 Azure SignalR Service 可讓您快速開發及部署可擴充的 SignalR 應用程式。 Azure Functions 是存留期較短的無伺服器程式碼方法，可結合以形成事件驅動、可擴充的應用程式。

本文和範例示範如何結合 Azure SignalR Service 和 Azure Functions Xamarin.Forms ，以將即時訊息傳遞給連線的用戶端。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>建立 Azure SignalR Service 和 Azure Functions 應用程式

範例應用程式包含三個主要元件： Azure SignalR Service 中樞、具有兩個函式的 Azure Functions 實例，以及可傳送和接收訊息的行動應用程式。 這些元件的互動方式如下：

1. 行動應用程式會叫用 **Negotiate** Azure 函式，以取得 SignalR 中樞的相關資訊。
1. 行動應用程式會使用協調資訊來向 SignalR hub 註冊自己，並形成連接。
1. 註冊之後，行動應用程式會將訊息張貼到 **交談** Azure 函數。
1. **交談** 函式會將傳入訊息傳遞至 SignalR 中樞。
1. SignalR 中樞會將訊息廣播至所有連線的行動應用程式實例，包括原始寄件者。

> [!IMPORTANT]
> 範例應用程式中的 **Negotiate** 和 **交談** 函式可以使用 Visual Studio 2019 和 Azure 執行時間工具在本機執行。 不過，Azure SignalR Service 無法在本機模擬，因此很難將本機裝載的 Azure Functions 公開給實體或虛擬裝置進行測試。 建議您將 Azure Functions 部署到 Azure Functions 的應用程式實例，因為這樣做可進行跨平臺測試。 如需部署的詳細資訊，請參閱 [使用 Visual Studio 2019 部署 Azure Functions](#deploy-azure-functions-with-visual-studio-2019)。

### <a name="create-an-azure-signalr-service"></a>建立 Azure SignalR Service

您可以選擇 Azure 入口網站左上角的 [ **建立資源** ]，並搜尋 **SignalR** 來建立 Azure SignalR Service。 Azure SignalR Service 可以在免費層建立。 Azure SignalR Service 必須處於 **無伺服器** 服務模式。 如果您不小心選擇預設或傳統服務模式，您可以稍後在 Azure SignalR Service 屬性中加以變更。

下列螢幕擷取畫面顯示如何建立新的 Azure SignalR Service：

![在 Azure 入口網站中建立 Azure SignalR Service 的螢幕擷取畫面](azure-signalr-images/azure-signalr-create.png "建立 Azure SignalR Service")

一旦建立之後，Azure SignalR Service 的 [ **金鑰** ] 區段會包含 **連接字串** ，用來將 Azure Functions 應用程式連線到 SignalR 中樞。 下列螢幕擷取畫面顯示在 Azure SignalR Service 中尋找連接字串的位置：

![Azure 入口網站中 Azure SignalR 連接字串的螢幕擷取畫面](azure-signalr-images/azure-signalr-connection-string.png "Azure SignalR 連接字串")

此連接字串是用來 [部署 Visual Studio 2019 的 Azure Functions](#deploy-azure-functions-with-visual-studio-2019)。

### <a name="create-an-azure-functions-app"></a>建立 Azure Functions 應用程式

若要測試範例應用程式，請在 Azure 入口網站中建立新的 Azure Functions 應用程式。 請記下應用程式 **名稱** ，因為範例應用程式 **Constants.cs** 檔中使用此 URL。 下列螢幕擷取畫面顯示如何建立名為 "xdocsfunctions" 的新 Azure Functions 應用程式：

[![建立 Azure Functions 應用程式的螢幕擷取畫面](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

您可以從 Visual Studio 2019 將 Azure 函式部署到 Azure Functions 的應用程式實例。 下列各節說明如何將範例應用程式中的兩個函式部署至 Azure Functions 應用程式實例。

### <a name="build-azure-functions-in-visual-studio-2019"></a>Visual Studio 2019 中的組建 Azure Functions

範例應用程式包含名為 **ChatServer** 的類別庫，其中包含兩個無伺服器 Azure Functions 在名為 **Negotiate.cs** 和 **Talk.cs** 的檔案中。

`Negotiate`函數 `SignalRConnectionInfo` 會使用包含 `AccessToken` 屬性和屬性的物件來回應 web 要求 `Url` 。 行動應用程式會使用這些值，向 SignalR hub 註冊自己的值。 下列程式碼顯示 `Negotiate` 函數：

```csharp
[FunctionName("Negotiate")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous,"get",Route = "negotiate")]
    HttpRequest req,
    [SignalRConnectionInfo(HubName = "simplechat")]
    SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

此函式 `Talk` 會回應 HTTP post 要求，以提供 POST 主體中的訊息物件。 POST 主體會轉換成 `SignalRMessage` ，並轉送到 SignalR 中樞。 下列程式碼顯示 `Talk` 函數：

```csharp
[FunctionName("Talk")]
public static async Task<IActionResult> Run(
    [HttpTrigger(
        AuthorizationLevel.Anonymous,
        "post",
        Route = "talk")]
    HttpRequest req,
    [SignalR(HubName = "simplechat")]
    IAsyncCollector<SignalRMessage> questionR,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    try
    {
        string json = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic obj = JsonConvert.DeserializeObject(json);

        var name = obj.name.ToString();
        var text = obj.text.ToString();

        var jObject = new JObject(obj);

        await questionR.AddAsync(
            new SignalRMessage
            {
                Target = "newMessage",
                Arguments = new[] { jObject }
            });

        return new OkObjectResult($"Hello {name}, your message was '{text}'");
    }
    catch (Exception ex)
    {
        return new BadRequestObjectResult("There was an error: " + ex.Message);
    }
}
```

若要深入瞭解 Azure 函數和 Azure Functions Apps，請參閱 [Azure Functions 檔](/azure/azure-functions/)。

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>使用 Visual Studio 2019 部署 Azure Functions

Visual Studio 2019 可讓您將函式部署至 Azure Functions 應用程式。 Azure 裝載的函式會為所有裝置提供可存取的測試端點，以簡化跨平臺測試。

以滑鼠右鍵按一下範例函式應用程式，然後選擇 [ **發佈** ] 會啟動對話方塊，以將函式發佈至您的 Azure Functions 應用程式。 如果您已遵循先前的步驟來設定 Azure 函數應用程式，您可以選擇 [ **選取現有** 的] 將範例應用程式發佈至 Azure Functions 應用程式。 下列螢幕擷取畫面顯示 Visual Studio 2019 中的 [發行] 對話方塊選項：

![Visual Studio 2019 中的 [發行選項] 對話方塊](azure-signalr-images/vs-publish-target.png "Visual Studio 2019 中的發佈選項")

登入您的 Microsoft 帳戶之後，您可以尋找並選擇您的 Azure Functions 應用程式做為發佈目標。 下列螢幕擷取畫面顯示 Visual Studio 2019 發佈對話方塊中 Azure Functions 應用程式範例：

![Visual Studio 2019 發佈對話方塊中的 Azure Functions 應用程式](azure-signalr-images/vs-app-selection.png "Visual Studio 2019 發佈對話方塊中的 Azure Functions 應用程式")

選取 Azure Functions 的應用程式實例後，會顯示網站 URL、設定和目標 Azure Functions 應用程式的其他相關資訊。 選擇 [ **編輯 Azure App Service 設定** ]，然後在 [ **遠端** ] 欄位中輸入您的連接字串。 **Negotiate** 和 **交談** 函式會使用連接字串連接到 Azure SignalR Service，並且可在 Azure 入口網站中 Azure SignalR Service 的 [ **金鑰** ] 區段中取得。 如需連接字串的詳細資訊，請參閱 [建立 Azure SignalR Service](#create-an-azure-signalr-service)。

輸入連接字串之後，您可以按一下 [ **發佈** ]，將您的函式部署到 Azure Functions 應用程式。 完成之後，這些函式會列在 Azure 入口網站的 Azure Functions 應用程式中。 下列螢幕擷取畫面顯示 Azure 入口網站中已發佈的函式：

![在 Azure Functions 應用程式中發佈的函式](azure-signalr-images/azure-functions-deployed.png "在 Azure Functions 應用程式中發佈的函式")

## <a name="integrate-azure-signalr-service-with-no-locxamarinforms"></a>整合 Azure SignalR Service 與 Xamarin.Forms

Azure SignalR Service 和應用程式之間的整合 Xamarin.Forms 是 SignalR 服務類別，在類別中具現化，並將 `MainPage` 事件處理常式指派給三個事件。 如需這些事件處理常式的詳細資訊，請參閱[中 Xamarin.Forms 的使用 SignalR 服務類別](#use-the-signalr-service-class-in-xamarinforms)。

範例應用程式包含必須使用 Azure Functions 應用程式的 URL 端點進行自訂的 **Constants.cs** 類別。 將屬性的值設定 `HostName` 為您 Azure Functions 的應用程式位址。 下列程式碼顯示具有範例值的 **Constants.cs** 屬性 `HostName` ：

```csharp
public static class Constants
{
    public static string HostName { get; set; } = "https://example-functions-app.azurewebsites.net/";

    // Used to differentiate message types sent via SignalR. This
    // sample only uses a single message type.
    public static string MessageName { get; set; } = "newMessage";

    public static string Username
    {
        get
        {
            return $"{Device.RuntimePlatform} User";
        }
    }
}
```

> [!NOTE]
> `Username`範例應用程式 **Constants.cs** 檔中的屬性會使用裝置的 `RuntimePlatform` 值做為使用者名稱。 這可讓您輕鬆地跨平臺測試裝置，並找出正在傳送訊息的裝置。 在真實世界的應用程式中，這個值可能是在註冊或登入程式期間所收集的唯一使用者名稱。

### <a name="the-signalr-service-class"></a>SignalR 服務類別

`SignalRService`範例應用程式中 **ChatClient** 專案中的類別會顯示在 Azure Functions 應用程式中叫用函式以連接到 Azure SignalR Service 的實作為。

`SendMessageAsync`類別中的方法 `SignalRService` 是用來將訊息傳送給連接到 Azure SignalR Service 的用戶端。 這個方法會對 Azure Functions 應用程式中所裝載的 **交談** 函式執行 HTTP POST 要求，包括 JSON 序列化 `Message` 物件作為 POST 承載。 **交談** 函式會將訊息傳遞至 Azure SignalR Service，以廣播至所有連線的用戶端。 下列程式碼顯示 `SendMessageAsync` 方法：

```csharp
public async Task SendMessageAsync(string username, string message)
{
    IsBusy = true;

    var newMessage = new Message
    {
        Name = username,
        Text = message
    };

    var json = JsonConvert.SerializeObject(newMessage);
    var content = new StringContent(json, Encoding.UTF8, "application/json");
    var result = await client.PostAsync($"{Constants.HostName}/api/talk", content);

    IsBusy = false;
}
```

`ConnectAsync`類別中的方法會 `SignalRService` 對 Azure Functions 應用程式中裝載的 **Negotiate** 函式執行 HTTP GET 要求。 **Negotiate** 函式會傳回已還原序列化為類別實例的 JSON `NegotiateInfo` 。 一旦抓取 `NegotiateInfo` 物件之後，就會使用類別的實例，直接向 Azure SignalR Service 註冊 `HubConnection` 。

ASP.NET Core SignalR 會將傳入的資料從開啟的連接轉譯為訊息，並可讓開發人員根據類型來定義訊息類型，並將事件處理常式系結至傳入訊息。 `ConnectAsync`方法會為範例應用程式的 **Constants.cs** 檔中所定義的訊息名稱註冊事件處理常式，預設為 ">newmessage"。

下列程式碼顯示 `ConnectAsync` 方法：

```csharp
public async Task ConnectAsync()
{
    try
    {
        IsBusy = true;

        string negotiateJson = await client.GetStringAsync($"{Constants.HostName}/api/negotiate");
        NegotiateInfo negotiate = JsonConvert.DeserializeObject<NegotiateInfo>(negotiateJson);
        HubConnection connection = new HubConnectionBuilder()
            .AddNewtonsoftJsonProtocol()
            .WithUrl(negotiate.Url, options =>
            {
                options.AccessTokenProvider = async () => negotiate.AccessToken;
            })
            .Build();

        connection.On<JObject>(Constants.MessageName, AddNewMessage);
        await connection.StartAsync();

        IsConnected = true;
        IsBusy = false;

        Connected?.Invoke(this, true, "Connection successful.");
    }
    catch (Exception ex)
    {
        ConnectionFailed?.Invoke(this, false, ex.Message);
    }
}
```

> [!NOTE]
> SignalR 服務預設會使用 `System.Text.Json` 將 JSON 序列化和還原序列化。 以其他程式庫（例如 Newtonsoft）序列化的資料，可能無法由 SignalR 服務還原序列化。 `HubConnection`範例專案中的實例包含的呼叫， `AddNewtonsoftJsonProtocol` 以指定 JSON 序列化程式。 這個方法是定義在 **AspNetCore SignalR** 的特殊 NuGet 套件中，該套件必須包含在專案中。 如果您使用 `System.Text.Json` 來序列化/還原序列化 JSON 資料，則不應使用此方法和 NuGet 套件。

`AddNewMessage`方法會系結為訊息中的事件處理常式 `ConnectAsync` ，如先前的程式碼所示。 收到訊息時， `AddNewMessage` 會使用提供為的訊息資料來呼叫方法 `JObject` 。 `AddNewMessage`方法會將轉換 `JObject` 成類別的實例 `Message` ，然後叫用的處理常式（ `NewMessageReceived` 如果已系結）。 下列程式碼顯示 `AddNewMessage` 方法：

```csharp
public void AddNewMessage(JObject message)
{
    Message messageModel = new Message
    {
        Name = message.GetValue("name").ToString(),
        Text = message.GetValue("text").ToString(),
        TimeReceived = DateTime.Now
    };

    NewMessageReceived?.Invoke(this, messageModel);
}
```

### <a name="use-the-signalr-service-class-in-no-locxamarinforms"></a>使用中的 SignalR 服務類別 Xamarin.Forms

在 Xamarin.Forms 程式 `SignalRService` `MainPage` 代碼後端類別中系結類別事件，即可在中利用 SignalR 服務類別。

`Connected` `SignalRService` 當 SignalR 連接成功完成時，就會引發類別中的事件。 `ConnectionFailed` `SignalRService` SignalR 連接失敗時，會引發類別中的事件。 `SignalR_ConnectionChanged`事件處理常式方法會系結至函式中的這兩個事件 `MainPage` 。 此事件處理常式會根據連接引數來更新 [連接] 和 [傳送] 按鈕狀態 `success` ，並使用方法將事件提供的訊息新增到交談佇列 `AddMessage` 。 下列程式碼顯示 `SignalR_ConnectionChanged` 事件處理常式方法：

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

`NewMessageReceived` `SignalRService` 從 Azure SignalR Service 接收新訊息時，就會引發類別中的事件。 `SignalR_NewMessageReceived`事件處理常式方法會系結至函式 `NewMessageReceived` 中的事件 `MainPage` 。 此事件處理常式會將傳入的 `Message` 物件轉換成字串，並使用方法將其新增至聊天佇列 `AddMessage` 。 下列程式碼顯示 `SignalR_NewMessageReceived` 事件處理常式方法：

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

`AddMessage`方法會將新的訊息以物件的形式新增 `Label` 至聊天佇列。 `AddMessage`方法通常會由主要 UI 執行緒之外的事件處理常式呼叫，因此會強制 UI 更新在主執行緒上發生，以防止發生例外狀況。 下列程式碼顯示 `AddMessage` 方法：

```csharp
void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        Label label = new Label
        {
            Text = message,
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        };

        messageList.Children.Add(label);
    });
}
```

## <a name="test-the-application"></a>測試應用程式

您可以在 iOS、Android 和 UWP 上測試 SignalR chat 應用程式，前提是您有：

1. 建立 Azure SignalR Service。
1. 建立 Azure Functions 應用程式。
1. 使用 Azure Functions 應用程式端點自訂 **Constants.cs** 檔案。

完成這些步驟並執行應用程式之後，按一下 [連線 **]** 按鈕就會形成與 Azure SignalR Service 的連接。 輸入訊息，然後按一下 [ **傳送** ] 按鈕，就會在任何已連線的行動應用程式上顯示聊天佇列中的訊息。

## <a name="related-links"></a>相關連結

* [使用 Xamarin 和 SignalR 打造即時行動應用程式](https://www.youtube.com/watch?v=AlqZ1LpUXeg)
* [SignalR 簡介](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Azure Functions 簡介](/azure/azure-functions/functions-overview)
* [Azure Functions 文件](/azure/azure-functions/)
* [MVVM SignalR 聊天範例](https://github.com/lbugnion/sample-xamarin-signalr)
---
title: Azure SignalR Service 與Xamarin.Forms
description: 開始使用 Azure SignalR Service 和 Azure FunctionsXamarin.Forms
ms.prod: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ffa44beb68dc845a64d8bf2a9f86f6d7e56df8f9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139434"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>Azure SignalR Service 與Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresignalr/)

ASP.NET Core SignalR 是一種應用程式模型，可簡化將即時通訊新增至應用程式的過程。 Azure SignalR Service 可讓您快速開發及部署可擴充的 SignalR 應用程式。 Azure Functions 是短期、無伺服器的程式碼方法，可以合併以形成事件驅動、可擴充的應用程式。

本文和範例示範如何將 Azure SignalR Service 和 Azure Functions 結合 Xamarin.Forms ，以將即時訊息傳遞給已連線的用戶端。

> [!NOTE]
> 如果您沒有 [Azure 訂用帳戶](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://aka.ms/azfree-docs-mobileapps)。

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>建立 Azure SignalR Service 和 Azure Functions 應用程式

範例應用程式包含三個主要元件： Azure SignalR Service 中樞、包含兩個函式的 Azure Functions 實例，以及可傳送和接收訊息的行動應用程式。 這些元件的互動方式如下：

1. 行動應用程式會叫用**Negotiate** Azure 函式，以取得 SignalR 中樞的相關資訊。
1. 行動應用程式會使用協調資訊向 SignalR 中樞註冊自己，並形成連接。
1. 註冊之後，行動應用程式會將訊息張貼至「**交談**」 Azure 函式。
1. **交談**函式會將傳入訊息傳遞至 SignalR 中樞。
1. SignalR 中樞會將訊息廣播到所有已連線的行動應用程式實例，包括原始的傳送者。

> [!IMPORTANT]
> 範例應用程式中的**Negotiate**和**交談**功能可以使用 Visual Studio 2019 和 Azure 執行時間工具在本機執行。 不過，Azure SignalR Service 無法在本機上進行模擬，而且很容易將本機裝載的 Azure Functions 公開給實體或虛擬裝置進行測試。 建議您將 Azure Functions 部署至 Azure Functions 應用程式實例，因為這可允許跨平臺測試。 如需部署詳細資料，請參閱[使用 Visual Studio 2019 部署 Azure Functions](#deploy-azure-functions-with-visual-studio-2019)。

### <a name="create-an-azure-signalr-service"></a>建立 Azure SignalR Service

您可以選擇 Azure 入口網站左上角的 [**建立資源**]，然後搜尋**SignalR**來建立 Azure SignalR Service。 Azure SignalR Service 可以在免費層建立。 Azure SignalR Service 必須處於**無伺服器**服務模式。 如果您不小心選擇預設或傳統服務模式，可以稍後在 Azure SignalR Service 屬性中進行變更。

下列螢幕擷取畫面顯示建立新的 Azure SignalR Service：

![在 Azure 入口網站中建立 Azure SignalR Service 的螢幕擷取畫面](azure-signalr-images/azure-signalr-create.png "建立 Azure SignalR Service")

建立之後，Azure SignalR Service 的 [**金鑰**] 區段會包含**連接字串**，用來將 Azure Functions 應用程式連線到 SignalR 中樞。 下列螢幕擷取畫面顯示在 Azure SignalR Service 中尋找連接字串的位置：

![Azure 入口網站中 Azure SignalR 連接字串的螢幕擷取畫面](azure-signalr-images/azure-signalr-connection-string.png "Azure SignalR 連接字串")

此連接字串可用來[部署 Visual Studio 2019 的 Azure Functions](#deploy-azure-functions-with-visual-studio-2019)。

### <a name="create-an-azure-functions-app"></a>建立 Azure Functions 應用程式

若要測試範例應用程式，您應該在 Azure 入口網站中建立新的 Azure Functions 應用程式。 請記下應用程式**名稱**，因為範例應用程式**Constants.cs**檔中使用此 URL。 下列螢幕擷取畫面顯示建立名為 "xdocsfunctions" 的新 Azure Functions 應用程式：

[![建立 Azure Functions 應用程式的螢幕擷取畫面](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

Azure 函式可以部署至 Visual Studio 2019 的 Azure Functions 應用程式實例。 下列各節說明如何將範例應用程式中的兩個函式部署到 Azure Functions 應用程式實例。

### <a name="build-azure-functions-in-visual-studio-2019"></a>Visual Studio 2019 中的組建 Azure Functions

範例應用程式包含名為**ChatServer**的類別庫，其中包含兩個無伺服器 Azure Functions，位於名為**Negotiate.cs**和**Talk.cs**的檔案中。

函 `Negotiate` `SignalRConnectionInfo` 式會使用包含 `AccessToken` 屬性和屬性的物件來回應 web 要求 `Url` 。 行動應用程式會使用這些值，向 SignalR 中樞註冊其本身。 下列程式碼顯示 `Negotiate` 函數：

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

函式 `Talk` 會回應在 post 主體中提供訊息物件的 HTTP POST 要求。 POST 主體會轉換成 `SignalRMessage` ，並轉送至 SignalR 中樞。 下列程式碼顯示 `Talk` 函數：

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

若要深入瞭解 Azure 函式和 Azure Functions 應用程式，請參閱[Azure Functions 檔](/azure/azure-functions/)。

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>使用 Visual Studio 2019 部署 Azure Functions

Visual Studio 2019 可讓您將函式部署至 Azure Functions 應用程式。 Azure 裝載的函式可讓您為所有裝置提供可存取的測試端點，以簡化跨平臺測試。

以滑鼠右鍵按一下範例函式應用程式，然後選擇 [**發佈**] 會啟動對話方塊，以將函式發佈至您的 Azure Functions 應用程式。 如果您已遵循先前的步驟來設定 Azure 函數應用程式，您可以選擇 [**選取現有**的]，將範例應用程式發佈至您的 Azure Functions 應用程式。 下列螢幕擷取畫面顯示 Visual Studio 2019 中的 [發佈] 對話方塊選項：

![Visual Studio 2019 中的 [發佈選擇] 對話方塊](azure-signalr-images/vs-publish-target.png "Visual Studio 2019 中的發行選項")

登入您的 Microsoft 帳戶之後，您就可以找出並選擇您的 Azure Functions 應用程式作為發佈目標。 下列螢幕擷取畫面顯示 [Visual Studio 2019 發行] 對話方塊中的範例 Azure Functions 應用程式：

![[Visual Studio 2019 發行] 對話方塊中的 Azure Functions 應用程式](azure-signalr-images/vs-app-selection.png "Visual Studio 2019 發行對話方塊中的 Azure Functions 應用程式")

選取 Azure Functions 應用程式實例之後，就會顯示 [網站 URL]、[設定] 和 [目標 Azure Functions 應用程式的其他相關資訊。 選擇 [**編輯] Azure App Service 設定**]，然後在 [**遠端**] 欄位中輸入您的連接字串。 此連接字串是由**Negotiate**和**交談**函式用來連接到 Azure SignalR Service，而且可在 Azure 入口網站中 Azure SignalR Service 的 [**金鑰**] 區段中取得。 如需連接字串的詳細資訊，請參閱[建立 Azure SignalR Service](#create-an-azure-signalr-service)。

輸入連接字串之後，您可以按一下 [**發佈**]，將您的函式部署到 Azure Functions 應用程式。 完成後，函式會列在 Azure 入口網站的 Azure Functions 應用程式中。 下列螢幕擷取畫面顯示 Azure 入口網站中已發佈的函式：

![Azure Functions 應用程式中發佈的函式](azure-signalr-images/azure-functions-deployed.png "Azure Functions 應用程式中發佈的函式")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>整合 Azure SignalR Service 與Xamarin.Forms

Azure SignalR Service 與應用程式之間的整合 Xamarin.Forms 是 SignalR 服務類別，在類別中具現化， `MainPage` 並將事件處理常式指派給三個事件。 如需這些事件處理常式的詳細資訊，請參閱[使用中 Xamarin.Forms 的 SignalR 服務類別](#use-the-signalr-service-class-in-xamarinforms)。

範例應用程式包含必須使用 Azure Functions 應用程式的 URL 端點自訂的**Constants.cs**類別。 將屬性的值設定 `HostName` 為您 Azure Functions 的應用程式位址。 下列程式碼顯示具有範例值的**Constants.cs**屬性 `HostName` ：

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
> `Username`範例應用程式**Constants.cs**檔中的屬性會使用裝置的 `RuntimePlatform` 值做為使用者名稱。 這可讓您輕鬆測試跨平臺的裝置，並識別要傳送訊息的裝置。 在真實世界的應用程式中，此值可能是在註冊或登入處理期間所收集的唯一使用者名稱。

### <a name="the-signalr-service-class"></a>SignalR 服務類別

`SignalRService`範例應用程式中**ChatClient**專案中的類別會顯示在 Azure Functions 應用程式中叫用函式以連接至 Azure SignalR Service 的實作用。

`SendMessageAsync`類別中的方法 `SignalRService` 是用來將訊息傳送給連接到 Azure SignalR Service 的用戶端。 這個方法會對裝載于 Azure Functions 應用程式中的**交談**函式執行 HTTP POST 要求，包括以 JSON 序列化的 `Message` 物件做為 POST 承載。 **交談**函式會將訊息傳遞至 Azure SignalR Service，以供廣播到所有已連線的用戶端。 下列程式碼顯示 `SendMessageAsync` 方法：

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

`ConnectAsync`類別中的方法會 `SignalRService` 對 Azure Functions 應用程式中所裝載的**Negotiate**函式執行 HTTP GET 要求。 **Negotiate**函式會傳回已還原序列化為類別實例的 JSON `NegotiateInfo` 。 一旦抓取 `NegotiateInfo` 物件之後，就會使用類別的實例，直接向 Azure SignalR Service 註冊 `HubConnection` 。

ASP.NET Core SignalR 會將開啟連接的傳入資料轉譯成訊息，並可讓開發人員定義訊息類型，並依類型將事件處理常式系結至傳入訊息。 `ConnectAsync`方法會為範例應用程式的**Constants.cs**檔中定義的訊息名稱註冊事件處理常式，預設為 "newMessage"。

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
> 根據預設，SignalR 服務會使用 `System.Text.Json` 來序列化和還原序列化 JSON。 與其他程式庫（例如 Newtonsoft）序列化的資料可能無法由 SignalR 服務還原序列化。 `HubConnection`範例專案中的實例包含呼叫來 `AddNewtonsoftJsonProtocol` 指定 JSON 序列化程式。 這個方法是在名為**AspNetCore SignalR**的特殊 NuGet 套件中定義，必須包含在專案中。 如果您使用 `System.Text.Json` 來序列化/還原序列化 JSON 資料，則不應使用此方法和 NuGet 套件。

`AddNewMessage`方法會系結為訊息中的事件處理常式， `ConnectAsync` 如先前的程式碼所示。 當收到訊息時， `AddNewMessage` 會使用提供的訊息資料來呼叫方法 `JObject` 。 `AddNewMessage`方法會將轉換 `JObject` 成類別的實例 `Message` ，然後叫用的處理常式（ `NewMessageReceived` 如果已系結的話）。 下列程式碼顯示 `AddNewMessage` 方法：

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

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>使用中的 SignalR 服務類別Xamarin.Forms

在中使用 SignalR 服務類別 Xamarin.Forms ，是藉由系結程式 `SignalRService` 代碼後置類別中的類別事件來完成 `MainPage` 。

`Connected` `SignalRService` 成功完成 SignalR 連接時，就會引發類別中的事件。 `ConnectionFailed` `SignalRService` 當 SignalR 連接失敗時，會引發類別中的事件。 `SignalR_ConnectionChanged`事件處理常式方法會系結至函式中的兩個事件 `MainPage` 。 這個事件處理常式會根據連接引數來更新 [連接] 和 [傳送] 按鈕狀態 `success` ，並使用方法將事件提供的訊息加入至聊天佇列 `AddMessage` 。 以下程式碼顯示 `SignalR_ConnectionChanged` 事件處理常式方法：

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

`NewMessageReceived` `SignalRService` 從 Azure SignalR Service 收到新的訊息時，就會引發類別中的事件。 `SignalR_NewMessageReceived`事件處理常式方法會系結至函式 `NewMessageReceived` 中的事件 `MainPage` 。 這個事件處理常式會將傳入的 `Message` 物件轉換成字串，並使用方法將其新增至聊天佇列 `AddMessage` 。 以下程式碼顯示 `SignalR_NewMessageReceived` 事件處理常式方法：

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

`AddMessage`方法會將新的訊息當做 `Label` 物件新增至聊天佇列。 `AddMessage`方法通常是由主要 UI 執行緒外部的事件處理常式所呼叫，因此它會強制在主執行緒上進行 UI 更新，以避免發生例外狀況。 下列程式碼顯示 `AddMessage` 方法：

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

您可以在 iOS、Android 和 UWP 上測試 SignalR 交談應用程式，前提是您有：

1. 建立 Azure SignalR Service。
1. 建立 Azure Functions 應用程式。
1. 使用 Azure Functions 應用程式端點自訂**Constants.cs**檔案。

完成這些步驟並執行應用程式之後，按一下 [**連接]** 按鈕會形成與 Azure SignalR Service 的連接。 輸入訊息，然後按一下 [**傳送**] 按鈕，會導致在任何連線的行動應用程式上出現于聊天佇列中的訊息。

## <a name="related-links"></a>相關連結

* [使用 Xamarin 和 SignalR 製作即時行動應用程式](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [SignalR 簡介](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Azure Functions 簡介](/azure/azure-functions/functions-overview)
* [Azure Functions 文件](/azure/azure-functions/)
* [MVVM SignalR 交談範例](https://github.com/lbugnion/sample-xamarin-signalr)

---
title: 使用 Xamarin 的 Azure SignalR Service
description: 利用 Xamarin Azure SignalR Service 和 Azure Functions 入門
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: a4d0f5c5ceefcfe9a36a5fcf10c6fb4937c1db90
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "68739217"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>使用 Xamarin 的 Azure SignalR Service

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresignalr/)

ASP.NET Core SignalR 是一種應用程式模型，可簡化將即時通訊新增至應用程式的過程。 Azure SignalR Service 可讓您快速開發及部署可擴充的 SignalR 應用程式。 Azure Functions 是短期、無伺服器的程式碼方法，可以合併以形成事件驅動、可擴充的應用程式。

本文和範例示範如何結合 Azure SignalR Service 和 Azure Functions 與 Xamarin. 表單，將即時訊息傳遞給連線的用戶端。

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>建立 Azure SignalR Service 和 Azure Functions 應用程式

範例應用程式包含三個主要元件： Azure SignalR Service 中樞、包含兩個函式的 Azure Functions 實例，以及可傳送和接收訊息的行動應用程式。 這些元件的互動方式如下：

1. 行動應用程式會叫用**Negotiate** Azure 函式，以取得 SignalR 中樞的相關資訊。
1. 行動應用程式會使用協調資訊向 SignalR 中樞註冊自己，並形成連接。
1. 註冊之後，行動應用程式會將訊息張貼至「**交談**」 Azure 函式。
1. **交談**函式會將傳入訊息傳遞至 SignalR 中樞。
1. SignalR 中樞會將訊息廣播到所有已連線的行動應用程式實例，包括原始的傳送者。

> [!NOTE]
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

[建立 Azure Functions 應用程式的 ![Screenshot](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

Azure 函式可以部署至 Visual Studio 2019 的 Azure Functions 應用程式實例。 下列各節說明如何將範例應用程式中的兩個函式部署到 Azure Functions 應用程式實例。

### <a name="build-azure-functions-in-visual-studio-2019"></a>Visual Studio 2019 中的組建 Azure Functions

範例應用程式包含名為**ChatServer**的類別庫，其中包含兩個無伺服器 Azure Functions，位於名為**Negotiate.cs**和**Talk.cs**的檔案中。

@No__t_0 函式會使用包含 `AccessToken` 屬性和 `Url` 屬性的 `SignalRConnectionInfo` 物件來回應 web 要求。 行動應用程式會使用這些值，向 SignalR 中樞註冊其本身。 下列程式碼顯示 `Negotiate` 函式：

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

@No__t_0 函式會回應在 POST 主體中提供訊息物件的 HTTP POST 要求。 POST 主體會轉換成 `SignalRMessage` 並轉送到 SignalR 中樞。 下列程式碼顯示 `Talk` 函式：

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

選取 Azure Functions 應用程式實例之後，就會顯示 網站 URL、設定 和 目標 Azure Functions 應用程式的其他相關資訊。 選擇 **編輯 Azure App Service 設定**，然後在 **遠端** 欄位中輸入您的連接字串。 此連接字串是由**Negotiate**和**交談**函式用來連接到 Azure SignalR Service，而且可在 Azure 入口網站中 Azure SignalR Service 的 [**金鑰**] 區段中取得。 如需連接字串的詳細資訊，請參閱[建立 Azure SignalR Service](#create-an-azure-signalr-service)。

輸入連接字串之後，您可以按一下 [**發佈**]，將您的函式部署到 Azure Functions 應用程式。 完成後，函式會列在 Azure 入口網站的 Azure Functions 應用程式中。 下列螢幕擷取畫面顯示 Azure 入口網站中已發佈的函式：

![Azure Functions 應用程式中發佈的函式](azure-signalr-images/azure-functions-deployed.png "Azure Functions 應用程式中發佈的函式")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>整合 Azure SignalR Service 與 Xamarin. 表單

Azure SignalR Service 與 Xamarin. Forms 應用程式之間的整合是 SignalR 服務類別，在 `MainPage` 類別中具現化，並將事件處理常式指派給三個事件。 如需這些事件處理常式的詳細資訊，請參閱[在 Xamarin 中使用 SignalR 服務類別](#use-the-signalr-service-class-in-xamarinforms)。

範例應用程式包含必須使用 Azure Functions 應用程式的 URL 端點自訂的**Constants.cs**類別。 將 [`HostName`] 屬性的值設定為您的 Azure Functions 應用程式位址。 下列程式碼顯示**Constants.cs**屬性，其中包含 `HostName` 值的範例：

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
> 範例應用程式**Constants.cs**檔中的 `Username` 屬性會使用裝置的 `RuntimePlatform` 值做為使用者名稱。 這可讓您輕鬆測試跨平臺的裝置，並識別要傳送訊息的裝置。 在真實世界的應用程式中，此值可能是在註冊或登入處理期間所收集的唯一使用者名稱。

### <a name="the-signalr-service-class"></a>SignalR 服務類別

範例應用程式中**ChatClient**專案中的 `SignalRService` 類別，會顯示在 Azure Functions 應用程式中叫用函式以連接至 Azure SignalR Service 的實作用。

@No__t_1 類別中的 `SendMessageAsync` 方法是用來傳送訊息給連接至 Azure SignalR Service 的用戶端。 這個方法會對裝載于 Azure Functions 應用程式中的**交談**函式執行 HTTP POST 要求，包括以 JSON 序列化的 `Message` 物件作為 POST 承載。 **交談**函式會將訊息傳遞至 Azure SignalR Service，以供廣播到所有已連線的用戶端。 下列程式碼顯示 `SendMessageAsync` 方法：

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

@No__t_1 類別中的 `ConnectAsync` 方法會對裝載于 Azure Functions 應用程式中的**Negotiate**函數執行 HTTP GET 要求。 **Negotiate**函式會傳回已還原序列化為 `NegotiateInfo` 類別之實例的 JSON。 一旦抓取 `NegotiateInfo` 物件之後，就會使用 `HubConnection` 類別的實例，直接向 Azure SignalR Service 註冊。

ASP.NET Core SignalR 會將開啟連接的傳入資料轉譯成訊息，並可讓開發人員定義訊息類型，並依類型將事件處理常式系結至傳入訊息。 @No__t_0 方法會為範例應用程式的**Constants.cs**檔中定義的訊息名稱註冊事件處理常式，預設為 "newMessage"。

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

@No__t_0 方法會系結為 `ConnectAsync` 訊息中的事件處理常式，如先前的程式碼所示。 當收到訊息時，會使用以 `JObject` 提供的訊息資料來呼叫 `AddNewMessage` 方法。 @No__t_0 方法會將 `JObject` 轉換為 `Message` 類別的實例，然後叫用 `NewMessageReceived` 的處理常式（如果已系結）。 下列程式碼顯示 `AddNewMessage` 方法：

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

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>使用 Xamarin 中的 SignalR 服務類別

藉由在 `MainPage` 程式碼後置類別中系結 `SignalRService` 類別事件，即可完成在 Xamarin 中使用 SignalR 服務類別。

當 SignalR 連接成功完成時，就會引發 `SignalRService` 類別中的 `Connected` 事件。 當 SignalR 連接失敗時，會引發 `SignalRService` 類別中的 `ConnectionFailed` 事件。 @No__t_0 事件處理常式方法會系結至 `MainPage` 的函式中的兩個事件。 這個事件處理常式會根據連接 `success` 引數來更新 [連接] 和 [傳送] 按鈕狀態，然後使用 `AddMessage` 方法，將事件提供的訊息加入至聊天佇列。 下列程式碼顯示 `SignalR_ConnectionChanged` 事件處理常式方法：

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

從 Azure SignalR Service 收到新的訊息時，就會引發 `SignalRService` 類別中的 `NewMessageReceived` 事件。 @No__t_0 事件處理常式方法會系結至 `MainPage` 的函式中的 `NewMessageReceived` 事件。 這個事件處理常式會將傳入的 `Message` 物件轉換成字串，然後使用 `AddMessage` 方法將它新增到聊天佇列中。 下列程式碼顯示 `SignalR_NewMessageReceived` 事件處理常式方法：

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

@No__t_0 方法會將新的訊息當做 `Label` 物件新增至聊天佇列。 @No__t_0 方法通常是由主要 UI 執行緒以外的事件處理常式所呼叫，因此它會強制在主執行緒上進行 UI 更新，以避免發生例外狀況。 下列程式碼顯示 `AddMessage` 方法：

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
* [Azure Functions 檔](/azure/azure-functions/)
* [MVVM SignalR 交談範例](https://github.com/lbugnion/sample-xamarin-signalr)

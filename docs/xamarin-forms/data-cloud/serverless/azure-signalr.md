---
title: 使用 Xamarin.Forms azure SignalR 服務
description: 開始使用 Azure SignalR 服務和 Azure Functions 使用 Xamarin.Forms
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: d79ffb844a65c145fd6cb22a5a3e1dfc7a6bfe41
ms.sourcegitcommit: 0f78ec17210b915b43ddab75937de8063e472c70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/27/2019
ms.locfileid: "67418121"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>使用 Xamarin.Forms azure SignalR 服務

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureSignalR)

ASP.NET Core SignalR 是簡化的程序新增至應用程式的即時通訊的應用程式模型。 Azure SignalR 服務可讓快速開發和部署可調整的 SignalR 應用程式。 Azure Functions 是可組合為表單的事件驅動、 可調整的應用程式的短期、 無伺服器程式碼方法。

此文件及範例顯示如何結合 Azure SignalR 服務和 Azure Functions 使用 Xamarin.Forms，將即時訊息傳遞到連線的用戶端。

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>建立 Azure SignalR 服務和 Azure Functions 應用程式

範例應用程式包含三個關鍵元件： Azure SignalR 服務中樞、 Azure Functions 執行個體的兩個函式和行動應用程式可以傳送和接收訊息。 這些元件的互動，如下所示：

1. 行動應用程式叫用**交涉**Azure 函式來取得 SignalR 中樞的相關資訊。
1. 行動應用程式使用的交涉資訊來自行註冊與 SignalR 中樞，並形成連線。
1. 註冊之後，行動應用程式將訊息張貼至**討論**Azure 函式。
1. **討論**函式會將內送訊息傳遞到 SignalR 中樞。
1. SignalR 中樞會廣播到所有已連線的行動應用程式執行個體，包括原始傳送者訊息。

> [!NOTE]
> **交涉**並**討論**可以使用 Visual Studio 2019 和 Azure 執行階段工具，在本機執行範例應用程式中的函式。 不過，Azure SignalR 服務無法在本機模擬，而且很難在本機裝載 Azure Functions 來進行測試的實體或虛擬裝置公開 （expose）。 建議您將 Azure Functions 部署至 Azure Functions 應用程式執行個體，因為這可讓跨平台測試。 部署的詳細資訊，請參閱 <<c0> [ 部署 Azure Functions 與 Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019)。

### <a name="create-an-azure-signalr-service"></a>建立 Azure SignalR 服務

Azure SignalR 服務可由選擇**建立資源**在左上角的 Azure 入口網站，然後搜尋**SignalR**。 Azure SignalR 服務可以在免費層建立。 Azure SignalR 服務必須處於**無伺服器**服務模式。 如果您不小心選擇預設值或傳統的服務模式，您可以稍後在 Azure SignalR 服務內容來進行變更。

下列螢幕擷取畫面顯示如何建立新的 Azure SignalR 服務：

![在 Azure 入口網站中建立 Azure SignalR 服務的螢幕擷取畫面](azure-signalr-images/azure-signalr-create.png "建立 Azure SignalR 服務")

建立之後，**按鍵**Azure SignalR 服務區段包含**連接字串**，用來連線到 SignalR 中樞的 Azure 函式應用程式。 下列螢幕擷取畫面顯示如何尋找 Azure SignalR 服務中的連接字串：

![在 Azure 入口網站的螢幕擷取畫面的 Azure SignalR 連接字串](azure-signalr-images/azure-signalr-connection-string.png "Azure SignalR 連接字串")

此連接字串是用來[部署 Azure Functions 與 Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019)。

### <a name="create-an-azure-functions-app"></a>建立 Azure 函式應用程式

若要測試範例應用程式，您應該在 Azure 入口網站中建立新的 Azure 函式應用程式。 請記下的**應用程式名稱**範例應用程式中使用此 URL **Constants.cs**檔案。 下列螢幕擷取畫面顯示如何建立稱為 「 xdocsfunctions"的新 Azure 函式應用程式：

[![建立 Azure Functions App 的螢幕擷取畫面](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

Azure functions 可以部署到 Azure Functions 應用程式執行個體，從 Visual Studio 2019。 下列各節描述的範例應用程式，Azure Functions 應用程式執行個體中的兩個函式的部署。

### <a name="build-azure-functions-in-visual-studio-2019"></a>建置在 Visual Studio 2019 的 Azure 函式

範例應用程式包含的類別庫，稱為**ChatServer**，其中包括兩個無伺服器 Azure Functions 中的檔案稱為**Negotiate.cs**並**Talk.cs**。

`Negotiate`函式會回應 web 要求`SignalRConnectionInfo`物件，包含`AccessToken`屬性和`Url`屬性。 行動應用程式會使用這些值，向 SignalR 中樞。 下列程式碼示範`Negotiate`函式：

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

`Talk`函式會回應 HTTP POST 要求，提供 POST 主體中的訊息物件。 POST 本文轉換成`SignalRMessage`並轉送至 SignalR 中樞。 下列程式碼示範`Talk`函式：

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

若要深入了解 Azure functions 和 Azure 函式應用程式，請參閱[Azure Functions 文件](/azure/azure-functions/)。

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>部署 Azure Functions 搭配 Visual Studio 2019

Visual Studio 2019 可讓您部署至 Azure 函式應用程式的函式。 Azure 託管函式可簡化跨平台測試提供所有的裝置可存取的測試端點。

範例函式應用程式上按一下滑鼠右鍵，然後選擇**發佈**啟動對話方塊，以發佈至 Azure Functions 應用程式的函式。 如果您遵循上述步驟，設定 Azure 函式應用程式時，您可以選擇**選取現有**範例應用程式發佈至 Azure Functions 應用程式。 下列螢幕擷取畫面顯示 Visual Studio 2019 中發佈 對話方塊選項：

![發佈選項 對話方塊，在 Visual Studio 2019](azure-signalr-images/vs-publish-target.png "在 Visual Studio 2019 發行選項")

一旦您已登入您的 Microsoft 帳戶，您可以找出並選擇您的 Azure 函式應用程式作為發佈目標。 下列螢幕擷取畫面顯示範例 Azure 函式應用程式在 Visual Studio 2019 的發佈對話方塊：

![Azure Functions 應用程式在 Visual Studio 2019 發行 對話方塊中的公式](azure-signalr-images/vs-app-selection.png "Azure 函式應用程式，在 Visual Studio 2019 的發佈對話方塊")

選取 Azure 函式應用程式之後會顯示執行個體、 網站 URL、 組態和目標 Azure Functions 應用程式的其他資訊。 選擇**編輯 Azure App Service 設定**並輸入您的連接字串中**遠端**欄位。 會使用連接字串**交涉**並**討論**函式來連線到 Azure SignalR 服務，適用於**金鑰**Azure SignalR 的區段在 Azure 入口網站中的服務。 如需有關連接字串的詳細資訊，請參閱 <<c0> [ 建立 Azure SignalR 服務](#create-an-azure-signalr-service)。

一旦您輸入的連接字串，您可以按一下**發佈**Azure Functions 應用程式部署您的函式。 完成後，函式會列在 Azure 入口網站中的 Azure 函式應用程式中。 下列螢幕擷取畫面會顯示在 Azure 入口網站中的已發行的函式：

![發佈 Azure 函式應用程式中的函式](azure-signalr-images/azure-functions-deployed.png "發佈 Azure 函式應用程式中的函式")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>Azure SignalR 服務整合 Xamarin.Forms

Azure SignalR 服務與 Xamarin.Forms 應用程式之間的整合是具現化中的 SignalR 服務類別`MainPage`與指派給三個事件的事件處理常式的類別。 如需有關這些事件處理常式的詳細資訊，請參閱 <<c0> [ 使用 Xamarin.Forms 中的 SignalR 服務類別](#use-the-signalr-service-class-in-xamarinforms)。

範例應用程式包含**Constants.cs**必須使用 Azure Functions 應用程式的 URL 端點進行自訂的類別。 值設定`HostName`到您的 Azure 函式應用程式位址的屬性。 下列程式碼示範**Constants.cs**舉例屬性`HostName`值：

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
> `Username`範例應用程式中的屬性**Constants.cs**檔會使用裝置的`RuntimePlatform`作為使用者名稱的值。 這可讓您輕鬆地測試裝置的跨平台，並識別哪些裝置正在傳送訊息。 在真實世界應用程式中，此值很可能是唯一的使用者名稱、 在註冊時收集註冊或登入程序。

### <a name="the-signalr-service-class"></a>SignalR 服務類別

`SignalRService`類別內**ChatClient**範例應用程式中的專案會示範如何叫用 Azure 函式應用程式連接到 Azure SignalR 服務中的函式的實作。

`SendMessageAsync`方法中的`SignalRService`類別用來將訊息傳送至用戶端連接到 Azure SignalR 服務。 這個方法會執行的 HTTP POST 要求**談**函式裝載於 Azure Functions 應用程式，包括 JSON 序列化`Message`作為 POST 裝載的物件。 **討論**函式傳遞訊息至 Azure SignalR 服務廣播到所有已連線的用戶端。 下列程式碼顯示 `SendMessageAsync` 方法：

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

`ConnectAsync`方法中的`SignalRService`類別會執行 HTTP GET 要求以**交涉**裝載於 Azure Functions 應用程式中的函式。 **交涉**函式會傳回 JSON 還原序列化的執行個體`NegotiateInfo`類別。 一次`NegotiateInfo`擷取物件，它用來直接與 Azure SignalR 服務使用的執行個體註冊`HubConnection`類別。

ASP.NET Core SignalR 平移成訊息，開啟的連線從傳入的資料，並可讓開發人員定義訊息類型，並將事件處理常式繫結類型的內送訊息。 `ConnectAsync`方法會註冊範例應用程式中所定義的訊息名稱的事件處理常式**Constants.cs**檔案，這是預設的 「 newMessage"。

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

`AddNewMessage`方法會繫結中的事件處理常式為`ConnectAsync`訊息，因為先前的程式碼所示。 當收到訊息時，`AddNewMessage`方法使用訊息所提供的資料做為呼叫`JObject`。 `AddNewMessage`方法將`JObject`的執行個體`Message`類別，然後再叫用的處理常式`NewMessageReceived`如果其中一個已繫結。 下列程式碼顯示 `AddNewMessage` 方法：

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

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>使用 Xamarin.Forms 中的 SignalR 服務類別

使用 Xamarin.Forms 中的 SignalR 服務類別，即可繫結`SignalRService`類別中的事件`MainPage`程式碼後置類別。

`Connected`中的事件`SignalRService`SignalR 連線已成功完成時，會引發類別。 `ConnectionFailed`中的事件`SignalRService`SignalR 連線失敗時，會引發類別。 `SignalR_ConnectionChanged`事件處理常式方法繫結至這兩個事件中`MainPage`建構函式。 此事件處理常式更新根據連線的連線 和 傳送 按鈕狀態`success`引數，並新增要對談佇列使用的事件所提供的訊息`AddMessage`方法。 下列程式碼示範`SignalR_ConnectionChanged`事件處理常式方法：

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

`NewMessageReceived`中的事件`SignalRService`類別會在從 Azure SignalR 服務收到新訊息時引發。 `SignalR_NewMessageReceived`事件處理常式方法繫結至`NewMessageReceived`中的事件`MainPage`建構函式。 這個事件處理常式會將內送`Message`轉換為字串物件，並將它新增至交談的佇列使用`AddMessage`方法。 下列程式碼示範`SignalR_NewMessageReceived`事件處理常式方法：

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

`AddMessage`方法會加入新郵件`Label`聊天佇列的物件。 `AddMessage`因此它會強制以避免例外狀況在主執行緒上發生的 UI 更新方法通常由從主要 UI 執行緒以外的事件處理常式呼叫。 下列程式碼顯示 `AddMessage` 方法：

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

SignalR 交談應用程式可以在 iOS、 Android 及 UWP 測試，前提是您：

1. 建立 Azure SignalR 服務。
1. 建立 Azure 函式應用程式。
1. 自訂**Constants.cs**檔案與 Azure Functions 應用程式端點。

一旦完成這些步驟，並執行應用程式時，按一下**Connect**按鈕表單與 Azure SignalR 服務的連線。 輸入訊息，然後按一下**傳送**按鈕不會出現在任何交談佇列的訊息會導致連接行動應用程式。

## <a name="related-links"></a>相關連結

* [製作即時與 Xamarin 及 SignalR 的行動裝置應用程式](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [SignalR 簡介](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Azure Functions 簡介](/azure/azure-functions/functions-overview)
* [Azure Functions 文件](/azure/azure-functions/)
* [MVVM SignalR 交談範例](https://github.com/lbugnion/sample-xamarin-signalr)

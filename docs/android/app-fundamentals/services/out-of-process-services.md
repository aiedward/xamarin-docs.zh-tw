---
title: "遠端處理序中執行 Android 的服務"
description: "一般而言，Android 應用程式中的所有元件將會都執行相同的程序。 Android 的服務是值得注意的例外狀況，這可以設定在他們自己的程序中執行並與其他應用程式，包括其他 Android 開發人員共用。 本指南將討論如何建立及使用 xamarin Android 的遠端服務。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 0f018aa666f08792a1693a79e22da150f82712f6
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="running-android-services-in-remote-processes"></a>遠端處理序中執行 Android 的服務

_一般而言，Android 應用程式中的所有元件將會都執行相同的程序。Android 的服務是值得注意的例外狀況，這可以設定在他們自己的程序中執行並與其他應用程式，包括其他 Android 開發人員共用。本指南將討論如何建立及使用 xamarin Android 的遠端服務。_

## <a name="out-of-process-services-overview"></a>不足的程序服務概觀

應用程式啟動時，Android 會建立在其中執行應用程式的處理序。 一般而言，所有元件應用程式會在都執行此一程序。 Android 的服務是值得注意的例外狀況，這可以設定在他們自己的程序中執行並與其他應用程式，包括其他 Android 開發人員共用。 這些類型的服務稱為_遠端服務_或_跨處理序服務_。 這些服務的程式碼將會包含在相同的 APK 主應用程式。不過，當服務已啟動 Android 將會建立新的處理序，只要該服務。 相反地，應用程式的其餘部分相同的程序中執行的服務有時稱為_本機服務_。

一般情況下，不需要實作遠端服務的應用程式。 本機服務是足夠 （和理想） 的應用程式的需求，在許多情況下。 Out-處理序已經它本身的記憶體空間必須由 Android 管理。 雖然這會導致更多成本負擔整體應用程式，有某些情況下，其中可以是自己的處理序中執行的服務優勢：

1. **共用功能**&ndash;某些應用程式開發人員可能會有多個應用程式和所有應用程式之間共用的功能。 封裝中執行它自己的處理序中可能會簡化維護應用程式的 Android 服務該功能。 它也可封裝在自己獨立的 APK 服務，並將它分別部署於應用程式的其餘部分。
2. **改善使用者經驗**&ndash;有兩個可能的方式跨處理序服務可以改善應用程式的使用者體驗。 第一種方式處理記憶體管理。 當記憶體回收 (GC) 循環，GC 完成之前，Android 將會暫停處理序中的所有活動。 使用者可能會察覺此暫停做為 「 間斷"或"jank"。 服務正在執行自己的處理程序，就是服務處理序已暫停，而非應用程式處理序。 此暫停會是比較不會注意到使用者，因為未暫停應用程式處理序 （以及使用者介面）。

    其次，如果處理程序的記憶體需求變得太大，Android 可能會終止該處理程序，以釋放裝置資源。 如果服務有較大的記憶體耗用量，而且它正在相同的程序在 UI，然後當 Android 強制作業回收了這些資源 UI 將會關閉，強制使用者啟動應用程式。 不過，如果 android 已關閉服務，它自己的處理序中執行，UI 程序仍會受影響。 UI 可以繫結，並重新啟動服務，透明的使用者，並繼續正常運作。

3. **改善應用程式效能**&ndash;可能終止或關閉服務處理序的獨立 UI 處理程序。 藉由將長時間啟動工作移至跨處理序服務，很可能的使用者介面的啟動時間可能改善 （假設服務處理序就會保持運作之間 UI 啟動的時間）。

在許多方面，另一個處理序中執行服務的繫結等同於[繫結至本機服務](~/android/app-fundamentals/services/creating-a-service/bound-services.md)。 用戶端將會叫用`BindService`繫結 （並啟動，如有必要） 服務。 `Android.OS.IServiceConnection`會建立物件來管理用戶端與服務之間的連線。 如果用戶端成功繫結至服務，則 Android 將會傳回物件，透過`IServiceConnection`，可用來在服務上叫用方法。 然後會使用此物件的服務與用戶端互動。 若要檢閱，以下是將繫結至服務的步驟：

* **建立目的**&ndash;明確的意圖必須用來對服務繫結。
* **實作和具現化`IServiceConnection`物件** &ndash; `IServiceConnection`物件做為用戶端與服務之間的媒介。  它會負責監視用戶端與伺服器之間的連線。
* **叫用`BindService`方法**&ndash;呼叫`BindService`會發送和 Android，它會負責啟動服務，並建立通訊的上一個步驟中建立的服務連接用戶端與服務之間

需要跨處理序界限會導致額外的複雜性： 的通訊是單向 （用戶端到伺服器） 和用戶端無法直接叫用服務類別上的方法。 請注意，當服務與用戶端執行相同的程序，Android 就會提供`IBinder`物件可能會允許進行雙向通訊。 這不是使用自己的處理序中執行服務的情況。 用戶端與遠端服務與通訊的說明`Android.OS.Messenger`類別。

若要與遠端服務繫結要求用戶端，會叫用 Android`Service.OnBind`存留週期方法，將會傳回內部`IBinder`封裝物件`Messenger`。 `Messenger`薄的包裝函式位於特殊`IBinder`實作所提供的 Android SDK。 `Messenger`負責兩個不同的處理序之間的通訊。 開發人員是在乎序列化訊息、 封送處理訊息跨處理序界限，和用戶端上還原序列化的詳細資料。 這項工作由`Messenger`物件。 下圖顯示用戶端起始跨處理序服務的繫結時，會需要用戶端 Android 元件：

![此圖顯示的步驟和元件繫結至服務的用戶端](out-of-process-services-images/ipc-01.png "圖顯示的步驟和元件繫結至服務的用戶端。")

`Service`遠端程序中的類別會透過相同的生命週期回呼，會進行本機處理序中的繫結的服務，且許多相關的 Api 都相同。 `Service.OnCreate` 用來初始化`Handler`和插入至`Messenger`物件。 同樣地，`OnBind`覆寫，但而不是傳回`IBinder`物件，則服務會傳回`Messenger`。  此圖說明用戶端繫結到它時，服務會發生什麼情況：

![此圖顯示的步驟和元件服務所繫結至遠端用戶端時閒內通過](out-of-process-services-images/ipc-02.png "圖顯示的步驟和元件服務所繫結至遠端用戶端時閒內通過。")

當`Message`接收服務所處理的執行個體中`Android.OS.Handler`。 服務會實作其本身`Handler`子類別必須覆寫`HandleMessage`方法。 這個方法會叫用由`Messenger`並接收`Message`做為參數。 `Handler`會檢查`Message`中繼資料並使用該資訊來叫用服務上的方法。

單向通訊發生於用戶端會建立`Message`物件並分派到服務使用`Messenger.Send`方法。 `Messenger.Send` 將序列化`Message`和 Android 跨處理序界限會將訊息路由傳送，並在服務關閉的序列化資料的手。  `Messenger`所裝載的服務將建立`Message`從內送資料的物件。 這`Message`放入佇列，其中訊息會送出一次一個`Handler`。 `Handler`會檢查中繼資料中包含`Message`上叫用適當的方法和`Service`。 下圖將說明這些動作中的各種概念：

![顯示如何將訊息傳遞處理序之間的圖表](out-of-process-services-images/ipc-03.png "顯示如何將訊息傳遞處理序之間的圖表。")

本指南將討論實作跨處理序服務的詳細資料。 它將討論如何實作自己的處理序中執行的服務和用戶端可能會與該服務使用通訊的方式`Messenger`架構。 它會也簡要地討論的雙向通訊： 傳送訊息至服務和服務將訊息傳送回用戶端的用戶端。 因為服務可以在不同的應用程式之間共用，本指南也會討論的技巧，可以使用 Android 的權限來限制用戶端服務的存取權。

> [!IMPORTANT]
> [Bugzilla 51940-獨立處理序與自訂應用程式類別的服務無法正確解析多載](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)Xamarin.Android 服務將不會啟動正確的報表時`IsolatedProcess`設`true`。 本指南提供的參考。 Xamarin.Android 應用程式應該仍然能夠與以 Java 撰寫的跨處理序服務進行通訊。

## <a name="requirements"></a>需求

本指南假設熟悉建立服務。

雖然可以使用隱含的對應方式與應用程式較舊的目標 Android 應用程式開發介面，本指南會專門著重在使用明確的對應方式。 應用程式的目標 Android 5.0 (API level 21) 或更高版本必須使用明確的意圖繫結與服務。這是將本指南中示範的技術...

## <a name="create-a-service-that-runs-in-a-separate-process"></a>建立個別的處理序中執行的服務

如上面所述，服務正在執行自己的處理序中的事實會表示包含一些不同的應用程式開發介面個。 為快速的概觀，以下是與繫結和使用遠端服務的步驟：  

* **建立`Service`子類別**&ndash;子類別`Service`輸入，並實作繫結服務生命週期的方法。 它也是必要設定會告知服務是在自己的處理序中執行的 Android 的中繼資料。
* **實作`Handler`**  &ndash; `Handler`負責分析用戶端要求，擷取用戶端，從傳遞任何參數並叫用服務上的適當方法。
* **具現化`Messenger`**  &ndash; (如上所述），每個`Service`必須維持的執行個體`Messenger`會路由傳送至用戶端要求的類別`Handler`上一個步驟中所建立。

要在自己的處理序中執行的服務，基本上，仍然是繫結的服務。 服務類別會擴充基底`Service`類別，並使用裝飾`ServiceAttribute`包含 Android 需要包裝 Android 資訊清單中的中繼資料。 若要開始使用的下列屬性`ServiceAttribute`重要跨處理序服務：

1. `Exported` &ndash; 這個屬性必須設定為`true`以允許其他應用程式與服務互動。 此屬性的預設值為 `false`。
2. `Process` &ndash; 必須設定這個屬性。 它用來指定此服務會在執行程序的名稱。
3. `IsolatedProcess` &ndash; 這個屬性會啟用額外的安全性，告訴 Android iteract 系統的其餘部分的最小權限與隔離沙箱中執行的服務。 請參閱[與隔離的處理序和自訂應用程式類別 Bugzilla 51940-服務無法正確解析多載](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)。
4. `Permission` &ndash; 您可指定用戶端必須要求 （以及被授與） 的權限來控制用戶端服務的存取權。

要執行自己的處理序中，服務`Process`屬性`ServiceAttribute`必須設為服務的名稱。 與外部的應用程式互動`Exported`屬性應該設定為`true`。 如果`Exported`是`false`，則相同的 APK （也就是相同的應用程式） 中的唯一用戶端和相同的處理序中執行都能夠與服務互動。

此服務會在執行程序的種類取決於值`Process`屬性。 Android 識別三種不同的處理序：

-   **私用程序**&ndash;私用程序是指只可用於啟動應用程式。 若要識別為私用程序，其名稱開頭必須**:** （以分號分隔）。 先前的程式碼片段和螢幕擷取畫面所示的服務是私用程序。 下列程式碼片段是範例`ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

-   **全域處理程序**&ndash;全域處理程序中執行的服務是在裝置上執行的所有應用程式存取。 全域處理程序必須是小寫字母字元開頭的完整的類別名稱。
    （會採取的步驟，以確保服務安全，除非其他應用程式可能會繫結並與其互動。 保護避免未經授權使用的服務將會討論在本指南稍後。）

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

-   **隔離的處理序**&ndash;隔離的處理序是在它自己與其他系統和其本身的任何特殊權限與隔離的沙箱中執行的程序。 要在隔離的處理序，執行服務`IsolatedProcess`屬性`ServiceAttribute`設`true`此程式碼片段所示：
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> 請參閱[與隔離的處理序和自訂應用程式類別 Bugzilla 51940-服務無法正確解析多載](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

外掛式主控的服務是簡單的方式來保護應用程式和裝置對不受信任的程式碼。 例如，應用程式可能會下載並執行指令碼，從網站。 在此情況下，在 隔離的處理序中執行這提供額外的安全性，以防止洩露 Android 裝置不受信任的程式碼。

> [!IMPORTANT]
> 一旦已匯出的服務，不應該變更服務的名稱。 變更服務的名稱可能會中斷其他應用程式會使用該服務。

若要查看效果，`Process`屬性，下列螢幕擷取畫面會顯示在它自己的私用程序中執行的服務：

![螢幕擷取畫面顯示私用程序中執行的服務](out-of-process-services-images/ipc-04.png "螢幕擷取畫面顯示私用程序中執行的服務。")

這個下一個螢幕擷取畫面顯示`Process="com.xamarin.xample.messengerservice.timestampservice_process"`和全域處理程序中執行的服務：

![執行全域的處理程序中的服務螢幕擷取畫面](out-of-process-services-images/ipc-05.png "螢幕擷取畫面的執行全域的處理程序中的服務。")

一次`ServiceAttribute`已設定，來實作的服務需求`Handler`。

### <a name="implementing-a-handler"></a>實作處理常式

若要處理的用戶端要求，服務必須實作`Handler`並覆寫`HandleMessage`methodThis 是方法會採用`Message`執行個體的封裝方法呼叫從用戶端以及轉譯為某些動作，該呼叫或服務將執行的工作。 `Message`物件會公開屬性，稱為`What`這是整數值的意義用戶端與服務之間共用，服務是用戶端執行的某些工作與相關。

下列程式碼片段來自範例應用程式顯示的其中一個範例`HandleMessage`。 在此範例中，有服務的用戶端可要求之兩個動作：

* 第一個動作是_Hello，World_訊息，用戶端已經傳送的簡單訊息給服務。
* 第二個動作將會叫用服務上的方法，並擷取字串，字串在此情況下會傳回已執行的服務啟動和多久時間的訊息：

```csharp
public class TimestampRequestHandler : Android.OS.Handler
{
    // other code omitted for clarity

    public override void HandleMessage(Message msg)
    {
        int messageType = msg.What;
        Log.Debug(TAG, $"Message type: {messageType}.");

        switch (messageType)
        {
            case Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE:
                // The client as sent a simple Hello, say in the Android Log.
                break;

            case Constants.GET_UTC_TIMESTAMP:
                // Call methods on the service to retrive a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

它也可在服務封裝參數`Message`。 這將在本指南稍後討論。 正在建立下一個要考慮主題`Messenger`物件以處理內送`Message`s。

### <a name="instantiating-the-messenger"></a>具現化 Messenger

如先前所討論，還原序列化`Message`物件和叫用`Handler.HandleMessage`是的 responsibilty`Messenger`物件。 `Messenger`類別也提供`IBinder`物件在用戶端將用來傳送訊息至服務。  

當服務啟動時，它會執行個體化`Messenger`，以及將`Handler`。 若要執行這類初始化最好先上`OnCreate`服務方法。 此程式碼片段會初始化它自己的服務的其中一個範例`Handler`和`Messenger`:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

此時，最後一個步驟是`Service`覆寫`OnBind`。

### <a name="implementing-serviceonbind"></a>實作 Service.OnBind

所有繫結的服務，不管它們執行自己的程序，必須實作`OnBind`方法。 這個方法的傳回值是某些用戶端可用來與服務互動的物件。 只有該物件是什麼，取決於服務是否為本機服務或遠端服務。 雖然本機服務將會傳回自訂`IBinder`實作中，將會傳回遠端服務`IBinder`封裝，但`Messenger`前一節中所建立：

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

一旦完成這三個步驟，遠端服務視為完成。

## <a name="consuming-the-service"></a>使用服務

所有用戶端必須實作能夠繫結，並使用遠端服務的一些程式碼。 在概念上，從用戶端的觀點來看，很少之間有差異本機服務或遠端服務繫結。 用戶端叫用`BindService`方法，傳遞明確用途來識別服務和`IServiceConnection`有助於管理用戶端與服務之間的連線。

此程式碼片段是如何建立範例**明確意圖**遠端服務繫結。 意圖必須識別包含服務和服務名稱的封裝。 若要設定這項資訊的一種方式為使用`Android.Content.ComponentName`物件，並提供的意圖。 此程式碼片段是一個範例：  

```csharp
// This is the package name of the APK, set in the Android manifest
const string REMOTE_SERVICE_COMPONENT_NAME = "com.xamarin.TimestampService";
// This is the name of the service, according the value of ServiceAttribute.Name
const string REMOTE_SERVICE_PACKAGE_NAME   = "com.xamarin.xample.messengerservice";

// Provide the package name and the name of the service with a ComponentName object.
ComponentName cn = new ComponentName(REMOTE_SERVICE_PACKAGE_NAME, REMOTE_SERVICE_COMPONENT_NAME);
Intent serviceToStart = new Intent();
serviceToStart.SetComponent(cn);
```

服務繫結，`IServiceConnection.OnServiceConnected`方法會叫用，並提供`IBinder`給用戶端。 不過，用戶端不會直接使用`IBinder`。 相反地，它會具現化`Messenger`從該物件`IBinder`。 這是`Messenger`用戶端將用來與遠端服務互動。

下列是範例非常基本的`IServiceConnection`實作，以示範如何在用戶端會處理連接與中斷連接的服務。 請注意，`OnServiceConnected`方法會接收和`IBinder`，和用戶端會建立`Messenger`與`IBinder`:

```csharp
public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection
{
    static readonly string TAG = typeof(TimestampServiceConnection).FullName;

    MainActivity mainActivity;
    Messenger messenger;

    public TimestampServiceConnection(MainActivity activity)
    {
        IsConnected = false;
        mainActivity = activity;
    }

    public bool IsConnected { get; private set; }
    public Messenger Messenger { get; private set; }

    public void OnServiceConnected(ComponentName name, IBinder service)
    {
        Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

        IsConnected = service != null
        Messenger = new Messenger(service);

        if (IsConnected)
        {
            // things to do when the connection is successful. perhaps notify the client? enable UI features?
        }
        else
        {
            // things to do when the connection isn't successful.
        }
    }

    public void OnServiceDisconnected(ComponentName name)
    {
        Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
        IsConnected = false;
        Messenger = null;

        // Things to do when the service disconnects. perhaps notify the client? disable UI features?

    }
}
```

一旦建立服務連線與意圖，呼叫用戶端可能是`BindService`啟動繫結程序：

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

用戶端已成功地繫結至服務之後，`Messenger`為可用，很可能讓用戶端傳送`Messages`服務。

## <a name="sending-messages-to-the-service"></a>傳送訊息至服務

一旦用戶端已連線且已`Messenger`物件時，可能與服務通訊的分派`Message`物件透過`Messenger`。 此通訊是單向通訊，用戶端傳送訊息，但沒有傳回訊息從服務到用戶端。 在這方面`Message`和不理 」 的機制。

若要建立的慣用的方式`Message`物件是使用[ `Message.Obtain` ](https://developer.xamarin.com/api/type/Android.OS.Message/#Public%20Methods) factory 方法。 這個方法會提取`Message`維護由 Android 全域集區中的物件。 `Message.Obtain` 也有一些多載方法，可讓`Message`要使用服務所需要的參數與值來初始化物件。  一次`Message`是具現化，它分派至服務藉由呼叫`Messenger.Send`。 此程式碼片段是一個範例中建立與分派的`Message`到服務處理序：

```csharp
Message msg = Message.Obtain(null, Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE);
try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a error trying to send the message.");
}
```

有數種不同形式的`Message.Obtain`方法。 先前的範例使用[ `Message.Obtain(Handler h, Int32 what)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/)。 因為這是跨處理序服務的非同步要求將服務，無回應而`Handler`設`null`。 第二個參數， `Int32 what`，將會儲存在`.What`屬性`Message`物件。 `.What`屬性可由服務處理序中的程式碼來叫用服務上的方法。

`Message`類別也會公開兩個額外的屬性可能用來決定 recipent:`Arg1`和`Arg2`。 這兩個屬性是整數值，可能會有一些特殊同意的有意義的用戶端與服務之間的值。 例如，`Arg1`可能含有客戶識別碼和`Arg2`可能含有該客戶訂單編號。 [ `Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/System.Int32/System.Int32/)可以用來設定兩個屬性時`Message`建立。 擴展這兩個值的另一個方法是設定`.Arg`和`.Arg2`屬性直接依據`Message`之後已經建立的物件。

### <a name="passing-additional-values-to-the-service"></a>傳遞至服務的額外值

您可使用更複雜的資料傳遞至服務`Bundle`。 在此情況下，額外的值可以放在`Bundle`及傳送連同`Message`藉由設定[`.Data`屬性](https://developer.xamarin.com/api/property/Android.OS.Message.Data/)傳送之前的屬性。

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> 一般情況下，`Message`不應該有裝載超過 1 MB。 大小限制可能會依根據 Android 版本及任何專屬變更廠商可能會對其實作的 Android 開啟來源專案 (AOSP) 的裝置套件組合。

## <a name="returning-values-from-the-service"></a>從服務傳回的值

是單向的訊息已討論至目前為止的架構，用戶端傳送訊息至服務。 如果要將值傳回給用戶端的服務需要然後已討論至目前為止的所有內容會反轉。 服務必須建立`Message`，封裝任何傳回值，以及分派`Message`透過`Messenger`給用戶端。 不過，服務不會建立自己`Messenger`; 相反地，它會仰賴具現化用戶端和套件`Messenger`做為初始要求的一部分。 此服務會`Send`使用此用戶端提供的訊息`Messenger`。  

這是雙向通訊的事件順序：

1. 用戶端繫結至服務。 當服務和用戶端連線時， `IServiceConnection` ，由維護用戶端便會參考`Messenger`物件，用來傳輸`Message`s 至服務。 為了避免混淆，這將稱為_服務 Messenger_。
2. 用戶端會具現化`Handler`(稱為_用戶端處理常式_) 並使用該值來初始化自己`Messenger`(_用戶端 Messenger_)。 請注意，服務 Messenger 和用戶端 Messenger 是兩個不同的物件，處理兩個不同的方向的流量。 服務訊息處理服務，從用戶端的訊息，而用戶端 Messenger 將會處理從服務至用戶端的訊息。
3. 用戶端會建立`Message`物件和集合`ReplyTo`屬性與用戶端 Messenger。 然後會將訊息傳送至使用服務 Messenger 服務。
4. 服務會接收來自用戶端的訊息，並執行要求的工作。
5. 當服務傳送回應給用戶端時，它會使用`Message.Obtain`來建立新的`Message`物件。
6. 此訊息傳送至用戶端，服務將會擷取從用戶端 Messenger`.ReplyTo`屬性，用戶端的訊息，並使用它來`.Send``Message`傳回給用戶端。
7. 用戶端收到回應時，它有自己`Handler`，將會處理`Message`藉由檢查`.What`屬性 (如有必要，擷取所包含的任何參數`Message`)。

此程式碼範例會示範如何在用戶端會具現化`Message`和封裝`Messenger`服務應該使用它的回應：

```csharp
Handler clientHandler = new ActivityHandler();
Messenger clientMessenger = new Messenger(activityHandler);

Message msg = Message.Obtain(null, Constants.GET_UTC_TIMESTAMP);
msg.ReplyTo = clientMessenger;

try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a problem sending the message.");
}
```

服務必須進行一些變更，以自己`Handler`擷取`Messenger`並用來傳送回覆至用戶端。 此程式碼片段是如何的範例服務的`Handler`會建立`Message`並將它傳送回用戶端：  

```csharp
// This is the message that the service will send to the client.
Message responseMessage = Message.Obtain(null, Constants.RESPONSE_TO_SERVICE);
Bundle dataToReturn = new Bundle();
dataToReturn.PutString(Constants.RESPONSE_MESSAGE_KEY, "This is the result from the service.");
responseMessage.Data = dataToReturn;

// The msg object here is the message that was received by the service. The service will not instantiate a client,
// It will use the client that is encapsulated by the message from the client.
Messenger clientMessenger = msg.ReplyTo;
if (clientMessenger!= null)
{
    try
    {
        clientMessenger.Send(responseMessage);
    }
    catch (Exception ex)
    {
        Log.Error(TAG, ex, "There was a problem sending the message.");
    }
}
```

請注意，在上面的程式碼範例`Messenger`由用戶端的執行個體是*不*相同服務所收到的物件。 這些是兩個不同`Messenger`代表通訊通道的兩個不同處理序中執行的物件。

## <a name="securing-the-service-with-android-permissions"></a>設定 Android 的權限與服務安全性

Android 裝置上執行的所有應用程式所存取的通用程序中執行的服務。 在某些情況下，此開放性和可用性不適當時，而且它不需要防範未經授權的用戶端存取服務。 限制遠端服務的存取權的一種方式是使用 Android 的權限。

權限可以藉由識別`Permission`屬性`ServiceAttribute`裝飾`Service`子類別。 這將會命名為繫結至服務時，必須授與用戶端的權限。 如果用戶端並沒有適當的權限，則會擲回 Android`Java.Lang.SecurityException`用戶端嘗試繫結至服務。

有 Android 所提供的四個不同的權限層級：

* **一般**&ndash;這是預設的權限層級。 它用來識別低風險可以自動授與權限 android 要求它的用戶端。 使用者不必明確地授與這些權限，但權限可以檢視應用程式設定。
* **簽章**&ndash;這是一種特殊類別將會自動授與 android 應用程式簽署的相同憑證的權限。 此權限的設計旨在讓它輕鬆的應用程式開發人員共用元件或終究常數核准的使用者其應用程式之間的資料。
* **signatureOrSystem** &ndash;這是非常類似於**簽章**上面所述的權限。 除了自動授與給相同的憑證所簽署的應用程式，此權限也會授與所簽署的應用程式相同的憑證用來簽署應用程式安裝在 Android 系統映像。 此權限通常只供 Android ROM 開發人員以允許應用程式，以使用協力廠商應用程式。 它不是常用的應用程式預定的散播一般分佈。
* **危險**&ndash;危險的使用權限是那些可能會導致使用者的問題。 基於這個理由，**危險**使用者必須明確地核准權限。

因為`signature`和`normal`權限自動授與在安裝階段 Android 相當重要的程式安裝在裝載服務的 APK**之前**APK 包含用戶端。 如果第一次安裝用戶端，Android 將會授與權限。 在此情況下，必須解除安裝用戶端 APK，安裝服務 APK，然後再重新安裝用戶端 APK。

有兩個常用的方式來保護含有 Android 的權限的服務：

1.  **實作簽章的層級安全性**&ndash;簽章的層級安全性表示權限自動授與這些會以用來簽署 APK 保留服務的相同金鑰簽署的應用程式。 這是簡單的方式保護他們的服務，同時保有其可從自己的應用程式存取的開發人員。 簽章的層級權限的宣告方式設定`Permission`屬性`ServiceAttribute`至`signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2.  **建立自訂的權限**&ndash;便可讓服務開發人員建立服務的自訂權限。 這是最佳的開發人員要與其他開發人員提供的應用程式共用其服務。 自訂的權限需要更多工作來實作，將以下涵蓋。

建立自訂的簡化的範例`normal`權限會在下一節中說明。 如需 Android 的權限的詳細資訊，請參閱 Google 的文件[最佳作法與安全性](https://developer.android.com/training/articles/security-tips.html)。 如需 Android 的權限的詳細資訊，請參閱[權限 區段](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)Android 的權限的詳細資訊的應用程式資訊清單的 Android 文件。

> [!NOTE]
> 一般情況下， [Google 阻止使用自訂權限](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions)時可能很令人混淆使用者。

### <a name="creating-a-custom-permission"></a>建立自訂的權限

若要使用的自訂權限，它是由宣告服務時用戶端明確地要求該權限。

在服務 APK 中建立權限`permission`項目加入至`manifest`中的項目**AndroidManifest.xml**。 必須要有此權限`name`， `protectionLevel`，和`label`屬性集。 `name`屬性必須設定為字串，可唯一識別的權限。 名稱將顯示在**應用程式資訊**檢視**Android 設定**（如在下一節中所示）。

`protectionLevel`屬性必須設定為其中一個上面所述的四個字串值。  `label`和`description`必須參考字串資源，而且用來提供給使用者的使用者易記的名稱和描述。

這個程式碼片段會宣告自訂的範例`permission`屬性**AndroidManifest.xml**的 APK 包含服務：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerservice">

    <uses-sdk android:minSdkVersion="21" />

    <permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP"
                android:protectionLevel="signature"
                android:label="@string/permission_label"
                android:description="@string/permission_description"
                />

    <application android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">

    </application>
</manifest>
```

然後， **AndroidManifest.xml**用戶端的 APK 必須明確地要求這個新權限。 這是藉由新增`users-permission`屬性**AndroidManifest.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerclient">

    <uses-sdk android:minSdkVersion="21" />

    <uses-permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP" />

    <application
            android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">
    </application>
    </manifest>
```

### <a name="view-the-permissions-granted-to-an-app"></a>檢視權限授與應用程式

若要檢視應用程式已被授與的權限，開啟 Android 設定應用程式，然後選取**應用程式**。 尋找並選取清單中的應用程式。 從**應用程式資訊**畫面上，點選**權限**這將會出現 顯示所有應用程式授與的權限的檢視：

[![從 Android 裝置，顯示如何尋找應用程式授與的權限的螢幕擷取畫面](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>總結

本指南是有關如何在遠端處理序中執行 Android 的服務的進階的討論。 已說明本機和遠端服務之間的差異，以及遠端服務可以很有幫助穩定性和效能的 Android 應用程式的一些原因。 之後的說明如何實作遠端服務和用戶端可以與服務通訊的方式，本指南發生要提供一個方式來限制服務的授權的用戶端存取。


## <a name="related-links"></a>相關連結

- [Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Message](https://developer.xamarin.com/api/type/Android.OS.Message/)
- [Messenger](https://developer.xamarin.com/api/type/Android.OS.Messenger/)
- [ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)
- [匯出屬性](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [與隔離的處理序和自訂應用程式類別的服務將無法正確解析多載](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [處理序和執行緒](https://developer.android.com/guide/components/processes-and-threads.html)
- [Android 資訊清單的權限](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [安全性提示](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/MessengerServiceDemo/)

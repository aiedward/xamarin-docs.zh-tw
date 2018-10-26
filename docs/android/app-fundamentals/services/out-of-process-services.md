---
title: 執行 Android 的服務，在遠端處理程序
description: 一般而言，Android 應用程式中的所有元件將會都執行相同的程序。 Android 服務是值得注意的例外，其中可以設定在他們自己的程序中執行並與其他應用程式，包括從其他 Android 開發人員共用。 本指南會討論如何建立和使用使用 Xamarin 在 Android 遠端服務。
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 3a11fe56da6076ba53e97643e6890f0fa14b8036
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113933"
---
# <a name="running-android-services-in-remote-processes"></a>執行 Android 的服務，在遠端處理程序

_一般而言，Android 應用程式中的所有元件將會都執行相同的程序。Android 服務是值得注意的例外，其中可以設定在他們自己的程序中執行並與其他應用程式，包括從其他 Android 開發人員共用。本指南會討論如何建立和使用使用 Xamarin 在 Android 遠端服務。_

## <a name="out-of-process-services-overview"></a>不足的程序服務概觀

當應用程式啟動時，Android 會建立程序是指執行應用程式。 一般而言，所有元件的應用程式會在都執行此一程序。 Android 服務是值得注意的例外，其中可以設定在他們自己的程序中執行並與其他應用程式，包括從其他 Android 開發人員共用。 這些類型的服務指_遠端服務_或是_放大處理序服務_。 這些服務的程式碼將會包含在與主應用程式; 相同的 APK不過，當該服務已啟動 Android 將會建立新的處理序，只要該服務。 相反地，在應用程式的其餘部分相同的程序中執行的服務有時稱為_本機服務_。

一般情況下，不需要實作遠端服務的應用程式。 本機服務是足夠的 （和需要的） 在許多情況下的應用程式的需求。 Out-處理序具有本身的記憶體空間必須受 Android。 雖然這會導致更多成本負擔整體的應用程式，有一些可幫助在自己的處理序中執行服務的案例：

1. **共用功能**&ndash;某些應用程式開發人員可能會有多個應用程式和所有應用程式之間共用的功能。 封裝自己的處理序中執行可能會簡化應用程式維護 Android 服務這項功能。 它也可封裝自己獨立的 APK 中的服務，然後分別部署的應用程式的其餘部分。
2. **改善使用者經驗**&ndash;有兩種可能的方式，跨處理序服務，可以改善應用程式的使用者經驗。 第一種方式處理記憶體管理。 當記憶體回收 (GC) 週期發生時，GC 完成之前，Android 會暫停處理程序中的所有活動。 使用者可能會察覺此暫停"間斷 」 或 「 jank"。 服務正在執行時是自己的程序，它是服務程序，已暫停，而非應用程式處理序。 此暫停會是使用者比較不明顯，因為未暫停應用程式處理序 （以及使用者介面）。

    其次，如果處理程序的記憶體需求變得太大，Android 可能會終止該處理程序，以便釋出資源的裝置。 如果服務有較大的記憶體耗用量，而且它相同的程序，為 UI 中執行，然後當 Android 強制回收這些資源 UI 將會關閉，強迫使用者啟動應用程式。 不過，如果服務，在自己的處理序中執行遭到關閉 android，UI 程序仍然不會受到影響。 UI 可以繫結，並重新啟動服務時，透明的使用者，並繼續正常運作。

3. **改善應用程式效能** &ndash; UI 處理程序可能會終止，或關閉服務處理程序無關。 藉由將冗長的啟動工作移至跨處理序服務，就可以啟動時間的 UI 可能改善 （假設服務處理序會保持運作，UI 會啟動的時間之間的）。

在許多方面，另一個處理序中執行服務的繫結等同[繫結至本機服務](~/android/app-fundamentals/services/creating-a-service/bound-services.md)。 用戶端將會叫用`BindService`繫結 （和啟動，如有必要） 服務。 `Android.OS.IServiceConnection`物件將會建立管理用戶端與服務之間的連線。 如果用戶端已成功繫結至服務，則 Android 將會傳回物件，透過`IServiceConnection`，可用來叫用服務上的方法。 用戶端然後會使用這個物件與服務互動。 若要檢閱，以下是繫結至服務的步驟：

* **建立意圖**&ndash;明確 intent 必須用來對服務繫結。
* **實作和具現化`IServiceConnection`物件** &ndash; `IServiceConnection`物件做為用戶端與服務之間的媒介。  它會負責監視用戶端與伺服器之間的連線。
* **叫用`BindService`方法**&ndash;呼叫`BindService`會分派的意圖和 Android，它會負責啟動服務，並建立通訊的前一個步驟中建立的服務連接之間用戶端和服務。

需要跨處理序界限會引進額外的複雜性： 通訊是單向 （用戶端到伺服器），且用戶端無法直接叫用服務類別上的方法。 您應該記得，當服務與用戶端執行相同的程序，Android 就會提供`IBinder`物件可能會允許雙向通訊。 這不是在自己的處理序中執行服務的情況。 用戶端通訊與遠端服務的協助`Android.OS.Messenger`類別。

當用戶端要求來與遠端服務繫結時，將會叫用 Android`Service.OnBind`生命週期方法，它會傳回內部`IBinder`物件所封裝`Messenger`。 `Messenger`是一種特殊的精簡型包裝函式`IBinder`Android SDK 所提供的實作。 `Messenger`會處理兩個不同的處理序之間的通訊。 開發人員會在乎序列化訊息，訊息的封送處理，跨處理序界限，再用戶端上還原序列化的詳細資料。 這項工作由`Messenger`物件。 下圖顯示當用戶端啟動跨處理序服務的繫結牽涉到的用戶端 Android 元件：

![此圖顯示的步驟和元件繫結至服務的用戶端](out-of-process-services-images/ipc-01.png "此圖顯示的步驟和元件繫結至服務的用戶端。")

`Service`遠端程序中的類別會經歷相同的生命週期回呼繫結的服務在本機處理序中將會經歷，也有許多相關的 api 相同。 `Service.OnCreate` 用來初始化`Handler`，以及插入至`Messenger`物件。 同樣地，`OnBind`會覆寫，但不傳回`IBinder`物件，則服務會傳回`Messenger`。  此圖說明用戶端繫結至它時，在服務中會發生什麼事：

![當遠端用戶端繫結至圖表，顯示的步驟和元件的服務會通過](out-of-process-services-images/ipc-02.png "時繫結至遠端用戶端會通過此圖顯示的步驟和元件服務。")

當`Message`收到的服務所處理的執行個體中`Android.OS.Handler`。 服務會實作自己`Handler`子類別必須覆寫`HandleMessage`方法。 這個方法會叫用`Messenger`，並接收`Message`做為參數。 `Handler`會檢查`Message`中繼資料，並使用該資訊來叫用服務上的方法。

當用戶端建立時，就會發生單向通訊`Message`物件，並將它分派到服務時使用`Messenger.Send`方法。 `Messenger.Send` 將序列化`Message`和 Android，就會跨處理序界限來路由訊息，並在服務關閉的序列化資料的手。  `Messenger`裝載的服務會建立`Message`從內送資料的物件。 這`Message`放入佇列，其中訊息會一次提交的一個`Handler`。 `Handler`會檢查所包含的中繼資料`Message`上叫用適當的方法和`Service`。 下圖說明這些不同的概念的動作：

![此圖顯示如何將訊息傳遞處理序之間](out-of-process-services-images/ipc-03.png "圖表，顯示如何處理序之間傳遞訊息。")

本指南會討論實作的跨處理序服務的詳細資料。 它將討論如何實作自己的處理序中執行的服務和用戶端可能會與該服務使用的通訊方式`Messenger`framework。 它同時也概述將討論的雙向通訊： 傳送訊息至服務和服務將訊息傳送回用戶端的用戶端。 因為服務可以在不同的應用程式之間共用，則本指南也會討論的技巧，可以使用 Android 的權限來限制用戶端服務的存取權。

> [!IMPORTANT]
> [Bugzilla 51940-隔離的程序與自訂應用程式類別的服務無法正確解析多載](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)Xamarin.Android 服務將無法啟動正確的報表時`IsolatedProcess`設定為`true`。 本指南可供參考。 Xamarin.Android 應用程式應該仍然能夠與以 Java 撰寫的跨處理序服務進行通訊。

## <a name="requirements"></a>需求

本指南假設您熟悉建立服務。

雖然您可以使用隱含的對應方式與應用程式該較舊的目標 Android Api，本指南會專門著重在使用明確的對應方式。 目標 Android 5.0 (API level 21) 的應用程式或更新版本必須使用明確的意圖繫結與服務;這是將本指南中示範的技術...

## <a name="create-a-service-that-runs-in-a-separate-process"></a>建立會在不同的處理序中執行的服務

如上面所述，服務正在執行自己的處理序中的事實表示一些不同的 Api 相關。 為快速概觀，以下是繫結和使用遠端服務的步驟：  

* **建立`Service`子類別**&ndash;子類別`Service`輸入，並實作繫結的服務的生命週期方法。 它也是以將中繼資料，其會通知服務是在自己的處理序中執行的 Android 設定必要的。
* **實作`Handler`**  &ndash; `Handler`負責分析用戶端要求，擷取用戶端，從傳遞任何參數叫用服務上適當的方法。
* **具現化`Messenger`**  &ndash;所述，每個`Service`必須維持的執行個體`Messenger`類別會路由傳送到用戶端要求`Handler`上一個步驟中建立。

要在自己的處理序中執行的服務基本上是仍然繫結的服務。 服務類別會擴充基底`Service`類別，並且以裝飾`ServiceAttribute`包含 Android 需要在 Android 資訊清單中項目組合的中繼資料。 若要開始的下列屬性`ServiceAttribute`重要處理序外服務：

1. `Exported` &ndash; 此屬性必須設為`true`允許其他應用程式與服務互動。 此屬性的預設值為 `false`。
2. `Process` &ndash; 必須設定此屬性。 它用來指定此服務會在執行程序的名稱。
3. `IsolatedProcess` &ndash; 此屬性可讓額外的安全性，告知 Android 在隔離的沙箱，具有 iteract 與系統的其餘部分的最小權限執行服務。 請參閱[隔離的程序與自訂應用程式類別的 Bugzilla 51940-服務無法正確解析多載](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)。
4. `Permission` &ndash; 您可指定用戶端必須要求 （並授與） 的權限來控制用戶端服務的存取權。

若要執行 service fabric 自己的處理序`Process`屬性上的`ServiceAttribute`必須設定為服務的名稱。 與外部的應用程式互動`Exported`屬性應設為`true`。 如果`Exported`是`false`，則只有在同一個 APK （也就是相同的應用程式） 中的用戶端並執行相同的程序都能夠與服務互動。

此服務會在執行的程序的種類取決於值`Process`屬性。 Android 識別程序的三種不同的類型：

-   **私用程序**&ndash;私用程序是一個只會提供給啟動應用程式。 若要識別為私用程序，其名稱必須以開頭 **:** （分號）。 上述程式碼片段和螢幕擷取畫面所示的服務是私用程序。 下列程式碼是範例`ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

-   **通用的處理程序**&ndash;通用的處理序中執行的服務是在裝置上執行的所有應用程式存取。 通用的處理程序必須以小寫字元開頭的完整的類別名稱。
    （除非採取步驟來保護服務，其他應用程式可能會繫結並與其互動。 保護服務，避免未經授權的使用會討論稍後在本指南中。）

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

-   **隔離程序**&ndash;隔離處理序是在它自己的系統，並具有自己的無特殊權限的其餘部分隔離的沙箱中執行的程序。 若要執行 service fabric 在隔離的處理序，`IsolatedProcess`的屬性`ServiceAttribute`設定為`true`中此程式碼片段所示：
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> 請參閱[隔離的程序與自訂應用程式類別的 Bugzilla 51940-服務無法正確解析多載](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

獨立的服務是簡單的方式來保護應用程式和裝置不受信任的程式碼。 例如，應用程式可能會下載，並從網站執行指令碼。 在此情況下，隔離處理序中執行這提供一層額外的安全性，以防止洩露的 Android 裝置不受信任的程式碼。

> [!IMPORTANT]
> 一旦已匯出的服務，不應該變更服務的名稱。 變更服務的名稱可能會中斷其他使用服務的應用程式。

若要查看效果，`Process`屬性，下列螢幕擷取畫面顯示在它自己的私用程序中執行的服務：

![顯示在私用程序中執行的服務的螢幕擷取畫面](out-of-process-services-images/ipc-04.png "螢幕擷取畫面顯示在私用程序中執行的服務。")

這個下一步 的螢幕擷取畫面顯示`Process="com.xamarin.xample.messengerservice.timestampservice_process"`和通用的處理程序中執行的服務：

![在全域的程序中執行之服務的螢幕擷取畫面](out-of-process-services-images/ipc-05.png "通用的處理序中執行服務的螢幕擷取畫面。")

一次`ServiceAttribute`已設定，服務需要實作`Handler`。

### <a name="implementing-a-handler"></a>實作處理常式

若要處理用戶端要求，服務必須實作`Handler`，並覆寫`HandleMessage`methodThis 是此方法會採用`Message`哪些其封裝方法呼叫從用戶端，而且會轉譯成某些動作呼叫執行個體或此服務會執行的工作。 `Message`物件會公開一個稱為屬性`What`這是整數值，其意義用戶端與服務之間共用，以及與相關聯的服務是用戶端執行一些工作。

下列程式碼片段來自範例應用程式顯示的其中一個範例`HandleMessage`。 在此範例中，有兩個動作，用戶端可以要求的服務：

* 第一個動作是_Hello，World_訊息，用戶端傳送簡單的訊息至服務。
* 第二個動作會叫用服務上的方法，並擷取字串，則字串會傳回時間已執行的服務啟動以及訊息的在此情況下：

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

它也可在服務封裝參數`Message`。 這將會在本指南稍後討論。 在建立下一個要考慮主題`Messenger`物件來處理內送`Message`s。

### <a name="instantiating-the-messenger"></a>具現化 Messenger

如先前所討論，還原序列化`Message`物件，並叫用`Handler.HandleMessage`是的 responsibilty`Messenger`物件。 `Messenger`類別也會提供`IBinder`物件，用戶端將用來將訊息傳送至服務。  

當服務啟動時，它會執行個體化`Messenger`，並將`Handler`。 執行這類初始化的好地方位於`OnCreate`服務方法。 此程式碼片段是一項服務，初始化它自己的其中一個範例`Handler`和`Messenger`:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

到目前為止，最後一個步驟適合`Service`覆寫`OnBind`。

### <a name="implementing-serviceonbind"></a>實作 Service.OnBind

所有繫結的服務，無論是執行自己的程序，必須實作`OnBind`方法。 這個方法的傳回值是一些用戶端可用來與服務互動的物件。 到底什麼該物件是取決於服務是否為本機服務或遠端服務。 雖然本機服務會傳回自訂`IBinder`實作中，遠端服務會傳回`IBinder`封裝，但`Messenger`上一節中所建立：

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

一旦完成這三個步驟是，遠端服務視為完成。

## <a name="consuming-the-service"></a>取用服務

所有用戶端必須實作能夠繫結，並使用遠端服務的一些程式碼。 在概念上，從用戶端的觀點來看，有極少數差異本機服務或遠端服務繫結。 用戶端叫用`BindService`方法，傳遞明確意圖來識別服務和`IServiceConnection`可協助管理用戶端與服務之間的連線。

此程式碼片段是如何建立的範例**明確 intent**遠端服務繫結。 目的必須識別的封裝，包含服務和服務的名稱。 若要設定這項資訊的一個方式是使用`Android.Content.ComponentName`物件，並提供的意圖。 此程式碼片段是一個範例：  

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

服務繫結時，`IServiceConnection.OnServiceConnected`方法會叫用，並提供`IBinder`給用戶端。 不過，用戶端不會直接使用`IBinder`。 相反地，它會執行個體化`Messenger`物件，從`IBinder`。 這是`Messenger`用戶端將用來與遠端服務互動。

以下是範例非常基本的`IServiceConnection`實作，以示範如何在用戶端會處理連接到與中斷連接的服務。 請注意，`OnServiceConnected`方法會接收並`IBinder`，和用戶端會建立`Messenger`於`IBinder`:

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

建立服務連接和意圖之後, 就可以讓用戶端呼叫`BindService`和起始繫結程序：

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

用戶端已成功繫結至服務之後，`Messenger`是可用，就可以讓用戶端傳送`Messages`至服務。

## <a name="sending-messages-to-the-service"></a>將訊息傳送至服務

一旦用戶端已連線且已`Messenger`物件，就可以與服務通訊的分派`Message`物件透過`Messenger`。 此通訊是單向的用戶端傳送的訊息，但並不會從服務到用戶端的傳回訊息。 在這方面，`Message`引發不理的機制。

若要建立的慣用的方法`Message`物件是使用[ `Message.Obtain` ](https://developer.xamarin.com/api/type/Android.OS.Message/#Public%20Methods) factory 方法。 這個方法會提取`Message`維護由 Android 全域集區中的物件。 `Message.Obtain` 也有一些多載的方法，可讓`Message`nelze s položkami 服務所需的參數與值的物件。  一次`Message`是具現化，它分派至服務藉由呼叫`Messenger.Send`。 此程式碼片段是一個範例建立和分派`Message`到服務處理序：

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

有數種不同形式的`Message.Obtain`方法。 先前的範例使用[ `Message.Obtain(Handler h, Int32 what)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/)。 因為這是非同步要求，以跨處理序服務;會有任何服務回應中，因此`Handler`設為`null`。 第二個參數， `Int32 what`，將會儲存在`.What`屬性`Message`物件。 `.What`屬性可由服務處理序中的程式碼來叫用服務上的方法。

`Message`類別也會公開兩個額外的屬性可能需要的收件者的使用：`Arg1`和`Arg2`。 這兩個屬性都可能有一些特殊同意值有意義的用戶端與服務之間的整數值。 例如，`Arg1`可能會保存客戶識別碼和`Arg2`可能含有該客戶的訂單編號。 [ `Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/System.Int32/System.Int32/)可用來設定兩個屬性時`Message`建立。 填入這兩個值的另一個方法是設定`.Arg`並`.Arg2`屬性直接在`Message`物件之後建立。

### <a name="passing-additional-values-to-the-service"></a>其他的值傳遞至服務

您可傳遞至服務的更複雜的資料，使用`Bundle`。 在此情況下，額外的值可以置於`Bundle`，然後再傳送連同`Message`藉由設定[`.Data`屬性](https://developer.xamarin.com/api/property/Android.OS.Message.Data/)之前傳送的屬性。

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> 一般情況下，`Message`不應該超過 1 MB 的承載。 大小限制而有所不同，根據的 Android 版本，並在專屬的任何變更廠商可能會對其實作的 Android 開放來源專案 (AOSP)，隨附於裝置。

## <a name="returning-values-from-the-service"></a>從服務傳回值

到目前為止已討論過的訊息傳送架構是單向的用戶端將訊息傳送至服務。 如果需要將值傳回給用戶端服務再到目前為止已討論過的所有內容會反轉。 服務必須建立`Message`封裝任何傳回值，以及分派`Message`透過`Messenger`給用戶端。 不過，服務不會建立它自己`Messenger`; 相反地，它會依賴的具現化用戶端和套件`Messenger`初始要求的一部分。 此服務會`Send`使用此用戶端提供的訊息`Messenger`。  

雙向通訊的事件的順序如下：

1. 用戶端繫結至服務。 當服務和用戶端連線時，`IServiceConnection`保留的用戶端便會參考`Messenger`物件，用來傳輸`Message`s 至服務。 為了避免混淆，這將會被稱為_服務 Messenger_。
2. 用戶端會具現化`Handler`(稱為_用戶端處理常式_) 並使用它來初始化它自己`Messenger`(_用戶端 Messenger_)。 請注意，服務訊息] 和 [用戶端 Messenger 兩個不同的物件處理兩個不同的方向的流量。 服務 Messenger 會處理用戶端的訊息至服務，而用戶端 Messenger 將處理從服務到用戶端的訊息。
3. 用戶端會建立`Message`物件，以及設定`ReplyTo`與用戶端 Messenger 搭配運作的屬性。 訊息然後傳送至使用服務 Messenger 服務。
4. 服務會從用戶端，接收訊息，並執行要求的工作。
5. 當它是傳送回應給用戶端的服務時，它會使用`Message.Obtain`來建立新的`Message`物件。
6. 用戶端傳送此訊息，服務會擷取從用戶端 Messenger`.ReplyTo`屬性，用戶端的訊息，並使用它來`.Send``Message`傳回給用戶端。
7. 當用戶端收到回應時，它有自己`Handler`，將會處理`Message`藉由檢查`.What`屬性 (如有必要，擷取任何所包含的參數和`Message`)。

此範例程式碼示範如何在用戶端會具現化`Message`和封裝`Messenger`服務應該使用它的回應：

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

服務必須進行一些變更以它自己`Handler`擷取`Messenger`並用來傳送回覆至用戶端。 此程式碼片段是如何的範例服務的`Handler`會建立`Message`並將它傳送回用戶端：  

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

請注意，在上述程式碼範例`Messenger`由用戶端的執行個體*不*相同服務所收到的物件。 這些是兩個不同`Messenger`代表通訊通道的兩個不同處理序中執行的物件。

## <a name="securing-the-service-with-android-permissions"></a>保護 Android 權限的服務

在 Android 裝置上執行的所有應用程式存取的通用的處理序中執行的服務。 在某些情況下，此開放性和可用性不適當，而必須防範未經授權的用戶端存取服務。 限制存取遠端服務的一個方法是使用 Android 權限。

可以藉由識別權限`Permission`的屬性`ServiceAttribute`裝飾`Service`子類別。 這將名稱繫結至服務時，必須授與用戶端的權限。 如果用戶端並沒有適當的權限，則 Android 將會擲回`Java.Lang.SecurityException`用戶端嘗試繫結至服務。

有 Android 提供的四個不同的權限層級：

* **正常**&ndash;這是預設的權限層級。 它用來識別低風險的權限，可以自動授與 Android 所要求的用戶端。 使用者沒有明確授與這些權限，但權限可檢視中的應用程式設定。
* **簽章**&ndash;這是一種特殊的類別會自動授與 android 會使用相同的憑證所有簽署的應用程式的權限。 此權限可讓它輕易地針對應用程式開發人員共用元件或唆常數核准使用者其應用程式之間的資料。
* **signatureOrSystem** &ndash;這是非常類似於**簽章**上面所述的權限。 除了自動授與給相同的憑證所簽署的應用程式，此權限也會授與已簽署的應用程式用來簽署應用程式的相同憑證安裝在 Android 的系統映像。 此權限通常只是 Android ROM 開發人員，讓其使用協力廠商應用程式的應用程式。 它不是常用的為了散播一般發佈的應用程式。
* **危險**&ndash;危險的使用權限是指可能會造成使用者的問題。 基於這個理由，**危險**使用者必須明確地核准的權限。

因為`signature`並`normal`權限自動授與在安裝階段 Android 而言十分重要 APK 裝載服務會安裝**之前**包含用戶端的 APK。 如果第一次安裝用戶端，Android 不會授與權限。 在此情況下，必須解除安裝用戶端 APK 安裝 APK，此服務，然後重新安裝用戶端的 APK。

有兩個常見的方式，以保護 Android 權限的服務：

1.  **實作簽章的層級安全性**&ndash;簽章的層級安全性表示權限會自動授與這些會以用來簽署 APK 保留服務的相同金鑰簽署的應用程式。 這是簡單的方式，讓開發人員保護其服務，同時保有這些可從自己的應用程式存取。 簽章的層級權限的宣告方式設定`Permission`的屬性`ServiceAttribute`到`signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2.  **建立自訂的權限**&ndash;就可以建立自訂的權限，服務開發人員的服務。 這是最適合開發人員要與其他開發人員的應用程式共用其服務。 自訂權限需要多一點工夫來實作，而且涵蓋下方。

建立自訂的簡化的範例`normal`權限，將下一節所述。 如需有關 Android 權限的詳細資訊，請參閱 Google 的文件[最佳作法與安全性](https://developer.android.com/training/articles/security-tips.html)。 如需有關 Android 權限的詳細資訊，請參閱[權限 區段](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)Android 權限的詳細資訊的應用程式資訊清單的 Android 文件。

> [!NOTE]
> 一般情況下， [Google 不建議使用自訂權限](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions)，它們可以證明造成使用者混淆。

### <a name="creating-a-custom-permission"></a>建立自訂的權限

若要使用的自訂權限，它是服務所宣告的用戶端明確地要求該權限時。

若要建立 APK，此服務中的權限`permission`項目加入至`manifest`中的項目**AndroidManifest.xml**。 必須要有此權限`name`， `protectionLevel`，和`label`屬性集。 `name`屬性必須設為字串，可唯一識別的權限。 名稱會顯示在**應用程式資訊**檢視**Android 設定**（如在下一節中所示）。

`protectionLevel`屬性必須設為其中一個上面所述的四個字串值。  `label`和`description`必須參考字串資源，並用來提供給使用者的使用者易記的名稱和描述。

此程式碼片段會宣告自訂的範例`permission`屬性中**AndroidManifest.xml**包含服務之 apk:

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

然後， **AndroidManifest.xml**用戶端的 APK 必須明確地要求這個新權限。 這是藉由新增`users-permission`屬性設定為**AndroidManifest.xml**:

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

### <a name="view-the-permissions-granted-to-an-app"></a>檢視應用程式授與的權限

若要檢視應用程式已被授與的權限，請開啟 Android 設定 應用程式中，並選取**應用程式**。 尋找並選取清單中的應用程式。 從**應用程式資訊**畫面上，點選**權限**這會顯示的檢視可顯示所有應用程式授與的權限：

[![顯示如何尋找應用程式授與的權限的 Android 裝置的螢幕擷取畫面](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>總結

本指南是有關如何執行 Android 的服務遠端處理序中的進階的討論。 在本機和遠端服務之間的差異的說明，以及遠端服務為何很有幫助穩定性和效能的 Android 應用程式的一些原因。 說明如何實作遠端服務和用戶端可以與服務通訊的方式之後, 快速入門出在提供一種方式限制只有已獲授權的用戶端從服務存取。


## <a name="related-links"></a>相關連結

- [處理常式](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [訊息](https://developer.xamarin.com/api/type/Android.OS.Message/)
- [Messenger](https://developer.xamarin.com/api/type/Android.OS.Messenger/)
- [ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)
- [匯出屬性](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [隔離的程序與自訂應用程式類別的服務無法正確解析多載](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [處理序和執行緒](https://developer.android.com/guide/components/processes-and-threads.html)
- [Android 資訊清單的權限](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [安全性秘訣](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/MessengerServiceDemo/)

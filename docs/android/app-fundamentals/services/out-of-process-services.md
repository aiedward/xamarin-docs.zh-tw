---
title: 在遠端進程中執行 Android 服務
description: 一般而言, Android 應用程式中的所有元件都會在相同的進程中執行。 Android 服務在此是值得注意的例外狀況, 因為它們可以設定為在自己的進程中執行, 並與其他應用程式共用, 包括來自其他 Android 開發人員的工作。 本指南將討論如何使用 Xamarin 建立和使用 Android 遠端服務。
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 2794a1d23cd7c1eab9cf4e94eaa805ad2b8bca61
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119124"
---
# <a name="running-android-services-in-remote-processes"></a>在遠端進程中執行 Android 服務

_一般而言, Android 應用程式中的所有元件都會在相同的進程中執行。Android 服務在此是值得注意的例外狀況, 因為它們可以設定為在自己的進程中執行, 並與其他應用程式共用, 包括來自其他 Android 開發人員的工作。本指南將討論如何使用 Xamarin 建立和使用 Android 遠端服務。_

## <a name="out-of-process-services-overview"></a>進程外服務總覽

當應用程式啟動時, Android 會建立用來執行應用程式的進程。 通常, 應用程式會在此一進程中執行的所有元件。 Android 服務在此是值得注意的例外狀況, 因為它們可以設定為在自己的進程中執行, 並與其他應用程式共用, 包括來自其他 Android 開發人員的工作。 這些類型的服務稱為_遠端服務_或_跨進程服務_。 這些服務的程式碼將包含在與主應用程式相同的 APK 中;不過, 啟動服務時, Android 只會針對該服務建立新的處理常式。 相反地, 在與其余應用程式相同的進程中執行的服務有時稱為「_本機服務_」。

一般而言, 應用程式不需要執行遠端服務。 在許多情況下, 本機服務對應用程式的需求而言就已足夠 (而且是理想的)。 跨進程有自己的記憶體空間, 必須由 Android 管理。 雖然這會造成整體應用程式的額外負荷, 但在某些情況下, 在自己的進程中執行服務可能會有好處:

1. **共用功能**&ndash;有些應用程式開發人員可能會有多個應用程式和功能, 在所有應用程式之間共用。 在 Android 服務中封裝該功能以在自己的進程中執行, 可以簡化應用程式維護。 您也可以在自己的獨立 APK 中封裝服務, 並將其與應用程式的其餘部分分開部署。
2. **改善使用者體驗**&ndash;跨進程服務有兩種可能的方式可以改善應用程式的使用者體驗。 第一種方式是處理記憶體管理。 當垃圾收集 (GC) 迴圈發生時, Android 會暫停進程中的所有活動, 直到 GC 完成為止。 使用者可能會將此暫停視為「斷斷續續」或「jank」。 當服務在它自己的進程中執行時, 它就是暫停的服務程式, 而不是應用程式進程。 當應用程式進程 (也就是使用者介面) 未暫停時, 這段暫停時間對使用者來說會比較不明顯。

    其次, 如果進程的記憶體需求變得太大, Android 可能會終止該進程以釋放裝置的資源。 如果服務的記憶體使用量很大, 而且在與 UI 相同的進程中執行, 則當 Android 強制回收這些資源時, UI 將會關閉, 強制使用者啟動應用程式。 不過, 如果在自己的進程中執行的服務由 Android 關閉, 則 UI 進程會受到影響。 UI 可以系結 (並重新啟動) 服務, 對使用者而言是透明的, 並繼續正常運作。

3. **改善應用程式效能**&ndash; UI 進程可能會終止或關閉, 獨立于服務進程之外。 藉由將冗長的啟動工作移至跨進程服務, 可能會改善 UI 的啟動時間 (假設服務進程在啟動 UI 的時間之間保持運作)。

在許多方面, 系結至在另一個處理常式中執行的服務, 與系結[至本機服務](~/android/app-fundamentals/services/creating-a-service/bound-services.md)相同。 用戶端會`BindService`叫用來系結 (並視需要啟動) 服務。 系統`Android.OS.IServiceConnection`會建立一個物件來管理用戶端與服務之間的連接。 如果用戶端成功系結至服務, 則 Android 會透過傳回物件`IServiceConnection` , 其可用於在服務上叫用方法。 接著, 用戶端會使用這個物件與服務互動。 若要進行檢查, 以下是系結至服務的步驟:

- **建立意圖**&ndash;明確的意圖必須用來系結至服務。
- **執行物件, 並`IServiceConnection`** 具現`IServiceConnection`化&ndash;物件, 做為用戶端與服務之間的媒介。  負責監視用戶端與伺服器之間的連接。
- **`BindService`** 叫用&ndash;呼叫的`BindService`方法會將先前步驟中建立的意圖和服務連線分派至 Android, 這將負責啟動服務, 並建立之間的通訊。用戶端和服務。

跨進程界限的需求會帶來額外的複雜性: 通訊是單向 (用戶端到伺服器), 而用戶端無法直接叫用服務類別上的方法。 回想一下, 當服務與用戶端執行相同的程式時, Android 會提供`IBinder`可能允許雙向通訊的物件。 當服務在自己的進程中執行時, 就不會發生這種情況。 用戶端會透過`Android.OS.Messenger`類別的協助與遠端服務進行通訊。

當用戶端要求與遠端服務系結時, Android 會`Service.OnBind`叫用生命週期方法, 這會傳回所封裝`Messenger`的內部`IBinder`物件。 是由 Android SDK 所提供的特殊`IBinder`實作為精簡型包裝函式。 `Messenger` `Messenger`會負責處理兩個不同程式之間的通訊。 開發人員會不在乎序列化訊息、跨進程界限封送處理訊息, 然後在用戶端上還原序列化的詳細資料。 這項工作是由`Messenger`物件處理。 此圖顯示當用戶端起始系結至進程外服務時所涉及的用戶端 Android 元件:

此![圖顯示用戶端系結至服務的步驟和元件](out-of-process-services-images/ipc-01.png "此圖顯示用戶端系結至服務的步驟和元件。")

遠端`Service`進程中的類別將會經歷相同的生命週期回呼, 在本機進程中系結的服務會通過, 而涉及的許多 api 也會相同。 `Service.OnCreate`是用來初始化`Handler` , 並將其插入至`Messenger`物件。 同樣地`OnBind` , 會覆寫, 但`IBinder`不會傳回物件, 服務將會`Messenger`傳回。  此圖說明當用戶端系結到服務時, 會發生什麼情況:

![此圖顯示當服務由遠端用戶端系結時, 所經歷的步驟和元件](out-of-process-services-images/ipc-02.png "此圖顯示服務在遠端用戶端系結時所經歷的步驟和元件。")

當服務收到時, 會在的`Android.OS.Handler`實例中處理它。 `Message` 服務將會執行自己`Handler`的子類別, 必須覆`HandleMessage`寫方法。 這個方法是由所`Messenger` `Message`叫用, 並會接收做為參數。 `Handler` 會`Message`檢查中繼資料, 並使用該資訊來叫用服務上的方法。

當用戶端建立`Message`物件並`Messenger.Send`使用方法將它分派給服務時, 就會發生單向通訊。 `Messenger.Send`會序列化`Message` , 並將序列化的資料交給 Android, 這會將訊息路由傳送至整個進程界限和服務。  服務`Messenger`所裝載的會從傳入的資料`Message`建立物件。 這`Message`會放入佇列中, 其中的訊息會一次提交`Handler`至。 會檢查包含`Message`在中的中繼資料, 並在上叫用適當`Service`的方法。 `Handler` 下圖說明各種作用中的概念:

![顯示如何在進程之間傳遞訊息的圖表](out-of-process-services-images/ipc-03.png "顯示如何在進程之間傳遞訊息的圖表。")

本指南將討論如何執行跨進程服務的詳細資料。 它會討論如何執行一項服務, 其目的是要在自己的進程中執行, 以及用戶端如何使用`Messenger`架構與該服務進行通訊。 它也會簡短討論雙向通訊: 用戶端將訊息傳送至服務, 以及將訊息傳送回用戶端的服務。 因為服務可以在不同的應用程式之間共用, 所以本指南也會討論使用 Android 許可權來限制用戶端存取服務的一項技巧。

> [!IMPORTANT]
> [Bugzilla 51940/GitHub 1950-具有隔離進程和自訂應用程式類別的服務無法正確解析](https://github.com/xamarin/xamarin-android/issues/1950)多載, 報告當設定為`IsolatedProcess` `true`時, 不會正確啟動 Xamarin Android 服務。 本指南提供參考。 Xamarin Android 應用程式仍應能夠與以 JAVA 撰寫的跨進程服務進行通訊。

## <a name="requirements"></a>需求

本指南假設您已熟悉如何建立服務。

雖然可以使用隱含意圖搭配以舊版 Android Api 為目標的應用程式, 但本指南將僅著重于使用明確意圖。 以 Android 5.0 (API 層級 21) 或更高版本為目標的應用程式必須使用明確的意圖來與服務系結;這是將在本指南中示範的技術。

## <a name="create-a-service-that-runs-in-a-separate-process"></a>建立在個別進程中執行的服務

如上所述, 服務會在自己的進程中執行, 這表示牽涉到一些不同的 Api。 簡單的介紹, 以下是系結和使用遠端服務的步驟:  

- **`Service`**  建立子`Service`類別的型別, 並為系結的服務實作為生命週期方法。 &ndash; 您也必須設定中繼資料來通知 Android, 服務會在自己的進程中執行。
- 執行負責分析用戶端要求、解壓縮從用戶端傳遞的任何參數, 以及在服務上叫用適當的方法。 **`Handler`** &ndash; `Handler`
- `Handler` `Service` `Messenger` **`Messenger`** 如上面所述具現化,每個都必須維護一個類別的實例,以將用戶端要求路由傳送至在上一個步驟中建立的。&ndash;

要在自己的進程中執行的服務, 基本上是系結服務。 服務類別會擴充基類`Service` , 並`ServiceAttribute`使用包含 android 需要在 android 資訊清單中配套之中繼資料的來裝飾。 一開始, 的下列屬性`ServiceAttribute`對跨進程服務很重要:

1. `Exported`這個屬性必須設定為`true` , 才能讓其他應用程式與服務互動。 &ndash; 此屬性的預設值為 `false`。
2. `Process`&ndash;必須設定此屬性。 它是用來指定服務將在其中執行的進程名稱。
3. `IsolatedProcess`&ndash;這個屬性會啟用額外的安全性, 並告知 Android 在隔離的沙箱中執行服務, 而且具有最少的許可權可與系統的其餘部分互動。 請參閱[Bugzilla 51940-具有隔離進程和自訂應用程式類別的服務無法正確解析](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)多載。
4. `Permission`&ndash;您可以藉由指定用戶端必須要求 (並授與) 的許可權, 來控制用戶端對服務的存取。

若要執行服務自己的進程, `Process` `ServiceAttribute`必須將上的屬性設定為服務的名稱。 若要與外部應用程式互動`Exported` , 屬性應該設定為`true`。 如果`Exported` 為`false`, 則只有相同 APK 中的用戶端 (也就是相同的應用程式), 而且在相同的進程中執行, 才能夠與服務互動。

服務將在哪種進程中執行, 取決於`Process`屬性的值。 Android 會識別三種不同類型的進程:

- **私**用程式&ndash;私用程式是僅供啟動它的應用程式使用的進程。 若要將進程識別為私用, 其名稱必須以 **:** (分號) 開頭。 先前的程式碼片段中所描述的服務和螢幕擷取畫面是私用程式。 下列程式碼片段是的範例`ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

- **全域**程式&ndash;在全域程式中執行的服務, 可供在裝置上執行的所有應用程式存取。 全域處理常式必須是以小寫字元開頭的完整類別名稱。
    (除非採取步驟來保護服務, 否則其他應用程式可能會系結並與其互動。 本指南稍後將討論如何保護服務免于未經授權的使用。)

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

- **隔離的進程**&ndash;隔離的進程是在自己的沙箱中執行的進程, 與系統的其餘部分隔離, 而且沒有自己的特殊許可權。 若要在隔離的進程中執行服務, `IsolatedProcess`的屬性`ServiceAttribute`會設定為`true` , 如下列程式碼片段所示:
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> 請參閱[Bugzilla 51940-具有隔離進程和自訂應用程式類別的服務無法正確解析](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)多載

隔離服務是一種簡單的方式, 可保護應用程式和裝置不受信任的程式碼。 例如, 應用程式可能會從網站下載並執行腳本。 在此情況下, 在隔離的進程中執行此作業, 可針對不受信任的程式碼, 對 Android 裝置產生額外的安全性層級。

> [!IMPORTANT]
> 一旦匯出服務之後, 服務的名稱就不應該變更。 變更服務的名稱可能會中斷其他使用服務的應用程式。

為了查看`Process`屬性具有的效果, 下列螢幕擷取畫面顯示在自己的私用程式中執行的服務:

![顯示在私用程式中執行之服務的螢幕擷取畫面](out-of-process-services-images/ipc-04.png "顯示在私用程式中執行之服務的螢幕擷取畫面。")

下一個螢幕擷取畫面`Process="com.xamarin.xample.messengerservice.timestampservice_process"`顯示全域程式中執行的和服務:

![在全域進程中執行之服務的螢幕擷取畫面](out-of-process-services-images/ipc-05.png "在全域進程中執行之服務的螢幕擷取畫面。")

一旦設定之後, 服務就必須`Handler`執行。 `ServiceAttribute`

### <a name="implementing-a-handler"></a>執行處理常式

若要處理用戶端要求, 服務必須執行`Handler` , 並覆`HandleMessage`寫 methodThis, 方法`Message`是使用從用戶端封裝方法呼叫的實例, 並將該呼叫轉譯為某個動作或工作服務將會執行。 物件會公開名`What`為的屬性, 這是一個整數值, 其意義是在用戶端與服務之間共用, 而且與服務要針對用戶端執行的某些工作相關聯。 `Message`

範例應用程式中的下列程式碼片段會顯示的`HandleMessage`其中一個範例。 在此範例中, 用戶端可以要求服務的動作有兩種:

- 第一個動作是_Hello, World_訊息, 用戶端已將簡單訊息傳送給服務。
- 第二個動作會在服務上叫用方法並抓取字串, 在此案例中, 字串是傳回服務啟動時間和其執行時間長度的訊息:

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
                // Call methods on the service to retrieve a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

您也可以在中`Message`封裝服務的參數。 本指南稍後將討論此內容。 下一個要考慮的主題是建立`Messenger`物件來處理傳入`Message`的。

### <a name="instantiating-the-messenger"></a>具現化 Messenger

如先前所討論, 還原`Message`序列化物件`Handler.HandleMessage`和叫用`Messenger`是物件的責任。 類別也會`IBinder`提供物件, 讓用戶端用來傳送訊息至服務。 `Messenger`  

當服務啟動時, 它會將具`Messenger`現化, `Handler`並插入。 執行此初始化的最佳位置是在服務的`OnCreate`方法上。 此程式碼片段是服務的其中一個範例, 它會初始化`Handler`它`Messenger`自己的和:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

此時, 最後一個步驟是用於`Service`覆寫。 `OnBind`

### <a name="implementing-serviceonbind"></a>執行服務。 OnBind

所有系結服務 (不論它們是否在自己的進程中執行) 都必須`OnBind`執行方法。 這個方法的傳回值是用戶端可用來與服務互動的一些物件。 該物件確切取決於服務是本機服務還是遠端服務。 雖然本機服務會傳回自訂`IBinder`的執行, 但遠端服務會`IBinder`傳回已封裝的, 但`Messenger`在上一節中建立的:

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

完成這三個步驟之後, 就可以將遠端服務視為已完成。

## <a name="consuming-the-service"></a>使用服務

所有用戶端都必須執行一些程式碼, 才能系結和使用遠端服務。 從概念上來說, 從用戶端的觀點來看, 系結至本機服務或遠端服務之間的差異非常少。 用戶端`BindService`會叫用方法, 傳遞明確意圖來識別服務`IServiceConnection` , 以及協助管理用戶端與服務之間的連接。

此程式碼片段是如何建立系結至遠端服務之**明確意圖**的範例。 此意圖必須識別包含服務的封裝和服務的名稱。 設定這項資訊的其中一種方式是`Android.Content.ComponentName`使用物件, 並將其提供給意圖。 此程式碼片段是其中一個範例:  

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

系結服務時, `IServiceConnection.OnServiceConnected`會叫用方法, 並將`IBinder`提供給用戶端。 不過, 用戶端不會直接使用`IBinder`。 相反地, 它會將`Messenger`物件`IBinder`具現化。 這是客戶`Messenger`端將用來與遠端服務互動的。

以下是非常基本`IServiceConnection`的執行範例, 示範用戶端如何處理與服務的連線和中斷連接。 請注意, `OnServiceConnected`方法會接收`IBinder`和, `Messenger`而用戶端會`IBinder`從建立:

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

一旦建立服務連線和意圖之後, 用戶端就可以呼叫`BindService`並起始系結程式:

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

在用戶端成功系結至服務且`Messenger`可供使用之後, 用戶端可能會傳送`Messages`至服務。

## <a name="sending-messages-to-the-service"></a>將訊息傳送至服務

一旦用戶端連線並擁有`Messenger`物件, 就可以透過分派`Message`物件`Messenger`, 來與服務通訊。 這種通訊是單向的, 用戶端會傳送訊息, 但從服務到用戶端則不會傳回訊息。 `Message`就這一點而言, 是一種火災和忘的機制。

建立`Message`物件的慣用方法是[`Message.Obtain`](xref:Android.OS.Message)使用 factory 方法。 這個方法會從由`Message` Android 維護的全域集區提取物件。 `Message.Obtain`也有一些多載的方法, `Message`可讓物件使用服務所需的值和參數來初始化。  一旦具`Messenger.Send`現化, 它就會藉由呼叫來分派給服務。 `Message` 此程式碼片段是建立和分派`Message`至服務程式的其中一個範例:

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

`Message.Obtain`方法有數種不同的形式。 上一個範例會使用[`Message.Obtain(Handler h, Int32 what)`](xref:Android.OS.Message.Obtain)。 因為這是跨進程服務的非同步要求,服務不會有任何回應, 因此`Handler`會設定為。 `null` 第二個參數`Int32 what`會儲存`.What`在`Message`物件的屬性中。 `.What`屬性是由服務進程中的程式碼用來叫用服務上的方法。

類別也會公開兩個可能用於收件者的其他屬性: `Arg1`和`Arg2`。 `Message` 這兩個屬性都是整數值, 可能會有一些特殊的同意值, 其在用戶端與服務之間有意義。 例如, `Arg1`可能會保留客戶識別碼, 而且`Arg2`可能會保留該客戶的訂單號碼。 建立[`Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)`](xref:Android.OS.Message.Obtain) 時,可以使用來設定`Message`這兩個屬性。 填入這兩個值的另一種方式是`.Arg`在`.Arg2` `Message`物件建立之後, 直接在物件上設定和屬性。

### <a name="passing-additional-values-to-the-service"></a>將其他值傳遞至服務

您可以使用`Bundle`, 將更複雜的資料傳遞給服務。 在此情況下, 您可以在傳送之前設定`Bundle` [ `.Data`屬性](xref:Android.OS.Message.Data)屬性, 將額外`Message`的值放在中, 並與一起傳送。

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> 一般而言, `Message`不應具有大於1mb 的承載。 大小限制可能會根據 Android 版本和廠商可能對其在裝置上執行的 Android 開放原始碼專案 (AOSP) 所做的任何專屬變更而有所不同。

## <a name="returning-values-from-the-service"></a>從服務傳回值

此點已討論過的訊息架構是單向的, 用戶端會將訊息傳送給服務。 如果服務必須將值傳回給用戶端, 則已對此點進行討論的所有內容都是相反的。 服務必須建立`Message`、封裝任何傳回值, 並透過將透過`Message`分派`Messenger`至用戶端。 不過, 服務不會建立自己`Messenger`的, 而是依賴用戶端具現化並封裝 a `Messenger`做為初始要求的一部分。 服務將會`Send`使用此用戶端提供`Messenger`的訊息。  

雙向通訊的事件順序如下:

1. 用戶端會系結至服務。 當服務和用戶端連接時, `IServiceConnection`由用戶端維護的會有`Messenger`物件的參考, 而該物件是用來將傳送`Message`至服務。 為了避免混淆, 這會稱為「_服務 Messenger_」。
2. 用戶端會`Handler`具現化 (稱為_用戶端處理常式_), 並使用它來`Messenger`初始化它自己的 (_用戶端 Messenger_)。 請注意, 服務 Messenger 和用戶端 Messenger 是兩個不同的物件, 會以兩個不同的方向來處理流量。 服務 Messenger 會處理從用戶端到服務的訊息, 而用戶端 Messenger 會處理從服務到用戶端的訊息。
3. 用戶端會建立`Message`物件, 並使用客戶`ReplyTo`端 Messenger 來設定屬性。 接著, 訊息會使用服務 Messenger 傳送至服務。
4. 服務會接收來自用戶端的訊息, 並執行要求的工作。
5. 當服務將回應傳送至用戶端時, 它會使用`Message.Obtain`來建立新`Message`的物件。
6. 為了將此訊息傳送給用戶端, 服務會從用戶端訊息的`.ReplyTo`屬性將用戶端 Messenger 解壓縮, 並`.Send` `Message`將其用於回到用戶端。
7. 當用戶端收到回應時, 它`Handler`會有自己的, 它會藉`Message`由檢查`.What`屬性來處理 (如有必要, 請將包含的`Message`任何參數解壓縮)。

這個範例程式碼會示範用戶端如何具`Message`現化服務`Messenger`應用於其回應的和封裝 a:

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

服務必須對自己`Handler`進行一些變更, 以`Messenger`解壓縮, 並使用它將回復傳送至用戶端。 此程式碼片段是服務的`Handler` `Message`建立方式, 並將其傳送回用戶端的範例:  

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

請注意, 在上述的程式碼範例`Messenger`中, 用戶端所建立的實例*不*是服務所接收的相同物件。 這些是兩個`Messenger`不同的物件, 會在代表通道的兩個個別進程中執行。

## <a name="securing-the-service-with-android-permissions"></a>使用 Android 許可權保護服務

在全域程式中執行的服務, 可供在該 Android 裝置上執行的所有應用程式存取。 在某些情況下, 這是不需要的開放性和可用性, 而且必須保護服務免于未經授權的用戶端存取。 限制遠端服務存取的一種方式是使用 Android 許可權。

許可權可以由`Permission`裝飾`Service`子類別的`ServiceAttribute`的屬性來識別。 這會命名在系結至服務時, 必須授與用戶端的許可權。 如果用戶端沒有適當的許可權, 則`Java.Lang.SecurityException`當用戶端嘗試系結至服務時, Android 將會擲回。

Android 提供四種不同的許可權等級:

- **正常**&ndash;這是預設的許可權層級。 它可用來識別可由 Android 自動授與要求它的用戶端的低風險許可權。 使用者不需要明確授與這些許可權, 但可以在應用程式設定中查看許可權。
- 簽章&ndash;這是特殊的許可權類別, 會由 Android 自動授與所有以相同憑證簽署的應用程式。 此許可權的設計可讓應用程式開發人員輕鬆地在其應用程式之間共用元件或資料, 而不需要中斷使用者進行持續核准。
- **signatureOrSystem**這與上面所述的簽章許可權非常類似。 &ndash; 除了自動授與由相同憑證簽署的應用程式之外, 此許可權也會授與給已簽署相同憑證的應用程式, 該憑證是用來簽署與 Android 系統映射一起安裝的應用程式。 此許可權通常僅供 Android ROM 開發人員使用, 以允許其應用程式使用協力廠商應用程式。 這種應用程式通常不會使用這種方式, 因為公用的一般散發是大型的。
- **危險**&ndash;危險的許可權是可能會對使用者造成問題的許可權。 基於這個理由, 使用者必須明確核准**危險**的許可權。

因為`signature`和`normal`許可權會在 Android 安裝的時間自動授與, 所以在包含用戶端的 APK 之前, 必須**先**安裝 APK 裝載服務的關鍵。 如果先安裝用戶端, 則 Android 不會授與許可權。 在此情況下, 必須將用戶端 APK 卸載、安裝服務 APK, 然後重新安裝用戶端 APK。

有兩個常見的方法可以使用 Android 許可權保護服務:

1. **執行簽章層級安全性**&ndash;簽章層級安全性表示系統會自動授與許可權給那些使用相同金鑰簽署的應用程式, 而該索引鍵是用來簽署保留服務的 APK。 這是一種簡單的方法, 讓開發人員保護其服務, 同時讓他們可以從自己的應用程式中存取。 簽章層級許可權的宣告方式`Permission`是將的`ServiceAttribute`屬性`signature`設定為:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2. **建立自訂許可權**&ndash;服務的開發人員可以建立服務的自訂許可權。 當開發人員想要與其他開發人員的應用程式共用其服務時, 這是最適合的做法。 自訂許可權需要更多的執行工作, 並將于下面討論。

下一節將說明建立自`normal`定義許可權的簡單範例。 如需 Android 許可權的詳細資訊, 請參閱 Google 的檔, 以取得[& 安全性的最佳做法](https://developer.android.com/training/articles/security-tips.html)。 如需 Android 許可權的詳細資訊, 請參閱應用程式資訊清單之 Android 檔的[許可權一節](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms), 以取得 android 許可權的詳細資訊。

> [!NOTE]
> 一般來說, [Google 不鼓勵使用自訂許可權](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions), 因為他們可能會對使用者造成混淆。

### <a name="creating-a-custom-permission"></a>建立自訂許可權

若要使用自訂許可權, 則在用戶端明確要求該許可權時, 服務會宣告它。

若要在服務 APK 中建立許可權, `permission`元素會加入至**androidmanifest.xml**中的`manifest`元素。 這個許可權必須`name`設定、 `protectionLevel`和`label`屬性。 `name`屬性必須設定為可唯一識別許可權的字串。 該名稱將會顯示在**Android 設定**的 [**應用程式資訊**] 視圖中 (如下一節所示)。

`protectionLevel`屬性必須設定為上述四個字串值的其中一個。  `label` 和`description`必須參考字串資源, 並用來提供使用者易記的名稱和描述給使用者。

此程式碼片段是在包含服務之 APK `permission`的**androidmanifest.xml**中宣告自訂屬性的範例:

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

然後, 用戶端 APK 的**androidmanifest.xml**必須明確要求這個新的許可權。 這是藉由將`users-permission`屬性新增至**androidmanifest.xml**來完成:

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

### <a name="view-the-permissions-granted-to-an-app"></a>查看授與應用程式的許可權

若要查看已授與應用程式的許可權, 請開啟 [Android 設定] 應用程式, 然後選取 [**應用**程式]。 尋找並選取清單中的應用程式。 在 [**應用程式資訊**] 畫面上, 按一下 [**許可權**], 這會顯示授與應用程式擁有權限的視圖:

[![Android 裝置的螢幕擷取畫面, 顯示如何尋找授與應用程式的許可權](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>總結

本指南是有關如何在遠端進程中執行 Android 服務的先進討論。 說明本機與遠端服務之間的差異, 並提供遠端服務有助於 Android 應用程式穩定性和效能的一些原因。 在說明如何執行遠端服務以及用戶端如何與服務通訊之後, 本指南會開始提供一種方式, 限制只有經過授權的用戶端才能存取服務。


## <a name="related-links"></a>相關連結

- [處理器](xref:Android.OS.Handler)
- [Message](xref:Android.OS.Message)
- [Messenger](xref:Android.OS.Messenger)
- [ServiceAttribute](xref:Android.App.ServiceAttribute)
- [匯出的屬性](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [具有隔離進程和自訂應用程式類別的服務無法正確解析多載](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [進程和執行緒](https://developer.android.com/guide/components/processes-and-threads.html)
- [Android 資訊清單-許可權](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [安全性秘訣](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-messengerservicedemo)

---
title: 在遠端進程中執行 Android 服務
description: 一般而言，Android 應用程式中的所有元件都會在相同的進程中執行。 Android 服務是很明顯的例外，因為它們可以設定為在自己的進程中執行，並與其他應用程式共用，包括來自其他 Android 開發人員的應用程式。 本指南將討論如何使用 Xamarin 建立和使用 Android 遠端服務。
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 14db376fbb40575cc3b11c5b23f0af6d32370503
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455325"
---
# <a name="running-android-services-in-remote-processes"></a>在遠端進程中執行 Android 服務

_一般而言，Android 應用程式中的所有元件都會在相同的進程中執行。Android 服務是很明顯的例外，因為它們可以設定為在自己的進程中執行，並與其他應用程式共用，包括來自其他 Android 開發人員的應用程式。本指南將討論如何使用 Xamarin 建立和使用 Android 遠端服務。_

## <a name="out-of-process-services-overview"></a>進程外服務總覽

當應用程式啟動時，Android 會建立要在其中執行應用程式的進程。 通常，應用程式會在此一進程中執行的所有元件。 Android 服務是很明顯的例外，因為它們可以設定為在自己的進程中執行，並與其他應用程式共用，包括來自其他 Android 開發人員的應用程式。 這些類型的服務稱為「 _遠端服務_ 」或「 _跨進程服務_」。 這些服務的程式碼將包含在與主應用程式相同的 APK 中;不過，當服務啟動時，Android 只會為該服務建立新的處理常式。 相反地，在與應用程式其餘部分相同的進程中執行的服務有時也稱為 _本機服務_。

一般情況下，應用程式不需要執行遠端服務。 在許多情況下，本機服務是足夠的 (和應用程式需求的理想) 。 跨進程有它自己的記憶體空間，必須由 Android 管理。 雖然這對整體應用程式帶來更多的額外負荷，但在某些情況下，在它自己的程式中執行服務可能會有好處：

1. **共用功能** &ndash; 有些應用程式開發人員可能會在所有應用程式之間共用多個應用程式和功能。 在 Android 服務中封裝該功能（在其本身的進程中執行）可以簡化應用程式維護。 您也可以將服務封裝在它自己的獨立 APK 中，並將其與應用程式的其餘部分分開部署。
2. **改善使用者體驗** &ndash; 跨進程服務有兩種可能的方式可以改善應用程式的使用者體驗。 第一種處理記憶體管理的方法。 當垃圾收集 (GC) 迴圈發生時，Android 會暫停進程中的所有活動，直到 GC 完成為止。 使用者可能會察覺到此暫停為「斷斷續續」或「jank」。 當服務在其本身的進程中執行時，它會是已暫停的服務進程，而不是應用程式進程。 當應用程式處理 (時，這個暫停將對使用者來說比較不明顯，因此使用者介面) 不會暫停。

    其次，如果進程的記憶體需求變得太大，Android 可能會終止該進程，以釋出裝置的資源。 如果服務的記憶體使用量很大，而且是在與 UI 相同的進程中執行，則當 Android 強制回收這些資源時，UI 將會關閉，強制使用者啟動應用程式。 但是，如果在其本身的進程中執行的服務是由 Android 關閉，則 UI 進程會保持不變。 UI 可以系結 (並重新啟動) 服務，對使用者而言是透明的，並繼續正常運作。

3. **改善應用程式效能** &ndash; UI 進程可能會終止，或與服務進程分開關閉。 藉由將冗長的啟動工作移至跨進程服務，可能會改善 UI 的啟動時間， (假設服務進程會在 UI 啟動的時間之間保持運作) 。

在許多方面，系結至另一個進程中執行的服務，與系結 [至本機服務](~/android/app-fundamentals/services/creating-a-service/bound-services.md)相同。 如果需要) 服務，則用戶端會叫用以系結 `BindService` (並啟動。 `Android.OS.IServiceConnection`將建立物件來管理用戶端與服務之間的連接。 如果用戶端成功系結至服務，則 Android 會透過 `IServiceConnection` 可用來叫用服務上方法的，傳回物件。 然後，用戶端會使用此物件與服務互動。 若要瞭解，以下是系結至服務的步驟：

- **建立意圖** &ndash; 明確的意圖必須用來系結至服務。
- **執行和具現化 `IServiceConnection` 物件** &ndash;`IServiceConnection`物件可做為用戶端與服務之間的媒介。  它負責監視用戶端與伺服器之間的連接。
- 叫用** `BindService` 方法** &ndash;呼叫 `BindService` 會將在先前步驟中建立的意圖和服務連接分派至 Android，這會負責啟動服務並建立用戶端與服務之間的通訊。

跨進程界限的需求會造成額外的複雜性：通訊為伺服器) 的單向 (用戶端，而用戶端無法直接在服務類別上叫用方法。 回想一下，當服務執行與用戶端相同的進程時，Android 會提供一個 `IBinder` 可能允許雙向通訊的物件。 這並不是服務在自己的進程中執行的情況。 用戶端會透過類別的協助與遠端服務進行通訊 `Android.OS.Messenger` 。

當用戶端要求與遠端服務系結時，Android 會叫用 `Service.OnBind` 生命週期方法，這會傳回 `IBinder` 由封裝的內建物件 `Messenger` 。 `Messenger`是由 Android SDK 提供的特殊實作為的精簡型包裝函式 `IBinder` 。 `Messenger`會負責處理兩個不同程式之間的通訊。 開發人員會不在乎序列化訊息、跨進程界限封送處理訊息，然後在用戶端上還原序列化的詳細資料。 這項工作是由 `Messenger` 物件處理。 下圖顯示用戶端起始系結至跨進程服務時所牽涉到的用戶端 Android 元件：

![顯示用戶端系結至服務之步驟和元件的圖表](out-of-process-services-images/ipc-01.png "顯示用戶端系結至服務之步驟和元件的圖表。")

`Service`遠端進程中的類別將會經歷本機進程中系結服務將會經歷的相同生命週期回呼，而許多相關的 api 都是相同的。 `Service.OnCreate` 用來初始化，並將其 `Handler` 插入 `Messenger` 物件中。 同樣地， `OnBind` 會覆寫，但不 `IBinder` 會傳回物件，服務會傳回 `Messenger` 。  下圖說明當用戶端系結至服務時，服務會發生什麼情況：

![此圖顯示服務在系結至遠端用戶端時所經歷的步驟和元件](out-of-process-services-images/ipc-02.png "此圖顯示服務在系結至遠端用戶端時，所經歷的步驟和元件。")

當 `Message` 服務收到時，會在的實例中處理它 `Android.OS.Handler` 。 服務將會執行它自己 `Handler` 的子類別，而這個子類別必須覆寫 `HandleMessage` 方法。 這個方法是由叫用 `Messenger` ，並接收 `Message` 做為參數。 `Handler`會檢查 `Message` 中繼資料，並使用該資訊來叫用服務上的方法。

當用戶端建立 `Message` 物件並使用方法將其分派至服務時，就會發生單向通訊 `Messenger.Send` 。 `Messenger.Send` 會將序列化的資料序列化， `Message` 並將該序列化的資料交給 Android，以將訊息路由傳送至整個進程界限和服務。  服務所裝載的 `Messenger` 會 `Message` 從傳入資料建立物件。 這 `Message` 會放置在佇列中，一次將訊息提交至 `Handler` 。 `Handler`會檢查中包含的中繼資料 `Message` ，並在上叫用適當的方法 `Service` 。 下圖說明這些各種不同的概念：

![顯示如何在進程之間傳遞訊息的圖表](out-of-process-services-images/ipc-03.png "顯示如何在進程之間傳遞訊息的圖表。")

本指南將討論如何執行跨進程服務的詳細資料。 它會討論如何執行要在自己的進程中執行的服務，以及用戶端如何使用架構與該服務進行通訊 `Messenger` 。 它也會簡短地討論雙向通訊：用戶端傳送訊息至服務，以及將訊息傳送回用戶端的服務。 因為服務可以在不同的應用程式之間共用，所以本指南也會討論使用 Android 許可權來限制用戶端存取服務的一種技術。

> [!IMPORTANT]
> [Bugzilla 51940/GitHub 1950-具有隔離處理常式和自訂應用程式類別的服務無法正確解析](https://github.com/xamarin/xamarin-android/issues/1950) 多載，請報告當設定為時，Xamarin. Android 服務將無法正常啟動 `IsolatedProcess` `true` 。 本指南提供參考。 Xamarin. Android 應用程式仍應能夠與以 JAVA 撰寫的跨進程服務進行通訊。

## <a name="requirements"></a>需求

本指南假設您已經熟悉如何建立服務。

雖然您可以對以舊版 Android Api 為目標的應用程式使用隱含意圖，但本指南將著重于明確意圖的使用方式。 以 Android 5.0 (API 層級 21) 或更高版本為目標的應用程式，必須使用明確的意圖來系結服務。這是將在本指南中示範的技巧。

## <a name="create-a-service-that-runs-in-a-separate-process"></a>建立在個別進程中執行的服務

如上所述，服務在其本身的進程中執行的事實表示涉及一些不同的 Api。 以下是與遠端服務系結和使用的步驟：  

- **建立子 `Service` 類別** &ndash; 將型別的子類別化 `Service` ，並針對系結服務執行生命週期方法。 您也必須設定中繼資料，以通知 Android 服務要在自己的進程中執行。
- **執行 `Handler` **&ndash; `Handler` 負責分析用戶端要求、將從用戶端傳遞的任何參數解壓縮，並在服務上叫用適當的方法。
- 具現** `Messenger` 化**如上所述 &ndash; ，每個都 `Service` 必須維持 `Messenger` 類別的實例，此實例會將用戶端要求路由至 `Handler` 在上一個步驟中建立的。

要在本身的進程中執行的服務，基本上是仍是系結的服務。 服務類別將擴充基類 `Service` ，並使用 `ServiceAttribute` 包含 Android 在 android 資訊清單中所需之中繼資料的裝飾。 首先，的下列屬性對 `ServiceAttribute` 跨進程服務而言很重要：

1. `Exported`&ndash;這個屬性必須設定為， `true` 才能讓其他應用程式與服務互動。 此屬性的預設值為 `false`。
2. `Process`&ndash;必須設定這個屬性。 它是用來指定服務將在其中執行之進程的名稱。
3. `IsolatedProcess`&ndash;這個屬性會啟用額外的安全性，告知 Android 以最基本的許可權在隔離的沙箱中執行服務，以與系統的其餘部分互動。 請參閱 [Bugzilla 51940-具有隔離處理常式和自訂應用程式類別的服務無法正確地解析](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)多載。
4. `Permission`您可以藉 &ndash; 由指定用戶端必須要求 (的許可權，並將其授與) ，來控制用戶端對服務的存取。

若要執行服務本身的進程， `Process` 必須將上的屬性 `ServiceAttribute` 設定為服務的名稱。 若要與外部應用程式互動， `Exported` 屬性應該設定為 `true` 。 如果 `Exported` 為 `false` ，則只有相同 APK 中的用戶端 (也就是相同的應用程式) ，而且在相同的進程中執行，將能夠與服務互動。

服務將在哪種處理常式中執行，取決於屬性的值 `Process` 。 Android 識別三種不同類型的進程：

- **私用程式** &ndash; 私用程式只適用于啟動它的應用程式。 若要將處理常式識別為私用，其名稱必須以 **：** (分號開頭) 。 先前程式碼片段中所描述的服務和螢幕擷取畫面是私用程式。 下列程式碼片段是的範例 `ServiceAttribute` ：

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

- **全域進程** &ndash; 在全域進程中執行的服務，可供裝置上執行的所有應用程式存取。 全域處理常式必須是以小寫字元開頭的完整類別名稱。
     (除非採取步驟來保護服務，否則其他應用程式可能會系結並與其互動。 本指南稍後將討論保護服務免于未經授權的使用。 ) 

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

- **隔離進程** &ndash; 獨立程式是在它自己的沙箱中執行的程式，與系統的其餘部分隔離，而且沒有專屬的特殊許可權。 若要在隔離的進程中執行服務，的 `IsolatedProcess` 屬性 `ServiceAttribute` 會設定為， `true` 如下列程式碼片段所示：
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> 請參閱[Bugzilla 51940-具有隔離處理常式和自訂應用程式類別的服務無法正確解析](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)多載

隔離的服務是保護應用程式和裝置免于受信任程式碼的簡單方法。 例如，應用程式可能會從網站下載並執行腳本。 在此情況下，在隔離的程式中執行此作業，可針對未受信任的程式碼危害 Android 裝置，提供額外的安全性層級。

> [!IMPORTANT]
> 一旦匯出服務之後，服務的名稱就不應變更。 變更服務的名稱可能會中斷使用服務的其他應用程式。

為了查看屬性所擁有的效果 `Process` ，下列螢幕擷取畫面顯示在其專屬私用程式中執行的服務：

![顯示在私用程式中執行之服務的螢幕擷取畫面](out-of-process-services-images/ipc-04.png "顯示在私用程式中執行之服務的螢幕擷取畫面。")

下一個螢幕擷取畫面顯示 `Process="com.xamarin.xample.messengerservice.timestampservice_process"` 在全域進程中執行的服務：

![在全域進程中執行的服務螢幕擷取畫面](out-of-process-services-images/ipc-05.png "在全域進程中執行的服務螢幕擷取畫面。")

一旦 `ServiceAttribute` 設定之後，服務必須執行 `Handler` 。

### <a name="implementing-a-handler"></a>執行處理常式

若要處理用戶端要求，服務必須執行 `Handler` ，並覆寫 `HandleMessage` 方法。 此方法會使用實例，此 `Message` 實例會從用戶端封裝方法呼叫，並將該呼叫轉譯為服務將執行的某些動作或工作。 此 `Message` 物件會公開一個稱為 `What` 整數值的屬性，其意義是在用戶端與服務之間共用，而且與服務要針對用戶端執行的一些工作相關。

下列來自範例應用程式的程式碼片段會顯示一個範例 `HandleMessage` 。 在此範例中，用戶端可以要求服務的動作有兩種：

- 第一個動作是 _Hello，World_ 訊息，用戶端已將簡單訊息傳送至服務。
- 第二個動作會在服務上叫用方法並取出字串，在此情況下，字串是傳回服務啟動時間和其執行時間長度的訊息：

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
                // The client has sent a simple Hello, say in the Android Log.
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

您也可以在中封裝服務的參數 `Message` 。 本指南稍後將討論這項功能。 下一個要考慮的主題是建立 `Messenger` 物件來處理傳入的 `Message` 。

### <a name="instantiating-the-messenger"></a>具現化 Messenger

如先前所討論，還原序列化 `Message` 物件和叫用 `Handler.HandleMessage` 是物件的責任 `Messenger` 。 `Messenger`類別也 `IBinder` 會提供用戶端用來將訊息傳送至服務的物件。  

當服務啟動時，它會具現化， `Messenger` 並插入 `Handler` 。 執行這項初始化的理想位置是在 `OnCreate` 服務的方法上。 此程式碼片段是服務的其中一個範例，其會初始化它自己的 `Handler` 和 `Messenger` ：

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

此時，最後一步是 `Service` 要覆寫 `OnBind` 。

### <a name="implementing-serviceonbind"></a>執行服務。 OnBind

所有系結的服務（不論是否在自己的進程中執行）都必須執行 `OnBind` 方法。 這個方法的傳回值是用戶端可用來與服務互動的一些物件。 該物件確切取決於服務是本機服務或遠端服務。 雖然本機服務會傳回自訂的 `IBinder` 執行，但遠端服務會傳回 `IBinder` 已封裝但在 `Messenger` 上一節中建立的：

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

完成這三個步驟之後，就可以將遠端服務視為完成。

## <a name="consuming-the-service"></a>使用服務

所有用戶端都必須執行一些程式碼，才能系結和使用遠端服務。 從概念上來說，從用戶端的觀點來看，系結至本機服務或遠端服務之間的差異極少。 用戶端會叫用 `BindService` 方法，並傳遞明確意圖來識別服務，以及 `IServiceConnection` 協助管理用戶端與服務之間的連接。

此程式碼片段是如何建立 **明確意圖** 來系結至遠端服務的範例。 意圖必須識別包含服務的封裝和服務的名稱。 設定這項資訊的其中一個方法是使用 `Android.Content.ComponentName` 物件，並將其提供給意圖。 此程式碼片段是一個範例：  

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

當服務系結時， `IServiceConnection.OnServiceConnected` 會叫用方法，並提供 `IBinder` 給用戶端。 不過，用戶端不會直接使用 `IBinder` 。 相反地，它會將 `Messenger` 來自的物件具現化 `IBinder` 。 這是 `Messenger` 用戶端將用來與遠端服務互動的。

以下是一個非常基本的實作為範例 `IServiceConnection` ，示範用戶端如何處理與服務的連線和中斷連接。 請注意， `OnServiceConnected` 方法會接收和 `IBinder` ，而用戶端 `Messenger` 會從建立 `IBinder` ：

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

        IsConnected = service != null;
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

一旦建立服務連接和意圖之後，用戶端就可以呼叫並起始系結程式 `BindService` ：

```csharp
var serviceConnection = new TimestampServiceConnection(this);
BindService(serviceToStart, serviceConnection, Bind.AutoCreate);
```

當用戶端成功系結至服務，而且 `Messenger` 可供使用之後，就可以將用戶端傳送 `Messages` 至服務。

## <a name="sending-messages-to-the-service"></a>將訊息傳送至服務

用戶端連接並擁有 `Messenger` 物件之後，就可以透過分派物件來與服務通訊 `Message` `Messenger` 。 這項通訊是單向的，用戶端會傳送訊息，但不會從服務傳回訊息給用戶端。 在這種情況下， `Message` 是一種火災和忘記的機制。

建立物件的慣用方式 `Message` 是使用 [`Message.Obtain`](xref:Android.OS.Message) factory 方法。 這個方法會 `Message` 從由 Android 維護的全域集區提取物件。 `Message.Obtain` 也有一些多載的方法，可讓 `Message` 物件以服務所需的值和參數進行初始化。  一旦具 `Message` 現化之後，它就會藉由呼叫來分派給服務 `Messenger.Send` 。 此程式碼片段是建立和分派至服務程式的其中一個範例 `Message` ：

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

方法有數種不同的形式 `Message.Obtain` 。 上述範例使用 [`Message.Obtain(Handler h, Int32 what)`](xref:Android.OS.Message.Obtain) 。 因為這是對跨進程服務的非同步要求;服務將不會有回應，因此 `Handler` 會將設定為 `null` 。 第二個參數 `Int32 what` 會儲存在 `.What` 物件的屬性中 `Message` 。 `.What`服務進程中的程式碼會使用屬性來叫用服務上的方法。

`Message`類別也會公開兩個額外的屬性，這些屬性可能會用在收件者： `Arg1` 和 `Arg2` 。 這兩個屬性是整數值，在用戶端與服務之間有一些特殊的值可能有意義。 例如， `Arg1` 可能會保留客戶識別碼，並 `Arg2` 可保留該客戶的訂單號碼。 [`Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)`](xref:Android.OS.Message.Obtain)當建立時，可以使用來設定這兩個屬性 `Message` 。 填入這兩個值的另一種方式是 `.Arg` 在 `.Arg2` 物件建立之後，直接在物件上設定和屬性 `Message` 。

### <a name="passing-additional-values-to-the-service"></a>將其他值傳遞給服務

您可以使用將更複雜的資料傳遞給服務 `Bundle` 。 在此情況下，您可以在 `Bundle` 傳送 `Message` 之前設定[ `.Data` property](xref:Android.OS.Message.Data)屬性，將額外的值放在中，並連同一起傳送。

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```

> [!NOTE]
> 一般而言， `Message` 不應該有大於1mb 的承載。 大小限制可能會因 Android 版本而異，而廠商可能對 Android 開放原始碼專案實施的任何專屬變更， (AOSP 與裝置配套的) 。

## <a name="returning-values-from-the-service"></a>從服務傳回值

此點已討論過的訊息架構是單向的，用戶端會將訊息傳送至服務。 如果服務需要將值傳回給用戶端，則已對此點討論過的所有內容都會反轉。 服務必須建立 `Message` 、封裝任何傳回值，然後將 `Message` via 分派 `Messenger` 至用戶端。 不過，此服務不會自行建立， `Messenger` 而是依賴用戶端具現化，並在初始要求中封裝 a `Messenger` 。 服務將會 `Send` 使用此用戶端提供的訊息 `Messenger` 。  

雙向通訊事件的順序如下：

1. 用戶端系結至服務。 當服務和用戶端連接時， `IServiceConnection` 用戶端所維護的會有 `Messenger` 物件的參考，該物件是用來將傳送 `Message` 至服務。 為了避免混淆，這會被稱為「 _服務 Messenger_」。
2. 用戶端會具現化 `Handler` 稱為 _用戶端處理常式_) 的 (，並使用它來初始化自己 `Messenger` 的 (_用戶端 Messenger_) 。 請注意，服務 Messenger 和用戶端 Messenger 是以兩種不同的方向處理流量的兩個不同物件。 服務 Messenger 會處理從用戶端到服務的訊息，而用戶端 Messenger 會處理從服務到用戶端的訊息。
3. 用戶端會建立 `Message` 物件，並 `ReplyTo` 使用用戶端 Messenger 來設定屬性。 然後使用服務 Messenger 將訊息傳送至服務。
4. 服務會接收來自用戶端的訊息，並執行所要求的工作。
5. 當服務將回應傳送給用戶端時，它將會用 `Message.Obtain` 來建立新的 `Message` 物件。
6. 為了將此訊息傳送至用戶端，此服務會從用戶端訊息的屬性中將用戶端 Messenger 解壓縮，並將其用於 `.ReplyTo` `.Send` `Message` 回用戶端。
7. 當用戶端收到回應時，它 `Handler` 會 `Message` 檢查 `.What` 屬性 (，並視需要將) 所包含的任何參數解壓縮，以處理該回應 `Message` 。

此範例程式碼會示範用戶端如何具現化 `Message` 和封裝 a `Messenger` ，服務應該使用它來回應：

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

服務必須對自己進行一些變更， `Handler` 才能將 `Messenger` 回復傳送給用戶端，並使用它來傳送回復給用戶端。 此程式碼片段是一個範例，說明服務會如何 `Handler` 建立 `Message` ，並將它傳回給用戶端：  

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

請注意，在上述程式碼範例中， `Messenger` 用戶端所建立的實例 *不* 是服務所接收的相同物件。 這兩個不同 `Messenger` 的物件是在兩個代表通道的不同進程中執行的。

## <a name="securing-the-service-with-android-permissions"></a>使用 Android 許可權保護服務

在全域進程中執行的服務，可供在該 Android 裝置上執行的所有應用程式存取。 在某些情況下，這種開放性和可用性是不必要的，而且必須保護服務免于未經授權的用戶端存取。 限制存取遠端服務的其中一種方式是使用 Android 許可權。

`Permission` `ServiceAttribute` 裝飾子類別之的屬性可以識別許可權 `Service` 。 這會命名用戶端在系結至服務時必須授與的許可權。 如果用戶端沒有適當的許可權，則 `Java.Lang.SecurityException` 當用戶端嘗試系結至服務時，Android 會擲回。

Android 提供四種不同的許可權等級：

- **正常** &ndash; 這是預設的許可權等級。 它是用來識別低風險許可權，這些許可權可由 Android 自動授與給要求的用戶端。 使用者不需要明確授與這些許可權，但可以在應用程式設定中查看許可權。
- 簽章**signature** &ndash;這是特殊的許可權類別，會由 Android 自動授與所有使用相同憑證簽署的應用程式。 此許可權的設計目的是讓應用程式開發人員輕鬆地在其應用程式之間共用元件或資料，而不竟然想使用者進行持續核准。
- **signatureOrSystem** &ndash;這非常類似于上面所述的簽**章許可權。** 除了自動授與給相同憑證所簽署的應用程式之外，也會將此許可權授與已簽署相同憑證的應用程式，而該憑證是用來簽署與 Android 系統映射一起安裝的應用程式。 此許可權通常僅供 Android ROM 開發人員使用，以允許其應用程式使用協力廠商應用程式。 這種應用程式通常不會使用這種應用程式，這通常是公用的一般散發。
- **危險** &ndash; 危險的許可權是可能導致使用者問題的許可權。 基於這個理由，使用者必須明確地核准 **危險** 的許可權。

因為 `signature` 和 `normal` 許可權會在 Android 的安裝時間自動授與，所以在包含用戶端的 APK **之前** ，APK 裝載服務是很重要的。 如果先安裝用戶端，則 Android 不會授與許可權。 在此情況下，必須卸載用戶端 APK、安裝服務 APK，然後重新安裝用戶端 APK。

有兩種常見的方式可使用 Android 許可權保護服務：

1. **執行簽章層級安全性** &ndash; 簽章層級安全性表示系統會自動將許可權授與使用相同金鑰簽署的應用程式，而這些應用程式是用來簽署保存服務的 APK。 這是一種簡單的方法，可讓開發人員保護其服務的安全，同時讓它們可從自己的應用程式存取。 簽章層級許可權的宣告方式是將的 `Permission` 屬性設定 `ServiceAttribute` 為 `signature` ：

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2. **建立自訂許可權** &ndash; 服務的開發人員可以建立服務的自訂許可權。 當開發人員想要與其他開發人員的應用程式共用服務時，這是最適合的做法。 自訂許可權需要更多投入時間才能完成，並將于下方討論。

下一節將說明建立自訂許可權的簡化範例 `normal` 。 如需 Android 許可權的詳細資訊，請參閱 Google 的檔，以取得 [& 安全性的最佳作法](https://developer.android.com/training/articles/security-tips.html)。 如需 Android 許可權的詳細資訊，請參閱應用程式資訊清單 Android 檔的 [許可權一節](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) ，以取得 android 許可權的詳細資訊。

> [!NOTE]
> 一般情況下， [Google 會防止使用自訂許可權](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) ，因為它們可能會對使用者造成混淆。

### <a name="creating-a-custom-permission"></a>建立自訂許可權

若要使用自訂許可權，當用戶端明確要求該許可權時，服務會將其宣告。

若要在服務 APK 中建立許可權， `permission` 元素會加入至 `manifest` **AndroidManifest.xml**中的專案。 此許可權必須 `name` `protectionLevel` 設定、和 `label` 屬性。 `name`屬性必須設定為可唯一識別許可權的字串。 名稱將會顯示在 [ **Android 設定**] (的 [**應用程式資訊**] 中，如下一節所示) 。

`protectionLevel`屬性必須設定為上述四個字串值的其中一個。  `label`和 `description` 必須參考字串資源，用來為使用者提供易記的名稱和描述。

此程式碼片段是 `permission` 在包含服務的 APK **AndroidManifest.xml** 中宣告自訂屬性的範例：

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

然後，用戶端 APK 的 **AndroidManifest.xml** 必須明確地要求這個新的許可權。 將 `users-permission` 屬性新增至 **AndroidManifest.xml**，即可完成此動作：

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

若要查看已授與應用程式的許可權，請開啟 [Android 設定] 應用程式，然後選取 [ **應用**程式]。 在清單中尋找並選取應用程式。 從 [ **應用程式資訊** ] 畫面中，按一下 [ **許可權** ]，以顯示已授與應用程式的擁有權限：

[![Android 裝置的螢幕擷取畫面，顯示如何尋找授與應用程式的許可權](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>摘要

本指南是有關如何在遠端進程中執行 Android 服務的先進討論。 說明本機和遠端服務之間的差異，以及一些遠端服務對於 Android 應用程式的穩定性和效能有説明的原因。 說明如何執行遠端服務以及用戶端如何與服務通訊之後，本指南會提供一種方法來限制只有經過授權的用戶端才能存取服務。

## <a name="related-links"></a>相關連結

- [處理常式](xref:Android.OS.Handler)
- [訊息](xref:Android.OS.Message)
- [Messenger](xref:Android.OS.Messenger)
- [ServiceAttribute](xref:Android.App.ServiceAttribute)
- [匯出的屬性](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [具有隔離處理常式和自訂應用程式類別的服務無法正確解析多載](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [進程和執行緒](https://developer.android.com/guide/components/processes-and-threads.html)
- [Android 資訊清單-許可權](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [安全性秘訣](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-messengerservicedemo)
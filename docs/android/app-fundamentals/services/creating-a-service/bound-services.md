---
title: Xamarin 中的系結服務
description: 系結服務是 Android 服務，提供用戶端（例如 Android 活動）可與互動的用戶端伺服器介面。 本指南將討論與建立系結服務相關的主要元件，以及如何在 Xamarin Android 應用程式中使用它。
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/04/2018
ms.openlocfilehash: 584f523446584192cfa882697c0f76865ce78a10
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70754917"
---
# <a name="bound-services-in-xamarinandroid"></a>Xamarin 中的系結服務

_系結服務是 Android 服務，提供用戶端（例如 Android 活動）可與互動的用戶端伺服器介面。本指南將討論與建立系結服務相關的主要元件，以及如何在 Xamarin Android 應用程式中使用它。_

## <a name="bound-services-overview"></a>系結服務總覽

提供用戶端伺服器介面的服務，可讓用戶端直接與服務互動，稱為系結_服務_。  可以同時有多個用戶端連線到服務的單一實例。 系結服務和用戶端會彼此隔離。 相反地，Android 提供一系列的中繼物件來管理兩者之間的連接狀態。 這個狀態是由執行[`Android.Content.IServiceConnection`](xref:Android.Content.IServiceConnection)介面的物件維護。  這個物件是由用戶端所建立，並做為參數傳遞至[`BindService`](xref:Android.Content.Context.BindService*)方法。 @No__t_0 可在任何[`Android.Content.Context`](xref:Android.Content.Context)物件（例如活動）上使用。 這是對 Android 作業系統提出的要求，可啟動服務並將用戶端系結至它。 有三種方式可以使用 `BindService` 方法，將用戶端系結至服務：

- **服務系結器 &ndash; 服務**系結器是一個可執行[`Android.OS.IBinder`](xref:Android.OS.IBinder)介面的類別。 大部分的應用程式都不會直接執行此介面，而是會擴充[`Android.OS.Binder`](xref:Android.OS.Binder)類別。 這是最常見的方法，適用于服務和用戶端存在於相同的進程中。
- **使用 Messenger** &ndash; 這項技術適用于服務可能存在於不同的進程中。 相反地，服務要求會透過[`Android.OS.Messenger`](xref:Android.OS.Messenger)在用戶端和服務之間進行封送處理。 系統會在服務中建立[`Android.OS.Handler`](xref:Android.OS.Handler) ，以處理 `Messenger` 的要求。 這會在另一個指南中涵蓋。
- **使用 Android 介面定義語言（AIDL）** &ndash; [AIDL](https://developer.android.com/guide/components/aidl)是不會在本指南中涵蓋的先進技術。

一旦用戶端已系結至服務，這兩者之間的通訊會透過 `Android.OS.IBinder` 物件進行。  這個物件會負責讓用戶端與服務互動的介面。 每個 Xamarin Android 應用程式都不需要從頭開始執行此介面，Android SDK 提供[`Android.OS.Binder`](xref:Android.OS.Binder)類別，它會處理用戶端與服務之間的封送處理物件所需的大部分程式碼。

當用戶端使用服務完成時，它必須藉由呼叫 `UnbindService` 方法來解除系結。 當最後一個用戶端已從服務解除系結之後，Android 將會停止並處置系結服務。

下圖說明活動、服務連接、系結程式和服務彼此之間的關聯：

![顯示服務元件如何相互關聯的圖表](bound-services-images/bound-services-02.png "圖表，顯示服務元件彼此之間的關聯性。")

本指南將討論如何擴充 `Service` 類別，以執行系結服務。 它也會涵蓋如何執行 `IServiceConnection` 並擴充 `Binder`，以允許用戶端與服務進行通訊。 本指南隨附的範例應用程式，其中包含具有單一 Xamarin. Android 專案的解決方案，稱為 **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** 。 這是一個非常基本的應用程式，示範如何執行服務，以及如何將活動系結至它。 系結服務有一個非常簡單的 API，其中只有一個方法，`GetFormattedTimestamp`，它會傳回一個字串，告訴使用者服務啟動的時間，以及它已執行多久。 應用程式也可讓使用者手動解除系結和系結至服務。

[在 Android 手機上執行的應用程式 ![Screenshot](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>執行和使用系結服務

有三個必須執行的元件，Android 應用程式才能使用系結的服務：

1. **擴充 `Service` 類別並實作為生命週期回呼方法，** &ndash; 此類別將包含將會執行服務所要求之工作的程式碼。 下面將更詳細地說明這一點。
2. **建立可執行 `IServiceConnection` 的類別**&ndash; 此介面提供的回呼方法將由 Android 叫用，以在服務的連線變更時通知用戶端，也就是用戶端已連接或中斷服務的連接。 服務連線也會提供物件的參考，讓用戶端用來直接與服務互動。 此參考稱為系結器 _。_
3. **建立一個會執行 `IBinder` 的類別，** &ndash; 系結_器執行會_提供用戶端用來與服務通訊的 API。 系結器可以提供系結服務的參考，允許直接叫用方法，或系結器可以提供用戶端 API 來封裝和隱藏應用程式中的系結服務。 @No__t_0 必須提供遠端程序呼叫所需的程式碼。 不需要（或建議）直接執行 `IBinder` 介面。 相反地，應用程式應該擴充 `Binder` 類型，以提供 `IBinder` 所需的大部分基本功能。
4. **啟動和系結至服務**&ndash; 在服務連線、系結器和服務建立之後，Android 應用程式會負責啟動服務並系結至它。

下列各節將更詳細地討論每個步驟。

### <a name="extend-the-service-class"></a>擴充 `Service` 類別

若要使用 Xamarin 建立服務，必須將 `Service` 子類別化，並使用[`ServiceAttribute`](xref:Android.App.ServiceAttribute)裝飾類別。 在應用程式的 Androidmanifest.xml 中，會使用屬性來正確地將服務註冊到 app 的檔案中，就像活動一樣，系結服務擁有其本身的生命週期，以及與中重大事件相關聯的回呼方法它的生命週期。 下列清單是服務將會執行的一些較常見的回呼方法範例：

- `OnCreate` &ndash; 此方法是由 Android 叫用，因為它會將服務具現化。 它是用來初始化服務存留期期間所需的任何變數或物件。 這個方法是一個選擇項目。
- `OnBind` &ndash; 這個方法必須由所有系結的服務來執行。 當第一個用戶端嘗試連接到服務時，就會叫用它。 它會傳回 `IBinder` 的實例，讓用戶端可以與服務互動。 只要服務正在執行，就會使用 `IBinder` 物件來滿足未來任何要系結至服務的用戶端要求。
- `OnUnbind` &ndash; 當所有系結的用戶端都已解除系結時，會呼叫這個方法。 藉由從這個方法傳回 `true`，服務會在新的用戶端系結至 `OnUnbind` 時，于稍後使用傳遞至的意圖來呼叫 `OnRebind`。 當服務在解除系結之後繼續執行時，您會這麼做。 如果最近解除系結的服務也是已啟動的服務，而且 `StopService` 或 `StopSelf` 尚未呼叫，就會發生這種情況。 在這種情況下，`OnRebind` 允許取得意圖。 預設會傳回 `false`，而不會執行任何操作。 選擇項。
- `OnDestroy` &ndash; 當 Android 終結服務時，會呼叫這個方法。 任何必要的清除作業（例如釋放資源）都應該在這個方法中執行。 選擇項。

系結服務的主要生命週期事件如下圖所示：

![顯示生命週期方法呼叫順序的圖表](bound-services-images/bound-services-01.png "圖表，顯示生命週期方法的呼叫順序。")

下列程式碼片段（來自本指南隨附的隨附應用程式）說明如何在 Xamarin 中執行系結服務：

```csharp
using Android.App;
using Android.Util;
using Android.Content;
using Android.OS;

namespace BoundServiceDemo
{
    [Service(Name="com.xamarin.ServicesDemo1")]
    public class TimestampService : Service, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampService).FullName;
        IGetTimestamp timestamper;

        public IBinder Binder { get; private set; }

        public override void OnCreate()
        {
            // This method is optional to implement
            base.OnCreate();
            Log.Debug(TAG, "OnCreate");
            timestamper = new UtcTimestamper();
        }

        public override IBinder OnBind(Intent intent)
        {
            // This method must always be implemented
            Log.Debug(TAG, "OnBind");
            this.Binder = new TimestampBinder(this);
            return this.Binder;
        }

        public override bool OnUnbind(Intent intent)
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnUnbind");
            return base.OnUnbind(intent);
        }

        public override void OnDestroy()
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnDestroy");
            Binder = null;
            timestamper = null;
            base.OnDestroy();
        }

        /// <summary>
        /// This method will return a formatted timestamp to the client.
        /// </summary>
        /// <returns>A string that details what time the service started and how long it has been running.</returns>
        public string GetFormattedTimestamp()
        {
            return timestamper?.GetFormattedTimestamp();
        }
    }
}
```

在此範例中，`OnCreate` 方法會初始化一個物件，它會保存用來抓取和格式化用戶端所要求之時間戳記的邏輯。 當第一個用戶端嘗試系結至服務時，Android 會叫用 `OnBind` 方法。 此服務將會具現化 `TimestampBinder` 物件，讓用戶端能夠存取此執行中服務的實例。 下一節將討論 `TimestampBinder` 類別。

### <a name="implementing-ibinder"></a>執行 IBinder

如前所述，`IBinder` 物件會提供用戶端與服務之間的通道。 Android 應用程式不應該執行 `IBinder` 介面， [`Android.OS.Binder`](xref:Android.OS.Binder)應該擴充。 @No__t_0 類別提供許多必要的基礎結構，需要將系結器物件從服務（可能在不同的進程中執行）封送處理至用戶端。 在大部分情況下，`Binder` 子類別只是幾行程式碼，並且會包裝對服務的參考。 在此範例中，`TimestampBinder` 具有可向用戶端公開 `TimestampService` 的屬性：

```csharp
public class TimestampBinder : Binder
{
    public TimestampBinder(TimestampService service)
    {
        this.Service = service;
    }

    public TimestampService Service { get; private set; }
}
```

這個 `Binder` 可以在服務上叫用公用方法;例如：

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

一旦 `Binder` 擴充之後，就必須執行 `IServiceConnection`，才能將所有專案連接在一起。

### <a name="creating-the-service-connection"></a>建立服務連接

@No__t_0 將會顯示 | 引進 | 公開 | 將 `Binder` 物件連接到用戶端。 除了執行 `IServiceConnection` 介面，類別也必須擴充 `Java.Lang.Object`。 服務連線也應該提供一些方法，讓用戶端可以存取 `Binder` （因而與系結服務通訊）。

這段程式碼來自隨附的範例專案，是執行 `IServiceConnection` 的一種可能方式：

```csharp
using Android.Util;
using Android.OS;
using Android.Content;

namespace BoundServiceDemo
{
    public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampServiceConnection).FullName;

        MainActivity mainActivity;
        public TimestampServiceConnection(MainActivity activity)
        {
            IsConnected = false;
            Binder = null;
            mainActivity = activity;
        }

        public bool IsConnected { get; private set; }
        public TimestampBinder Binder { get; private set; }

        public void OnServiceConnected(ComponentName name, IBinder service)
        {
            Binder = service as TimestampBinder;
            IsConnected = this.Binder != null;

            string message = "onServiceConnected - ";
            Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

            if (IsConnected)
            {
                message = message + " bound to service " + name.ClassName;
                mainActivity.UpdateUiForBoundService();
            }
            else
            {
                message = message + " not bound to service " + name.ClassName;
                mainActivity.UpdateUiForUnboundService();
            }

            Log.Info(TAG, message);
            mainActivity.timestampMessageTextView.Text = message;

        }

        public void OnServiceDisconnected(ComponentName name)
        {
            Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
            IsConnected = false;
            Binder = null;
            mainActivity.UpdateUiForUnboundService();
        }

        public string GetFormattedTimestamp()
        {
            if (!IsConnected)
            {
                return null;
            }

            return Binder?.GetFormattedTimestamp();
        }
    }
}

```

作為系結程式的一部分，Android 會叫用 `OnServiceConnected` 方法，提供所系結之服務的 `name`，以及保留服務本身參考的 `binder`。 在此範例中，服務連接有兩個屬性，一個保存系結器的參考，而如果用戶端連線到服務，則為的布林值旗標。

只有當用戶端與服務之間的連接意外遺失或中斷時，才會叫用 `OnServiceDisconnected` 方法。 這個方法可讓用戶端有機會回應服務中斷。  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>以明確意圖啟動和系結至服務

若要使用系結服務，用戶端（例如活動）必須具現化可執行 `Android.Content.IServiceConnection` 並叫用 `BindService` 方法的物件。 如果服務系結至，`BindService` 會傳回 `true`，如果不是，則會傳回 `false`。 `BindService` 方法採用三個參數：

- **@No__t_1** &ndash; 意圖應該明確識別要連接的服務。
- @No__t_2 這個物件**的 `IServiceConnection` 物件**是一個媒介，它會提供回呼方法，以便在系結服務啟動和停止時通知用戶端。
- **[`Android.Content.Bind`](xref:Android.Content.Bind)列舉**&ndash; 此參數是系統在系結物件時所使用的一組旗標。 最常使用的值是[`Bind.AutoCreate`](xref:Android.Content.Bind.AutoCreate)，如果服務尚未執行，則會自動啟動。

下列程式碼片段是如何在活動中使用明確意圖啟動系結服務的範例：

```csharp
protected override void OnStart ()
{
    base.OnStart ();

    if (serviceConnection == null)
    {
        this.serviceConnection = new TimestampServiceConnection(this);
    }

    Intent serviceToStart = new Intent(this, typeof(TimestampService));
    BindService(serviceToStart, this.serviceConnection, Bind.AutoCreate);

}
```

> [!IMPORTANT]
> 從 Android 5.0 （API 層級21）開始，只能系結至具有明確意圖的服務。

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>關於服務連線和系結器的架構注意事項。

某些 OOP purists 可能會 disapprove `TimestampBinder` 類別的上一次執行，因為違反了[Demeter」法則](https://en.wikipedia.org/wiki/Law_of_Demeter)，其最簡單的形式是「不要與陌生人交談;僅與您的朋友交談」。 這個特定的執行會向所有用戶端公開具體的 `TimestampService` 類別。

嚴格來說，用戶端不需要知道 `TimestampService`，並將該具體類別公開給用戶端，就可以讓應用程式更脆弱，而且在它的存留期間更難維護。 另一種方法是使用介面來公開 `GetFormattedTimestamp()` 方法，並透過 `Binder` （或服務連接類別）對服務進行 proxy 呼叫：  

```csharp
public class TimestampBinder : Binder, IGetTimestamp
{
    TimestampService service;
    public TimestampBinder(TimestampService service)
    {
        this.service = service;
    }

    public string GetFormattedTimestamp()
    {
        return service?.GetFormattedTimestamp();
    }
}
```

這個特定的範例可讓活動在服務本身上叫用方法：

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```

## <a name="related-links"></a>相關連結

- [Android 應用程式服務](xref:Android.App.Service)
- [Android. Bind](xref:Android.Content.Bind)
- [Android. 內容](xref:Android.Content.Context)
- [IServiceConnection](xref:Android.Content.IServiceConnection)
- [Android。](xref:Android.OS.Binder)
- [IBinder](xref:Android.OS.IBinder)
- [BoundServiceDemo （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-boundservicedemo)

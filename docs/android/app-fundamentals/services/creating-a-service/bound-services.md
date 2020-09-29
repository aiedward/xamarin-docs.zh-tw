---
title: Xamarin 中的系結服務
description: 系結的服務是 Android 服務，提供用戶端伺服器介面，用戶端 (例如 Android 活動) 可與其互動。 本指南將討論建立系結服務所涉及的重要元件，以及如何在 Xamarin Android 應用程式中使用它。
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/04/2018
ms.openlocfilehash: 3aacb0f9b3aca0c8b64e01eb79270e73750c9f3d
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455494"
---
# <a name="bound-services-in-xamarinandroid"></a>Xamarin 中的系結服務

_系結的服務是 Android 服務，提供用戶端伺服器介面，用戶端 (例如 Android 活動) 可與其互動。本指南將討論建立系結服務所涉及的重要元件，以及如何在 Xamarin Android 應用程式中使用它。_

## <a name="bound-services-overview"></a>系結服務總覽

提供用戶端伺服器介面讓用戶端直接與服務互動的服務，稱為系結  _服務_。  您可以有多個用戶端同時連線到服務的單一實例。 系結服務和用戶端彼此隔離。 相反地，Android 會提供一系列的中繼物件來管理兩者之間的連接狀態。 此狀態是由實介面的物件所維護 [`Android.Content.IServiceConnection`](xref:Android.Content.IServiceConnection) 。  此物件是由用戶端所建立，並以參數形式傳遞給 [`BindService`](xref:Android.Content.Context.BindService*) 方法。 `BindService`可用於任何 [`Android.Content.Context`](xref:Android.Content.Context) 物件 (例如活動) 。 這是對 Android 作業系統的要求，可啟動服務並將用戶端系結至該服務。 用戶端有三種方式可以使用方法系結至服務 `BindService` ：

- 服務系結器**A service binder** &ndash;服務系結器是實介面的類別 [`Android.OS.IBinder`](xref:Android.OS.IBinder) 。 大部分的應用程式不會直接執行此介面，而是會擴充 [`Android.OS.Binder`](xref:Android.OS.Binder) 類別。 這是最常見的方法，適用于服務和用戶端都存在於相同的進程時。
- **使用 Messenger** &ndash; 這項技術適用于當服務可能存在於不同的進程時。 相反地，服務要求會透過，在用戶端與服務之間進行封送處理 [`Android.OS.Messenger`](xref:Android.OS.Messenger) 。 會 [`Android.OS.Handler`](xref:Android.OS.Handler) 在服務中建立，以處理 `Messenger` 要求。 這將會在另一個指南中討論。
- **使用 Android 介面定義語言 (AIDL) ** &ndash;[AIDL](https://developer.android.com/guide/components/aidl)是一種不會涵蓋在本指南中的先進技術。

一旦用戶端系結至服務，兩者之間的通訊會透過 `Android.OS.IBinder` 物件進行。  此物件負責將允許用戶端與服務互動的介面。 每個 Xamarin. Android 應用程式都不需要從頭開始執行此介面，Android SDK 會提供類別，以 [`Android.OS.Binder`](xref:Android.OS.Binder) 處理在用戶端與服務之間封送處理物件時所需的大部分程式碼。

當用戶端使用服務完成時，它必須藉由呼叫方法解除系結 `UnbindService` 。 當最後一個用戶端從服務解除系結之後，Android 將會停止並處置系結服務。

下圖說明活動、服務連接、系結器與服務彼此之間的關聯：

![顯示服務元件如何彼此關聯的圖表](bound-services-images/bound-services-02.png "此圖顯示服務元件彼此之間的關聯性。")

本指南將討論如何擴充 `Service` 類別以執行系結服務。 它也會涵蓋執行 `IServiceConnection` 和擴充 `Binder` ，以允許用戶端與服務進行通訊。 本指南隨附一個範例應用程式，其中包含一個名為 **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** 的單一 Xamarin. Android 專案的解決方案。 這是非常基本的應用程式，示範如何執行服務，以及如何將活動系結至該服務。 系結服務有一個非常簡單的 API，其中只有一個方法， `GetFormattedTimestamp` 它會傳回一個字串，告知使用者服務啟動的時間，以及其執行的時間長度。 應用程式也可讓使用者手動解除系結和系結至服務。

[![Android 手機上執行的應用程式螢幕擷取畫面](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>執行和使用系結服務

為了讓 Android 應用程式取用系結服務，必須執行三個元件：

1. **擴充 `Service` 類別並執行生命週期回呼方法** &ndash; 此類別包含的程式碼將會執行服務所要求的工作。 以下將詳細說明這一點。
2. **建立可 `IServiceConnection` 執行的類別**&ndash;此介面會提供回呼方法，以在服務的連線已變更（亦即用戶端已連線或中斷連線至服務）時，由 Android 叫用來通知用戶端。 服務連接也會提供物件的參考，讓用戶端可以用來直接與服務互動。 此參考稱為系結器 _。_
3. **建立可 `IBinder` 執行的類別**系結器 &ndash; 執行會提供用戶端用來與服務通訊的 API。 _Binder_ 系結器可以提供系結服務的參考、允許直接叫用方法，或系結器可以提供用戶端 API，從應用程式封裝和隱藏系結的服務。 `IBinder`必須提供遠端程序呼叫的必要程式碼。 不需要 (或建議的) `IBinder` 直接執行介面。 相反地，應用程式應擴充型別， `Binder` 以提供所需的大部分基本功能 `IBinder` 。
4. **啟動和系結至服務** &ndash; 一旦建立服務連線、系結器和服務之後，Android 應用程式會負責啟動服務並系結至該服務。

下列各節將更詳細地討論這些步驟。

### <a name="extend-the-service-class"></a>擴充 `Service` 類別

若要使用 Xamarin 建立服務，必須使用子類別 `Service` 和來裝飾類別 [`ServiceAttribute`](xref:Android.App.ServiceAttribute) 。 Xamarin. Android build tools 會使用此屬性，在應用程式的 **AndroidManifest.xml** 檔案中適當地註冊服務，就像活動一樣，系結的服務有它自己的生命週期，以及與在其生命週期中的重大事件相關聯的回呼方法。 下列清單是服務將會執行的一些較常見回呼方法的範例：

- `OnCreate`&ndash;此方法是由 Android 叫用，因為它會將服務具現化。 它是用來初始化服務在存留期間內所需的任何變數或物件。 這個方法是一個選擇項目。
- `OnBind`&ndash;這個方法必須由所有系結的服務來執行。 它是在第一個用戶端嘗試連接到服務時叫用。 它會傳回的實例， `IBinder` 讓用戶端可以與服務互動。 只要服務正在執行， `IBinder` 物件將用來滿足未來任何系結至服務的用戶端要求。
- `OnUnbind`&ndash;當所有系結的用戶端都未系結時，就會呼叫這個方法。 藉由  `true` 從這個方法傳回，服務稍後會在 `OnRebind` 新用戶端系結時，使用傳遞至的意圖來呼叫 `OnUnbind` 。 如果服務在解除系結之後仍繼續執行，您就可以這麼做。 如果最近解除系結的服務也是已啟動的服務，  `StopService` 或尚未呼叫，就會發生這種情況  `StopSelf` 。 在這種情況下，  `OnRebind` 允許抓取意圖。 預設值  `false` 會傳回，而不會執行任何動作。 選擇性。
- `OnDestroy`&ndash;當 Android 終結服務時，會呼叫這個方法。 任何必要的清除作業（例如釋出資源）都應該在此方法中執行。 選擇性。

此圖表顯示系結服務的主要生命週期事件：

![顯示生命週期方法呼叫順序的圖表](bound-services-images/bound-services-01.png "圖表，顯示呼叫生命週期方法的順序。")

下列程式碼片段會從本指南隨附的隨附應用程式中，說明如何在 Xamarin 中執行系結服務：

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

在此範例中， `OnCreate` 方法會初始化物件，此物件會保存用來抓取和格式化用戶端所要求之時間戳記的邏輯。 當第一個用戶端嘗試系結至服務時，Android 會叫用 `OnBind` 方法。 這項服務會將物件具現化 `TimestampBinder` ，以允許用戶端存取這個執行中服務的實例。 `TimestampBinder`下一節將討論這個類別。

### <a name="implementing-ibinder"></a>執行 IBinder

如前所述，物件會 `IBinder` 提供用戶端與服務之間的通道。 Android 應用程式不應該執行 `IBinder` 介面， [`Android.OS.Binder`](xref:Android.OS.Binder) 應該擴充。 `Binder`類別可提供許多必要的基礎結構，此基礎結構需要從服務封送處理系結器物件， (可能會在用戶端) 個別的進程中執行。 在大部分的情況下， `Binder` 子類別只是幾行程式碼，並且會包裝對服務的參考。 在此範例中， `TimestampBinder` 有一個屬性會 `TimestampService` 向用戶端公開：

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

如此一 `Binder` 來，就可以在服務上叫用公用方法，例如：

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

一旦 `Binder` 延伸之後，就必須執行， `IServiceConnection` 才能將所有專案連接在一起。

### <a name="creating-the-service-connection"></a>建立服務連接

`IServiceConnection`將會出現 | 引進 | 公開 | 將 `Binder` 物件連接至用戶端。 除了執行 `IServiceConnection` 介面，類別也必須延伸 `Java.Lang.Object` 。 服務連接也應該提供一些方法，讓用戶端存取 `Binder` (，因此可以與系結服務) 通訊。

這段程式碼是來自隨附的範例專案，這是一種可行的方式 `IServiceConnection` ：

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

在系結程式中，Android 將會叫用 `OnServiceConnected` 方法，提供所系結 `name` 之服務的，以及 `binder` 保存服務本身參考的。 在此範例中，服務連接有兩個屬性，一個保存系結器的參考，而如果用戶端連接到服務，則為布林值旗標。

`OnServiceDisconnected`只有當用戶端與服務之間的連接意外遺失或中斷時，才會叫用此方法。 此方法可讓用戶端有機會回應服務中斷。  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>啟動並系結至具有明確意圖的服務

若要使用系結的服務，用戶端 (例如活動) 必須具現化可執行和叫用方法的物件 `Android.Content.IServiceConnection` `BindService` 。 `BindService` 如果服務系結，則會傳回 `true` ，如果不是，則為 `false` 。 `BindService` 方法採用三個參數：

- **A `Intent` **&ndash;意圖應該明確地識別要連接的服務。
- **A `IServiceConnection` 物件** &ndash; ：此物件是可提供回呼方法的媒介，可在系結服務啟動和停止時通知用戶端。
- ** [`Android.Content.Bind`](xref:Android.Content.Bind) 列舉** &ndash;此參數是系統在系結物件時，所使用的一組旗標。 最常使用的值是 [`Bind.AutoCreate`](xref:Android.Content.Bind.AutoCreate) ，如果服務尚未執行，則會自動啟動該服務。

下列程式碼片段是如何使用明確意圖在活動中啟動系結服務的範例：

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
> 從 Android 5.0 開始 (API 層級 21) 只可以系結至具有明確意圖的服務。

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>有關服務連接和系結器的架構附注。

某些 OOP purists 可能 disapprove 了先前的 `TimestampBinder` 類別實，因為它違反了 [Demeter」的法律](https://en.wikipedia.org/wiki/Law_of_Demeter) ，其最簡單的形式是「不要與陌生人對話;只與您的朋友對話」。 這項特定的執行會將具體 `TimestampService` 類別公開給所有用戶端。

嚴格來說，用戶端不需要知道， `TimestampService` 並將該具體類別公開給用戶端，可以讓應用程式更脆弱且更難維護其存留期。 替代方法是使用介面，此介面會公開 `GetFormattedTimestamp()` 方法，並透過 `Binder` (或服務連接類別) ，來對服務進行 proxy 呼叫：  

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

此特定範例可讓活動在服務本身上叫用方法：

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```

## <a name="related-links"></a>相關連結

- [Android. App. 服務](xref:Android.App.Service)
- [Android. Bind](xref:Android.Content.Bind)
- [Android. 內容](xref:Android.Content.Context)
- [IServiceConnection](xref:Android.Content.IServiceConnection)
- [Android。](xref:Android.OS.Binder)
- [IBinder](xref:Android.OS.IBinder)
- [BoundServiceDemo (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-boundservicedemo)
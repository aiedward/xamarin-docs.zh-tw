---
title: Xamarin.Android 中繫結的服務
description: 繫結的服務是 Android 提供用戶端-伺服器介面 （例如 Android 的活動） 的用戶端可以與之互動。 本指南將討論建立繫結的服務，以及如何使用 Xamarin.Android 應用程式中所涉及的關鍵元件。
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 18cfe6acae08efac85223c9c121a12f102f846cc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="bound-services-in-xamarinandroid"></a>Xamarin.Android 中繫結的服務

_繫結的服務是 Android 提供用戶端-伺服器介面 （例如 Android 的活動） 的用戶端可以與之互動。本指南將討論建立繫結的服務，以及如何使用 Xamarin.Android 應用程式中所涉及的關鍵元件。_

## <a name="bound-services-overview"></a>繫結的服務概觀

提供用戶端與伺服器的介面直接互動的用戶端與服務的服務稱為_繫結服務_。  可以有多個用戶端連線到服務的單一執行個體，在相同的時間。 繫結的服務和用戶端會互相隔離。 相反地，Android 提供一系列的中繼管理兩者之間的連線狀態的物件。 此狀態由該物件會實作維護[ `Android.Content.IServiceConnection` ](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)介面。  這個物件是由用戶端，做為參數來傳遞[ `BindService` ](https://developer.xamarin.com/api/member/Android.Content.Context.BindService/)方法。 `BindService`位於任何[ `Android.Content.Context` ](https://developer.xamarin.com/api/type/Android.Content.Context)物件 （例如活動）。 它是 Android 作業系統來啟動服務，並將用戶端繫結至它的要求。 有三種方式可在用戶端可以繫結至服務，使用`BindService`方法：

* **服務繫結器**&ndash;的服務繫結器是一個類別，實作[ `Android.OS.IBinder` ](https://developer.xamarin.com/api/type/Android.OS.IBinder)介面。 大部分的應用程式不會實作這個介面直接，它們將會改為擴充[ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder)類別。 這是最常見的方法，而且適用於當服務和用戶端存在於相同的程序。
* **使用 Messenger** &ndash;這項技術是適用於當服務可能會存在於不同的處理序。 相反地，服務要求會封送處理之間的用戶端和服務透過[ `Android.OS.Messenger` ](https://developer.xamarin.com/api/type/Android.OS.Messenger)。 [ `Android.OS.Handler` ](https://developer.xamarin.com/api/type/Android.OS.Handler)處理服務中建立`Messenger`要求。 這會涵蓋其他指南中。
* **使用 AIDL** &ndash;這萬一恐怖攻擊的大部分的 Android 開發人員中心中並不會涵蓋在本指南中。

一旦用戶端已經繫結至服務，兩者之間的通訊是透過`Android.OS.IBinder`物件。  此物件負責將會允許用戶端與服務互動的介面。 您不需要實作這個介面，從可用的每個 Xamarin.Android 應用程式，提供 Android SDK [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder)負責大部分的程式碼所需要的物件之間封送處理類別用戶端和服務。

當用戶端與服務完成時，它必須解除繫結從它藉由呼叫`UnbindService`方法。 最後一個用戶端有未繫結從服務時，一旦 Android 將會停止，並處置繫結的服務。

此圖說明活動、 服務連線、 繫結器及服務所有如何彼此相關：

![以圖表顯示的服務元件如何與相關](bound-services-images/bound-services-02.png "圖，顯示如何將服務元件與彼此相關。")

本指南將說明如何延伸`Service`類別來實作繫結的服務。 它也會說明實作`IServiceConnection`和擴充`Binder`以允許用戶端與服務通訊。 範例應用程式隨附本指南中，其中包含呼叫的單一 Xamarin.Android 專案的方案**[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** 。 這是非常基本的應用程式示範如何實作服務，以及如何將活動連結到它。 繫結的服務有只有一個方法，非常簡單 API `GetFormattedTimestamp`，它會傳回已啟動服務時，會告知使用者字串和多久已執行。 應用程式也可讓使用者以手動方式解除繫結和繫結至服務。

[![在 Android 手機上執行的應用程式的螢幕擷取畫面](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>實作及使用繫結的服務

有三個元件必須實作為了讓 Android 應用程式使用的繫結的服務：

1. **擴充`Service`類別並實作生命週期的回呼方法**&ndash;此類別所包含的程式碼，將會執行將會要求服務的工作。 這將以下更詳細說明。
2. **建立類別實作`IServiceConnection`**  &ndash;此物件包含從服務它已連線至 （或中斷連線） 時，通知用戶端的回呼方法。 服務連線也會提供用戶端可用來直接與服務互動的物件的參考。 這個參考指_繫結器_。
3. **建立類別實作`IBinder`**  &ndash; A_繫結器_實作提供用戶端與服務通訊時所使用的 API。 繫結器可以提供的參考繫結的服務，允許直接叫用方法或繫結器可以提供用戶端應用程式開發介面，用以封裝和隱藏從應用程式繫結的服務。 `IBinder`必須提供必要的程式碼的遠端程序呼叫。 不需要 （或不建議） 若要實作`IBinder`直接介面。 `IBinder` Instead 應用程式應該擴充`Binder`這樣會提供大部分的所需的基底功能`IBinder`。
4. **啟動和繫結至服務**&ndash;一旦建立服務連線、 繫結器，以及服務 Android 應用程式負責啟動服務，並繫結至它。

每個步驟將更多詳細資料中的下列各節中討論。

### <a name="extend-the-service-class"></a>擴充`Service`類別

若要建立使用 Xamarin.Android 服務時，就必須子類別化`Service`和來裝飾的類別與[ `ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)。 屬性可由 Xamarin.Android 建置工具來適當地註冊服務的應用程式中**AndroidManifest.xml**檔案類似活動中，繫結的服務有它本身的生命週期和回呼的方法相關聯在其生命週期中的重大事件。 下面是一些較為常見服務會實作的回呼方法的範例：

* `OnCreate` &ndash; 這個方法會叫用由 Android，因為它具現化服務。 它用來初始化任何變數或物件所需的服務，它的存留期間。 這個方法是一個選擇項目。
* `OnBind` &ndash; 這個方法必須由所有繫結的服務實作。 當第一個用戶端嘗試連線到服務時，它會叫用。 它會傳回的執行個體`IBinder`，讓用戶端可能與服務互動。 服務正在執行，因為`IBinder`物件會用來滿足任何未來的用戶端要求，將繫結至服務。
* `OnUnbind` &ndash; 繫結的所有用戶端具有未繫結時，會呼叫這個方法。 藉由傳回`true`從這個方法中，服務將會稍後呼叫`OnRebind`傳遞至意圖`OnUnbind`當新的用戶端繫結到它。 當服務繼續執行解除繫結之後，您需要執行此動作。 如果最近未繫結的服務也已啟動的服務，就會發生此和`StopService`或`StopSelf`以往被呼叫。 在這種情況下，`OnRebind`允許其用意是為了擷取。 預設會傳回`false`，沒有作用。 選擇性。
* `OnDestroy` &ndash; Android 會終結服務時，會呼叫這個方法。 應該執行任何必要的清除，例如釋放資源，這個方法。 選擇性。

此圖表顯示的繫結的服務金鑰生命週期事件：

![以圖表顯示的順序存留週期方法稱為](bound-services-images/bound-services-01.png "圖，顯示在其中存留週期方法的呼叫的順序。")

下列程式碼片段中的，從 companion 應用程式隨附本指南中，示範如何實作 Xamarin.Android 中的繫結的服務：

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

在範例中，`OnCreate`方法初始化的物件，包含用於擷取和格式設定的用戶端會要求的時間戳記的邏輯。 當第一個用戶端嘗試繫結至服務時，將會叫用 Android`OnBind`方法。 此服務會具現化`TimestampServiceBinder`可讓用戶端存取此執行個體執行中服務的物件。 `TimestampServiceBinder`類別會在下一節中討論。

### <a name="implementing-ibinder"></a>實作 IBinder

如前所述，`IBinder`物件提供用戶端和服務之間的通訊通道。 Android 應用程式不應實作`IBinder`介面， [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder/)應該擴充。 `Binder`類別可提供許多必要的基礎結構也就是必要的封送處理至用戶端服務 （可能以不同的處理序執行） 的繫結器物件。 在大部分情況下，`Binder`子類別是只需幾行程式碼，然後換行至服務的參考。 在此範例中，`TimestampBinder`有公開的屬性`TimestampService`給用戶端：

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

這`Binder`讓您能夠叫用在服務上的公用方法，例如：

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

一次`Binder`已經過擴充，則必須實作`IServiceConnection`連接在一起的所有項目。

### <a name="creating-the-service-connection"></a>建立服務連線

`IServiceConnection`會呈現 | 引入 | 公開 | 連接`Binder`用戶端的物件。 除了實作`IServiceConnection`介面的類別必須擴充`Java.Lang.Object`。 服務連線也應該提供一些用戶端可以存取的方式`Binder`（並因此與繫結的服務通訊）。

此程式碼取自隨附範例專案是一種可能的方式實作`IServiceConnection`:

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

繫結程序的一部分，將會叫用 Android`OnServiceConnected`方法，提供`name`之服務的繫結和`binder`保存服務本身的參考。 在此範例中，服務連接會有兩個屬性，如果用戶端已連線至服務，或不會保留，參考繫結器和布林值旗標的其中一個。

`OnServiceDisconnected`方法才會叫用時意外遺失或中斷用戶端與服務之間的連線。 這個方法可讓用戶端有機會回應到服務中斷。  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>啟動和繫結至服務，以明確的意圖

若要使用繫結的服務，用戶端 （例如活動） 必須具現化物件實作`Android.Content.IServiceConnection`和叫用`BindService`方法。` BindService` 會傳回`true`服務是否繫結至，`false`如果不是。 `BindService` 方法採用三個參數：

* **`Intent`**  &ndash;意圖應該明確地識別要連接到服務。
* **`IServiceConnection`物件**&ndash;這個物件是提供繫結的服務啟動及停止時，通知用戶端的回呼方法的媒介。
* **[`Android.Content.Bind`](https://developer.xamarin.com/api/type/Android.Content.Bind/) 列舉**&ndash;這個參數是一組旗標會由系統用於當繫結物件。 最常使用的值是[ `Bind.AutoCreate` ](https://developer.xamarin.com/api/field/Android.Content.Bind.AutoCreate/)，如果不正在執行，它將自動啟動服務。

下列程式碼片段是如何開始使用明確的意圖活動中的繫結的服務的範例：

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
> 從 Android 5.0 (API level 21) 它是只可以繫結至服務，以明確的意圖。

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>架構服務連線和繫結器的詳細資訊。

某些 OOP purists 可能能取消核准的先前實作`TimestampBinder`類別的違規情形，所以[Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter) ，它的最簡單的形式表示 「 不交談陌生人;僅連絡您的朋友"。 這個特定的實作公開實體`TimestampService`類別的所有用戶端。

嚴格來說，不需要了解用戶端`TimestampService`和公開給用戶端的具象類別可以讓應用程式更容易損毀且難以維護它的存留期。 一種替代方法是使用介面公開`GetFormattedTimestamp()`方法，並透過服務的 proxy 呼叫`Binder`（或可能的服務連接類別）：  

```csharp
public class TimestampBinder : Binder, IGetTimesamp
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

此特定範例允許活動本身的服務上叫用方法：

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```


## <a name="related-links"></a>相關連結

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.Content.Bind](https://developer.xamarin.com/api/type/Android.Content.Bind/)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [Android.Content.IServiceConnection](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)
- [Android.OS.Binder](https://developer.xamarin.com/api/type/Android.OS.Binder/)
- [Android.OS.IBinder](https://developer.xamarin.com/api/type/Android.OS.IBinder)
- [BoundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/BoundServiceDemo/)

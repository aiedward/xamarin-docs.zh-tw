---
title: 在 Xamarin.Android 中繫結的服務
description: 繫結的服務是 Android 提供用戶端-伺服器介面的用戶端 （例如 Android 活動） 可以與之互動。 本指南會討論建立繫結的服務，以及如何在 Xamarin.Android 應用程式中使用它所涉及的主要元件。
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/04/2018
ms.openlocfilehash: c0adee0dae1135bdfd076082e85a471db1cd1ecf
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528555"
---
# <a name="bound-services-in-xamarinandroid"></a>在 Xamarin.Android 中繫結的服務

_繫結的服務是 Android 提供用戶端-伺服器介面的用戶端 （例如 Android 活動） 可以與之互動。本指南會討論建立繫結的服務，以及如何在 Xamarin.Android 應用程式中使用它所涉及的主要元件。_

## <a name="bound-services-overview"></a>繫結的服務概觀

提供的用戶端-伺服器介面直接互動的用戶端與服務的服務指_繫結服務_。  可以有多個同時連線至服務的單一執行個體的用戶端。 繫結的服務和用戶端會彼此隔離。 相反地，Android 會提供一系列中繼管理兩者之間的連線狀態的物件。 此狀態由該物件會實作維護[ `Android.Content.IServiceConnection` ](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)介面。  這個物件是由用戶端，做為參數傳遞[ `BindService` ](https://developer.xamarin.com/api/member/Android.Content.Context.BindService/)方法。 `BindService`是可用於任何[ `Android.Content.Context` ](https://developer.xamarin.com/api/type/Android.Content.Context)物件 （例如活動）。 它是 Android 作業系統啟動服務，並對它繫結的用戶端的要求。 有三種方式可在用戶端可能會繫結至服務，使用`BindService`方法：

* **服務繫結器**&ndash;的服務繫結器是一個類別，實作[ `Android.OS.IBinder` ](https://developer.xamarin.com/api/type/Android.OS.IBinder)介面。 大部分的應用程式會直接實作這個介面，它們將會改為擴充[ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder)類別。 這是最常見的方法，而且適用於服務和用戶端時存在於相同的程序。
* **使用 Messenger** &ndash;這項技術是適用於當服務可能會存在於不同的處理序。 相反地，服務要求會封送處理之間的用戶端和服務經由[ `Android.OS.Messenger` ](https://developer.xamarin.com/api/type/Android.OS.Messenger)。 [ `Android.OS.Handler` ](https://developer.xamarin.com/api/type/Android.OS.Handler)中的服務也會處理建立`Messenger`要求。 這將涵蓋在其他指南。
* **使用 Android 介面定義語言 (AIDL)** &ndash; [AIDL](https://developer.android.com/guide/components/aidl)是一種不涵蓋在本指南中的進階的技術。

一旦用戶端已經繫結至服務，兩者之間的通訊是透過`Android.OS.IBinder`物件。  此物件負責可讓用戶端與服務互動的介面。 您不需要每個 Xamarin.Android 應用程式來實作這個介面，從零開始，Android SDK 提供[ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder)類別處理大部分所需要的物件之間的封送處理程式碼用戶端和服務。

用戶端與服務完成之後，它必須從解除繫結它藉由呼叫`UnbindService`方法。 最後一個用戶端有未從服務繫結，一旦 Android 將會停止並處置繫結的服務。

此圖說明活動、 服務連線、 繫結器及服務所有如何彼此相關：

![圖表，顯示的服務元件如何與相關](bound-services-images/bound-services-02.png "圖表，顯示的服務元件如何互相關聯性。")

本指南會討論如何擴充`Service`類別來實作繫結的服務。 它也會涵蓋實作`IServiceConnection`及擴充`Binder`以允許用戶端與服務通訊。 範例應用程式隨附於本指南中，其中包含的方案使用單一的 Xamarin.Android 專案，稱為 **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** 。 這是非常基本的應用程式示範如何實作服務以及如何將活動繫結到它。 繫結的服務有個簡單的 API，只有一個方法， `GetFormattedTimestamp`，它會傳回字串，會告知使用者，當服務開始時，多久它已經執行。 應用程式也可讓使用者以手動方式解除繫結和繫結至服務。

[![在 Android 手機上執行的應用程式的螢幕擷取畫面](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>實作及使用繫結的服務

有三個元件必須實作為了讓 Android 應用程式取用繫結的服務：

1. **擴充`Service`類別，並實作生命週期的回呼方法**&ndash;此類別所包含的程式碼，將會執行將會要求服務的工作。 這將在下面詳細說明。
2. **建立類別實作`IServiceConnection`**  &ndash;這個介面會提供回呼方法會叫用 android 通知用戶端對服務的連線已變更時，也就是用戶端已連線，或若要中斷連線服務。 服務連接也會提供用戶端可用來直接與服務互動的物件的參考。 此參考指_繫結器_。
3. **建立類別實作`IBinder`**  &ndash; A_繫結器_實作提供的 API，用戶端用來與服務通訊。 繫結器可以提供的參考繫結的服務，允許直接叫用的方法，或繫結器可以提供用戶端封裝，並會隱藏繫結的服務，從應用程式的 API。 `IBinder`必須提供必要的程式碼的遠端程序呼叫。 它不是必要的 （或建議的） 來實作`IBinder`直接介面。 擴充應用程式應該改用`Binder`型別可提供所需的基本功能的大部分`IBinder`。
4. **啟動和繫結至服務** &ndash; Android 應用程式一旦建立服務連線、 繫結器，以及服務會負責啟動服務，並繫結到它。

每個步驟將在下列各節更詳細地討論。

### <a name="extend-the-service-class"></a>擴充`Service`類別

若要建立使用 Xamarin.Android 的服務，就必須以子類別`Service`，來裝飾類別[ `ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)。 Xamarin.Android 建置工具會使用屬性來正確地在應用程式的註冊服務**AndroidManifest.xml**檔案類似活動中，繫結的服務有本身的生命週期和回呼方法與相關聯在其生命週期中的重大事件。 下列清單是一些較為常見的服務會實作的回呼方法的範例：

* `OnCreate` &ndash; 這個方法會叫用 android，因為它具現化服務。 它用來初始化任何變數或所需的服務，它的存留期間的物件。 這個方法是一個選擇項目。
* `OnBind` &ndash; 所有繫結的服務，就必須實作這個方法。 當第一個用戶端嘗試連線到服務時，它會叫用。 它會傳回的執行個體`IBinder`，讓用戶端可能會與服務互動。 只要服務正在執行，`IBinder`物件將用來滿足繫結至服務的任何未來的用戶端要求。
* `OnUnbind` &ndash; 所有繫結的用戶端具有未繫結時，會呼叫這個方法。 藉由傳回`true`服務會稍後呼叫這個方法，從`OnRebind`傳遞至目的`OnUnbind`當新的用戶端繫結到它。 當服務繼續執行未繫結之後，您可以這樣做。 如果最近未繫結的服務也已啟動的服務，會發生並`StopService`或`StopSelf`還沒被呼叫。 在這類案例中，`OnRebind`允許的意圖，以擷取。 預設值會傳回`false`，其不執行任何動作。 選擇性。
* `OnDestroy` &ndash; Android 會終結該服務時，會呼叫此方法。 應該執行任何必要的清除作業，例如釋放資源，這個方法。 選擇性。

繫結的服務金鑰生命週期事件會由圖表所示：

![圖表，顯示在其中的生命週期方法的呼叫的順序](bound-services-images/bound-services-01.png "圖表，顯示在其中的生命週期方法的呼叫的順序。")

下列的程式碼片段中，而從附屬應用程式是本指南中，示範如何在 Xamarin.Android 中實作的繫結的服務：

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

在範例中，`OnCreate`方法會初始化物件，持有用來擷取並格式化會要求用戶端的時間戳記的邏輯。 當第一個用戶端會嘗試將繫結至服務時，將會叫用 Android`OnBind`方法。 此服務會具現化`TimestampBinder`可讓用戶端能夠存取執行中服務的這個執行個體的物件。 `TimestampBinder`類別會在下一節中討論。

### <a name="implementing-ibinder"></a>實作 IBinder

如前所述，`IBinder`物件提供用戶端與服務之間的通訊通道。 Android 應用程式不應該實作`IBinder`介面， [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder/)應該擴充。 `Binder`類別會提供許多必要的基礎結構也就是必要的封送處理至用戶端的服務 （也可能會個別處理序中執行） 的繫結器物件。 在大部分情況下，`Binder`子類別是只需幾行程式碼，並包裝至服務的參考。 在此範例中，`TimestampBinder`的屬性會公開 （expose）`TimestampService`給用戶端：

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

這`Binder`讓您能夠叫用服務; 上的公用方法，例如：

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

一次`Binder`已經過擴充，就必須實作`IServiceConnection`將一切連接在一起。

### <a name="creating-the-service-connection"></a>建立服務連接

`IServiceConnection`將會顯示 | 介紹 | 公開 | 連接`Binder`給用戶端的物件。 除了實作以外`IServiceConnection`介面，類別必須擴充`Java.Lang.Object`。 服務連接也應該提供一些用戶端可以存取的方式`Binder`（並因此與繫結的服務通訊）。

此程式碼取自隨附範例專案是一種可能的方式來實作`IServiceConnection`:

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

繫結程序的一部分，會叫用 Android`OnServiceConnected`方法，提供`name`繫結的服務和`binder`保存服務本身的參考。 在此範例中，服務連接會有兩個屬性，如果用戶端連接至服務或不包含繫結器的參考之布林值旗標的其中一個。

`OnServiceDisconnected`方法才會叫用時用戶端與服務之間的連線意外遺失或損毀。 這個方法可讓用戶端有機會回應服務中斷。  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>啟動並繫結至明確 Intent 服務

若要使用繫結的服務，為用戶端 （例如活動） 必須具現化物件可實作`Android.Content.IServiceConnection`，並叫用`BindService`方法。` BindService` 會傳回`true`服務所繫結，如果`false`如果不是。 `BindService` 方法採用三個參數：

* **`Intent`**  &ndash;意圖應該明確地識別連線到哪個服務。
* **`IServiceConnection`物件**&ndash;這個物件會提供繫結的服務啟動及停止時，通知用戶端的回呼方法的媒介。
* **[`Android.Content.Bind`](https://developer.xamarin.com/api/type/Android.Content.Bind/) 列舉**&ndash;這個參數是系統會使用一組旗標時繫結的物件。 最常使用的值是[ `Bind.AutoCreate` ](https://developer.xamarin.com/api/field/Android.Content.Bind.AutoCreate/)，如果它不正在執行，這會自動啟動服務。

下列程式碼片段是如何在活動中使用明確意圖啟動繫結的服務的範例：

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
> 啟動 Android 5.0 (API level 21) 使用的是，只能夠繫結至服務，以明確的意圖。

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>架構的服務連線並繫結器的詳細資訊。

先前實作的一些 OOP 純化論者可能能取消核准`TimestampBinder`類別的違規情形後，即[Demeter 法則](https://en.wikipedia.org/wiki/Law_of_Demeter)，它的最簡單的形式表示 「 不交談陌生人;僅連絡您的朋友 」。 這個特定的實作公開具象`TimestampService`類別的所有用戶端。

嚴格來說，不需要了解用戶端`TimestampService`和公開給用戶端該實體類別可以讓應用程式，更不可靠且更難維護它的存留期。 替代方法是使用介面會公開`GetFormattedTimestamp()`方法，並透過服務的 proxy 呼叫`Binder`（或可能的服務連接類別）：  

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

這個範例允許服務本身上叫用方法的活動：

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
- [BoundServiceDemo （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/BoundServiceDemo/)

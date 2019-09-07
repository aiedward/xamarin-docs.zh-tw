---
title: Xamarin 中的廣播接收器
description: 本節討論如何使用廣播接收器。
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: 9266d8c4e1723adfb7e5e55dce7ede6d47f6f116
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755484"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Xamarin 中的廣播接收器

_本節討論如何使用廣播接收器。_

## <a name="broadcast-receiver-overview"></a>廣播接收器總覽

_廣播接收器_是一種 Android 元件，可讓應用程式回應 android 作業系統或應用程式[`Intent`](xref:Android.Content.Intent)所廣播的訊息（android）。 廣播會遵循_發佈-訂閱_模型&ndash; ，事件會導致廣播發布，並由那些對事件感興趣的元件接收。

Android 會識別兩種類型的廣播：

- **明確廣播**&ndash;這些類型的廣播是以特定應用程式為目標。 明確廣播的最常見用法是啟動活動。 當應用程式需要撥打電話號碼時的明確廣播範例;它會分派以 Android 上的電話應用程式為目標的意圖，並沿著電話號碼傳遞以撥打電話。 然後，Android 會將意圖路由傳送至電話應用程式。
- **隱含廣播**&ndash;這些廣播會分派至裝置上的所有應用程式。 `ACTION_POWER_CONNECTED`意圖是隱含廣播的範例。 每次 Android 偵測到裝置上的電池充電時，都會發佈此意圖。 Android 會將此意圖路由至所有已註冊此事件的應用程式。

廣播接收器是`BroadcastReceiver`類型的子類別，必須覆[`OnReceive`](xref:Android.Content.BroadcastReceiver.OnReceive*)寫方法。 Android 會在`OnReceive`主執行緒上執行，因此這個方法應該設計成可快速執行。 在中`OnReceive`產生執行緒時應特別小心，因為當方法完成時，Android 可能會終止進程。 如果廣播接收器必須執行長時間執行的工作，建議使用`JobScheduler`或_Firebase 作業發送器_來排程_作業_。 使用工作排程工作將會在個別的指南中討論。

_意圖篩選器_可用來註冊廣播接收器，讓 Android 可以正確地路由傳送訊息。 您可以在執行時間指定意圖篩選（這有時稱為內容登錄的_接收者_或_動態_登錄），也可以在 Android 資訊清單中以靜態方式定義（已_註冊資訊清單的接收者_）。 Xamarin 提供的C#屬性`IntentFilterAttribute`會以靜態方式註冊意圖篩選（這會在本指南稍後詳細討論）。 從 Android 8.0 開始，應用程式不可能以靜態方式註冊隱含廣播。

資訊清單註冊的收件者與內容註冊的接收者之間的主要差異在於，內容登錄的接收者只會在應用程式執行時回應廣播，而資訊清單註冊的接收者則可以回應即使應用程式可能不在執行中，也會進行廣播。  

有兩組 Api 可用於管理廣播接收器和傳送廣播：

1. **`Context`** &ndash; 類別可以用來註冊會回應全系統`Android.Content.Context`事件的廣播接收器。 `Context`也可用來發行全系統的廣播。
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** 這是可透過[Xamarin 支援程式庫 v4 NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)取得的 API。 &ndash; 這個類別是用來讓廣播和廣播接收器在使用它們的應用程式內容中隔離。 此類別可用於防止其他應用程式回應僅限應用程式的廣播，或將訊息傳送至私用接收者。

廣播接收器可能不會顯示對話，因此強烈建議您從廣播接收器內啟動活動。 如果廣播接收器必須通知使用者，則應該發佈通知。

您無法從廣播接收器內系結或啟動服務。 

本指南將說明如何建立廣播接收器，以及如何進行註冊以接收廣播。

## <a name="creating-a-broadcast-receiver"></a>建立廣播接收器

若要在 Xamarin 中建立廣播接收器，應用程式應該將`BroadcastReceiver`類別子類別化，並`BroadcastReceiverAttribute`使用來裝飾它，並`OnReceive`覆寫方法：

```csharp
[BroadcastReceiver(Enabled = true, Exported = false)]
public class SampleReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here.

        String value = intent.GetStringExtra("key");
    }
}
```

當 Xamarin 編譯類別時，它也會使用必要的中繼資料來更新 Androidmanifest.xml，以註冊接收者。 若為靜態註冊的廣播接收器，適當`Enabled`的必須設定為`true`，否則 Android 將無法建立接收者的實例。

`Exported`屬性控制廣播接收器是否可以接收來自應用程式外部的訊息。 如果未明確設定屬性，則屬性的預設值是由 Android 根據是否有任何與廣播接收器相關聯的意圖篩選來決定。 如果廣播接收器至少有一個意圖篩選器，則 Android 會假設該`Exported`屬性為。 `true` 如果沒有與廣播接收器相關聯的意圖篩選器，則 Android 會假設值為`false`。 

方法會接收分派至廣播接收器`Intent`之的參考。 `OnReceive` 這可以讓意圖的傳送者將值傳遞給廣播接收器。

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>使用意圖篩選來靜態註冊廣播接收器

當使用裝飾時`<intent-filter>` ，Xamarin 會在編譯時期將必要的專案加入至 Android 資訊清單。 [`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute) `BroadcastReceiver` 下列程式碼片段是當裝置完成開機時，將會執行的廣播接收器範例（如果使用者已授與適當的 Android 許可權）：

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { Android.Content.Intent.ActionBootCompleted })]
public class MyBootReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Work that should be done when the device boots.     
    }
}
```

您也可以建立會回應自訂意圖的意圖篩選準則。 參考下列範例： 

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { "com.xamarin.example.TEST" })]
public class MySampleBroadcastReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here
    }
}
```

以 Android 8.0 （API 層級26）或更高版本為目標的應用程式，可能無法以靜態方式註冊隱含廣播。 應用程式可能仍會以靜態方式註冊以進行明確的廣播。 有一小部分的隱含廣播清單不受這項限制。 這些例外狀況會在 Android 檔的[隱含廣播例外](https://developer.android.com/guide/components/broadcast-exceptions.html)狀況指南中加以說明。 對隱含廣播感興趣的應用程式必須使用`RegisterReceiver`方法，以動態方式執行此動作。 接下來會說明這一點。

### <a name="context-registering-a-broadcast-receiver"></a>內容-註冊廣播接收器

接收者的內容註冊（也稱為動態登錄）是藉由叫用`RegisterReceiver`方法來執行，而且廣播接收器必須透過呼叫`UnregisterReceiver`方法來取消註冊。 若要避免流失資源，當接收者不再與內容（活動或服務）相關時，請務必將它取消註冊。 例如，服務可能會廣播意圖，以通知活動可以向使用者顯示更新。 當活動開始時，它會註冊這些意圖。 當活動移至背景，而且使用者不會再看到時，它應該取消註冊接收者，因為顯示更新的 UI 已不再顯示。 下列程式碼片段是如何在活動內容中註冊和取消註冊廣播接收器的範例：

```csharp
[Activity(Label = "MainActivity", MainLauncher = true, Icon = "@mipmap/icon")]
public class MainActivity: Activity 
{
    MySampleBroadcastReceiver receiver;

    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        receiver = new MySampleBroadcastReceiver()

        // Code omitted for clarity
    }

    protected override OnResume() 
    {
        base.OnResume();
        RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
        // Code omitted for clarity
    }

    protected override OnPause() 
    {
        UnregisterReceiver(receiver);
        // Code omitted for clarity
        base.OnPause();
    }
}
```

在上述範例中，當活動進入前景時，會使用`OnResume`生命週期方法，註冊將接聽自訂意圖的廣播接收器。 當活動移至背景時， `OnPause()`方法將會取消註冊接收者。

## <a name="publishing-a-broadcast"></a>發佈廣播

廣播可能會發佈到裝置上安裝的所有應用程式建立意圖物件，並使用`SendBroadcast` `SendOrderedBroadcast`或方法分派。  

1. **SendBroadcast 方法**&ndash;此方法有數個執行方式。
   這些方法會將意圖廣播到整個系統。 將以不確定的順序接收意圖的廣播接收器。 這可提供很大的彈性，但也表示其他應用程式可以註冊並接收意圖。 這可能會造成潛在的安全性風險。 應用程式可能需要執行額外的安全性，以防止未經授權的存取。 其中一個可行的解決方案是使用`LocalBroadcastManager` ，它只會在應用程式的私用空間內分派訊息。 此程式碼片段是如何使用其中一個`SendBroadcast`方法分派意圖的其中一個範例：

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    此程式碼片段是使用`Intent.SetAction`方法來識別動作的另一個傳送廣播範例：

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. SendOrderedBroadcast&ndash;這種方法非常類似`Context.SendBroadcast`，差別在於意圖會依照接收者的註冊順序，一次發行至接收者。」。

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

[Xamarin 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)提供名[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html)為的 helper 類別。 `LocalBroadcastManager`適用于不想要從裝置上的其他應用程式傳送或接收廣播的應用程式。 只會在應用程式的內容中發佈訊息，而且只會在向註冊的`LocalBroadcastManager`廣播接收器上發行。 `LocalBroadcastManager` 此程式碼片段是向`LocalBroadcastManager`註冊廣播接收器的範例：

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

裝置上的其他應用程式無法接收以`LocalBroadcastManager`發行的訊息。 此程式碼片段說明如何使用`LocalBroadcastManager`來分派意圖：

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>相關連結

- [BroadcastReceiver API](xref:Android.Content.BroadcastReceiver)
- [RegisterReceiver API](xref:Android.Content.Context.RegisterReceiver*)
- [SendBroadcast API](xref:Android.Content.Context.SendBroadcast*)
- [UnregisterReceiver API](xref:Android.Content.Context.UnregisterReceiver*)
- [意圖 API](xref:Android.Content.Intent)
- [下文 intentfilter API](xref:Android.App.IntentFilterAttribute)
- [LocalBroadcastManager （Android 檔）](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Android 指南中的本機通知](~/android/app-fundamentals/notifications/local-notifications.md)
- [Android 支援程式庫 v4 （NuGet）](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

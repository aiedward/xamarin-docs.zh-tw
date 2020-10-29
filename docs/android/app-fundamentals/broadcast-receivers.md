---
title: Xamarin 中的廣播接收者
description: 本節討論如何使用廣播接收器。
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: 9e702bdb31563588d20b9cd70e35b67aefe67e0c
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928369"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Xamarin 中的廣播接收者

_本節討論如何使用廣播接收器。_

## <a name="broadcast-receiver-overview"></a>廣播接收器總覽

_廣播接收器_ 是一個 android 元件，可讓應用程式回應 android [`Intent`](xref:Android.Content.Intent) 作業系統或應用程式所廣播 (android) 的訊息。 廣播會遵循 _發佈-訂閱_ 模型， &ndash; 事件會導致在事件中有興趣的元件發佈及接收廣播。

Android 會識別兩種類型的廣播：

- **明確廣播** &ndash; 這些類型的廣播以特定的應用程式為目標。 明確廣播最常見的用法是啟動活動。 當應用程式需要撥電話號碼時的明確廣播範例;它會分派以 Android 上的電話應用程式為目標的意圖，並將撥打的電話號碼傳遞給您。 Android 接著會將意圖路由傳送至 Phone 應用程式。
- **隱含廣播** &ndash; 這些廣播會分派給裝置上的所有應用程式。 隱含廣播的範例是 `ACTION_POWER_CONNECTED` 意圖。 每次 Android 偵測到裝置上的電池充電時，即會發佈此意圖。 Android 會將此意圖路由傳送至此事件已註冊的所有應用程式。

廣播接收器是型別的子類別 `BroadcastReceiver` ，必須覆寫 [`OnReceive`](xref:Android.Content.BroadcastReceiver.OnReceive*) 方法。 Android 將會 `OnReceive` 在主執行緒上執行，因此此方法應該設計為可快速執行。 在中產生執行緒時，請務必小心， `OnReceive` 因為 Android 可能會在方法完成時終止進程。 如果廣播接收器必須執行長時間執行的工作，建議使用 _job_ `JobScheduler` 或 _Firebase 作業發送器_ 來排程作業。 使用工作排程工作將會在個別的指南中討論。

_意圖篩選準則_ 可用來註冊廣播接收器，讓 Android 可以正確地路由傳送訊息。 您可以在執行時間指定意圖篩選準則 (這有時稱為 _內容註冊的接收器_ 或 _動態_ 登錄) 或可在 Android 資訊清單中以靜態方式定義， (由 _資訊清單註冊的接收者_ ) 。 Xamarin 提供了 c # 屬性， `IntentFilterAttribute` 會以靜態方式註冊意圖篩選 (這將在本指南稍後的) 中更詳細地討論。 從 Android 8.0 開始，應用程式無法以靜態方式註冊隱含廣播。

資訊清單註冊的接收者和內容註冊的接收者之間的主要差異在於，內容註冊的接收者只會在應用程式執行時回應廣播，而資訊清單註冊的接收者可以回應廣播，即使應用程式可能未執行。  

有兩組 Api 可用於管理廣播接收器和傳送廣播：

1. **`Context`**&ndash; `Android.Content.Context` 類別可以用來註冊將回應全系統事件的廣播接收器。 `Context`也可以用來發佈全系統的廣播。
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))**&ndash;這是可透過 [Xamarin 支援程式庫 v4 NuGet 套件](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)取得的 API。 這個類別是用來讓廣播和廣播接收器在使用它們的應用程式內容中隔離。 此類別有助於防止其他應用程式回應僅限應用程式的廣播或傳送訊息給私人接收者。

廣播接收器可能不會顯示對話方塊，而且強烈建議您從廣播接收器內啟動活動。 如果廣播接收器必須通知使用者，則應該發佈通知。

您無法從廣播接收器內系結或啟動服務。

本指南將說明如何建立廣播接收器，以及如何註冊，讓它可以接收廣播。

## <a name="creating-a-broadcast-receiver"></a>建立廣播接收器

若要在 Xamarin 中建立廣播接收器，應用程式應該將類別類別化 `BroadcastReceiver` 、使用來裝飾該類別， `BroadcastReceiverAttribute` 然後覆寫 `OnReceive` 方法：

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

當 Xamarin 編譯類別時，它也會使用必要的中繼資料來更新 AndroidManifest，以註冊接收者。 若為靜態註冊的廣播接收器，則 `Enabled` 必須將正確設定為 `true` ，否則 Android 將無法建立接收者的實例。

`Exported`屬性控制廣播接收器是否可以從應用程式外部接收訊息。 如果未明確設定屬性，則屬性的預設值取決於是否有任何意圖篩選與廣播接收器相關聯，以 Android 為基礎。 如果廣播接收器至少有一個意圖篩選，則 Android 會假設該 `Exported` 屬性為 `true` 。 如果沒有與廣播接收器相關聯的意圖篩選，則 Android 會假設值為 `false` 。

`OnReceive`方法 `Intent` 會接收已分派給廣播接收器之的參考。 這可讓意圖的寄件者將值傳遞給廣播接收器。

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>使用意圖篩選以靜態方式註冊廣播接收器

當 `BroadcastReceiver` 使用裝飾時 [`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute) ，Xamarin 會 `<intent-filter>` 在編譯時期將必要的元素新增至 Android 資訊清單。 下列程式碼片段是在裝置完成開機時，將會執行的廣播接收器範例 (如果使用者) 授與適當的 Android 許可權：

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

> [!NOTE]
> 在 Android 8.0 (API 26 和更新版本) 中，Google 在使用者未直接與其互動的情況下，會 [限制](https://developer.android.com/about/versions/oreo/background) 應用程式的功能。 這些限制會影響背景服務和隱含廣播接收器（例如） `Android.Content.Intent.ActionBootCompleted` 。 由於這些限制，您可能會 `Boot Completed` 在較新版本的 Android 上註冊廣播接收器時遇到問題。 如果是這種情況，請注意這些限制不適用於可從廣播接收器呼叫的前景服務。

您也可以建立會回應自訂意圖的意圖篩選。 請考慮下列範例：

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

以 Android 8.0 (API 層級 26) 或更高版本為目標的應用程式，可能無法以靜態方式註冊隱含廣播。 應用程式仍然可以靜態方式註冊明確的廣播。 有一小部分的隱含廣播清單免除了這項限制。 這些例外狀況會在 Android 檔的「 [隱含廣播例外](https://developer.android.com/guide/components/broadcast-exceptions.html) 狀況指南」中說明。 對隱含廣播感興趣的應用程式必須以動態方式使用 `RegisterReceiver` 方法來進行。 接下來會說明這一點。

### <a name="context-registering-a-broadcast-receiver"></a>Context-Registering 廣播接收器

內容註冊 (也稱為「動態登錄」，接收者的動態註冊) 是藉由 `RegisterReceiver` 叫用方法來執行，而且廣播接收器必須透過呼叫方法來取消註冊 `UnregisterReceiver` 。 若要防止資源流失，當接收者不再與活動或服務) 的內容 (相關時，請務必取消註冊。 例如，服務可能會廣播意圖來通知活動，可向使用者顯示更新。 當活動開始時，它會註冊這些意圖。 當活動移至背景，且使用者不再看見時，它應該取消註冊接收者，因為顯示更新的 UI 將不再顯示。 下列程式碼片段是如何在活動內容中註冊和取消註冊廣播接收器的範例：

```csharp
[Activity(Label = "MainActivity", MainLauncher = true, Icon = "@mipmap/icon")]
public class MainActivity: Activity
{
    MySampleBroadcastReceiver receiver;

    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        receiver = new MySampleBroadcastReceiver();

        // Code omitted for clarity
    }

    protected override void OnResume()
    {
        base.OnResume();
        RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
        // Code omitted for clarity
    }

    protected override void OnPause()
    {
        UnregisterReceiver(receiver);
        // Code omitted for clarity
        base.OnPause();
    }
}
```

在上述範例中，當活動進入前景時，它將會註冊廣播接收器，以使用生命週期方法來接聽自訂意圖 `OnResume` 。 當活動進入背景時， `OnPause()` 方法將會取消註冊接收者。

## <a name="publishing-a-broadcast"></a>發佈廣播

廣播可以發佈至裝置上所安裝的所有應用程式，並建立意圖物件，並使用 `SendBroadcast` 或方法分派 `SendOrderedBroadcast` 。  

1. **SendBroadcast 方法** &ndash; 此方法有幾個執行。
   這些方法會將意圖廣播至整個系統。 將以不定順序接收意圖的廣播接收者。 這可提供極大的彈性，但也表示其他應用程式可能會註冊及接收意圖。 這可能會造成潛在的安全性風險。 應用程式可能需要執行額外的安全性，以防止未經授權的存取。 其中一個可能的解決方案是使用， `LocalBroadcastManager` 它只會在應用程式的私用空間內分派訊息。 此程式碼片段是如何使用其中一種方法分派意圖的範例 `SendBroadcast` ：

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    此程式碼片段是使用方法來傳送廣播的另一個範例  `Intent.SetAction` ，以識別該動作：

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **SendOrderedBroadcast** &ndash; 這種方法非常類似 `Context.SendBroadcast` ，差別在於會將意圖一次發行至接收者，並依照接收者註冊的順序。

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

[Xamarin 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)提供稱為的 helper 類別 [`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html) 。 適用 `LocalBroadcastManager` 于不想要從裝置上的其他應用程式傳送或接收廣播的應用程式。 `LocalBroadcastManager`只會將訊息發佈到應用程式的內容中，而且只會發佈到已向註冊的廣播接收者 `LocalBroadcastManager` 。 此程式碼片段是向註冊廣播接收器的範例 `LocalBroadcastManager` ：

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

裝置上的其他應用程式無法接收隨一起發行的訊息 `LocalBroadcastManager` 。 此程式碼片段說明如何使用下列方法分派意圖 `LocalBroadcastManager` ：

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>相關連結

- [>broadcastreceiver API](xref:Android.Content.BroadcastReceiver)
- [RegisterReceiver API](xref:Android.Content.Context.RegisterReceiver*)
- [SendBroadcast API](xref:Android.Content.Context.SendBroadcast*)
- [UnregisterReceiver API](xref:Android.Content.Context.UnregisterReceiver*)
- [意圖 API](xref:Android.Content.Intent)
- [IntentFilter API](xref:Android.App.IntentFilterAttribute)
- [LocalBroadcastManager (Android 檔) ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Android 中的本機通知](~/android/app-fundamentals/notifications/local-notifications.md) 指南
- [Android 支援程式庫 v4 (NuGet) ](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

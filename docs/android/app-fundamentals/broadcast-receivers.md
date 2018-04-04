---
title: 廣播 Xamarin.Android 接收器
description: 本節討論如何使用廣播的接收者。
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/19/2018
ms.openlocfilehash: 75d42da4ba01aaefded0081da02b8e1651695f46
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>廣播 Xamarin.Android 接收器

_本節討論如何使用廣播的接收者。_


## <a name="broadcast-receiver-overview"></a>廣播的接收者概觀

A_廣播的接收者_是 Android 的元件，可讓應用程式回應訊息 (Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)) 的廣播 Android 作業系統或應用程式。 請依照下列廣播_發行-訂閱_模型&ndash;事件造成廣播来發行和收到的事件有興趣的元件。 

Android 識別廣播的兩種類型：

* **明確的廣播**&ndash;廣播的這些類型為目標的特定應用程式。 明確的常見用法是廣播的啟動活動。 舉例來說，當應用程式需要撥打電話號碼; 明確廣播它會分派設為目標在 Android 和傳遞的電話號碼沿著撥打的電話應用程式的意圖。 Android 然後會將目的路由到 Phone 應用程式。
* **隱含 broadcase** &ndash;這些廣播會發送給所有裝置上的應用程式。 舉例來說，隱含的廣播是`ACTION_POWER_CONNECTED`意圖。 Android 偵測到在裝置上的電池正在充電每次發行此意圖。 Android 會路由此意圖，此事件的已註冊的所有應用程式。

廣播的接收器是子類別的`BroadcastReceiver`類型也必須覆寫[ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/)方法。 將執行 android`OnReceive`主執行緒，因此這個方法應該設計成可快速執行。 應該小心繁衍 （spawn） 中的執行緒時`OnReceive`因為 Android 可能會在方法完成時終止處理程序。 如果廣播的收件者必須執行長時間執行的工作，則建議您排程_作業_使用`JobScheduler`或_Firebase 作業發送器_。 排程工作與工作將個別的指南中討論。

_意圖篩選_用來註冊廣播的接收器，以便 Android 適當地路由傳送訊息。 意圖篩選條件可以指定在執行階段 (這有時候稱為_內容註冊接收者_或_動態登錄_) 或可以在 Android 資訊清單 (靜態定義_資訊清單登錄接收者_)。 Xamarin.Android 提供 C# 屬性， `IntentFilterAttribute`，，將會以靜態方式註冊意圖 （這會討論更多詳細資料，稍後在本指南中） 的篩選器。 從 Android 8.0 開始，它不可能的應用程式以靜態方式註冊隱含的廣播。

資訊清單已註冊的收件者與內容註冊接收者之間的主要差異是，內容已註冊的接收者將只回應廣播當應用程式正在執行，而資訊清單登錄的接收者可以回應廣播即使可能不在執行應用程式。  

管理廣播的接收者和傳送廣播有兩個 Api 集：

1. **`Context`** &ndash; `Android.Content.Context`類別可以用來註冊廣播的接收者所將回應系統事件。 `Context`也會用來發行全系統的廣播。
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; 這是可透過 API [Xamarin 支援程式庫 v4 NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。 這個類別用來保留廣播和隔離的應用程式正在使用的內容中的廣播的接收者。 這個類別可用於防止其他應用程式回應應用程式專用的廣播，或將訊息傳送至私用的接收者。

廣播的接收者可能不會顯示對話方塊，而且強烈建議您不要啟動廣播接收者內的活動。 如果廣播的接收者必須通知使用者，它就應該發行通知。

您不可能將繫結至或啟動從內廣播的收件者的服務。 

本指南將涵蓋如何建立廣播的接收器以及如何註冊它，使它可能會收到的廣播。

## <a name="creating-a-broadcast-receiver"></a>建立廣播的接收器

若要建立 Xamarin.Android 廣播的接收者，應用程式應該子類別`BroadcastReceiver`類別，可以使用裝飾`BroadcastReceiverAttribute`，並覆寫`OnReceive`方法：

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

當 Xamarin.Android 編譯類別時，它也會更新與必要中繼資料註冊接收者 AndroidManifest。 用於靜態登錄的廣播接收器，`Enabled`正確必須設為`true`，否則 Android 無法建立收件者的執行個體。
 
`Exported`屬性會控制廣播的收件者是否能夠接收來自外部應用程式訊息。 如果未明確設定的屬性，屬性的預設值取決於 Android 根據是否有任何意圖篩選廣播的收件者相關聯。 如果沒有至少一個廣播接收者意圖篩選則 Android 會假設`Exported`屬性是`true`。 如果相關聯廣播的接收者，沒有意圖篩選則 Android 將會假設值是`false`。 

`OnReceive`方法會接收的參考`Intent`，已被分派至廣播的收件者。 如此便可利用試圖將值傳遞至廣播的收件者的寄件者。

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>以靜態方式將廣播接收者向意圖篩選器

時`BroadcastReceiver`以裝飾[ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)，Xamarin.Android 會新增必要`<intent-filter>`Android 項目在編譯時期資訊清單。 下列程式碼片段是廣播接收器時裝置已完成開機 （如果使用者所授與適當的 Android 權限） 執行的範例：

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

它也可建立自訂的對應方式將回應意圖篩選器。 參考下列範例： 

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

Android 8.0 (API level 26) 為目標的應用程式或更高版本可能無法以靜態方式註冊隱含的廣播。 應用程式可能仍會以靜態方式註冊明確的廣播。 沒有隱含免套用此限制的廣播的小型清單。 這些例外狀況中所述[隱含廣播的例外狀況](https://developer.android.com/guide/components/broadcast-exceptions.html)Android 文件中的指南。 隱含的廣播感興趣的應用程式必須執行以動態使用`RegisterReceiver`方法。 這是接下來描述。  

### <a name="context-registering-a-broadcast-receiver"></a>內容註冊廣播的收件者 

內容的註冊 （也稱為動態登錄） 的收件者藉由叫用`RegisterReceiver`方法和廣播的收件者必須藉由呼叫取消註冊`UnregisterReceiver`方法。 若要避免資源流失，請務必取消登錄不再相關的內容 （活動或服務） 的收件者。 例如，服務可能廣播試圖通知更新可供顯示給使用者的活動。 當活動開始時，它會登錄這些對應方式。 當活動移至背景，而且不會再顯示給使用者，它應該取消註冊收件者因為顯示更新的 UI 不再顯示。 下列程式碼片段是活動的如何註冊及取消註冊廣播的接收者內容中的範例：

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

在上述範例中，當活動進入前景，它將會註冊廣播的接收器，以便使用自訂的意圖會接聽`OnResume`存留週期方法。 當活動移動到背景`OnPause()`方法會將取消登錄收件者。

## <a name="publishing-a-broadcast"></a>發佈廣播

廣播可發行至所有應用程式建立的意圖物件並分派它以在裝置上安裝`SendBroadcast`或`SendOrderedBroadcast`方法。  

1. **Context.SendBroadcast 方法**&ndash;數種方式實作這個方法。
   這些方法會廣播到整個系統意圖。 廣播的接收者 thatwill 收到意圖以不定順序。 這提供極大的彈性，但表示很可能讓其他應用程式註冊及接收的意圖。 這可能會造成潛在的安全性風險。 應用程式可能需要實作加法安全性可防止未經授權的存取。 一個可行的解決方案是使用`LocalBroadcastManager`這只會發送的應用程式私用的空間內的訊息。 此程式碼片段是一個範例如何分派使用其中一種用途`SendBroadcast`方法：

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   intent.PutExtra("key", "value");
   SendBroadcast(intent);
   ```

    這個程式碼片段是另一個範例使用傳送廣播`Intent.SetAction`方法來識別此動作：
    
    ```csharp 
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```
   
2. **Context.SendOrderedBroadcast** &ndash;這種方法非常類似於`Context.SendBroadcast`，兩者的差異在於意圖將會發行的一次接收者，recievers 已註冊的順序。
   
### <a name="localbroadcastmanager"></a>LocalBroadcastManager

[Xamarin 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)提供呼叫的協助程式類別[ `LocalBroadcastManager` ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html)。 `LocalBroadcastManager`適用於不想要傳送或廣播接收來自其他應用程式在裝置上的應用程式。 `LocalBroadcastManager`才會發佈應用程式的內容中的訊息。 在裝置上的其他應用程式無法接收訊息與已發行`LocalBroadcastManager`。 

此程式碼片段示範如何分派意圖使用`LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
intent.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
``` 

## <a name="related-links"></a>相關連結

- [BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Context.RegisterReceiver](https://developer.xamarin.com/api/member/Android.Content.Context.RegisterReceiver/p/Android.Content.BroadcastReceiver/Android.Content.IntentFilter/System.String/Android.OS.Handler/)
- [Context.SendBroadcast](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/)
- [Context.UnregisterReceiver](https://developer.xamarin.com/api/member/Android.Content.Context.UnregisterReceiver/p/Android.Content.BroadcastReceiver/)
- [意圖](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [IntentFilter](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
- [LocalBroadcastManager](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [在 Android 中的本機通知](~/android/app-fundamentals/notifications/local-notifications.md)
- [Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

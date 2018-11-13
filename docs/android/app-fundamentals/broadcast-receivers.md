---
title: 在 Xamarin.Android 中的廣播的接收器
description: 本節討論如何使用廣播接收器。
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/20/2018
ms.openlocfilehash: a411d4d85877c9868ec49f92b53ca8d7a81f9959
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528529"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>在 Xamarin.Android 中的廣播的接收器

_本節討論如何使用廣播接收器。_

## <a name="broadcast-receiver-overview"></a>廣播的接收器概觀

A_廣播的接收器_是 Android 元件，可讓應用程式回應訊息 (Android [ `Intent` ](https://developer.xamarin.com/api/type/Android.Content.Intent/)) 的廣播 Android 作業系統或應用程式。 請依照下列廣播_發佈-訂閱_模型&ndash;事件造成的廣播發行，並收到這些事件感興趣的元件。

Android 識別兩種類型的廣播：

* **明確的廣播**&ndash;廣播的這些類型為目標的特定應用程式。 明確的常見用法是廣播的啟動活動。 舉例來說，當應用程式需要撥打的電話號碼; 明確廣播它會分派目標 Android 和傳遞的電話號碼以及撥接電話應用程式意圖。 Android 電話應用程式，就會接著路由目的。
* **隱含的廣播**&ndash;這些廣播會分派至裝置上的所有應用程式。 隱含的廣播的範例是`ACTION_POWER_CONNECTED`意圖。 Android 會偵測裝置上的電池在充電時，每次發行此意圖。 Android 會在本項目中已註冊的所有應用程式路由此意圖。

廣播的接收器是子類別的`BroadcastReceiver`型別也必須覆寫[ `OnReceive` ](https://developer.xamarin.com/api/member/Android.Content.BroadcastReceiver.OnReceive/p/Android.Content.Context/Android.Content.Intent/)方法。 Android 將會執行`OnReceive`主執行緒，因此這個方法應該設計成快速執行。 繁衍執行緒中的時，就應該特別注意`OnReceive`因為 Android 方法完成時，可能會終止處理序。 如果廣播的接收器必須執行長時間執行的工作，則建議您排定_作業_使用`JobScheduler`或_Firebase 作業發送器_。 排程工作與作業將會討論在個別的指南。

_意圖篩選_用來註冊廣播的接收器，使之 Android 可以適當地路由傳送訊息。 意圖篩選可以指定在執行階段 (這有時候稱為_內容註冊接收者_或_動態註冊_)，或者可以在 Android 資訊清單 (以靜態方式定義_資訊清單註冊接收者_)。 Xamarin.Android 提供C#屬性， `IntentFilterAttribute`，會以靜態方式將註冊意圖篩選 （這會討論在本指南稍後的更多詳細資料）。 從 Android 8.0 開始，就無法以靜態方式進行隱含的廣播註冊應用程式。

資訊清單註冊的收件者和內容登錄的接收者之間的主要差異是，內容登錄的接收器會只回應廣播時應用程式正在執行，而資訊清單註冊接收者可以回應廣播即使可能不在執行應用程式。  

用於管理廣播的接收器和傳送廣播，有兩個 Api 集：

1. **`Context`** &ndash; `Android.Content.Context`類別可以用來註冊回應系統事件的廣播的接收器。 `Context`也會用來發行整個系統的廣播。
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; 這是一種 API 可透過[Xamarin 支援程式庫 v4 NuGet 套件](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)。 這個類別用來保存廣播，並使用它們的應用程式的內容中隔離的廣播的接收器。 這個類別可用於防止其他應用程式回應應用程式專用的廣播，或將訊息傳送至私用的接收者。

廣播的接收器可能不會顯示對話方塊，而且強烈建議您不要開始廣播接收器內的活動。 如果廣播的接收器必須通知使用者，它就應該發行通知。

您不可以將繫結至或開始廣播接收器內的服務。 

本指南將討論如何建立廣播的接收器以及如何註冊它，因此它可能會收到廣播。

## <a name="creating-a-broadcast-receiver"></a>建立廣播的接收器

若要建立廣播的接收器在 Xamarin.Android 中，應用程式應該子類別`BroadcastReceiver`類別中，它與裝飾`BroadcastReceiverAttribute`，並覆寫`OnReceive`方法：

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

當 Xamarin.Android 編譯類別時，它也會更新與必要的中繼資料註冊接收器 AndroidManifest。 靜態註冊的廣播接收器，如`Enabled`正確必須設為`true`，否則 Android 無法建立收件者的執行個體。
 
`Exported`屬性可讓您控制廣播的接收器是否可以接收來自外部應用程式訊息。 如果未明確設定屬性，屬性的預設值取決於 Android 基礎，如果有任何意圖篩選相關聯的廣播接收器。 如果沒有至少一個廣播接收器意圖篩選，則 Android 將會假設`Exported`屬性是`true`。 如果廣播接收器中，相關聯沒有意圖篩選器，則 Android 會假設值是`false`。 

`OnReceive`方法會接收的參考`Intent`，分派至廣播接收器。 如此一來，有可能的寄件者的意圖，以將值傳遞至廣播接收器。

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>以靜態方式向意圖篩選中的廣播接收器

當`BroadcastReceiver`裝飾[ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)，Xamarin.Android 會加入必要`<intent-filter>`元素 android 資訊清單在編譯時期。 下列程式碼片段是廣播接收器會在裝置已完成開機 （如果有適當的 Android 權限所授與使用者） 時執行的範例：

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

它也可建立自訂的意圖會回應的意圖篩選器。 參考下列範例： 

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

目標 Android 8.0 （API 層級 26） 的應用程式或更高版本可能不靜態暫存器用於隱含的廣播。 應用程式可能仍然以靜態方式註冊明確的廣播。 沒有免套用此限制的隱含廣播的小型清單。 這些例外狀況所述[隱含廣播的例外狀況](https://developer.android.com/guide/components/broadcast-exceptions.html)Android 文件中的指南。 隱含的廣播感興趣的應用程式必須執行以動態使用`RegisterReceiver`方法。 這會在接下來說明。

### <a name="context-registering-a-broadcast-receiver"></a>內容註冊廣播的接收器

內容註冊 （也稱為動態註冊） 的收件者藉由叫用`RegisterReceiver`方法和廣播的接收器必須取消註冊呼叫`UnregisterReceiver`方法。 若要避免遺漏的資源，請務必不再相關內容 （活動或服務） 時，取消註冊的接收者。 例如，服務可能廣播通知更新可供顯示給使用者的活動之意圖。 當活動啟動時，它會登錄這些意圖。 當活動就會移到背景，並不會再顯示給使用者，它應該取消註冊接收者因為顯示更新的 UI 不再可見。 下列程式碼片段是如何註冊和取消註冊活動的內容中的廣播的接收器的範例：

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

在上述範例中，當活動放置到前景，其將註冊使用自訂的意圖會接聽的廣播的接收器`OnResume`生命週期方法。 當活動進入背景，`OnPause()`方法將會移除登錄的接收器。

## <a name="publishing-a-broadcast"></a>發佈廣播

廣播可能會發佈至所有的應用程式建立意圖物件並分派它以在裝置上安裝`SendBroadcast`或`SendOrderedBroadcast`方法。  

1. **Context.SendBroadcast 方法**&ndash;有數個實作這個方法。
   這些方法會廣播到整個系統的意圖。 將會以不定順序收到意圖的廣播的接收器。 這提供極大的彈性，但表示它會讓其他應用程式，可以註冊並接收其目的。 這可能會造成潛在的安全性風險。 應用程式可能需要實作新增安全性以防止未經授權的存取。 一個可行的解決方案是使用`LocalBroadcastManager`這只會分派在應用程式的私用空間內的訊息。 此程式碼片段是一個範例如何分派使用其中一種意圖`SendBroadcast`方法：

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    此程式碼片段是另一個範例使用傳送廣播`Intent.SetAction`來識別此動作的方法：

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context.SendOrderedBroadcast** &ndash;這種方法非常類似於`Context.SendBroadcast`，與，意圖將會發行的一次接收者、 接收者是否已註冊的順序的差異。

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

[Xamarin 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)提供 helper 類別，稱為[ `LocalBroadcastManager` ](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html)。 `LocalBroadcastManager`適用於不想要傳送或接收廣播來自其他應用程式在裝置上的應用程式。 `LocalBroadcastManager`才會發佈訊息內容的應用程式，並且只會針對已向這些廣播接收器中`LocalBroadcastManager`。 此程式碼片段是註冊廣播的接收器使用的範例`LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

在裝置上的其他應用程式無法接收的訊息，使用發佈`LocalBroadcastManager`。 此程式碼片段示範如何分派意圖使用`LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>相關連結

- [BroadcastReceiver API](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Context.RegisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.RegisterReceiver/p/Android.Content.BroadcastReceiver/Android.Content.IntentFilter/System.String/Android.OS.Handler/)
- [Context.SendBroadcast API](https://developer.xamarin.com/api/member/Android.Content.Context.SendBroadcast/p/Android.Content.Intent/)
- [Context.UnregisterReceiver API](https://developer.xamarin.com/api/member/Android.Content.Context.UnregisterReceiver/p/Android.Content.BroadcastReceiver/)
- [意圖的 API](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [IntentFilter API](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)
- [LocalBroadcastManager （Android 文件）](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [在 Android 中的本機通知](~/android/app-fundamentals/notifications/local-notifications.md)指南
- [Android 支援 v4 程式庫 (NuGet)](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

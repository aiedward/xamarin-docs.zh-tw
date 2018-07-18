---
title: 建立服務
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 05/03/2018
ms.openlocfilehash: 00785ad161f5f05fd70b059bb0a3f1c8d6c31f97
ms.sourcegitcommit: daa089d41cfe1ed0456d6de2f8134cf96ae072b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33850830"
---
# <a name="creating-a-service"></a>建立服務

Xamarin.Android 服務必須遵守 Android 服務的兩個構築一道不可侵犯的規則：

* 它們必須延伸[ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/)。
* 必須以裝飾[ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)。

Android 服務的另一個需求就是它們必須登錄在**AndroidManifest.xml**並提供唯一的名稱。 Xamarin.Android 會自動登錄服務資訊清單中在建置時所需的 XML 屬性。

此程式碼片段是符合這兩項需求的 Xamarin.Android 中建立服務的最簡單的範例：  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

在編譯時期 Xamarin.Android 會將下列 XML 項目，以便註冊服務**AndroidManifest.xml** （請注意 Xamarin.Android 產生隨機的服務名稱）：

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

可與其他 Android 應用程式共用服務_匯出_它。 這是藉由設定`Exported`屬性`ServiceAttribute`。 匯出服務時`ServiceAttribute.Name`屬性也應該提供有意義的公用名稱的服務設定。 這個程式碼片段示範如何匯出和服務名稱：

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

**AndroidManifest.xml**這個服務項目然後看起來像這樣：

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

服務具有自己的生命週期，與建立服務時叫用的回呼方法。 叫用的方法完全取決於服務的型別。 啟動的服務必須實作不同的存留週期方法比繫結的服務，而混合式服務必須實作的回呼方法啟動的服務和繫結的服務。 這些方法的所有成員的`Service`類別，則服務啟動時，將會決定哪些存留週期方法將會叫用。 這些存留週期方法將稍後詳細討論。

根據預設，服務會啟動在 Android 應用程式相同的程序。 您可在自己的處理序啟動服務，藉由設定`ServiceAttribute.IsolatedProcess`屬性設定為 true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

下一個步驟是檢查如何啟動服務，然後移至檢驗如何實作三種不同類型的服務。

> [!NOTE]
> 服務的執行緒上執行 UI，所以如果任何工作是要執行的封鎖 UI，服務必須使用執行緒來執行的工作。

## <a name="starting-a-service"></a>正在啟動服務

Android 在啟動服務的最基本方式是分派`Intent`其中包含中繼資料，找出哪些服務應該要啟動。 有兩個不同的樣式的對應方式可以用來啟動服務：

-   **明確的意圖** &ndash; _明確意圖_會識別完全哪些服務應該用來完成指定的動作。 明確的意圖可以想成字母具有特定的地址。Android 會明確識別服務路由的意圖。 這個程式碼片段是一個範例使用明確的意圖啟動服務，稱為`DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **隱含的意圖**&ndash;這種類型的意圖鬆散識別的使用者想要執行，但確切的服務，以完成該動作未知的動作。 隱含的意圖可以想像因為字母的收件者的 「 To Whom It 可能問題 … 」。
    Android 會檢查內容的意圖，並判斷是否有現有的服務符合目的。

    _意圖篩選_用來協助符合隱含的目的與已註冊的服務。 意圖的篩選條件是 XML 項目加入至**AndroidManifest.xml**其中包含必要中繼資料，可協助符合服務，以隱含的意圖。

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

如果 Android 有多個可能的相符項目為隱含的意圖，它可能會要求使用者選取的元件來處理動作：

![螢幕擷取畫面去除混淆對話方塊的](images/creating-a-service-01.png "去除混淆對話方塊螢幕擷取畫面")

> [!IMPORTANT]
> 從 Android 5.0 (AP level 21) 開始隱含的意圖不能用來啟動服務。

如果可行，應用程式應該使用明確的對應方式來啟動服務。 隱含的意圖不要求特定的服務啟動&ndash;它是安裝在裝置上某些服務的要求來處理要求。 這項模稜兩可的要求可能會導致錯誤的服務處理的要求或另一個應用程式不必要地啟動 （這會增加的裝置上的資源不足的壓力）。

分派意圖的方式取決於服務的型別，以及將每個服務類型的特定指南中稍後詳細討論。


### <a name="creating-an-intent-filter-for-implicit-intents"></a>建立隱含的對應方式的意圖篩選器

若要與隱含意圖關聯服務，Android 應用程式必須提供一些中繼資料來識別服務的功能。 此中繼資料由_意圖篩選_。 意圖篩選包含一些資訊，例如動作或資料，必須要有意圖啟動的服務中的類型。 Xamarin.Android，在中註冊意圖篩選**AndroidManifest.xml**而將服務[ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)。 比方說，下列程式碼會加入相關聯動作的意圖篩選器`com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

這會導致項目包含在**AndroidManifest.xml**檔案&ndash;封裝於應用程式的方式類似於下列範例中的項目：

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

Xamarin.Android 服務的方式的基本概念，讓我們來檢查不同的子類型的更詳細的服務。


## <a name="related-links"></a>相關連結

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)

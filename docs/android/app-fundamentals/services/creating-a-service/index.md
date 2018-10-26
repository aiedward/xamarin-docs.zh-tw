---
title: 建立服務
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: 24d86827ab93dcf7dfc4da39c4a03a0a2805f332
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107426"
---
# <a name="creating-a-service"></a>建立服務

Xamarin.Android 服務必須遵循 Android 服務的兩個構築一道不可侵犯的規則：

* 它們必須延伸[ `Android.App.Service` ](https://developer.xamarin.com/api/type/Android.App.Service/)。
* 必須使用裝飾[ `Android.App.ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)。

Android 服務另一個需求就是他們必須註冊在**AndroidManifest.xml**和提供唯一的名稱。 Xamarin.Android 會自動註冊中的服務資訊清單在建置階段所需的 XML 屬性。

此程式碼片段是建立服務，在 Xamarin.Android 中符合這兩項需求的最簡單的範例：  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

在編譯時期，Xamarin.Android 會插入到下列的 XML 項目註冊服務**AndroidManifest.xml** （請注意，Xamarin.Android 會產生服務的隨機名稱）：

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

可與其他 Android 應用程式共用服務_匯出_它。 這可以藉由設定`Exported`屬性上的`ServiceAttribute`。 匯出的服務時`ServiceAttribute.Name`屬性也應該設定為提供有意義的公用名稱的服務。 此程式碼片段示範如何匯出和服務名稱：

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

**AndroidManifest.xml**項目，此服務接著會看起來像：

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

服務有自己的生命週期，與建立服務時叫用的回呼方法。 方法會叫用完全取決於服務的型別。 已啟動的服務必須實作不同的生命週期方法，比繫結的服務，而混合式服務必須實作已啟動的服務和繫結的服務之回呼方法。 這些方法是中的所有成員`Service`類別; 服務啟動時，將會決定哪些生命週期方法將會叫用。 將更多詳細資料中稍後討論這些生命週期方法。

根據預設，服務會開始在 Android 應用程式相同的程序。 您可在自己的處理序啟動服務，藉由設定`ServiceAttribute.IsolatedProcess`屬性設為 true:

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

下一個步驟是檢查如何啟動服務，然後移至檢驗如何實作三種不同類型的服務。

> [!NOTE]
> 服務的執行緒上執行 UI，所以如果任何工作是要執行其會封鎖 UI，服務必須使用執行緒來執行的工作。

## <a name="starting-a-service"></a>啟動服務

在 Android 中啟動服務的最基本方式是分派`Intent`其中包含中繼資料，並協助您識別哪些服務應該要啟動。 有兩種可用來啟動服務的對應方式的不同樣式：

-   **明確 Intent** &ndash; _明確 Intent_會識別完全哪些服務應該用來完成指定的動作。 明確 Intent 可以視為字母具有特定位址;Android 會明確識別服務路由的意圖。 此程式碼片段是一個範例使用明確的意圖啟動服務，稱為`DownloadService`:

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

-   **隱含 Intent** &ndash;鬆散識別這種類型的意圖動作，使用者想要執行，但以完成該動作的實際服務是未知。 隱含 Intent 可以視為字母所定址的 「 To Whom It 可能問題...」。
    Android 會檢查內容的目的，並判斷是否有現有的服務用來比對的意圖。

    _意圖篩選_用來協助符合隱含的對應方式，與已註冊的服務。 意圖篩選是 XML 項目加入至**AndroidManifest.xml**其中包含必要中繼資料，以協助符合隱含用途的服務。

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

如果 Android 有多個可能的相符項目為隱含的意圖，它可能會要求使用者選取要處理動作的元件：

![去除混淆對話方塊的螢幕擷取畫面](images/creating-a-service-01.png "去除混淆對話方塊的螢幕擷取畫面")

> [!IMPORTANT]
> 從 Android 5.0 (AP level 21) 開始隱含 intent 無法用來啟動服務。

可能的話，應用程式應該使用明確的對應方式，來啟動服務。 隱含 Intent 不會要求特定的服務啟動&ndash;它是裝置上安裝某些服務的要求來處理要求。 這項模稜兩可的要求可能會導致錯誤的服務處理要求或另一個應用程式不必要地啟動 （這會增加在裝置上的資源壓力）。

分派意圖的方式取決於服務的型別，以及將每個服務類型的特定指南中稍後詳細討論。


### <a name="creating-an-intent-filter-for-implicit-intents"></a>建立隱含的對應方式的意圖篩選器

隱含 Intent 相關聯的服務，Android 應用程式必須提供一些中繼資料來識別服務的功能。 此中繼資料由提供_意圖篩選器_。 意圖篩選器包含一些資訊，例如動作或類型的資料，必須存在於意圖啟動的服務。 在 Xamarin.Android 中，意圖篩選會在中註冊**AndroidManifest.xml**裝飾服務[ `IntentFilterAttribute` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)。 例如，下列程式碼加入相關聯動作的意圖篩選器`com.xamarin.DemoService`:

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

這會導致項目中所含**AndroidManifest.xml**檔案&ndash;封裝於應用程式的方式類似於下列範例中的項目：

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

Xamarin.Android 服務的方式的基本概念，讓我們來檢查更多詳細資料中的服務不同的子類型。


## <a name="related-links"></a>相關連結

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute/)
- [Android.App.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent/)
- [Android.App.IntentFilterAttribute](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)

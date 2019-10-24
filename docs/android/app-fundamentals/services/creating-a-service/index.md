---
title: 建立服務
ms.prod: xamarin
ms.assetid: A78A55E7-FB5C-4C42-8E3E-939B5E98F9EB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/03/2018
ms.openlocfilehash: 4cec06287963fb607ba2f523c6f47e56c08e655f
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70754899"
---
# <a name="creating-a-service"></a>建立服務

Xamarin Android 服務必須遵守 Android 服務的兩個 inviolable 規則：

- 他們必須擴充[`Android.App.Service`](xref:Android.App.Service)。
- 它們必須以[`Android.App.ServiceAttribute`](xref:Android.App.ServiceAttribute)裝飾。

Android 服務的另一個需求是必須在**androidmanifest.xml**中註冊，並提供唯一的名稱。 Xamarin 會在組建期間，使用必要的 XML 屬性自動在資訊清單中註冊服務。

此程式碼片段是在符合這兩項需求的 Xamarin 中建立服務的最簡單範例：  

```csharp
[Service]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

在編譯時期，Xamarin 會藉由將下列 XML 元素插入**androidmanifest.xml**來註冊服務（請注意，xamarin 會為服務產生隨機名稱）：

```xml
<service android:name="md5a0cbbf8da641ae5a4c781aaf35e00a86.DemoService" />
```

您可以藉由_匯出_服務與其他 Android 應用程式共用。 這是藉由在 `ServiceAttribute` 上設定 `Exported` 屬性來完成。 匯出服務時，也應該設定 `ServiceAttribute.Name` 屬性，為服務提供有意義的公用名稱。 此程式碼片段示範如何匯出和命名服務：

```csharp
[Service(Exported=true, Name="com.xamarin.example.DemoService")]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things.
}
```

此服務的**androidmanifest.xml**元素將會如下所示：

```xml
<service android:exported="true" android:name="com.xamarin.example.DemoService" />
```

服務擁有自己的生命週期，其具有回呼方法，會在建立服務時叫用。 確切叫用的方法取決於服務的類型。 已啟動的服務必須執行與系結服務不同的生命週期方法，而混合式服務必須同時為已啟動的服務和系結服務同時執行回呼方法。 這些方法都是 `Service` 類別的成員;服務的啟動方式會決定要叫用的生命週期方法。 稍後將更詳細地討論這些生命週期方法。

根據預設，服務會在與 Android 應用程式相同的進程中啟動。 將 `ServiceAttribute.IsolatedProcess` 屬性設定為 true，就可以在自己的進程中啟動服務：

```csharp
[Service(IsolatedProcess=true)]
public class DemoService : Service
{
    // Magical code that makes the service do wonderful things, in it's own process!
}
```

下一步是檢查如何啟動服務，然後繼續檢查如何執行三種不同類型的服務。

> [!NOTE]
> 服務會在 UI 執行緒上執行，因此如果有任何工作會封鎖 UI，服務就必須使用執行緒來執行工作。

## <a name="starting-a-service"></a>啟動服務

在 Android 中啟動服務的最基本方式是分派包含中繼資料的 `Intent`，以協助識別應該啟動的服務。 有兩種不同的意圖樣式可以用來啟動服務：

- **明確意圖 &ndash;** _明確意圖_會識別應該使用哪一種服務來完成指定的動作。 明確的意圖可以視為具有特定位址的字母;Android 會將意圖路由傳送至明確識別的服務。 此程式碼片段是使用明確意圖啟動名為 `DownloadService` 之服務的其中一個範例：

    ```csharp
    // Example of creating an explicit Intent in an Android Activity
    Intent downloadIntent = new Intent(this, typeof(DownloadService));
    downloadIntent.data = Uri.Parse(fileToDownload);
    ```

- **隱含意圖**&ndash; 這種意圖會鬆散識別使用者想要執行的動作，但完成該動作的確切服務是未知的。 隱含的意圖可以視為「可能會關注的目標」的字母。
    Android 會檢查意圖的內容，並判斷是否有符合意圖的現有服務。

    _意圖篩選_是用來協助符合隱含意圖與已註冊的服務。 意圖篩選是新增至**androidmanifest.xml**的 xml 專案，其中包含必要的中繼資料，以協助符合具有隱含意圖的服務。

    ```csharp
    Intent sendIntent = new Intent("common.xamarin.DemoService");
    sendIntent.Data = Uri.Parse(fileToDownload);
    ```

如果 Android 有一個以上的隱含意圖相符，則可能會要求使用者選取要處理動作的元件：

![消除混淆對話方塊的螢幕擷取畫面](images/creating-a-service-01.png "消除混淆對話方塊的螢幕擷取畫面")

> [!IMPORTANT]
> 從 Android 5.0 （AP 層級21）開始，隱含意圖不能用來啟動服務。

可能的話，應用程式應該使用明確意圖來啟動服務。 隱含意圖不會要求特定服務啟動 &ndash; 它是安裝在裝置上以處理要求的某個服務的要求。 這個不明確的要求可能會導致錯誤的服務處理要求，或另一個應用程式不必要地啟動（這會增加裝置上資源的壓力）。

分派意圖的方式取決於服務類型，稍後將在每個服務類型特定的指南中更詳細地討論。

### <a name="creating-an-intent-filter-for-implicit-intents"></a>為隱含意圖建立意圖篩選

若要將服務與隱含意圖建立關聯，Android 應用程式必須提供一些中繼資料來識別服務的功能。 此中繼資料是由_意圖篩選_條件所提供。 意圖篩選包含某些資訊（例如動作或資料類型），其必須存在於意圖中以啟動服務。 在 Xamarin 中，意圖篩選是以[`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute)來裝飾服務，在**androidmanifest.xml**中註冊。 例如，下列程式碼會使用 `com.xamarin.DemoService` 的相關聯動作來新增意圖篩選：

```csharp
[Service]
[IntentFilter(new String[]{"com.xamarin.DemoService"})]
public class DemoService : Service
{
}
```

這會導致**androidmanifest.xml**中包含一個專案，&ndash; 與應用程式一起封裝的專案，方法類似下列範例：

```xml
<service android:name="demoservice.DemoService">
    <intent-filter>
        <action android:name="com.xamarin.DemoService" />
    </intent-filter>
</service>
```

隨著 Xamarin. Android 服務的基本概念，讓我們更詳細地檢查服務的不同子類型。

## <a name="related-links"></a>相關連結

- [Android 應用程式服務](xref:Android.App.Service)
- [ServiceAttribute](xref:Android.App.ServiceAttribute)
- [Android. App. 意圖](xref:Android.Content.Intent)
- [IntentFilterAttribute](xref:Android.App.IntentFilterAttribute)

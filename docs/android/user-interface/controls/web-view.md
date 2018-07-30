---
title: Web 檢視
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 8d7b0e1abc8eb11bf812a111764b9cccfb41e041
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241171"
---
# <a name="web-view"></a>Web 檢視

[`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) 可讓您建立您自己的視窗來檢視網頁 （或甚至開發完整的瀏覽器）。 在本教學課程中，您將建立簡單[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)可檢視及瀏覽的網頁。

建立新的專案，名為**HelloWebView**。

開啟**Resources/Layout/Main.axml**並插入下列：

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

因為此應用程式會連線到網際網路，您必須新增適當的權限，android 資訊清單檔案。 開啟您的專案屬性，以指定應用程式運作所需的權限。 啟用`INTERNET`權限，如下所示：

![在 Android 資訊清單設定網際網路權限](web-view-images/01-set-internet-permissions.png)

現在，請開啟**MainActivity.cs**和新增 using 指示詞 Webkit:

```csharp
using Android.Webkit;
```

在頂端`MainActivity`類別中，宣告[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)物件：

```csharp
WebView web_view;
```

當**WebView**會要求載入 URL，它會將預設委派的預設瀏覽器的要求。 能夠**WebView**載入 URL （而不是預設的瀏覽器），您必須子類別化`Android.Webkit.WebViewClient`，並覆寫`ShouldOverriderUrlLoading`方法。 此自訂的執行個體`WebViewClient`提供給`WebView`。 若要這樣做，請新增下列巢狀`HelloWebViewClient`類別內`MainActivity`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    public override bool ShouldOverrideUrlLoading (WebView view, string url)
    {
        view.LoadUrl(url);
        return false;
    }
}
```

當`ShouldOverrideUrlLoading`會傳回`false`，它會通知 Android，目前`WebView`執行個體處理要求，並不需要任何進一步的動作。 

如果您的目標 API 層級 24 或更新版本，使用的多載`ShouldOverrideUrlLoading`採用`IWebResourceRequest`第二個引數，而不是`string`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    // For API level 24 and later
    public override bool ShouldOverrideUrlLoading (WebView view, IWebResourceRequest request)
    {
        view.LoadUrl(request.Url.ToString());
        return false;
    }
}
```

接下來，使用下列程式碼[ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    web_view = FindViewById<WebView> (Resource.Id.webview);
    web_view.Settings.JavaScriptEnabled = true;
    web_view.SetWebViewClient(new HelloWebViewClient());
    web_view.LoadUrl ("https://www.xamarin.com/university");
}
```

這會初始化成員[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)其中一個程式[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)版面配置，並可讓 JavaScript [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) 與[`JavaScriptEnabled` ](https://developer.xamarin.com/api/property/Android.Webkit.WebSettings.JavaScriptEnabled/) 
 `= true` (請參閱[呼叫 C\#從 JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)配方，如需如何呼叫 C\#從 JavaScript 函式)。 最後，會載入初始網頁[ `LoadUrl(String)` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/%2fM%2fLoadUrl)。

建置和執行應用程式。 簡單的網頁檢視器應用程式應該會看到與下列螢幕擷取畫面所示：

[![顯示 WebView 應用程式範例](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

若要處理**回**按鈕按下按鍵，新增下列 using 陳述式：

```csharp
using Android.Views;
```

接下來，新增下列方法內`HelloWebView`活動：

```csharp
public override bool OnKeyDown (Android.Views.Keycode keyCode, Android.Views.KeyEvent e)
{
    if (keyCode == Keycode.Back && web_view.CanGoBack ())
    {
        web_view.GoBack ();
        return true;
    }
    return base.OnKeyDown (keyCode, e);
}
```

這[ `OnKeyDown(int, KeyEvent)` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnKeyDown/(Android.Views.Keycode%2cAndroid.Views.KeyEvent))每當活動執行時按下按鍵時，就會呼叫回呼方法。 內使用條件[ `KeyEvent` ](https://developer.xamarin.com/api/type/Android.Views.KeyEvent/)若要檢查的索引鍵是否按下**回** 按鈕及是否[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)實際上能夠瀏覽回 （如果有歷程記錄）。 如果兩者都為 true，則[ `GoBack()` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.GoBack/)呼叫方法時，它將會瀏覽回上一步[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)歷程記錄。 傳回`true`指出事件已處理。 如果不符合此條件，事件會傳送回系統。

再次執行應用程式。 您現在應該能夠遵循連結，並向後巡覽頁面記錄：

[![作用中的 [上一頁] 按鈕的範例螢幕擷取畫面](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)


*此頁面上的部分是根據工作建立及 Android 的開放原始碼專案所共用，並依據所述的條款來使用修改*
[*Creative Commons 2.5 Attribution License*](http://creativecommons.org/licenses/by/2.5/).


## <a name="related-links"></a>相關連結

- [從 JavaScript 呼叫 C#](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
- [Android.Webkit.WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView)
- [KeyEvent](https://developer.xamarin.com/api/type/Android.Webkit.WebView/Client)

---
title: "網頁檢視"
ms.topic: article
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 0d418786a7364946e4e20100157fa0907b66deeb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="web-view"></a>網頁檢視

[`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) 可讓您建立您自己的視窗，檢視網頁 （或甚至開發完整的瀏覽器）。 在此教學課程中，您將建立簡單[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)可檢視及瀏覽網頁。

建立新的專案，名為**HelloWebView**。

開啟**Resources/Layout/Main.axml**並插入下列：

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

因為此應用程式會存取網際網路，您必須新增適當的權限 Android 資訊清單檔案。 開啟您的專案屬性，以指定應用程式運作所需的權限。 啟用`INTERNET`權限，如下所示：

![設定 Android 資訊清單中的網際網路權限](web-view-images/01-set-internet-permissions.png)

現在開啟**Weatherapp**和加入 using 指示詞 Webkit:

```csharp
using Android.Webkit;
```

在頂端`MainActivity`類別中，宣告[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)物件：

```csharp
WebView web_view;
```

當**WebView**會要求載入的 URL，它會依預設委派的預設瀏覽器的要求。 具有**WebView**載入 URL （而非預設的瀏覽器），您必須子類別化`Android.Webkit.WebViewClient`並覆寫`ShouldOverriderUrlLoading`方法。 此自訂的執行個體`WebViewClient`提供給`WebView`。 若要這樣做，請將新增下列巢狀`HelloWebViewClient`類別內`MainActivity`:

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

當`ShouldOverrideUrlLoading`傳回`false`，它會通知 Android，目前`WebView`執行個體處理要求，並不需要任何進一步的動作。 

如果您的目標 24 或更新版本的應用程式開發介面層級，使用的多載`ShouldOverrideUrlLoading`採用`IWebResourceRequest`第二個引數，而不是`string`:

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

此成員初始化[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)與一個從[ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)版面配置，並啟用 JavaScript 的[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) 與[`JavaScriptEnabled` ](https://developer.xamarin.com/api/property/Android.Webkit.WebSettings.JavaScriptEnabled/) 
 `= true` (請參閱[呼叫 C\#從 JavaScript](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript)如需如何呼叫 C 配方\#從 JavaScript 函式)。 最後，會載入初始的 web 網頁[ `LoadUrl(String)` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/%2fM%2fLoadUrl)。

建置和執行應用程式。 簡單的網頁檢視器應用程式應該會看到與下列螢幕擷取畫面所示：

[![顯示 WebView 應用程式的範例](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

若要處理**回**按鈕按下按鍵，加入下列 using 陳述式：

```csharp
using Android.Views;
```

接下來，加入下列方法內`HelloWebView`活動：

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

這[ `OnKeyDown(int, KeyEvent)` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnKeyDown/(Android.Views.Keycode%2cAndroid.Views.KeyEvent))每當活動執行時，按下按鈕時，將會呼叫回呼方法。 條件內使用[ `KeyEvent` ](https://developer.xamarin.com/api/type/Android.Views.KeyEvent/)來檢查是否已按下索引鍵是**回**按鈕及是否[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)實際上能夠瀏覽回 （如果有記錄）。 如果兩者都為 true，則[ `GoBack()` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.GoBack/)呼叫方法時，其會瀏覽回其中一個步驟中的[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)歷程記錄。 傳回`true`指出已經處理事件。 如果不符合此條件，然後會傳送回系統的事件。

再次執行應用程式。 您現在應該能夠遵循連結，並瀏覽網頁歷程記錄：

[![作用中的 [上一頁] 按鈕的範例螢幕擷取畫面](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)


*此頁面上的部分是修改依據工作建立及 Android 的開放原始碼專案所共用，並依據條款中所述來使用*
[*Creative Commons 2.5 Attribution 授權*](http://creativecommons.org/licenses/by/2.5/).


## <a name="related-links"></a>相關連結

- [從 JavaScript 呼叫 C#](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript)
- [Android.Webkit.WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView)
- [KeyEvent](https://developer.xamarin.com/api/type/Android.Webkit.WebView/Client)

---
title: Web View
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: c1fcb16bd40b818b27b57b877534e051a789a6c9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029025"
---
# <a name="xamarinandroid-web-view"></a>Xamarin. Android Web View

[`WebView`](xref:Android.Webkit.WebView)可讓您建立自己的視窗來查看網頁（或甚至開發完整的瀏覽器）。 在本教學課程中，您將建立簡單的[`Activity`](xref:Android.App.Activity)
可以查看和流覽網頁。

建立名為**HelloWebView**的新專案。

開啟**Resources/Layout/axml** ，並插入下列內容：

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

因為此應用程式會存取網際網路，所以您必須將適當的許可權新增至 Android 資訊清單檔案。 開啟專案的屬性，以指定應用程式運作所需的許可權。 啟用 [`INTERNET`] 許可權，如下所示：

![在 Android 資訊清單中設定網際網路許可權](web-view-images/01-set-internet-permissions.png)

現在開啟**MainActivity.cs** ，並為 Webkit 新增 using 指示詞：

```csharp
using Android.Webkit;
```

在 `MainActivity` 類別的頂端，宣告[`WebView`](xref:Android.Webkit.WebView)物件：

```csharp
WebView web_view;
```

當流覽器要求載入 URL 時，它預設會將要求委派給預設瀏覽器。 若要讓**web**工作載入 URL （而不是預設的瀏覽器），您必須將 `Android.Webkit.WebViewClient` 子類別化，並覆寫 `ShouldOverriderUrlLoading` 方法。 這個自訂 `WebViewClient` 的實例會提供給 `WebView`。 若要這麼做，請在 `MainActivity`內新增下列嵌套 `HelloWebViewClient` 類別：

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

當 `ShouldOverrideUrlLoading` 傳回 `false`時，它會向 Android 通知目前的 `WebView` 實例已處理要求，而不需要進一步的動作。 

如果您的目標是 API 層級24或更新版本，請使用 `ShouldOverrideUrlLoading` 的多載，以取得第二個引數的 `IWebResourceRequest`，而不是 `string`：

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

接下來，使用[`OnCreate()`](xref:Android.App.Activity.OnCreate*)）方法的下列程式碼：

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

這會使用[`Activity`](xref:Android.App.Activity)配置中的[`WebView`](xref:Android.Webkit.WebView)來初始化成員，並為具有[`JavaScriptEnabled`](xref:Android.Webkit.WebSettings.JavaScriptEnabled)
`= true` 的[`WebView`](xref:Android.Webkit.WebView)啟用 JavaScript （請參閱[從 JavaScript 配方呼叫 C\#](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)如何從 JavaScript 呼叫 C\# 函式的相關資訊。 最後，會載入包含[`LoadUrl(String)`](xref:Android.Webkit.WebView)的初始頁面。

建置和執行應用程式。 您應該會看到簡單的網頁檢視器應用程式，如下列螢幕擷取畫面所示：

[顯示 Web 工作程式的![範例](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

若要處理 [**上一頁**] 按鈕按鍵，請新增下列 using 語句：

```csharp
using Android.Views;
```

接下來，在 `HelloWebView` 活動內新增下列方法：

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

此[`OnKeyDown(int, KeyEvent)`](xref:Android.App.Activity.OnKeyDown*)
每當活動正在執行時按下按鈕時，就會呼叫回呼方法。 內的條件會使用[`KeyEvent`](xref:Android.Views.KeyEvent)來檢查按下的按鍵是否為 [**上一頁**] 按鈕，以及[`WebView`](xref:Android.Webkit.WebView)是否確實能夠回頭導覽（如果它有歷程記錄）。 如果兩者都是 true，則會呼叫[`GoBack()`](xref:Android.Webkit.WebView.GoBack)方法，這會在[`WebView`](xref:Android.Webkit.WebView)歷程記錄中流覽回一個步驟。 傳回 `true` 表示已處理事件。 如果不符合此條件，則會將事件傳送回系統。

再次執行應用程式。 您現在應該可以追蹤連結，並透過頁面歷程記錄回頭流覽：

[動作中 [上一頁] 按鈕的![範例螢幕擷取畫面](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)

*此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改，並根據*
[*創意 Commons 2.5 屬性授權*](https://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。

## <a name="related-links"></a>相關連結

- [從 JavaScript 呼叫 C#](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
- [Webkit。 Web 視圖](xref:Android.Webkit.WebView)
- [KeyEvent](xref:Android.Webkit.WebView)

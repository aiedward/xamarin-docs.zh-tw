---
title: 網頁檢視
description: 在本機或網路網頁內容與文件。
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: a1cba53223567e353194a4fcd52c8e22fa48ddf0
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="webview"></a>網頁檢視

[WebView](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)是檢視以顯示 web 和 HTML 的應用程式中的內容。 不同於`OpenUri`，其可接受使用者在裝置上的網頁瀏覽器`WebView`顯示您的應用程式內的 HTML 內容。

本指南是由下列各節：

- **[內容](#Content)** &ndash; WebView 支援各種內容的來源，包括內嵌的 HTML、 web 頁面，以及 HTML 字串。
- **[瀏覽](#Navigation)** &ndash; WebView 包含瀏覽至特定頁面然後返回的支援。
- **[事件](#Events)** &ndash;接聽以及回應使用者在 WebView 中所採取的動作。
- **[效能](#Performance)** &ndash;深入了解每個平台的 WebView 效能特性。
- **[權限](#Permissions)** &ndash;了解如何設定權限，以便在您的應用程式中運作 WebView。
- **[版面配置](#Layout)** &ndash; WebView 有一些非常特定的需求，如它的配置方式。了解如何確定 WebView 會正常顯示：

![](webview-images/in-app-browser.png "在應用程式瀏覽器")

## <a name="content"></a>內容

WebView 隨附支援下列類型的內容：

- HTML 與 CSS 網站&ndash;WebView 具有完整支援使用 HTML 和 CSS，包括支援 JavaScript 撰寫的網站。
- 文件&ndash;WebView WebView 實作每個平台上使用原生元件，因為是顯示在每個平台可檢視的文件。 這表示 PDF 檔案使用 iOS 和 Android 上。
- HTML 字串&ndash;WebView 可以顯示從記憶體的 HTML 字串。
- 本機檔案&ndash;WebView 可以呈現內嵌於應用程式中任何上述的內容類型。

> [!NOTE]
> `WebView` 在 Windows 上不支援 Silverlight、 Flash 或任何 ActiveX 控制項，即使該平台上支援 Internet explorer。

### <a name="websites"></a>網站

若要顯示來自網際網路的網站，請設定`WebView`的[ `Source` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebViewSource/)屬性，以字串 URL:

```csharp
var browser = new WebView {
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Url 必須完全正確與指定的通訊協定 （也就是它必須有"http://"或"https://"前面會加上）。

#### <a name="ios-and-ats"></a>iOS 和 AT

自 9 版 iOS 將只允許您的應用程式的預設實作最佳安全性的伺服器進行通訊。 值必須在設定`Info.plist`才能與不安全的伺服器進行通訊。

> [!NOTE]
> 如果您的應用程式需要的連接不安全的網站，您應該一律使用例外狀況的形式輸入網域`NSExceptionDomains`而不是完全使用時，關閉 AT `NSAllowsArbitraryLoads`。 `NSAllowsArbitraryLoads` 應該只用在極端的緊急情況下。

下列示範如何啟用特定網域中 （在此案例的 xamarin.com)，略過 AT 需求：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSExceptionDomains</key>
        <dict>
            <key>xamarin.com</key>
            <dict>
                <key>NSIncludesSubdomains</key>
                <true/>
                <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSTemporaryExceptionMinimumTLSVersion</key>
                <string>TLSv1.1</string>
            </dict>
        </dict>
    </dict>
```

它是僅會允許略過 AT，讓您可以使用信任的網站時不受信任網域上的其他安全性所獲益的某些網域的最佳作法。 以下示範如何停用應用程式的 AT 較不安全的方法：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
```

請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)如需 iOS 9 這項新功能詳細資訊。

### <a name="html-strings"></a>HTML 字串

如果您想要呈現的 HTML 程式碼中以動態方式定義字串，您必須建立的執行個體[ `HtmlWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.HtmlWebViewSource/):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![](webview-images/html-string.png "網頁檢視顯示的 HTML 字串")

在上述程式碼中，`@`用來將 HTML 標記做為字串常值，這表示所有一般的逸出字元會被忽略。

### <a name="local-html-content"></a>本機的 HTML 內容

網頁檢視可以顯示內容，從 HTML、 CSS 和 Javascript 內嵌的應用程式內。 例如: 

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamrin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

CSS:

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

請注意，上述的 CSS 中指定的字型都必須是可自訂的每個平台，因為並非所有平台都有相同的字型。

若要顯示本機內容使用`WebView`，您必須開啟 HTML 檔案，如同任何其他的則載入內容當做字串`Html`屬性`HtmlWebViewSource`。 如需有關開啟檔案的詳細資訊，請參閱[使用檔案](~/xamarin-forms/app-fundamentals/files.md)。

下列螢幕擷取畫面會顯示每個平台上顯示本機內容的結果：

![](webview-images/local-content.png "WebView 顯示本機內容")

尚未載入的第一頁，雖然`WebView`不認得 HTML 來自何處。 處理參考本機資源的網頁時，這會是問題。 本機的頁面連結至每個其他，網頁會使用個別的 JavaScript 檔案，或頁面會連結到的 CSS 樣式表時，就會加入時，可能會發生情況的範例。  

若要解決這個問題，您必須告訴`WebView`尋找檔案系統上的檔案的位置。 這樣做，藉由設定`BaseUrl`屬性`HtmlWebViewSource`供`WebView`。

由於檔案系統上每個作業系統有不同，您必須判斷該 URL 上每個平台。 Xamarin.Forms 會公開`DependencyService`解決在各平台上的執行階段相依性。

若要使用`DependencyService`，先定義每個平台實作的介面：

```csharp
public interface IBaseUrl { string Get(); }
```

請注意，每個平台上實作的介面，直到應用程式將不會執行。 在一般的專案中，請確定您記得設定`BaseUrl`使用`DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

然後必須提供每個平台介面的實作。

#### <a name="ios"></a>iOS

在 iOS 上，web 內容應該位於專案的根目錄或**資源**目錄用於建置動作*BundleResource*如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/ios-vs.png "在 iOS 上的本機檔案")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](webview-images/ios-xs.png "在 iOS 上的本機檔案")

-----

`BaseUrl`應設為主要的組合的路徑：

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS{
  public class BaseUrl_iOS : IBaseUrl {
    public string Get() {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

在 Android 上，建置動作的資產資料夾中放置 HTML、 CSS 和圖像*AndroidAsset*依照以下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/android-vs.png "在 Android 上的本機檔案")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](webview-images/android-xs.png "在 Android 上的本機檔案")

-----

在 Android 上，`BaseUrl`應該設定為`"file:///android_asset/"`:

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android {
  public class BaseUrl_Android : IBaseUrl {
    public string Get() {
      return "file:///android_asset/";
    }
  }
}
```

在 Android 上，檔案中**資產**資料夾也可以存取目前的 Android 內容，由透過`MainActivity.Instance`屬性：

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html"))) {
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>通用 Windows 平台

通用 Windows 平台 (UWP) 專案中放置 HTML、 CSS 和圖像專案根目錄中建置動作設定為*內容*。

`BaseUrl`應該設定為`"ms-appx-web:///"`:

```csharp
[assembly: Dependency(typeof(BaseUrl))]
namespace WorkingWithWebview.UWP
{
    public class BaseUrl : IBaseUrl
    {
        public string Get()
        {
            return "ms-appx-web:///";
        }
    }
}
```

## <a name="navigation"></a>巡覽

WebView 支援瀏覽數個方法和屬性，它可提供：

- **GoForward()** &ndash;如果`CanGoForward`為 true，呼叫`GoForward`向前巡覽至下一步 瀏覽的網頁。
- **GoBack()** &ndash;如果`CanGoBack`為 true，呼叫`GoBack`會瀏覽至最後一個瀏覽的網頁。
- **CanGoBack** &ndash; `true`如果有頁面瀏覽至，`false`如果瀏覽器起始 url。
- **CanGoForward** &ndash; `true`如果使用者已向後巡覽，而且可以向前移動至已經瀏覽過的頁面。

中的頁面，`WebView`不支援多點觸控筆勢。 請務必先確定該內容行動最佳化且會出現，而不需要縮放。

它是很常見的應用程式顯示內的連結`WebView`，而不是裝置的瀏覽器。 在這些情況下，有助於讓一般的瀏覽，但是當使用者叫用備份開始連結時，應用程式應會返回一般應用程式檢視。

為了實現此案例中使用的內建瀏覽方法和屬性。

藉由建立瀏覽器檢視頁面啟動：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.InAppDemo"
Title="In App Browser">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout Orientation="Horizontal" Padding="10,10">
                <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="backClicked" />
                <Button Text="Forward" HorizontalOptions="End" Clicked="forwardClicked" />
            </StackLayout>
            <WebView x:Name="Browser" WidthRequest="1000" HeightRequest="1000" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在我們的程式碼後置：

```csharp
public partial class InAppDemo : ContentPage
{
  //sets the URL for the browser in the page at creation
    public InAppDemo (string URL)
    {
        InitializeComponent ();
        Browser.Source = URL;
    }


    private void backClicked(object sender, EventArgs e)
    {
    // Check to see if there is anywhere to go back to
        if (Browser.CanGoBack) {
            Browser.GoBack ();
        } else { // If not, leave the view
            Navigation.PopAsync ();
        }
    }

    private void forwardClicked(object sender, EventArgs e)
    {
        if (Browser.CanGoForward) {
            Browser.GoForward ();
        }
    }
}
```

就這麼容易！

![](webview-images/in-app-browser.png "WebView 瀏覽按鈕")

## <a name="events"></a>事件

網頁檢視會引發兩個事件，以協助您回應狀態的變更：

- **瀏覽** &ndash; WebView 會開始載入新的頁面時引發的事件。
- **瀏覽**&ndash;載入的頁面，並瀏覽已停止時引發的事件。

如果您預期使用需要很長的時間來載入的網頁，請考慮使用這些事件實作之狀態指標。 例如在 XAML 看起來像這樣：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.LoadingDemo" Title="Loading Demo">
  <ContentPage.Content>
    <StackLayout>
      <Label x:Name="LoadingLabel"
        Text="Loading..."
        HorizontalOptions="Center"
        IsVisible="false" />
      <WebView x:Name="Browser"
      HeightRequest="1000"
      WidthRequest="1000"
      Navigating="webOnNavigating"
      Navigated="webOnEndNavigating" />
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

兩個事件處理常式中：

```csharp
void webOnNavigating (object sender, WebNavigatingEventArgs e)
{
    LoadingLabel.IsVisible = true;
}

void webOnEndNavigating (object sender, WebNavigatedEventArgs e)
{
    LoadingLabel.IsVisible = false;
}
```

這會產生下列輸出 （載入）：

![](webview-images/loading-start.png "WebView 巡覽事件範例")

完成的載入：

![](webview-images/loading-end.png "WebView 巡覽事件範例")

## <a name="performance"></a>效能

近來過採用技術，像是硬體加速呈現和 JavaScript 編譯每一個熱門的 web 瀏覽器。 不幸的是，由於安全性限制，大部分的繁雜所沒有的 iOS equaivalent `WebView`， `UIWebView`。 Xamarin.Forms`WebView`使用`UIWebView`。 如果是問題，您必須撰寫自訂轉譯器，它使用`WKWebView`，可支援更快速瀏覽。 請注意， `WKWebView` iOS 8 及更新版本上才支援。

依預設在 Android 上的網頁檢視是差不多一樣內建瀏覽器。

[UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view)使用 Microsoft Edge 轉譯引擎。 桌面與 tablet pc 的裝置應該會看到相同的效能與使用 Edge 瀏覽器本身。

## <a name="permissions"></a>權限

為了讓`WebView`運作，您必須確定每個平台已設定權限。 請注意，在某些平台，`WebView`偵錯模式，但不是會針對發行建置在運作。 這是因為依預設會由 Visual Studio 在偵錯模式中的 mac 設定某些權限，如同在 Android 上，網際網路存取。

- **UWP** &ndash;時顯示的網路內容需要網際網路 （用戶端和伺服器） 功能。
- **Android** &ndash;需要`INTERNET`才顯示從網路的內容。 本機內容需要任何特殊權限。
- **iOS** &ndash;需要任何特殊權限。

## <a name="layout"></a>配置

不同於其他大部分 Xamarin.Forms 檢視，`WebView`要求`HeightRequest`和`WidthRequest`包含在 StackLayout 或 RelativeLayout 時指定。 如果您無法指定這些屬性`WebView`將不會呈現。

下列範例示範的配置，導致工作，轉譯`WebView`s:

與 WidthRequest HeightRequest StackLayout:

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

與 WidthRequest HeightRequest RelativeLayout:

```xaml
<RelativeLayout>
    <Label Text="test"
        RelativeLayout.XConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=10}"
        RelativeLayout.YConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=20}" />
    <WebView Source="http://www.xamarin.com/"
        RelativeLayout.XConstraint="{ConstraintExpression Type=Constant,
                                     Constant=10}"
        RelativeLayout.YConstraint="{ConstraintExpression Type=Constant,
                                     Constant=50}"
        WidthRequest="1000" HeightRequest="1000" />
</RelativeLayout>
```

AbsoluteLayout*沒有*WidthRequest & HeightRequest:

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

方格*沒有*WidthRequest & HeightRequest。 方格是其中幾個配置，不需要指定要求的高度和寬度。:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="100" />
        <RowDefinition Height="*" />
    </Grid.RowDefinitions>
    <Label Text="test" Grid.Row="0" />
    <WebView Source="http://www.xamarin.com/" Grid.Row="1" />
</Grid>
```


## <a name="related-links"></a>相關連結

- [使用 WebView （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)
- [WebView （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView)

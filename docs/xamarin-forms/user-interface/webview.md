---
title: Xamarin.Forms 切換
description: 本文說明如何使用網頁 Xamarin.Forms 記錄類別，向使用者呈現本機或網路 web 內容和檔。
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d535956e03ac02e2cf0c5d7528ee8e363a2a1e55
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561647"
---
# <a name="no-locxamarinforms-webview"></a>Xamarin.Forms 切換

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) 是在您的應用程式中顯示 web 和 HTML 內容的視圖：

![在應用程式瀏覽器中](webview-images/in-app-browser.png)

## <a name="content"></a>內容

`WebView` 支援下列類型的內容：

- HTML & CSS 網站的 &ndash; web 程式可完整支援使用 HTML & css 撰寫的網站，包括 JavaScript 支援。
- 檔 &ndash; 是因為在每個平臺上使用原生元件來執行 web 程式，但 web 程式能夠以基礎平臺所支援的格式來顯示檔。
- HTML 字串 &ndash; web 可顯示記憶體中的 html 字串。
- 本機檔案的 &ndash; web 程式可顯示在應用程式中內嵌的任何內容類型。

> [!NOTE]
> `WebView` 在 Windows 上並不支援 Silverlight、Flash 或任何 ActiveX 控制項，即使該平臺上的 Internet Explorer 支援它們也是一樣。

### <a name="websites"></a>網站

若要從網際網路顯示網站，請將的 `WebView` [`Source`](xref:Xamarin.Forms.WebViewSource) 屬性設定為字串 URL：

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Url 的格式必須完全符合指定的通訊協定 (也就是它必須在) 的前面加上 "HTTP://" 或 "HTTPs://"。

#### <a name="ios-and-ats"></a>iOS 和 ATS

自第9版起，iOS 只允許您的應用程式與預設執行最佳作法安全性的伺服器進行通訊。 您必須在中設定值 `Info.plist` ，才能與不安全的伺服器進行通訊。

> [!NOTE]
> 如果您的應用程式需要連接到不安全的網站，您應該一律使用來輸入網域做為例外狀況， `NSExceptionDomains` 而不是使用完全關閉 ATS `NSAllowsArbitraryLoads` 。 `NSAllowsArbitraryLoads` 應該只在極端緊急情況下使用。

以下示範如何啟用特定網域 (在此案例中為 xamarin.com) 略過 ATS 需求：

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
    ...
</key>
```

最佳做法是只讓某些網域略過 ATS，讓您可以使用信任的網站，同時受益不受信任網域上的額外安全性。 以下示範針對應用程式停用 ATS 的較不安全方法：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

如需 iOS 9 中這項新功能的詳細資訊，請參閱 [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md) 。

### <a name="html-strings"></a>HTML 字串

如果您想要呈現在程式碼中動態定義的 HTML 字串，您必須建立的實例 [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) ：

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![顯示 HTML 字串的 Web 型](webview-images/html-string.png)

在上述程式碼中， `@` 是用來將 HTML 標示為 [逐字字串常](/dotnet/csharp/programming-guide/strings/#regular-and-verbatim-string-literals)值，這表示會忽略大部分的 escape 字元。

> [!NOTE]
> 您可能必須設定的 `WidthRequest` 和 `HeightRequest` 屬性 [`WebView`](xref:Xamarin.Forms.WebView) ，才能查看 HTML 內容，這取決於的版面配置 `WebView` 是的子系。 例如，在中，這是必要的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。

### <a name="local-html-content"></a>本機 HTML 內容

Web 程式可顯示應用程式中內嵌的 HTML、CSS 和 JavaScript 內容。 例如：

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamarin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

Css：

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

請注意，在上述 CSS 中指定的字型必須針對每個平臺自訂，因為並非每個平臺都有相同的字型。

若要使用來顯示本機內容 `WebView` ，您將需要像任何其他檔案一樣開啟 HTML 檔案，然後將內容以字串的形式載入至的 `Html` 屬性 `HtmlWebViewSource` 。 如需開啟檔案的詳細資訊，請參閱 [使用](~/xamarin-forms/data-cloud/data/files.md)檔案。

下列螢幕擷取畫面顯示在每個平臺上顯示本機內容的結果：

![顯示本機內容的 Web 內容](webview-images/local-content.png)

雖然第一頁已載入，但並 `WebView` 不知道 HTML 的來源。 這是處理參考本機資源的頁面時的問題。 可能發生的情況的範例包括：當本機頁面彼此連結時，頁面會使用個別的 JavaScript 檔案，或頁面連結至 CSS 樣式表單。  

若要解決這個問題，您必須告訴在檔 `WebView` 系統上尋找檔案的位置。 若要這麼做，請 `BaseUrl` 在所使用的上設定屬性 `HtmlWebViewSource` `WebView` 。

因為每個作業系統上的 filesystem 都不同，所以您需要在每個平臺上判斷該 URL。 Xamarin.Forms 公開， `DependencyService` 以便在每個平臺上的執行時間解析相依性。

若要使用 `DependencyService` ，請先定義可在每個平臺上執行的介面：

```csharp
public interface IBaseUrl { string Get(); }
```

請注意，在每個平臺上執行介面之前，應用程式將不會執行。 在一般專案中，請務必記得 `BaseUrl` 使用下列專案設定 `DependencyService` ：

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

接著，您必須提供每個平臺的介面介面。

#### <a name="ios"></a>iOS

在 iOS 上，web 內容應該位於專案的 [根目錄] 或 [ **資源** ] 目錄中，並具有組建動作 *套件套件*，如下所示：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![IOS 上的本機檔案](webview-images/ios-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![IOS 上的本機檔案](webview-images/ios-xs.png)

-----

`BaseUrl`應設定為主要套件組合的路徑：

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS
{
  public class BaseUrl_iOS : IBaseUrl
  {
    public string Get()
    {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

在 Android 上，于 [資產] 資料夾中放置 HTML、CSS 和影像，並以組建動作 *AndroidAsset* ，如下所示：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Android 上的本機檔案](webview-images/android-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![Android 上的本機檔案](webview-images/android-xs.png)

-----

在 Android 上， `BaseUrl` 應設定為 `"file:///android_asset/"` ：

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android
{
  public class BaseUrl_Android : IBaseUrl
  {
    public string Get()
    {
      return "file:///android_asset/";
    }
  }
}
```

在 Android 上，[ **資產** ] 資料夾中的檔案也可以透過目前的 Android 內容（由屬性公開）來存取 `MainActivity.Instance` ：

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平臺 (UWP) 專案中，將 HTML、CSS 和影像放在專案根目錄中，並將 [組建] 動作設定為 [ *內容*]。

`BaseUrl`應設定為 `"ms-appx-web:///"` ：

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

Web 程式支援透過許多可用的方法和屬性進行流覽：

- **GoForward ( # B1 ** &ndash; 如果 `CanGoForward` 是 true，則呼叫會 `GoForward` 向前流覽至下一個流覽過的頁面。
- **GoBack ( # B1 ** &ndash; 若 `CanGoBack` 為 true，則呼叫 `GoBack` 將會流覽到最後一個流覽過的頁面。
- **>cangoback** &ndash;`true`如果有可流覽的頁面，則為， `false` 如果瀏覽器位於起始 URL，則為。
- **>cangoforward** &ndash;`true`如果使用者已向後導覽，並可移至已造訪的頁面。

在頁面內，不 `WebView` 支援多點觸控手勢。 請務必確定內容已進行行動優化，而且會出現而不需要縮放。

應用程式通常會在中顯示連結 `WebView` ，而不是在裝置的瀏覽器中顯示。 在這些情況下，允許正常的流覽是很有用的，但當使用者在起始連結時，當使用者叫用時，應用程式應該會返回一般的應用程式視圖。

使用內建的導覽方法和屬性來啟用此案例。

首先，建立瀏覽器視圖的頁面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.InAppBrowserXaml"
             Title="Browser">
    <StackLayout Margin="20">
        <StackLayout Orientation="Horizontal">
            <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="OnBackButtonClicked" />
            <Button Text="Forward" HorizontalOptions="EndAndExpand" Clicked="OnForwardButtonClicked" />
        </StackLayout>
        <!-- WebView needs to be given height and width request within layouts to render. -->
        <WebView x:Name="webView" WidthRequest="1000" HeightRequest="1000" />
    </StackLayout>
</ContentPage>
```

在程式碼後端中：

```csharp
public partial class InAppBrowserXaml : ContentPage
{
    public InAppBrowserXaml(string URL)
    {
        InitializeComponent();
        webView.Source = URL;
    }

    async void OnBackButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoBack)
        {
            webView.GoBack();
        }
        else
        {
            await Navigation.PopAsync();
        }
    }

    void OnForwardButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoForward)
        {
            webView.GoForward();
        }
    }
}
```

這樣就完成了！

![Web 瀏覽按鈕](webview-images/in-app-browser.png)

## <a name="events"></a>事件

Web 工作會引發下列事件，以協助您回應狀態的變更：

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) –在 Web 工作開始載入新頁面時引發的事件。
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) –載入頁面並停止導覽時引發的事件。
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested) –提出要求以重載目前內容時引發的事件。

[`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs)事件隨附的物件 [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) 有四個屬性：

- `Cancel` –指出是否要取消導覽。
- `NavigationEvent` –引發的導覽事件。
- `Source` –執行導覽的元素。
- `Url` –導覽目的地。

[`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs)事件隨附的物件 [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) 有四個屬性：

- `NavigationEvent` –引發的導覽事件。
- `Result` –使用列舉成員描述導覽的結果 [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) 。 有效值為 `Cancel`、`Failure``Success` 及 `Timeout`。
- `Source` –執行導覽的元素。
- `Url` –導覽目的地。

如果您預期使用的網頁需要很長的時間載入，請考慮使用 [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) 和 [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) 事件來執行狀態指標。 例如：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.LoadingLabelXaml"
             Title="Loading Demo">
    <StackLayout>
        <!--Loading label should not render by default.-->
        <Label x:Name="labelLoading" Text="Loading..." IsVisible="false" />
        <WebView HeightRequest="1000" WidthRequest="1000" Source="http://www.xamarin.com" Navigated="webviewNavigated" Navigating="webviewNavigating" />
    </StackLayout>
</ContentPage>
```

這兩個事件處理常式：

```csharp
void webviewNavigating(object sender, WebNavigatingEventArgs e)
{
    labelLoading.IsVisible = true;
}

void webviewNavigated(object sender, WebNavigatedEventArgs e)
{
    labelLoading.IsVisible = false;
}
```

這會導致下列輸出 (載入) ：

![Web 流覽事件範例](webview-images/loading-start.png)

已完成載入：

![Web 流覽事件範例](webview-images/loading-end.png)

## <a name="reloading-content"></a>正在重載內容

[`WebView`](xref:Xamarin.Forms.WebView) 具有 `Reload` 可用於重載目前內容的方法：

```csharp
var webView = new WebView();
...
webView.Reload();
```

叫 `Reload` 用方法時 `ReloadRequested` ，就會引發事件，表示已提出要求以重載目前的內容。

## <a name="performance"></a>效能

熱門的網頁瀏覽器採用硬體加速轉譯和 JavaScript 編譯等技術。 在 Xamarin.Forms 4.4 之前， Xamarin.Forms `WebView` 是由類別在 iOS 上執行 `UIWebView` 。 不過，這些技術中有許多無法在此實行中使用。 因此，自 Xamarin.Forms 4.4 起， Xamarin.Forms `WebView` 會由類別在 iOS 上執行 `WkWebView` ，以支援更快速的流覽。

> [!NOTE]
> 在 iOS 上， `WkWebViewRenderer` 具有接受引數的函式多載 `WkWebViewConfiguration` 。 這可讓轉譯器在建立時設定。

基於相容性的理由，應用程式可以傳回使用 iOS `UIWebView` 類別來執行 Xamarin.Forms `WebView` 。 將下列程式碼新增至應用程式之 iOS 平臺專案中的 **AssemblyInfo.cs** 檔案，即可達成此目的：

```csharp
// Opt-in to using UIWebView instead of WkWebView.
[assembly: ExportRenderer(typeof(Xamarin.Forms.WebView), typeof(Xamarin.Forms.Platform.iOS.WebViewRenderer))]
```

`WebView` 在 Android 上，預設會與內建瀏覽器的速度一樣快。

[UWP web](/windows/uwp/design/controls-and-patterns/web-view)工作使用 Microsoft Edge 轉譯引擎。 桌上型電腦和平板電腦裝置應該會看到與使用 Edge 瀏覽器本身相同的效能。

## <a name="permissions"></a>權限

`WebView`您必須確定已針對每個平臺設定許可權，才能運作。 請注意，在某些平臺上， `WebView` 將會以「偵測模式」運作，而不是在建立以供發行時使用。 這是因為某些許可權（像是在 Android 上存取網際網路的許可權）依預設會在處於 debug 模式時 Visual Studio for Mac 設定。

- **UWP** &ndash; 顯示網路內容時，要求 (用戶端 & 伺服器) 功能的網際網路。
- **Android** &ndash;`INTERNET`只有在顯示來自網路的內容時才需要。 本機內容不需要任何特殊許可權。
- **iOS** &ndash; 不需要任何特殊許可權。

## <a name="layout"></a>配置

不同于其他大部分 Xamarin.Forms 的視圖，在 `WebView` `HeightRequest` `WidthRequest` StackLayout 或 RelativeLayout 中包含時，會要求和都必須指定。 如果您無法指定這些屬性， `WebView` 將不會呈現。

下列範例示範會導致運作的版面配置，並呈現 `WebView` ：

StackLayout with WidthRequest & HeightRequest：

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

RelativeLayout with WidthRequest & HeightRequest：

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

AbsoluteLayout *但沒有* WidthRequest & HeightRequest：

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

*不含*WidthRequest 的方格 & HeightRequest。 格線是幾個不需要指定所要求高度和寬度的版面配置之一。：

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

## <a name="invoking-javascript"></a>叫用 JavaScript

[`WebView`](xref:Xamarin.Forms.WebView) 包含從 c # 叫用 JavaScript 函式，並將任何結果傳回給呼叫 c # 程式碼的功能。 這是利用方法來完成 [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) ，如下列範例 [所](/samples/xamarin/xamarin-forms-samples/userinterface-webview) 示：

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

[`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*)方法會評估指定為引數的 JavaScript，並傳回做為的任何結果 `string` 。 在此範例中， `factorial` 會叫用 JavaScript 函數，以傳回結果的階乘 `number` 。 這個 JavaScript 函式是在載入的本機 HTML 檔案中定義 [`WebView`](xref:Xamarin.Forms.WebView) ，如下列範例所示：

```html
<html>
<body>
<script type="text/javascript">
function factorial(num) {
        if (num === 0 || num === 1)
            return 1;
        for (var i = num - 1; i >= 1; i--) {
            num *= i;
        }
        return num;
}
</script>
</body>
</html>
```

## <a name="cookies"></a>Cookie

您可以在上設定 cookie [`WebView`](xref:Xamarin.Forms.WebView) ，然後以 web 要求傳送至指定的 URL。 這是藉由將 `Cookie` 物件加入至 `CookieContainer` ，然後將其設定為可系結屬性的值來完成 `WebView.Cookies` 。 下列程式碼顯示此範例：

```csharp
using System.Net;
using Xamarin.Forms;
// ...

CookieContainer cookieContainer = new CookieContainer();
Uri uri = new Uri("https://dotnet.microsoft.com/apps/xamarin", UriKind.RelativeOrAbsolute);

Cookie cookie = new Cookie
{
    Name = "XamarinCookie",
    Expires = DateTime.Now.AddDays(1),
    Value = "My cookie",
    Domain = uri.Host,
    Path = "/"
};
cookieContainer.Add(uri, cookie);
webView.Cookies = cookieContainer;
webView.Source = new UrlWebViewSource { Url = uri.ToString() };
```

在此範例中，會將單一 `Cookie` 新增至 `CookieContainer` 物件，然後將其設定為屬性的值 `WebView.Cookies` 。 當將  [`WebView`](xref:Xamarin.Forms.WebView) web 要求傳送至指定的 URL 時，cookie 會隨要求傳送。

## <a name="uiwebview-deprecation-and-app-store-rejection-itms-90809"></a>UIWebView 淘汰及 App Store 拒絕 (ITMS-90809) 

從2020年4月開始， [Apple 會拒絕](https://developer.apple.com/news/?id=12232019b) 仍使用已淘汰 API 的應用程式 `UIWebView` 。 雖然 Xamarin.Forms 已切換為 `WKWebView` 預設值，但二進位檔中仍有舊版 SDK 的參考 Xamarin.Forms 。 目前的 [iOS 連結器](~/ios/deploy-test/linker.md) 行為並不會移除這種情況，因此 `UIWebView` 當您提交至 App Store 時，已淘汰的 API 仍會顯示為從您的應用程式參考。

您可以使用連結器的預覽版本來修正此問題。 若要啟用預覽，您將需要提供額外的引數 `--optimize=experimental-xforms-product-type` 給連結器。

這項工作的必要條件如下：

- ** Xamarin.Forms 4.5 或更高版本**。 Xamarin.Forms 如果您的應用程式使用材質視覺效果，則需要4.6 或更高版本。
- **13.10.0.17 或更高版本**。 檢查 [Visual Studio 中](~/cross-platform/troubleshooting/questions/version-logs.md#version-information)的 Xamarin 版本。 此版本的 Xamarin 隨附于 Visual Studio for Mac dbms-guide-8.4.1 和 Visual Studio 16.4.3 版。
- **移除的參考 `UIWebView` **。 您的程式碼不應該有任何參考 `UIWebView` 或任何使用的類別 `UIWebView` 。

如需偵測和移除參考的詳細資訊 `UIWebView` ，請參閱 [UIWebView](~/ios/user-interface/controls/webview.md#uiwebview-deprecation)取代。

### <a name="configure-the-linker"></a>設定連結器

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

請遵循下列步驟，讓連結器移除 `UIWebView` 參考：

1. **開啟 iOS 專案屬性** &ndash; 以滑鼠右鍵按一下您的 iOS 專案，然後選擇 [ **屬性**]。
1. **流覽至 [IOS 組建] 區段** &ndash; 選取 [ **IOS 組建** ] 區段。
1. **更新其他 mtouch 引數** &ndash; 在 [ **其他 mtouch 引數** ] 中， `--optimize=experimental-xforms-product-type` 除了任何可能已存在) 中的值之外，還會新增此旗標 (。 注意：此旗標會搭配設定為 [**僅 SDK** ] 或 [**全部連結**] 的**連結器行為**一起運作。 如果基於任何原因，您會在將連結器行為設定為 [全部] 時看到錯誤，這很可能是因為應用程式程式碼或協力廠商程式庫中的問題，而不是連結器安全。 如需連結器的詳細資訊，請參閱 [連結 Xamarin IOS 應用程式](~/ios/deploy-test/linker.md)。
1. **更新所有組建** &ndash; 設定您可以使用視窗頂端 **的 [設定** ] 和 [ **平臺** ] 清單來更新所有組建設定。 要更新的最重要設定是 **Release/iPhone** 設定，因為這通常用來建立 App Store 提交的組建。

您可以在此螢幕擷取畫面中看到具有新旗標的視窗：

[![在 iOS 組建區段中設定旗標](webview-images/iosbuildblade-vs-sml.png)](webview-images/iosbuildblade-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

請遵循下列步驟，讓連結器移除 `UIWebView` 參考：

1. **開啟 iOS 專案選項** &ndash; 以滑鼠右鍵按一下您的 iOS 專案，然後選擇 [ **選項**]。
1. **流覽至 [IOS 組建] 區段** &ndash; 選取 [ **IOS 組建** ] 區段。
1. **_mtouch_更新** &ndash; **其他_mtouch_引數**中的其他 mtouch 引數會將此旗標新增 `--optimize=experimental-xforms-product-type` (除了任何可能已存在) 中的值。 注意：此旗標會搭配設定為 [**僅 SDK** ] 或 [**全部連結**] 的**連結器行為**一起運作。 如果基於任何原因，您會在將連結器行為設定為 [全部] 時看到錯誤，這很可能是因為應用程式程式碼或協力廠商程式庫中的問題，而不是連結器安全。 如需連結器的詳細資訊，請參閱 [連結 Xamarin IOS 應用程式](~/ios/deploy-test/linker.md)。
1. **更新所有組建** &ndash; 設定您可以使用視窗頂端 **的 [設定** ] 和 [ **平臺** ] 清單來更新所有組建設定。 要更新的最重要設定是 **Release/iPhone** 設定，因為這通常用來建立 App Store 提交的組建。

您可以在此螢幕擷取畫面中看到具有新旗標的視窗：

[![在 iOS 組建區段中設定旗標](webview-images/iosbuildblade-xs-sml.png)](webview-images/iosbuildblade-xs.png#lightbox)

-----

現在，當您建立新的 (版本) 組建並提交到 App Store 時，就不應該對已淘汰的 API 產生任何警告。

## <a name="related-links"></a>相關連結

- [使用 Web (範例) ](/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [Web (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-webview)
- [UIWebView 淘汰](~/ios/user-interface/controls/webview.md#uiwebview-deprecation)
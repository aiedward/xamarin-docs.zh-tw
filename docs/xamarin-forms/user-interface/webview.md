---
title: Xamarin.FormsWebView
description: 本文說明如何使用 [web 工作] 類別，將 Xamarin.Forms 本機或網路 web 內容和檔呈現給使用者。
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9b07e044e55f99a7a183e55c566bf59dbd082655
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198161"
---
# <a name="xamarinforms-webview"></a>Xamarin.FormsWebView

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView)是在您的應用程式中顯示 web 和 HTML 內容的視圖：

![在應用程式瀏覽器中](webview-images/in-app-browser.png)

## <a name="content"></a>內容

`WebView`支援下列類型的內容：

- HTML & CSS 網站的 &ndash; web 程式可完全支援使用 HTML & css 撰寫的網站，包括 JavaScript 支援。
- 檔 &ndash; 是因為使用每個平臺上的原生元件來執行 web 流覽，所以 web 流覽可以顯示每個平臺上可查看的檔。 這表示 PDF 檔案適用于 iOS 和 Android。
- HTML 字串 &ndash; web 視圖可以顯示記憶體中的 html 字串。
- 本機檔案的 &ndash; web 工作，可以呈現應用程式中內嵌的任何內容類型。

> [!NOTE]
> `WebView`在 Windows 上不支援 Silverlight、Flash 或任何 ActiveX 控制項，即使該平臺上的 Internet Explorer 支援它們也一樣。

### <a name="websites"></a>網站

若要從網際網路顯示網站，請將 `WebView` 的 [`Source`](xref:Xamarin.Forms.WebViewSource) 屬性設定為字串 URL：

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Url 必須以指定的通訊協定完整格式（也就是它前面必須加上 "HTTP://" 或 "HTTPs://"）。

#### <a name="ios-and-ats"></a>iOS 和 ATS

自第9版起，iOS 只會允許您的應用程式與預設執行最佳做法安全性的伺服器進行通訊。 您必須在中設定值 `Info.plist` ，以啟用與不安全伺服器的通訊。

> [!NOTE]
> 如果您的應用程式需要連線到不安全的網站，您應該一律使用來輸入網域做為例外狀況， `NSExceptionDomains` 而不要使用來完全關閉 ATS `NSAllowsArbitraryLoads` 。 `NSAllowsArbitraryLoads`應該僅用於極緊急的情況。

以下示範如何啟用特定網域（在此案例中為 xamarin.com）以略過 ATS 需求：

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

最佳做法是只讓某些網域略過 ATS，讓您可以使用信任的網站，同時從不受信任的網域上的額外安全性進行受益。 以下示範針對應用程式停用 ATS 的較不安全方法：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

如需 iOS 9 中這項新功能的詳細資訊，請參閱[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)。

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

在上述程式碼中， `@` 是用來將 HTML 標籤為[逐字字串常](/dotnet/csharp/programming-guide/strings/#regular-and-verbatim-string-literals)值，這表示會忽略大部分的換用字元。

> [!NOTE]
> 您可能需要設定的 `WidthRequest` 和屬性， `HeightRequest` [`WebView`](xref:Xamarin.Forms.WebView) 以查看 HTML 內容，視配置是的子系而定 `WebView` 。 例如，在中，這是必要的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。

### <a name="local-html-content"></a>本機 HTML 內容

Web 工作可從應用程式內的 HTML、CSS 和 JavaScript 中顯示內容。 例如：

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

</C3>

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

若要使用顯示本機內容 `WebView` ，您必須開啟 HTML 檔案，就像任何其他檔案一樣，然後將內容當做字串載入至的 `Html` 屬性 `HtmlWebViewSource` 。 如需開啟檔案的詳細資訊，請參閱[使用](~/xamarin-forms/data-cloud/data/files.md)檔案。

下列螢幕擷取畫面顯示在每個平臺上顯示本機內容的結果：

![顯示本機內容的 Web 視圖](webview-images/local-content.png)

雖然已載入第一頁，但並 `WebView` 不知道 HTML 來自何處。 這是處理參考本機資源的頁面時的問題。 當本機頁面彼此連結時，網頁會使用個別的 JavaScript 檔案，或網頁連結至 CSS 樣式表單，這可能會發生的情況。  

若要解決這個問題，您需要告訴 `WebView` 哪裡可以在檔案系統上尋找檔案。 若要這麼做，請 `BaseUrl` 在所使用的上設定屬性 `HtmlWebViewSource` `WebView` 。

由於每個作業系統上的檔案系統不同，因此您必須在每個平臺上判斷該 URL。 Xamarin.Forms公開， `DependencyService` 以便在每個平臺上的執行時間解析相依性。

若要使用 `DependencyService` ，請先定義可以在每個平臺上執行的介面：

```csharp
public interface IBaseUrl { string Get(); }
```

請注意，在每個平臺上執行介面之前，應用程式都不會執行。 在一般專案中，請確定您記得使用來設定 `BaseUrl` `DependencyService` ：

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

接著，您必須提供每個平臺的介面的實現。

#### <a name="ios"></a>iOS

在 iOS 上，web 內容應該位於專案的根目錄或**Resources**目錄中，並具有組建動作*BundleResource*，如下所示：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![IOS 上的本機檔案](webview-images/ios-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![IOS 上的本機檔案](webview-images/ios-xs.png)

-----

`BaseUrl`應設定為主要配套的路徑：

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

在 Android 上，使用 [組建動作*AndroidAsset* ] 將 HTML、CSS 和影像放在 [資產] 資料夾中，如下所示：

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Android 上的本機檔案](webview-images/android-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![Android 上的本機檔案](webview-images/android-xs.png)

-----

在 Android 上， `BaseUrl` 應該設定為 `"file:///android_asset/"` ：

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

在 Android 上，[**資產**] 資料夾中的檔案也可以透過由屬性公開的目前 Android 內容來存取 `MainActivity.Instance` ：

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平臺（UWP）專案上，將 HTML、CSS 和影像放在專案根目錄中，並將 [組建] 動作設定為 [*內容*]。

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

## <a name="navigation"></a>瀏覽

Web 程式支援透過數個可用的方法和屬性進行流覽：

- **GoForward （）** &ndash;如果 `CanGoForward` 為 true，則呼叫會 `GoForward` 向前流覽至下一個造訪的頁面。
- **GoBack （）** &ndash;如果 `CanGoBack` 為 true，則呼叫 `GoBack` 將會流覽至最後一個造訪的頁面。
- **CanGoBack** &ndash;`true`如果有頁面可流覽回，則為; `false` 如果瀏覽器位於起始 URL，則為。
- **CanGoForward** &ndash;`true`如果使用者已回溯流覽，並可移至已經流覽過的頁面。

在頁面內，不 `WebView` 支援多點觸控手勢。 請務必確定內容已進行行動裝置優化，並出現而不需要縮放。

應用程式通常會顯示內的連結 `WebView` ，而不是裝置的瀏覽器。 在這些情況下，允許正常導覽是很有用的，但是當使用者在開始連結上叫用時，應用程式應該會回到一般的應用程式視圖。

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

在程式碼後置中：

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

完成了！

![Web 流覽導覽按鈕](webview-images/in-app-browser.png)

## <a name="events"></a>事件

Web 工作會引發下列事件，以協助您回應狀態的變更：

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating)–當 Web 工作開始載入新頁面時引發的事件。
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated)–載入頁面並停止導覽時引發的事件。
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested)–提出要求以重載目前內容時引發的事件。

[`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs)事件隨附的物件 [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) 有四個屬性：

- `Cancel`–指出是否要取消導覽。
- `NavigationEvent`–引發的導覽事件。
- `Source`–執行導覽的元素。
- `Url`–導覽目的地。

[`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs)事件隨附的物件 [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) 有四個屬性：

- `NavigationEvent`–引發的導覽事件。
- `Result`–使用列舉成員來描述導覽的結果 [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) 。 有效值為 `Cancel`、`Failure``Success` 及 `Timeout`。
- `Source`–執行導覽的元素。
- `Url`–導覽目的地。

如果您預期使用需要較長時間載入的網頁，請考慮使用 [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) 和 [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) 事件來執行狀態指示器。 例如：

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

這會導致下列輸出（載入）：

![Web 流覽事件範例](webview-images/loading-start.png)

完成載入：

![Web 流覽事件範例](webview-images/loading-end.png)

## <a name="reloading-content"></a>重載內容

[`WebView`](xref:Xamarin.Forms.WebView)具有 `Reload` 可用於重載目前內容的方法：

```csharp
var webView = new WebView();
...
webView.Reload();
```

叫 `Reload` 用方法時 `ReloadRequested` ，會引發事件，表示已提出要求來重載目前的內容。

## <a name="performance"></a>效能

熱門的 web 瀏覽器採用硬體加速轉譯和 JavaScript 編譯等技術。 在 Xamarin.Forms 4.4 之前， Xamarin.Forms `WebView` 是由類別在 iOS 上執行 `UIWebView` 。 不過，這其中許多技術在此實行中無法使用。 因此，自 Xamarin.Forms 4.4 起， Xamarin.Forms `WebView` 會透過類別在 iOS 上執行， `WkWebView` 以支援更快速的流覽。

> [!NOTE]
> 在 iOS 上， `WkWebViewRenderer` 具有接受引數的函數多載 `WkWebViewConfiguration` 。 這可讓轉譯器在建立時設定。

基於 `UIWebView` Xamarin.Forms `WebView` 相容性的考慮，應用程式可以傳回使用 iOS 類別來執行。 將下列程式碼新增至應用程式的 iOS 平臺專案中的**AssemblyInfo.cs**檔案，即可達成此目的：

```csharp
// Opt-in to using UIWebView instead of WkWebView.
[assembly: ExportRenderer(typeof(Xamarin.Forms.WebView), typeof(Xamarin.Forms.Platform.iOS.WebViewRenderer))]
```

`WebView`在 Android 上，預設與內建瀏覽器的速度一樣快。

[UWP web](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view)工作會使用 Microsoft Edge 轉譯引擎。 桌面和平板電腦裝置應該會看到與使用 Edge 瀏覽器本身相同的效能。

## <a name="permissions"></a>權限

為了讓 `WebView` 能夠正常執行，您必須確定已針對每個平臺設定許可權。 請注意，在某些平臺上， `WebView` 會在「偵錯工具」模式下工作，但不會在建立發行時使用。 這是因為某些許可權（例如 Android 上的網際網路存取）預設會在處於「偵錯工具」模式時 Visual Studio for Mac 來設定。

- **UWP** &ndash;顯示網路內容時需要網際網路（用戶端 & 伺服器）功能。
- **Android** &ndash;`INTERNET`只有在顯示來自網路的內容時才需要。 本機內容不需要任何特殊許可權。
- **iOS** &ndash;不需要任何特殊許可權。

## <a name="layout"></a>Layout

不同于大部分其他 Xamarin.Forms 的視圖， `WebView` 會要求在 `HeightRequest` `WidthRequest` 包含在 StackLayout 或 RelativeLayout 時指定和。 如果您無法指定這些屬性， `WebView` 將不會呈現。

下列範例示範會導致運作的版面配置，轉譯 `WebView` s：

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

AbsoluteLayout*不含*WidthRequest & HeightRequest：

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

*沒有*WidthRequest 的方格 & HeightRequest。 方格是其中一種不需要指定要求的高度和寬度的版面配置。：

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

[`WebView`](xref:Xamarin.Forms.WebView)包含從 c # 叫用 JavaScript 函式，並將任何結果傳回給呼叫 c # 程式碼的功能。 這是使用方法來完成，如 [Web 工作] [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) 範例中的下列[WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)範例所示：

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

[`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*)方法會評估指定為引數的 JavaScript，並以的形式傳回任何結果 `string` 。 在此範例中，會叫用 JavaScript 函式，此函式 `factorial` 會傳回結果的階乘 `number` 。 這個 JavaScript 函式會在載入的本機 HTML 檔案中定義 [`WebView`](xref:Xamarin.Forms.WebView) ，如下列範例所示：

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

Cookie 可以在上設定 [`WebView`](xref:Xamarin.Forms.WebView) ，然後透過 web 要求傳送至指定的 URL。 這是藉由將 `Cookie` 物件新增至 `CookieContainer` ，然後將它設定為可系結屬性的值來完成 `WebView.Cookies` 。 下列程式碼顯示這種情況的範例：

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

在此範例中，會將單一 `Cookie` 新增至 `CookieContainer` 物件，然後將它設定為屬性的值 `WebView.Cookies` 。 當將 [`WebView`](xref:Xamarin.Forms.WebView) web 要求傳送至指定的 URL 時，cookie 會隨要求一起傳送。

## <a name="uiwebview-deprecation-and-app-store-rejection-itms-90809"></a>UIWebView 淘汰和 App Store 拒絕（ITMS-90809）

從2020年4月開始， [Apple 會拒絕](https://developer.apple.com/news/?id=12232019b)仍使用已被取代之 API 的應用程式 `UIWebView` 。 雖然 Xamarin.Forms 已切換至 `WKWebView` 做為預設值，但仍有二進位檔案中舊版 SDK 的參考 Xamarin.Forms 。 目前的[iOS 連結器](~/ios/deploy-test/linker.md)行為並不會移除此動作，因此， `UIWebView` 當您提交至 app Store 時，已淘汰的 API 仍會顯示為從您的應用程式參考。

連結器的預覽版本可用來修正此問題。 若要啟用預覽，您必須提供連結器的額外引數 `--optimize=experimental-xforms-product-type` 。

此作業的必要條件如下：

- ** Xamarin.Forms 4.5 或更高版本**。 Xamarin.Forms如果您的應用程式使用材質視覺效果，則需要4.6 或更高版本。
- **13.10.0.17 或更高版本**。 檢查[Visual Studio 中](~/cross-platform/troubleshooting/questions/version-logs.md#version-information)的 Xamarin. iOS 版本。 這個版本的 Xamarin 隨附于 Visual Studio for Mac dbms-guide-8.4.1 和 Visual Studio 16.4.3 中。
- **移除對的 `UIWebView` 參考**。 您的程式碼不應該有任何對的參考， `UIWebView` 或任何利用的類別 `UIWebView` 。

如需偵測和移除參考的詳細資訊 `UIWebView` ，請參閱[UIWebView](~/ios/user-interface/controls/webview.md#uiwebview-deprecation)取代。

### <a name="configure-the-linker"></a>設定連結器

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

請遵循下列步驟來讓連結器移除 `UIWebView` 參考：

1. **開啟 iOS 專案屬性** &ndash;以滑鼠右鍵按一下您的 iOS 專案，然後選擇 [**屬性**]。
1. **流覽至 IOS 組建區段** &ndash;選取 [ **IOS 組建**] 區段。
1. **更新其他 mtouch 引數** &ndash;在 [**其他 mtouch 引數**] 中，新增此旗標 `--optimize=experimental-xforms-product-type` （除了任何可能已存在的值）。 注意：此旗標可搭配設定為 [**僅限 SDK** ] 或 [**全部連結**] 的**連結器行為**一起運作。 如果您基於任何原因，在將連結器行為設定為 [全部] 時看到錯誤，這很可能是應用程式代碼或不是連結器安全之協力廠商程式庫內的問題。 如需連結器的詳細資訊，請參閱[連結 Xamarin IOS 應用程式](~/ios/deploy-test/linker.md)。
1. **更新所有組建** &ndash; 設定使用視窗頂端的 [設定]**和 [** **平臺**] 清單來更新所有組建設定。 更新的最重要設定是**Release/iPhone**設定，因為這通常是用來建立 App Store 提交的組建。

在此螢幕擷取畫面中，您可以看到具有新旗標的視窗：

[![在 iOS 組建區段中設定旗標](webview-images/iosbuildblade-vs-sml.png)](webview-images/iosbuildblade-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

請遵循下列步驟來讓連結器移除 `UIWebView` 參考：

1. **開啟 iOS 專案選項** &ndash;以滑鼠右鍵按一下您的 iOS 專案，然後選擇 [**選項**]。
1. **流覽至 IOS 組建區段** &ndash;選取 [ **IOS 組建**] 區段。
1. **更新_mtouch_ ** &ndash; **其他_mtouch_引數**中的其他 mtouch 引數加入此旗標 `--optimize=experimental-xforms-product-type` （除了其中可能已存在的任何值）。 注意：此旗標可搭配設定為 [**僅限 SDK** ] 或 [**全部連結**] 的**連結器行為**一起運作。 如果您基於任何原因，在將連結器行為設定為 [全部] 時看到錯誤，這很可能是應用程式代碼或不是連結器安全之協力廠商程式庫內的問題。 如需連結器的詳細資訊，請參閱[連結 Xamarin IOS 應用程式](~/ios/deploy-test/linker.md)。
1. **更新所有組建** &ndash; 設定使用視窗頂端的 [設定]**和 [** **平臺**] 清單來更新所有組建設定。 更新的最重要設定是**Release/iPhone**設定，因為這通常是用來建立 App Store 提交的組建。

在此螢幕擷取畫面中，您可以看到具有新旗標的視窗：

[![在 iOS 組建區段中設定旗標](webview-images/iosbuildblade-xs-sml.png)](webview-images/iosbuildblade-xs.png#lightbox)

-----

現在，當您建立新的（發行）組建並提交至 App Store 時，應該不會有關于已被取代之 API 的警告。

## <a name="related-links"></a>相關連結

- [使用 Web 工作（sample）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [Web 視圖（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
- [UIWebView 淘汰](~/ios/user-interface/controls/webview.md#uiwebview-deprecation)

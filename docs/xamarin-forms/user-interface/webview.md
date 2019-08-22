---
title: Xamarin.Forms web 檢視
description: 這篇文章會說明如何使用 Xamarin.Forms WebView 類別來提供本機或網路 web 內容和文件給使用者。
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2019
ms.openlocfilehash: 65bcd9f534685a8a953d217d3573f5fe4b0b7400
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887923"
---
# <a name="xamarinforms-webview"></a>Xamarin.Forms web 檢視

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) 會顯示在您的應用程式中的 web 和 HTML 內容的檢視。 不同於`OpenUri`，其採用使用者裝置上的網頁瀏覽器`WebView`會顯示在您的應用程式內的 HTML 內容。

![](webview-images/in-app-browser.png "在應用程式的瀏覽器中")

## <a name="content"></a>內容

`WebView` 支援下列類型的內容：

- HTML 和 CSS 網站&ndash;WebView 具有完整支援使用 HTML 和 CSS，包括支援 JavaScript 撰寫的網站。
- 文件&ndash;WebView 實作在每個平台上使用原生元件，因為 web 檢視可以顯示每個平台可檢視的文件。 也就是說，PDF 檔案是在 iOS 和 Android 上運作。
- HTML 字串&ndash;WebView 可以顯示從記憶體的 HTML 字串。
- 本機檔案&ndash;WebView 可以呈現內嵌於應用程式中任何上述內容的類型。

> [!NOTE]
> `WebView` 在 Windows 上不支援 Silverlight、 Flash 或任何 ActiveX 控制項，即使該平台所支援的 Internet explorer。

### <a name="websites"></a>網站

若要顯示來自網際網路的網站，請設定`WebView`的[ `Source` ](xref:Xamarin.Forms.WebViewSource)屬性至字串的 URL:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Url 必須使用完整格式與指定的通訊協定 （也就是它必須有"http://"或"https://"附加到它）。

#### <a name="ios-and-ats"></a>iOS 和 ATS

第 9 版，因為 iOS 只允許您的應用程式的預設實作的安全性最佳做法的伺服器通訊。 值必須設定`Info.plist`能夠與不安全的伺服器通訊。

> [!NOTE]
> 如果您的應用程式必須連線至不安全的網站，您應該一律使用例外狀況的形式輸入網域`NSExceptionDomains`而不是完全使用時，關閉 ATS `NSAllowsArbitraryLoads`。 `NSAllowsArbitraryLoads` 應該只用在極端的緊急情況下。

以下示範如何啟用特定網域中 （在此案例的 xamarin.com)，以略過 ATS 需求：

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

它是只讓某些網域略過 ATS，讓您可以使用信任的網站，同時享有不受信任網域上的其他安全性的最佳作法。 以下示範對應用程式停用 ATS 較不安全的方式：

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)如需 iOS 9 中這項新功能詳細資訊。

### <a name="html-strings"></a>HTML 字串

如果您想要呈現的 HTML 程式碼中以動態方式定義的字串，您必須建立的執行個體[ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![](webview-images/html-string.png "Web 檢視顯示的 HTML 字串")

在上述程式碼中，`@`用來將 HTML 標記做為字串常值，這表示所有一般的逸出字元會被忽略。

> [!NOTE]
> 您`WidthRequest`可能需要設定的和`HeightRequest`屬性[`WebView`](xref:Xamarin.Forms.WebView) , 以查看`WebView` HTML 內容, 視配置是的子系而定。 例如, 在中[`StackLayout`](xref:Xamarin.Forms.StackLayout), 這是必要的。

### <a name="local-html-content"></a>本機的 HTML 內容

Web 檢視可顯示內容從 HTML、 CSS 和 Javascript 內嵌在應用程式。 例如:

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

請注意，上述的 CSS 中指定的字型必須是可自訂的每個平台，因為並非所有平台都有相同的字型。

若要顯示本機內容使用`WebView`，您必須開啟 HTML 檔案，如同任何其他的然後將內容載入到字串形式`Html`屬性`HtmlWebViewSource`。 如需有關開啟檔案的詳細資訊，請參閱[使用檔案](~/xamarin-forms/data-cloud/data/files.md)。

下列螢幕擷取畫面顯示每個平台上顯示本機內容的結果：

![](webview-images/local-content.png "WebView 顯示本機內容")

雖然已載入的第一頁，`WebView`一無所知的 HTML 來自何處。 處理參考本機資源的頁面時，這會是問題。 本機的頁面連結至每個其他，頁面會使用個別的 JavaScript 檔案，或頁面會連結到 CSS 樣式表時，就會加入範例時，可能會發生。  

若要解決這個問題，您需要告訴`WebView`何處可找到檔案系統上的檔案。 達成`BaseUrl`上的屬性`HtmlWebViewSource`供`WebView`。

因為檔案系統上每個作業系統都不同，您必須判斷每個平台上的該 URL。 Xamarin.Forms 會公開`DependencyService`解決在各平台上的執行階段的相依性。

若要使用`DependencyService`，首先會定義每個平台實作的介面：

```csharp
public interface IBaseUrl { string Get(); }
```

請注意，直到每個平台上實作的介面時，應用程式將不會執行。 在一般專案中，請確定您記得設`BaseUrl`使用`DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

然後必須提供每個平台介面的實作。

#### <a name="ios"></a>iOS

在 iOS 上，web 內容應該位於專案的根目錄或**資源**建置動作目錄*BundleResource*，如下所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](webview-images/ios-vs.png "在 iOS 上的本機檔案")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](webview-images/ios-xs.png "在 iOS 上的本機檔案")

-----

`BaseUrl`應該設定為主要的套件組合的路徑：

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

在 Android 上，放在建置動作的 [Assets] 資料夾中的 HTML、 CSS 和映像*AndroidAsset*如下所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](webview-images/android-vs.png "在 Android 上的本機檔案")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](webview-images/android-xs.png "在 Android 上的本機檔案")

-----

在 Android 上，`BaseUrl`應該設定為`"file:///android_asset/"`:

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

在 Android 上，檔案中**資產**也可以透過目前的 Android 內容，由存取資料夾`MainActivity.Instance`屬性：

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>通用 Windows 平台

用於通用 Windows 平台 (UWP) 專案，將 HTML、 CSS 和映像的專案根目錄中建置動作設為*內容*。

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

WebView 支援瀏覽一些方法和屬性，可讓：

- **GoForward()** &ndash;如果`CanGoForward`為 true，呼叫`GoForward`向前巡覽至下一步 瀏覽的網頁。
- **GoBack()** &ndash;如果`CanGoBack`為 true，呼叫`GoBack`會瀏覽至最後一個瀏覽的網頁。
- **CanGoBack** &ndash; `true`如果有頁面瀏覽至，`false`如果瀏覽器起始 url。
- **CanGoForward** &ndash; `true`如果使用者已向後巡覽，並可以移至已經瀏覽過的頁面。

中的頁面，`WebView`不支援多點觸控筆勢。 請務必要確定該內容是行動裝置最佳化並隨即出現，而不需要縮放。

它是很常見的應用程式顯示中的連結`WebView`，而不是裝置的瀏覽器。 在這些情況下，有助於讓一般的瀏覽，但當使用者叫用備份開始的連結時，應用程式應該傳回至一般應用程式檢視。

若要啟用此案例中使用的內建導覽功能的方法和屬性。

開始建立瀏覽器檢視的頁面：

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

在 程式碼後置：

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

就這麼容易！

![](webview-images/in-app-browser.png "WebView 瀏覽按鈕")

## <a name="events"></a>事件

WebView 中，會引發下列事件，以協助您回應狀態的變更：

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating)–當 Web 工作開始載入新頁面時引發的事件。
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated)–載入頁面並停止導覽時引發的事件。
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested)–提出要求以重載目前內容時引發的事件。

事件隨附的物件有四個屬性: [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) [`Navigating`](xref:Xamarin.Forms.WebView.Navigating)

- `Cancel`–指出是否要取消導覽。
- `NavigationEvent`–引發的導覽事件。
- `Source`–執行導覽的元素。
- `Url`–導覽目的地。

事件隨附的物件有四個屬性: [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) [`Navigated`](xref:Xamarin.Forms.WebView.Navigated)

- `NavigationEvent`–引發的導覽事件。
- `Result`–使用[`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult)列舉成員來描述導覽的結果。 有效值為 `Cancel`、`Failure`、`Success` 和 `Timeout`。
- `Source`–執行導覽的元素。
- `Url`–導覽目的地。

如果您預期使用需要較長時間載入的網頁, 請考慮使用[`Navigating`](xref:Xamarin.Forms.WebView.Navigating)和[`Navigated`](xref:Xamarin.Forms.WebView.Navigated)事件來執行狀態指示器。 例如：

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

兩個事件處理常式中：

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

這會導致下列輸出 （正在載入）：

![](webview-images/loading-start.png "WebView 瀏覽事件範例")

完成的載入：

![](webview-images/loading-end.png "WebView 巡覽事件範例")

## <a name="reloading-content"></a>重新載入內容

[`WebView`](xref:Xamarin.Forms.WebView) 具有`Reload`方法，可用來重新載入目前的內容：

```csharp
var webView = new WebView();
...
webView.Reload();
```

當`Reload`方法會叫用`ReloadRequested`引發事件時，表示要求，已重新載入目前的內容。

## <a name="performance"></a>效能

熱門的 web 瀏覽器現在採用技術，例如硬體加速呈現和 JavaScript 編譯。 在 iOS 上，依預設，Xamarin.Forms`WebView`藉由`UIWebView`類別，而許多這些技術會在此實作中，您無法使用。 不過，應用程式可以選擇加入，以在使用 iOS`WkWebView`類別來實作 Xamarin.Forms `WebView`，可支援更快速瀏覽。 這可以藉由新增下列程式碼，來達成**AssemblyInfo.cs**應用程式在 iOS 平台專案檔：

```csharp
// Opt-in to using WkWebView instead of UIWebView.
[assembly: ExportRenderer(typeof(WebView), typeof(Xamarin.Forms.Platform.iOS.WkWebViewRenderer))]
```

> [!NOTE]
> 在 iOS 上, `WkWebViewRenderer`具有`WkWebViewConfiguration`接受引數的函數多載。 這可讓轉譯器在建立時設定。

`WebView` 依預設在 Android 上是做為內建的瀏覽器差不多一樣快。

[UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view)使用 Microsoft Edge 轉譯引擎。 桌上型電腦和平板電腦裝置應該會看到相同的效能與使用 Edge 瀏覽器本身。

## <a name="permissions"></a>權限

為了讓`WebView`運作，您必須確定每個平台已設定權限。 請注意，在某些平台，`WebView`在偵錯模式中，但只有在建置要發行時，不會運作。 這是因為某些權限，例如，適用於網際網路存取，在 Android 上，依預設是由 Visual Studio for Mac 中偵錯模式時設定。

- **UWP** &ndash;顯示網路內容時，需要網際網路 （用戶端和伺服器） 功能。
- **Androi** &ndash;只有需要顯示來自網路的內容時，才需要`INTERNET`。 本機內容需要任何特殊權限。
- **iOS** &ndash;需要任何特殊權限。

## <a name="layout"></a>配置

不同於大部分其他 Xamarin.Forms 檢視`WebView`要求`HeightRequest`和`WidthRequest`時 StackLayout 或 RelativeLayout 中包含所指定。 如果您無法指定這些屬性，`WebView`不會呈現。

下列範例示範工作，會導致的版面配置轉譯`WebView`s:

WidthRequest 與 HeightRequest StackLayout:

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

WidthRequest 與 HeightRequest RelativeLayout:

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

AbsoluteLayout*而不需要*WidthRequest & HeightRequest:

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

方格*而不需要*WidthRequest & HeightRequest。 格線是其中一個不需要指定所要求的高度和寬度的幾個配置。:

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

## <a name="invoking-javascript"></a>叫用的 JavaScript

[`WebView`](xref:Xamarin.Forms.WebView) 包括能夠叫用的 JavaScript 函式，從C#，並傳回任何結果呼叫C#程式碼。 這可完成[ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*)方法，從下列範例所示[WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)範例：

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

[ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*)方法會評估指定為引數，並傳回任何結果為 JavaScript `string`。 在此範例中， `factorial` JavaScript 函式會叫用，它會傳回的階乘`number`結果。 此 JavaScript 函式定義於本機 HTML 檔案[ `WebView` ](xref:Xamarin.Forms.WebView)載入時，與下列範例所示：

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

## <a name="related-links"></a>相關連結

- [使用 WebView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [Web 檢視 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)

---
title: Xamarin. Forms Web 視圖
description: 本文說明如何使用 Xamarin Forms 類別，向使用者呈現本機或網路 web 內容和檔。
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
ms.openlocfilehash: dedce45d0c09f807aaf2ecbf540b8c9f319a4f16
ms.sourcegitcommit: 3e94c6d2b6d6a70c94601e7bf922d62c4a6c7308
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "76031407"
---
# <a name="xamarinforms-webview"></a>Xamarin. Forms Web 視圖

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView)是在您的應用程式中顯示 WEB 和 HTML 內容的視圖：

![在應用程式瀏覽器中](webview-images/in-app-browser.png)

## <a name="content"></a>內容

`WebView` 支援下列類型的內容：

- HTML & CSS 網站 &ndash; Web 服務對於使用 HTML & CSS 撰寫的網站有完整的支援，包括 JavaScript 支援。
- 檔 &ndash; 因為使用每個平臺上的原生元件來執行 Web 流覽，所以 Web 程式可顯示每個平臺上可查看的檔。 這表示 PDF 檔案適用于 iOS 和 Android。
- HTML 字串 &ndash; Web 視圖可以顯示記憶體中的 HTML 字串。
- 本機檔案 &ndash; 的 Web 工作，可以呈現應用程式中內嵌的任何內容類型。

> [!NOTE]
> Windows 上的 `WebView` 不支援 Silverlight、Flash 或任何 ActiveX 控制項，即使該平臺上的 Internet Explorer 支援也一樣。

### <a name="websites"></a>Websites

若要從網際網路顯示網站，請將 `WebView`的[`Source`](xref:Xamarin.Forms.WebViewSource)屬性設定為字串 URL：

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Url 必須以指定的通訊協定完整格式（也就是它前面必須加上 "HTTP://" 或 "HTTPs://"）。

#### <a name="ios-and-ats"></a>iOS 和 ATS

自第9版起，iOS 只會允許您的應用程式與預設執行最佳做法安全性的伺服器進行通訊。 您必須在 `Info.plist` 中設定值，以啟用與不安全伺服器的通訊。

> [!NOTE]
> 如果您的應用程式需要連接到不安全的網站，您應該一律使用 `NSExceptionDomains` 將該網域輸入為例外狀況，而不是使用 `NSAllowsArbitraryLoads`完全關閉 ATS。 `NSAllowsArbitraryLoads` 應該僅用於極緊急的情況。

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

如果您想要呈現在程式碼中動態定義的 HTML 字串，您必須建立[`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource)的實例：

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

在上述程式碼中，`@` 是用來將 HTML 標籤為字串常值，這表示會忽略所有一般的逸出字元。

> [!NOTE]
> 您可能必須設定[`WebView`](xref:Xamarin.Forms.WebView)的 `WidthRequest` 和 `HeightRequest` 屬性，才能看到 HTML 內容，視 `WebView` 是的子配置而定。 例如，在[`StackLayout`](xref:Xamarin.Forms.StackLayout)中，這是必要的。

### <a name="local-html-content"></a>本機 HTML 內容

Web 工作可從應用程式內的 HTML、CSS 和 JAVAscript 中顯示內容。 例如：

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

請注意，在上述 CSS 中指定的字型必須針對每個平臺自訂，因為並非每個平臺都有相同的字型。

若要使用 `WebView`顯示本機內容，您需要開啟 HTML 檔案，就像任何其他檔案一樣，然後將內容當做字串載入至 `HtmlWebViewSource`的 `Html` 屬性。 如需開啟檔案的詳細資訊，請參閱[使用](~/xamarin-forms/data-cloud/data/files.md)檔案。

下列螢幕擷取畫面顯示在每個平臺上顯示本機內容的結果：

![顯示本機內容的 Web 視圖](webview-images/local-content.png)

雖然已載入第一頁，但 `WebView` 並不知道 HTML 來自何處。 這是處理參考本機資源的頁面時的問題。 當本機頁面彼此連結時，網頁會使用個別的 JavaScript 檔案，或網頁連結至 CSS 樣式表單，這可能會發生的情況。  

若要解決此問題，您必須告訴 `WebView` 在檔案系統上尋找檔案的位置。 若要這麼做，請在 `WebView`所使用的 `HtmlWebViewSource` 上設定 [`BaseUrl`] 屬性。

由於每個作業系統上的檔案系統不同，因此您必須在每個平臺上判斷該 URL。 Xamarin 會公開 `DependencyService`，以便在每個平臺上的執行時間解析相依性。

若要使用 `DependencyService`，請先定義可在每個平臺上執行的介面：

```csharp
public interface IBaseUrl { string Get(); }
```

請注意，在每個平臺上執行介面之前，應用程式都不會執行。 在一般專案中，請務必記得使用 `DependencyService`來設定 `BaseUrl`：

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

接著，您必須提供每個平臺的介面的實現。

#### <a name="ios"></a>iOS

在 iOS 上，web 內容應該位於專案的根目錄或**Resources**目錄中，並具有組建動作*BundleResource*，如下所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![IOS 上的本機檔案](webview-images/ios-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![IOS 上的本機檔案](webview-images/ios-xs.png)

-----

`BaseUrl` 應設定為主要配套的路徑：

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

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Android 上的本機檔案](webview-images/android-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![Android 上的本機檔案](webview-images/android-xs.png)

-----

在 Android 上，`BaseUrl` 應該設定為 `"file:///android_asset/"`：

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

在 Android 上，[**資產**] 資料夾中的檔案也可以透過 `MainActivity.Instance` 屬性公開的目前 Android 內容來存取：

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平臺（UWP）專案上，將 HTML、CSS 和影像放在專案根目錄中，並將 [組建] 動作設定為 [*內容*]。

`BaseUrl` 應設定為 `"ms-appx-web:///"`：

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

- **GoForward （）** &ndash; 如果 `CanGoForward` 為 true，則呼叫 `GoForward` 會向前流覽至下一個造訪的頁面。
- **GoBack （）** &ndash; 如果 `CanGoBack` 為 true，則呼叫 `GoBack` 將會流覽至最後造訪的頁面。
- **CanGoBack** &ndash; `true` 如果有頁面可流覽回，`false` 如果瀏覽器位於起始 URL。
- **CanGoForward** &ndash; `true` 使用者是否已回溯流覽，並可繼續前往已造訪的頁面。

在頁面中，`WebView` 不支援多點觸控手勢。 請務必確定內容已進行行動裝置優化，並出現而不需要縮放。

應用程式通常會在 `WebView`（而不是裝置的瀏覽器）中顯示連結。 在這些情況下，允許正常導覽是很有用的，但是當使用者在開始連結上叫用時，應用程式應該會回到一般的應用程式視圖。

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

就這麼容易！

![Web 流覽導覽按鈕](webview-images/in-app-browser.png)

## <a name="events"></a>「事件」

Web 工作會引發下列事件，以協助您回應狀態的變更：

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) –當 web 工作開始載入新頁面時，所引發的事件。
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) –載入頁面並停止導覽時引發的事件。
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested) –要求重載目前內容時所引發的事件。

伴隨[`Navigating`](xref:Xamarin.Forms.WebView.Navigating)事件的[`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs)物件有四個屬性：

- `Cancel` –指出是否要取消導覽。
- `NavigationEvent` –引發的導覽事件。
- `Source` –執行導覽的元素。
- `Url` –導覽目的地。

伴隨[`Navigated`](xref:Xamarin.Forms.WebView.Navigated)事件的[`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs)物件有四個屬性：

- `NavigationEvent` –引發的導覽事件。
- `Result` –使用[`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult)列舉成員來描述導覽的結果。 有效值為 `Cancel`、`Failure`、`Success` 和 `Timeout`。
- `Source` –執行導覽的元素。
- `Url` –導覽目的地。

如果您預期使用需要較長時間載入的網頁，請考慮使用[`Navigating`](xref:Xamarin.Forms.WebView.Navigating)和[`Navigated`](xref:Xamarin.Forms.WebView.Navigated)事件來執行狀態指示器。 例如：

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

[`WebView`](xref:Xamarin.Forms.WebView)具有 `Reload` 方法，可用於重載目前的內容：

```csharp
var webView = new WebView();
...
webView.Reload();
```

叫用 `Reload` 方法時，`ReloadRequested` 事件會引發，表示已提出要求來重載目前的內容。

## <a name="performance"></a>效能

熱門的 web 瀏覽器採用硬體加速轉譯和 JavaScript 編譯等技術。 在 Xamarin. 表單4.4 之前，`UIWebView` 類別會在 iOS 上執行 Xamarin. Forms `WebView`。 不過，這其中許多技術在此實行中無法使用。 因此，自 Xamarin. 表單4.4 之後，就會透過 `WkWebView` 類別在 iOS 上執行 Xamarin. Forms `WebView`，以支援更快速的流覽。

> [!NOTE]
> 在 iOS 上，`WkWebViewRenderer` 具有接受 `WkWebViewConfiguration` 引數的函數多載。 這可讓轉譯器在建立時設定。

基於相容性的考慮，應用程式可以使用 iOS `UIWebView` 類別來執行 Xamarin `WebView`。 將下列程式碼新增至應用程式的 iOS 平臺專案中的**AssemblyInfo.cs**檔案，即可達成此目的：

```csharp
// Opt-in to using UIWebView instead of WkWebView.
[assembly: ExportRenderer(typeof(Xamarin.Forms.WebView), typeof(Xamarin.Forms.Platform.iOS.WebViewRenderer))]
```

根據預設，Android 上的 `WebView` 與內建瀏覽器的速度一樣快。

[UWP web](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view)工作會使用 Microsoft Edge 轉譯引擎。 桌面和平板電腦裝置應該會看到與使用 Edge 瀏覽器本身相同的效能。

## <a name="permissions"></a>權限

為了讓 `WebView` 能夠正常執行，您必須確定已針對每個平臺設定許可權。 請注意，在某些平臺上，`WebView` 會在「偵錯工具」模式下工作，但在建立發行時不會使用。 這是因為某些許可權（例如 Android 上的網際網路存取）預設會在處於「偵錯工具」模式時 Visual Studio for Mac 來設定。

- **UWP** &ndash; 在顯示網路內容時需要網際網路（用戶端 & 伺服器）功能。
- 只有在從網路顯示內容時， **Android** &ndash; 才需要 `INTERNET`。 本機內容不需要任何特殊許可權。
- **iOS** &ndash; 不需要任何特殊許可權。

## <a name="layout"></a>配置

不同于大部分其他的 Xamarin. 表單檢視，`WebView` 需要在 StackLayout 或 RelativeLayout 中包含時，指定 `HeightRequest` 和 `WidthRequest`。 如果您無法指定這些屬性，則不會呈現 `WebView`。

下列範例示範會導致運作的版面配置、轉譯 `WebView`s：

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

[`WebView`](xref:Xamarin.Forms.WebView)包括從C#叫用 JavaScript 函式，並將任何結果傳回給呼叫C#程式碼的功能。 這項作業是使用[`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*)方法來完成，如來自[web](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)工作範例的下列範例所示：

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

[`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*)方法會評估指定為引數的 JavaScript，並以 `string`的形式傳回任何結果。 在此範例中，會叫用 `factorial` JavaScript 函數，以傳回 `number` 的階乘。 這個 JavaScript 函式定義于[`WebView`](xref:Xamarin.Forms.WebView)載入的本機 HTML 檔案中，如下列範例所示：

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

## <a name="uiwebview-deprecation-and-app-store-rejection-itms-90809"></a>UIWebView 淘汰和 App Store 拒絕（ITMS-90809）

從2020年4月開始， [Apple 會拒絕](https://developer.apple.com/news/?id=12232019b)仍使用已被取代之 `UIWebView` API 的應用程式。 雖然 Xamarin 已切換至 `WKWebView` 做為預設值，但是在 Xamarin 中，仍有舊版 SDK 的參考。 目前的[iOS 連結器](~/ios/deploy-test/linker.md)行為並不會移除此動作，因此，當您提交至 app Store 時，已淘汰的 `UIWebView` API 仍然會在應用程式中被參考。

連結器的預覽版本可用來修正此問題。 若要啟用預覽，您將需要提供額外的引數 `--optimize=experimental-xforms-product-type` 至連結器。 

此作業的必要條件如下：

- Xamarin.**表單4.5 或更高**版本 &ndash; Xamarin 的預先發行版本。您可以使用表單4.5。
- **13.10.0.17 或更新版本**&ndash; 檢查[Visual Studio 中](~/cross-platform/troubleshooting/questions/version-logs.md#version-information)的 xamarin. ios 版本。 這個版本的 Xamarin 隨附于 Visual Studio for Mac dbms-guide-8.4.1 和 Visual Studio 16.4.3 中。
- **移除 `UIWebView`的參考**&ndash; 您的程式碼不應該有任何 `UIWebView` 的參考，或是任何使用 `UIWebView`的類別。

### <a name="configure-the-linker-preview"></a>設定連結器預覽

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

請遵循下列步驟來讓連結器移除 `UIWebView` 參考：

1. **開啟 ios 專案屬性**&ndash; 以滑鼠右鍵按一下您的 ios 專案，然後選擇 [**屬性**]。
1. **流覽至 [Ios 組建] 區段**&ndash; 選取 [ **ios 組建**] 區段。
1. 更新其他**mtouch 引數**&ndash; 中**的其他 mtouch 引數**，將此旗標新增 `--optimize=experimental-xforms-product-type` （除了任何可能已存在的值中）。 
1. **更新所有組建**設定 &ndash; 使用視窗頂端**的 [設定**] 和 [**平臺**] 清單來更新所有組建設定。 更新的最重要設定是**Release/iPhone**設定，因為這通常是用來建立 App Store 提交的組建。

在此螢幕擷取畫面中，您可以看到具有新旗標的視窗：

[![在 iOS 組建區段中設定旗標](webview-images/iosbuildblade-vs-sml.png)](webview-images/iosbuildblade-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

請遵循下列步驟來讓連結器移除 `UIWebView` 參考

1. **開啟 ios 專案選項**&ndash; 以滑鼠右鍵按一下您的 ios 專案，然後選擇 [**選項**]。
1. **流覽至 [Ios 組建] 區段**&ndash; 選取 [ **ios 組建**] 區段。
1. **更新其他_mtouch_引數**&ndash; 在**額外的_mtouch_引數**新增此旗標 `--optimize=experimental-xforms-product-type` （除了其中可能已有的任何值）。
1. **更新所有組建**設定 &ndash; 使用視窗頂端**的 [設定**] 和 [**平臺**] 清單來更新所有組建設定。 更新的最重要設定是**Release/iPhone**設定，因為這通常是用來建立 App Store 提交的組建。

在此螢幕擷取畫面中，您可以看到具有新旗標的視窗：

[![在 iOS 組建區段中設定旗標](webview-images/iosbuildblade-xs-sml.png)](webview-images/iosbuildblade-xs.png#lightbox)

-----

現在，當您建立新的（發行）組建並提交至 App Store 時，應該不會有關于已被取代之 API 的警告。

## <a name="related-links"></a>相關連結

- [使用 Web 工作（sample）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [Web 視圖（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)

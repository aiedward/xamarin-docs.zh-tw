---
標題：「自訂 web 程式名稱」描述：「web 工作」 Xamarin.Forms 是在您的應用程式中顯示 web 和 HTML 內容的視圖。 本文說明如何建立自訂轉譯器，以擴充 Web 工作，以允許從 JavaScript 叫用 c # 程式碼。」
assetid： 58DFFA52-4057-49A8-8682-50A58C7E842C ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：03/31/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="customizing-a-webview"></a>自訂 WebView

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_Xamarin.Forms `WebView` 是在您的應用程式中顯示 WEB 和 HTML 內容的視圖。本文說明如何建立自訂轉譯器，以擴充 `WebView` 以允許從 JavaScript 叫用 c # 程式碼。_

每 Xamarin.Forms 個視圖都會針對每個建立原生控制項實例的平臺，隨附一個轉譯器。 當 [`WebView`](xref:Xamarin.Forms.WebView) Xamarin.Forms 應用程式在 iOS 上呈現時， `WkWebViewRenderer` 類別會具現化，然後再具現化原生 `WkWebView` 控制項。 在 Android 平台上，`WebViewRenderer` 類別會具現化原生 `WebView` 控制項。 在通用 Windows 平台 (UWP) 上，`WebViewRenderer` 類別會具現化原生 `WebView` 控制項。 如需控制項對應之轉譯器和原生控制項類別的詳細資訊 Xamarin.Forms ，請參閱轉譯器[基類和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明 [`View`](xref:Xamarin.Forms.View) 和執行它的對應原生控制項之間的關聯性：

![](hybridwebview-images/webview-classes.png "Relationship Between the WebView Class and its Implementing Native Classes")

轉譯程式可以 [`WebView`](xref:Xamarin.Forms.WebView) 在每個平臺上建立的自訂轉譯器，用來執行平臺自訂。 執行這項作業的流程如下：

1. [建立](#create-the-hybridwebview)`HybridWebView` 自訂控制項。
1. [使用](#consume-the-hybridwebview) `HybridWebView` 來自的 Xamarin.Forms 。
1. 在每個平台上[建立](#create-the-custom-renderer-on-each-platform)`HybridWebView` 的自訂轉譯器。

現在將會討論每個專案，然後再執行 `HybridWebView` 增強的轉譯器， Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) 以允許從 JavaScript 叫用 c # 程式碼。 `HybridWebView` 執行個體會用以顯示 HTML 網頁，要求使用者輸入其名稱。 然後，當使用者按一下 HTML 按鈕時，JavaScript 函式會叫用 C# `Action`，其顯示包含使用者名稱的快顯視窗。

如需從 JavaScript 叫用 c # 程式的詳細資訊，請參閱[從 javascript 叫用 c #](#invoke-c-from-javascript)。 如需 HTML 網頁的詳細資訊，請參閱[建立網頁](#create-the-web-page)。

> [!NOTE]
> [`WebView`](xref:Xamarin.Forms.WebView)可以從 c # 叫用 JavaScript 函式，並將任何結果傳回給呼叫 c # 程式碼。 如需詳細資訊，請參閱叫用[JavaScript](~/xamarin-forms/user-interface/webview.md#invoking-javascript)。

## <a name="create-the-hybridwebview"></a>建立 HybridWebView

您 `HybridWebView` 可以藉由子類別化類別來建立自訂控制項 [`WebView`](xref:Xamarin.Forms.WebView) ：

```csharp
public class HybridWebView : WebView
{
    Action<string> action;

    public static readonly BindableProperty UriProperty = BindableProperty.Create(
        propertyName: "Uri",
        returnType: typeof(string),
        declaringType: typeof(HybridWebView),
        defaultValue: default(string));

    public string Uri
    {
        get { return (string)GetValue(UriProperty); }
        set { SetValue(UriProperty, value); }
    }

    public void RegisterAction(Action<string> callback)
    {
        action = callback;
    }

    public void Cleanup()
    {
        action = null;
    }

    public void InvokeAction(string data)
    {
        if (action == null || data == null)
        {
            return;
        }
        action.Invoke(data);
    }
}
```

`HybridWebView` 自訂控制項會在 .NET Standard 程式庫專案中建立，並定義下列控制項的 API：

- 指定要載入之網頁位址的 `Uri` 屬性。
- 向控制項登錄 `Action` 的 `RegisterAction` 方法。 已註冊的動作會從 JavaScript 叫用，此 JavaScript 包含在透過 `Uri` 屬性參考的 HTML 檔案中。
- 移除已註冊 `Action` 之參考的 `CleanUp` 方法。
- 叫用已註冊 `Action` 的 `InvokeAction` 方法。 這個方法會從每個平臺專案中的自訂轉譯器呼叫。

## <a name="consume-the-hybridwebview"></a>使用 HybridWebView

您可以宣告控制項的位置命名空間並使用自訂控制項上的命名空間前置詞，在 .NET Standard 程式庫專案的 XAML 中參考 `HybridWebView` 自訂控制項。 下列程式碼範例示範 XAML 頁面如何使用 `HybridWebView` 自訂控制項：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,40,0,0">
    <local:HybridWebView x:Name="hybridWebView"
                         Uri="index.html" />
</ContentPage>

```

`local` 命名空間前置詞沒有命名限制。 不過，`clr-namespace` 和 `assembly` 值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，即會使用前置詞來參考自訂控制項。

下列程式碼範例示範 C# 頁面如何使用 `HybridWebView` 自訂控制項：

```csharp
public HybridWebViewPageCS()
{
    var hybridWebView = new HybridWebView
    {
        Uri = "index.html"
    };
    // ...
    Padding = new Thickness(0, 40, 0, 0);
    Content = hybridWebView;
}
```

`HybridWebView` 執行個體會用於顯示每個平台的原生 Web 控制項。 它的 `Uri` 屬性會設定為儲存在每個平臺專案中的 HTML 檔案，而原生 web 控制項將會顯示此檔案。 轉譯的 HTML 會要求使用者輸入其名稱，以 JavaScript 函式叫用 C# `Action` 以回應 [HTML] 按鈕的按一下動作。

`HybridWebViewPage` 註冊要從 JavaScript 叫用的動作，如下列程式碼範例所示：

```csharp
public partial class HybridWebViewPage : ContentPage
{
    public HybridWebViewPage()
    {
        // ...
        hybridWebView.RegisterAction(data => DisplayAlert("Alert", "Hello " + data, "OK"));
    }
}
```

這個動作會呼叫 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 方法來顯示強制回應快顯視窗，其中呈現實例所顯示的 HTML 頁面中輸入的名稱 `HybridWebView` 。

自訂轉譯器現在可以新增至每個應用程式專案，藉由允許從 JavaScript 叫用 c # 程式碼來增強平臺 web 控制項。

## <a name="create-the-custom-renderer-on-each-platform"></a>在每個平臺上建立自訂轉譯器

建立自訂轉譯器類別的程序如下：

1. `WkWebViewRenderer`在 iOS 上建立類別的子類別，並在 `WebViewRenderer` ANDROID 和 UWP 上建立可呈現自訂控制項的類別。
1. 覆寫 `OnElementChanged` 呈現的方法， [`WebView`](xref:Xamarin.Forms.WebView) 並撰寫可自訂的邏輯。 建立物件時，會呼叫這個方法 `HybridWebView` 。
1. 將 `ExportRenderer` 屬性加入至自訂轉譯器類別或*AssemblyInfo.cs*，以指定它將用來呈現 Xamarin.Forms 自訂控制項。 這個屬性是用來向註冊自訂轉譯器 Xamarin.Forms 。

> [!NOTE]
> 對於大部分的 Xamarin.Forms 元素而言，在每個平臺專案中提供自訂轉譯器是選擇性的。 如果自訂轉譯器尚未註冊，則會使用控制項基底類別的預設轉譯器。 不過，轉譯 [View](xref:Xamarin.Forms.View) 項目時，每個平台專案都必須要有自訂轉譯器。

下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![](hybridwebview-images/solution-structure.png "HybridWebView Custom Renderer Project Responsibilities")

`HybridWebView`自訂控制項是由平臺轉譯器類別轉譯，其衍生自 `WkWebViewRenderer` iOS 上的類別，以及從 `WebViewRenderer` Android 和 UWP 上的類別。 這會導致每個 `HybridWebView` 自訂控制項都使用原生 web 控制項呈現，如下列螢幕擷取畫面所示：

![](hybridwebview-images/screenshots.png "HybridWebView on each Platform")

`WkWebViewRenderer`和 `WebViewRenderer` 類別會公開 `OnElementChanged` 方法，這會在 Xamarin.Forms 建立自訂控制項以轉譯對應的原生 web 控制項時呼叫。 這個方法會採用 `VisualElementChangedEventArgs` 包含 `OldElement` 和屬性的參數 `NewElement` 。 這些屬性代表轉譯器 Xamarin.Forms 附加到的*was*專案，以及轉譯器 Xamarin.Forms 附加至的元素。 *is* 在應用程式範例中，`OldElement` 屬性會是 `null`，而 `NewElement` 屬性會包含 `HybridWebView` 執行個體的參考。

`OnElementChanged`在每個平臺轉譯器類別中，方法的覆寫版本是執行原生 web 控制項自訂的位置。 要轉譯 Xamarin.Forms 之控制項的參考可透過 `Element` 屬性取得。

每個自訂轉譯器類別都會以向註冊轉譯器的 `ExportRenderer` 屬性裝飾 Xamarin.Forms 。 屬性會採用兩個參數– Xamarin.Forms 所呈現之自訂控制項的類型名稱，以及自訂轉譯器的類型名稱。 屬性的 `assembly` 前置詞會指定套用至整個組件的屬性。

下列各節將討論每個原生 web 控制項所載入的網頁結構、從 JavaScript 叫用 c # 的程式，以及在每個平臺自訂轉譯器類別中執行這個動作。

### <a name="create-the-web-page"></a>建立網頁

下列程式碼範例示範將由 `HybridWebView` 自訂控制項顯示的網頁：

```html
<html>
<body>
    <script src="http://code.jquery.com/jquery-2.1.4.min.js"></script>
    <h1>HybridWebView Test</h1>
    <br />
    Enter name: <input type="text" id="name">
    <br />
    <br />
    <button type="button" onclick="javascript: invokeCSCode($('#name').val());">Invoke C# Code</button>
    <br />
    <p id="result">Result:</p>
    <script type="text/javascript">function log(str) {
            $('#result').text($('#result').text() + " " + str);
        }

        function invokeCSCode(data) {
            try {
                log("Sending Data:" + data);
                invokeCSharpAction(data);
            }
            catch (err) {
                log(err);
            }
        }</script>
</body>
</html>
```

網頁可讓使用者在 `input` 項目中輸入他們的姓名，並提供按一下即可叫用 C# 程式碼的 `button` 項目。 執行這項作業的程序如下：

- 當使用者按一下 `button` 項目時，即會呼叫 `invokeCSCode` JavaScript 函式，並將 `input` 項目的值傳遞至函式。
- `invokeCSCode` 函式會呼叫 `log` 函式，顯示它傳送到 C# `Action` 的資料。 然後，它會呼叫 `invokeCSharpAction` 方法來叫用 C# `Action`，傳遞從 `input` 項目收到的參數。

`invokeCSharpAction` JavaScript 函式不是在網頁中定義，而是由每個自訂轉譯器插入至網頁。

在 iOS 上，此 HTML 檔案位於具有 **BundleResource** 建置動作的平台專案 [內容] 資料夾中。 在 Android 上，此 HTML 檔案則位於具有 **AndroidAsset** 建置動作的平台專案 [資產/內容] 資料夾中。

### <a name="invoke-c-from-javascript"></a>從 JavaScript 叫用 c #

每個平台從 JavaScript 叫用 C# 的程序都完全相同：

- 自訂轉譯器會建立原生 Web 控制項，並載入 `HybridWebView.Uri` 屬性指定的 HTML 檔案。
- 一旦載入網頁，自訂轉譯器就會將 `invokeCSharpAction` JavaScript 函式插入至網頁。
- 當使用者輸入其名稱並按一下 HTML `button` 項目時，即會叫用 `invokeCSCode` 函式，然後接著叫用 `invokeCSharpAction` 函式。
- `invokeCSharpAction` 函式會在自訂轉譯器中叫用方法，再接著叫用 `HybridWebView.InvokeAction` 方法。
- `HybridWebView.InvokeAction` 方法叫用已註冊的 `Action`。

下列各節會討論如何在每個平台上實作此程序。

### <a name="create-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例示範適用於 iOS 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : WkWebViewRenderer, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        public HybridWebViewRenderer() : this(new WKWebViewConfiguration())
        {
        }

        public HybridWebViewRenderer(WKWebViewConfiguration config) : base(config)
        {
            userController = config.UserContentController;
            var script = new WKUserScript(new NSString(JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
            userController.AddUserScript(script);
            userController.AddScriptMessageHandler(this, "invokeAction");
        }

        protected override void OnElementChanged(VisualElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                userController.RemoveAllUserScripts();
                userController.RemoveScriptMessageHandler("invokeAction");
                HybridWebView hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }

            if (e.NewElement != null)
            {
                string filename = Path.Combine(NSBundle.MainBundle.BundlePath, $"Content/{((HybridWebView)Element).Uri}");
                LoadRequest(new NSUrlRequest(new NSUrl(filename, false)));
            }
        }

        public void DidReceiveScriptMessage(WKUserContentController userContentController, WKScriptMessage message)
        {
            ((HybridWebView)Element).InvokeAction(message.Body.ToString());
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

`HybridWebViewRenderer`類別會將屬性中所指定的網頁載入 `HybridWebView.Uri` 原生 [`WKWebView`](xref:WebKit.WKWebView) 控制項，並將 JavaScript 函式 `invokeCSharpAction` 插入網頁。 一旦使用者輸入其名稱並按一下 HTML `button` 項目，即會使用網頁收到訊息後呼叫的 `DidReceiveScriptMessage` 方法來執行 `invokeCSharpAction` JavaScript 函式。 接著，此方法會叫用 `HybridWebView.InvokeAction` 方法，這樣會叫用已註冊的動作來顯示快顯。

執行此功能的程序如下：

- 轉譯器的構造函式會建立 `WkWebViewConfiguration` 物件，並抓取其 [`WKUserContentController`](xref:WebKit.WKUserContentController) 物件。 `WkUserContentController`物件允許張貼訊息，並將使用者腳本插入至網頁。
- 轉譯器的函式 [`WKUserScript`](xref:WebKit.WKUserScript) 會建立物件，在 `invokeCSharpAction` 網頁載入後將 JavaScript 函式插入至網頁。
- 轉譯器的函式會呼叫 [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) 方法，將 [`WKUserScript`](xref:WebKit.WKUserScript) 物件新增至內容控制器。
- 轉譯器的函式會呼叫 [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) 方法，將名為的腳本訊息處理常式加入 `invokeAction` 至 [`WKUserContentController`](xref:WebKit.WKUserContentController) 物件，這會導致 `window.webkit.messageHandlers.invokeAction.postMessage(data)` 使用物件之所有實例的所有框架中都定義了 JavaScript 函數 `WebView` `WKUserContentController` 。
- 假設自訂轉譯器已附加至新的 Xamarin.Forms 元素：
  - [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest))方法會載入屬性所指定的 HTML 檔案 `HybridWebView.Uri` 。 程式碼指定該檔案會儲存在專案的 `Content` 資料夾中。 一旦顯示網頁，即將 `invokeCSharpAction` JavaScript 函式插入至網頁。
- 當轉譯器附加至的專案變更時，就會釋放資源。
- 處置轉譯器 Xamarin.Forms 時，會清除元素。

> [!NOTE]
> 僅 iOS 8 和更新版本支援 `WKWebView` 類別。

此外，必須更新 **Info.plist** 以包含下列值：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

### <a name="create-the-custom-renderer-on-android"></a>在 android 上建立自訂轉譯器

下列程式碼範例示範適用於 Android 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : WebViewRenderer
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<WebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                ((HybridWebView)Element).Cleanup();
            }
            if (e.NewElement != null)
            {
                Control.SetWebViewClient(new JavascriptWebViewClient(this, $"javascript: {JavascriptFunction}"));
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{((HybridWebView)Element).Uri}");
            }
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

`HybridWebViewRenderer`類別會將屬性中指定的網頁載入 `HybridWebView.Uri` 原生 [`WebView`](xref:Android.Webkit.WebView) 控制項，並在網頁完成載入之後，將 JavaScript 函式插入網頁中，並在 `invokeCSharpAction` `OnPageFinished` 類別中使用覆寫 `JavascriptWebViewClient` ：

```csharp
public class JavascriptWebViewClient : FormsWebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(HybridWebViewRenderer renderer, string javascript) : base(renderer)
    {
        _javascript = javascript;
    }

    public override void OnPageFinished(WebView view, string url)
    {
        base.OnPageFinished(view, url);
        view.EvaluateJavascript(_javascript, null);
    }
}
```

一旦使用者輸入其名稱並按一下 HTML `button` 項目，即會執行 `invokeCSharpAction` JavaScript 函式。 執行此功能的程序如下：

- 假設自訂轉譯器已附加至新的 Xamarin.Forms 元素：
  - `SetWebViewClient`方法會將新的 `JavascriptWebViewClient` 物件設定為的執行 `WebViewClient` 。
  - [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*)方法會將新的 `JSBridge` 實例插入到 web 視圖的 JavaScript 內容的主框架中，並將它命名為 `jsBridge` 。 以便從 JavaScript 存取 `JSBridge` 類別中的方法。
  - [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*)方法會載入屬性所指定的 HTML 檔案 `HybridWebView.Uri` 。 程式碼指定該檔案會儲存在專案的 `Content` 資料夾中。
  - 在 `JavascriptWebViewClient` 類別中，`invokeCSharpAction` JavaScript 函式會在頁面載入完成後插入至網頁。
- 當轉譯器附加至的專案變更時，就會釋放資源。
- 處置轉譯器 Xamarin.Forms 時，會清除元素。

當 `invokeCSharpAction` JavaScript 函式執行時，它會依序叫用 `JSBridge.InvokeAction` 方法，如下列程式碼範例所示：

```csharp
public class JSBridge : Java.Lang.Object
{
    readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

    public JSBridge(HybridWebViewRenderer hybridRenderer)
    {
        hybridWebViewRenderer = new WeakReference<HybridWebViewRenderer>(hybridRenderer);
    }

    [JavascriptInterface]
    [Export("invokeAction")]
    public void InvokeAction(string data)
    {
        HybridWebViewRenderer hybridRenderer;

        if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget(out hybridRenderer))
        {
            ((HybridWebView)hybridRenderer.Element).InvokeAction(data);
        }
    }
}
```

類別必須衍生自 `Java.Lang.Object`，而向 JavaScript 公開的方法則必須使用 `[JavascriptInterface]` 和 `[Export]` 屬性裝飾。 因此，當 `invokeCSharpAction` JavaScript 函式插入至網頁且執行時，它會因為正使用 `[JavascriptInterface]` 和 `[Export("invokeAction")]` 屬性裝飾而呼叫 `JSBridge.InvokeAction` 方法。 接著， `InvokeAction` 方法會叫用 `HybridWebView.InvokeAction` 方法，這會叫用已註冊的動作以顯示快顯視窗。

> [!IMPORTANT]
> 使用屬性的 Android 專案 `[Export]` 必須包含的參考 `Mono.Android.Export` ，否則將會產生編譯器錯誤。

請注意，`JSBridge` 類別會將 `WeakReference` 維持為 `HybridWebViewRenderer` 類別。 這是為了避免建立兩個類別之間的循環參考。 如需詳細資訊，請參閱[弱式參考](/en-us/dotnet/standard/garbage-collection/weak-references)。

### <a name="create-the-custom-renderer-on-uwp"></a>在 UWP 上建立自訂轉譯器

下列程式碼範例示範適用於 UWP 的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : WebViewRenderer
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.WebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri($"ms-appx-web:///Content//{((HybridWebView)Element).Uri}");
            }
        }

        async void OnWebViewNavigationCompleted(Windows.UI.Xaml.Controls.WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            ((HybridWebView)Element).InvokeAction(e.Value);
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

`HybridWebViewRenderer` 類別會將 `HybridWebView.Uri` 屬性中所指定網頁載入到原生的 `WebView` 控制項中，並在網頁載入後，使用 `WebView.InvokeScriptAsync` 方法將 `invokeCSharpAction` JavaScript 函式插入至網頁。 一旦使用者輸入其名稱並按一下 HTML `button` 項目，即會使用網頁收到通知後呼叫的 `OnWebViewScriptNotify` 方法來執行 `invokeCSharpAction` JavaScript 函式。 接著，此方法會叫用 `HybridWebView.InvokeAction` 方法，這樣會叫用已註冊的動作來顯示快顯。

執行此功能的程序如下：

- 假設自訂轉譯器已附加至新的 Xamarin.Forms 元素：
  - 則會註冊 `NavigationCompleted` 和 `ScriptNotify` 事件的事件處理常式。 當原生 `WebView` 控制項已完成載入目前的內容或導覽失敗時，會引發 `NavigationCompleted` 事件。 當原生 `WebView` 控制項使用 JavaScript 將字串傳遞至應用程式時，會引發 `ScriptNotify` 事件。 網頁在傳遞 `string` 參數時呼叫 `window.external.notify`，藉以引發 `ScriptNotify` 事件。
  - `WebView.Source` 屬性設為 `HybridWebView.Uri` 屬性所指定之 HTML 檔案的 URI。 程式碼假設該檔案會儲存在專案的 `Content` 資料夾中。 一旦顯示網頁，就會引發 `NavigationCompleted` 事件並叫用 `OnWebViewNavigationCompleted` 方法。 然後使用 `WebView.InvokeScriptAsync` 方法將 `invokeCSharpAction` JavaScript 函式插入至網頁，假設導覽已順利完成。
- 當轉譯器附加至的專案變更時，會取消訂閱事件。
- 處置轉譯器 Xamarin.Forms 時，會清除元素。

## <a name="related-links"></a>相關連結

- [HybridWebView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

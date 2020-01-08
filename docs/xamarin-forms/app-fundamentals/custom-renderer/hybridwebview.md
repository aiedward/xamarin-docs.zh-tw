---
title: 自訂 Web 工作
description: 「Xamarin」是一種可在應用程式中顯示 web 和 HTML 內容的視圖。 本文說明如何建立自訂轉譯器，以擴充 Web 工作，以C#允許從 JavaScript 叫用程式碼。
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2019
ms.openlocfilehash: 46d0b245246d9e93040cd8591dab8ed3a816268d
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487005"
---
# <a name="customizing-a-webview"></a>自訂 Web 工作

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_[Xamarin] `WebView` 是在您的應用程式中顯示 web 和 HTML 內容的視圖。本文說明如何建立自訂轉譯器，以擴充 `WebView`，以允許C#從 JavaScript 叫用程式碼。_

每個 Xamarin.Forms 檢視都隨附每個平台的轉譯器，這些平台可建立原生控制項的執行個體。 當[`WebView`](xref:Xamarin.Forms.WebView)是由 iOS 上的 Xamarin 應用程式轉譯時，`WkWebViewRenderer` 類別會具現化，然後再具現化原生的 `WkWebView` 控制項。 在 Android 平台上，`WebViewRenderer` 類別會具現化原生 `WebView` 控制項。 在通用 Windows 平台 (UWP) 上，`WebViewRenderer` 類別會具現化原生 `WebView` 控制項。 如需 Xamarin.Forms 控制項對應的轉譯器和原生控制項類別詳細資訊，請參閱[轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明 [`View`](xref:Xamarin.Forms.View) 和實作它對應原生控制項間的關聯性：

![](hybridwebview-images/webview-classes.png "Relationship Between the WebView Class and its Implementing Native Classes")

藉由在每個平臺上建立[`WebView`](xref:Xamarin.Forms.WebView)的自訂轉譯器，可以使用轉譯程式來執行平臺自訂。 執行這項作業的流程如下：

1. [建立](#create-the-hybridwebview)`HybridWebView` 自訂控制項。
1. [使用](#consume-the-hybridwebview) Xamarin.Forms 的 `HybridWebView`。
1. 在每個平台上[建立](#create-the-custom-renderer-on-each-platform)`HybridWebView` 的自訂轉譯器。

現在會先討論每個專案，然後再執行 `HybridWebView` 轉譯器，以增強 Xamarin. 表單[`WebView`](xref:Xamarin.Forms.WebView)C# ，以允許從 JavaScript 叫用程式碼。 `HybridWebView` 執行個體會用以顯示 HTML 網頁，要求使用者輸入其名稱。 然後，當使用者按一下 HTML 按鈕時，JavaScript 函式會叫用 C# `Action`，其顯示包含使用者名稱的快顯視窗。

如需C#從 javascript 叫用程式的詳細資訊，請參閱[ C#從 javascript 叫用](#invoke-c-from-javascript)。 如需 HTML 網頁的詳細資訊，請參閱[建立網頁](#create-the-web-page)。

> [!NOTE]
> [`WebView`](xref:Xamarin.Forms.WebView)可以從C#叫用 JavaScript 函數，並將任何結果傳回給呼叫C#程式碼。 如需詳細資訊，請參閱叫用[JavaScript](~/xamarin-forms/user-interface/webview.md#invoking-javascript)。

## <a name="create-the-hybridwebview"></a>建立 HybridWebView

`HybridWebView` 自訂控制項可透過子類別化[`WebView`](xref:Xamarin.Forms.WebView)類別來建立：

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

`HybridWebView` 執行個體會用於顯示每個平台的原生 Web 控制項。 它的 `Uri` 屬性會設定為儲存在每個平臺專案中的 HTML 檔案，而原生 web 控制項將會顯示該檔案。 轉譯的 HTML 會要求使用者輸入其名稱，以 JavaScript 函式叫用 C# `Action` 以回應 [HTML] 按鈕的按一下動作。

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

這個動作會呼叫 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 方法，顯示強制回應快顯，此快顯可呈現 `HybridWebView` 執行個體所顯示之 HTML 頁面中輸入的名稱。

自訂轉譯器現在可以新增至每個應用程式專案，藉由允許C#從 JavaScript 叫用程式碼來增強平臺 web 控制項。

## <a name="create-the-custom-renderer-on-each-platform"></a>在每個平臺上建立自訂轉譯器

建立自訂轉譯器類別的流程如下：

1. 在 iOS 上建立 `WkWebViewRenderer` 類別的子類別，以及可呈現自訂控制項的 Android 和 UWP 上的 `WebViewRenderer` 類別。
1. 覆寫呈現[`WebView`](xref:Xamarin.Forms.WebView)的 `OnElementChanged` 方法，並撰寫自訂的邏輯。 建立 `HybridWebView` 物件時，會呼叫這個方法。
1. 將 `ExportRenderer` 屬性加入至自訂轉譯器類別或*AssemblyInfo.cs*，以指定它將用來呈現 Xamarin. form 自訂控制項。 這個屬性會用來向 Xamarin.Forms 註冊自訂轉譯器。

> [!NOTE]
> 對大部分的 Xamarin.Forms 項目而言，可以選擇是否在每個平台專案中提供自訂轉譯器。 如果自訂轉譯器尚未註冊，則會使用控制項基底類別的預設轉譯器。 不過，轉譯 [View](xref:Xamarin.Forms.View) 項目時，每個平台專案都必須要有自訂轉譯器。

下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![](hybridwebview-images/solution-structure.png "HybridWebView Custom Renderer Project Responsibilities")

`HybridWebView` 自訂控制項是由平臺轉譯器類別（衍生自 iOS 上的 `WkWebViewRenderer` 類別），以及從 Android 和 UWP 上的 `WebViewRenderer` 類別所呈現。 這會導致每個 `HybridWebView` 的自訂控制項都使用原生 web 控制項呈現，如下列螢幕擷取畫面所示：

![](hybridwebview-images/screenshots.png "HybridWebView on each Platform")

`WkWebViewRenderer` 和 `WebViewRenderer` 類別會公開 `OnElementChanged` 方法，這會在建立 Xamarin. form 自訂控制項以轉譯對應的原生 web 控制項時呼叫。 這個方法會採用包含 `OldElement` 和 `NewElement` 屬性的 `VisualElementChangedEventArgs` 參數。 這些屬性分別代表轉譯器「過去」所附加的 Xamarin.Forms 項目，以及「現在」所附加的 Xamarin.Forms 項目。 在應用程式範例中，`OldElement` 屬性會是 `null`，而 `NewElement` 屬性會包含 `HybridWebView` 執行個體的參考。

在每個平臺轉譯器類別中，`OnElementChanged` 方法的覆寫版本是執行原生 web 控制項自訂的位置。 要轉譯之 Xamarin. form 控制項的參考可透過 `Element` 屬性取得。

每個自訂轉譯器類別都裝飾了向 Xamarin.Forms 註冊轉譯器的 `ExportRenderer` 屬性。 此屬性接受兩個參數：正在轉譯的 Xamarin.Forms 自訂控制項類型名稱，以及自訂轉譯器的類型名稱。 屬性的 `assembly` 前置詞會指定套用至整個組件的屬性。

下列各節將討論每個原生 web 控制項所載入之網頁的結構、 C#從 JavaScript 叫用的程式，以及在每個平臺自訂轉譯器類別中執行此作業的流程。

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

### <a name="invoke-c-from-javascript"></a>從C# JavaScript 調用

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
    }
}
```

`HybridWebViewRenderer` 類別會將 `HybridWebView.Uri` 屬性中所指定網頁載入到原生的 [`WKWebView`](xref:WebKit.WKWebView) 控制項中，而 `invokeCSharpAction` JavaScript 函式會插入至網頁。 一旦使用者輸入其名稱並按一下 HTML `button` 項目，即會使用網頁收到訊息後呼叫的 `DidReceiveScriptMessage` 方法來執行 `invokeCSharpAction` JavaScript 函式。 接著，此方法會叫用 `HybridWebView.InvokeAction` 方法，這樣會叫用已註冊的動作來顯示快顯。

執行此功能的程序如下：

- 轉譯器的構造函式會建立 `WkWebViewConfiguration` 物件，並抓取其[`WKUserContentController`](xref:WebKit.WKUserContentController)物件。 `WkUserContentController` 物件允許張貼訊息，並將使用者腳本插入至網頁。
- 轉譯器的函式會建立[`WKUserScript`](xref:WebKit.WKUserScript)物件，這會在載入網頁之後，將 `invokeCSharpAction` JavaScript 函式插入至網頁。
- 轉譯器的函式會呼叫[`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript))方法，將[`WKUserScript`](xref:WebKit.WKUserScript)物件新增至內容控制器。
- 轉譯器的函式會呼叫[`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String))方法，將名為 `invokeAction` 的腳本訊息處理常式加入至[`WKUserContentController`](xref:WebKit.WKUserContentController)物件，這會導致在使用 `WebView` 物件的所有 `WKUserContentController` 實例的所有框架中定義 JavaScript 函數 `window.webkit.messageHandlers.invokeAction.postMessage(data)`。
- 假設自訂轉譯器附加於新的 Xamarin.Forms 項目：
  - [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) 方法會載入 `HybridWebView.Uri` 屬性所指定的 HTML 檔案。 程式碼指定該檔案會儲存在專案的 `Content` 資料夾中。 一旦顯示網頁，即將 `invokeCSharpAction` JavaScript 函式插入至網頁。
- 當轉譯器附加至的項目變更時：
  - 釋放資源。

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
                Control.SetWebViewClient(new JavascriptWebViewClient($"javascript: {JavascriptFunction}"));
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{((HybridWebView)Element).Uri}");
            }
        }
    }
}
```

`HybridWebViewRenderer` 類別會將 `HybridWebView.Uri` 屬性中所指定網頁載入到原生的 [`WebView`](xref:Android.Webkit.WebView) 控制項中，並在網頁載入完成後，使用 `JavascriptWebViewClient` 類別的 `OnPageFinished` 覆寫將 `invokeCSharpAction` JavaScript 函式插入至網頁：

```csharp
public class JavascriptWebViewClient : WebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(string javascript)
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

- 假設自訂轉譯器附加於新的 Xamarin.Forms 項目：
  - `SetWebViewClient` 方法會將新的 `JavascriptWebViewClient` 物件設定為 `WebViewClient`的執行。
  - [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*) 方法會將新的 `JSBridge` 執行個體插入至 WebView JavaScript 內容的主框架，將它命名為 `jsBridge`。 以便從 JavaScript 存取 `JSBridge` 類別中的方法。
  - [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*) 方法會載入 `HybridWebView.Uri` 屬性所指定的 HTML 檔案。 程式碼指定該檔案會儲存在專案的 `Content` 資料夾中。
  - 在 `JavascriptWebViewClient` 類別中，`invokeCSharpAction` JavaScript 函式會在頁面載入完成後插入至網頁。
- 當轉譯器附加至的項目變更時：
  - 釋放資源。

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

類別必須衍生自 `Java.Lang.Object`，而向 JavaScript 公開的方法則必須使用 `[JavascriptInterface]` 和 `[Export]` 屬性裝飾。 因此，當 `invokeCSharpAction` JavaScript 函式插入至網頁且執行時，它會因為正使用 `[JavascriptInterface]` 和 `[Export("invokeAction")]` 屬性裝飾而呼叫 `JSBridge.InvokeAction` 方法。 接著，`InvokeAction` 方法會叫用 `HybridWebView.InvokeAction` 方法，這會叫用已註冊的動作以顯示快顯視窗。

> [!IMPORTANT]
> 使用 `[Export]` 屬性的 Android 專案必須包含 `Mono.Android.Export`的參考，否則將會產生編譯器錯誤。

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
    }
}
```

`HybridWebViewRenderer` 類別會將 `HybridWebView.Uri` 屬性中所指定網頁載入到原生的 `WebView` 控制項中，並在網頁載入後，使用 `WebView.InvokeScriptAsync` 方法將 `invokeCSharpAction` JavaScript 函式插入至網頁。 一旦使用者輸入其名稱並按一下 HTML `button` 項目，即會使用網頁收到通知後呼叫的 `OnWebViewScriptNotify` 方法來執行 `invokeCSharpAction` JavaScript 函式。 接著，此方法會叫用 `HybridWebView.InvokeAction` 方法，這樣會叫用已註冊的動作來顯示快顯。

執行此功能的程序如下：

- 假設自訂轉譯器附加於新的 Xamarin.Forms 項目：
  - 則會註冊 `NavigationCompleted` 和 `ScriptNotify` 事件的事件處理常式。 當原生 `WebView` 控制項已完成載入目前的內容或導覽失敗時，會引發 `NavigationCompleted` 事件。 當原生 `WebView` 控制項使用 JavaScript 將字串傳遞至應用程式時，會引發 `ScriptNotify` 事件。 網頁在傳遞 `string` 參數時呼叫 `window.external.notify`，藉以引發 `ScriptNotify` 事件。
  - `WebView.Source` 屬性設為 `HybridWebView.Uri` 屬性所指定之 HTML 檔案的 URI。 程式碼假設該檔案會儲存在專案的 `Content` 資料夾中。 一旦顯示網頁，就會引發 `NavigationCompleted` 事件並叫用 `OnWebViewNavigationCompleted` 方法。 然後使用 `WebView.InvokeScriptAsync` 方法將 `invokeCSharpAction` JavaScript 函式插入至網頁，假設導覽已順利完成。
- 當轉譯器附加至的項目變更時：
  - 就會由此處取消訂閱事件。

## <a name="related-links"></a>相關連結

- [HybridWebView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

---
title: 自訂 WebView
description: Xamarin.Forms WebView 是一個在應用中顯示 Web 和 HTML 內容的檢視。 本文介紹如何創建擴展 WebView 以允許從 JavaScript 調用 C# 代碼的自定義呈現器。
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/31/2020
ms.openlocfilehash: c736c083d4a8c424d3e017dae3cc30e35ad4fa3b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "80419058"
---
# <a name="customizing-a-webview"></a>自訂 WebView

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_Xamarin.Forms`WebView`是一個在應用中顯示 Web 和 HTML 內容的檢視。本文介紹如何創建自定義呈現器,該呈現器擴展`WebView`以允許從 JavaScript 調用 C# 代碼。_

每個 Xamarin.Forms 檢視都隨附每個平台的轉譯器，這些平台可建立原生控制項的執行個體。 [`WebView`](xref:Xamarin.Forms.WebView)當由 iOS 上的 Xamarin.Forms`WkWebViewRenderer`應用程式呈現 時 ,將實例化類,`WkWebView`從而實例化本機 控件。 在 Android 平台上，`WebViewRenderer` 類別會具現化原生 `WebView` 控制項。 在通用 Windows 平台 (UWP) 上，`WebViewRenderer` 類別會具現化原生 `WebView` 控制項。 如需 Xamarin.Forms 控制項對應的轉譯器和原生控制項類別詳細資訊，請參閱[轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明了 實現它的[`View`](xref:Xamarin.Forms.View)的 和相應的本機控件之間的關係:

![](hybridwebview-images/webview-classes.png "Relationship Between the WebView Class and its Implementing Native Classes")

渲染過程可用於透過每個平臺上為的創建自訂[`WebView`](xref:Xamarin.Forms.WebView)呈現器實現平臺自定義。 執行這項作業的流程如下：

1. [建立](#create-the-hybridwebview)`HybridWebView` 自訂控制項。
1. [使用](#consume-the-hybridwebview) Xamarin.Forms 的 `HybridWebView`。
1. 在每個平台上[建立](#create-the-custom-renderer-on-each-platform)`HybridWebView` 的自訂轉譯器。

現在將依次討論每個專案,以實現增強`HybridWebView`Xamarin.Forms[`WebView`](xref:Xamarin.Forms.WebView)的呈現器,以允許從 JavaScript 調用 C# 代碼。 `HybridWebView` 執行個體會用以顯示 HTML 網頁，要求使用者輸入其名稱。 然後，當使用者按一下 HTML 按鈕時，JavaScript 函式會叫用 C# `Action`，其顯示包含使用者名稱的快顯視窗。

有關從 JAVAScript 調用 C# 的過程的詳細資訊,請參閱[從 JavaScript 調用 C#。](#invoke-c-from-javascript) 關於 HTML 頁面的詳細資訊,請參考[網頁](#create-the-web-page)。

> [!NOTE]
> 可以從[`WebView`](xref:Xamarin.Forms.WebView)C# 呼叫 JavaScript 函數,並將任何結果傳回給呼叫 C# 程式碼。 有關詳細資訊,請參閱呼叫[JavaScript](~/xamarin-forms/user-interface/webview.md#invoking-javascript)。

## <a name="create-the-hybridwebview"></a>建立混合 Web 檢視

可以`HybridWebView`通過[`WebView`](xref:Xamarin.Forms.WebView)對 類別進行子類別來建立自訂控制者:

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
- 叫用已註冊 `Action` 的 `InvokeAction` 方法。 此方法將從每個平台專案中的自定義呈現器調用。

## <a name="consume-the-hybridwebview"></a>使用混合 Web 檢視

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

`HybridWebView` 執行個體會用於顯示每個平台的原生 Web 控制項。 它`Uri`的屬性設置為存儲在每個平台專案中的 HTML 檔,該檔將由本機 Web 控件顯示。 轉譯的 HTML 會要求使用者輸入其名稱，以 JavaScript 函式叫用 C# `Action` 以回應 [HTML] 按鈕的按一下動作。

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

此操作調用[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))方法以顯示模式彈出視窗,該彈出視窗`HybridWebView`顯示 實例顯示的 HTML 頁中輸入的名稱。

現在可以將自定義呈現器添加到每個應用程式專案中,通過允許從 JavaScript 調用 C# 代碼來增強平臺 Web 控件。

## <a name="create-the-custom-renderer-on-each-platform"></a>在每個平台上建立自訂呈現器

建立自訂轉譯器類別的程序如下：

1. 在 iOS`WkWebViewRenderer`上 創建類的子`WebViewRenderer`類,在 Android 和 UWP 上創建呈現自定義控制件的類。
1. 重寫呈現`OnElementChanged`[`WebView`](xref:Xamarin.Forms.WebView)和 寫入邏輯以對其進行自定義的方法。 創建`HybridWebView`物件時調用此方法。
1. 將`ExportRenderer`屬性添加到自定義呈現器類或*AssemblyInfo.cs*,以指定該屬性將用於呈現 Xamarin.Forms 自定義控制項。 這個屬性會用來向 Xamarin.Forms 註冊自訂轉譯器。

> [!NOTE]
> 對大部分的 Xamarin.Forms 項目而言，可以選擇是否在每個平台專案中提供自訂轉譯器。 如果自訂轉譯器尚未註冊，則會使用控制項基底類別的預設轉譯器。 不過，轉譯 [View](xref:Xamarin.Forms.View) 項目時，每個平台專案都必須要有自訂轉譯器。

下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![](hybridwebview-images/solution-structure.png "HybridWebView Custom Renderer Project Responsibilities")

`HybridWebView`自定義控制項由平台呈現器類(派生自iOS上的`WkWebViewRenderer`類)和Android和UWP上的`WebViewRenderer`類呈現。 這將導致使用本機`HybridWebView`Web 控制檔呈現每個自訂控制項,如以下螢幕截圖所示:

![](hybridwebview-images/screenshots.png "HybridWebView on each Platform")

`WkWebViewRenderer`和`WebViewRenderer`類`OnElementChanged`公開 該方法,在創建 Xamarin.Forms 自定義控制項以呈現相應的本機 Web 控件時調用該方法。 此方法採用包含`VisualElementChangedEventArgs`和`NewElement`屬性`OldElement`的參數。 這些屬性分別代表轉譯器「過去」** 所附加的 Xamarin.Forms 項目，以及「現在」** 所附加的 Xamarin.Forms 項目。 在應用程式範例中，`OldElement` 屬性會是 `null`，而 `NewElement` 屬性會包含 `HybridWebView` 執行個體的參考。

在每個平台呈現器類中`OnElementChanged`重寫該方法的版本是執行本機 Web 控件自定義的位置。 可透過 屬性獲取對正在呈現的 Xamarin.Forms`Element`控制件的引用。

每個自訂轉譯器類別都裝飾了向 Xamarin.Forms 註冊轉譯器的 `ExportRenderer` 屬性。 此屬性接受兩個參數：正在轉譯的 Xamarin.Forms 自訂控制項類型名稱，以及自訂轉譯器的類型名稱。 屬性的 `assembly` 前置詞會指定套用至整個組件的屬性。

以下各節討論每個本機 Web 控件載入的網頁的結構、從 JavaScript 調用 C# 的過程,以及在每個平臺自定義呈現器類中實現此內容的過程。

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

### <a name="invoke-c-from-javascript"></a>從 JavaScript 呼叫 C#

每個平台從 JavaScript 叫用 C# 的程序都完全相同：

- 自訂轉譯器會建立原生 Web 控制項，並載入 `HybridWebView.Uri` 屬性指定的 HTML 檔案。
- 一旦載入網頁，自訂轉譯器就會將 `invokeCSharpAction` JavaScript 函式插入至網頁。
- 當使用者輸入其名稱並按一下 HTML `button` 項目時，即會叫用 `invokeCSCode` 函式，然後接著叫用 `invokeCSharpAction` 函式。
- `invokeCSharpAction` 函式會在自訂轉譯器中叫用方法，再接著叫用 `HybridWebView.InvokeAction` 方法。
- `HybridWebView.InvokeAction` 方法叫用已註冊的 `Action`。

下列各節會討論如何在每個平台上實作此程序。

### <a name="create-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂呈現器

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

類別`HybridWebViewRenderer`將`HybridWebView.Uri`屬性中指定的網頁載入到本[`WKWebView`](xref:WebKit.WKWebView)機 控制項中`invokeCSharpAction`,並將 JavaScript 函數注入到網頁中。 一旦使用者輸入其名稱並按一下 HTML `button` 項目，即會使用網頁收到訊息後呼叫的 `DidReceiveScriptMessage` 方法來執行 `invokeCSharpAction` JavaScript 函式。 接著，此方法會叫用 `HybridWebView.InvokeAction` 方法，這樣會叫用已註冊的動作來顯示快顯。

執行此功能的程序如下：

- 呈現器建構函數創建物件`WkWebViewConfiguration`並檢索[`WKUserContentController`](xref:WebKit.WKUserContentController)其 物件。 該`WkUserContentController`物件允許發佈消息並將使用者腳本注入網頁。
- 呈現器建構函數創建一個[`WKUserScript`](xref:WebKit.WKUserScript)物件,該物件在載`invokeCSharpAction`入 網頁後將 JAvaScript 函數注入到網頁中。
- 呈現器建構函數調用[`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript))方法[`WKUserScript`](xref:WebKit.WKUserScript)將 物件添加到內容控制器。
- 呈現器建構函數調用[`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String))方法以`invokeAction`[`WKUserContentController`](xref:WebKit.WKUserContentController)向 物件添加命名的腳本消息處理程式,這將導致`window.webkit.messageHandlers.invokeAction.postMessage(data)``WebView``WKUserContentController`在使用 該物件的所有實例中的所有幀中定義 JAVAScript 函數。
- 假設自訂轉譯器附加於新的 Xamarin.Forms 項目：
  - 該方法[`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest))載`HybridWebView.Uri`入 屬性指定的 HTML 檔。 程式碼指定該檔案會儲存在專案的 `Content` 資料夾中。 一旦顯示網頁，即將 `invokeCSharpAction` JavaScript 函式插入至網頁。
- 當呈現器附加到更改的元素時,將釋放資源。
- 釋放渲染器時,將清理 Xamarin.Forms 元素。

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

### <a name="create-the-custom-renderer-on-android"></a>在 android 上建立自訂成像器

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

類別`HybridWebViewRenderer``HybridWebView.Uri`[`WebView`](xref:Android.Webkit.WebView)將 屬性中指定的網頁載入到本機控制項中`invokeCSharpAction`,並且 JavaScript 函數在網頁完成載入後注入`OnPageFinished``JavascriptWebViewClient`到網頁中, 並在類別中重寫:

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

- 假設自訂轉譯器附加於新的 Xamarin.Forms 項目：
  - 該方法`SetWebViewClient`將`JavascriptWebViewClient`新 物件`WebViewClient`集為的實現。
  - 此方法[`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*)將`JSBridge`新的實體注入 WebView 的 JavaScript 中文的主框架`jsBridge`,並命名它 。 以便從 JavaScript 存取 `JSBridge` 類別中的方法。
  - 該方法[`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*)載`HybridWebView.Uri`入 屬性指定的 HTML 檔。 程式碼指定該檔案會儲存在專案的 `Content` 資料夾中。
  - 在 `JavascriptWebViewClient` 類別中，`invokeCSharpAction` JavaScript 函式會在頁面載入完成後插入至網頁。
- 當呈現器附加到更改的元素時,將釋放資源。
- 釋放渲染器時,將清理 Xamarin.Forms 元素。

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

類別必須衍生自 `Java.Lang.Object`，而向 JavaScript 公開的方法則必須使用 `[JavascriptInterface]` 和 `[Export]` 屬性裝飾。 因此，當 `invokeCSharpAction` JavaScript 函式插入至網頁且執行時，它會因為正使用 `[JavascriptInterface]` 和 `[Export("invokeAction")]` 屬性裝飾而呼叫 `JSBridge.InvokeAction` 方法。 反過來,`InvokeAction`該方法調用`HybridWebView.InvokeAction`方法 ,該方法將調用已註冊的操作來顯示彈出視窗。

> [!IMPORTANT]
> 使用 該屬性`[Export]`的 Android`Mono.Android.Export`項目必須包含對的引用,否則將導致編譯器錯誤。

請注意，`JSBridge` 類別會將 `WeakReference` 維持為 `HybridWebViewRenderer` 類別。 這是為了避免建立兩個類別之間的循環參考。 有關詳細資訊,請參閱[弱參考](/en-us/dotnet/standard/garbage-collection/weak-references)。

### <a name="create-the-custom-renderer-on-uwp"></a>在 UWP 上建立自訂呈現器

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

- 假設自訂轉譯器附加於新的 Xamarin.Forms 項目：
  - 則會註冊 `NavigationCompleted` 和 `ScriptNotify` 事件的事件處理常式。 當原生 `WebView` 控制項已完成載入目前的內容或導覽失敗時，會引發 `NavigationCompleted` 事件。 當原生 `WebView` 控制項使用 JavaScript 將字串傳遞至應用程式時，會引發 `ScriptNotify` 事件。 網頁在傳遞 `string` 參數時呼叫 `window.external.notify`，藉以引發 `ScriptNotify` 事件。
  - `WebView.Source` 屬性設為 `HybridWebView.Uri` 屬性所指定之 HTML 檔案的 URI。 程式碼假設該檔案會儲存在專案的 `Content` 資料夾中。 一旦顯示網頁，就會引發 `NavigationCompleted` 事件並叫用 `OnWebViewNavigationCompleted` 方法。 然後使用 `WebView.InvokeScriptAsync` 方法將 `invokeCSharpAction` JavaScript 函式插入至網頁，假設導覽已順利完成。
- 當呈現器附加到更改的元素時,將取消訂閱事件。
- 釋放渲染器時,將清理 Xamarin.Forms 元素。

## <a name="related-links"></a>相關連結

- [混合 Web 檢視(範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

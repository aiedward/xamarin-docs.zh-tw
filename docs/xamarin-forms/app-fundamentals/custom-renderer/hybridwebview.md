---
title: 實作 HybridWebView
description: 本文示範如何建立 HybridWebView 自訂控制項的自訂轉譯器，該自訂控制項會示範如何強化平台特定的 Web 控制項，允許從 JavaScript 叫用 C# 程式碼。
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: 0277a5abd4e34d092b231cd42746f9e05c91b8df
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199957"
---
# <a name="implementing-a-hybridwebview"></a>實作 HybridWebView

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_Xamarin.Forms 自訂使用者介面控制項應該衍生自用來在螢幕上放置配置和控制項的 View 類別。本文示範如何建立 HybridWebView 自訂控制項的自訂轉譯器，該自訂控制項會示範如何強化平台特定的 Web 控制項，允許從 JavaScript 叫用 C# 程式碼。_

每個 Xamarin.Forms 檢視都隨附每個平台的轉譯器，這些平台可建立原生控制項的執行個體。 當 iOS 中的 Xamarin.Forms 應用程式轉譯 [`View`](xref:Xamarin.Forms.View) 時，會先具現化 `ViewRenderer` 類別，接著具現化原生的 `UIView` 控制項。 在 Android 平台上，`ViewRenderer` 類別會具現化原生的 `View` 控制項。 在通用 Windows 平台 (UWP) 上，`ViewRenderer` 類別會具現化原生的 `FrameworkElement` 控制項。 如需 Xamarin.Forms 控制項對應之轉譯器和原生控制項類別的詳細資訊，請參閱[轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明 [`View`](xref:Xamarin.Forms.View) 和實作它之對應原生控制項間的關聯性：

![](hybridwebview-images/view-classes.png "View 類別與其實作原生類別之間的關聯性")

您可在每個平台上建立 [`View`](xref:Xamarin.Forms.View) 的自訂轉譯器，使用轉譯程序實作平台特定自訂。 執行這項作業的程序如下：

1. [建立](#Creating_the_HybridWebView) `HybridWebView` 自訂控制項。
1. [使用](#Consuming_the_HybridWebView) Xamarin.Forms 的 `HybridWebView`。
1. 在每個平台上[建立](#creating-the-custom-renderer-on-each-platform) `HybridWebView` 的自訂轉譯器。

現在您可依序討論每個項目，實作可增強平台特定 Web 控制項的 `HybridWebView` 轉譯器，來從 JavaScript 叫用 C# 程式碼。 `HybridWebView` 執行個體會用以顯示 HTML 網頁，要求使用者輸入其名稱。 然後，當使用者按一下 HTML 按鈕時，JavaScript 函式會叫用 C# `Action`，其顯示包含使用者名稱的快顯視窗。

如需從 JavaScript 叫用 C# 程序的詳細資訊，請參閱[從 JavaScript 叫用C#](#Invoking_C_from_JavaScript)。 如需 HTML 頁面的詳細資訊，請參閱[建立網頁](#Creating_the_Web_Page)。

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>建立 HybridWebView

您可以子類別化 [`View`](xref:Xamarin.Forms.View) 類別來建立 `HybridWebView` 自訂控制項，如下列程式碼範例所示：

```csharp
public class HybridWebView : View
{
  Action<string> action;
  public static readonly BindableProperty UriProperty = BindableProperty.Create (
    propertyName: "Uri",
    returnType: typeof(string),
    declaringType: typeof(HybridWebView),
    defaultValue: default(string));

  public string Uri {
    get { return (string)GetValue (UriProperty); }
    set { SetValue (UriProperty, value); }
  }

  public void RegisterAction (Action<string> callback)
  {
    action = callback;
  }

  public void Cleanup ()
  {
    action = null;
  }

  public void InvokeAction (string data)
  {
    if (action == null || data == null) {
      return;
    }
    action.Invoke (data);
  }
}
```

`HybridWebView` 自訂控制項會在 .NET Standard 程式庫專案中建立，並定義下列控制項的 API：

- 指定要載入之網頁位址的 `Uri` 屬性。
- 向控制項登錄 `Action` 的 `RegisterAction` 方法。 已註冊的動作會從 JavaScript 叫用，此 JavaScript 包含在透過 `Uri` 屬性參考的 HTML 檔案中。
- 移除已註冊 `Action` 之參考的 `CleanUp` 方法。
- 叫用已註冊 `Action` 的 `InvokeAction` 方法。 這個方法以後會從每個平台特定專案的自訂轉譯器呼叫。

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>使用 HybridWebView

您可以宣告控制項的位置命名空間並使用自訂控制項上的命名空間前置詞，在 .NET Standard 程式庫專案的 XAML 中參考 `HybridWebView` 自訂控制項。 下列程式碼範例示範 XAML 頁面如何使用 `HybridWebView` 自訂控制項：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <local:HybridWebView x:Name="hybridWebView" Uri="index.html"
          HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
    </ContentPage.Content>
</ContentPage>
```

`local` 命名空間前置詞沒有命名限制。 不過，`clr-namespace` 和 `assembly` 值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，即會使用前置詞來參考自訂控制項。

下列程式碼範例示範 C# 頁面如何使用 `HybridWebView` 自訂控制項：

```csharp
public class HybridWebViewPageCS : ContentPage
{
  public HybridWebViewPageCS ()
  {
    var hybridWebView = new HybridWebView {
      Uri = "index.html",
      HorizontalOptions = LayoutOptions.FillAndExpand,
      VerticalOptions = LayoutOptions.FillAndExpand
    };
    ...
    Padding = new Thickness (0, 20, 0, 0);
    Content = hybridWebView;
  }
}
```

`HybridWebView` 執行個體會用於顯示每個平台的原生 Web 控制項。 其 `Uri` 屬性設定為儲存在每個平台特定專案中的 HTML 檔，且將由原生 Web 控制項顯示。 轉譯的 HTML 會要求使用者輸入其名稱，以 JavaScript 函式叫用 C# `Action` 以回應 [HTML] 按鈕的按一下動作。

`HybridWebViewPage` 註冊要從 JavaScript 叫用的動作，如下列程式碼範例所示：

```csharp
public partial class HybridWebViewPage : ContentPage
{
  public HybridWebViewPage ()
  {
    ...
    hybridWebView.RegisterAction (data => DisplayAlert ("Alert", "Hello " + data, "OK"));
  }
}
```

這個動作會呼叫 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) 方法，顯示強制回應快顯，此快顯可呈現 `HybridWebView` 執行個體所顯示之 HTML 頁面中輸入的名稱。

藉由允許從 JavaScript 叫用 C# 程式碼，自訂轉譯器現在可以新增至每個應用程式專案來加強平台特定的 Web 控制項。

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每個平台上建立自訂轉譯器

建立自訂轉譯器類別的程序如下：

1. 建立轉譯自訂控制項之 `ViewRenderer<T1,T2>` 類別的子類別。 第一個型別引數應該是轉譯器所針對的自訂控制項，在本例中為 `HybridWebView`。 第二個型別引數應該是會實作自訂檢視的原生控制項。
1. 覆寫轉譯自訂控制項的 `OnElementChanged` 方法，並撰寫自訂方法的邏輯。 此方法會在建立對應 Xamarin.Forms 自訂控制項時呼叫。
1. 將 `ExportRenderer` 屬性新增至自訂轉譯器類別，指定用它轉譯 Xamarin.Forms 自訂控制項。 這個屬性用來向 Xamarin.Forms 註冊自訂轉譯器。

> [!NOTE]
> 對大部分的 Xamarin.Forms 項目而言，可以選擇是否在每個平台專案中提供自訂轉譯器。 如果自訂轉譯器未註冊，則會使用控制項基底類別的預設轉譯器。 不過，轉譯 [View](xref:Xamarin.Forms.View) 項目時，每個平台專案都必須要有自訂轉譯器。

下圖說明應用程式範例中每個專案的責任，以及它們之間的關聯性：

![](hybridwebview-images/solution-structure.png "HybridWebView 自訂轉譯器專案責任")

`HybridWebView` 自訂控制項是由平台特定轉譯器類別轉譯，其全部衍生自各平台的 `ViewRenderer` 類別。 這會導致每個 `HybridWebView` 自訂控制項都使用平台特定 Web 控制項轉譯，如下列螢幕擷取畫面所示：

![](hybridwebview-images/screenshots.png "每個平台上的 HybridWebView")

`ViewRenderer` 類別會公開 `OnElementChanged` 方法，在建立 Xamarin.Forms 自訂控制項以轉譯對應的原生 Web 控制項時，便會呼叫此方法。 此方法會接受 `ElementChangedEventArgs` 參數，其中包含 `OldElement` 和 `NewElement` 屬性。 這些屬性分別代表轉譯器「過去」所附加的 Xamarin.Forms 項目，以及「現在」所附加的 Xamarin.Forms 項目。 在應用程式範例中，`OldElement` 屬性會是 `null`，而 `NewElement` 屬性會包含 `HybridWebView` 執行個體的參考。

在每個平台特定的轉譯器類別中，`OnElementChanged` 方法的覆寫版本是執行原生 Web 控制項具現化和自訂的位置。 您應該使用 `SetNativeControl` 方法來具現化原生 Web 控制項，而且此方法也會將控制項參考指派給 `Control` 屬性。 此外，您可透過 `Element` 屬性取得要轉譯的 Xamarin.Forms 控制項參考。

在某些情況下，可能會多次呼叫 `OnElementChanged` 方法。 因此，為避免記憶體流失，在具現化新的原生控制項時，請務必謹慎。 下列程式碼範例顯示在自訂轉譯器中具現化新的原生控制項時所使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    if (Control == null) {
      // Instantiate the native control and assign it to the Control property with
      // the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

當 `Control` 屬性是 `null` 時，新的原生控制項只應具現化一次。  此外，應該只在自訂控制項附加於新的 Xamarin.Forms 元素時，才建立、設定控制項並訂閱事件處理常式。 同樣地，應該只在轉譯器附加到的元素變更時，才取消訂閱任何已訂閱的事件處理常式。 採用這個方法將有助於建立高效能的自訂轉譯器，避免發生記憶體流失。

> [!IMPORTANT]
> 只有當 `e.NewElement` 不是 `null` 時，才應呼叫 `SetNativeControl`方法。

每個自訂轉譯器類別都裝飾了向 Xamarin.Forms 註冊轉譯器的 `ExportRenderer` 屬性。 此屬性接受兩個參數：正在轉譯的 Xamarin.Forms 自訂控制項類型名稱，以及自訂轉譯器的類型名稱。 屬性的 `assembly` 前置詞會指定套用至整個組件的屬性。

下列各節討論每個原生 Web 控制項載入的網頁結構、從 JavaScript 叫用 C# 的程序，以及此作業在每個平台特定自訂轉譯器類別中的實作。

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>建立網頁

下列程式碼範例示範將由 `HybridWebView` 自訂控制項顯示的網頁：

```html
<html>
<body>
<script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
<h1>HybridWebView Test</h1>
<br/>
Enter name: <input type="text" id="name">
<br/>
<br/>
<button type="button" onclick="javascript:invokeCSCode($('#name').val());">Invoke C# Code</button>
<br/>
<p id="result">Result:</p>
<script type="text/javascript">
function log(str)
{
    $('#result').text($('#result').text() + " " + str);
}

function invokeCSCode(data) {
    try {
        log("Sending Data:" + data);
        invokeCSharpAction(data);
    }
    catch (err){
          log(err);
    }
}
</script>
</body>
</html>
```

網頁可讓使用者在 `input` 項目中輸入他們的姓名，並提供按一下即可叫用 C# 程式碼的 `button` 項目。 執行這項作業的程序如下：

- 當使用者按一下 `button` 項目時，即會呼叫 `invokeCSCode` JavaScript 函式，並將 `input` 項目的值傳遞至函式。
- `invokeCSCode` 函式會呼叫 `log` 函式，顯示它傳送到 C# `Action` 的資料。 然後，它會呼叫 `invokeCSharpAction` 方法來叫用 C# `Action`，傳遞從 `input` 項目收到的參數。

`invokeCSharpAction` JavaScript 函式不是在網頁中定義，而是由每個自訂轉譯器插入至網頁。

在 iOS 上，此 HTML 檔案位於具有 **BundleResource** 建置動作的平台專案 [內容] 資料夾中。 在 Android 上，此 HTML 檔案則位於具有 **AndroidAsset** 建置動作的平台專案 [資產/內容] 資料夾中。

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>從 JavaScript 叫用 C#

每個平台從 JavaScript 叫用 C# 的程序都完全相同：

- 自訂轉譯器會建立原生 Web 控制項，並載入 `HybridWebView.Uri` 屬性指定的 HTML 檔案。
- 一旦載入網頁，自訂轉譯器就會將 `invokeCSharpAction` JavaScript 函式插入至網頁。
- 當使用者輸入其名稱並按一下 HTML `button` 項目時，即會叫用 `invokeCSCode` 函式，然後接著叫用 `invokeCSharpAction` 函式。
- `invokeCSharpAction` 函式會在自訂轉譯器中叫用方法，再接著叫用 `HybridWebView.InvokeAction` 方法。
- `HybridWebView.InvokeAction` 方法叫用已註冊的 `Action`。

下列各節會討論如何在每個平台上實作此程序。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例示範適用於 iOS 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer (typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, WKWebView>, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        protected override void OnElementChanged (ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                userController.RemoveAllUserScripts ();
                userController.RemoveScriptMessageHandler ("invokeAction");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup ();
            }
            if (e.NewElement != null) {
                if (Control == null) {
                    userController = new WKUserContentController ();
                    var script = new WKUserScript (new NSString (JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
                    userController.AddUserScript (script);
                    userController.AddScriptMessageHandler (this, "invokeAction");

                    var config = new WKWebViewConfiguration { UserContentController = userController };
                    var webView = new WKWebView (Frame, config);
                    SetNativeControl (webView);
                }
                string fileName = Path.Combine (NSBundle.MainBundle.BundlePath, string.Format ("Content/{0}", Element.Uri));
                Control.LoadRequest (new NSUrlRequest (new NSUrl (fileName, false)));
            }
        }

        public void DidReceiveScriptMessage (WKUserContentController userContentController, WKScriptMessage message)
        {
            Element.InvokeAction (message.Body.ToString ());
        }
    }
}
```

`HybridWebViewRenderer` 類別會將 `HybridWebView.Uri` 屬性中所指定網頁載入到原生的 [`WKWebView`](xref:WebKit.WKWebView) 控制項中，而 `invokeCSharpAction` JavaScript 函式會插入至網頁。 一旦使用者輸入其名稱並按一下 HTML `button` 項目，即會使用網頁收到訊息後呼叫的 `DidReceiveScriptMessage` 方法來執行 `invokeCSharpAction` JavaScript 函式。 接著，此方法會叫用 `HybridWebView.InvokeAction` 方法，這樣會叫用已註冊的動作來顯示快顯。

執行此功能的程序如下：

- 假設自訂轉譯器附加於新的 Xamarin.Forms 項目：
  - 假設 `Control` 屬性是 `null`，則執行下列作業：
    - 建立 [`WKUserContentController`](xref:WebKit.WKUserContentController) 執行個體，允許在網頁中張貼訊息及插入使用者指令碼。
    - 建立 [`WKUserScript`](xref:WebKit.WKUserScript) 執行個體，在載入網頁後將 `invokeCSharpAction` JavaScript 函式插入至網頁。
    - [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) 方法將 [`WKUserScript`](xref:WebKit.WKUserScript) 執行個體新增至內容控制站。
    - [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) 方法會將名為 `invokeAction` 的指令碼訊息處理常式新增至 [`WKUserContentController`](xref:WebKit.WKUserContentController) 執行個體，而這會定義所有使用 `WKUserContentController` 執行個體之 Web 檢視中所有框架的 JavaScript 函式 `window.webkit.messageHandlers.invokeAction.postMessage(data)`。
    - 建立 [`WKWebViewConfiguration`](xref:WebKit.WKWebViewConfiguration) 執行個體，並將 [`WKUserContentController`](xref:WebKit.WKUserContentController) 執行個體設為內容控制站。
    - 具現化 [`WKWebView`](xref:WebKit.WKWebView) 控制項，並呼叫 `SetNativeControl` 方法將 `WKWebView` 控制項的參考指派給 `Control` 屬性。
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

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

下列程式碼範例示範適用於 Android 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                    var webView = new Android.Webkit.WebView(_context);
                    webView.Settings.JavaScriptEnabled = true;
                    webView.SetWebViewClient(new JavascriptWebViewClient($"javascript: {JavascriptFunction}"));
                    SetNativeControl(webView);
                }
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{Element.Uri}");
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
  - 假設 `Control` 屬性是 `null`，則執行下列作業：
    - 建立原生 [`WebView`](xref:Android.Webkit.WebView) 執行個體 (控制項中已啟用 JavaScript)，並將 `JavascriptWebViewClient` 執行個體設為 `WebViewClient` 的實作。
    - 然後會呼叫 `SetNativeControl` 方法，將原生 [`WebView`](xref:Android.Webkit.WebView) 控制項的參考指派給 `Control` 屬性。
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

  public JSBridge (HybridWebViewRenderer hybridRenderer)
  {
    hybridWebViewRenderer = new WeakReference <HybridWebViewRenderer> (hybridRenderer);
  }

  [JavascriptInterface]
  [Export ("invokeAction")]
  public void InvokeAction (string data)
  {
    HybridWebViewRenderer hybridRenderer;

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer))
    {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

類別必須衍生自 `Java.Lang.Object`，而向 JavaScript 公開的方法則必須使用 `[JavascriptInterface]` 和 `[Export]` 屬性裝飾。 因此，當 `invokeCSharpAction` JavaScript 函式插入至網頁且執行時，它會因為正使用 `[JavascriptInterface]` 和 `[Export("invokeAction")]` 屬性裝飾而呼叫 `JSBridge.InvokeAction` 方法。 接著，`InvokeAction` 方法會叫用 `HybridWebView.InvokeAction` 方法，這樣會叫用已註冊的動作來顯示快顯。

> [!NOTE]
> 使用 `[Export]` 屬性的專案必須包含 `Mono.Android.Export` 的參考，否則會產生編譯器錯誤。

請注意，`JSBridge` 類別會將 `WeakReference` 維持為 `HybridWebViewRenderer` 類別。 這是為了避免建立兩個類別之間的循環參考。 如需詳細資訊，請參閱 MSDN 上的[弱式參考](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx)。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上建立自訂轉譯器

下列程式碼範例示範適用於 UWP 的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Windows.UI.Xaml.Controls.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                    SetNativeControl(new Windows.UI.Xaml.Controls.WebView());
                }
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri(string.Format("ms-appx-web:///Content//{0}", Element.Uri));
            }
        }

        async void OnWebViewNavigationCompleted(WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            Element.InvokeAction(e.Value);
        }
    }
}
```

`HybridWebViewRenderer` 類別會將 `HybridWebView.Uri` 屬性中所指定網頁載入到原生的 `WebView` 控制項中，並在網頁載入後，使用 `WebView.InvokeScriptAsync` 方法將 `invokeCSharpAction` JavaScript 函式插入至網頁。 一旦使用者輸入其名稱並按一下 HTML `button` 項目，即會使用網頁收到通知後呼叫的 `OnWebViewScriptNotify` 方法來執行 `invokeCSharpAction` JavaScript 函式。 接著，此方法會叫用 `HybridWebView.InvokeAction` 方法，這樣會叫用已註冊的動作來顯示快顯。

執行此功能的程序如下：

- 假設自訂轉譯器附加於新的 Xamarin.Forms 項目：
  - 假設 `Control` 屬性是 `null`，則執行下列作業：
    - 呼叫 `SetNativeControl` 方法來具現化新的原生 `WebView` 控制項，並將其參考指派給 `Control` 屬性。
  - 則會註冊 `NavigationCompleted` 和 `ScriptNotify` 事件的事件處理常式。 當原生 `WebView` 控制項已完成載入目前的內容或導覽失敗時，會引發 `NavigationCompleted` 事件。 當原生 `WebView` 控制項使用 JavaScript 將字串傳遞至應用程式時，會引發 `ScriptNotify` 事件。 網頁在傳遞 `string` 參數時呼叫 `window.external.notify`，藉以引發 `ScriptNotify` 事件。
  - `WebView.Source` 屬性設為 `HybridWebView.Uri` 屬性所指定之 HTML 檔案的 URI。 程式碼假設該檔案會儲存在專案的 `Content` 資料夾中。 一旦顯示網頁，就會引發 `NavigationCompleted` 事件並叫用 `OnWebViewNavigationCompleted` 方法。 然後使用 `WebView.InvokeScriptAsync` 方法將 `invokeCSharpAction` JavaScript 函式插入至網頁，假設導覽已順利完成。
- 當轉譯器附加至的項目變更時：
  - 就會由此處取消訂閱事件。

## <a name="summary"></a>總結

本文示範如何建立 `HybridWebView` 自訂控制項的自訂轉譯器，該自訂控制項會示範如何強化平台特定的 Web 控制項，以允許從 JavaScript 叫用 C# 程式碼。


## <a name="related-links"></a>相關連結

- [CustomRendererHybridWebView (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)
- [從 JavaScript 呼叫 C#](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)

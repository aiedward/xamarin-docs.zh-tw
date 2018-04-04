---
title: 實作 HybridWebView
description: Xamarin.Forms 自訂使用者介面控制項應衍生自檢視類別，用來放置版面配置和螢幕上的控制項。 本文示範如何建立自訂轉譯器 HybridWebView 自訂控制項，示範如何以加強平台專屬 web 控制項可讓 C# 程式碼叫用 javascript。
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 4adff8a95f9981dbecc44bf177dcd98b7984a3a9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="implementing-a-hybridwebview"></a>實作 HybridWebView

_Xamarin.Forms 自訂使用者介面控制項應衍生自檢視類別，用來放置版面配置和螢幕上的控制項。本文示範如何建立自訂轉譯器 HybridWebView 自訂控制項，示範如何以加強平台專屬 web 控制項可讓 C# 程式碼叫用 javascript。_

Xamarin.Forms 中的每個檢視有隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) Xamarin.Forms 應用程式在 iOS 中，呈現`ViewRenderer`類別具現化，這又會具現化原生`UIView`控制項。 Android 平台上，`ViewRenderer`類別具現化`View`控制項。 在 Windows Phone 和通用 Windows 平台 (UWP)，`ViewRenderer`類別具現化的原生`FrameworkElement`控制項。 如需有關轉譯器，而且 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱[轉譯器的基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)和對應的原生控制項實作它：

![](hybridwebview-images/view-classes.png "檢視類別和實作的原生類別之間的關聯性")

轉譯程序可用來藉由建立自訂轉譯器的實作特定平台自訂[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)每個平台上。 執行此程序如下所示：

1. [建立](#Creating_the_HybridWebView)`HybridWebView`自訂控制項。
1. [取用](#Consuming_the_HybridWebView)`HybridWebView`從 Xamarin.Forms。
1. [建立](#Creating_the_Custom_Renderer_on_each_Platform)的自訂轉譯器`HybridWebView`每個平台上。

每個項目將現在依次討論實作`HybridWebView`增強的平台專屬 web 控制項可讓 C# 程式碼叫用來自 JavaScript 的轉譯器。 `HybridWebView`執行個體將會用來顯示要求使用者輸入其名稱的 HTML 網頁。 接著，當使用者按一下 HTML 按鈕時，JavaScript 函式會叫用 C#`Action`會顯示快顯視窗包含使用者名稱。

叫用 C# 從 JavaScript 程序的詳細資訊，請參閱[叫用 C# 從 JavaScript](#Invoking_C_from_JavaScript)。 如需有關 HTML 網頁的詳細資訊，請參閱[建立網頁](#Creating_the_Web_Page)。

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>建立 HybridWebView

`HybridWebView`可以建立自訂控制項，透過子類別化[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)類別，如下列程式碼範例所示：

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

`HybridWebView`建立可攜式類別庫 (PCL) 專案中自訂控制項，並定義控制項的下列 API:

- A`Uri`屬性，指定要載入的網頁位址。
- A`RegisterAction`方法註冊`Action`與控制項。 註冊的動作將會叫用從透過參考的 HTML 檔案中所包含的 JavaScript`Uri`屬性。
- A`CleanUp`方法，以移除已註冊的參考`Action`。
- `InvokeAction`方法叫用已註冊的`Action`。 從每個平台專屬專案中自訂轉譯器，就會呼叫這個方法。

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>耗用 HybridWebView

`HybridWebView`自訂控制項可以在 XAML 中參考 PCL 專案中，宣告的命名空間，做為其位置，並使用自訂控制項上的命名空間前置詞。 下列程式碼範例示範如何`HybridWebView`自訂控制項以供 XAML 頁面：

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

`local`命名空間前置詞可以命名為任何項目。 不過，`clr-namespace`和`assembly`值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，前置詞用來參考自訂控制項。

下列程式碼範例示範如何`HybridWebView`自訂控制項可供 C# 頁面：

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

`HybridWebView`執行個體將會用來顯示每個平台上的原生 web 控制項。 它有`Uri`屬性設定為儲存在每個平台專屬專案中，且，會顯示由原生 web 控制項的 HTML 檔案。 呈現的 HTML 會要求使用者輸入其名稱，以叫用 C# 的 JavaScript 函式`Action`HTML 按鈕 click 回應。

`HybridWebViewPage`註冊從 JavaScript 中，叫用的動作，如下列程式碼範例所示：

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

這個動作會呼叫[ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/)方法，以顯示強制回應的快顯視窗，顯示所顯示的 HTML 頁面中輸入的名稱`HybridWebView`執行個體。

自訂轉譯器現在可以新增至每個應用程式專案，以加強平台專屬 web 控制項允許從 JavaScript 叫用的 C# 程式碼。

<a nane="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>每個平台上建立的自訂轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`ViewRenderer<T1,T2>`呈現自訂控制項的類別。 第一個型別引數應該是自訂控制項轉譯器，在此情況下`HybridWebView`。 第二個型別引數應該是原生控制項將實作自訂的檢視。
1. 覆寫`OnElementChanged`呈現自訂它的自訂控制項和寫入邏輯方法。 建立對應的 Xamarin.Forms 自訂控制項時，會呼叫這個方法。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 自訂控制項的自訂轉譯器類別。 此屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 大部分的 Xamarin.Forms 項目，則是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，將使用預設的產生器控制項的基底類別。 不過，自訂轉譯器所需每個平台專案中時呈現[檢視](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)項目。

下圖說明範例應用程式，以及它們之間的關聯性中的每一個專案的責任：

![](hybridwebview-images/solution-structure.png "HybridWebView 自訂轉譯器專案責任")

`HybridWebView`自訂控制項的呈現由平台特定轉譯器類別，這些全都衍生自`ViewRenderer`每個平台的類別。 這會導致每個`HybridWebView`呈現由平台專屬 web 控制項，如下列螢幕擷取畫面所示的自訂控制項：

![](hybridwebview-images/screenshots.png "每個平台上 HybridWebView")

`ViewRenderer`類別會公開`OnElementChanged`Xamarin.Forms 自訂控制項是用來呈現對應的原生 web 控制項時所呼叫的方法。 這個方法會採用`ElementChangedEventArgs`參數包含`OldElement`和`NewElement`屬性。 這些屬性代表 Xamarin.Forms 項目，轉譯器*已*附加和 Xamarin.Forms 的項目，轉譯器*是*附加至分別。 範例應用程式中`OldElement`屬性會是`null`和`NewElement`屬性會包含參考`HybridWebView`執行個體。

覆寫的版本`OnElementChanged`方法，在每個平台特定轉譯器類別，是要執行的原生 web 控制項具現化及自訂的位置。 `SetNativeControl`方法應該使用原生 web 控制項，具現化，而且這個方法也會指派的控制參考`Control`屬性。 此外，要呈現 Xamarin.Forms 控制項的參考可以透過取得`Element`屬性。

在某些情況下`OnElementChanged`方法可以呼叫多次。 因此，若要避免記憶體流失，必須小心具現化新的原生控制項時。 下列程式碼範例顯示在自訂轉譯器中具現化新的原生控制項時所使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control and assign it to the Control property with
    // the SetNativeControl method
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

當 `Control` 屬性是 `null` 時，新的原生控制項只應具現化一次。 應該只在自訂控制項附加於新的 Xamarin.Forms 元素時，才設定控制項並訂閱事件處理常式。 同樣地，應該只在轉譯器附加到的元素變更時，才取消訂閱任何已訂閱的事件處理常式。 採用這個方法可協助建立高效能的自訂轉譯器，不會發生記憶體流失。

每個自訂轉譯器類別以裝飾`ExportRenderer`xamarin.forms 註冊轉譯器的屬性。 屬性會採用兩個參數 – Xamarin.Forms 自訂控制項所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列章節會討論每個原生 web 控制項，叫用 C# 的 JavaScript 中，從處理序，在每個平台專屬的自訂轉譯器類別的實作載入網頁的結構。

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>建立 Web 網頁

下列程式碼範例示範將依顯示的 web 網頁`HybridWebView`自訂控制項：

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

網頁可讓使用者輸入其名稱中的`input`項目，並提供`button`會叫用 C# 程式碼時按下的項目。 要達到這個程序如下所示：

- 當使用者按一下`button`項目，`invokeCSCode`呼叫 JavaScript 函式時，其值為`input`傳遞至函式項目。
- `invokeCSCode`函式呼叫`log`函式來顯示的資料傳送到 C# `Action`。 然後它會呼叫`invokeCSharpAction`方法來叫用 C# `Action`，傳遞參數從收到`input`項目。

`invokeCSharpAction` JavaScript 函式未定義在網頁上，且會將每個自訂轉譯器插入到其中。

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>叫用 C# 從 JavaScript

叫用 C# 來自 JavaScript 的程序是在每個平台上完全相同：

- 自訂轉譯器會建立原生 web 控制項，並載入所指定的 HTML 檔案`HybridWebView.Uri`屬性。
- 一旦載入網頁時，自訂轉譯器會插入`invokeCSharpAction`JavaScript 函式匯入該網頁。
- 當使用者輸入其名稱，然後按一下 HTML`button`項目，`invokeCSCode`函式會叫用，它會接著叫用`invokeCSharpAction`函式。
- `invokeCSharpAction`函式會叫用的方法會接著叫用的自訂轉譯器中`HybridWebView.InvokeAction`方法。
- `HybridWebView.InvokeAction`方法會叫用已註冊`Action`。

下列各節將討論此程序在每個平台上的實作方式。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例會顯示 iOS 平台的自訂轉譯器：

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

            if (Control == null) {
                userController = new WKUserContentController ();
                var script = new WKUserScript (new NSString (JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
                userController.AddUserScript (script);
                userController.AddScriptMessageHandler (this, "invokeAction");

                var config = new WKWebViewConfiguration { UserContentController = userController };
                var webView = new WKWebView (Frame, config);
                SetNativeControl (webView);
            }
            if (e.OldElement != null) {
                userController.RemoveAllUserScripts ();
                userController.RemoveScriptMessageHandler ("invokeAction");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup ();
            }
            if (e.NewElement != null) {
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

`HybridWebViewRenderer`類別載入中指定的網頁`HybridWebView.Uri`原生物件的屬性[ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/)控制項，而`invokeCSharpAction`JavaScript 函式插入 web 網頁。 一旦使用者輸入其名稱並按 HTML`button`項目， `invokeCSharpAction` JavaScript 函式執行時，與`DidReceiveScriptMessage`網頁上收到訊息之後呼叫的方法。 接著，這個方法會叫用`HybridWebView.InvokeAction`方法，將會叫用已註冊的動作，以顯示快顯視窗中。

達成這項功能，如下所示：

- 前提是`Control`屬性是`null`，執行下列作業：
  - A [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/)建立執行個體時，可讓公佈訊息，並將插入 web 網頁，以便使用者指令碼。
  - A [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/)執行個體是用來插入`invokeCSharpAction`JavaScript 函式匯入之後載入網頁的網頁。
  - [ `WKUserContentController.AddScript` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddUserScript/p/WebKit.WKUserScript/)方法會將[ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/)內容的控制站的執行個體。
  - [ `WKUserContentController.AddScriptMessageHandler` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddScriptMessageHandler/p/WebKit.IWKScriptMessageHandler/System.String/)方法會將指令碼訊息的處理常式，名為`invokeAction`至[ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/)執行個體，這會導致 JavaScript 函式`window.webkit.messageHandlers.invokeAction.postMessage(data)`中所有定義將使用的所有 web 檢視中的框架`WKUserContentController`執行個體。
  - A [ `WKWebViewConfiguration` ](https://developer.xamarin.com/api/type/WebKit.WKWebViewConfiguration/)建立執行個體時，與[ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/)設定為內容的控制站的執行個體。
  - A [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/)控制具現化，和`SetNativeControl`呼叫方法，將參考指派給`WKWebView`控制權傳輸至`Control`屬性。
- 提供的自訂轉譯器會附加至新的 Xamarin.Forms 項目：
  - [ `WKWebView.LoadRequest` ](https://developer.xamarin.com/api/member/WebKit.WKWebView.LoadRequest/p/Foundation.NSUrlRequest/)方法會將所指定的 HTML 檔案載入`HybridWebView.Uri`屬性。 程式碼會指定該檔案會儲存在`Content`專案的資料夾。 一旦顯示網頁， `invokeCSharpAction` JavaScript 函式會插入至網頁。
- 當轉譯器的項目附加至變更：
  - 會釋放資源。

> [!NOTE]
> `WKWebView` IOS 8 及更新版本中才支援類別。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立的自訂轉譯器

下列程式碼範例會顯示 Android 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                var webView = new Android.Webkit.WebView(_context);
                webView.Settings.JavaScriptEnabled = true;
                SetNativeControl(webView);
            }
            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }
            if (e.NewElement != null)
            {
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl(string.Format("file:///android_asset/Content/{0}", Element.Uri));
                InjectJS(JavaScriptFunction);
            }
        }

        void InjectJS(string script)
        {
            if (Control != null)
            {
                Control.LoadUrl(string.Format("javascript: {0}", script));
            }
        }
    }
}
```

`HybridWebViewRenderer`類別載入中指定的網頁`HybridWebView.Uri`原生物件的屬性[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)控制項，而`invokeCSharpAction`JavaScript 函式插入網頁，網頁已經載入，與`InjectJS`方法。 一旦使用者輸入其名稱並按 HTML`button`項目， `invokeCSharpAction` JavaScript 函式會執行。 達成這項功能，如下所示：

- 前提是`Control`屬性是`null`，執行下列作業：
  - 原生[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)建立執行個體，並在控制項中啟用 JavaScript。
  - `SetNativeControl`呼叫方法，將參考指派給原生[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)控制權傳輸至`Control`屬性。
- 提供的自訂轉譯器會附加至新的 Xamarin.Forms 項目：
  - [ `WebView.AddJavascriptInterface` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/)方法會將新`JSBridge`到主框架的 WebView 的 JavaScript 內容，其命名的執行個體`jsBridge`。 這可讓方法中的`JSBridge`從 JavaScript 存取的類別。
  - [ `WebView.LoadUrl` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/)方法會將所指定的 HTML 檔案載入`HybridWebView.Uri`屬性。 程式碼會指定該檔案會儲存在`Content`專案的資料夾。
  - `InjectJS`方法會叫用來插入`invokeCSharpAction`JavaScript 函式匯入該網頁。
- 當轉譯器的項目附加至變更：
  - 會釋放資源。

當`invokeCSharpAction`執行 JavaScript 函式，它會接著叫用`JSBridge.InvokeAction`方法，下列程式碼範例所示：

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

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer)) {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

類別必須衍生自`Java.Lang.Object`，並公開給 JavaScript 的方法必須使用裝飾`[JavascriptInterface]`和`[Export]`屬性。 因此，當`invokeCSharpAction`JavaScript 函式插入至網頁，並執行，它會呼叫`JSBridge.InvokeAction`方法，因為正在使用裝飾`[JavascriptInterface]`和`[Export("invokeAction")]`屬性。 接著，`InvokeAction`方法會叫用`HybridWebView.InvokeAction`將叫用已註冊的動作，以顯示快顯視窗中的方法。

> [!NOTE]
> 專案使用`[Export]`屬性必須包含的參考`Mono.Android.Export`，否則會產生編譯器錯誤。

請注意，`JSBridge`類別會維護`WeakReference`至`HybridWebViewRenderer`類別。 這是為了避免建立兩個類別之間的循環參考。 如需詳細資訊，請參閱[弱式參考](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx)MSDN 上。

> [!IMPORTANT]
> 在 Android Oreo 確定 Android 資訊清單設定**目標 Android 版本**至**自動**。 否則，執行此程式碼會產生錯誤訊息"invokeCSharpAction 未定義 」。

### <a name="creating-the-custom-renderer-on-windows-phone-and-uwp"></a>建立自訂轉譯器，在 Windows Phone 和 UWP

下列程式碼範例顯示 Windows Phone 和 UWP 的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.WinPhone81
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Windows.UI.Xaml.Controls.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                SetNativeControl(new Windows.UI.Xaml.Controls.WebView());
            }
            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
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

`HybridWebViewRenderer`類別載入中指定的網頁`HybridWebView.Uri`原生物件的屬性`WebView`控制項，而`invokeCSharpAction`JavaScript 函式插入網頁，網頁已經載入，與`WebView.InvokeScriptAsync`方法。 一旦使用者輸入其名稱並按 HTML`button`項目， `invokeCSharpAction` JavaScript 函式執行時，與`OnWebViewScriptNotify`網頁上收到通知之後呼叫的方法。 接著，這個方法會叫用`HybridWebView.InvokeAction`方法，將會叫用已註冊的動作，以顯示快顯視窗中。

達成這項功能，如下所示：

- 前提是`Control`屬性是`null`，執行下列作業：
  - `SetNativeControl`呼叫方法來具現化新的原生`WebView`控制，並將該參考指派`Control`屬性。
- 提供的自訂轉譯器會附加至新的 Xamarin.Forms 項目：
  - 事件處理常式`NavigationCompleted`和`ScriptNotify`登錄事件。 `NavigationCompleted`事件引發時的原生`WebView`控制項已完成載入目前的內容，或如果巡覽失敗。 `ScriptNotify`事件引發時在原生內容`WebView`控制項用來將字串傳遞至應用程式的 JavaScript。 Web 網頁引發`ScriptNotify`藉由呼叫事件`window.external.notify`時傳遞`string`參數。
  - `WebView.Source`屬性設定為指定的 HTML 檔案的 URI`HybridWebView.Uri`屬性。 此程式碼會假設檔案儲存在`Content`專案的資料夾。 一旦顯示網頁，`NavigationCompleted`事件就會引發和`OnWebViewNavigationCompleted`叫用方法。 `invokeCSharpAction` JavaScript 函數，然後會插入至網頁`WebView.InvokeScriptAsync`方法，但前提是已順利完成的導覽。
- 當轉譯器的項目附加至變更：
  - 事件會從取消訂閱。

## <a name="summary"></a>總結

本文示範如何建立自訂轉譯器`HybridWebView`自訂控制項，可示範如何以加強平台專屬 web 控制項可讓 C# 程式碼叫用 javascript。


## <a name="related-links"></a>相關連結

- [CustomRendererHybridWebView （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [從 JavaScript 呼叫 C#](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript/)

---
title: 實作 HybridWebView
description: 這篇文章會示範如何建立自訂轉譯器 HybridWebView 自訂控制，示範如何強化平台專屬的 web 控制項，以允許從 JavaScript 的 C# 程式碼來叫用。
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: f0b21277b91c44edbb574aece92664de2e49a65a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996356"
---
# <a name="implementing-a-hybridwebview"></a>實作 HybridWebView

_Xamarin.Forms 自訂使用者介面控制項應該衍生自檢視類別，用來放置版面配置和螢幕上的控制項。這篇文章會示範如何建立自訂轉譯器 HybridWebView 自訂控制，示範如何強化平台專屬的 web 控制項，以允許從 JavaScript 的 C# 程式碼來叫用。_

Xamarin.Forms 中的每個檢視都有隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `View` ](xref:Xamarin.Forms.View) Xamarin.Forms 應用程式在 iOS 中，呈現`ViewRenderer`類別具現化，以依序具現化原生`UIView`控制項。 Android 平台上，`ViewRenderer`類別會具現化`View`控制項。 在通用 Windows 平台 (UWP)，`ViewRenderer`類別會具現化原生`FrameworkElement`控制項。 如需有關轉譯器和 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱 <<c0> [ 轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `View` ](xref:Xamarin.Forms.View)和對應的原生控制項實作它：

![](hybridwebview-images/view-classes.png "檢視類別與它實作的原生類別之間的關聯性")

轉譯程序可以用來建立自訂轉譯器來實作平台專屬的自訂[ `View` ](xref:Xamarin.Forms.View)每個平台。 執行此動作的程序如下所示：

1. [建立](#Creating_the_HybridWebView)`HybridWebView`自訂控制項。
1. [取用](#Consuming_the_HybridWebView)`HybridWebView`從 Xamarin.Forms。
1. [建立](#Creating_the_Custom_Renderer_on_each_Platform)的自訂轉譯器`HybridWebView`每個平台。

每個項目會現在依次討論實作`HybridWebView`增強以允許從 JavaScript 的 C# 程式碼來叫用的平台專屬的 web 控制項的轉譯器。 `HybridWebView`執行個體將會用來顯示會要求使用者輸入其名稱的 HTML 網頁。 然後，當使用者按一下的 [HTML] 按鈕，將會叫用的 JavaScript 函式 C#`Action`顯示快顯視窗，其中包含使用者名稱。

叫用 C# 從 JavaScript 程序的相關資訊，請參閱[叫用 C# 從 JavaScript](#Invoking_C_from_JavaScript)。 如需有關 HTML 頁面的詳細資訊，請參閱[建立網頁](#Creating_the_Web_Page)。

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>建立 HybridWebView

`HybridWebView`可以建立自訂控制項的子類別化[ `View` ](xref:Xamarin.Forms.View)類別，如下列程式碼範例所示：

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

`HybridWebView` .NET Standard 程式庫專案中建立自訂控制項，並定義控制項的下列 API:

- A`Uri`屬性，指定要載入的網頁位址。
- A`RegisterAction`註冊的方法`Action`與控制項。 會叫用已註冊的動作，從透過參考 HTML 檔案中包含的 JavaScript`Uri`屬性。
- A`CleanUp`方法，以移除已註冊的參考`Action`。
- `InvokeAction`叫用已註冊的方法`Action`。 從每個平台專屬專案中自訂轉譯器，就會呼叫這個方法。

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>使用 HybridWebView

`HybridWebView`自訂控制項可以在 XAML 中參考.NET Standard 程式庫專案中，藉由宣告其位置的命名空間，並使用自訂控制項的命名空間前置詞。 下列程式碼範例示範如何`HybridWebView`自訂控制項可供 XAML 頁面：

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

`HybridWebView`執行個體將會用來顯示每個平台的原生 web 控制項。 它有`Uri`屬性設定為 HTML 檔儲存在每個平台專屬專案中，將原生 web 控制項所顯示哪些。 轉譯的 HTML 會要求使用者輸入其名稱、 使用 JavaScript 函式叫用 C# `Action` HTML 按鈕 click 回應。

`HybridWebViewPage`註冊從 JavaScript 中，叫用動作，如下列程式碼範例所示：

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

這個動作會呼叫[ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))方法，以顯示強制回應快顯視窗，顯示所顯示的 HTML 頁面中輸入的名稱，`HybridWebView`執行個體。

自訂轉譯器現在可以新增至每個應用程式專案，以允許從 JavaScript 叫用的 C# 程式碼來加強平台專屬的 web 控制項。

<a nane="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>每個平台上建立自訂轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`ViewRenderer<T1,T2>`呈現自訂控制項的類別。 第一個類型引數應該是自訂控制項轉譯器，在此情況下`HybridWebView`。 第二個類型引數應該是原生控制項，將實作自訂的檢視。
1. 覆寫`OnElementChanged`呈現來自訂它的自訂控制及寫入邏輯的方法。 建立對應的 Xamarin.Forms 自訂控制項時，會呼叫這個方法。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 自訂控制項的自訂轉譯器類別。 這個屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 大部分的 Xamarin.Forms 元素，則是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，則會使用預設的轉譯器控制項的基底類別。 不過，自訂轉譯器所需的每個平台專案中時轉譯[檢視](xref:Xamarin.Forms.View)項目。

下圖說明範例應用程式，以及其間的關聯性中的每個專案的責任：

![](hybridwebview-images/solution-structure.png "HybridWebView 自訂轉譯器專案責任")

`HybridWebView`自訂控制項的呈現的平台特定的轉譯器類別，這些全都衍生自`ViewRenderer`每個平台的類別。 這會導致每個`HybridWebView`如下列螢幕擷取畫面所示，使用平台專屬的 web 控制項所呈現的自訂控制項：

![](hybridwebview-images/screenshots.png "每個平台的 HybridWebView")

`ViewRenderer`類別會公開`OnElementChanged`方法，來呈現對應的原生 web 控制項建立 Xamarin.Forms 自訂控制項時呼叫。 這個方法會採用`ElementChangedEventArgs`參數，其中包含`OldElement`和`NewElement`屬性。 這些屬性代表 Xamarin.Forms 項目，轉譯器*已*附加至，和 Xamarin.Forms 的項目，轉譯器*是*附加分別。 在範例應用程式`OldElement`屬性會是`null`並`NewElement`屬性會包含參考`HybridWebView`執行個體。

覆寫的新版`OnElementChanged`方法，在每個平台特定的轉譯器類別，是要執行的原生 web 控制項具現化及自訂的位置。 `SetNativeControl`具現化原生 web 控制項，應該使用方法，而且這個方法也會將控制項參考指派`Control`屬性。 此外，所呈現的 Xamarin.Forms 控制項的參考可以透過取得`Element`屬性。

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

當 `Control` 屬性是 `null` 時，新的原生控制項只應具現化一次。 應該只在自訂控制項附加於新的 Xamarin.Forms 元素時，才設定控制項並訂閱事件處理常式。 同樣地，應該只在轉譯器附加到的元素變更時，才取消訂閱任何已訂閱的事件處理常式。 採用這個方法將有助於建立高效能的自訂轉譯器不會發生記憶體流失。

每個自訂轉譯器類別裝飾了`ExportRenderer`向 Xamarin.Forms 中的轉譯器的屬性。 屬性會採用兩個參數-Xamarin.Forms 自訂控制項所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列各節討論的每個原生 web 控制項，叫用 C# 從 JavaScript 的程序，這個實作，每個平台專屬的自訂轉譯器類別中載入網頁的結構。

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>建立網頁

下列程式碼範例示範將所顯示的 web 網頁`HybridWebView`自訂控制項：

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

網頁可讓使用者輸入他們的姓名`input`項目，並提供`button`會叫用 C# 程式碼時按下的項目。 達到此目標的程序如下所示：

- 當使用者按一下`button`項目，`invokeCSCode`呼叫 JavaScript 函式時，其值為`input`傳遞至函式的項目。
- `invokeCSCode`函式會呼叫`log`函式來顯示的資料傳送到 C# `Action`。 然後它會呼叫`invokeCSharpAction`方法來叫用 C# `Action`，並傳遞參數從收到`input`項目。

`invokeCSharpAction` JavaScript 函式未定義在網頁上，且會插入至它，每個自訂轉譯器。

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>叫用 C# 從 JavaScript

叫用 C# 從 JavaScript 的程序是在每個平台上完全相同：

- 自訂轉譯器會建立原生 web 控制項，並載入所指定的 HTML 檔案`HybridWebView.Uri`屬性。
- 一旦載入網頁時，自訂轉譯器會插入`invokeCSharpAction`到網頁的 JavaScript 函式。
- 當使用者輸入其名稱，然後按一下 HTML`button`項目，`invokeCSCode`函式會叫用，這會接著叫用`invokeCSharpAction`函式。
- `invokeCSharpAction`函式會叫用中自訂轉譯器會接著叫用的方法`HybridWebView.InvokeAction`方法。
- `HybridWebView.InvokeAction`方法會叫用已註冊`Action`。

下列各節將討論每個平台上實作此程序的方式。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例顯示適用於 iOS 平台的自訂轉譯器：

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

`HybridWebViewRenderer`類別載入網頁所述`HybridWebView.Uri`屬性插入原生[ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/)控制項，而`invokeCSharpAction`JavaScript 函式會插入 web 網頁。 一旦使用者輸入其名稱，然後按一下 HTML`button`項目， `invokeCSharpAction` JavaScript 函式執行時，使用`DidReceiveScriptMessage`網頁上收到訊息之後呼叫的方法。 接著，此方法會叫用`HybridWebView.InvokeAction`方法，將會叫用已註冊的動作，以顯示快顯視窗中。

這項功能為止，如下所示：

- 前提`Control`屬性是`null`，執行下列作業：
  - A [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/)建立執行個體，以便將訊息張貼，並插入網頁中的使用者指令碼。
  - A [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/)將會建立執行個體`invokeCSharpAction`到網頁載入後的網頁的 JavaScript 函式。
  - [ `WKUserContentController.AddScript` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddUserScript/p/WebKit.WKUserScript/)方法會加入[ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/)內容的控制站的執行個體。
  - [ `WKUserContentController.AddScriptMessageHandler` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddScriptMessageHandler/p/WebKit.IWKScriptMessageHandler/System.String/)方法會將名為指令碼訊息處理常式`invokeAction`來[ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/)執行個體，這會導致 JavaScript 函式`window.webkit.messageHandlers.invokeAction.postMessage(data)`中所有定義將使用的所有 web 檢視中的框架`WKUserContentController`執行個體。
  - A [ `WKWebViewConfiguration` ](https://developer.xamarin.com/api/type/WebKit.WKWebViewConfiguration/)建立執行個體時，與[ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/)設定為內容的控制站的執行個體。
  - A [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/)控制項具現化，而`SetNativeControl`呼叫方法來指派參考`WKWebView`控制權傳輸至`Control`屬性。
- 前提是自訂轉譯器附加至新的 Xamarin.Forms 元素：
  - [ `WKWebView.LoadRequest` ](https://developer.xamarin.com/api/member/WebKit.WKWebView.LoadRequest/p/Foundation.NSUrlRequest/)方法會載入所指定的 HTML 檔案`HybridWebView.Uri`屬性。 程式碼會指定該檔案會儲存在`Content`專案的資料夾。 一旦顯示網頁， `invokeCSharpAction` JavaScript 函式會插入至網頁。
- 當項目轉譯器附加至變更：
  - 釋放資源。

> [!NOTE]
> `WKWebView` IOS 8 和更新版本才支援類別。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

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

`HybridWebViewRenderer`類別載入網頁所述`HybridWebView.Uri`屬性到原生[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)控制項，和`invokeCSharpAction`JavaScript 函式會插入網頁中，載入網頁，使用`InjectJS`方法。 一旦使用者輸入其名稱，然後按一下 HTML`button`項目，`invokeCSharpAction`會執行 JavaScript 函式。 這項功能為止，如下所示：

- 前提`Control`屬性是`null`，執行下列作業：
  - 原生[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)建立執行個體，並在控制項中啟用 JavaScript。
  - `SetNativeControl`會呼叫方法以將參考指派給原生[ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/)若要控制`Control`屬性。
- 前提是自訂轉譯器附加至新的 Xamarin.Forms 元素：
  - [ `WebView.AddJavascriptInterface` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/)方法會插入新`JSBridge`執行個體到主框架的 WebView 的 JavaScript 內容，將它命名為`jsBridge`。 這可讓方法在`JSBridge`從 JavaScript 存取的類別。
  - [ `WebView.LoadUrl` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/)方法會載入所指定的 HTML 檔案`HybridWebView.Uri`屬性。 程式碼會指定該檔案會儲存在`Content`專案的資料夾。
  - `InjectJS`方法會叫用來插入`invokeCSharpAction`到網頁的 JavaScript 函式。
- 當項目轉譯器附加至變更：
  - 釋放資源。

當`invokeCSharpAction`會執行 JavaScript 函式，它會接著叫用`JSBridge.InvokeAction`方法，以下列程式碼範例所示：

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

類別必須衍生自`Java.Lang.Object`，而且會向 JavaScript 公開的方法必須使用裝飾`[JavascriptInterface]`和`[Export]`屬性。 因此，當`invokeCSharpAction`JavaScript 函式插入至網頁，並執行，它會呼叫`JSBridge.InvokeAction`方法，因為正在附有`[JavascriptInterface]`和`[Export("invokeAction")]`屬性。 依次`InvokeAction`方法會叫用`HybridWebView.InvokeAction`哪些會叫用已註冊的動作，以顯示快顯視窗中的方法。

> [!NOTE]
> 專案使用`[Export]`屬性必須包含的參考`Mono.Android.Export`，否則會產生編譯器錯誤。

請注意，`JSBridge`類別會負責維護`WeakReference`到`HybridWebViewRenderer`類別。 這是為了避免建立兩個類別之間的循環參考。 如需詳細資訊，請參閱[弱式參考](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx)MSDN 上。

> [!IMPORTANT]
> 在 Android Oreo 上，確定 Android 資訊清單設定**目標 Android 版本**要**自動**。 否則，執行此程式碼會導致錯誤訊息"invokeCSharpAction 未定義 」。

### <a name="creating-the-custom-renderer-on-uwp"></a>建立 UWP 上的自訂轉譯器

下列程式碼範例顯示適用於 UWP 的自訂轉譯器：

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

`HybridWebViewRenderer`類別載入中指定的網頁`HybridWebView.Uri`屬性插入原生`WebView`控制項，而`invokeCSharpAction`JavaScript 函式會插入網頁中，載入網頁，與`WebView.InvokeScriptAsync`方法。 一旦使用者輸入其名稱，然後按一下 HTML`button`項目， `invokeCSharpAction` JavaScript 函式執行時，使用`OnWebViewScriptNotify`之後從網頁上接收到通知時所呼叫的方法。 接著，此方法會叫用`HybridWebView.InvokeAction`方法，將會叫用已註冊的動作，以顯示快顯視窗中。

這項功能為止，如下所示：

- 前提`Control`屬性是`null`，執行下列作業：
  - `SetNativeControl`會呼叫方法來具現化新的原生`WebView`控制項，並指派它的參考`Control`屬性。
- 前提是自訂轉譯器附加至新的 Xamarin.Forms 元素：
  - 事件處理常式`NavigationCompleted`和`ScriptNotify`註冊事件。 `NavigationCompleted`時，會引發事件的原生`WebView`控制項已完成載入目前的內容，或如果無法瀏覽。 `ScriptNotify`時，會引發事件的內容，在原生`WebView`控制項會用來將字串傳遞至應用程式的 JavaScript。 網頁會引發`ScriptNotify`藉由呼叫的事件`window.external.notify`同時傳遞`string`參數。
  - `WebView.Source`屬性設定為所指定的 HTML 檔案的 URI`HybridWebView.Uri`屬性。 此程式碼會假設檔案儲存在`Content`專案的資料夾。 就會顯示網頁，一旦`NavigationCompleted`事件就會引發和`OnWebViewNavigationCompleted`方法會叫用。 `invokeCSharpAction` JavaScript 函式則會插入至網頁`WebView.InvokeScriptAsync`方法，前提是已順利完成的導覽。
- 當項目轉譯器附加至變更：
  - 會取消訂閱事件。

## <a name="summary"></a>總結

這篇文章已示範如何建立自訂轉譯器`HybridWebView`自訂控制項，可示範如何以加強平台專屬的 web 控制項，以允許從 JavaScript 的 C# 程式碼來叫用。


## <a name="related-links"></a>相關連結

- [CustomRendererHybridWebView （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [從 JavaScript 呼叫 C#](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript/)

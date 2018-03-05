---
title: "使用 Razor 範本建置 HTML 檢視"
description: " 使用全螢幕網頁來呈現 HTML，可以是簡單且有效的方式，來呈現複雜的格式，跨平台的方式，特別是如果您已經有 HTML、 Javascript 和 CSS 的網站專案。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 7e4d1cab532a5c81da1dfc47df33aa0628c7f6c6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="building-html-views-using-razor-templates"></a>使用 Razor 範本建置 HTML 檢視

在行動裝置開發世界詞彙 「 混合式應用程式 」 通常指的是呈現部分 （或所有） 其螢幕內託管的 web 檢視器控制項的 HTML 網頁的應用程式。

有某些開發環境，可讓您建置行動應用程式完全以 HTML 和 Javascript，但是這些應用程式時要完成複雜的處理或 UI 效果就會遇到效能問題，是也同時受限於平台他們可以存取的功能。

Xamarin 提供最佳的這兩個環境，尤其是當使用 HTML Razor 範本引擎。 使用 Xamarin 您更有彈性地建置跨平台樣板化 HTML 檢視使用 Javascript 和 CSS，但也可以完整存取基礎平台 Api 和使用 C# 的快速處理。

本文件說明如何使用的 Razor 範本引擎建立可在使用 Xamarin 的行動平台上的 HTML + Javascript + CSS 檢視。

## <a name="using-web-views-programmatically"></a>以程式設計方式使用 Web 檢視

我們了解 Razor 之前這一節會說明如何使用直接 – 顯示 HTML 內容的網頁檢視特別 HTML 應用程式內產生的內容。

Xamarin iOS 和 Android 上提供完整存取基礎平台應用程式開發介面，因此很容易建立顯示使用 C# 的 HTML。 每個平台的基本語法如下所示。

### <a name="ios"></a>iOS

HTML 在控制項中顯示 UIWebView Xamarin.iOS 中也會使用幾行程式碼：

```
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

請參閱[iOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/)如需有關使用 UIWebView 控制項的配方。

### <a name="android"></a>Android

使用 Xamarin.Android 在 WebView 控制項中顯示 HTML 被完成的程式碼只需要幾行：

```
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

請參閱[Android WebView](http://docs.xamarin.com/recipes/android/controls/webview/)如需有關使用 WebView 控制項的配方。

### <a name="specifying-the-base-directory"></a>指定的基底目錄

在兩個平台上沒有指定的 HTML 網頁的基底目錄的參數。 這是用來解析相對參考資源，例如影像和 CSS 檔案的裝置的檔案系統上的位置。 例如，標記喜歡


    <link rel="stylesheet" href="style.css" />
    <img src="monkey.jpg" />
    <script type="text/javascript" src="jscript.js">


這些檔案，請參閱： **style.css**， **monkey.jpg**和**jscript.js**。 基底目錄設定會告知 web 檢視這些檔案位於何處以便可載入頁面。

#### <a name="ios"></a>iOS

在下列 C# 程式碼的 iOS 中呈現的範本輸出：

```
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

基底目錄指定為`NSBundle.MainBundle.BundleUrl`參考在安裝應用程式的目錄。 中的所有檔案**資源**資料夾會複製到這個位置，例如**style.css**檔案如下所示：

 ![iPhoneHybrid 方案](images/image1_240x163.png)

所有的靜態內容檔案的建置動作應該**BundleResource**:

 ![iOS 專案的建置動作： BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android 也需要在 web 檢視中顯示 html 字串時，做為參數傳遞的基底目錄。

```
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

特殊字串**file:///android_asset/**指的是應用程式中，顯示以下包含 Android 的 Assets 資料夾**style.css**檔案。

 ![AndroidHybrid 方案](images/image3_240x167.png)

所有的靜態內容檔案的建置動作應該**AndroidAsset**。

 ![Android 專案的建置動作： AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>從 HTML 和 Javascript 呼叫 C# #

Html 網頁載入 web 檢視時，它會將連結和 form 如同它已從伺服器載入的頁面。 這表示，如果使用者按一下連結或送出表單的網頁檢視會嘗試瀏覽至指定的目標。

如果連結至外部伺服器 （例如 google.com) web 檢視將會嘗試載入該外部網站 （假設沒有網際網路連線）。

```
<a href="http://google.com/">Google</a>
```

如果是相對的連結網頁檢視會從其基底目錄載入該內容。 顯然沒有網路連線是為了要使其運作，內容儲存在裝置上的應用程式中。

```
<a href="somepage.html">Local content</a>
```

表單的動作，請遵循相同的規則。

```
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

您不會去裝載 web 伺服器上的用戶端。不過，您可以使用相同的伺服器通訊技術用在現今的回應式設計模式來呼叫服務透過 HTTP GET 時，並透過發出 Javascript 以非同步方式處理回應 （或呼叫 Javascript 已裝載在 web 檢視中）。 這可讓您輕鬆地將資料從 HTML 傳遞回至 C# 程式碼的處理則會顯示在 HTML 頁面上的結果傳回。

IOS 和 Android 提供應用程式程式碼攔截這些巡覽事件，讓應用程式程式碼可以回應 （如有必要） 的機制。 這項功能很重要建置混合式應用程式，因為它可讓 web 檢視與互動的原生程式碼。

#### <a name="ios"></a>iOS

在 iOS 中的網頁檢視的 ShouldStartLoad 事件會覆寫以允許應用程式程式碼來處理 （例如連結點擊） 巡覽要求。 方法參數提供的所有資訊

```
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

並將指定的事件處理常式：

```
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

在 Android 上只要 WebViewClient 子類別，然後實作程式碼，以回應巡覽要求。

```
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, string url) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

然後設定網頁檢視中的 用戶端：

```
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>從 C# 呼叫 Javascript

除了告訴網頁檢視載入新的 HTML 網頁，C# 程式碼也可以執行 Javascript 目前所顯示的網頁內。 或整個 Javascript 程式碼區塊可以使用 C# # 字串建立和執行，您就可以建立透過頁面已提供 Javascript 的方法呼叫`script`標記。

#### <a name="android"></a>Android

建立 Javascript 程式碼執行，然後將它與前置詞"javascript:"，並指示網頁檢視載入該字串：

```
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

iOS web 檢視提供特別要呼叫 Javascript 的方法：

```
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>總結

本節介紹了 web 檢視控制項，讓建置混合式應用程式使用 Xamarin，我們的 iOS 和 Android 上的功能包括：

-  若要從程式碼，來產生的字串載入 HTML 能力
-  參考 （CSS、 Javascript、 影像或其他 HTML 檔案） 的本機檔案的能力
-  能夠攔截 C# 程式碼中，巡覽要求
-  若要從 C# 程式碼呼叫 Javascript 功能。


下一節介紹 Razor，方便在混合式應用程式中建立要使用 HTML。

## <a name="what-is-razor"></a>Razor 是什麼？

Razor 是 ASP.NET MVC 中，原先以在伺服器上執行，並產生 HTML，以便提供服務到網頁瀏覽器中引入樣板化引擎。

Razor 範本引擎會擴充標準 HTML 語法與 C# #，使您 express 配置並輕鬆地將 CSS 樣式表和 Javascript。 範本可以參考模型類別，它可以是任何自訂類型和其屬性可直接從範本。 其主要的優點之一是能夠輕易地混合 HTML 和 C# # 語法。

Razor 的範本不限於使用伺服器端使用，也可以包含 Xamarin 應用程式中。 使用 Razor templates，以及能夠以程式設計方式處理網頁檢視，可讓複雜的跨平台混合式應用程式使用 Xamarin 建置。

### <a name="razor-template-basics"></a>Razor 範本的基本概念

Razor 的範本檔案有**.cshtml**檔案副檔名。 加入 Xamarin 專案中的文字範本化區段從**新檔案**對話方塊：

 ![新的檔案-Razor 範本](images/image5_400x201.png)

簡單的 Razor 範本 ( **RazorView.cshtml**) 如下所示。

```
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

請注意從一般的 HTML 檔案的下列差異：

-  `@`符號 Razor 範本中有特殊意義-它表示下列運算式是 C# # 進行評估。
- `@model` 指示詞一律會顯示為 Razor 的範本檔案的第一行。
-  `@model`指示詞後面必須接著型別。 在此範例中簡單字串傳遞至範本，但這可能是任何自訂的類別。
-  當`@Model`參考整個範本，它會提供產生 （在此範例中，它會是一個字串） 時，傳遞至範本物件的參考。
-  IDE 會自動產生的範本部分類別 (具有檔案**.cshtml**副檔名)。 您可以檢視這段程式碼，但它不應編輯。
 ![RazorView.cshtml](images/image6_125x34.png)部分類別的名稱為 RazorView 符合.cshtml 範本檔案名稱。 它是用來參考 C# 程式碼中的範本，此名稱。
- `@using` 陳述式也可以包含 Razor 範本加入額外的命名空間的頂端。


然後可使用下列 C# 程式碼產生的最後一個 HTML 輸出。 請注意，我們會指定為字串"Hello World"將會合併到轉譯的範本輸出的模型。

```
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

在 iOS 模擬器和 Android 模擬器上的網頁檢視中顯示的輸出如下：

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>更多的 Razor 語法

在這一節，我們將介紹一些基本的 Razor 語法，可協助您開始使用它。 本節中的範例填入下列類別的資料，並顯示它使用 Razor:

```
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

所有範例都使用下列資料初始化程式碼

```
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>顯示模型屬性

當模型具有屬性的類別，會輕鬆地在參考這些 Razor 範本在這個範例範本中所示：

```
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

這可以轉譯為字串，使用下列程式碼：

```
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

最後的輸出如下所示在 iOS 模擬器和 Android 模擬器上的 web 檢視中：

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>C# # 陳述式

更複雜 C# # 可以包含在範本中，例如模型屬性的更新和年齡計算，在此範例中：

```
@model Monkey
<html>
    <body>
    @{
        Model.Name = "Rupert X. Monkey";
        Model.Birthday = new DateTime(2011,3,1);
    }
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    </body>
</html>
```

您可以撰寫複雜單行的 C# 運算式 （例如格式化年齡） 周圍的程式碼與`@()`。

可以寫入多個 C# # 陳述式圍繞它們與`@{}`。

#### <a name="if-else-statements"></a>If else 陳述式

可以使用表示程式碼分支`@if`這個範本範例所示。

```
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <p>@Model.FavoriteFoods.Count favorites</p>
    }
    </body>
</html>
```

#### <a name="loops"></a>迴圈

迴圈建構，例如`foreach`也可以加入。 `@`迴圈變數上，可以使用前置詞 (`@food`在此情況下) 來呈現 html。

```
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <ul>
            @foreach (var food in @Model.FavoriteFoods) {
                <li>@food</li>
            }
        </ul>
    }
    </body>
</html>
```

上述範本的輸出會顯示在 iOS 模擬器和 Android 模擬器上執行：

 ![Rupert X 猴子](images/image9_520x277.png)

本節涵蓋使用 Razor templates 轉譯簡單的唯讀檢視表的基本概念。 下一節說明如何建置更完整的應用程式使用 Razor 可接受使用者輸入，並在 HTML 檢視和 C# 中的 Javascript 之間相互操作。

## <a name="using-razor-templates-with-xamarin"></a>使用 Xamarin Razor 範本

本節說明如何使用建置自己的混合式應用程式，使用 Visual Studio 中的解決方案範本 for mac。 有可用的三個範本**檔案 > 新增 > 方案...**視窗：

-  Android > 應用程式 > Android WebView 應用程式
-  iOS > 應用程式 > WebView 應用程式
- ASP.NET MVC Project



**新方案**iPhone 和 Android 專案針對視窗看起來像這樣-右邊的方案描述會反白顯示 Razor 範本引擎的支援。

 ![建立 iPhone 和 Android 的解決方案](images/image13_1139x959.png)

請注意，您可以輕鬆加入**.cshtml** Razor 範本*任何*現有 Xamarin 專案，則不需要使用這些解決方案範本。 iOS 的專案不需要使用 Razor; 分鏡腳本只需將 UIWebView 控制項加入任何檢視中以程式設計方式，您可以轉譯 Razor templates 整個 C# 程式碼中。

IPhone 和 Android 專案的預設範本方案內容如下所示：

 ![iPhone 和 Android 的範本](images/image10_428x310.png)

範本可讓您已備妥要移至應用程式基礎結構，來載入資料模型物件的 Razor 範本、 處理使用者輸入並回到使用者可透過 Javascript 通訊。

方案的重要部分是：

-  靜態內容，例如**style.css**檔案。
-  Razor.cshtml 範本檔案喜歡**RazorView.cshtml** 。
-  這類參考 Razor 範本中的類別建立模型**ExampleModel.cs** 。
-  平台特定類別，以建立網頁檢視，並呈現此範本中，例如`MainActivity`在 Android 上和`iPhoneHybridViewController`在 iOS 上。


本節說明如何建立專案的運作方式。

### <a name="static-content"></a>靜態內容

靜態內容包含 CSS 樣式表、 影像、 Javascript 檔案或可以從連結或 web 檢視中顯示的 HTML 檔所參考的其他內容。

範本專案包含示範如何在混合式應用程式中包含靜態內容的最小的樣式表。 CSS 樣式表會參考在範本中，像這樣：

```
<link rel="stylesheet" href="style.css" />
```

您可以新增任何樣式表，而且您也必須包括架構，例如 JQuery Javascript 檔案。

### <a name="razor-cshtml-templates"></a>Razor cshtml 範本

範本包含 Razor **.cshtml**預先撰寫程式碼，以協助進行通訊的 HTML/Javascript 和 C# 之間的資料檔案。 這可讓您建置複雜的混合式應用程式，不只顯示唯讀資料模型，但也接受 HTML 中的使用者輸入並將它傳遞回 C# 程式碼的處理或儲存體。

#### <a name="rendering-the-template"></a>轉譯範本

呼叫`GenerateString`範本呈現可立即在 web 檢視中顯示的 HTML。 如果範本使用模型，則它應該在呈現前提供。 此圖說明呈現的運作方式 – 不所解決的靜態資源的網頁檢視，在執行階段，使用提供的基底目錄以尋找指定的檔案。

 ![Razor 的流程圖](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>從範本中呼叫 C# 程式碼

從轉譯的 web 檢視回呼叫到 C# # 的通訊方式是設定 web 檢視中，URL，然後攔截處理原生要求，而不重新載入網頁檢視的要求中 C# #。

中如何處理 RazorView 的按鈕，就可以看到的範例。 按鈕的下列 HTML:

```
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

`InvokeCSharpWithFormValues` Javascript 函式會讀取來自 HTML 表單和集合的所有值`location.href`web 檢視：

```
location.href = "hybrid:" + elm.name + "?" + qs;
```

這會嘗試瀏覽至我們組成的自訂配置的 URL web 檢視 (`hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

當原生 web 檢視處理這項功能要求時，我們有機會攔截它。 在 iOS 中，這是藉由處理 UIWebView HandleShouldStartLoad 事件。 在 Android 中，我們只要 WebViewClient 用於表單，並覆寫 ShouldOverrideUrlLoading 的子類別。

這些兩個導覽的攔截器的內部資訊基本上都相同。

首先，我們請檢查 URL，web 檢視嘗試載入，而且如果沒有自動啟動與我們的自訂結構描述 (`hybrid:`)，我們允許以正常進行導覽。

針對我們自訂的 URL 配置，我們將所有項目在配置之間的 URL 和 「？ 」 為處理 （在此情況下，「 UpdateLabel"） 的方法名稱。 查詢字串中的所有項目將會被視為在方法呼叫的參數：

```
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

UpdateLabel 在此範例中並未少量的字串操作上的文字方塊中的參數 (前面加上"C# # 指出 ' 」 的字串)，然後再呼叫回 web 檢視。

在我們 URL 處理結束時，我們會中止巡覽，使 web 檢視不會嘗試完成瀏覽至我們的自訂 URL。

#### <a name="manipulating-the-template-from-c"></a>處理從 C# 範本

從 C# # 呈現的 HTML 網頁檢視的通訊方式是在 web 檢視中呼叫 Javascript。 在 iOS 上，這是藉由呼叫`EvaluateJavascript`UIWebView 上：

```
webView.EvaluateJavascript (js);
```

在 Android 上，Javascript 可以叫用在網頁檢視載入做為 URL，使用 Javascript `"javascript:"` URL 配置：

```
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>讓應用程式真正的混合式

這些範本並讓每個平台上的原生控制項使用 – 單一 web 檢視填滿整個螢幕。

HTML 可以就很適合建立原型，並顯示的各種網路是最適合例如 rtf 文字和回應的版面配置。 例如，HTML 和 Javascript – 捲動的資料，較長的清單適合不過並非所有的工作執行最好在 Android 上使用原生 UI 控制項，例如 (在 iOS 上的 UITableView) 或 ListView。

範本中的 web 檢視可以輕鬆地與平台專屬控制項 – 只要編輯增強**MainStoryboard.storyboard** iOS 設計工具中或**Resources/layout/Main.axml**在 Android 上。

### <a name="razortodo-sample"></a>RazorTodo 範例

[RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)儲存機制 含有兩個不同的解決方案，以顯示完整的 HTML 驅動的應用程式與 HTML 結合原生控制項的應用程式之間的差異：

-  **RazorTodo** -完全 HTML 驅動的應用程式使用 Razor 的範本。
-  **RazorNativeTodo** ： 使用適用於 iOS 和 Android 的原生的清單檢視控制項，但會顯示與 HTML 和 Razor 的 [編輯] 螢幕。


在 iOS 和 Android 使用可攜式類別庫 (PCLs) 共用通用程式碼，例如資料庫和模型的類別上，執行這些 Xamarin 應用程式。 Razor **.cshtml**範本也可以包含在 PCL 中以便輕鬆地在跨平台共用。

兩個範例應用程式併入 Twitter 共用與文字轉換語音 Api 的原生的平台，示範，使用 Xamarin 混合式應用程式還是可以存取所有的基礎功能從 HTML Razor 範本驅動的檢視。

**RazorTodo**應用程式的清單，並編輯檢視中使用 HTML Razor 的範本。 這表示我們可以建置應用程式幾乎完全共用可攜式類別庫 (包括資料庫和**.cshtml** Razor 範本)。 以下螢幕擷取畫面顯示 iOS 和 Android 應用程式。

 ![RazorTodo](images/Both_700x290.png)

**RazorNativeTodo**應用程式的編輯檢視中，會使用 HTML Razor 範本，但實作每個平台上的原生捲動清單。 這會提供一些優點包括：

-  效能-原生捲動控制項使用虛擬化，確保快速的平滑捲動，即使有很長資料的清單。
-  原生體驗-平台特定 UI 項目會輕鬆地啟用，例如快速捲動索引中的支援 iOS 和 Android。


建立使用 Xamarin 混合式應用程式的主要優點是您可以用來啟動具有完整的 HTML 驅動的使用者介面 （就像第一個範例中），然後再新增特定平台 （如第二個範例所示） 所需的功能。 原生清單畫面和 HTML Razor 編輯螢幕上兩個 iOS 和 Android 如下所示。

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>總結

本文說明可用的 web 檢視控制項的功能在 iOS 和 Android 的建置混合式應用程式。

然後將討論 Razor 範本引擎，以及可用來輕鬆地產生 HTML，使用的 Xamarin 應用程式中的語法。**cshtml** Razor 範本檔案。 它也會說明 Visual Studio for Mac 解決方案範本可讓您快速開始建置 xamarin 混合式應用程式。

最後，它會引進 RazorTodo 範例可示範如何結合 web 檢視與原生使用者介面和應用程式開發介面。

### <a name="related-links"></a>相關連結

- [RazorTodo 範例](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3-Razor 檢視引擎 (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [使用 Razor 語法 (Microsoft) 的 ASP.NET Web 程式設計簡介](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)
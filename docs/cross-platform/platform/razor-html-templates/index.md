---
title: 使用 Razor 範本建置 HTML 檢視
description: " 使用全螢幕網頁來呈現 HTML，可以是簡單且有效的方式來呈現複雜的格式，跨平台的方式，尤其是如果您已經有 HTML、 Javascript 和 CSS 從網站專案。"
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: asb3993
ms.author: amburns
ms.date: 07/24/2018
ms.openlocfilehash: 7e569aaddef912d9534e98f2f987ad5dfca8a5a6
ms.sourcegitcommit: 46bb04016d3c35d91ff434b38474e0cb8197961b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39270128"
---
# <a name="building-html-views-using-razor-templates"></a>使用 Razor 範本建置 HTML 檢視

在行動裝置開發世界中詞彙 「 混合式應用程式 」 通常是指託管的 web 檢視器控制項內的 HTML 網頁呈現部分 （或所有） 其畫面的應用程式。

有某些開發環境，可讓您建置行動應用程式完全以 HTML 和 Javascript，但這些應用程式可能會受到影響的效能問題時嘗試完成複雜的處理或 UI 效果，而且是也限制在平台他們可以存取的功能。

Xamarin 會提供最佳的兩者的優點，尤其是當使用 Razor HTML 範本化引擎。 使用 Xamarin，您會有彈性地建置跨平台樣板化 HTML 檢視，使用 Javascript 和 CSS，但也有完整存取基礎平台 Api 和使用 C# 的快速處理。

本文件說明如何使用的 Razor 範本化引擎建置可用於使用 Xamarin 的行動平台的 HTML + Javascript + CSS 檢視。

## <a name="using-web-views-programmatically"></a>以程式設計方式使用 Web 檢視

我們了解 Razor 之前這一節會說明如何使用 web 檢視，以顯示 HTML 內容直接 – 特別是 HTML 應用程式內產生的內容。

Xamarin iOS 和 Android 上提供完整存取基礎平台 Api，因此很容易建立和顯示使用 C# 的 HTML。 每個平台的基本語法如下所示。

### <a name="ios"></a>iOS

在 Xamarin.iOS 中 UIWebView 控制項中顯示 HTML 時，也會採用在短短幾行程式碼：

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

請參閱[iOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/)更多有關使用 UIWebView 控制項的訣竅。

### <a name="android"></a>Android

使用 Xamarin.Android 的 WebView 控制項內顯示 HTML 可在短短幾行程式碼來完成：

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable Javascript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

請參閱[Android WebView](http://docs.xamarin.com/recipes/android/controls/webview/)更多有關使用 WebView 控制項的訣竅。

### <a name="specifying-the-base-directory"></a>指定的基底目錄

在這兩個平台上沒有指定的 HTML 網頁的基底目錄的參數。 這是用來解析相對參考資源，例如影像和 CSS 檔案的裝置的檔案系統上的位置。 例如，想標記

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

這些檔案，請參閱： **style.css**， **monkey.jpg**並**jscript.js**。 基底目錄設定會告訴 web 檢視這些檔案的所在位置以便可載入的頁面。

#### <a name="ios"></a>iOS

範本輸出呈現在 iOS 中使用下列 C# 程式碼：

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

基底目錄指定為`NSBundle.MainBundle.BundleUrl`就是指在安裝應用程式的目錄。 中的所有檔案**資源**這類資料夾複製到這個位置**style.css**檔案如下所示：

 ![iPhoneHybrid 解決方案](images/image1_240x163.png)

所有的靜態內容檔案的建置動作應該**BundleResource**:

 ![iOS 專案的建置動作： BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android 也需要在 web 檢視中顯示 html 字串時，做為參數傳遞的基底目錄。

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

特殊字串**file:///android_asset/** 係指在您應用程式中顯示以下包含 Android 資產資料夾**style.css**檔案。

 ![AndroidHybrid 解決方案](images/image3_240x167.png)

所有的靜態內容檔案的建置動作應該**AndroidAsset**。

 ![Android 專案的建置動作： AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>從 HTML 和 Javascript 呼叫 C#

Html 網頁載入網頁檢視時，它會將連結和表單如果從伺服器載入網頁的一樣。 這表示，如果使用者按一下連結或送出表單 web 檢視會嘗試瀏覽至指定的目標。

如果連結至外部伺服器 （例如 google.com) 網頁檢視將會嘗試載入外部網站 （假設沒有網際網路連線）。

```html
<a href="http://google.com/">Google</a>
```

如果是相對的連結 web 檢視將會嘗試從其基底目錄載入該內容。 顯然沒有網路連線是為了此做法能夠運作，因為內容會儲存在裝置上的應用程式。

```html
<a href="somepage.html">Local content</a>
```

表單動作會遵循相同的規則。

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

您不打算裝載 web 伺服器上的用戶端。不過，您可以使用相同的伺服器通訊技術運用在現今的回應式設計模式透過 HTTP GET 時，呼叫服務，並發出 Javascript 以非同步方式處理回應 （或呼叫的 Javascript 已裝載在 web 檢視中）。 這可讓您輕鬆地將資料從 HTML 傳遞回處理之後顯示結果傳回 HTML 網頁的 C# 程式碼。

IOS 和 Android 提供機制來攔截這些巡覽事件，讓應用程式的程式碼可以回應 （如有必要） 的應用程式程式碼。 這項功能，請務必要建置混合式應用程式，因為它可讓 web 檢視進行互動的原生程式碼。

#### <a name="ios"></a>iOS

在 iOS 中的 [web] 檢視中的 ShouldStartLoad 事件可以覆寫，以允許應用程式程式碼來處理巡覽要求 （例如，連結按一下）。 方法參數提供的所有資訊

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

並將指定的事件處理常式：

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

在 Android 上只要子類別來處理，然後實作程式碼來回應巡覽要求。

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

然後設定 web 檢視中的 用戶端：

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>從 C# 中呼叫 Javascript

除了告訴 web 檢視，以載入新的 HTML 網頁，C# 程式碼可以也在目前顯示的頁面中執行 Javascript。 可以使用 C# 字串建立整個 Javascript 程式碼區塊，並執行，或您可以製作已經透過頁面上提供的 Javascript 的方法呼叫`script`標記。

#### <a name="android"></a>Android

建立 Javascript 程式碼來執行，然後將它與前置詞"javascript: 」，並指示網頁檢視載入該字串：

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

iOS web 檢視會提供特別要呼叫 Javascript 的方法：

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>總結

這一節介紹 web 檢視控制項，讓我們建置使用 Xamarin，混合式應用程式的 iOS 和 Android 上的功能包括：

-  從程式碼中，產生的字串載入 HTML 的能力
-  參考本機檔案 （CSS、 Javascript、 影像或其他 HTML 檔案） 的能力
-  能夠攔截 C# 程式碼、 巡覽要求
-  若要從 C# 程式碼呼叫 Javascript 功能。


下一節介紹 Razor，方便在混合式應用程式中建立要使用的 HTML。

## <a name="what-is-razor"></a>Razor 是什麼？

Razor 是引進了使用 ASP.NET MVC，原本要在伺服器上執行，並產生 HTML，以提供給 web 瀏覽器的樣板化引擎。

Razor 範本化引擎延伸使用 C# 的標準 HTML 語法，讓您可以表示的配置，並輕鬆地將 CSS 樣式表和 Javascript。 範本可以參考的模型類別，它可以是任何自訂類型和其屬性可直接從範本。 其主要優點之一是能夠輕鬆地混用 HTML 與 C# 語法。

Razor 範本並不限於伺服器端使用，也可以包含 Xamarin 應用程式中。 使用 Razor 範本，以及能夠以程式設計方式處理 web 檢視可讓使用 Xamarin 來建置複雜的跨平台混合式應用程式。

### <a name="razor-template-basics"></a>Razor 範本的基本概念

Razor 範本檔案已 **.cshtml**副檔名。 加入 Xamarin 專案中的文字範本化一節**新的檔案**對話方塊：

 ![新的檔案-Razor 範本](images/image5_400x201.png)

簡單的 Razor 範本 ( **RazorView.cshtml**) 如下所示。

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

請注意從一般的 HTML 檔案的下列差異：

-  `@`符號 Razor 範本中具有特殊意義-它表示下列運算式是 C# 進行評估。
- `@model` 指示詞一律會顯示為 Razor 範本檔案的第一行。
-  `@model`指示詞後面應該接著型別。 在此範例中簡單的字串傳遞至範本，但這可能是任何自訂的類別。
-  當`@Model`參考在範本中，它提供產生 （在此範例中，它會是一個字串） 時，傳遞至範本物件的參考。
-  IDE 會自動產生的範本的部分類別 (檔案與 **.cshtml**延伸模組)。 您可以檢視這段程式碼，但它不可編輯。
 ![RazorView.cshtml](images/image6_125x34.png)部分類別名為 RazorView 符合.cshtml 範本檔案名稱。 它是用來參考 C# 程式碼中的範本，此名稱。
- `@using` 陳述式也可以包含在 Razor 範本以包含其他命名空間的頂端。


下列 C# 程式碼時，可以再產生最終的 HTML 輸出。 請注意，我們會指定為字串"Hello World"將會合併到轉譯的範本輸出的模型。

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

以下是在 iOS 模擬器、 Android 模擬器上的 web 檢視中顯示的輸出：

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>更多的 Razor 語法

在本節中我們將介紹一些基本的 Razor 語法，可協助您開始使用它。 在本節中的範例填入下列資料類別，並顯示使用 Razor:

```csharp
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

所有範例都使用下列的資料初始化程式碼

```csharp
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>顯示模型屬性

當模型具有屬性的類別，輕鬆地參考這些 Razor 範本中，在此範例範本中所示：

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

這可以轉譯成字串，使用下列程式碼：

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

在 iOS 模擬器、 Android 模擬器上的 web 檢視如下所示的最終輸出：

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>C# 陳述式

更複雜的 C# 可以包含在範本中，例如模型屬性的更新和年齡計算，在此範例中：

```html
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

您可以撰寫複雜單行的 C# 運算式 （例如格式化年齡） 周圍的程式碼`@()`。

可以寫入多個 C# 陳述式以它們周圍`@{}`。

#### <a name="if-else-statements"></a>If else 陳述式

可以使用表示程式碼分支`@if`此範本範例所示。

```html
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

迴圈建構，例如`foreach`也可以加入。 `@`迴圈變數上，可以使用前置詞 (`@food`在此情況下) 來呈現 HTML 中。

```html
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

上述範本的輸出會顯示在 iOS 模擬器、 Android 模擬器上執行：

 ![Rupert X Monkey](images/image9_520x277.png)

本節涵蓋使用 Razor 範本來呈現簡單的唯讀檢視之基本概念。 下節將說明如何建置更完整的應用程式使用 Razor 可以接受使用者輸入，並在 HTML 檢視和 C# 中的 Javascript 之間相互操作。

## <a name="using-razor-templates-with-xamarin"></a>搭配 Xamarin 使用 Razor 範本

本節說明如何使用建置自己的解決方案範本在 Visual Studio for mac 中使用的混合式應用程式 有三個範本可從**檔案 > 新增 > 方案...** 視窗：

- **Android > 應用程式 > Android WebView 應用程式**
- **iOS > 應用程式 > WebView 應用程式**
- **ASP.NET MVC 專案**



**新的方案**iPhone 和 Android 專案視窗看起來像這樣-右側的方案描述反白顯示的 Razor 範本化引擎的支援。

 ![建立 iPhone 和 Android 的方案](images/image13_1139x959.png)

請注意，您可以輕鬆地新增 **.cshtml** Razor 範本*任何*現有的 Xamarin 專案，它不需要使用這些解決方案範本。 iOS 專案不需要是; 使用 Razor 分鏡腳本只要將 UIWebView 控制項加入任何檢視中以程式設計方式，您可以轉譯 Razor 範本整個 C# 程式碼。

IPhone 和 Android 專案的預設範本方案內容如下所示：

 ![iPhone 和 Android 的範本](images/image10_428x310.png)

範本可讓您準備就緒應用程式基礎結構，以載入資料模型物件的 Razor 範本、 處理使用者輸入和傳回給使用者，透過 Javascript 通訊。

此解決方案的重要部分是：

-  這類靜態內容**style.css**檔案。
-  Razor.cshtml 範本檔案，例如**RazorView.cshtml** 。
-  模型中的 Razor 範本這類參考的類別**ExampleModel.cs** 。
-  建立 web 檢視，並呈現此範本中，這類的平台特定類別`MainActivity`在 Android 上和`iPhoneHybridViewController`在 iOS 上。


下一節說明專案的運作方式。

### <a name="static-content"></a>靜態內容

靜態內容包括 CSS 樣式表、 影像、 Javascript 檔案或其他內容，可以從連結或網頁檢視中顯示 HTML 檔案參考。

範本專案包含示範如何在混合式應用程式中包含靜態內容的最小的樣式表。 CSS 樣式表這類範本中參考：

```html
<link rel="stylesheet" href="style.css" />
```

您可以新增任何樣式表和 Javascript 檔案，您也需要包括 JQuery 等架構。

### <a name="razor-cshtml-templates"></a>Razor cshtml 範本

此範本包括 Razor **.cshtml**有預先撰寫的程式碼來協助傳達 HTML/Javascript 和 C# 之間的資料的檔案。 這可讓您建置複雜的混合式應用程式，不只是顯示唯讀資料模型，但也接受 HTML 中的使用者輸入並將它傳遞回處理或儲存體的 C# 程式碼。

#### <a name="rendering-the-template"></a>轉譯範本

呼叫`GenerateString`範本呈現可立即在 web 檢視中顯示的 HTML。 如果範本會使用模型，則它應該提供再進行轉譯。 此圖說明轉譯的運作方式 – 不所解決的靜態資源的 web 檢視，在執行階段，使用提供的基底目錄以尋找指定的檔案。

 ![Razor 的流程圖](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>從範本中呼叫 C# 程式碼

藉由設定 web 檢視的 URL，然後攔截 C# 中的要求，而不重新載入網頁檢視處理原生的要求是回呼至 C# 呈現的網頁檢視的通訊。

中如何處理 RazorView 的按鈕，就可以查看範例。 按鈕具有下列 HTML:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

`InvokeCSharpWithFormValues` Javascript 函式會讀取所有的值來自 HTML 表單和集合`location.href`web 檢視：

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

這會嘗試瀏覽至 URL，以使用自訂配置 （例如 web 檢視 `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

當原生 web 檢視會處理此巡覽要求時，我們有機會攔截它。 在 iOS 中，這是由處理 UIWebView HandleShouldStartLoad 事件。 在 Android 中，我們只是子類別來處理在表單中，使用，並覆寫 ShouldOverrideUrlLoading。

這些兩個瀏覽攔截器的內部資訊基本上都相同。

首先，請檢查 URL，web 檢視嘗試載入，而且如果它不會開始使用自訂配置 (`hybrid:`)，允許瀏覽發生如往常。

適用於自訂的 URL 配置，在配置之間的 URL 中的所有項目和 「？ 」 是 （在此情況下，「 UpdateLabel"） 中處理的方法名稱。 查詢字串中的所有項目將會被視為方法呼叫的參數：

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` 在此範例中的最少的字串操作 （前面加上"C# says"的字串），在文字方塊中參數，並回呼至 web 檢視。

之後處理的 URL，方法會中止瀏覽，以便 web 檢視不會嘗試完成瀏覽至自訂 URL。

#### <a name="manipulating-the-template-from-c"></a>管理從 C# 範本

從 C# 呈現的 HTML 網頁檢視的通訊是由 web 檢視中呼叫 Javascript。 在 iOS 上，這是藉由呼叫`EvaluateJavascript`UIWebView 上：

```csharp
webView.EvaluateJavascript (js);
```

在 Android 上，Javascript 可以叫用網頁檢視中載入 URL，使用 Javascript `"javascript:"` URL 配置：

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>讓應用程式真正的混合式

這些範本就不會讓使用的每個平台上的原生控制項 – 單一 web 檢視填滿整個螢幕。

HTML 可以是適合用來建立原型，而且顯示的各種網路是最適合例如 rtf 文字和回應式配置。 例如，HTML 和 Javascript – 捲動的資料，較長的清單適合不過並非所有的工作執行最好在 Android 上使用原生 UI 控制項，例如 (UITableView 在 iOS 上的) 或 ListView。

在範本中的 web 檢視可以輕易地與平台專屬控制項 – 只要編輯增強**MainStoryboard.storyboard** iOS 設計工具中的**Resources/layout/Main.axml**在 Android 上。

### <a name="razortodo-sample"></a>RazorTodo 範例

[RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)存放庫包含兩個不同的解決方案，以顯示完整的 HTML 驅動的應用程式與 HTML 結合原生控制項的應用程式之間的差異：

-  **RazorTodo** -使用 Razor 範本的完整 HTML 驅動應用程式。
-  **RazorNativeTodo** -使用適用於 iOS 和 Android 的原生的清單檢視控制項，但會顯示使用 HTML 和 Razor 編輯畫面。


在 iOS 和 Android，使用可攜式類別庫 (Pcl) 共用通用的程式碼，例如資料庫和模型類別上，執行這些 Xamarin 應用程式。 Razor **.cshtml**範本也可以包含在 PCL 中以便輕鬆地在跨平台共用。

這兩個範例應用程式納入 Twitter 共用和示範，使用 Xamarin 的混合式應用程式還是可以存取所有的基礎功能從 HTML Razor 範本驅動檢視原生平台，從文字轉換語音 Api。

**RazorTodo**應用程式中使用清單] 和 [編輯檢視 HTML Razor 範本。 這表示我們可以在建置應用程式幾乎完全共用的可攜式類別庫 (包括資料庫和 **.cshtml** Razor 範本)。 以下螢幕擷取畫面會顯示 iOS 和 Android 應用程式。

 ![RazorTodo](images/Both_700x290.png)

**RazorNativeTodo**應用程式的 [編輯] 檢視中，會使用 HTML Razor 範本，但每個平台上實作原生的捲動清單。 這會提供一些優點包括：

-  效能-原生的捲動控制項使用虛擬化來確保快速、 平滑捲動即使有很長資料的清單。
-  原生體驗-平台特定 UI 項目會輕鬆地啟用，例如快速捲動索引中的支援 iOS 和 Android。


建置使用 Xamarin 的混合式應用程式的主要優點是您可以用來啟動具有完全的 HTML 驅動的使用者介面 （例如第一個範例中），然後再新增時 （如第二個範例所示） 所需的平台專屬功能。 原生清單畫面和 HTML Razor 編輯畫面，這兩個 ios 和 Android 如下所示。

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>總結

本文說明可用的 web 檢視控制項的功能在 iOS 和 Android，方便建置混合式應用程式。

然後會討論 Razor 範本化引擎，可用來輕鬆地產生 HTML，Xamarin 應用程式使用中的語法。**cshtml** Razor 範本檔案。 它也會說明 Visual Studio for Mac 解決方案範本可讓您快速開始建置使用 Xamarin 的混合式應用程式。

最後，它會引進 RazorTodo 範例示範如何使用原生使用者介面與 Api 結合 web 檢視。

### <a name="related-links"></a>相關連結

- [RazorTodo 範例](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3-Razor 檢視引擎 (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [使用 Razor 語法 (Microsoft) 的 ASP.NET Web 程式設計簡介](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)

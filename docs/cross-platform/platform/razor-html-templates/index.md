---
title: 使用 Razor 範本建立 HTML 視圖
description: " 使用全螢幕網頁來轉譯 HTML, 是以跨平臺方式轉譯複雜格式的簡單且有效的方式, 特別是當您已經有網站專案的 HTML、JavaScript 和 CSS 時。"
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: asb3993
ms.author: amburns
ms.date: 07/24/2018
ms.openlocfilehash: c6c279a31b6081f7e20b4047e2a8e82c79aeac23
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521812"
---
# <a name="building-html-views-using-razor-templates"></a>使用 Razor 範本建立 HTML 視圖

在行動裝置開發環境中, 「混合式應用程式」一詞通常是指在主控的 web 檢視器控制項內, 將其畫面呈現為 HTML 網頁的應用程式。

有一些開發環境可讓您完全以 HTML 和 JavaScript 建立您的行動應用程式, 不過當嘗試完成複雜的處理或 UI 效果時, 這些應用程式可能會受到效能問題的影響, 而且也會在平臺中受到限制。他們可以存取的功能。

Xamarin 提供兩種世界的最佳選擇, 特別是在使用 Razor HTML 範本化引擎時。 有了 Xamarin, 您可以彈性地建立使用 JavaScript 和 CSS 的跨平臺樣板化 HTML 視圖, 但也可以完整存取基礎平臺 Api, 並使用C#快速處理。

本檔說明如何使用 Razor 範本化引擎來建立可跨行動平臺使用 Xamarin 的 HTML + JavaScript + CSS 視圖。

## <a name="using-web-views-programmatically"></a>以程式設計方式使用 Web Views

在瞭解 Razor 之前, 本節涵蓋如何使用 web views 直接顯示 HTML 內容, 特別是在應用程式內產生的 HTML 內容。

Xamarin 提供 iOS 和 Android 上基礎平臺 Api 的完整存取權, 讓您可以輕鬆地使用C#建立和顯示 HTML。 每個平臺的基本語法如下所示。

### <a name="ios"></a>iOS

在 Xamarin 的 UIWebView 控制項中顯示 HTML。 iOS 也只需要幾行程式碼:

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

如需使用 UIWebView 控制項的詳細資訊, 請參閱[IOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/)配方。

### <a name="android"></a>Android

只需幾行程式碼, 即可在使用 Xamarin 的 Web 視圖控制項中顯示 HTML:

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable JavaScript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

如需使用 Web 視圖控制項的詳細資訊, 請參閱[Android web](http://docs.xamarin.com/recipes/android/controls/webview/)程式。

### <a name="specifying-the-base-directory"></a>指定基底目錄

在這兩個平臺上, 有一個參數可指定 HTML 網頁的基底目錄。 這是裝置的檔案系統上用來解析資源 (例如影像和 CSS 檔案) 之相對參考的位置。 例如, 標記如下:

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

請參閱下列檔案: **style .css**、**猴子 .jpg**和**jscript**。 [基底目錄] 設定會告訴 web view 該檔案的所在位置, 以便將它們載入至頁面。

#### <a name="ios"></a>iOS

範本輸出會在 iOS 中以下列C#程式碼呈現:

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

指定基底目錄, `NSBundle.MainBundle.BundleUrl`其參照的是應用程式安裝所在的目錄。 **Resources**資料夾中的所有檔案都會複製到這個位置, 例如**style .css**檔案, 如下所示:

 ![iPhoneHybrid 解決方案](images/image1_240x163.png)

所有靜態內容檔案的組建動作都應該是**BundleResource**:

 ![iOS 專案組建動作:BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

在 web 視圖中顯示 html 字串時, Android 也需要以參數的形式傳遞基底目錄。

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

特殊字元串**file:///android_asset/** 指的是應用程式中的 android 資產資料夾, 如下所示, 其中包含**style .css**檔案。

 ![AndroidHybrid 解決方案](images/image3_240x167.png)

所有靜態內容檔案的組建動作都應該是**AndroidAsset**。

 ![Android 專案組建動作:AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>從C# HTML 和 JavaScript 呼叫

將 html 網頁載入 web 視圖時, 它會將連結和表單視為頁面是從伺服器載入的。 這表示, 如果使用者按一下連結或提交表單, web view 會嘗試流覽至指定的目標。

如果連結到外部伺服器 (例如 google.com), 則 web 視圖會嘗試載入外部網站 (假設有網際網路連線)。

```html
<a href="http://google.com/">Google</a>
```

如果連結是相對的, 則 web 視圖會嘗試從基底目錄載入該內容。 很明顯地, 此作業不需要網路連線, 因為內容會儲存在裝置上的應用程式中。

```html
<a href="somepage.html">Local content</a>
```

表單動作會遵循相同的規則。

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

您不會在用戶端上裝載網頁伺服器;不過, 您可以使用現今回應式設計模式中所採用的相同伺服器通訊技術, 透過 HTTP GET 呼叫服務, 併發出 JavaScript 以非同步方式處理回應 (或呼叫已經裝載于 web view 中的 JavaScript)。 這可讓您輕鬆地將 HTML 中的資料傳遞C#回程序代碼進行處理, 然後將結果顯示在 html 網頁上。

IOS 和 Android 都提供應用程式代碼的機制來攔截這些導覽事件, 讓應用程式程式碼可以回應 (如有需要)。 這項功能對於建立混合式應用程式很重要, 因為它可讓機器碼與 web view 互動。

#### <a name="ios"></a>iOS

您可以覆寫 iOS 中 web view 上的 ShouldStartLoad 事件, 以允許應用程式程式碼處理導覽要求 (例如連結按一下)。 方法參數提供所有資訊

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

然後指派事件處理常式:

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

在 Android 上, 只要將來處理子類別化, 然後再執行程式碼來回應導覽要求即可。

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

然後在 web view 上設定用戶端:

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>從呼叫 JavaScriptC#

除了告訴 web view 載入新的 HTML 網頁, C#程式碼也可以在目前顯示的頁面內執行 JavaScript。 您可以使用C#字串來建立整個 javascript 程式碼區塊, 並加以執行, 或者您可以透過標籤, 為頁面`script`上已提供的 javascript 製作方法呼叫。

#### <a name="android"></a>Android

建立要執行的 JavaScript 程式碼, 然後在其前面加上 "JavaScript:", 並指示 web view 載入該字串:

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

iOS web views 提供專門用來呼叫 JavaScript 的方法:

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>總結

本節已引進 Android 和 iOS 上的 web view 控制項功能, 可讓我們使用 Xamarin 建立混合式應用程式, 包括:

- 能夠從程式碼中產生的字串載入 HTML。
- 參考本機檔案 (CSS、JavaScript、影像或其他 HTML 檔案) 的能力,
- 能夠攔截程式碼中C#的導覽要求,
- 從C#程式碼呼叫 JavaScript 的能力。


下一節介紹 Razor, 這可讓您輕鬆地建立要在混合式應用程式中使用的 HTML。

## <a name="what-is-razor"></a>Razor 是什麼？

Razor 是 ASP.NET MVC 引進的範本化引擎, 最初是在伺服器上執行, 並產生 HTML 以提供給網頁瀏覽器。

Razor 樣板化引擎會使用C#擴充標準 HTML 語法, 讓您可以快速表達版面配置, 並輕鬆地結合 CSS 樣式表單和 JavaScript。 範本可以參考模型類別, 它可以是任何自訂類型, 而且可以直接從範本存取其屬性。 其中一個主要優點是能夠輕鬆地混合 HTML 和C#語法。

Razor 範本不限於伺服器端使用, 它們也可以包含在 Xamarin 應用程式中。 使用 Razor 範本以及透過程式設計的方式來處理 web views, 讓複雜的跨平臺混合式應用程式能夠以 Xamarin 建立。

### <a name="razor-template-basics"></a>Razor 範本基本概念

Razor 範本檔案的副檔名為 **.** #。 您可以從 [**新增**檔案] 對話方塊中的 [文字模板化] 區段, 將它們加入 Xamarin 專案:

 ![新增檔案-Razor 範本](images/image5_400x201.png)

簡單的 Razor 範本 ( **RazorView. cshtml**) 如下所示。

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

請注意下列與一般 HTML 檔案的差異:

- 符號在 Razor 範本中具有特殊意義, 這表示C#要評估下列運算式。 `@`
- `@model`指示詞一律會顯示為 Razor 範本檔案的第一行。
- `@model`指示詞後面應該接著型別。 在此範例中, 會將簡單字串傳遞至範本, 但這可能是任何自訂類別。
- 在`@Model`整個範本中參考時, 它會在產生時提供傳遞至範本之物件的參考 (在此範例中, 它會是字串)。
- IDE 會自動產生範本的部分類別 (副檔名為**cshtml**的檔案)。 您可以查看此程式碼, 但不能進行編輯。
 ![RazorView 部分類別的名稱為 RazorView, 以符合. cshtml 範本檔案名。](images/image6_125x34.png) 這是用來在程式碼中C#參考範本的名稱。
- `@using`語句也可以包含在 Razor 範本的頂端, 以包含其他命名空間。


接著, 您可以使用下列C#程式碼來產生最終的 HTML 輸出。 請注意, 我們將模型指定為字串 "Hello World", 這會併入轉譯的範本輸出中。

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

以下是 iOS 模擬器上 web 視圖中顯示的輸出, 並 Android Emulator:

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>更多 Razor 語法

在本節中, 我們將介紹一些基本 Razor 語法, 以協助您開始使用它。 本節中的範例會將資料填入下列類別, 並使用 Razor 加以顯示:

```csharp
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

所有範例都會使用下列資料初始化程式碼

```csharp
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>顯示模型屬性

當模型是具有屬性的類別時, 會在 Razor 範本中輕鬆地參考它們, 如下列範例範本所示:

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

這可以使用下列程式碼轉譯為字串:

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

最後的輸出會顯示在 iOS 模擬器的 web 視圖中, 並 Android Emulator:

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>C#報表

範本中C#可以包含更複雜的內容, 例如模型屬性更新和此範例中的年齡計算:

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

您可以使用`@()`括住程式碼C# , 來撰寫複雜的單行運算式 (例如格式化年齡)。

您C#可以使用`@{}`來括住多個語句來撰寫。

#### <a name="if-else-statements"></a>If-else 語句

您可以使用來表示程式`@if`代碼分支, 如此範本範例中所示。

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

#### <a name="loops"></a>環回

也可以新增`foreach`迴圈結構, 例如。 前置詞可用於迴圈變數 ( `@food`在此案例中為), 以便在 HTML 中呈現。 `@`

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

上述範本的輸出會顯示在 iOS 模擬器上執行, 並 Android Emulator:

 ![Rupert X 猴子](images/image9_520x277.png)

本節涵蓋使用 Razor 範本轉譯簡單唯讀視圖的基本概念。 下一節說明如何使用 Razor 建立更完整的應用程式, 以接受使用者輸入, 並在 HTML 視圖和C#中的 JavaScript 之間相交互操作。

## <a name="using-razor-templates-with-xamarin"></a>搭配使用 Razor 範本與 Xamarin

本節說明如何使用 Visual Studio for Mac 中的解決方案範本, 以建立您自己的混合式應用程式。 檔案中有三個可用的範本 **> 新的 > 解決方案 ...**  視窗:

- **Android > 應用程式 > Android Web 視圖應用程式**
- **iOS > App > Web 應用程式**
- **ASP.NET MVC 專案**



[**新增解決方案**] 視窗在 IPhone 和 Android 專案中看起來像這樣: 在右側的解決方案描述會強調 Razor 範本化引擎的支援。

 ![建立 iPhone 和 Android 解決方案](images/image13_1139x959.png)

請注意, 您可以輕鬆地將**cshtml** Razor 範本新增至*任何*現有的 Xamarin 專案, 而不需要使用這些解決方案範本。 iOS 專案不需要分鏡腳本就可以使用 Razor;只要以程式設計方式將 UIWebView 控制項新增至任何視圖, 您就可以在C#程式碼中轉譯 Razor 範本。

IPhone 和 Android 專案的預設範本解決方案內容如下所示:

 ![iPhone 和 Android 範本](images/image10_428x310.png)

範本可讓您準備好使用應用程式基礎結構, 以透過資料模型物件載入 Razor 範本、處理使用者輸入, 以及透過 JavaScript 與使用者通訊。

解決方案的重要部分包括:

- 靜態內容, 例如**style .css**檔案。
- Razor. cshtml 範本檔案, 例如**RazorView。**
- Razor 範本中參考的模型類別, 例如**ExampleModel.cs** 。
- 建立 web view 並轉譯範本的平臺特定類別, 例如 Android 上的`MainActivity` `iPhoneHybridViewController`和 iOS 上的。


下一節將說明專案的工作方式。

### <a name="static-content"></a>靜態內容

靜態內容包括 CSS 樣式表單、影像、JavaScript 檔案或其他內容, 可從 web 視圖中顯示的 HTML 檔案連結或加以參考。

範本專案包含最小的樣式表單, 以示範如何在混合式應用程式中包含靜態內容。 CSS 樣式表單會在範本中參考, 如下所示:

```html
<link rel="stylesheet" href="style.css" />
```

您可以新增所需的任何樣式表單和 JavaScript 檔案, 包括 JQuery 之類的架構。

### <a name="razor-cshtml-templates"></a>Razor cshtml 範本

此範本包含 Razor **. cshtml**檔案, 該檔案具有預先撰寫的程式碼, 可協助在 HTML/JavaScript 和C#之間傳達資料。 這可讓您建立複雜的混合式應用程式, 而不只會顯示模型中的唯讀資料, 也會接受 HTML 中的使用者輸入, 並將C#其傳遞回程序代碼以進行處理或儲存。

#### <a name="rendering-the-template"></a>轉譯範本

在範本`GenerateString`上呼叫, 可呈現 HTML 準備好在 web 視圖中顯示。 如果範本使用模型, 則應該在呈現之前提供。 此圖說明轉譯的運作方式, 而不是 web view 在執行時間解析靜態資源, 而是使用提供的基底目錄來尋找指定的檔案。

 ![Razor 流程圖](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>從C#範本呼叫程式碼

從轉譯的 web 視圖進行通訊的方式C#是設定 web 視圖的 URL, 然後在中C#攔截要求以處理原生要求, 而不需要重載 web view。

您可以在 RazorView 的按鈕處理方式中看到範例。 按鈕具有下列 HTML:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

JavaScript 函式會讀取 HTML 表單中的所有值, 並設定 web view 的`location.href`: `InvokeCSharpWithFormValues`

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

這會嘗試將 web view 流覽至具有自訂配置的 URL (例如, `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

當原生 web view 處理此流覽要求時, 我們有機會攔截它。 在 iOS 中, 這是藉由處理 UIWebView 的 HandleShouldStartLoad 事件來完成。 在 Android 中, 我們只會將表單中使用的來處理子類別化, 並覆寫 ShouldOverrideUrlLoading。

這兩個流覽攔截器的內部基本上是相同的。

首先, 檢查 web view 嘗試載入的 URL, 如果它不是以自訂配置 (`hybrid:`) 開頭, 則允許導覽正常執行。

針對自訂 URL 配置, 此配置與 "？" 之間的 URL 中的所有專案 這是要處理的方法名稱 (在此案例中為 "UpdateLabel")。 查詢字串中的所有專案都會被視為方法呼叫的參數:

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel`在此範例中, 會在 textbox 參數上進行最少量的字串操作 (C#在字串前面加上「表示」), 然後回呼至 web view。

處理 URL 之後, 方法會中止流覽, 讓 web view 不會嘗試完成流覽至自訂 URL。

#### <a name="manipulating-the-template-from-c"></a>從操作範本C#

從C#到轉譯的 HTML web view 的通訊是藉由呼叫 web view 中的 JavaScript 來完成。 在 iOS 上, 您可以在 UIWebView `EvaluateJavascript`上呼叫來完成這項作業:

```csharp
webView.EvaluateJavascript (js);
```

在 Android 上, 您可以在 web view 中叫用 javascript, 方法是使用`"javascript:"` url 配置將 javascript 載入為 url:

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>讓應用程式真正混合

這些範本不會使用每個平臺上的原生控制項–整個畫面會以單一 web 視圖填滿。

HTML 非常適合用來建立原型, 並顯示 web 最適合的專案類型, 例如 rtf 和回應式版面配置。 不過, 並非所有工作都適用于 HTML 和 JavaScript –透過長資料清單進行滾動, 例如, 使用原生 UI 控制項 (例如 iOS 上的 UITableView 或 Android 上的 ListView) 執行得更好。

範本中的 web views 可以輕鬆地使用平臺特定控制項來增強–只要在 iOS 設計工具中編輯**mainstoryboard.storyboard** , 或在 Android 上編輯**Resources/layout/axml** 。

### <a name="razortodo-sample"></a>RazorTodo 範例

[RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)存放庫包含兩個不同的解決方案, 以顯示完全以 html 為導向的應用程式, 以及結合 HTML 與原生控制項的應用程式之間的差異:

- **RazorTodo** -使用 Razor 範本的完全 HTML 驅動應用程式。
- **RazorNativeTodo** -使用適用于 IOS 和 Android 的原生清單視圖控制項, 但以 HTML 和 Razor 顯示編輯畫面。


這些 Xamarin 應用程式會在 iOS 和 Android 上執行, 利用可移植的類別庫 (Pcl) 來共用一般程式碼, 例如資料庫和模型類別。 您也可以在 PCL 中包含 Razor 樣板範本, 以便輕鬆地跨平臺共用它們。

這兩個範例應用程式都是從原生平臺納入 Twitter 共用和文字轉換語音 Api, 示範使用 Xamarin 的混合式應用程式仍可從 HTML Razor 範本驅動的視圖存取所有基礎功能。

**RazorTodo**應用程式會針對 [清單] 和 [編輯] 視圖使用 HTML Razor 範本。 這表示我們幾乎可以在共用的可攜性類別庫 (包括資料庫和 **. cshtml** Razor 範本) 中完全建立應用程式。 下列螢幕擷取畫面顯示 iOS 和 Android 應用程式。

 ![RazorTodo](images/Both_700x290.png)

**RazorNativeTodo**應用程式會針對 [編輯] 視圖使用 HTML Razor 範本, 但會在每個平臺上執行原生滾動清單。 這可提供許多優點, 包括:

- 效能-原生的滾動控制項使用虛擬化來確保快速、順暢地滾動, 即使有非常長的資料清單也一樣。
- 原生體驗-可以輕鬆地啟用平臺特定的 UI 元素, 例如 iOS 和 Android 中的快速滾動索引支援。


使用 Xamarin 建立混合式應用程式的主要優點是, 您可以從完全以 HTML 為導向的使用者介面 (例如第一個範例) 開始, 然後在必要時新增平臺特定的功能 (如第二個範例所示)。 IOS 和 Android 上的原生清單畫面和 HTML Razor 編輯畫面如下所示。

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>總結

本文說明可在 iOS 和 Android 上使用的 web view 控制項功能, 有助於建立混合式應用程式。

接著討論過 Razor 範本化引擎和語法, 可使用在 Xamarin 應用程式中輕鬆地產生 HTML。**cshtml**Razor 範本檔案。 其中也說明了 Visual Studio for Mac 解決方案範本, 可讓您快速開始使用 Xamarin 建立混合式應用程式。

最後, 它引進了 RazorTodo 範例, 示範如何結合 web views 與原生使用者介面和 Api。

### <a name="related-links"></a>相關連結

- [RazorTodo 範例](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3-Razor View 引擎 (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [使用 Razor 語法 ASP.NET Web 程式設計的簡介 (Microsoft)](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)

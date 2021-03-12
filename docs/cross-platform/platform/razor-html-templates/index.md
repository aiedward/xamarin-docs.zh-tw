---
title: 使用 Razor 範本建立 HTML 視圖
description: " 使用全螢幕網頁轉譯 HTML 可能是以跨平臺方式轉譯複雜格式的簡單且有效的方式，特別是當您已經有網站專案的 HTML、JavaScript 和 CSS。"
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: davidortinau
ms.author: daortin
ms.date: 07/24/2018
ms.openlocfilehash: a74ee38e8321ed4537d5181cda37f542d6a87dc6
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602836"
---
# <a name="building-html-views-using-razor-templates"></a>使用 Razor 範本建立 HTML 視圖

在行動裝置開發環境中，「混合式應用程式」一詞通常是指在裝載的 web 檢視器控制項內以 HTML 網頁呈現某些 (或其畫面的所有) 的應用程式。

有一些開發環境可讓您以 HTML 和 JavaScript 完全建立行動應用程式，但這些應用程式可能會在嘗試完成複雜的處理或 UI 效果時遇到效能問題，而且也會受到其可存取的平臺功能限制。

Xamarin 提供這兩個領域的最佳選擇，特別是在利用 Razor HTML 範本化引擎時。 有了 Xamarin，您可以彈性地建立使用 JavaScript 和 CSS 的跨平臺樣板化 HTML 視圖，但也可以完整存取基礎平臺 Api，並使用 c # 進行快速處理。

本檔說明如何使用 Razor 範本引擎來建立 HTML + JavaScript + CSS 視圖，以使用 Xamarin 跨行動平臺使用。

## <a name="using-web-views-programmatically"></a>以程式設計方式使用 Web 視圖

在我們瞭解 Razor 之前，本節討論如何使用 web 視圖直接顯示 HTML 內容，特別是在應用程式內產生的 HTML 內容。

Xamarin 提供 iOS 和 Android 上基礎平臺 Api 的完整存取權，因此您可以輕鬆地使用 c # 建立及顯示 HTML。 每個平臺的基本語法如下所示。

### <a name="ios"></a>iOS

在 Xamarin 的 UIWebView 控制項中顯示 HTML。 iOS 也只需要幾行程式碼：

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

如需使用 UIWebView 控制項的詳細資訊，請參閱 [IOS UIWebView](https://github.com/xamarin/docs-archive/tree/master/Recipes/ios/content_controls/web_view) 配方。

### <a name="android"></a>Android

使用 Xamarin 在 Web 視圖控制項中顯示 HTML，只需幾行程式碼即可完成：

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable JavaScript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

如需使用 [Web 程式] 控制項的詳細資訊，請參閱 [Android 資料檢視](https://github.com/xamarin/docs-archive/tree/master/Recipes/android/controls/webview) 食譜。

### <a name="specifying-the-base-directory"></a>指定基底目錄

在這兩個平臺上都有一個參數，可指定 HTML 網頁的基底目錄。 這是裝置檔案系統上的位置，可用來解析資源（例如影像和 CSS 檔案）的相對參考。 例如，標記，例如

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

請參閱下列檔案： **style .css**、 **monkey.jpg** 和 **jscript.js**。 [基底目錄] 設定會告訴 web 視圖這些檔案所在的位置，以便將這些檔案載入頁面中。

#### <a name="ios"></a>iOS

範本輸出會以下列 c # 程式碼在 iOS 中呈現：

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

基底目錄是指定為 `NSBundle.MainBundle.BundleUrl` 應用程式安裝所在的目錄。 [ **Resources** ] 資料夾中的所有檔案都會複製到這個位置，例如此處顯示的 **style .css** 檔案：

 ![iPhoneHybrid 解決方案](images/image1_240x163.png)

所有靜態內容檔案的組建動作都應該是 **套件套件**：

 ![iOS 專案組建動作：套件套件](images/image2_250x131.png)

#### <a name="android"></a>Android

當 web 視圖中顯示 html 字串時，Android 也需要以參數形式傳遞基底目錄。

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

特殊字元串 **file:///android_asset/** 是指您應用程式中的 [android 資產] 資料夾，其中包含 **.css 樣式的 .css** 檔案。

 ![AndroidHybrid 解決方案](images/image3_240x167.png)

所有靜態內容檔案的組建動作都應該是 **AndroidAsset**。

 ![Android 專案組建動作： AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>從 HTML 和 JavaScript 呼叫 c #

將 html 網頁載入至 web 視圖時，會將連結和表單視為從伺服器載入頁面時的方式。 這表示，如果使用者按一下連結或提交表單，web view 會嘗試流覽至指定的目標。

如果連結是連到外部伺服器 (例如 google.com) 則 web view 會嘗試載入外部網站 (假設有網際網路連接) 。

```html
<a href="http://google.com/">Google</a>
```

如果連結是相對的，則 web 視圖會嘗試從基底目錄載入該內容。 顯然，此作業不需要網路連線，因為內容會儲存在裝置上的應用程式中。

```html
<a href="somepage.html">Local content</a>
```

表單動作遵循相同的規則。

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

您不會在用戶端上裝載 web 伺服器;不過，您可以使用在現今回應式設計模式中採用的相同伺服器通訊技術，透過 HTTP GET 呼叫服務，並藉由發出 JavaScript (或呼叫已裝載于 web view) 的 JavaScript，以非同步方式處理回應。 這可讓您輕鬆地將資料從 HTML 傳遞回 c # 程式碼以進行處理，然後將結果顯示在 HTML 頁面上。

IOS 和 Android 都提供一種機制，讓應用程式程式碼攔截這些導覽事件，讓應用程式程式碼可以在必要時回應 (的) 。 這項功能對於建立混合式應用程式非常重要，因為它可讓原生程式碼與 web view 互動。

#### <a name="ios"></a>iOS

您可以覆寫 iOS web view 上的 ShouldStartLoad 事件，以允許應用程式程式碼處理導覽要求 (例如，按一下) 的連結。 方法參數提供所有資訊

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

然後指派事件處理常式：

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

在 Android 上，直接將來處理子類別化，然後執行程式碼以回應導覽要求。

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

然後在 web view 上設定用戶端：

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>從 C 呼叫 JavaScript\#

除了告訴 web view 載入新的 HTML 頁面之外，c # 程式碼也可以在目前顯示的頁面內執行 JavaScript。 您可以使用 c # 字串和執行程式來建立整個 JavaScript 程式碼區塊，也可以透過標記製作網頁上已有的 JavaScript 方法呼叫 `script` 。

#### <a name="android"></a>Android

建立要執行的 JavaScript 程式碼，然後在其前面加上 "JavaScript："，並指示 web view 載入該字串：

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

iOS web views 提供專門用來呼叫 JavaScript 的方法：

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>摘要

本節介紹 Android 和 iOS 上的 web view 控制項功能，可讓我們使用 Xamarin 建立混合式應用程式，包括：

- 從程式碼中產生的字串載入 HTML 的能力。
-  (CSS、JavaScript、映射或其他 HTML 檔案參考本機檔案的能力) 
- 在 c # 程式碼中攔截導覽要求的能力，
- 從 c # 程式碼呼叫 JavaScript 的能力。

下一節介紹 Razor，可讓您輕鬆地建立要在混合式應用程式中使用的 HTML。

## <a name="what-is-razor"></a>什麼是 Razor？

Razor 是 ASP.NET MVC 引進的範本化引擎，最初是在伺服器上執行，並產生 HTML 來提供給網頁瀏覽器使用。

Razor 範本引擎使用 c # 擴充標準 HTML 語法，讓您可以表達版面配置，並輕鬆地併入 CSS 樣式表單和 JavaScript。 範本可以參考模型類別，這可以是任何自訂型別，而且可以直接從範本存取其屬性。 其中一個主要優點是能夠輕鬆地混合 HTML 和 c # 語法。

Razor 範本不限於伺服器端使用，它們也可以包含在 Xamarin 應用程式中。 使用 Razor 範本以及能夠以程式設計方式使用 web 視圖，可讓您以 Xamarin 建立精密的跨平臺混合式應用程式。

### <a name="razor-template-basics"></a>Razor 範本基本概念

Razor 範本檔案的副檔名為 **cshtml** 。 您可以在 [ **新增** 檔案] 對話方塊中，從 [文字模板] 區段將它們新增至 Xamarin 專案：

 ![新檔案-Razor 範本](images/image5_400x201.png)

簡單的 Razor 範本 ( **RazorView**) 如下所示。

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

請注意下列與一般 HTML 檔案的差異：

- `@`符號在 Razor 範本中具有特殊意義-它表示下列運算式是要評估的 c #。
- `@model` 指示詞一律會顯示為 Razor 範本檔案的第一行。
- 指示詞 `@model` 後面應該接著型別。 在此範例中，會將簡單字串傳遞至範本，但這可能是任何自訂類別。
- 在 `@Model` 整個範本中參考時，它會提供在此範例中傳遞給範本之物件的參考 (在此範例中，它會是) 的字串。
- IDE 會自動產生範本 (檔案的部分類別，) 加上 **cshtml 副檔名。** 您可以查看這段程式碼，但不應該進行編輯。
 ![RazorView ](images/image6_125x34.png) ，部分類別的名稱為 RazorView，以符合 cshtml 範本檔案名。 這是用來在 c # 程式碼中參考範本的名稱。
- `@using` 語句也可以包含在 Razor 範本的頂端，以包含其他命名空間。

然後可以使用下列 c # 程式碼產生最後的 HTML 輸出。 請注意，我們將模型指定為字串 "Hello World"，將會併入轉譯的範本輸出中。

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

以下是 iOS 模擬器和 Android 模擬器上的 web 視圖所顯示的輸出：

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>更多 Razor 語法

在本節中，我們將介紹一些基本的 Razor 語法，以協助您開始使用它。 本節中的範例會將資料填入下列類別，並使用 Razor 來顯示它：

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

當模型是具有屬性的類別時，Razor 範本中會很容易參考，如下列範例範本所示：

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

您可以使用下列程式碼，將其轉譯為字串：

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

最後的輸出會顯示在 iOS 模擬器和 Android 模擬器的 web 視圖中：

 ![魯珀特](images/image8_516x160.png)

#### <a name="c-statements"></a>C # 語句

範本中可包含更複雜的 c #，例如模型屬性更新和此範例中的年齡計算：

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

您可以撰寫複雜的單行 c # 運算式 (例如，將程式碼周圍的時間) 格式化 `@()` 。

您可以使用多個 c # 語句來撰寫它們 `@{}` 。

#### <a name="if-else-statements"></a>If else 語句

您可以使用來表示程式碼分支， `@if` 如這個範本範例所示。

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

`foreach`也可以加入像這樣的迴圈結構。 `@`前置詞可用於迴圈變數 ( `@food` 在此案例中) 以 HTML 格式呈現。

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

上述範本的輸出會顯示在 iOS 模擬器和 Android 模擬器上執行：

 ![Rupert X 猴子](images/image9_520x277.png)

本節涵蓋了使用 Razor 範本來呈現簡單唯讀視圖的基本概念。 下一節說明如何使用 Razor 來建立更完整的應用程式，以接受使用者輸入，並在 HTML view 和 c # 中的 JavaScript 之間相交互操作。

## <a name="using-razor-templates-with-xamarin"></a>使用 Razor 範本搭配 Xamarin

本節說明如何使用 Visual Studio for Mac 中的解決方案範本來建立您自己的混合式應用程式。 檔案中有三個可用的範本 **> 新的 > 解決方案 ...** 視窗：

- **Android > 應用程式 > Android Web 程式應用程式**
- **iOS > 應用程式 > Web 應用程式**
- **ASP.NET MVC 專案**

適用于 iPhone 和 Android 專案的 **新方案** 視窗看起來像這樣：在右側的解決方案描述中，會針對 Razor 範本化引擎的支援醒目顯示支援。

 ![建立 iPhone 和 Android 解決方案](images/image13_1139x959.png)

請注意，您可以輕鬆地將 **cshtml** Razor 範本新增至 *任何* 現有的 Xamarin 專案，不需要使用這些解決方案範本。 iOS 專案不需要分鏡腳本就可以使用 Razor。只要以程式設計的方式將 UIWebView 控制項新增至任何視圖，您就可以在 c # 程式碼中全部轉譯 Razor 範本。

IPhone 和 Android 專案的預設範本解決方案內容如下所示：

 ![iPhone 和 Android 範本](images/image10_428x310.png)

這些範本提供您現成的應用程式基礎結構，以使用資料模型物件載入 Razor 範本、處理使用者輸入，並透過 JavaScript 與使用者通訊。

解決方案的重要部分包括：

- 靜態內容，例如  **style .css** 檔。
- Razor. cshtml 範本檔案，例如 **RazorView。**
- Razor 範本（例如  **ExampleModel.cs** ）中所參考的模型類別。
- 建立 web view 並轉譯範本的平臺特定類別，例如  `MainActivity` Android 上的和  `iPhoneHybridViewController` iOS 上的。

下一節將說明專案的運作方式。

### <a name="static-content"></a>靜態內容

靜態內容包括 CSS 樣式表單、影像、JavaScript 檔案或其他內容，可由 web view 中顯示的 HTML 檔案進行連結或參考。

範本專案包含最基本的樣式表單，以示範如何在混合式應用程式中包含靜態內容。 CSS 樣式表單會在範本中參考，如下所示：

```html
<link rel="stylesheet" href="style.css" />
```

您可以新增任何您需要的樣式表單和 JavaScript 檔案，包括 JQuery 之類的架構。

### <a name="razor-cshtml-templates"></a>Razor cshtml 範本

範本 **包含 Razor 檔** ，該檔案具有預先撰寫的程式碼，可協助您在 HTML/JavaScript 和 c # 之間進行資料通訊。 這可讓您建立複雜的混合式應用程式，這些應用程式不只會顯示模型中的唯讀資料，還接受 HTML 中的使用者輸入，並將其傳遞回 c # 程式碼以進行處理或儲存。

#### <a name="rendering-the-template"></a>轉譯範本

`GenerateString`在範本上呼叫會呈現 HTML，以供在 web 視圖中顯示。 如果範本使用模型，則應該在轉譯之前提供。 下圖說明轉譯的運作方式，而不是 web view 在執行時間解析靜態資源，而是使用提供的基底目錄來尋找指定的檔案。

 ![Razor 流程圖](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>從範本呼叫 c # 程式碼

藉由設定 web view 的 URL，然後在 c # 中攔截要求以處理原生要求，而不需重載 web view，即可從已轉譯的 web view 呼叫回 c # 來進行通訊。

您可以在處理 RazorView 的按鈕時看到範例。 此按鈕具有下列 HTML：

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

JavaScript 函式會 `InvokeCSharpWithFormValues` 讀取 HTML 表單中的所有值，並設定 `location.href` web view 的：

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

這會嘗試使用自訂配置將 web view 移至 URL， (例如 `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

當原生 web view 處理此流覽要求時，我們有機會攔截它。 在 iOS 中，這是藉由處理 UIWebView 的 HandleShouldStartLoad 事件來完成。 在 Android 中，我們只是將表單中所使用的來處理子類別化，並覆寫 ShouldOverrideUrlLoading。

這兩個流覽攔截器的內部本質上是相同的。

首先，請檢查 web view 嘗試載入的 URL，如果它不是以自訂配置 () 開始，則 `hybrid:` 允許依正常情況進行導覽。

針對自訂 URL 配置，配置與 "？" 之間的 URL 中的所有專案 這是要處理的方法名稱 (在此案例中為 "UpdateLabel" ) 。 查詢字串中的所有專案都會被視為方法呼叫的參數：

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` 在此範例中，會對 textbox 參數執行最少的字串操作， (在字串) 前面加上 "c #"，然後再回呼至 web view。

處理 URL 之後，方法會中止流覽，讓 web view 不會嘗試完成流覽至自訂 URL。

#### <a name="manipulating-the-template-from-c"></a>從 C 處理範本\#

從 c # 對轉譯的 HTML web view 進行通訊的方式是在 web view 中呼叫 JavaScript。 在 iOS 上，您可以在 UIWebView 上呼叫來完成此動作 `EvaluateJavascript` ：

```csharp
webView.EvaluateJavascript (js);
```

在 Android 上，您可以在 web view 中叫用 JavaScript，方法是使用 url 配置，將 JavaScript 載入為 URL `"javascript:"` ：

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>使應用程式真正的混合式

這些範本不會在每個平臺上使用原生控制項–整個畫面都會以單一 web 視圖填滿。

HTML 可能很適合用來建立原型，並顯示 web 最適合的專案類型，例如 rtf 和回應式版面配置。 不過，並非所有工作都適用于 HTML 和 JavaScript –滾動完整的資料清單，例如，使用原生 UI (控制項（例如在 iOS 上 Ios uitableview 範例或 Android) 上的 ListView）可以更妥善地執行。

您可以輕鬆地使用平臺特定的控制項來增強範本中的網頁流覽-只要在 Mac 上使用 Xcode 或在 Android 上使用 **資源/配置/.axml** 來編輯 **MainStoryboard。**

### <a name="razortodo-sample"></a>RazorTodo 範例

[RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)存放庫包含兩個不同的解決方案，可顯示完全 html 驅動的應用程式，以及結合 HTML 與原生控制項的應用程式之間的差異：

- 使用 Razor 範本的 **RazorTodo** 完全 HTML 驅動應用程式。
- **RazorNativeTodo** -使用適用于 IOS 和 Android 的原生清單視圖控制項，但會顯示具有 HTML 和 Razor 的編輯畫面。

這些 Xamarin 應用程式會在 iOS 和 Android 上執行，利用可攜的類別庫 (Pcl) 共用一般程式碼，例如資料庫和模型類別。 Razor **樣板** 也可以包含在 PCL 中，以便輕鬆地跨平臺共用。

這兩個範例應用程式會從原生平臺納入 Twitter 共用和文字轉換語音 Api，並示範使用 Xamarin 的混合式應用程式，仍然可以從 HTML Razor 範本驅動的觀點存取所有的基礎功能。

**RazorTodo** 應用程式會針對清單和編輯檢視使用 HTML Razor 範本。 這表示我們幾乎可以在共用的便攜類別庫中建立應用程式， (包括資料庫和 **cshtml** Razor 範本) 。 下列螢幕擷取畫面顯示 iOS 和 Android 應用程式。

 ![RazorTodo](images/Both_700x290.png)

**RazorNativeTodo** 應用程式會針對編輯檢視使用 HTML Razor 範本，但會在每個平臺上執行原生滾動清單。 這可提供許多優點，包括：

- 效能-原生滾動控制項會使用虛擬化，以確保即使有非常長的資料清單，也能快速且順暢地滾動。
- 原生體驗-平臺特定的 UI 元素很容易啟用，例如 iOS 和 Android 的快速滾動索引支援。

使用 Xamarin 建立混合式應用程式的主要優點，就是您可以使用完全以 HTML 導向的使用者介面來開始 (例如第一個範例) ，然後在必要時加入平臺特定的功能 (如第二個範例所示) 。 IOS 和 Android 上的原生清單畫面和 HTML Razor 編輯畫面如下所示。

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>摘要

本文已說明可在 iOS 和 Android 上使用的 web view 控制項功能，可協助您建立混合式應用程式。

接著會討論 Razor 範本化引擎，以及可以用來在 Xamarin 應用程式中輕鬆地產生 HTML 的語法。**cshtml** Razor 範本檔案。 文中也說明了 Visual Studio for Mac 解決方案範本，可讓您快速開始使用 Xamarin 建立混合式應用程式。

最後，它引進了 RazorTodo 範例，示範如何結合 web views 與原生使用者介面和 Api。

### <a name="related-links"></a>相關連結

- [RazorTodo 範例](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3-Razor View Engine (Microsoft) ](https://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [使用 Razor 語法 ASP.NET Web 程式設計的簡介 (Microsoft) ](https://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)

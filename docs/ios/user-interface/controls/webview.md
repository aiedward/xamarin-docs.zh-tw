---
title: Xamarin.iOS 中的 Web 檢視
description: 本文件介紹了 Xamarin.iOS 應用顯示 Web 內容的各種方式。 它討論了 WKWebView、SFSafariView控制器、Safari 和應用程式傳輸安全性。
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: cc6c87452bf5312d66e33b7c49d3dc216eceb7d6
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628302"
---
# <a name="web-views-in-xamarinios"></a>Xamarin.iOS 中的 Web 檢視

在 iOS 的生命週期中,Apple 發布了許多應用開發人員在其應用中集成 Web 視圖功能的方法。 大多數使用者在其 iOS 裝置上使用內建的 Safari Web 瀏覽器,因此希望其他應用的 Web 視圖功能與此體驗一致。 他們期望相同的手勢工作,性能是平和的,功能相同。

iOS 11 引入了`WKWebView`新的`SFSafariViewController`變更, 對與 。 有關這些的詳細資訊,請參閱[iOS 11 指南中的 Web 更改](~/ios/platform/introduction-to-ios11/web.md)。

## <a name="wkwebview"></a>WKWebView

`WKWebView`iOS 8 中引入了,允許應用開發人員實現類似於移動 Safari 的 Web 瀏覽介面。 部分原因在於`WKWebView`使用 Nitro Javascript 引擎,該引擎與移動 Safari 使用的引擎相同。 `WKWebView`由於性能提高(內置了使用者友好的手勢),以及網頁和應用程式之間交互的便利性,因此應始終在 UIWebView 上使用。

`WKWebView`可以以與 UIWebView 幾乎相同的方式添加到應用中,但是作為開發人員,您可以對 UI/UX 和功能有更多的控制。 創建和顯示 Web 檢視物件將顯示請求的頁面,但您可以控制檢視的顯示方式、用戶導航方式以及使用者退出檢視的方式。  

以下代碼可用於在 Xamarin.iOS 應用中`WKWebView`啟動 :

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

請務必注意,`WKWebView`該名稱空間`WebKit`位於 命名空間中,因此您必須使用指令將此指令添加到類的頂部。

`WKWebView`也可以在 Xamarin.Mac 應用中使用,如果您正在創建跨平臺 Mac/iOS 應用,則應使用它。

[處理 JavaScript 警報](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts)配方還提供有關使用 WKWebView 與 Javascript 一起使用的資訊。

## <a name="sfsafariviewcontroller"></a>SFSafariView控制器

 `SFSafariViewController`是從你的應用提供 Web 內容的最新方式,可在 iOS 9 及更高版本中提供。 與`UIWebView``WKWebView``SFSafariViewController`或 是檢視控制器,因此不能與其他檢視一起使用。 您應該以新的`SFSafariViewController`檢視控制器方式呈現,就像呈現任何檢視控制器一樣。

 `SFSafariViewController`本質上是一個"迷你野生動物園",可以嵌入到你的應用中。 與 WKWebView 一樣,它使用相同的 Nitro Javascript 引擎,但也提供了一系列額外的 Safari 功能,如自動填充、讀取器,以及與移動 Safari 共用 Cookie 和數據的能力。 用戶和之間的`SFSafariViewController`交互對應用是無法訪問的。 你的應用將無法訪問任何預設的 Safari 功能。

默認情況下,它還實現了 **「完成」** 按鈕,允許用戶輕鬆返回到你的應用,以及前進和後退導航按鈕,允許使用者流覽一堆網頁。 此外,它還為使用者提供了一個位址欄,讓他們安心,他們出現在預期的網頁上。 位址列不允許使用者更改 URL。

無法更改這些實現,因此`SFSafariViewController`,如果應用希望在沒有任何自定義的情況下顯示網頁,則最好用作預設瀏覽器。

以下代碼可用於在 Xamarin.iOS 應用中`SFSafariViewController`啟動 :

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

這會產生以下 Web 檢視:

[![帶有 SFSafariView 控制器的範例 Web 檢視](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

還可以使用以下代碼從應用內打開移動 Safari 應用:

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

這會產生以下 Web 檢視:

[![在 Safari 中展示的網頁](webview-images/safari.png)](webview-images/safari.png#lightbox)

通常應始終避免將使用者從應用導航到 Safari。 大多數使用者不會期望在應用程式外部進行導航,因此,如果您遠離應用導航,使用者可能永遠不會返回它,這實質上會扼殺參與度。

iOS 9 的改進允許用戶通過 Safari 頁面左上角提供的後退按鈕輕鬆返回到你的應用。

## <a name="app-transport-security"></a>應用程式傳輸安全性

應用傳輸安全 *(ATS)* 由 Apple 在 iOS 9 中引入,以確保所有互聯網通信都符合安全連接最佳實踐。

有關 ATS 的詳細資訊(包括如何在應用中實現它),請參閱[應用傳輸安全](~/ios/app-fundamentals/ats.md)指南。

## <a name="uiwebview-deprecation"></a>UIWebView 棄用

`UIWebView`是 Apple 在應用中提供 Web 內容的傳統方式。 它在 iOS 2.0 中發佈,並且自 8.0 起已被棄用。

> [!IMPORTANT]
> `UIWebView` 已被取代。 自 2020 年 4 月起,使用此控制項的新應用[將不接受 App Store,並且在 2020 年 12 月前不接受使用此控制件的應用更新](https://developer.apple.com/news/?id=12232019b)。
>
> [此檔案`UIWebView`顯示](https://developer.apple.com/documentation/uikit/uiwebview),套用應該改[`WKWebView`](#wkwebview)用 。
>
> 如果您在使用 Xamarin.Forms`UIWebView`時尋找與棄用警告 (ITMS-90809) 有關的資源,請參閱[Xamarin.Forms WebView](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809)文件。

在過去六個月(或更長時間)提交 iOS 應用程式的開發人員可能會收到來自 App Store`UIWebView`的警告,警告 他們被棄用。

API 的棄用很常見。 Xamarin.iOS 使用自定義屬性向開發人員發出信號(如果可用,建議替換)。 這次不同,更不常見的是,蘋果的App Store將在提交時**強制**棄用。

沒有是,`UIWebView``Xamarin.iOS.dll`從中移除類型是[二進制的破壞變更](https://docs.microsoft.com/dotnet/core/compatibility/categories#binary-compatibility)。 此更改將破壞現有的第三方庫,包括一些可能不支援甚至重新編譯的庫(例如,關閉源)。 這隻會給開發人員帶來其他問題。 因此,我們*尚未*刪除類型。

從[Xamarin.iOS 13.16](https://docs.microsoft.com/xamarin/ios/release-notes/13/13.16)開始,新的檢測和工具可`UIWebView`説明您從 進行遷移。

### <a name="detection"></a>偵測

如果您最近沒有向 Apple App Store 提交 iOS 應用程式,您可能會懷疑這種情況是否適用於您的應用程式。

要找出答案,您可以添加到`--warn-on-type-ref=UIKit.UIWebView`專案的 **「附加 mtouch 參數」。。** 這將警告**any**對`UIWebView`應用程式 內部棄用的任何引用(及其所有依賴項)。 不同的警告用於在託管連結器**before**執行**之前和之後**報告類型。

警告和其他警告一樣,可以使用 轉換為`-warnaserror:`錯誤 。 如果要確保驗證後不會添加新的依賴項,`UIWebView`這非常有用。 例如：

* `-warnaserror:1502`如果在預連結程式集中找到任何引用,將報告錯誤。
* `-warnaserror:1503`將在連結後程式集中找到任何引用時報告錯誤。

如果預/後鏈接結果沒有用,也可以靜默警告。 例如：

* `-nowarn:1502`如果在預連結程式集中找到任何引用,**將不會**報告警告。
* `-nowarn:1503`如果在連結後程式集中找到任何引用,**將不會**報告警告。

### <a name="removal"></a>移除

每個應用程式都是獨一無二的。 根據`UIWebView`使用方式和位置,從應用程式中刪除可能需要不同的步驟。 最常見的方案如下:

- 應用程式`UIWebView`內部沒有用。 一切都很好 提交到 AppStore 時**不應**有警告。 您不需要任何其他內容。
- 應用程式直接使用`UIWebView`。 首先刪除使用的`UIWebView`,例如,將其替換為較`WKWebView`新的 (iOS`SFSafariViewController`8) 或 (iOS 9) 類型。 完成此項後,託管連結器不應看到任何引用,`UIWebView`最終應用二進位檔將沒有追蹤。
- 間接使用。 `UIWebView`可以存在於應用程式使用的某些第三方庫中,無論是託管庫還是本機庫。 首先,將外部依賴項更新到其最新版本,因為這種情況可能已經在較新版本中解決。 如果沒有,請與庫的維護者聯繫,詢問其更新計劃。

或者,您可以嘗試以下方法:

1. 如果您使用的是**Xamarin.Forms**,請閱讀此[部落格文章](https://devblogs.microsoft.com/xamarin/uiwebview-deprecation-xamarin-forms/)。
1. 啟用託管連結器(在整個專案上,或者至少在使用`UIWebView`依賴項上啟用),以便*刪除*它(如果未引用)。 這將解決此問題,但可能需要其他工作來使代碼連結器安全。
1. 如果無法更改託管連結器設置,請參閱下面的特殊情況。

#### <a name="applications-cannot-use-the-linker-or-change-its-settings"></a>應用程式無法使用連結器(或變更其設定)

如果由於某種原因**您不使用**託管連結器(例如,**不要連結**`UIWebView`),則該符號將保留在您提交給 Apple 的二進位應用程式中,並且可能會被拒絕。

一*個強有力的*解決方案是新增到`--optimize=force-rejected-types-removal`你的項目**的附加 mtouch 論點**。 這會移除到應用程式中移除`UIWebView`追蹤 。 但是,任何引用該類型的**代碼將無法正常工作**(預期出現異常或崩潰)。 僅當確定代碼在運行時無法訪問時(即使可以通過靜態分析訪問代碼),才應使用此方法。

#### <a name="support-for-ios-7x-or-earlier"></a>支援 iOS 7.x(或更早版本)

`UIWebView`自 v2.0 以來一直是 iOS 的一部分。 最常見的更換是`WKWebView`(iOS 8) 和`SFSafariViewController`(iOS 9)。 如果應用程式仍然支援較舊的 iOS 版本,則應考慮以下選項:

* 使 iOS 8 成為最低目標版本(生成時間決策)。
* 僅當應用`WKWebView`在 iOS 8+ 上運行時(運行時決策)時才使用。

#### <a name="applications-not-submitted-to-apple"></a>未提交給 Apple 的申請

如果您的應用程式未提交到 Apple,則應計劃遠離已棄用的 API,因為可以在將來的 iOS 版本中將其刪除。 但是,您可以使用自己的時程表執行此轉換。

## <a name="related-links"></a>相關連結

- [網頁檢視(範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)

---
title: Xamarin 中的 Web Views
description: 本檔說明 Xamarin iOS 應用程式可以用來顯示 web 內容的各種方式。 其中討論 WKWebView、SFSafariViewController、Safari 和應用程式傳輸安全性。
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: a9dce962c35e5f9cfdcd674da9ad71cf8935e7d4
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2020
ms.locfileid: "80070323"
---
# <a name="web-views-in-xamarinios"></a>Xamarin 中的 Web Views

在 iOS Apple 的存留期內，應用程式開發人員已發行數種方式，以在其應用程式中納入 web view 功能。 大部分的使用者在其 iOS 裝置上使用內建的 Safari web 瀏覽器，因此會預期來自其他應用程式的 web view 功能會與此體驗一致。 它們預期會有相同的手勢來運作、將效能視為同等，以及功能相同。

iOS 11 引進了 `WKWebView` 和 `SFSafariViewController`的新變更。 如需這些功能的詳細資訊，請參閱[iOS 11 中的 Web 變更指南](~/ios/platform/introduction-to-ios11/web.md)。

## <a name="wkwebview"></a>WKWebView

`WKWebView` 是在 iOS 8 中引進，可讓應用程式開發人員執行類似于行動 Safari 的 web 流覽介面。 這種情況的原因是，`WKWebView` 使用 Nitro JAVAscript 引擎，這是 mobile Safari 所使用的相同引擎。 `WKWebView` 應該一律在可能的情況下使用於 UIWebView，因為效能增加、內建的使用者易記手勢，以及網頁與應用程式之間的互動輕鬆。

`WKWebView` 可以使用幾乎完全相同的方式新增至您的應用程式，但身為開發人員，您對 UI/UX 和功能有更大的控制權。 建立和顯示 web view 物件將會顯示要求的頁面，不過您可以控制如何呈現視圖、使用者可以流覽的方式，以及使用者如何結束視圖。  

下列程式碼可用來啟動您的 Xamarin iOS 應用程式中的 `WKWebView`：

```csharp
WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
View.AddSubview(webView);

var url = new NSUrl("https://docs.microsoft.com");
var request = new NSUrlRequest(url);
webView.LoadRequest(request);
```

請務必注意，`WKWebView` 是在 `WebKit` 命名空間中，因此您必須將此 using 指示詞新增至類別的頂端。

`WKWebView` 也可以在 Xamarin. Mac 應用程式中使用，如果您要建立跨平臺的 Mac/iOS 應用程式，則應使用此功能。

「[處理 JAVAscript 警示](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts)」配方也會提供有關使用 WKWebView 搭配 JavaScript 的資訊。

## <a name="sfsafariviewcontroller"></a>SFSafariViewController

 `SFSafariViewController` 是從您的應用程式提供 web 內容，並可在 iOS 9 和更新版本中取得的最新方式。 不同于 `UIWebView` 或 `WKWebView`，`SFSafariViewController` 是視圖控制器，因此不能與其他視圖一起使用。 您應該以新的視圖控制器的方式呈現 `SFSafariViewController`，就像使用任何視圖控制器一樣。

 `SFSafariViewController` 基本上是「迷你 safari」，可以內嵌到您的應用程式中。 如同 WKWebView，它會使用相同的 Nitro JAVAscript 引擎，但也會提供一系列額外的 Safari 功能，例如自動填滿、讀者，以及與 mobile Safari 共用 cookie 和資料的能力。 使用者和 `SFSafariViewController` 之間的互動無法供您的應用程式存取。 您的應用程式將無法存取任何預設的 Safari 功能。

根據預設，它也會執行 [**完成**] 按鈕，讓使用者可以輕鬆地返回您的應用程式，以及向前和向後瀏覽按鈕，讓您的使用者可以流覽一堆網頁。 此外，它也會為使用者提供一個網址列，讓他們知道他們是在預期的網頁上。 網址列不允許使用者變更 url。

這些執行無法變更，因此，如果您的應用程式想要呈現不含任何自訂的網頁，則最好使用 `SFSafariViewController` 做為預設瀏覽器。

下列程式碼可用來啟動您的 Xamarin iOS 應用程式中的 `SFSafariViewController`：

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

這會產生下列 web 視圖：

[![使用 SFSafariViewController 的範例 web 視圖](webview-images/sfsafariviewcontroller.png)](webview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

您也可以使用下列程式碼，從您的應用程式內開啟 mobile Safari 應用程式：

```csharp
var url = new NSUrl("https://docs.microsoft.com");

UIApplication.SharedApplication.OpenUrl(url);
```

這會產生下列 web 視圖：

[![Safari 中顯示的網頁](webview-images/safari.png)](webview-images/safari.png#lightbox)

通常一律避免將使用者從您的應用程式移至 Safari。 大部分的使用者都不會預期流覽至您的應用程式之外，因此，如果您離開應用程式，使用者可能永遠不會傳回它，基本上是終止 engagement。

iOS 9 改良功能可讓使用者透過 Safari 頁面左上角提供的 [上一頁] 按鈕，輕鬆地返回您的應用程式。

## <a name="app-transport-security"></a>應用程式傳輸安全性

IOS 9 中的 Apple 引進了應用程式傳輸安全性或*ATS* ，以確保所有網際網路通訊都符合安全連線的最佳作法。

如需 ATS 的詳細資訊，包括如何在您的應用程式中加以執行，請參閱[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)指南。

## <a name="uiwebview-deprecation"></a>UIWebView 淘汰

`UIWebView` 是 Apple 在您的應用程式中提供 web 內容的舊版方式。 它是在 iOS 2.0 中發行，並已淘汰8.0。

> [!IMPORTANT]
> `UIWebView` 已被取代。 [在2020年4月起，使用此控制項的新應用程式將不會接受到 App Store，而且12月2020將不會接受使用此控制項的應用程式更新](https://developer.apple.com/news/?id=12232019b)。
>
> [Apple 的 `UIWebView` 檔](https://developer.apple.com/documentation/uikit/uiwebview)建議應用程式應該改用[`WKWebView`](#wkwebview) 。
>
> 如果您要在使用 Xamarin 時尋找 `UIWebView` 取代警告（ITMS-90809）的相關資源，請參閱[Xamarin web](~/xamarin-forms/user-interface/webview.md#uiwebview-deprecation-and-app-store-rejection-itms-90809)工作檔。

過去六個月（或也就是）提交 iOS 應用程式的開發人員可能收到來自 App Store 的警告，關於 `UIWebView` 即將淘汰。

Api 的棄用功能是常見的。 Xamarin 會使用自訂屬性來通知這些 Api （並在可用時建議替代專案）回到開發人員。 這次的差異很大，而且較不常見，這是 Apple 的 App Store 在提交時將會**強制執行**取代。

可惜的是，從 `Xamarin.iOS.dll` 移除 `UIWebView` 類型是[二進位的重大變更](https://docs.microsoft.com/dotnet/core/compatibility/categories#binary-compatibility)。 這種變更將會中斷現有的協力廠商程式庫，包括可能不再支援或甚至重新編譯的部分（例如，關閉的來源）。 這只會為開發人員建立額外的問題。 因此，我們*還*不會移除型別。

從[Xamarin. iOS 13.16](https://docs.microsoft.com/xamarin/ios/release-notes/13/13.16)開始提供新的偵測和工具，協助您從 `UIWebView`進行遷移。

### <a name="detection"></a>偵測

如果您 have'nt 最近將 iOS 應用程式提交至 Apple App Store，您可能會想知道這種情況是否適用于您的應用程式。

若要找出，您可以將 `--warn-on-type-ref=UIKit.UIWebView` 新增至專案的**其他 mtouch 引數。** 這會警告應用程式內已淘汰之 `UIWebView` 的**任何**參考（及其所有相依性）。 在執行 managed 連結器**之前**和**之後**，會使用不同的警告來報告類型。

您可以使用 `-warnaserror:`，將警告視為錯誤。 如果您想要確保不會在驗證之後加入 `UIWebView` 的新相依性，這項功能就很有用。 例如：

* 如果在預先連結的元件中找到任何參考，`-warnaserror:1502` 將會報告錯誤。
* 如果在連結後的元件中找到任何參考，`-warnaserror:1503` 將會報告錯誤。

如果前置/後置連結結果不實用，您也可以將警告解除通知。 例如：

* 如果在預先連結的元件中找到任何參考，`-nowarn:1502` 將**不**會報告警告。
* 如果在連結後的元件中找到任何參考，`-nowarn:1503` 將**不**會報告警告。

### <a name="removal"></a>移除

每個應用程式都是唯一的。 從您的應用程式移除 `UIWebView` 可能需要不同的步驟，視使用的方式和位置而定。 最常見的案例如下：

- 您的應用程式內沒有使用 `UIWebView`。 一切都沒問題。 提交至 AppStore 時，您**不**應該有警告。 您不需要其他任何東西。
- 應用程式 `UIWebView` 的直接使用。 一開始請先移除您的 `UIWebView`使用方式，例如，將它取代為較新的 `WKWebView` （iOS 8）或 `SFSafariViewController` （iOS 9）類型。 完成此作業之後，managed 連結器應該看不到任何 `UIWebView` 的參考，最後的應用程式二進位檔將不會追蹤它。
- 間接使用。 `UIWebView` 可以出現在應用程式所使用的某些協力廠商程式庫中（受控或原生）。 開始將您的外部相依性更新為最新版本，因為這種情況可能已經在較新的版本中解決。 如果沒有，請聯絡程式庫的維護人員，並詢問其更新計畫。

或者，您可以嘗試下列方法：

1. 如果您使用的是 [ **Xamarin**]，請閱讀這[篇 blog 文章](https://devblogs.microsoft.com/xamarin/uiwebview-deprecation-xamarin-forms/)。
1. 啟用 managed 連結器（在整個專案上，或至少使用 `UIWebView`的相依性），以便在未參考的情況下*將它移除*。 這會解決問題，但可能需要額外的工作，才能讓您的程式碼連結器安全。
1. 如果您無法變更受管理的連結器設定，請參閱以下的特殊案例。

#### <a name="applications-cannot-use-the-linker-or-change-its-settings"></a>應用程式無法使用連結器（或變更其設定）

如果您因為某些原因而**未**使用 managed 連結器（例如 [**不要連結**]），則 `UIWebView` 符號會保留在您提交給 Apple 的二進位應用程式中，而且可能會遭到拒絕。

*強制*性解決方案是將 `--optimization=force-rejected-types-removal` 新增至您專案的**其他 mtouch 引數**。 這會從應用程式移除 `UIWebView` 的追蹤。 不過，參考該類型的任何程式碼都將**無法**正常運作（預期例外狀況或當機）。 只有在您確定程式碼無法在執行時間連線時（即使是透過靜態分析來連線），才應該使用這個方法。

#### <a name="support-for-ios-7x-or-earlier"></a>支援 iOS 7.x （或更早版本）

自 v2.0 以來，`UIWebView` 是 iOS 的一部分。 最常見的替代專案是 `WKWebView` （iOS 8）和 `SFSafariViewController` （iOS 9）。 如果您的應用程式仍然支援舊版的 iOS 版本，您應該考慮下列選項：

* 讓 iOS 8 成為您最小的目標版本（組建時間決策）。
* 只有當應用程式在 iOS 8 + 上執行（執行時間決策）時，才使用 `WKWebView`。

#### <a name="applications-not-submitted-to-apple"></a>未提交至 Apple 的應用程式

如果您的應用程式未提交至 Apple，您應該計畫離開已淘汰的 API，因為它可以在未來的 iOS 版本中移除。 不過，您可以使用自己的時程表來執行這種轉換。

## <a name="related-links"></a>相關連結

- [網站（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/webview)

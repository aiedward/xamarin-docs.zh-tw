---
title: 針對以 Xamarin 建立的 tvOS 10 應用程式進行疑難排解
description: 本文提供數個在 Xamarin 應用程式中使用 tvOS 10 的疑難排解秘訣。 其中描述與 App Store、二進位相容性、CFNetwork HttpProtocol、CloudKit、Core Image、NSUserActivity 和 UIKit 相關的問題。
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a6588dee675aee3e2580b70dfdea2920c6235775
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030607"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>針對以 Xamarin 建立的 tvOS 10 應用程式進行疑難排解

下列各節列出搭配 Xamarin 使用 tvOS 10 時可能發生的一些已知問題，以及這些問題的解決方案：

- [App Store](#App-Store)
- [二進位相容性](#Binary-Compatibility)
- [CFNetwork HTTP 通訊協定](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [核心映射](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

已知問題：

- 在沙箱環境中測試應用程式內購買時，[驗證] 對話方塊可能會出現兩次。
- 在沙箱環境中測試具有託管內容的應用程式內購買時，每次應用程式進入前景後都會出現 [密碼] 對話方塊，直到完成內容下載。

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>二進位相容性

已知問題：

- 呼叫 `NSObject.ValueForKey` 將會產生例外狀況，`null` 金鑰。
- 呼叫 `UIFont.WithName` 時，依名稱參考字型會導致當機。
- 在 `http://` Url 的 TLS 交握期間，`NSURLSession` 和 `NSURLConnection` 不會再有 RC4 加密套件。
- 如果應用程式在 `ViewWillLayoutSubviews` 或 `LayoutSubviews` 方法中修改 superview 頂端的幾何，就會停止回應。
- 針對所有 SSL/TLS 連線，現在預設會停用 RC4 對稱式加密。 此外，安全傳輸 API 已不再支援 SSLv3，建議應用程式儘快停止使用 SHA-1 和3DES 密碼編譯。

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>CFNetwork HTTP 通訊協定

`NSMutableURLRequest` 類別的 `HTTPBodyStream` 屬性必須設定為未開啟的資料流程，因為 `NSURLConnection` 和 `NSURLSession` 現在會嚴格地強制執行這項需求。

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

長時間執行的作業會傳回「_您沒有儲存檔案的許可權」。_ 糾錯.

<a name="CoreImage" />

## <a name="core-image"></a>核心映射

`CIImageProcessor` API 現在支援任意輸入影像計數。 包含在 tvOS 10 Beta 1 中的 `CIImageProcessor` API 將會移除。

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

在遞交作業之後，`NSUserActivity` 物件的 `UserInfo` 屬性可能是空的。 以目前的因應措施明確呼叫 `BecomeCurrent` NSUserActivity ' 物件。

<a name="UIKit" />

## <a name="uikit"></a>UIKit

已知問題：

- `UINavigationBar`、`UITabBar` 或 `UIToolBar` 的背景外觀變更，可能會導致版面配置傳遞來解決新的外觀。 嘗試在 `LayoutSubviews`、`UpdateConstraints`、`WillLayoutSubviews` 或 `DidUpdateSubviews` 事件內修改這些外觀，可能會導致無限的版面配置迴圈。
- 在 tvOS 10 中，呼叫 `UIView` 物件的 `RemoveGestureRecognizer` 方法，會明確地取消所有進行中的手勢辨識器。
- 呈現的視圖控制器現在會影響狀態列的外觀。
- 當子類別化 `UIViewController` 並覆寫 `AwakeFromNib` 方法時，tvOS 10 會要求開發人員呼叫 `base.AwakeFromNib`。
- 具有自訂 `UIView` 子類別的應用程式會在呼叫 `base.LayoutSubviews` 之前覆寫 `LayoutSubviews` 和變更配置，可能會在 tvOS 10 中觸發無限的版面配置迴圈。
- 當指派給 `UIButton` 物件時，方向特定或 flippable 影像資產不會翻轉。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [TvOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)

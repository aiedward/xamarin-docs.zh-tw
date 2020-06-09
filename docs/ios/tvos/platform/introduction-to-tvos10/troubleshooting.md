---
title: 針對以 Xamarin 建立的 tvOS 10 應用程式進行疑難排解
description: 本文提供數個在 Xamarin 應用程式中使用 tvOS 10 的疑難排解秘訣。 其中描述與 App Store、二進位相容性、CFNetwork HttpProtocol、CloudKit、Core Image、NSUserActivity 和 UIKit 相關的問題。
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: cd04450d1429092453e6d8b65278d87b5d52e45e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571619"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>針對以 Xamarin 建立的 tvOS 10 應用程式進行疑難排解

下列各節列出搭配 Xamarin 使用 tvOS 10 時可能發生的一些已知問題，以及這些問題的解決方案：

- [應用程式市集](#App-Store)
- [二進位相容性](#Binary-Compatibility)
- [CFNetwork HTTP 通訊協定](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [核心映射](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store"></a>

## <a name="app-store"></a>App Store

已知問題：

- 在沙箱環境中測試應用程式內購買時，[驗證] 對話方塊可能會出現兩次。
- 在沙箱環境中測試具有託管內容的應用程式內購買時，每次應用程式進入前景後都會出現 [密碼] 對話方塊，直到完成內容下載。

<a name="Binary-Compatibility"></a>

## <a name="binary-compatibility"></a>二進位相容性

已知問題：

- 呼叫 `NSObject.ValueForKey` 將會 `null` 導致例外狀況。
- 呼叫時依名稱參考字型會 `UIFont.WithName` 導致當機。
- 在 `NSURLSession` `NSURLConnection` URL 的 TLS 信號交換期間，和都不會再有 RC4 加密套件 `http://` 。
- 如果應用程式在或方法中修改 superview 頂端的幾何，則會停止回應 `ViewWillLayoutSubviews` `LayoutSubviews` 。
- 針對所有 SSL/TLS 連線，現在預設會停用 RC4 對稱式加密。 此外，安全傳輸 API 已不再支援 SSLv3，建議應用程式儘快停止使用 SHA-1 和3DES 密碼編譯。

<a name="CFNetwork-HTTP-Protocol"></a>

## <a name="cfnetwork-http-protocol"></a>CFNetwork HTTP 通訊協定

`HTTPBodyStream`類別的屬性 `NSMutableURLRequest` 必須設定為未開啟的資料流程，因為 `NSURLConnection` 和現在會嚴格地 `NSURLSession` 強制執行這項需求。

<a name="CloudKit"></a>

## <a name="cloudkit"></a>CloudKit

長時間執行的作業會傳回「_您沒有儲存檔案的許可權」。_ 的錯誤。

<a name="CoreImage"></a>

## <a name="core-image"></a>核心映射

`CIImageProcessor`API 現在支援任意輸入影像計數。 `CIImageProcessor`包含在 tvOS 10 Beta 1 中的 API 將會移除。

<a name="NSUserActivity"></a>

## <a name="nsuseractivity"></a>NSUserActivity

在遞交作業之後， `UserInfo` 物件的屬性 `NSUserActivity` 可能是空的。 `BecomeCurrent`以目前的因應措施明確呼叫 NSUserActivity 的物件。

<a name="UIKit"></a>

## <a name="uikit"></a>UIKit

已知問題：

- 的背景外觀變更 `UINavigationBar` ， `UITabBar` 或 `UIToolBar` 可能會導致版面配置傳遞以解決新的外觀。 嘗試在、或事件內修改這些 `LayoutSubviews` 外觀 `UpdateConstraints` ， `WillLayoutSubviews` `DidUpdateSubviews` 可能會產生無限的版面配置迴圈。
- 在 tvOS 10 中，呼叫 `RemoveGestureRecognizer` 物件的方法會 `UIView` 明確地取消任何進行中的手勢辨識器。
- 呈現的視圖控制器現在會影響狀態列的外觀。
- tvOS 10 需要開發人員在子類別化 `base.AwakeFromNib` `UIViewController` 和覆寫 `AwakeFromNib` 方法時呼叫。
- 具有自訂子類別的應用程式在 `UIView` 呼叫之前覆寫並變更配置， `LayoutSubviews` `base.LayoutSubviews` 可能會觸發 tvOS 10 中的無限版面配置迴圈。
- 當指派給物件時，方向特定或 flippable 影像資產不會翻轉 `UIButton` 。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [TvOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)

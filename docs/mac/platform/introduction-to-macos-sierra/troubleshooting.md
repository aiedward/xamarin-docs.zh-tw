---
title: Xamarin. Mac-macOS Sierra 疑難排解
description: 本檔提供數個在 Xamarin. Mac 應用程式中使用 macOS Sierra 的疑難排解秘訣。 與 Mac App Store、Apple Pay、二進位相容性、CFNetwork、CloudKit 等相關的秘訣。
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/22/2016
ms.openlocfilehash: e7bc6fa12ab6720842ab264678cbf8124353fc40
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574414"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin. Mac-macOS Sierra 疑難排解

_本文提供數個在 Xamarin. Mac 應用程式中使用 macOS Sierra 的疑難排解秘訣。_

下列各節列出使用 macOS Sierra 搭配 Xamarin 和解決這些問題的解決方案時，可能會發生的一些已知問題：

- [應用程式市集](#App-Store)
- [Apple Pay](#Apple-Pay)
- [二進位相容性](#Binary-Compatibility)
- [CFNetwork HTTP 通訊協定](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [核心映射](#CoreImage)
- [通知](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store"></a>

## <a name="app-store"></a>App Store

已知問題：

- 在沙箱環境中測試應用程式內購買時，[驗證] 對話方塊可能會出現兩次。
- 在沙箱環境中測試具有託管內容的應用程式內購買時，每次應用程式進入前景後都會出現 [密碼] 對話方塊，直到完成內容下載。

<a name="Apple-Pay"></a>

## <a name="apple-pay"></a>Apple Pay

將新的付款卡新增至 Apple Pay 時，如果輸入了不正確的到期日或安全性代碼（CW），卡片布建程式將會終止。

<a name="Binary-Compatibility"></a>

## <a name="binary-compatibility"></a>二進位相容性

已知問題：

- 呼叫 `NSObject.ValueForKey` 將會 `null` 導致例外狀況。
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

`CIImageProcessor`API 現在支援任意輸入影像計數。 `CIImageProcessor`MacOS Sierra Beta 1 中所包含的 API 將會移除。

<a name="Notifications"></a>

## <a name="notifications"></a>通知

使用通知內容延伸模組時，不會正確地釋放 View controller，而且當達到延伸模組記憶體限制時，可能會導致損毀。

<a name="NSUserActivity"></a>

## <a name="nsuseractivity"></a>NSUserActivity

在遞交作業之後， `UserInfo` 物件的屬性 `NSUserActivity` 可能是空的。 `BecomeCurrent` `NSUserActivity` 以目前的因應措施明確呼叫物件。

<a name="Safari"></a>

## <a name="safari"></a>Safari

WebGeolocation 需要安全（ `https://` ） URL，才能在 iOS 10 和 macOS Sierra 上工作，以避免惡意使用位置資料。

## <a name="related-links"></a>相關連結

- [Mac 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [MacOS 10.12 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)

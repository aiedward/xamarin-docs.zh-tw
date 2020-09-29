---
title: Xamarin-macOS Sierra 疑難排解
description: 本檔提供在 Xamarin 應用程式中使用 macOS Sierra 的一些疑難排解秘訣。 與 Mac App Store、Apple Pay、二進位相容性、CFNetwork、CloudKit 等相關的秘訣。
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/22/2016
ms.openlocfilehash: d3ec300cbda5d22a285c3f63d3281bde29b94024
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430050"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin-macOS Sierra 疑難排解

_本文提供在 Xamarin 應用程式中使用 macOS Sierra 的幾個疑難排解秘訣。_

下列各節將列出搭配 Xamarin 使用 macOS Sierra 時可能發生的一些已知問題，以及這些問題的解決方案：

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
- 在沙箱環境中測試內含託管內容的應用程式內購買時，每次應用程式進入前景時都會出現 [密碼] 對話方塊，直到下載內容完成為止。

<a name="Apple-Pay"></a>

## <a name="apple-pay"></a>Apple Pay

若將新的付款卡新增到 Apple Pay 時，如果輸入的到期日或安全性代碼不正確 (CW) ，將會終止卡片布建程式。

<a name="Binary-Compatibility"></a>

## <a name="binary-compatibility"></a>二進位相容性

已知問題：

- 呼叫 `NSObject.ValueForKey` 將會 `null` 導致例外狀況。
- `NSURLSession`和在 `NSURLConnection` URL 的 TLS 信號交換期間，都不會再有 RC4 加密套件 `http://` 。
- 如果應用程式在或方法中修改了超級檢視表的幾何，則可能會停止回應 `ViewWillLayoutSubviews` `LayoutSubviews` 。
- 針對所有的 SSL/TLS 連線，現在預設會停用 RC4 對稱式加密。 此外，安全傳輸 API 已不再支援 SSLv3，建議應用程式儘快停止使用 SHA-1 和3DES 密碼編譯。

<a name="CFNetwork-HTTP-Protocol"></a>

## <a name="cfnetwork-http-protocol"></a>CFNetwork HTTP 通訊協定

`HTTPBodyStream`類別的屬性 `NSMutableURLRequest` 必須設定為未開啟的資料流程 `NSURLConnection` ，但現在會 `NSURLSession` 嚴格強制執行這項需求。

<a name="CloudKit"></a>

## <a name="cloudkit"></a>CloudKit

長時間執行的作業會傳回「 _您沒有儲存檔案的許可權」。_ 錯誤內容。

<a name="CoreImage"></a>

## <a name="core-image"></a>核心映射

`CIImageProcessor`API 現在支援任意輸入映射計數。 `CIImageProcessor` MacOS Sierra Beta 1 中所包含的 API 將會移除。

<a name="Notifications"></a>

## <a name="notifications"></a>通知

使用通知內容延伸模組時，視圖控制器未正確釋出，而且可能會在達到延伸模組記憶體限制時導致損毀。

<a name="NSUserActivity"></a>

## <a name="nsuseractivity"></a>NSUserActivity

在提交作業之後， `UserInfo` 物件的屬性 `NSUserActivity` 可能是空的。 明確地呼叫 `BecomeCurrent` `NSUserActivity` 物件作為目前的因應措施。

<a name="Safari"></a>

## <a name="safari"></a>Safari

WebGeolocation 需要安全 (`https://`) URL，才能在 iOS 10 和 macOS Sierra 上運作，以防止惡意使用位置資料。

## <a name="related-links"></a>相關連結

- [Mac 範例](/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [MacOS 10.12 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
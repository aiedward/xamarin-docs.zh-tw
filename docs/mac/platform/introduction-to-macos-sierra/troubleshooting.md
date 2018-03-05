---
title: "疑難排解"
description: "這篇文章提供數個疑難排解秘訣 macOS 利也 Xamarin.Mac 應用程式中使用。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/22/2016
ms.openlocfilehash: 739cffb2b5418e4fc52bd91f97f4123b01abf0c7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting"></a>疑難排解

_這篇文章提供數個疑難排解秘訣 macOS 利也 Xamarin.Mac 應用程式中使用。_

他下列各節列出一些使用 macOS 利也 Xamarin.mac 與這些問題的解決方案時可能發生的已知的問題：

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [二進位碼相容性](#Binary-Compatibility)
- [CFNetwork HTTP Protocol](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [CoreImage](#CoreImage)
- [通知](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>應用程式市集

已知問題：

- 當沙箱環境中測試應用程式內購買項目，[驗證] 對話方塊可能會出現兩次。
- 在測試應用程式內購買與沙箱環境中裝載的內容時，每次應用程式移到前景內容的下載作業完成之前，會出現 [密碼] 對話方塊。

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

如果加入新的付款卡片 Apple Pay 時輸入不正確的到期日期或安全碼 (CW)，則智慧卡的佈建程序將會終止。

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>二進位碼相容性

已知問題：

- 呼叫`NSObject.ValueForKey`將`null`金鑰會導致例外狀況。
- 同時`NSURLSession`和 NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http://' Url。
- 如果它們就會修改在超級檢視表的幾何，應用程式可以突出`ViewWillLayoutSubviews`或`LayoutSubviews`方法。
- 對於所有 SSL/TLS 連線，RC4 對稱式加密現在預設會停用。 此外，安全傳輸 API 不再支援 SSLv3，建議應用程式停止儘速使用 sha-1 和 3DES 加密。

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>CFNetwork HTTP 通訊協定

`HTTPBodyStream`屬性`NSMutableURLRequest`類別必須設定為自未開啟資料流`NSURLConnection`和`NSURLSession`現在會嚴格強制執行這項需求。

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

長時間執行的作業會傳回_「 您沒有儲存檔案的權限 」。_ 發生錯誤。

<a name="CoreImage" />

## <a name="coreimage"></a>CoreImage

`CIImageProcessor` API 現在支援任意輸入的影像計數。 `CIImageProcessor` 將移除 macOS 利也 beta 1 中所包含的 API。

<a name="Notifications" />

## <a name="notifications"></a>通知

使用時通知內容的擴充功能，檢視控制站不會正確地釋放，並達到擴充記憶體限制時，可能會導致損毀。

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

遞交作業之後，`UserInfo`屬性`NSUserActivity`物件可能會是空白。 明確地呼叫`BecomeCurrent``NSUserActivity`物件做為目前的因應措施。

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation 需要透過安全 (`https://`) 來處理 iOS 10 和 macOS 利也以防止惡意使用位置資料的 URL。







## <a name="related-links"></a>相關連結

- [Mac 範例](https://developer.xamarin.com/samples/mac/)
- [OS X 10.12 中最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)

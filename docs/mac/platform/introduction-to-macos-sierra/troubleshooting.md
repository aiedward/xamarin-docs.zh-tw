---
title: Xamarin.Mac-macOS Sierra 疑難排解
description: 本文件提供用於具有 macOS Sierra Xamarin.Mac 應用程式中的數個疑難排解的秘訣。 秘訣與 Mac App Store、 Apple Pay，二進位碼相容性、 CFNetwork、 CloudKit，和更多功能。
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 09/22/2016
ms.openlocfilehash: 322acff3279d0513266c7d9883726cac726334f7
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830547"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin.Mac-macOS Sierra 疑難排解

_這篇文章會提供數個疑難排解秘訣，用於具有 macOS Sierra Xamarin.Mac 應用程式中。_

下列各節列出一些使用 macOS Sierra Xamarin.mac 和這些問題的解決方案時可能發生的已知的問題：

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [二進位碼相容性](#Binary-Compatibility)
- [CFNetwork HTTP 通訊協定](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Core 映像](#CoreImage)
- [通知](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>App Store

已知問題：

- 在測試應用程式內購買的沙箱環境中時，[驗證] 對話方塊可能會出現兩次。
- 當裝載的內容，在沙箱環境中測試應用程式內購買項目，每次應用程式移到前景內容下載完成之前，會出現 [密碼] 對話方塊。

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

如果將新的付款卡片新增至 Apple Pay 時輸入不正確的到期日期或安全碼 (CW)，則佈建程序卡片將會終止。

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>二進位碼相容性

已知問題：

- 呼叫`NSObject.ValueForKey`將`null`金鑰將會導致例外狀況。
- 兩者`NSURLSession`並`NSURLConnection`不再 RC4 加密套件的 TLS 信號交換期間`http://`Url。
- 應用程式可以停止回應，當修改中的超級檢視表的幾何`ViewWillLayoutSubviews`或`LayoutSubviews`方法。
- 對於所有 SSL/TLS 連線，RC4 對稱編碼器現在預設會停用。 此外，安全傳輸 API 不再支援 SSLv3 與建議的應用程式停止儘速使用 sha-1 和 3DES 加密。

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>CFNetwork HTTP 通訊協定

`HTTPBodyStream`的屬性`NSMutableURLRequest`類別必須設定為自未開啟資料流`NSURLConnection`和`NSURLSession`現在會嚴格強制執行這項需求。

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

長時間執行的作業會傳回 _「 您沒有儲存檔案的權限 」。_ 發生錯誤。

<a name="CoreImage" />

## <a name="core-image"></a>Core 映像

`CIImageProcessor` API 現可支援任意的輸入的影像計數。 `CIImageProcessor` 在 macOS Sierra beta 1 中所包含的 API 將會移除。

<a name="Notifications" />

## <a name="notifications"></a>通知

使用時通知內容延伸模組，檢視控制器不會正確地發行，並到達延伸模組的記憶體限制時，可能會導致當機。

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

遞移式作業之後，`UserInfo`屬性`NSUserActivity`可能是空的物件。 明確呼叫`BecomeCurrent``NSUserActivity`物件做為目前的因應措施。

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation 需要安全 (`https://`) 才能在 iOS 10 和 macOS Sierra 來防止惡意使用位置資料的 URL。







## <a name="related-links"></a>相關連結

- [Mac 範例](https://developer.xamarin.com/samples/mac/)
- [在 OS X 10.12 最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)

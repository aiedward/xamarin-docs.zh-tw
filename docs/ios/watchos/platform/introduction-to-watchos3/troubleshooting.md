---
title: watchOS 3 疑難排解
description: 本檔提供在 Xamarin 中使用 watchOS 3 時，有幾個有用的疑難排解秘訣。 秘訣與活動、Apple Pay、背景重新整理、NSURLConnection、隱私權等相關。
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 2a55991dacd6c35a2fe90cf90ad6f0e2208ff71f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430462"
---
# <a name="watchos-3-troubleshooting"></a>watchOS 3 疑難排解

_本文提供在 Xamarin Apple Watch apps 中使用 watchOS 3 的幾個疑難排解秘訣。_

此頁面會列出使用 watchOS 3 搭配 Xamarin 時可能發生的一些已知問題，以及這些問題的解決方案。

## <a name="activities"></a>活動

為了讓活動共用正常運作，所有配對的 Apple watch 都必須執行 watchOS 3。

已知問題：

- 回復活動共用通知有時會失敗。
- 回復具有訊息的活動共用通知可能會失敗。
- 活動共用通知訊息上方的內容文字將會不正確。

## <a name="apple-pay"></a>Apple Pay

已知問題：

- 如果在 Apple Pay 中輸入了不正確的到期日或 CW 代碼以進行新的付款，則當 **下一次** 執行中的進程時，就會當機。
- 需要 PIN 碼的應用程式內 Apple Pay 購買可能會損毀。

## <a name="auto-mac-unlock"></a>自動 Mac 解除鎖定

藉由使用 watchOS 3 Beta 2 (或更高的) 和 macOS Sierra Beta 2 (或更高的) ，如果在使用者的 iCloud 帳戶上啟用雙因素驗證，他們就可以使用他們的 Apple Watch 自動解除鎖定其 Mac。

## <a name="background-refresh"></a>背景重新整理

違反系統資源會導致 watchOS 3 應用程式當機，並出現下列例外狀況代碼：

- **0xc51bad01** -應用程式耗用太多 CPU 時間。
- **0xc51bad02** -應用程式耗用太多時間。
- **0xc51bad03** -應用程式的執行時間不足，無法完成目前的工作。

## <a name="clock"></a>Clock

新安裝 Apple Watch 應用程式的複雜可能會顯示為空白。 重新開機 Apple Watch 以修正此問題。

## <a name="connectivity"></a>連線能力

已知問題：

- watchOS 不會提示使用者提供 Apple Watch 上受保護使用者資料的存取權限。 使用 watch 應用程式中的資料之前，請先授與 iPhone 應用程式的存取權。
- Apple Watch 可以輸入所有 WatchConnectivity 傳輸失敗的狀態，然後重新開機 Apple Watch 以修正。

## <a name="notifications"></a>通知

如果媒體附件太大，則會顯示在使用者的 iPhone 上，但不會顯示 Apple Watch。

## <a name="nsurlconnection"></a>NSURLConnection

`NSURLConnection`使用舊版 TLS 通訊協定的任何連線將會失敗。 針對所有的 SSL/TLS 連線，現在預設會停用 RC4 對稱式加密。 此外，安全傳輸 API 已不再支援 SSLv3，建議應用程式儘快停止使用 SHA-1 和3DES 密碼編譯。

從 watchOS 3 起，Apple 會嚴格強制執行 SSL/TLS 連接安全性。 受影響的服務和應用程式應該更新 web 伺服器，以使用最新的 TLS 通訊協定版本。

## <a name="nsurlsession"></a>NSURLSession

從 watchOS 3 `HTTPBodyStream` 開始，類別的屬性 `NSMutableURLRequest` 必須設定為未開啟的資料流程， `NSURLConnection` 但現在會 `NSURLSession` 嚴格強制執行這項需求。

## <a name="privacy"></a>隱私權

已知問題：

使用 url 時 `https://` `NSURLSession` ，不會 `NSURLConnection` 再于 TLS 信號交換期間支援 RC4 加密套件。 可能會產生下列其中一個錯誤碼：

- **-1200 或-98** -用於 `NSURLErrorSecurityConnectionFailed` 和 SecureTransport 錯誤。
- **-1200 [3:-9824]** -Http 載入失敗。
- **-1200**  -  `NSURLConnection`已完成，但發生錯誤。

從 watchOS 3 起，Apple 會嚴格強制執行 SSL/TLS 連接安全性。 受影響的服務和應用程式應該更新 web 伺服器，以使用最新的 TLS 通訊協定版本。 如需詳細資訊，請參閱上述 [NSURLConnection](#nsurlconnection) 。

## <a name="snapshots"></a>快照集

未採用新 API 的 WatchKit 應用程式 `HandelBackgroundTask` 將不會再收到 watchOS 3 的定期更新。 

## <a name="watchkit"></a>WatchKit

當應用程式在 watchOS Dock 中輸入背景時，SpriteKit 和 SceneKit 場景將會暫停。

## <a name="related-links"></a>相關連結

- [watchOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchOS)
- [WatchOS 3 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
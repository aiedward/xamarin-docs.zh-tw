---
title: watchOS 3 疑難排解
description: WatchOS 3 在 Xamarin 中使用時，這份文件會提供數個疑難排解提示很有用。 秘訣與相關活動、 Apple Pay，背景重新整理、 NSURLConnection、 隱私權和更多功能。
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 6d2aaf12bd6c45f6268cf87a77d2ee03a9d7a888
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677725"
---
# <a name="watchos-3-troubleshooting"></a>watchOS 3 疑難排解

_這篇文章會提供數個疑難排解秘訣，watchOS 3 Xamarin Apple Watch 應用程式中使用。_

此頁面會列出一些使用 watchOS 3 使用 Xamarin 和這些問題的解決方案時可能發生的已知的問題。

## <a name="activities"></a>活動

對於共用才能正常運作的活動，所有配對的 Apple Watch 必須執行 watchOS 3。

已知問題：

- 信賴憑證者到活動共用通知有時會失敗。
- 回覆訊息活動共用通知可能會失敗。
- 上述活動共用通知訊息的內容文字會不正確。

## <a name="apple-pay"></a>Apple Pay

已知問題：

- 如果不正確的到期日或 CW 程式碼輸入新付款心力在 Apple Pay，當達到**下一步**執行程序將會損毀。
- 需要 PIN 碼中的應用程式的 Apple Pay 的購買項目可能會損毀。

## <a name="auto-mac-unlock"></a>自動 Mac 解除鎖定

WatchOS 3 beta 2 （或以上） 及 macOS Sierra beta 2 （或更新版本），如果使用者的 iCloud 帳戶啟用雙因素驗證，他們可以透過使用其 Apple Watch 自動解除鎖定其 mac。

## <a name="background-refresh"></a>背景重新整理

違反系統資源會產生下列例外狀況代碼在 watchOS 3 應用程式當機：

- **0xc51bad01** -應用程式耗用太多 CPU 時間。
- **0xc51bad02** -應用程式耗用太多時間。
- **0xc51bad03** -應用程式沒有足夠的執行階段來完成目前的工作。

## <a name="clock"></a>時鐘

從新安裝 Apple Watch 應用程式的複雜性可能會顯示為空白。 重新開機以修正此問題的 Apple Watch。

## <a name="connectivity"></a>連線能力

已知問題：

- watchOS 不會提示使用者輸入 Apple Watch 上的受保護的使用者資料的存取權限。 在 iPhone 應用程式，才能使用監看式應用程式中的資料上授與存取權。
- Apple Watch 可以進入其中所有 WatchConnectivity 傳輸都失敗的狀態重新啟動 Apple Watch，若要修正問題。

## <a name="notifications"></a>通知

如果媒體附件太大，它會在使用者的 iPhone 但不是其 Apple Watch 上看到。

## <a name="nsurlconnection"></a>NSURLConnection

任何`NSURLConnection`使用較舊 TLS 通訊協定的連接將會失敗。 對於所有 SSL/TLS 連線，RC4 對稱編碼器現在預設會停用。 此外，安全傳輸 API 不再支援 SSLv3 與建議的應用程式停止儘速使用 sha-1 和 3DES 加密。

WatchOS 3，截至 SSL/TLS 連線安全性已嚴格強制執行由 Apple。 受影響的服務和應用程式應該已更新 web 伺服器，以使用最新的 TLS 通訊協定版本。

## <a name="nsurlsession"></a>NSURLSession

WatchOS 3，截至`HTTPBodyStream`的屬性`NSMutableURLRequest`類別必須設定為自未開啟資料流`NSURLConnection`和`NSURLSession`現在會嚴格強制執行這項需求。

## <a name="privacy"></a>隱私權

已知問題：

使用時`https://`Url，這兩個`NSURLSession`和`NSURLConnection`不再支援 RC4 加密套件，在 TLS 信號交換期間。 其中一個下列的錯誤代碼可能會產生：

- **-1200年或-98** -作為`NSURLErrorSecurityConnectionFailed`和 SecureTransport 錯誤。
- **[3:-9824]-1200** -http 載入失敗。
- **-1200**  -  `NSURLConnection`完成時發生錯誤。

WatchOS 3，截至 SSL/TLS 連線安全性已嚴格強制執行由 Apple。 受影響的服務和應用程式應該已更新 web 伺服器，以使用最新的 TLS 通訊協定版本。 請參閱[NSURLConnection](#nsurlconnection)上方如需詳細資訊。

## <a name="snapshots"></a>快照

WatchKit 應用程式，皆不會採用新`HandelBackgroundTask`API 不會再收到 watchOS 3 中的定期更新。 

## <a name="watchkit"></a>WatchKit

應用程式在 watchOS 停駐進入背景時，將會暫停 SpriteKit 和 SceneKit 場景。

## <a name="related-links"></a>相關連結

- [watchOS 範例](https://developer.xamarin.com/samples/watchos/all/)
- [WatchOS 3 中最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)

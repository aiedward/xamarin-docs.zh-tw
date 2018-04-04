---
title: watchOS 3 疑難排解
description: 這篇文章提供數個疑難排解秘訣 watchOS 3 Xamarin Apple Watch 應用程式中使用。
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 159c6a6dadcaa325abc7fd747abc9b2ba2f26a9c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="watchos-3-troubleshooting"></a>watchOS 3 疑難排解

_這篇文章提供數個疑難排解秘訣 watchOS 3 Xamarin Apple Watch 應用程式中使用。_

此頁面會列出可以在使用 Xamarin 和這些問題的解決方案使用 watchOS 3 時可能發生的一些已知的問題。

## <a name="activities"></a>活動

活動共用正確地運作，所有配對的 Apple 監看式必須執行 watchOS 3。

已知問題：

- 有時回覆活動共用通知將會失敗。
- 回覆訊息的活動共用通知可能會失敗。
- 上方活動共用通知訊息內容的文字會是不正確。


## <a name="apple-pay"></a>Apple Pay

已知問題：

- 如果不正確的到期日或 CW 程式碼輸入為新的付款小心，在 Apple Pay 時叫用,**下一步**執行的處理序將會損毀。
- 需要 PIN 碼的應用程式內 Apple Pay 購買可能會損毀。



## <a name="auto-mac-unlock"></a>自動 Mac 解除鎖定

使用 watchOS 3 beta 2 （或以上） 及 macOS 利也 beta 2 （或更高），如果使用者的 iCloud 帳戶啟用雙因素驗證之後，他們可以使用其 Apple Watch 來自動解除鎖定其 mac。



## <a name="background-refresh"></a>背景重新整理

違反系統資源，將會產生與下列例外狀況代碼 watchOS 3 應用程式損毀：

- **0xc51bad01** -應用程式耗用太多 CPU 時間。
- **0xc51bad02** -應用程式耗用太多時間。
- **0xc51bad03** -應用程式沒有足夠的執行階段來完成目前的工作。



## <a name="clock"></a>時鐘

從新安裝 Apple Watch 應用程式的複雜性可能顯示為空白。 重新開機以修正此問題的 Apple Watch。


## <a name="connectivity"></a>連線能力

已知問題：

- watchOS 不會提示使用者輸入 Apple Watch 上的受保護的使用者資料的存取權限。 在 iPhone 應用程式，才能監看式應用程式中使用資料上授與存取權。
- Apple Watch 進入的狀態，其中所有 WatchConnectivity 傳輸都失敗時，若要修正 Apple Watch 重新開機。


## <a name="notifications"></a>通知

如果媒體附件太大，它將會出現在使用者的 iPhone 但不是其 Apple Watch 上。


## <a name="nsurlconnection"></a>NSURLConnection

任何`NSURLConnection`使用舊的 TLS 通訊協定的連接將會失敗。 對於所有 SSL/TLS 連線，RC4 對稱式加密現在預設會停用。 此外，安全傳輸 API 不再支援 SSLv3，建議應用程式停止儘速使用 sha-1 和 3DES 加密。

為準，watchOS 3，SSL/TLS 連線安全性會嚴格強制執行由 Apple。 受影響的服務和應用程式應該更新網頁伺服器，以便使用最新的 TLS 通訊協定版本。


## <a name="nsurlsession"></a>NSURLSession

為準，3，watchOS`HTTPBodyStream`屬性`NSMutableURLRequest`類別必須設定為自未開啟資料流`NSURLConnection`和`NSURLSession`現在會嚴格強制執行這項需求。


## <a name="privacy"></a>隱私權

已知問題：

當使用`https://`這兩個 Url`NSURLSession`和`NSURLConnection`不再支援 RC4 加密套件 TLS 交握期間。 可能會產生下列錯誤碼的其中一個：

- **-1200年或-98** -如果要讓`NSURLErrorSecurityConnectionFailed`和 SecureTransport 錯誤。
- **[3:-9824]-1200** -http 下載/載入失敗。
- **-1200**  -  `NSURLConnection`完成發生錯誤。

為準，watchOS 3，SSL/TLS 連線安全性會嚴格強制執行由 Apple。 受影響的服務和應用程式應該更新網頁伺服器，以便使用最新的 TLS 通訊協定版本。 請參閱[NSURLConnection](#NSURLConnection)上方如需詳細資訊。


## <a name="snapshots"></a>快照

WatchKit 不採用新的應用程式`HandelBackgroundTask`應用程式開發介面不會再收到 watchOS 3 中的定期更新。 


## <a name="watchkit"></a>WatchKit

應用程式進入 watchOS 停駐在背景時，會暫停 SpriteKit 和 SceneKit 場景。


## <a name="related-links"></a>相關連結

- [watchOS 範例](https://developer.xamarin.com/samples/watchos/all/)
- [什麼是 watchOS 3 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)

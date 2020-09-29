---
title: 針對以 Xamarin 建立的 tvOS 10 應用程式進行疑難排解
description: 本文提供在 Xamarin 應用程式中使用 tvOS 10 的一些疑難排解秘訣。 它描述與 App Store、二進位相容性、CFNetwork HttpProtocol、CloudKit、Core Image、NSUserActivity 和 UIKit 相關的問題。
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 65a76c0196b79a17f935f59902c8e6d2f9f25933
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434916"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>針對以 Xamarin 建立的 tvOS 10 應用程式進行疑難排解

下列各節列出搭配 Xamarin 使用 tvOS 10 時可能會發生的一些已知問題，以及這些問題的解決方案：

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
- 在沙箱環境中測試內含託管內容的應用程式內購買時，每次應用程式進入前景時都會出現 [密碼] 對話方塊，直到下載內容完成為止。

<a name="Binary-Compatibility"></a>

## <a name="binary-compatibility"></a>二進位相容性

已知問題：

- 呼叫 `NSObject.ValueForKey` 將會 `null` 導致例外狀況。
- 呼叫時依名稱參考字型 `UIFont.WithName` 會造成損毀。
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

`CIImageProcessor`API 現在支援任意輸入映射計數。 `CIImageProcessor` TvOS 10 Beta 1 中所包含的 API 將會移除。

<a name="NSUserActivity"></a>

## <a name="nsuseractivity"></a>NSUserActivity

在提交作業之後， `UserInfo` 物件的屬性 `NSUserActivity` 可能是空的。 明確地呼叫 `BecomeCurrent` NSUserActivity 的物件作為目前的因應措施。

<a name="UIKit"></a>

## <a name="uikit"></a>UIKit

已知問題：

- 的背景外觀變更 `UINavigationBar` `UITabBar` 或 `UIToolBar` 可能會導致版面配置傳遞，以解決新的外觀。 嘗試修改、或事件內的這些 `LayoutSubviews` 外觀 `UpdateConstraints` `WillLayoutSubviews` `DidUpdateSubviews` 可能會產生無限的版面配置迴圈。
- 在 tvOS 10 中，呼叫 `RemoveGestureRecognizer` 物件的方法會 `UIView` 明確取消任何進行中的手勢辨識器。
- 顯示的視圖控制器現在可以影響狀態列的外觀。
- tvOS 10 要求開發人員在子類別化 `base.AwakeFromNib` `UIViewController` 和覆寫 `AwakeFromNib` 方法時呼叫。
- 具有自訂子類別的應用程式在 `UIView` 呼叫之前覆寫並變更配置， `LayoutSubviews` `base.LayoutSubviews` 可能會在 tvOS 10 中觸發無限的版面配置迴圈。
- 特定方向或 flippable 影像資產在指派給物件時，不會進行任何翻轉 `UIButton` 。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [TvOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
---
title: 針對以 Xamarin 建立的 tvOS 10 應用程式進行疑難排解
description: 本文提供數個在 Xamarin 應用程式中使用 tvOS 10 的疑難排解秘訣。 其中描述與 App Store、二進位相容性、CFNetwork HttpProtocol、CloudKit、Core Image、NSUserActivity 和 UIKit 相關的問題。
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: c734bfdd1baaf89cf25d687657e5f14bdbc7834c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283446"
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

- `NSObject.ValueForKey` 呼叫`null`將會導致例外狀況。
- 呼叫`UIFont.WithName`時依名稱參考字型會導致當機。
- 在`NSURLSession` url `NSURLConnection`的 TLS 信號交換期間，和都不會`http://`再有 RC4 加密套件。
- 如果應用程式在`ViewWillLayoutSubviews`或`LayoutSubviews`方法中修改 superview 頂端的幾何，則會停止回應。
- 針對所有 SSL/TLS 連線，現在預設會停用 RC4 對稱式加密。 此外，安全傳輸 API 已不再支援 SSLv3，建議應用程式儘快停止使用 SHA-1 和3DES 密碼編譯。

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>CFNetwork HTTP 通訊協定

`NSMutableURLRequest` `NSURLConnection`類別的`NSURLSession`屬性必須設定為未開啟的資料流程，因為和現在會嚴格地強制執行這項需求。 `HTTPBodyStream`

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

長時間執行的作業會傳回「_您沒有儲存檔案的許可權」。_ 糾錯.

<a name="CoreImage" />

## <a name="core-image"></a>核心映射

`CIImageProcessor` API 現在支援任意輸入影像計數。 `CIImageProcessor`包含在 tvOS 10 Beta 1 中的 API 將會移除。

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

在遞交作業之後， `UserInfo` `NSUserActivity`物件的屬性可能是空的。 以目前`BecomeCurrent`的因應措施明確呼叫 NSUserActivity 的物件。

<a name="UIKit" />

## <a name="uikit"></a>UIKit

已知問題：

- 的背景外觀`UINavigationBar`變更， `UITabBar`或`UIToolBar`可能會導致版面配置傳遞以解決新的外觀。 `LayoutSubviews`嘗試在`UpdateConstraints` 、或`DidUpdateSubviews`事件內修改這些外觀，可能會產生無限的版面配置迴圈。 `WillLayoutSubviews`
- 在 tvOS 10 中，呼叫`RemoveGestureRecognizer` `UIView`物件的方法會明確地取消任何進行中的手勢辨識器。
- 呈現的視圖控制器現在會影響狀態列的外觀。
- tvOS 10 需要開發人員在子`base.AwakeFromNib`類別化`UIViewController`和覆寫`AwakeFromNib`方法時呼叫。
- 具有自訂`UIView`子類別的`LayoutSubviews`應用程式在呼叫`base.LayoutSubviews`之前覆寫並變更配置，可能會觸發 tvOS 10 中的無限版面配置迴圈。
- 當指派`UIButton`給物件時，方向特定或 flippable 影像資產不會翻轉。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [TvOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)

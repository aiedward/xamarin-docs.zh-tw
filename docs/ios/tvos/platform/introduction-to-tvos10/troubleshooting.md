---
title: 疑難排解 tvOS 10 使用 Xamarin 建置的應用程式
description: 這篇文章會提供數個疑難排解秘訣，tvOS 10 Xamarin 應用程式中使用。 它會描述應用程式市集，二進位碼相容性、 CFNetwork HttpProtocol、 CloudKit、 Core 映像、 NSUserActivity 和 UIKit 相關問題。
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3815790cfb73f93f399c14d3da44aa3210725388
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932434"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>疑難排解 tvOS 10 使用 Xamarin 建置的應用程式

下列各節列出一些使用 tvOS 10 使用 Xamarin 和這些問題的解決方案時可能發生的已知的問題：

- [App Store](#App-Store)
- [二進位碼相容性](#Binary-Compatibility)
- [CFNetwork HTTP 通訊協定](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Core 映像](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

已知問題：

 - 在測試應用程式內購買的沙箱環境中時，[驗證] 對話方塊可能會出現兩次。
 - 當裝載的內容，在沙箱環境中測試應用程式內購買項目，每次應用程式移到前景內容下載完成之前，會出現 [密碼] 對話方塊。

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>二進位碼相容性

已知問題：

 - 呼叫`NSObject.ValueForKey`將`null`金鑰將會導致例外狀況。
 - 呼叫時，依名稱參考字型`UIFont.WithName`會造成當機。
 - 兩者`NSURLSession`和 NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http://' Url。
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

`CIImageProcessor` API 現可支援任意的輸入的影像計數。 `CIImageProcessor` TvOS 10 beta 1 中所包含的 API 將會移除。

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

遞移式作業之後，`UserInfo`屬性`NSUserActivity`可能是空的物件。 明確地呼叫`BecomeCurrent`NSUserActivity' 物件做為目前的因應措施。

<a name="UIKit" />

## <a name="uikit"></a>UIKit

已知問題：

 - 變更為背景的外觀`UINavigationBar`，`UITabBar`或`UIToolBar`可能會導致配置傳遞解決新的外觀。 嘗試修改內的這些外觀`LayoutSubviews`， `UpdateConstraints`，`WillLayoutSubviews`或`DidUpdateSubviews`事件可能會導致無限的版面配置迴圈。
 - TvOS 10，呼叫在`RemoveGestureRecognizer`方法的`UIView`物件明確取消任何進行中的筆勢辨識器。
 - 呈現的檢視控制器現在可能會影響 [狀態] 列的外觀。
 - tvOS 10 會要求開發人員呼叫`base.AwakeFromNib`子類別化時`UIViewController`並覆寫`AwakeFromNib`方法。
 - 使用自訂的應用程式`UIView`覆寫的子類別`LayoutSubviews`應急的版面配置，然後再呼叫`base.LayoutSubviews`可能會觸發無限的版面配置中迴圈 tvOS 10。
 - 不開關，當指派的方向特定或 flippable 映像資產`UIButton`物件。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [TvOS 10 中最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)

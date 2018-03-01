---
title: "某些特定的授權錯誤"
ms.topic: article
ms.prod: xamarin
ms.assetid: D26BDF2D-923B-4BC1-841A-74583155DB71
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 80006ce594db5baef5d295537f198181fe0b0fe1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="some-specific-licensing-errors"></a>某些特定的授權錯誤

> [!IMPORTANT]
> 下列資訊不適用於 MSDN 使用者，因為它們是舊版 Xamarin 授權的特定問題。 如果您是 MSDN 使用者，而且您會看到錯誤類似下面的，請嘗試[更新至最新版本的 Xamarin](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) （& s) 要參考這個[授權選項指南](~/cross-platform/get-started/requirements.md)。



## <a name="invalid-license"></a>「 無效的授權 」

> 授權無效。 請重新啟動 Xamarin.Android (XA9999) 無法判斷授權版本。 (XA9010)

在幾個不同的情況下，可能會出現這些訊息。

-   在磁碟上目前的授權可能不同步的電腦上目前的使用者資訊。 [重新整理授權檔案](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)可解決這個問題，在許多情況下。

-   電腦可能會有 2 衝突重複啟動。 Xamarin 是無法再使用這種類型的授權。 如果您遇到問題，會顯示有關此錯誤，請[電子郵件支援](https://www.xamarin.com/support)。

-   Xamarin 帳戶可能尚未邀請 Xamarin 授權。 另請參閱：[小組授權管理](~/cross-platform/troubleshooting/legacy-licenses/team-management.md)。

## <a name="failed-to-load-android-entitlements"></a>「 無法載入 Android 權利 」

> Mono.VisualStudio.Shell.ShellPackage 錯誤： 0： 無法載入 Android 的權利： 無效的授權。 請重新啟動 Xamarin.Android (XA9999) Mono.VisualStudio.Shell.ShellPackage 錯誤： 0： 無法更新授權： 無效的授權。 請重新啟動 Xamarin.Android (XA9999)

這是較舊版本的上述 「 無效的授權 」 問題。 適用於相同的疑難排解步驟。

## <a name="this-version-was-released-after-your-subscription-expired"></a>「 這個版本發行您的訂用帳戶過期之後 」

> 錯誤 XA9000： 此版本已發行您的訂用帳戶過期之後 (2014 年 11 月 11 日下午 5:11:41)。 (XA9000)(Mobile.Android.Utilities) 錯誤 XA9010： 無法判斷授權版本。 (XA9010)(Mobile.Android.Utilities)

這些訊息通常會出現兩種情況：

-   磁碟上的授權已過期。 [重新整理授權檔案](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)可解決這個問題，在許多情況下。

-   Xamarin 訂用帳戶已不再作用中，且較新的訂用帳戶到期日比 Xamarin 目前安裝的版本。 正確的修正方法是在此情況下[降級](http://kb.xamarin.com/customer/portal/articles/1699777)Xamarin 訂用帳戶到期前發行的版本。 歡迎傳送電子郵件給[ hello@xamarin.com ](mailto:hello@xamarin.com)要求此訂用帳戶最新的有效版本。 如果您仍然可以看到錯誤降級以後，請務必嘗試過重新整理授權檔案。

## <a name="additional-references"></a>其他參考資料

-   [如何重新整理授權](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)
-   [如何降級 Xamarin](http://kb.xamarin.com/customer/portal/articles/1699777-downgrading)
-   [Xamarin.Android 錯誤碼的清單](~/android/troubleshooting/errors.md)
-   [MonoTouch (Xamarin.iOS) 錯誤碼的清單](~/ios/troubleshooting/mtouch-errors.md)

### <a name="next-steps"></a>後續步驟
如需進一步協助，請連絡我們，或如果此問題仍然發生即使利用上述的資訊，請參閱[xamarin 有哪些支援的選項？](~/cross-platform/troubleshooting/support-options.md)有關連絡人的選項，建議，以及如何如有需要的檔案新的 bug。

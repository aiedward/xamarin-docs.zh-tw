---
title: "為什麼無法我登入 Visual Studio 或 Visual Studio 中的 Xamarin 的 Mac？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6EF2B553-5DF9-41CC-B68F-77A7F64572FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: b50969e4c1d75c0bd79c08223dd959241dcb229a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="why-cant-i-log-into-xamarin-in-visual-studio-or-visual-studio-for-mac"></a>為什麼無法我登入 Visual Studio 或 Visual Studio 中的 Xamarin 的 Mac？

> [!IMPORTANT]
> 本指南並不適用於大部分的 MSDN 使用者因為它們不需要擁有或登入 Xamarin 帳戶，除非使用[Xamarin 元件儲存](https://components.xamarin.com/)或[Visual Studio for Mac (Mac)](~/cross-platform/get-started/requirements.md)。 MSDN 授權持有者應該要參考這個[授權選項指南](~/cross-platform/get-started/requirements.md)改為。



## <a name="overview"></a>總覽
有幾個常見的原因，可能會導致您無法登入您在 IDE 中的 Xamarin 帳戶。 已知的問題和修正如下所述。

### <a name="finding-the-login-screen"></a>尋找登入畫面

如需參考，登入畫面是下列位置找到：

- Visual Studio for Mac
   - 右上角的 [歡迎] 畫面
   - **Visual Studio for Mac > 帳戶**(Mac)
   - **工具 > 帳戶**(Windows)
- Visual Studio
   - **工具 > Xamarin 帳戶**

## <a name="the-ide-is-connecting-but-the-account-screen-isnt-showing-correct-login-information"></a>正在連接的 IDE，但 [帳戶] 畫面未顯示正確的登入資訊

以手動授權重新同步處理是通常會解決此問題。
請遵循本文章中的指引：[如何手動 resyncronize Xamarin 授權？](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)

## <a name="invalid-account-information"></a>無效的帳戶資訊

如果您移至在 Xamarin 網站[登入頁面](https://store.xamarin.com/Login?from=%2faccount%2f)，您可以嘗試登入您目前帳戶的認證。
網頁也有連結重設密碼，並建立新的帳戶。

## <a name="account-is-valid-but-the-ide-cant-connect"></a>帳戶有效，但無法連接的 IDE

這通常被導致防火牆或其他安全性設定會封鎖存取所需的端點 IDE 時。
啟用伺服器必須存取下列項目：

> activation.xamarin.com store.xamarin.com auth.xamarin.com

不過，其他數個端點是重要的一般開發程序，例如入門 NuGet 封裝、 等等的更新。因此，建議您確認*所有*的端點會加入從[Xamarin 防火牆設定指南](~/cross-platform/get-started/installation/firewall.md)。

### <a name="ios-in-xamarin-studio-windows"></a>在 Windows 的 Xamarin Studio 的 iOS
Xamarin Studio for Windows 中不支援 iOS 開發。 存取時的登入畫面，將不會顯示 iOS 授權。

相反地，透過 Xamarin Studio (Mac) 或 Visual Studio 使用舊版授權的登入。 請注意，在 Windows 上的 MSDN 使用者不需要登入。

## <a name="additional-support"></a>其他支援

如果上述狀況不描述您的情況下，或解決此問題，請參閱這些[支援選項](https://www.xamarin.com/support)。

---
title: 如何手動同步 Xamarin 授權？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D0BD93E9-3A1F-4E5B-8EE8-36ADC33DCFE4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: b06a1a7d525c91d7c3973b2b02d3d2835ce482f9
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2018
---
# <a name="how-do-i-manually-resynchronize-xamarin-licenses"></a>如何手動同步 Xamarin 授權？

> [!IMPORTANT]
> 本指南並不適用於大部分的 MSDN 使用者因為它們不需要擁有或登入 Xamarin 帳戶，除非使用[Xamarin 元件儲存](https://components.xamarin.com/)或[Visual Studio for Mac (Mac)](~/cross-platform/get-started/requirements.md)。




## <a name="overview"></a>總覽

通常授權資訊將會重新同步處理與 Xamarin 授權伺服器會自動啟動 IDE 時。 比方說，通常會自動將會出現授權升級、 降級，並更新之後重新啟動 IDE。 顯示在 IDE 中的授權資訊應符合上所顯示的資訊您[帳戶 頁面上](https://store.xamarin.com/account/my/subscription/computers)。 如果資訊仍不相符，您可以先請仔細檢查您存放區的帳戶資訊看起來正確，並再手動重新同步處理授權登出、 刪除授權磁碟上的檔案，並再登入。

### <a name="note-for-ios-developers-on-windows"></a>請注意，在 Windows 上的 iOS 開發人員

在 Windows 上的 iOS 開發人員可能也要針對 Visual Studio 中執行這些步驟，適用於 Mac;即使您沒有直接開發配對的 Mac 建置主機上。

## <a name="quick-manual-refresh-steps"></a>快速手動重新整理步驟

這個快速的程序會略過刪除授權檔案，這可能足夠在某些情況下的磁碟上的步驟。 

1.  登出您的 Xamarin 帳戶透過 IDE:
    -   Visual Studio for Mac
        -   右上角的 [歡迎] 畫面
        -   **Visual Studio for Mac > 帳戶**(Mac)
        -   **工具 > 帳戶**(Windows)
    -   Visual Studio
        -   **工具 > Xamarin 帳戶**
2.  登入您在 IDE 中的 Xamarin 帳戶。

## <a name="extended-manual-license-refresh-steps"></a>擴充授權手動重新整理步驟

1.  登出您的 Xamarin 帳戶透過 IDE。 
2.  結束 IDE。
3.  請檢查儲存帳戶資訊正確無誤。 特別是重複的電腦名稱會在檢查[電腦頁面](https://store.xamarin.com/account/my/subscription/computers)。

4.  如果您看到一組重複的電腦名稱時，使用**停用**下拉式功能表来移除的項目_兩者_組的成員：
    
    ![授權]-> [停用上https://store.xamarin.com/account/my/subscription/computers ](resync-licenses-images/deactivate.png "使用停用下拉式清單功能表項目移除該配對的兩個成員")

5.  刪除任何剩餘的份數授權仍存在於磁碟上的檔案。
    -   Windows

        刪除所有的下列資料夾：
        -   `%PROGRAMDATA%\Mono for Android`
        -   `%PROGRAMDATA%\MonoTouch`
        -   `%LOCALAPPDATA%\VirtualStore\ProgramData\Mono for Android`
        -   `%LOCALAPPDATA%\VirtualStore\ProgramData\MonoTouch`

        在預設安裝的 Windows:
        -   `%PROGRAMDATA%` 將展開到 `C:\ProgramData`
        -   `%LOCALAPPDATA%` 將展開到 `C:\Users\%USERNAME%\AppData\Local`

        `VirtualStore`授權副本會自動建立 Windows 在某些情況下。 如果 VirtualStore 副本都存在，系統會讀取這些_改為_中授權的`%PROGRAMDATA%`。

    -   Mac

        刪除所有的下列資料夾：

        -   `~/Library/MonoAndroid`
        -   `~/Library/MonoTouch`
        -   `~/Library/Xamarin.Mac`

        您可以存取這些路徑中的**Finder**選取**移 > 移至資料夾**功能表，並將它們貼到對話方塊視窗。 搜尋會自動取代 ~ 波狀符號字元，與您使用者資料夾。

6.  開啟 Visual Studio for Mac 或 Visual Studio 和記錄檔送回您的 Xamarin 帳戶。

## <a name="supplementary-information-individual-license-file-locations"></a>補充資訊： 個別的授權檔案位置

### <a name="windows"></a>Windows

在 Windows 上的個別的授權檔案會儲存在下列位置：

-   Xamarin.Android:  
     `%PROGRAMDATA%\Mono for Android\License\monoandroid.licx`
-   Xamarin.iOS:  
     `%PROGRAMDATA%\MonoTouch\License\monotouch.licx`

授權*試用*訂用帳戶名稱不同：

-   Xamarin.Android:  
     `%PROGRAMDATA%\Mono for Android\License\monoandroid.trial.licx`
-   Xamarin.iOS:  
     `%PROGRAMDATA%\MonoTouch\License\monotouch.trial.licx`

### <a name="mac"></a>Mac

在 Mac 上的個別的授權檔案會儲存在下列位置：

-   Xamarin.Android:  
     `~/Library/MonoAndroid/License`
-   Xamarin.iOS:  
     `~/Library/MonoTouch/License.v2`
-   Xamarin.Mac:  
     `~/Library/Xamarin.Mac/License`

授權*試用*訂用帳戶名稱不同：

-   Xamarin.Android:  
     `~/Library/MonoAndroid/License.trial`
-   Xamarin.iOS:  
     `~/Library/MonoTouch/License.trial`
-   Xamarin.Mac:  
     `~/Library/Xamarin.Mac/License.trial`

## <a name="additional-troubleshooting-steps"></a>其他疑難排解步驟

-   檢查是否在您的使用者名稱、 您的電腦名稱，或在任何完整展開之授權檔案的路徑中有任何非 ASCII 字元。

-   [請連絡 Xamarin 開發人員支援](http://xamarin.com/support)

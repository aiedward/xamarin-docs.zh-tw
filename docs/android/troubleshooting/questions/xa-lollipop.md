---
title: 哪些版本的 Xamarin.Android 加入棒棒糖符號支援？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 065c68a373f67bb352b59dc88ef89daec8b51ef8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>哪些版本的 Xamarin.Android 加入棒棒糖符號支援？

**注意：**本指南原本針對 Android L 預覽所撰寫。

-   [Xamarin.Android 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/)新增 Android L 預覽支援。
-   [Xamarin.Android 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/)新增 Android 棒棒糖符號的支援。

Xamarin 主動只支援 Xamarin 工具目前穩定版本。 提供下列資訊 」 做為-是 「 針對較舊版的工具。 如需 Xamarin 版本最新的資訊，請檢查[這裡](http://releases.xamarin.com/)。

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>中的 「 遺漏 android.jar API Level 21 的"Android L 預覽

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

下列錯誤訊息 （或類似） 可能會顯示：

```cmd
Error 1 Could not find android.jar for API Level 21.
```

這則訊息表示沒有安裝的應用程式開發介面層級 21 的 Android SDK 平台。 請安裝 Android SDK 管理員 (工具 > 開啟 Android SDK Manager...)，或將 Xamarin.Android 專案變更為已安裝應用程式開發介面版本為目標。

有幾個此問題的解決方法：

1. 變更您的專案，讓它的目標應用程式開發介面 19 或更低。

2. 重新命名您的 android 21 資料夾從 android 21 至 android l。 （最佳情況下，這應該只用做為暫時修正，和它可能完全無法運作非常好）。

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. 暫時降級回 Android API 層級 21"L"預覽 [1]:

    1.  刪除**%LOCALAPPDATA%\\Android\\android sdk\\平台\\android 21** 
    2.  展開 [1] 到**c:\\使用者\\<username>\\AppData\\本機\\Android\\android sdk\\平台**建立**android L**資料夾。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

下列錯誤訊息 （或類似） 可能會顯示：

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

這表示沒有安裝的應用程式開發介面層級 21 的 Android SDK 平台。請安裝 Android SDK 管理員 (工具 > SDK Manager...)，或將 Xamarin.Android 專案變更為已安裝應用程式開發介面版本為目標。

有幾個此問題的解決方法：

1. 變更您的專案，讓它的目標應用程式開發介面 19 或更低。

2. 重新命名您的 android 21 資料夾從 android 21 至 android l。 （最佳情況下，這應該只用做為暫時修正，和它可能完全無法運作非常好）。

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. 暫時降級回 Android API 層級 21"L"預覽 [1]:

    1.  Delete **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  展開 [1] 到**/Users/username/Library/Developer/Xamarin/android-sdk-macosx**建立**android L**資料夾。

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)

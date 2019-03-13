---
title: 哪些版本的 Xamarin.Android 新增了 Lollipop 支援？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7e31f9ad46a04b648a6a1f24c075426f7d98a663
ms.sourcegitcommit: 6655cccf9d3be755773c2f774b5918e0b141bf84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2019
ms.locfileid: "57305603"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>哪些版本的 Xamarin.Android 新增了 Lollipop 支援？

**注意：** 本指南原先是針對 Android L 預覽編寫。

-   [Xamarin.Android 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/)新增 Android L 預覽支援。
-   [Xamarin.Android 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/)新增了 Android Lollipop 支援。

Xamarin 僅主動支援目前的穩定版本的 Xamarin 工具。 提供下列資訊 」 做為-是 「 適用於舊版工具。 如需有關 Xamarin 版本的最新資訊，請參閱[此處](http://releases.xamarin.com/)。

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>中的 「 遺漏 android.jar API 層級 21 「 Android L 預覽

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

下列錯誤訊息 （或類似） 可能會顯示：

```cmd
Error 1 Could not find android.jar for API Level 21.
```

此訊息表示未安裝 API Level 21 的 Android SDK 平台。 在 Android SDK Manager 中安裝它 (**工具 > 開啟 Android SDK 管理員...**)，或變更您的 Xamarin.Android 專案目標的已安裝的 API 版本。

有此問題的幾個因應措施：

1. 變更您的專案，讓它的目標 API 19 或較低。

2. 重新命名您的 android 21 資料夾從 android 21 至 android l。 （最佳情況下，這應該只能作為暫時的修正程式，而且它可能完全無法運作非常好）。

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. 暫時降級回 Android API Level 21"L"預覽 [1]:

    1.  刪除 **%LOCALAPPDATA%\\Android\\android sdk\\平台\\android 21** 
    2.  展開 [1] 到**c:\\使用者\\&lt;使用者名稱&gt;\\AppData\\本機\\Android\\android sdk\\平台**來建立**android-L**資料夾。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

下列錯誤訊息 （或類似） 可能會顯示：

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

這表示未安裝 [API Level 21 的 Android SDK 平台。在 Android SDK Manager 中安裝它 (工具 > SDK 管理員])，或變更您的 Xamarin.Android 專案目標的已安裝的 API 版本。

有此問題的幾個因應措施：

1. 變更您的專案，讓它的目標 API 19 或較低。

2. 重新命名您的 android 21 資料夾從 android 21 至 android l。 （最佳情況下，這應該只能作為暫時的修正程式，而且它可能完全無法運作非常好）。

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. 暫時降級回 Android API Level 21"L"預覽 [1]:

    1.  刪除 **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  展開 [1] 到 **/Users/username/Library/Developer/Xamarin/android-sdk-macosx**來建立**android-L**資料夾。

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)

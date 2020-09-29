---
title: 哪些版本的 Xamarin.Android 新增了 Lollipop 支援？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 27631d19ea157c1af08666b55a067e729b806434
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453632"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>哪些版本的 Xamarin.Android 新增了 Lollipop 支援？

> [!NOTE]
> 本指南原本是針對 Android L preview 所撰寫。

- [Xamarin. android 4.17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md) 新增了 Android L Preview 支援。
- [Xamarin. android 4.20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md) 新增了 Android 棒棒支援。

Xamarin 只主動支援 Xamarin 工具的目前穩定版本。 以下是舊版工具的「依原樣」提供的資訊。 如需 Xamarin 版本的最新資訊，請參閱 [版本](/xamarin/android/release-notes/)資訊。

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>Android L Preview 中的「缺少適用于 API 層級21的 android .jar」

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

可能會顯示下列錯誤訊息 (或類似的) ：

```cmd
Error 1 Could not find android.jar for API Level 21.
```

此訊息表示未安裝 API 層級21的 Android SDK 平臺。 將它安裝在 Android SDK 管理員 (**工具 > 開啟 Android SDK 管理員** ]) 中，或將您的 Xamarin. Android 專案變更為以安裝的 API 版本為目標。

有幾個解決方法可解決此問題：

1. 變更您的專案，使其以 API 19 或更低版本為目標。

2. 將您的 android-21 資料夾從 android-21 重新命名為 android-L。  (最棒的是，這應該僅做為暫時的修正程式，而且可能無法完全正常運作。 ) 

   **% LOCALAPPDATA% \\ android \\ android-sdk \\ 平臺 \\ android-21**

3. 暫時降級回 Android API 層級 21 "L" preview [1]：

    1. 刪除 **% LOCALAPPDATA% \\ android \\ android-sdk \\ 平臺 \\ android-21** 
    2. 將 [1] 解壓縮至**C： \\ 使用者使用者 \\ &lt; 名稱 &gt; \\ AppData \\ 本機 \\ Android \\ android sdk \\ 平臺**以建立**Android-L**資料夾。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

可能會顯示下列錯誤訊息 (或類似的) ：

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

這表示未安裝 API 層級21的 Android SDK 平臺。請將它安裝在 Android SDK 管理員 (工具 > SDK 管理員 ... ) ，或將您的 Xamarin Android 專案變更為以安裝的 API 版本為目標。

有幾個解決方法可解決此問題：

1. 變更您的專案，使其以 API 19 或更低版本為目標。

2. 將您的 android-21 資料夾從 android-21 重新命名為 android-L。  (最棒的是，這應該僅做為暫時的修正程式，而且可能無法完全正常運作。 ) 

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. 暫時降級回 Android API 層級 21 "L" preview [1]：

    1. 刪除 **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2. 將 [1] 解壓縮至 **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** 以建立 **android-L** 資料夾。

-----

[1]- [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
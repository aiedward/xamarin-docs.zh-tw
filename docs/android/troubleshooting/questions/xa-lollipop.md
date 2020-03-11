---
title: 哪些版本的 Xamarin.Android 新增了 Lollipop 支援？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 9e36189c771ed0c91a6030fd0ab615ab9af4dd52
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73026708"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>哪些版本的 Xamarin.Android 新增了 Lollipop 支援？

> [!NOTE]
> 本指南原本是針對 Android L preview 所撰寫。

- [Xamarin. android 4.17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md)已新增 Android L Preview 支援。
- [Xamarin. android 4.20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md)已新增 Android 棒糖支援。

Xamarin 只會主動支援 Xamarin 工具目前穩定的版本。 較舊版本的工具會以「原樣」提供下列資訊。 如需 Xamarin 版本的最新資訊，請參閱[版本](https://docs.microsoft.com/xamarin/whats-new/#product-release-notes)資訊。

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>Android L Preview 中的「缺少 API 層級21的 android .jar」

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

可能會顯示下列錯誤訊息（或類似）：

```cmd
Error 1 Could not find android.jar for API Level 21.
```

此訊息表示未安裝適用于 API 層級21的 Android SDK 平臺。 將它安裝在 Android SDK 管理員（[**工具 > 開啟 Android SDK 管理員**]），或變更您的 Xamarin. Android 專案，將目標設定為已安裝的 API 版本。

有一些因應措施可解決此問題：

1. 變更您的專案，使其以 API 19 或更低版本為目標。

2. 將 android-21 資料夾從 android-21 重新命名為 android-L。 （最棒的是，這只能用來做為暫時的修正程式，而且可能完全無法運作）。

   **% LOCALAPPDATA%\\Android\\android-sdk\\平臺\\android-21**

3. 暫時降級回到 Android API 層級 21 "L" preview [1]：

    1. 刪除 android **\\android-sdk\\平臺的% LOCALAPPDATA%\\\\android-21** 
    2. 將 [1] 解壓縮到**C：\\使用者\\&lt;username&gt;\\AppData\\本機\\android\\android-sdk\\平臺**來建立**android-L**資料夾。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

可能會顯示下列錯誤訊息（或類似）：

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

這表示未安裝適用于 API 層級21的 Android SDK 平臺。將它安裝在 Android SDK 管理員（[工具 > SDK 管理員 ...]）中，或將您的 Xamarin. Android 專案變更為以已安裝的 API 版本為目標。

有一些因應措施可解決此問題：

1. 變更您的專案，使其以 API 19 或更低版本為目標。

2. 將 android-21 資料夾從 android-21 重新命名為 android-L。 （最棒的是，這只能用來做為暫時的修正程式，而且可能完全無法運作）。

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. 暫時降級回到 Android API 層級 21 "L" preview [1]：

    1. 刪除 **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2. 將 [1] 解壓縮至 **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** ，以建立**android-L**資料夾。

-----

[1]- [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)

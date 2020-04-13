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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026708"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>哪些版本的 Xamarin.Android 新增了 Lollipop 支援？

> [!NOTE]
> 本指南最初是為 Android L 預覽編寫的。

- [Xamarin.Android 4.17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md)添加了 Android L 預覽支援。
- [Xamarin.安卓4.20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md)添加了安卓棒棒糖支援。

Xamarin 僅積極支援 Xamarin 工具的當前穩定釋放。 以下資訊為舊版本的工具提供"正版" 有關 Xamarin 版本的最新資訊,請檢視[發行說明](https://docs.microsoft.com/xamarin/whats-new/#product-release-notes)。

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>在 Android L 預覽版中"缺少 API 級別 21 的 android.jar"

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

可能會出現以下錯誤訊息(或類似訊息):

```cmd
Error 1 Could not find android.jar for API Level 21.
```

此消息表示未安裝 API 21 級別的 Android SDK 平臺。 要麼將其安裝在 Android SDK 管理員(**工具>打開 Android SDK 管理器..."),** 要麼更改 Xamarin.Android 專案以定位已安裝的 API 版本。

此問題有一些解決方法:

1. 更改專案,使其目標為 API 19 或更低。

2. 重新命名您的android-21資料夾從android-21到android-L。 (充其量,這應該只用作臨時修復,而且可能根本不工作。

   **%本地應用資料%\\\\安卓\\安 卓-sdk 平臺\\的安卓-21**

3. 暫時降級回 Android API 級別 21"L"預覽 [1]:

    1. 移除**\\\\%LOCALAPPDATA% Android-sdk\\平臺\\android-21** 
    2. 提取 [1] 到**\\\\C: 使用者\\\\&lt;&gt;\\\\使用者名 AppData\\本地 Android-sdk 平台**建立**android-L**資料夾.

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

可能會出現以下錯誤訊息(或類似訊息):

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

這意味著未安裝 API 21 級別的 Android SDK 平臺。將其安裝在 Android SDK 管理器(工具> SDK 管理器...")中,或更改 Xamarin.Android 專案以定位已安裝的 API 版本。

此問題有一些解決方法:

1. 更改專案,使其目標為 API 19 或更低。

2. 重新命名您的android-21資料夾從android-21到android-L。 (充其量,這應該只用作臨時修復,而且可能根本不工作。

   ***/圖書館/開發人員/夏馬林/安卓-sdk-macosx/android-21**

3. 暫時降級回 Android API 級別 21"L"預覽 [1]:

    1. 刪除 **/使用者/使用者名/庫/開發人員/Xamarin/android-sdk-macosx/android-21**
    2. 將 [1] 提取到 **/使用者/使用者名/庫/開發人員/Xamarin/android-sdk-macosx**以創建一個**android-L**資料夾。

-----

[1] -[https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)

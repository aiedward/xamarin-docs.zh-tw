---
title: Xamarin.安卓常見問題
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 28b13951a0d681ffdb8e643667e496e0b3606628
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78293028"
---
# <a name="android-frequently-asked-questions"></a>安卓常見問題

## <a name="installation--setup"></a>安裝&安裝

### <a name="which-android-sdk-packages-should-i-install"></a>[應該安裝哪一個 Android SDK 套件？](install-android-sdk-packages.md)

安裝 Android SDK 不會自動包含開發所需的所有軟體包。 雖然開發人員個人需求各不相同,但本指南討論了使用 Xamarin.Android 開發通常需要的軟體包。

### <a name="where-can-i-set-my-android-sdk-locations"></a>[在哪裡可以設定 Android SDK 的位置？](android-sdk-location.md)

本指南介紹了 Android SDK 的兩個預設設置,這些設置應適用於大多數設置;以及如何根據需要在 Mac 或 Visual Studio 的可視化工作室中更改這些預設值。

### <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>[如何更新 Java 開發套件 (JDK) 版本？](update-jdk.md)

本文演示如何在 Windows 和 Mac 上更新 Java 開發工具套件 (JDK) 版本。

### <a name="can-i-use-java-development-kit-jdk-version-9-or-later"></a>[可以使用 Java 開發套件 (JDK) 9 版或更新版本嗎？](jdk9-errors.md)

Xamarin.Android 需要 JDK 8 或微軟行動開放 JDK。 本文列出了一些常見的錯誤消息,您可能會看到是否安裝了 JDK 9 或更高版本,以及檢查 JDK 版本的說明。

### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>[如何手動安裝 Xamarin.Android.Support 套件所需的 Android 支援程式庫？](install-android-support-library.md)

本指南提供了在 Windows `Xamarin.Android.Support.v4` & Mac 上安裝支援庫的示例步驟。

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>[在 Windows 上對 Android 進行偵錯需要哪些 USB 裝置？](android-drivers-debug-windows.md)

在 Windows 中開發時在 Android 設備上調試;您需要安裝相容的 USB 驅動程式。 默認情況下,Android SDK 管理器包括"谷歌 USB 驅動程式",這增加了對 Nexus 設備的支援。
其他設備需要設備製造商發佈的USB驅動程式。 本指南提供有關查找這些驅動程序的資訊以及替代測試方法。

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>[從 Windows VM 連線到在 Mac 上執行的 Android 模擬器是否可行？](connect-android-emulator-mac-windows.md)

本指南介紹了使用 Android 模擬器時的方法。

## <a name="general-questions"></a>一般問題

### <a name="how-do-i-automate-an-android-nunit-test-project"></a>[如何將 Android NUnit 測試專案自動化？](automate-android-nunit-test.md)

本指南介紹了設置 Android NUnit 測試專案的步驟,_而不是_Xamarin.UITest 專案。 Xamarin.UITest 指南[可在此處](/appcenter/test-cloud/preparing-for-upload)找到。

### <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>[為何我的 Android 發行組建無法連線到網際網路？](android-internet.md)

此問題的最常見原因是**INTERNET**許可權自動包含在調試生成中,但必須手動設置發佈版本。 本指南介紹如何啟用發佈版本的許可權。

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>[更聰明的 Xamarin Android 支援 v4 / v13 NuGet 套件](android-support-v4v13-libraries.md)

`Support-v4``Support-v13`不能在同一個應用程式中一起使用,也就是說,它們是互斥的。 這是因為`Support-v13`實際上`Support-v4`包含的所有類型和實現。 如果在同一項目中嘗試並引用這兩個,則會遇到重複的類型錯誤。

### <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>[如何解決路徑「長異常錯誤」?](path-too-long-exception.md)

本文介紹如何解決在構建 Xamarin.Android 專案時可能出現的**PathToolongException**錯誤。

## <a name="deprecated"></a>已被取代

> [!NOTE]
> 以下文章適用於 Xamarin 的最新版本中已解決的問題。 但是,如果軟體的最新版本出現問題,請使用您的完整版本控制資訊和完整生成日誌輸出提交[新 Bug。](~/cross-platform/troubleshooting/questions/howto-file-bug.md)

### <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>[哪些版本的 Xamarin.Android 新增了 Lollipop 支援？](xa-lollipop.md)

本指南最初是為 Android L 預覽編寫的。Xamarin.Android 4.17 添加了安卓 L 預覽支援& Xamarin.Android 4.20 添加了安卓棒棒糖支援。

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>[Android.Support.v7.AppCompat - 找不到符合指定名稱：attr 'android:actionModeShareDrawable' 的資源](missing-action-mode-share-drawable.md)

如果缺少某些必需的 Android SDK 包,則早期版本的 Xamarin 中可能會出現此錯誤。

### <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>[調整適用於 Android Designer 的 JAVA 記憶體參數](android-designer-java-memory.md)

在為 Android 設計`java`器啟動 進程時使用的預設記憶體參數可能與某些系統配置不相容。 從 Xamarin 工作室 5.7.2.7 和 Visual Studio 3.9.344 的 Xamarin 開始,這些設置可以按專案進行自定義。

### <a name="my-android-resourcedesignercs-file-will-not-update"></a>[我的 Android Resource.designer.cs 檔案不會更新](resource-designer-wont-update.md)

Xamarin.Studio 5.1 中的一個 Bug 以前通過部分或全部刪除 .csproj 檔中的 xml 代碼而損壞了 .csproj 檔。 這將導致 Android 構建系統的重要部分(如更新 Android Resource.designer.cs)失敗。 截至7月15日5.1.4穩定發佈,此 Bug 已修復;但在許多情況下,專案檔必須手動修復,如本指南中所述。

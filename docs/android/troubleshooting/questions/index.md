---
title: Xamarin Android 常見問題
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 28b13951a0d681ffdb8e643667e496e0b3606628
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78293028"
---
# <a name="android-frequently-asked-questions"></a>Android 常見問題

## <a name="installation--setup"></a>安裝 & 安裝程式

### <a name="which-android-sdk-packages-should-i-install"></a>[我應該安裝哪一個 Android SDK 套件？](install-android-sdk-packages.md)

安裝 Android SDK 不會自動包含開發所需的所有必要套件。 雖然個別的開發人員需要不同，但本指南會討論使用 Xamarin 進行開發時通常需要的套件。

### <a name="where-can-i-set-my-android-sdk-locations"></a>[在哪裡可以設定 Android SDK 的位置？](android-sdk-location.md)

本指南說明 Android SDK 的預設設定，這應該適用于大部分的設定;以及如何在必要時，在 Visual Studio for Mac 或 Visual Studio 中變更這些預設值。

### <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>[如何更新 Java 開發套件 (JDK) 版本？](update-jdk.md)

本文說明如何更新 Windows 和 Mac 上的 JAVA 開發工具組（JDK）版本。

### <a name="can-i-use-java-development-kit-jdk-version-9-or-later"></a>[我可以使用 JAVA 開發工具組（JDK）9版或更新版本嗎？](jdk9-errors.md)

Xamarin. Android 需要 JDK 8 或 Microsoft Mobile OpenJDK。 本文列出您在安裝 JDK 9 或更新版本時可能會看到的一些常見錯誤訊息，以及檢查 JDK 版本的指示。

### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>[如何可以手動安裝 Xamarin.Android.Support 套件所需的 Android 支援程式庫？](install-android-support-library.md)

本指南提供在 Windows & Mac 上安裝 `Xamarin.Android.Support.v4` 支援程式庫的範例步驟。

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>[在 Windows 上對 Android 進行偵錯需要哪些 USB 裝置？](android-drivers-debug-windows.md)

在 Windows 中進行開發時，在 Android 裝置上進行偵錯工具;您必須安裝相容的 USB 驅動程式。 Android SDK Manager 預設包含「Google USB 驅動程式」，這會新增對結點裝置的支援。
其他裝置則需要裝置製造商所發佈的 USB 驅動程式。 本指南提供尋找這些驅動程式的相關資訊，以及替代的測試方法。

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>[是否可能從 Windows VM 連線到在 Mac 上執行的 Android 模擬器？](connect-android-emulator-mac-windows.md)

本指南涵蓋使用 Android 模擬器的方法。

## <a name="general-questions"></a>一般問題

### <a name="how-do-i-automate-an-android-nunit-test-project"></a>[如何將 Android NUnit 測試專案自動化？](automate-android-nunit-test.md)

本指南涵蓋設定 Android NUnit 測試專案，而_不_是 UITest 專案的步驟。 您可以在[這裡](/appcenter/test-cloud/preparing-for-upload)找到 UITest 指南。

### <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>[為何我的 Android 發行組建無法連線到網際網路？](android-internet.md)

此問題最常見的原因是**網際網路**許可權會自動包含在 debug 組建中，但必須針對發行組建手動設定。 本指南描述如何啟用發行組建的許可權。

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>[更聰明的 Xamarin Android 支援 v4 / v13 NuGet 套件](android-support-v4v13-libraries.md)

`Support-v4` 和 `Support-v13` 不能同時在相同的應用程式中使用，也就是說，它們是互斥的。 這是因為 `Support-v13` 實際上包含 `Support-v4`的所有類型和實作為。 如果您嘗試同時在相同的專案中參考，將會遇到重複的類型錯誤。

### <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>[如何? 解決 PathTooLongException 錯誤？](path-too-long-exception.md)

本文說明如何解決建立 Xamarin Android 專案時可能發生的**PathTooLongException**錯誤。

## <a name="deprecated"></a>已被取代

> [!NOTE]
> 下列文章適用于最新 Xamarin 版本中已解決的問題。 不過，如果軟體的最新版本發生問題，請使用完整版本設定資訊和完整組建記錄檔輸出來提出[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

### <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>[哪些版本的 Xamarin.Android 新增了 Lollipop 支援？](xa-lollipop.md)

本指南原本是針對 Android L preview 所撰寫。Xamarin. Android 4.17 已新增 & Xamarin 的 Android L Preview 支援。 Android 4.20 新增了 Android 棒的支援。

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>[Android.Support.v7.AppCompat - 找不到符合所提供名稱：attr 'android:actionModeShareDrawable' 的資源](missing-action-mode-share-drawable.md)

如果缺少部分必要的 Android SDK 套件，則舊版 Xamarin 可能會發生此錯誤。

### <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>[調整適用於 Android Designer 的 JAVA 記憶體參數](android-designer-java-memory.md)

啟動 Android designer 的 `java` 程式時，所使用的預設記憶體參數可能與某些系統組態不相容。 從 Xamarin Studio 5.7.2.7 和 Xamarin for Visual Studio 開始，可以根據每個專案來自訂這些設定。

### <a name="my-android-resourcedesignercs-file-will-not-update"></a>[我的 Android Resource.designer.cs 檔案不會更新](resource-designer-wont-update.md)

5\.1 Xamarin 中的 bug 先前已損毀 .csproj 檔案中的 xml 程式碼，或在 .csproj 檔案中完全刪除該檔案。 這會導致 Android 組建系統的重要部分（例如，更新 Android Resource.designer.cs）失敗。 從5.1.4 穩定版本于7月15日起，已修正此 bug;但在許多情況下，專案檔必須手動修復，如本指南中所述。

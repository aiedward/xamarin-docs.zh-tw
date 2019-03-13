---
title: 常見問題集
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: ad3fc32245880f6603c63d33aac49309fd61b753
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "36935460"
---
# <a name="frequently-asked-questions"></a>常見問題集

## <a name="installation--setup"></a>安裝和設定

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[我應該安裝哪一個 Android SDK 套件？](install-android-sdk-packages.md)

安裝 Android SDK 不會自動包含開發所有最小必要的封裝。 雖然個別的開發人員必須不盡相同，則本指南會討論通常會需要使用 Xamarin.Android 開發的封裝。

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[在哪裡可以設定 Android SDK 的位置？](android-sdk-location.md)

本指南將告訴您的 Android sdk，應可適用於大部分的配置; 這兩個預設設定以及如何視需要變更這些預設值，在 Visual Studio for Mac 或 Visual Studio。

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[如何更新 Java 開發套件 (JDK) 版本？](update-jdk.md)

這篇文章說明如何更新 Windows 和 mac 上的 Java Development Kit (JDK) 版本

### <a name="can-i-use-java-development-kit-jdk-version-9-or-laterjdk9-errorsmd"></a>[我可以使用 Java Development Kit (JDK) 9 或更新版本嗎？](jdk9-errors.md)

Xamarin.Android 需要 JDK 8 或 Microsoft 行動 OpenJDK。 本文列出一些常見的錯誤訊息，您可能會查看是否已安裝作業 9 或更新版本的 JDK，以及檢查 JDK 版本的指示。


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[如何可以手動安裝 Xamarin.Android.Support 套件所需的 Android 支援程式庫？](install-android-support-library.md)

本指南提供的範例步驟安裝`Xamarin.Android.Support.v4`支援 Windows 和 mac 上的程式庫

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[在 Windows 上對 Android 進行偵錯需要哪些 USB 裝置？](android-drivers-debug-windows.md)

在 Windows; 中開發時，在 Android 裝置上偵錯您要安裝相容的 USB 驅動程式。 Android SDK 管理員會包含 「 Google USB 驅動程式 」，根據預設，這會新增為 Nexus 裝置的支援。
其他裝置則需要 USB 裝置製造商所發佈的驅動程式。 本指南提供有關尋找這些驅動程式也為另一種測試方法的資訊。

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[是否可能從 Windows VM 連線到在 Mac 上執行的 Android 模擬器？](connect-android-emulator-mac-windows.md)

使用 Android 模擬器時，本指南將涵蓋方法。

## <a name="general-questions"></a>一般問題

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[如何將 Android NUnit 測試專案自動化？](automate-android-nunit-test.md)

本指南涵蓋設定 Android NUnit 測試專案中，步驟_不_Xamarin.UITest 專案。 您可以找到 Xamarin.UITest 輔助線[此處](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)。

### <a name="how-do-i-enable-intellisense-in-android-axml-filesenable-axml-intellisensemd"></a>[如何啟用 Android.axml 檔案中的 Intellisense？](enable-axml-intellisense.md)

本指南說明如何啟用 android.axml 檔案的 Visual Studio's Intellisense。

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[為何我的 Android 發行組建無法連線到網際網路？](android-internet.md)

此問題最常見的原因在於**網際網路**權限會自動包含在偵錯組建，但必須手動設定的發行組建。 本指南說明如何啟用發行組建的權限。

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[更聰明的 Xamarin Android 支援 v4 / v13 NuGet 套件](android-support-v4v13-libraries.md)

`Support-v4` 和`Support-v13`不適用於一起在相同的應用程式，也就是它們互斥。 這是因為`Support-v13`實際包含的所有類型和實作`Support-v4`。 如果您嘗試參考相同專案中，您會遇到重複的型別錯誤。

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[如何解決 PathTooLongException 錯誤？](path-too-long-exception.md)

這篇文章說明如何解決**PathTooLongException**建置 Xamarin.Android 專案時可能發生的錯誤。



## <a name="deprecated"></a>已被取代

> [!NOTE]
> 以下文章適用於 Xamarin 的最新版本中已解決的問題。 不過，如果最新版本的軟體，就會發生問題，請指導，申請[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本控制資訊和完整建置記錄檔輸出。

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[哪些版本的 Xamarin.Android 新增了 Lollipop 支援？](xa-lollipop.md)

本指南原先是針對 Android L 預覽編寫。Xamarin.Android 4.17 新增 Android 的 L 預覽支援和 Xamarin.Android 4.20 新增了 Android Lollipop 支援。

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat - 找不到符合所提供名稱：attr 'android:actionModeShareDrawable' 的資源](missing-action-mode-share-drawable.md)

如果遺漏部分必要的 Android SDK 套件，可能會發生這個錯誤，在較舊版本的 Xamarin。

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[調整適用於 Android Designer 的 JAVA 記憶體參數](android-designer-java-memory.md)

啟動時會用到的預設記憶體參數`java`處理 Android 設計工具可能與某些系統組態不相容。 使用 Xamarin Studio 5.7.2.7 與 Xamarin for Visual Studio 3.9.344 這些設定的啟動可以針對每個專案進行自訂。

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[我的 Android Resource.designer.cs 檔案不會更新](resource-designer-wont-update.md)

在 Xamarin.Studio 5.1 的 bug 之前損毀部分或完全刪除.csproj 檔案中的 xml 程式碼的.csproj 檔案。 這會導致重要組件的 Android 建置系統 （例如更新 Android Resource.designer.cs） 失敗。 截至 5.1.4 穩定的版本在 7 月 15 日，已修正此錯誤;但在許多情況下的專案檔必須以手動方式，本指南中所述修復。




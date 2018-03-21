---
title: "常見問題集"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/19/2018
ms.openlocfilehash: 69a5c905367f9e6dbd38acb664ad21b9dbe63efc
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2018
---
# <a name="frequently-asked-questions"></a>常見問題集

## <a name="installation--setup"></a>安裝 （& s) 安裝程式

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[我應該安裝哪一個 Android SDK 套件？](install-android-sdk-packages.md)

安裝 Android SDK，不會自動加入開發所有最小必要的的封裝。 個別的開發人員需要有所不同，本指南將告訴您通常會需要使用 Xamarin.Android 開發的封裝。

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[在哪裡可以設定 Android SDK 的位置？](android-sdk-location.md)

本指南說明這兩個預設設定的 Android sdk，應該適用於大部分的設定。以及如何視需要變更這些預設值，在 Visual Studio 中的 Mac 或 Visual Studio。

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[如何更新 Java 開發套件 (JDK) 版本？](update-jdk.md)

本文說明如何更新在 Windows 和 mac 上的 Java Development Kit (JDK) 版本

### <a name="can-i-use-java-development-kit-jdk-version-9jdk9-errorsmd"></a>[可以使用 Java 開發套件 (JDK) 9 版嗎？](jdk9-errors.md)

Xamarin.Android 需要 JDK 8，而不是 JDK 9。 本文列出一些常見的錯誤訊息，您可能會查看是否已安裝作業 JDK 9，以及如需檢查的 JDK 版本的指示。


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[如何可以手動安裝 Xamarin.Android.Support 套件所需的 Android 支援程式庫？](install-android-support-library.md)

本指南提供的範例步驟安裝`Xamarin.Android.Support.v4`支援 Windows 和 mac 上的程式庫

### <a name="how-do-i-install-google-play-services-in-an-emulatorinstall-gpsmd"></a>[如何在模擬器中安裝 Google Play 服務？](install-gps.md)

這個指南會連結上安裝 Visual Studio 的 Android 模擬器中的 Google Play 服務的資訊。

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[在 Windows 上對 Android 進行偵錯需要哪些 USB 裝置？](android-drivers-debug-windows.md)

若要偵錯 Android 裝置上開發 Windows; 中時您必須安裝相容的 USB 驅動程式。 Android SDK Manager 根據預設，加入 Nexus 裝置的支援包括 「 Google USB 驅動程式 」。
其他裝置則需要特別裝置製造商所發行的 USB 驅動程式。 本指南提供關於尋找這些驅動程式也為另一種測試方法的資訊。

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[是否可能從 Windows VM 連線到在 Mac 上執行的 Android 模擬器？](connect-android-emulator-mac-windows.md)

使用 Google Android 模擬器時，本指南涵蓋的方法。

## <a name="general-questions"></a>一般問題

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[如何將 Android NUnit 測試專案自動化？](automate-android-nunit-test.md)

本指南涵蓋設定 Android NUnit 測試專案中，步驟_不_Xamarin.UITest 專案。 您可以找到 Xamarin.UITest 指南[這裡](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)。

### <a name="how-do-i-enable-intellisense-in-android-axml-filesenable-axml-intellisensemd"></a>[如何啟用 Android.axml 檔案中的 Intellisense？](enable-axml-intellisense.md)

本指南說明如何啟動 Visual Studio Intellisense for Android.axml 檔案。

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[為何我的 Android 發行組建無法連線到網際網路？](android-internet.md)

此問題最常見的原因在於**網際網路**權限會自動包含在偵錯組建，但必須手動設定發行組建。 本指南說明如何啟用的發行組建的權限。

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[更聰明的 Xamarin Android 支援 v4 / v13 NuGet 套件](android-support-v4v13-libraries.md)

`Support-v4` 和`Support-v13`不能使用一起在相同的應用程式，也就是說，它們是互斥。 這是因為`Support-v13`實際包含的所有類型和實作`Support-v4`。 如果您再次嘗試同時在相同的專案參考您將會遇到重複的型別錯誤。

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[如何解決 PathTooLongException 錯誤？](path-too-long-exception.md)

這篇文章說明如何解決**PathTooLongException**建置 Xamarin.Android 專案時可能發生的錯誤。



## <a name="deprecated"></a>已被取代

> [!NOTE]
> 以下文章適用於 Xamarin 的最新版的已解決的問題。 但是，若發生此問題，軟體的最新版本，請檔案[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本資訊和完整建置記錄檔輸出。

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[哪些版本的 Xamarin.Android 新增了 Lollipop 支援？](xa-lollipop.md)

本指南原本是針對 Android L 預覽所撰寫。Xamarin.Android 4.17 加入 L Preview 的 Android 支援 （& s） Xamarin.Android 4.20 加入棒棒糖符號的 Android 支援。

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat - 找不到符合所提供名稱：attr 'android:actionModeShareDrawable' 的資源](missing-action-mode-share-drawable.md)

如果遺漏了某些必要的 Android SDK pacakages Xamarin 的舊版本可能會發生這個錯誤。

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[調整適用於 Android Designer 的 JAVA 記憶體參數](android-designer-java-memory.md)

啟動時所使用的預設記憶體參數`java`處理 Android 設計工具可能與某些系統組態不相容。 可以自訂啟動 Xamarin Studio 5.7.2.7 和 Xamarin for Visual Studio 3.9.344 這些設定，根據每個專案。

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[我的 Android Resource.designer.cs 檔案不會更新](resource-designer-wont-update.md)

Xamarin.Studio 5.1 中的 bug 之前損毀.csproj 檔案部分或完全刪除.csproj 檔案中的 xml 程式碼。 這會導致重要部分 Android 的建置系統 （例如更新 Android Resource.designer.cs） 失敗。 從穩定 5.1.4 開始發行版本上年 7 月 15，已修正這個問題;但在許多情況下的專案檔必須以手動方式，本指南中所述的修復。




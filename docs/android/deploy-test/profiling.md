---
title: 分析 Android 應用程式
description: 本指南說明如何使用分析工具來檢查 Android 應用程式的效能和記憶體使用量。
ms.topic: article
ms.prod: xamarin
ms.assetid: 8C823FEE-A6F6-4C31-9EB6-E51407A2FD8E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 400075a1cbd2303f2ecddb9b1cc9465bbcbde32d
ms.sourcegitcommit: f255aa286bd52e8a80ffa620c2e93c97f069f8ec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68680269"
---
# <a name="profiling-android-apps"></a>分析 Android 應用程式

將您的應用程式部署至 App store 之前，必須先找出並修正任何效能瓶頸、過多的記憶體使用量問題，或網路資源使用效率不佳等問題。 兩個分析工具可用於此用途：

-  Xamarin Profiler 
-  Android Studio 中的 Android Profiler

本指南介紹 Xamarin Profiler，並提供開始使用 Android Profiler 的詳細資訊。

 
## <a name="xamarin-profiler"></a>Xamarin Profiler

Xamarin Profiler 是獨立的應用程式，與 Visual Studio 和 Visual Studio for Mac 整合以從 IDE 中分析 Xamarin 應用程式。 如需使用 Xamarin Profiler 的詳細資訊，請參閱 [Xamarin Profiler](~/tools/profiler/index.md)。

> [!NOTE]
> 您必須是 [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) 的訂閱者才能解除鎖定 Visual Studio Enterprise on Windows 或 Visual Studio for Mac 中的 Xamarin Profiler 功能。
 
## <a name="android-studio-profiler"></a>Android Studio Profiler

Android Studio 3.0 和更新版本包含 Android Profiler 工具。 您可以使用 Android Profiler 測量使用 Visual Studio 建置的 Xamarin Android 應用程式效能 &ndash; 不需要 Visual Studio Enterprise 授權。 不過，與 Xamarin Profiler 不同之處在於 Android Profiler 未與 Visual Studio 整合，而且只能用來分析事先建置且已匯入 Android Profiler 的 Android 應用程式套件 (APK)。

### <a name="launching-a-xamarin-android-app-in-android-profiler"></a>在 Android Profiler 中啓動 Xamarin Android 應用程式

下列步驟說明如何在 Android Studio 的 Android Profiler 工具中啟動 Xamarin Android 應用程式。 在下面的範例螢幕擷取畫面中，Xamarin Forms [XamagonXuzzle](https://docs.microsoft.com/samples/xamarin/mobile-samples/liveplayer-xamagonxuzzlelp/) 應用程式已建立，且使用 Android Profiler 分析：

1.  在 Android 專案建置選項中，停用 [使用共用執行階段]  。 這可確保在不依賴共用的開發階段 Mono 執行階段的情況下，建置 Android 應用程式套件 (APK)。

    ![停用 [使用共用執行階段]](profiling-images/vswin/01-turn-off-shared-runtime.png)

2.  建置**偵錯**應用程式並將它部署到實體裝置或模擬器。 這會導致建置 APK 的已簽署**偵錯**版本。
    在 **XamagonXuzzle** 的範例中，產生的 APK 名為 **com.companyname.XamagonXuzzle Signed.apk**。

3.  開啟專案資料夾並巡覽至 **bin/Debug**。 在這個資料夾中，找到應用程式的 **Signed.apk** 版本，並將它複製到方便存取的位置 (例如桌面)。 在下列螢幕擷取畫面中，找到 APK **com.companyname.XamagonXuzzle Signed.apk** 並複製到桌面：

    [![偵錯已簽署 APK 檔案的位置](profiling-images/vswin/02-locating-the-debug-apk-sml.png)](profiling-images/vswin/02-locating-the-debug-apk.png#lightbox)

4.  啓動 Android Studio 並選取**設定檔或偵錯 APK**：

    ![從 Android Studio 啟動畫面啓動分析工具](profiling-images/vswin/03-android-studio.png)

5.  在 選取 APK 檔案  對話方塊中，巡覽至您之前建立並複製的 APK。 選取 APK 然後按一下 [確定]  ： 
    
    ![在 [選取 APK 檔案] 對話方塊中選取 APK](profiling-images/vswin/04-select-apk-dialog.png)

6.  Android Studio 會載入 APK 並反組譯 **classes.dex**：

    ![設定 APK](profiling-images/vswin/05-setting-up-the-apk.png)

7.  載入 APK 後，Android Studio 會顯示以下 APK 專案畫面。 以滑鼠右鍵按一下左側樹狀檢視中的應用程式名稱，然後選取 [Open Module Settings] \(開啟模組設定\)  ：

    [![[Open Module Settings] \(開啟模組設定\) 功能表項目的位置](profiling-images/vswin/06-open-module-settings-sml.png)](profiling-images/vswin/06-open-module-settings.png#lightbox)

8.  巡覽至 [專案設定] > [模組]  ，選取應用程式的 **-Signed** 節點，然後按一下 [&lt;無 SDK&gt;]  ：

    [![巡覽至 SDK 設定](profiling-images/vswin/07-project-settings-modules-sml.png)](profiling-images/vswin/07-project-settings-modules.png#lightbox)

9.  在 [Module SDK] \(模組 SDK\)  下拉式功能表中，選取用來建置應用程式的 Android SDK 層級 (本範例使用 API 層級 26 建置 **XamagonXuzzle**)：

    [![設定專案 SDK 層級](profiling-images/vswin/08-project-sdk-level-sml.png)](profiling-images/vswin/08-project-sdk-level.png#lightbox)

    按一下 [套用]  和 [確定]  儲存這個設定。

10. 從工具列圖示啟動分析工具：

    [![分析工具工具列圖示的位置](profiling-images/vswin/09-launch-profiler-sml.png)](profiling-images/vswin/09-launch-profiler.png#lightbox)

11. 選取執行/分析應用程式的部署目標，然後按一下 [確定]  。 部署目標可以是在模擬器中執行的實體裝置或虛擬裝置。 本範例中使用 Nexus 5X 裝置：

    ![選取部署目標](profiling-images/vswin/10-select-deployment-target.png)

12. 分析工具啟動後，會花數秒鐘連接到部署裝置和應用程式處理序。 在安裝 APK 時，Android Profiler 會報告**無連接的裝置**和**無可偵錯的處理序**。

    [![分析工具安裝 APK](profiling-images/vswin/11-no-connected-devices-sml.png)](profiling-images/vswin/11-no-connected-devices.png#lightbox)

13. 幾秒之後，Android Profiler 會完成 APK 安裝並啟動 APK，報告裝置名稱以及正在分析的應用程式處理序名稱 (在本範例中分別是 **LGE Nexus 5X** 和 **com.companyname.XamagonXuzzle**)：

    [![啓動後的分析工具視窗](profiling-images/vswin/12-profiler-starts-sml.png)](profiling-images/vswin/12-profiler-starts.png#lightbox)

14. 找到裝置和可偵錯的處理程序之後，Android Profiler 就會開始分析應用程式：

    [![正在執行的應用程式分析工具顯示畫面](profiling-images/vswin/13-profiler-running-sml.png)](profiling-images/vswin/13-profiler-running.png#lightbox)

15. 如果您點選 **XamagonXuzzle** 上的 [RANDOMIZE]  按鈕 (這會造成它移位並隨機化磚)，就會看到 CPU 使用量在應用程式隨機間隔期間增加：

    [![點選 [RANDOMIZE] 按鈕後的 CPU 使用量](profiling-images/vswin/14-tap-randomize-sml.png)](profiling-images/vswin/14-tap-randomize.png#lightbox)


### <a name="using-the-android-profiler"></a>使用 Android Profiler

使用 Android Profiler 的詳細資訊包含在 [Android Studio 文件](https://developer.android.com/studio/profile/android-profiler.html)中。
下列為 Xamarin Android 開發人員感興趣的主題：

-   [CPU 分析工具](https://developer.android.com/studio/profile/cpu-profiler.html) &ndash; 說明如何檢查即時的應用程式 CPU 使用量和執行緒活動。

-   [記憶體分析工具](https://developer.android.com/studio/profile/memory-profiler.html) &ndash; 顯示應用程式記憶體使用量的即時圖形，包含記錄記憶體配置以供分析的按鈕。

-   [網路分析工具](https://developer.android.com/studio/profile/network-profiler.html) &ndash; 顯示應用程式傳送和接收資料的即時網路活動。

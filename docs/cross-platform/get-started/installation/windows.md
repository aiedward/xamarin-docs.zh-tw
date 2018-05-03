---
title: 在 Visual Studio 2017 中安裝 Xamarin
ms.prod: xamarin
ms.assetid: E20D4463-368E-4B60-A059-F50DB8C5552D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 09/29/2017
ms.openlocfilehash: 0f1c014f316ff4f3eb7341fa9815475175d11937
ms.sourcegitcommit: dc6ccf87223942088ca926c0dadd5b5478c683cb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2018
---
# <a name="installing-xamarin-in-visual-studio-2017"></a>在 Visual Studio 2017 中安裝 Xamarin

<a name="requirements" />

## <a name="requirements"></a>需求

以下是在 Visual Studio 2017 中安裝 Xamarin 的必要項目：

1. Windows 7 或更新版本。

2. Visual Studio 2017 (Community、Professional 或 Enterprise)。

3. Xamarin for Visual Studio。

如需安裝和使用 Xamarin 的必要條件詳細資訊，請參閱[系統需求](~/cross-platform/get-started/requirements.md)。

<a name="installation" />

## <a name="installation"></a>安裝

Xamarin 可以隨著新的 Visual Studio 2017 安裝一起安裝。
若要這麼做，請使用下列步驟：

1. 從 [Visual Studio](https://www.visualstudio.com/vs/) 頁面下載 Visual Studio 2017 Community、Visual Studio Professional 或 Visual Studio Enterprise (下載連結是在底部提供)。

2. 按兩下已下載的套件來啟動安裝。

3. 從安裝畫面選取 [使用 .NET 進行行動開發] 工作負載： 

    [![[工作負載] 畫面上的 [使用 .NET 進行行動開發] 選項](windows-images/01-mobile-dev-workload-sml.png)](windows-images/01-mobile-dev-workload.png#lightbox)

4. 選取 [使用 .NET 進行行動開發] 後，查看右側的 [摘要] 面板。 您可以在這裡取消選取不想安裝的行動開發選項。 預設會安裝下列螢幕擷取畫面所示的所有選項 (**Xamarin Workbooks**、**Xamarin Profiler**、**Xamarin Remoted Simulator**、**Android NDK**、**Android SDK**、**Java SE 開發套件**、**Google Android Emulator**、**F# 支援**以及 **Intel HAXM**)：

    ![[摘要] 面板列出要安裝的 Xamarin 選項](windows-images/02-summary.png)

5. 當您準備好開始 Visual Studio 2017 安裝時，請按一下右下角的 [安裝] 按鈕：

    ![[安裝] 按鈕的位置](windows-images/03-click-install.png)

   根據您安裝的 Visual Studio 2017 版本，安裝程序可能需要很長一段時間才能完成。 您可以使用進度列來監視安裝：

    ![安裝期間的進度列範例螢幕擷取畫面](windows-images/04-progress-bars.png)

6. Visual Studio 2017 安裝完成之後，請按一下 [啟動] 按鈕來啟動 Visual Studio：

    ![[啟動] 按鈕的位置](windows-images/05-launch.png)

<a name="vs2017" />

### <a name="adding-xamarin-to-visual-studio-2017"></a>將 Xamarin 加入至 Visual Studio 2017

如果已安裝 Visual Studio 2017，您可以重新執行 Visual Studio 2017 安裝程式修改工作負載 (如需詳細訊，請參閱[修改 Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio))，來新增 Xamarin。 接著，請依照上面所列的步驟來安裝 Xamarin。

如需下載和安裝 Visual Studio 2017 的詳細資訊，請參閱[安裝 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。


### <a name="verifying-installation"></a>驗證安裝

在 Visual Studio 2017，您可以按一下 [說明] 功能表，確認是否已安裝 Xamarin。 如果已安裝 Xamarin，您應會看見 [Xamarin] 功能表項目，如此螢幕擷取畫面所示：

![顯示在 [說明] 功能表上的 Xamarin 功能表項目](windows-images/12-xamarin-menu-item.png)

如果您使用舊版的 Visual Studio，可以按一下 [說明] > [關於 Microsoft Visual Studio]，然後捲動已安裝產品的清單，查看是否已安裝 Xamarin：

![Visual Studio 已安裝產品的畫面](windows-images/13-xamarin-is-installed.png)

如需尋找版本資訊的詳細資訊，請參閱[哪裡可以找到版本資訊和記錄？](~/cross-platform/troubleshooting/questions/version-logs.md)

<a name="nextsteps" />

## <a name="next-steps"></a>後續步驟

在 Visual Studio 2017 中安裝 Xamarin 可讓您開始為應用程式撰寫程式碼，但不需要其他設定，即可建置應用程式並部署至模擬器 (simulator)、模擬器 (emulator) 和裝置。 請前往下列指南以完成安裝並開始建置跨平台應用程式。

### <a name="ios"></a>iOS

如需詳細資訊，請參閱[在 Windows 上安裝 Xamarin.iOS](~/ios/get-started/installation/windows/index.md) 指南。 

1. [安裝 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)
2. [將 Visual Studio 連線至 Mac 組建主機](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
3. [iOS 開發人員設定](~/ios/get-started/installation/device-provisioning/index.md)：在裝置上執行應用程式的必要項目
5. [遠端 iOS 模擬器](~/tools/ios-simulator.md)
6. [Xamarin.iOS for Visual Studio 簡介](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)

### <a name="android"></a>Android

如需詳細資訊，請參閱[在 Windows 上安裝 Xamarin.Android](~/android/get-started/installation/windows.md) 指南。

1. [Xamarin.Android 組態](~/android/get-started/installation/windows.md#configuration)
2. [使用 Xamarin Android SDK 管理員](~/android/get-started/installation/android-sdk.md?ide=vs) \(英文\)
3. [Android SDK 模擬器](~/android/get-started/installation/android-emulator/index.md) \(英文\)
4. [設定您的裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md) \(英文\)

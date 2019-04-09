---
title: 解除安裝 Xamarin
description: 本文件描述如何在 Mac 和 Windows 上解除安裝 Xamarin。 提供有關解除安裝 Mono、Xamarin.Android、Xamarin.iOS 和其他工具的特定指示。
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: asb3993
ms.author: amburns
ms.date: 04/08/2017
ms.openlocfilehash: 6a0fe95d6d471bfb464234f0540b8398acb22e6a
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854765"
---
# <a name="uninstalling-xamarin"></a>解除安裝 Xamarin

本指南說明如何在 Windows 上，從 macOS 或 Visual Studio 移除 Xamarin。

如有必要使用通用安裝程式重新安裝 Xamarin，建議您一律先重新啟動電腦。

## <a name="uninstalling-xamarin-on-macos"></a>在 macOS 上將 Xamarin 解除安裝

瀏覽至相關章節，即可使用本指南來個別解除安裝每個產品。 您可完全遵循本指南，將整個 Xamarin 工具組 (包括列出的產品) 解除安裝：

- [Mono](#uninstallmono)
- [Xamarin.Android](#uninstallandroid)
- [Xamarin.iOS](#uninstallios)
- [Xamarin.Mac](#uninstallmac)
- [活頁簿](#uninstallworkbooks)
- [Xamarin Profiler](#uninstallprofiler)
- [Installer](#uninstallinstaller)

> [!TIP]
> 我們為您提供了[解除安裝指令碼](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh)，當您要將 Xamarin 從 macOS 電腦移除時可加以利用。 如需有關如何使用指令碼的詳細資訊，請參閱本指南中的[使用解除安裝指令碼](#uninstallscript)一節。

### <a name="uninstalling-visual-studio-for-mac"></a>解除安裝 Visual Studio for Mac

從 Mac 將 Xamarin 解除安裝的第一個步驟是找出 **/Applications** 目錄中的 **Visual Studio.app**，並將其拖曳至 [資源回收筒]。 或者以滑鼠右鍵按一下並選取 [移至垃圾桶]，如下圖所示：

![將 Visual Studio 應用程式移至垃圾桶](uninstalling-xamarin-images/uninstall-image1.png)

刪除此應用程式套件組合會移除 Visual Studio for Mac，即使檔案系統上仍然有其他 Xamarin 相關檔案也是一樣。

若要移除 Visual Studio for Mac 的所有追蹤，請在終端機中執行下列命令：

```bash
sudo rm -rf "/Applications/Visual Studio.app"
rm -rf ~/Library/Caches/VisualStudio
rm -rf ~/Library/Preferences/VisualStudio
rm -rf ~/Library/Preferences/Visual\ Studio
rm -rf ~/Library/Logs/VisualStudio
rm -rf ~/Library/VisualStudio
rm -rf ~/Library/Preferences/Xamarin/
rm -rf ~/Library/Developer/Xamarin
rm -rf ~/Library/Application\ Support/VisualStudio
rm -rf ~/Library/Application\ Support/VisualStudio/7.0/LocalInstall/Addins/
```

> [!NOTE]
> 如需解除安裝 Visual Studio for Mac 的資訊，請參閱 docs.microsoft.com 上的[解除安裝](https://docs.microsoft.com/visualstudio/mac/uninstall)指南

<a name="uninstallmono" />

### <a name="uninstall-mono-sdk-mdk"></a>解除安裝 Mono SDK (MDK)

Mono 是 .NET Framework 的開放原始碼實作，並供所有 Xamarin 產品 (Xamarin.iOS、Xamarin.Android 和 Xamarin.Mac) 使用，以允許使用 C# 開發這些平台。

> [!WARNING]
> 除了 Xamarin 之外，也有其他應用程式使用 Mono，例如 Unity。 請先確認沒有與 Mono 的其他相依性，再將它解除安裝。

若要移除 Mono 架構，請在終端機中執行下列命令：

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
sudo rm /etc/paths.d/mono-commands
```

<a name="uninstallandroid" />

### <a name="uninstall-xamarinandroid"></a>解除安裝 Xamarin.Android

在使用 Xamarin.Android 時有幾個必要項目，例如 Android SDK 與 Java SDK，而在將 Xamarin.Android 解除安裝時則必須將這些項目移除。 本節會引導您將所有必要組建解除安裝。

若要移除 Xamarin.Android，請在終端機中執行以下命令：

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

#### <a name="uninstall-android-sdk-and-java-sdk"></a>解除安裝 Android SDK 和 Java SDK

需要有 Android SDK，才能開發 Android 應用程式。 若要完全移除 Android SDK 的所有部分，請在 **~/Library/Developer/Xamarin/** 找出檔案，並將它移至 [垃圾]。

您不需要解除安裝 Java SDK (JDK)，因為它已預先封裝為 Mac OS X 的一部分。

#### <a name="uninstall-android-avd"></a>解除安裝 Android AVD

> [!WARNING]
> Visual Studio for Mac 外部有其他應用程式也使用 Android AVD 以及這些額外的 Android 元件，例如 Android Studio。
> 移除這個目錄可能會造成專案在 Android Studio 內中斷。 

若要移除所有 Android AVD 與其他 Android 元件，請使用下列命令：

```bash
rm -rf ~/.android
```

若只要移除 Android AVD，則請使用下列命令：

```bash
rm -rf ~/.android/avd
```

<a name="uninstallios" />

### <a name="uninstall-xamarinios"></a>解除安裝 Xamarin.iOS

Xamarin.iOS 可讓您使用 C# 或 F# 進行 iOS 應用程式開發。 若要從電腦將 Xamarin.iOS 解除安裝，請遵循下列步驟：

若要移除所有 Xamarin.iOS 檔案，請在終端機中使用下列命令：

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
sudo pkgutil --forget com.xamarin.xamarin.ios.pkg
```

<a name="uninstallmac" />

### <a name="uninstall-xamarinmac"></a>解除安裝 Xamarin.Mac

您可從 Mac 使用下列兩個命令分別根除產品與授權，以從電腦移除 Xamarin.Mac：

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

### <a name="uninstall-workbooks"></a>解除安裝 Workbooks

若要移除 Xamarin Workbooks 1.2.2 版及更新版本，請在終端機內使用下列命令：

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

針對先前版本，請參閱 [Workbooks](~/tools/workbooks/install.md#uninstall-macos) 解除安裝指南。

<a name="uninstallprofiler" />

### <a name="uninstall-the-xamarin-profiler"></a>解除安裝 Xamarin Profiler

若要移除 Xamarin Profiler，請在終端機中使用下列命令：

```bash
sudo rm -rf "/Applications/Xamarin Profiler.app"
```

<a name="uninstallinstaller" />

### <a name="uninstall-the-xamarin-installer"></a>解除安裝 Xamarin Installer

若要移除 Xamarin 通用安裝程式的所有追蹤，請使用下列命令：

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Caches/VisualStudioInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Logs/VisualStudioInstaller/
rm -rf ~/Library/Preferences/Xamarin/
rm -rf "~/Library/Preferences/Visual Studio/"
```

<a name="uninstallscript" />

### <a name="using-the-uninstall-script"></a>使用解除安裝指令碼

此解除安裝指令碼可讓您一次將 Visual Studio for Mac 及其相關 Xamarin 元件解除安裝。

此指令碼包含可在本文中找到的大多數命令。 指令碼中有兩個主要省略項目，並且因可能的外部相依性而未包含：

- 將 Mono 解除安裝
- 將 Android AVD 解除安裝

若要執行指令碼，請執行下列步驟：

1. 以滑鼠右鍵按一下指令碼，並選取 [另存新檔...] 以將檔案儲存至 Mac。

2.  開啟**終端機**，並將工作目錄變更為已下載指令碼的位置：

        $ cd /location/of/file

3. 將指令碼設為可執行，並使用 **sudo** 執行它：

        $ chmod +x ./xamarin_uninstall.sh
        $ sudo ./xamarin_uninstall.sh

4. 最後，刪除解除安裝指令碼。

此時，Xamarin 應該已從您的電腦解除安裝。

<a name="uninstallwindows" />

## <a name="uninstalling-xamarin-on-windows"></a>在 Windows 上解除安裝 Xamarin

以下項目支援 Xamarin：

- [Visual Studio 2019 和 Visual Studio 2017](#uninstallvs2017)
- [Visual Studio 2015](#uninstallvs2015)
- [Visual Studio 2013](#uninstallvs2015) [**不支援**]
- [Xamarin Studio](#uninstallxamarinstudio) [**不支援**]

<a name="uninstallvs2017" />

### <a name="visual-studio-2019-and-visual-studio-2017"></a>Visual Studio 2019 和 Visual Studio 2017

從 Visual Studio 2019 和使用安裝程式應用程式的 Visual Studio 2017 將 Xamarin 解除安裝：

1. 使用 [開始] 功能表來開啟 **Visual Studio 安裝程式**。

2. 按下您想要變更之執行個體的 [修改] 按鈕。

    [![](uninstalling-xamarin-images/vs2017-02-sml.png "按下 [修改] 按鈕")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. 在 [工作負載] 索引標籤中，取消選取 [使用 .NET 進行行動開發] 選項 (位於**行動與遊戲**區塊)。

    [![](uninstalling-xamarin-images/vs2017-03-sml.png "取消核取 行動裝置開發工作負載")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. 按一下視窗右下角的 [修改] 按鈕。

5. 安裝程式會移除取消選取的元件 (Visual Studio 2017 必須在安裝程式進行任何變更之前關閉)。

    [![](uninstalling-xamarin-images/vs2017-04-sml.png "按下 [修改] 按鈕")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

個別的 Xamarin 元件 (例如 Profiler 或 Workbooks) 可透過在步驟 3 中切換至 [個別元件] 索引標籤，然後取消選取特定的元件來解除安裝：

[![](uninstalling-xamarin-images/vs2017-components-sml.png "解除安裝個別元件")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

若要完全解除安裝 Visual Studio 2017，請從 [啟動] 按鈕旁邊的三列功能表中選擇 [解除安裝]。

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "完全解除安裝 Visual Studio")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

> [!IMPORTANT]
> 若您並存 (SxS) 安裝兩個 (或更多個) Visual Studio 執行個體 (例如發行版和預覽版)，則將一個執行個體解除安裝可能會移除另一個 Visual Studio 執行個體中的一些 Xamarin 功能，包括：
>
> - Xamarin Profiler
> - Xamarin Workbooks/Inspector
> - Xamarin Remote iOS Simulator
> - Apple Bonjour SDK
>
> 在特定情況下，解除安裝其中一個並存 (SxS) 執行個體可能會不正確的移除這些功能。 這可能會降低移除並存執行個體後仍然留在系統上之 Visual Studio 執行個體上 Xamarin 平台的執行效能。
>
>您可以透過在 Visual Studio 安裝程式中執行 [修復] 選項來解決這個問題。該選項會重新安裝遺失的元件。


## <a name="uninstalling-older-and-unsupported-products"></a>將較舊及不支援的產品解除安裝

<a name="uninstallvs2015"></a>

### <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 和更早版本

若要完全移除 Visual Studio 2015，請使用 [visualstudio.com 上的支援解答](https://visualstudio.microsoft.com/vs/support/vs2015/uninstall-visual-studio-2015/)。

您可以透過**控制台**從 Windows 電腦解除安裝 Xamarin。 巡覽至 [程式和功能] 或 [程式] > [解除安裝程式]，如下圖所示：

 [![](uninstalling-xamarin-images/image3.png "瀏覽至程式和功能 或程式解除安裝程式，如下所示")](uninstalling-xamarin-images/image3.png#lightbox) 

從 [控制台] 將以下存在的所有項目解除安裝：

- Xamarin
- Xamarin for Windows
- Xamarin.Android
- Xamarin.iOS
- Xamarin for Visual Studio

在總管中，刪除 Xamarin Visual Studio 延伸模組資料夾 (所有版本，包含 Program Files 與 Program Files (x86)) 中的所有剩餘檔案：

``` 
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

刪除 Visual Studio 的 MEF 元件快取目錄，該目錄應該在下列位置：

``` 
%LOCALAPPDATA%\Microsoft\VisualStudio\1*.0\ComponentModelCache
```

簽入 **VirtualStore** 目錄以查看 Windows 是否已為 **Extensions\Xamarin** 或 **ComponentModelCache** 目錄在此處存放任何覆疊檔案：

``` 
%LOCALAPPDATA%\VirtualStore
```

開啟登錄編輯程式 (regedit) 並尋找下列機碼：

``` 
HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls
```

尋找並刪除所有符合此模式的項目：

``` 
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

尋找此機碼：

``` 
HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\1*.0\ExtensionManager\PendingDeletions
```

刪除所有看起來可能與 Xamarin 相關的項目。 例如，任何包含 `mono` 或 `xamarin` 字詞的項目。

開啟系統管理員 cmd.exe 命令提示字元，然後對每個安裝的 Visual Studio 版本執行 `devenv /setup` 與 `devenv /updateconfiguration` 命令。 例如，若為 Visual Studio 2015：

```cmd
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
```

<a name="uninstallxamarinstudio"></a>

### <a name="uninstall-xamarin-studio-on-windows"></a>將 Windows 上的 Xamarin Studio 解除安裝

您可透過 [控制台] 將 Xamarin Studio 解除安裝。 巡覽至 [程式和功能] 或 [程式] > [解除安裝程式] 

若要解除安裝 Xamarin Studio，請在程式清單中尋找 **Xamarin Studio 5.x.x**，然後按一下 [解除安裝] 按鈕。 

### <a name="uninstall-xamarin-studio-on-mac"></a>將 Mac 上的 Xamarin Studio 解除安裝

從 Mac 解除安裝 Xamarin Studio 的第一個步驟是找出 **/Applications** 目錄中的 **Xamarin Studio.app**，並將它拖曳至**垃圾桶**。 或者，以滑鼠右鍵按一下並選取 [移至垃圾筒]，如下所示：

 [![](uninstalling-xamarin-images/image1.png "或者，以滑鼠右鍵按一下並選取 移至 資源回收筒，如下所示")](uninstalling-xamarin-images/image1.png#lightbox)

雖然刪除此應用程式套件組合便會移除 Xamarin Studio；然而，檔案系統上還是會有其他與 Xamarin 相關的檔案。

若要完整移除 Xamarin Studio，請在終端機中執行下列命令：

```bash
sudo rm -rf "/Applications/Xamarin Studio.app"
rm -rf ~/Library/Caches/XamarinStudio-*
rm -rf ~/Library/Preferences/XamarinStudio-*
rm -rf ~/Library/Logs/XamarinStudio-*
rm -rf ~/Library/XamarinStudio-*
```

## <a name="summary"></a>總結

本文提供的指示，可讓您透過使用終端機命令，將 Mac 上的 Xamarin 完全解除安裝。 此外也提供從 Windows 電腦將 Xamarin 解除安裝的指示，方法是透過 [程式和功能] 選項 (適用於 Visual Studio 2015 及更舊版本)，以及使用適用於 Visual Studio 2017 的 **Visual Studio 安裝程式**。


## <a name="related-links"></a>相關連結

- [解除安裝指令碼 （範例）](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh)

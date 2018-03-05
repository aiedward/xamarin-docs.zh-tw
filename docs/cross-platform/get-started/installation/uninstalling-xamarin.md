---
title: "解除安裝 Xamarin"
description: "從電腦解除安裝 Xamarin 產品"
ms.topic: article
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/08/2017
ms.openlocfilehash: d2db4af7dd13611075bc2b100470b5fb3ba83118
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="uninstalling-xamarin"></a>解除安裝 Xamarin

> [!IMPORTANT]
> 本文說明如何從 Mac 或 Windows 電腦解除安裝 Xamarin Studio 或其他 Xamarin 產品。 如需解除安裝 Visual Studio for Mac 的資訊，請參閱 docs.microsoft.com 上的[解除安裝](https://docs.microsoft.com/visualstudio/mac/uninstall)指南

# <a name="overview"></a>總覽

有許多 Xamarin 產品可啟用跨平台應用程式開發，包含 Xamarin Studio 這類獨立應用程式，以及 Visual Studio 中的 Xamarin 支援這類其他應用程式的延伸模組。

本指南將說明如何在 macOS 上移除 Xamarin 功能，或是從 Windows 上的 Visual Studio 移除：

1.  [解除安裝 Xamarin Studio](#uninstallxamarinstudio)
  1.  [解除安裝 Mono](#uninstallmono)
  1.  [解除安裝 Xamarin.Android](#uninstallandroid)
  1.  [解除安裝 Xamarin.iOS](#uninstallios)
  1.  [解除安裝 Xamarin.Mac](#uninstallmac)
  2.  [解除安裝 Inspector 及 Workbooks](#uninstallworkbooks)
1.  [從 Windows 解除安裝 Xamarin](#uninstallwindows)
  1.  [從 Visual Studio 2015 或更早版本解除安裝 Xamarin](#uninstallvs2015)
  1.  [從 Visual Studio 2017 解除安裝 Xamarin](#uninstallvs2017)
1.  [解除安裝 Visual Studio for Mac](#uninstallvsmac)

若必須使用 Universal Installer 重新安裝 Xamarin，建議您先重新啟動電腦。

# <a name="uninstalling-xamarin-on-mac"></a>在 Mac 上解除安裝 Xamarin

瀏覽至相關章節，即可使用本指南來個別解除安裝每個產品。 您可以藉由完全遵循本指南來解除安裝整個 Xamarin 工具組。

如需使用[解除安裝指令碼](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh)的協助，請移至本指南底部的[使用解除安裝指令碼](#uninstallscript)。

<a name="uninstallxamarinstudio" />

## <a name="uninstall-xamarin-studio"></a>解除安裝 Xamarin Studio

從 Mac 解除安裝 Xamarin Studio 的第一個步驟是找出 **/Applications** 目錄中的 **Xamarin Studio.app**，並將它拖曳至**垃圾桶**。 或者，以滑鼠右鍵按一下並選取 [移至垃圾筒]，如下所示：

 [ ![](uninstalling-xamarin-images/image1.png "或者，以滑鼠右鍵按一下並選取 [移至垃圾桶]，如此圖所示")](uninstalling-xamarin-images/image1.png)

雖然刪除此應用程式套件組合便會移除 Xamarin Studio；然而，檔案系統上還是會有其他與 Xamarin 相關的檔案。

若要完整移除 Xamarin Studio，請在終端機中執行下列命令：

```bash
sudo rm -rf "/Applications/Xamarin Studio.app"
rm -rf ~/Library/Caches/XamarinStudio-*
rm -rf ~/Library/Preferences/XamarinStudio-*
rm -rf ~/Library/Logs/XamarinStudio-*
rm -rf ~/Library/XamarinStudio-*
```

<a name="uninstallmono" />

## <a name="uninstall-mono-sdk-mdk"></a>解除安裝 Mono SDK (MDK)

Mono 是 Microsoft .NET Framework 的開放原始碼實作，並供所有 Xamarin 產品 (Xamarin.iOS、Xamarin.Android 和 Xamarin.Mac) 使用，以允許使用 C# 開發這些平台。

> [!IMPORTANT]
> 注意：除了 Xamarin 之外，也有其他應用程式使用 Mono，例如 Unity。 請先確認沒有與 Mono 的其他相依性，再將它解除安裝。

若要從電腦中移除 Mono Framework，請在終端機中執行下列命令：

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
```

<a name="uninstallandroid" />

## <a name="uninstall-xamarinandroid"></a>解除安裝 Xamarin.Android

安裝和使用 Xamarin.Android 需要許多項目，例如 Android SDK 和 Java SDK。 您可以在[手動安裝](https://docs.microsoft.com/visualstudio/mac/installation/)指南中取得更多關於這些必要元件的資訊。

您可以使用下列命令移除 Xamarin.Android：

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

### <a name="uninstall-android-sdk-and-java-sdk"></a>解除安裝 Android SDK 和 Java SDK

需要有 Android SDK，才能開發 Android 應用程式。 若要完全移除 Android SDK 的所有部分，請在 **~/Library/Developer/Xamarin/** 找出檔案，並將它移至**垃圾桶**，如下列所示：

 [ ![ ] (uninstalling-xamarin-images/image2.png "若要完全移除 Android SDK 的所有部分，請找出檔案並將它移至垃圾桶，如此圖所示")](uninstalling-xamarin-images/image2.png)

您不需要解除安裝 Java SDK (JDK)，因為它已預先封裝為 Mac OS X 的一部分。

<a name="uninstallios" />

## <a name="uninstall-xamarinios"></a>解除安裝 Xamarin.iOS

Xamarin.iOS 允許在 Mac 上利用 Xamarin Studio 使用 C# 或 F# 來進行 iOS 應用程式開發。
Xamarin 組建主機也會自動與舊版 Xamarin.iOS 一起安裝，以允許在 Visual Studio 中進行 iOS 開發。 若要從電腦解除安裝兩者，請遵循下列步驟：

在終端機使用下列命令，從檔案系統移除所有 Xamarin.iOS 檔案：

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
```

### <a name="uninstall-the-mac-build-host"></a>解除安裝 Mac 組建主機

注意：若您已更新到 Xamarin 4，這可能已遭移除；請在終端機中執行下列命令來移除組建主機應用程式：

```bash
sudo rm -rf "/Applications/Xamarin.iOS Build Host.app"
```

組建主機處理序或 `launchd` 工作可能仍在執行中或接聽特定連接埠。
您可以藉由在終端機中執行 `launchctl list | grep com.xamarin.mtvs.buildserver` 來檢查其狀態。

```bash
sudo launchctl unload /Library/LaunchAgents/com.xamarin.mtvs.buildserver.plist
sudo rm -f /Library/LaunchAgents/com.xamarin.mtvs.buildserver.plist
```

<a name="uninstallmac" />

## <a name="uninstall-xamarinmac"></a>解除安裝 Xamarin.Mac

在成功解除安裝 Xamarin Studio 後，您便可以在您的 Mac 中分別使用下列兩個命令來根除產品及授權，以從您的電腦移除 Xamarin.Mac：

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

## <a name="uninstall-workbooks-and-inspector"></a>解除安裝 Workbooks 和 Inspector

下列 Bash 命令會移除 Xamarin Inspector 及 Workbooks 版本 1.2.2 及更新版本：

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

針對先前版本，請參閱 [Workbooks](~/tools/workbooks/install.md#uninstall-macos) 解除安裝指南。

## <a name="uninstall-the-xamarin-installer"></a>解除安裝 Xamarin Installer

使用下列命令，移除 Xamarin Universal Installer 的所有追蹤：

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Preferences/Xamarin/
```

<a name="uninstallscript" />

## <a name="using-the-uninstall-script"></a>使用解除安裝指令碼

[解除安裝指令碼](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh)會從電腦移除 Xamarin。 若要使用解除安裝指令碼：

1.  下載解除安裝指令碼並記下下載位置。 根據預設，此為 **/Downloads** 目錄。
1.  開啟**終端機**，並將工作目錄變更為已下載指令碼的位置：

        $ cd /location/of/file

1. 將指令碼設為可執行，並使用 **sudo** 執行它：

        $ chmod +x ./xamarin_uninstall.sh
        $ sudo ./xamarin_uninstall.sh

1. 最後，刪除解除安裝指令碼。

此時，Xamarin 應該已從您的電腦解除安裝。

<a name="uninstallwindows" />

# <a name="uninstalling-xamarin-on-windows"></a>在 Windows 上解除安裝 Xamarin

<a name="uninstallvs2015" />

## <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 和更早版本

您可以透過**控制台**從 Windows 電腦解除安裝 Xamarin。 巡覽至 [程式和功能] 或 [程式] > [解除安裝程式]，如下圖所示：

 [ ![](uninstalling-xamarin-images/image3.png "巡覽至 [程式和功能] 或 [程式]  [解除安裝程式]，如下圖所示")](uninstalling-xamarin-images/image3.png) [ ![](uninstalling-xamarin-images/image4.png "巡覽至 [程式和功能] 或 [程式]  [解除安裝程式]，如下圖所示")](uninstalling-xamarin-images/image4.png)

若要解除安裝 Xamarin Studio，請在程式清單中尋找 **Xamarin Studio 5.x.x**，然後按一下 [解除安裝] 按鈕。 若要移除 Visual Studio 的 Xamarin 延伸模組，請在程式清單中尋找 **Xamarin**，然後按一下 [解除安裝]。 下列螢幕擷取畫面說明這些操作：

 [ ![](uninstalling-xamarin-images/image4a.png "此螢幕擷取畫面說明這些操作")](uninstalling-xamarin-images/image4a.png)

您也可以移除這些程式來完全移除所有 Xamarin 元件：

-  Android SDK


  [ ![](uninstalling-xamarin-images/image5.png "您也可以移除這些程式來完全移除所有 Xamarin 元件")](uninstalling-xamarin-images/image5.png)
-  GTK#


  [ ![](uninstalling-xamarin-images/image6.png "您也可以移除這些程式來完全移除所有 Xamarin 元件")](uninstalling-xamarin-images/image6.png)
-  Xamarin Universal Installer


 [ ![](uninstalling-xamarin-images/image7.png "您也可以移除這些程式來完全移除所有 Xamarin 元件")](uninstalling-xamarin-images/image7.png)
-  Java SDK (在移除此程式時請小心，因為其可能會有其他相依性)


 [ ![](uninstalling-xamarin-images/image8.png "在移除 Java SDK 時請小心，因為其可能會有其他相依性")](uninstalling-xamarin-images/image8.png)

若要完全解除安裝 Visual Studio，請遵循 [Microsoft 的指示](https://msdn.microsoft.com/library/mt720585.aspx)。


<a name="uninstallvs2017" />

# <a name="visual-studio-2017"></a>Visual Studio 2017

Xamarin 可以使用安裝程式應用程式來從 Visual Studio 2017 中解除安裝：

1. 使用 [開始] 功能表來開啟 **Visual Studio 安裝程式**。

  [ ![](uninstalling-xamarin-images/vs2017-01-sml.png "啟動 Visual Studio 安裝程式")](uninstalling-xamarin-images/vs2017-01.png)

1. 按下您想要變更之執行個體的 [修改] 按鈕。

  [ ![](uninstalling-xamarin-images/vs2017-02-sml.png "按下修改按鈕")](uninstalling-xamarin-images/vs2017-02.png)

1. 在 [工作負載] 索引標籤中，取消選取 [使用 .NET 進行行動開發] 選項 (位於**行動與遊戲**區塊)。

  [ ![](uninstalling-xamarin-images/vs2017-03-sml.png "取消核取行動裝置應用程式開發工作負載")](uninstalling-xamarin-images/vs2017-03.png)

1. 按一下視窗右下角的 [修改] 按鈕。
1. 安裝程式會移除取消選取的元件 (Visual Studio 2017 必須在安裝程式進行任何變更之前關閉)。

  [ ![](uninstalling-xamarin-images/vs2017-04-sml.png "按下 [修改] 按鈕")](uninstalling-xamarin-images/vs2017-04.png)

個別的 Xamarin 元件 (例如 Profiler 或 Workbooks) 可透過在步驟 3 中切換至 [個別元件] 索引標籤，然後取消核取特定的元件來解除安裝：

[ ![](uninstalling-xamarin-images/vs2017-components-sml.png "解除安裝個別元件")](uninstalling-xamarin-images/vs2017-components.png)

若要完全解除安裝 Visual Studio 2017，請從 [啟動] 按鈕旁邊的三列功能表中選擇 [解除安裝]。

[ ![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "完全解除安裝 Visual Studio")](uninstalling-xamarin-images/vs2017-uninstall.png)

> [!IMPORTANT]
> **警告：**若您並存 (SxS) 安裝兩個 (或更多個) Visual Studio 執行個體 (例如發行版和預覽版)，則解除安裝一個執行個體可能會移除另一個 Visual Studio 執行個體中的一些 Xamarin 功能，包括：
>
> - Xamarin Profiler
> - Xamarin Workbooks/Inspector
> - Xamarin Remote iOS Simulator
> - Apple Bonjour SDK
>
> 在特定情況下，解除安裝其中一個並存 (SxS) 執行個體可能會不正確的移除這些功能。 這可能會降低移除並存執行個體後仍然留在系統上之 Visual Studio 執行個體上 Xamarin 平台的執行效能。
>
>您可以透過在 Visual Studio 安裝程式中執行 [修復] 選項來解決這個問題。該選項會重新安裝遺失的元件。

<a name="uninstallvsmac" />

# <a name="uninstalling-visual-studio-for-mac"></a>解除安裝 Visual Studio for Mac

若要解除安裝 Visual Studio for Mac 但繼續使用 Xamarin Studio，請在 **/Applications** 目錄中找出 **Visual Studio.app** 並將其拖曳到垃圾桶。 或者，以滑鼠右鍵按一下並選取 [移至垃圾筒]，如下所示：

 [ ![](uninstalling-xamarin-images/image9.png "以滑鼠右鍵按一下 Visual Studio 圖示，然後選取 [移至垃圾桶]")](uninstalling-xamarin-images/image9.png)

若要從您的電腦中完全解除安裝 Xamarin，請先刪除 Visual Studio for Mac，然後遵循[解除安裝 Xamarin Studio](#uninstallxamarinstudio) 一節中所列出的步驟。

# <a name="summary"></a>總結

在本文中我們探討使用終端機命令從 Mac 完全解除安裝 Xamarin，以及使用 [程式和功能] 選項 (適用於 Visual Studio 2015 及更早版本) 和使用 **Visual Studio 安裝程式** (適用於 Visual Studio 2017) 從 Windows 電腦中解除安裝 Xamarin。


## <a name="related-links"></a>相關連結

- [解除安裝指令碼 (範例)](http://download.xamarin.com/developer/cross-platform/xamarin_uninstall.sh)

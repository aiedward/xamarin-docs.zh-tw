---
title: "可以使用 Visual Studio 2017 發行候選版本使用 Xamarin 嗎？"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8E752F36-F73A-4EFC-9F82-4E18FDE1C9E2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 6144a422b9c01279ce345eccf9830bcd335597a7
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>可以使用 Visual Studio 2017 發行候選版本使用 Xamarin 嗎？

## <a name="can-i-use-visual-studio-2017-release-candidate-with-xamarin"></a>可以使用 Visual Studio 2017 發行候選版本使用 Xamarin 嗎？

可以。 也可以利用 Visual Studio 2017 發行候選版本透過 Xamarin。 不過，請注意，目前，Visual Studio 2017 rc 安裝 Xamarin，將會解除安裝任何舊版的安裝到 Visual Studio 2015/2013 Xamarin。 Xamarin for Visual Studio 2017 可以不安裝在同一時間為 Xamarin for Visual Studio 2015/2013 Visual Studio 2017 移開朝善用 Visual Studio Installer 系統 MSI 封裝的結果。

小組目前究竟略過這個預期的行為方式，而建議使用者選擇他們根據其需求的開發環境。 

> [!IMPORTANT]
> 如果您要建置 Xamarin.iOS 專案，請確定 Visual Studio for Mac 配對的 Mac 電腦上是相同版本的 Xamarin.iOS 通道。

## <a name="how-do-i-install-xamarin-to-visual-studio-2017-release-candidate"></a>如何在 Visual Studio 2017 發行候選版本安裝 Xamarin？

### <a name="installing-during-the-visual-studio-2017-rc-installer"></a>在 Visual Studio 2017 RC 安裝程式期間安裝

* 選取**Xamarin**元件做為新的一部分**Visual Studio 安裝程式**

  [![](visualstudio-2017-rc-images/install1-sml.png "Visual Studio 2017 RC Installer Screen")](visualstudio-2017-rc-images/install1-orig.png#lightbox)

這會安裝 Xamarin.iOS 和 Xamarin.Android 開發的 Visual Studio 擴充功能。

### <a name="installing-or-reinstalling-xamarin-in-an-existing-installation-of-visual-studio-2017-rc"></a>安裝或重新安裝 Visual Studio 2017 RC 的現有安裝中的 Xamarin

#### <a name="using-the-visual-studio-installer"></a>使用 Visual Studio 安裝程式：

1. 搜尋 Visual Studio 安裝程式應用程式

  [![](visualstudio-2017-rc-images/reinstall1-sml.png "Visual Studio 安裝程式的應用程式的搜尋結果")](visualstudio-2017-rc-images/reinstall1-orig.png#lightbox)

2. 選取：。 **行動裝置開發的.NET （預覽）**中工作負載 索引標籤，或

  [![](visualstudio-2017-rc-images/reinstall2-sml.png "VS 安裝程式工作負載 索引標籤")](visualstudio-2017-rc-images/reinstall2-orig.png#lightbox) b。 **Xamarin**中**個別元件** 索引標籤

  [![](visualstudio-2017-rc-images/reinstall3-sml.png "VS 安裝程式元件 索引標籤")](visualstudio-2017-rc-images/reinstall3-orig.png#lightbox)

#### <a name="using-the-visual-studio-installer-within-visual-studio"></a>使用 Visual Studio 中的 Visual Studio 安裝程式：
1. 瀏覽至 Visual Studio 2017 起始頁
2. 按一下**多專案範本**下**新專案**區段

    [![](visualstudio-2017-rc-images/reinstall4-sml.png "Visual Studio 起始頁")](visualstudio-2017-rc-images/reinstall4-orig.png#lightbox)
3. 按一下`Open Visual Studio Installer`的左窗格中

    [![](visualstudio-2017-rc-images/reinstall5-sml.png "新的專案畫面")](visualstudio-2017-rc-images/reinstall5-orig.png#lightbox)
4. 選取：
    
    a. **行動裝置開發的.NET （預覽）**中工作負載 索引標籤，或

    [![](visualstudio-2017-rc-images/reinstall2-sml.png "VS 安裝程式工作負載 索引標籤")](visualstudio-2017-rc-images/reinstall2-orig.png#lightbox) b。 **Xamarin**中**個別元件** 索引標籤

    [![](visualstudio-2017-rc-images/reinstall3-sml.png "VS 安裝程式元件 索引標籤")](visualstudio-2017-rc-images/reinstall3-orig.png#lightbox)

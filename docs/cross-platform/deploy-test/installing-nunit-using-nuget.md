---
title: 使用 NuGet 安裝 NUnit 2.6.4
description: 本指南涵蓋如何使用 NuGet 將 NUnit 3.0 降級至 NUnit 2.6.4。
ms.prod: xamarin
ms.assetid: 7683F2B8-7FDF-48C4-8E7D-649D4D4E79F0
author: asb3993
ms.author: amburns
ms.date: 03/24/2017
ms.openlocfilehash: e74975864dc7f3f00c6b04fe48139589c1f52ad5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="installing-nunit-264-using-nuget"></a>使用 NuGet 安裝 NUnit 2.6.4

_本指南涵蓋如何使用 NuGet 將 NUnit 3.0 降級至 NUnit 2.6.4。_

在 Visual Studio for Mac 中撰寫測試或使用 Xamarin.UITest 的開發人員應使用 [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4)，因為 NUnit 3.0 或更新版本和 Visual Studio for Mac 或 Xamarin.UITest 不相容。 使用 NUnit 3.0 嘗試在 Visual Studio for Mac 或 Xamarin.UITests 中執行單位測試會失敗。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

本指南會討論如何為 Visual Studio for Mac 使用 NuGet 來安裝 NUnit 2.6.4。 如有必要，這些步驟也會將 NUnit 3.0 解除安裝。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

本指南會討論如何在 Visual Studio 2015 中使用 NuGet 來將 NUnit 3.0 降級至 NUnit 2.6.4。

-----

## <a name="requirements"></a>需求

本指南假設現有的解決方案內含行動應用程式專案和測試專案。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="installing-nunit-264-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 中安裝 NUnit 2.6.4

下列步驟描述如何安裝 NUnit 2.6.4。


1. **開啟套件管理員** - 以滑鼠右鍵按一下 [套件]，然後從快顯功能表中選取 [新增套件]：

    [![](installing-nunit-using-nuget-images/add-packages-xs.png "以滑鼠右鍵按一下 [套件]，然後從快顯功能表中選取 [新增套件]")](installing-nunit-using-nuget-images/add-packages-xs.png#lightbox)
    
1. **搜尋`NUnit version:2.6.4`** - Visual Studio for Mac 會將 NUnit 3.0 解除安裝 (如有必要)，然後下載並安裝 NUnit 2.6.4。 在 [新增套件] 對話方塊右上角的 [搜尋] 欄位中，輸入文字 `nunit version:2.6.4`。 從搜尋結果中選取 [NUnit]，然後按一下 [新增套件] 按鈕：

    [![](installing-nunit-using-nuget-images/nunit-search-xs.png "從搜尋結果中選取 [NUnit]，然後按一下 [新增套件] 按鈕")](installing-nunit-using-nuget-images/nunit-search-xs.png#lightbox)


您可以檢查 Solution Pad 中 NUnit 套件的版本號碼來確認已安裝 NUnit 2.6.4：

[![](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png "檢查 Solution Pad 中 NUnit 套件的版本號碼")](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png#lightbox)

## <a name="summary"></a>總結

本指南討論如何在 Visual Studio for Mac 中使用套件管理員主控台將 NUnit 3.0 降級至 NUnit 2.6.4。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="installing-nunit-264-in-visual-studio"></a>在 Visual Studio 中安裝 NUnit 2.6.4

本節著重在 Visual Studio 2015 中使用「NuGet 套件管理員主控台」，將 NUnit 3.0 解除安裝，然後安裝 NUnit 2.6.4。


1. **啟動 NuGet 套件管理員主控台** - 選取 [工具] > [NuGet 套件管理員] > [套件管理員主控台]：

    [![](installing-nunit-using-nuget-images/package-manager-console.png "啟動 NuGet 套件管理員主控台 - 選取 [工具]  [NuGet 套件管理員]  [套件管理員主控台]")](installing-nunit-using-nuget-images/package-manager-console.png#lightbox)
    
1. **確認 NUnit 版本** - 您可以執行命令 `Get-Package -Project <UITEST PROJECT>`，選擇性地驗證所安裝之 NUnit 的版本：

    ```bash
    [PM] Get-Package -Project [TEST PROJECT NAME]
    
        Id                                  Versions                                 ProjectName
        --                                  --------                                 -----------
    NUnit                               {3.0.1}                                  [TEST PROJECT NAME]
    Xamarin.UITest                      {1.2.0}                                  [TEST PROJECT NAME]
    ```

如果您看到 NUnit 3.0 或更新版本，則必須降級至 NUnit 2.6.4。

1. **將 NUnit 3.0 解除安裝** - 使用 `Uninstall-Package` 指令程式來將 NUnit 3.0 解除安裝：

        <PM> Uninstall-Package NUnit -Project <TEST PROJECT NAME>
        Attempting to gather dependencies information for package 'NUnit.3.0.1' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
        Resolving actions to uninstall package 'NUnit.3.0.1'
        Resolved actions to uninstall package 'NUnit.3.0.1'
        Removed package 'NUnit.3.0.1' from 'packages.config'
        Successfully uninstalled 'NUnit.3.0.1' from <TEST PROJECT NAME>

1. **安裝 NUnit 2.6.4** - 使用 `Install-Package` 指令程式來安裝 Nunit 2.6.4，如下列程式碼片段所示：

        <PM> Install-Package NUnit -Version 2.6.4 -Project <TEST PROJECT NAME>
        Attempting to gather dependencies information for package 'NUnit.2.6.4' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
        Attempting to resolve dependencies for package 'NUnit.2.6.4' with DependencyBehavior 'Lowest'
        Resolving actions to install package 'NUnit.2.6.4'
        Resolved actions to install package 'NUnit.2.6.4'
        Adding package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
        Added package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
        Added package 'NUnit.2.6.4' to 'packages.config'
        Successfully installed 'NUnit 2.6.4' to <TEST PROJECT NAME>
    
## <a name="summary"></a>總結

本指南討論如何在 Visual Studio 2015 中使用套件管理員主控台將 NUnit 3.0 降級至 NUnit 2.6.4。

-----

## <a name="related-links"></a>相關連結

- [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4) \(英文\)
- [NUnit 2.6.4 NuGet 套件](https://www.nuget.org/packages/NUnit/2.6.4) \(英文\)

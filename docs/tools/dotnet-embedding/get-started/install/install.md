---
title: 安裝.NET 內嵌
description: 本文件說明如何安裝.NET 內嵌。 其中也會討論如何執行工具以手動方式，如何產生繫結如何自動執行，使用自訂的 MSBuild 目標和必要的建置後步驟。
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 4/18/2018
ms.openlocfilehash: 057a1f3f662b2dbe2f8aee277505e1d6e8798084
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793791"
---
# <a name="installing-net-embedding"></a>安裝.NET 內嵌

## <a name="installing-net-embedding-from-nuget"></a>安裝.NET NuGet 內嵌

選擇**新增 > 新增 NuGet 封裝...** 並安裝**Embeddinator 4000**從 NuGet 封裝管理員：

![NuGet 封裝管理員](images/visualstudionuget.png)

這將會安裝**Embeddinator 4000.exe**和**objcgen**到**封裝/Embeddinator 4000/工具**目錄。

一般情況下，應該選取最新版的 Embeddinator 4000 下載。 Objective C 支援需要 0.4 或更新版本。

## <a name="running-manually"></a>手動執行

既然已安裝 NuGet，您可以手動執行工具。

- 開啟 終端機 (macOS) 或命令提示字元 (Windows)
- 將目錄變更為您的方案根目錄
- 工具會安裝在：
    - **。 / 封裝/Embeddinator-4000。[版本] 工具/objcgen** (Objective C)
    - **。 / 封裝/Embeddinator-4000。[VERSION]/tools/Embeddinator-4000.exe** (Java/C) 
- 在 macOS **objcgen**可以直接執行。 
- 在 Windows 中， **Embeddinator 4000.exe**可以直接執行。
- 在 macOS **Embeddinator 4000.exe**必須執行與**單聲道**: 
    - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

每個命令引動過程需要的平台特定文件中所列的參數數目。

## <a name="automatic-binding-generation"></a>自動繫結產生

有兩種方式，自動執行.NET 內嵌在您的建置程序。

- 自訂的 MSBuild 目標
- 建置後步驟

雖然本文件將說明兩者，使用自訂的 MSBuild 目標是慣用的。 從命令列建置時，不會一定執行 post 建置步驟。

### <a name="custom-msbuild-targets"></a>自訂的 MSBuild 目標

若要自訂您的組建包含 MSbuild 目標，請先建立**Embeddinator 4000.targets**旁邊類似下面的您 csproj 檔案：

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

在這裡，應該會利用.NET 內嵌叫用的平台特定文件中所列的其中一個填入的命令文字。

若要使用此目標：

- 關閉您的專案在 Visual Studio 2017 或 Visual Studio for Mac
- 在文字編輯器中開啟文件庫 csproj
- 在底部，最終上方加入這行`</Project>`行：

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- 重新開啟您的專案

### <a name="post-build-steps"></a>建置後步驟

加入建置後步驟，以執行.NET 內嵌的步驟有所不同 IDE:

#### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

在 Visual Studio for Mac，移至**專案選項 > 建置 > 自訂命令**並加入**後建置**步驟。

設定平台特定文件中所列的命令。

> [!NOTE]
> 請務必使用您從 NuGet 所安裝的版本號碼

如果您要做的動作進行中的開發，C# 專案，您也可以加入自訂命令以清除**輸出**目錄執行.NET 內嵌之前：

```shell
rm -Rf '${SolutionDir}/output/'
```

![自訂的建置動作](images/visualstudiocustombuild.png)

> [!NOTE]
> 產生的繫結將會置於所指定的目錄`--outdir`或`--out`參數。 產生的繫結名稱可能會有所不同，因為某些平台上封裝名稱中有限制。

#### <a name="visual-studio-2017"></a>Visual Studio 2017

我們基本上會設定相同的作業，但在 Visual Studio 2017 功能表會有些許不同。 Shell 命令也有些許不同。

移至**專案選項 > 建置事件**輸入到平台特定文件中所列的命令和**建置後事件命令列**方塊。 例如: 

![內嵌在 Windows 上的.NET](images/visualstudiowindows.png)

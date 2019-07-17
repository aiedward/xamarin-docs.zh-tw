---
title: 我可以變更輸出路徑的 IPA 檔案嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 8b0686a91f18b41aa8e2e7db071123c0d96723a0
ms.sourcegitcommit: 32c7cf8b0d00464779e4b0ea43e2fd996632ebe0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68290108"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>我可以變更輸出路徑的 IPA 檔案嗎？

## <a name="for-cycle-7-and-higher"></a>循環 7 和更新版本
是，您可以使用自訂的 MSBuild 目標來達到此目的。 最簡單的選項可能是複製`.ipa`檔案之後已經建置。

這些步驟適用於任何使用 MSBuild 組建引擎，在 Mac 或 Windows 的 iOS 專案。 (注意： 統一 API 的所有專案都使用 MSBuild 組建引擎。)

1. 開啟`.csproj`iOS 應用程式專案，在文字編輯器中的檔案，並在結尾，然後新增下列幾行 (結尾之前，立即`</Project>`標記):
    
    ```
    <PropertyGroup>
           <CreateIpaDependsOn>
           $(CreateIpaDependsOn);
            CopyIpa
           </CreateIpaDependsOn>
    </PropertyGroup>
    
    <Target Name="CopyIpa"
        Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(IpaPackagePath)"
            DestinationFolder="$(OutputPath)"
        />
    </Target>
    ```

2. DestinationFolder 設所需的輸出資料夾中。 您可能會如往常般使用 MSBuild 屬性 （例如，如果您想要這個引數中 $(OutputPath))。

## <a name="notes"></a>附註
- `CreateIpaDependsOn`屬性定義於`Xamarin.iOS.Common.targets`檔案也就是 Xamarin.iOS 的一部分。 它的行為中所述[覆寫預先定義的目標](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets)一節的發行項[How to:擴充 Visual Studio 建置處理序](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)。

- 您可以使用**移動**工作而非**複製**如果您偏好的工作。 如果您選擇選項，您要建置在 Windows 上，您必須使用完整的工作名稱`<Microsoft.Build.Tasks.Move>`以避免模稜兩可的 XamarinVS 與建置工作。

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Xamarin Studio 6.0.0.5174 以前的版本 |Xamarin for Visual Studio 4.1.0.530

是，您可以使用自訂的 MSBuild 目標來達到此目的。 最簡單的選項可能是複製`.ipa`檔案之後已經建置。

這些步驟適用於任何使用 MSBuild 組建引擎，在 Mac 或 Windows 的 iOS 專案。 (注意： 統一 API 的所有專案都使用 MSBuild 組建引擎。)

1. 開啟`.csproj`iOS 應用程式專案，在文字編輯器中的檔案，並在結尾，然後新增下列幾行 (結尾之前，立即`</Project>`標記)。

    ```csharp
    <PropertyGroup>
        <CreateIpaDependsOn>
            $(CreateIpaDependsOn);
            CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>
    
    <Target Name="CopyIpa"
        Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(OutputPath)$(IpaPackageName)"
            DestinationFolder="/Users/macuser/Desktop/"
        />
    </Target>
    ```

2. 設定`DestinationFolder`到所需的輸出資料夾。 像往常一樣，您可以使用 MSBuild 屬性 (例如`$(OutputPath)`) 如果您想要這個引數中。

## <a name="notes"></a>附註
- `CreateIpaDependsOn`屬性定義於`Xamarin.iOS.Common.targets`檔案也就是 Xamarin.iOS 的一部分。 t 行為中所述[覆寫預先定義的目標](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets)一節的發行項[How to:擴充 Visual Studio 建置處理序](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)。

- 您可以使用**移動**工作而非**複製**如果您偏好的工作。 如果您選擇選項，您要建置在 Windows 上，您必須使用完整的工作名稱`<Microsoft.Build.Tasks.Move>`以避免模稜兩可的 XamarinVS 與建置工作。

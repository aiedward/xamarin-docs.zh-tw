---
title: 我可以變更 .IPA 檔案的輸出路徑嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 6df481688bfaa1e23cc56e6e34586f23d8ab9da6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031031"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>我可以變更 .IPA 檔案的輸出路徑嗎？

## <a name="for-cycle-7-and-higher"></a>適用于週期7和更新版本
是，您可以使用自訂的 MSBuild 目標來達到這個目的。 最簡單的選項可能是在建立之後複製 `.ipa` 檔案。

這些步驟適用于在 Mac 或 Windows 上使用 MSBuild 組建引擎的任何 iOS 專案。 （注意：所有 Unified API 專案都會使用 MSBuild 組建引擎）。

1. 在文字編輯器中開啟 iOS 應用程式專案的 `.csproj` 檔案，然後在結尾新增下列幾行（緊接在結束 `</Project>` 標記之前）：

    ```xml
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

2. 將 DestinationFolder 設定為所需的輸出檔案夾。 如往常一般，您可以視需要在此引數中使用 MSBuild 屬性（例如 $ （OutputPath））。

## <a name="notes"></a>備註

- `CreateIpaDependsOn` 屬性是在屬於 Xamarin 的 `Xamarin.iOS.Common.targets` 檔案中定義。 其行為如[如何：擴充 Visual Studio 的組建](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)程式一文中的覆[寫預先定義的目標](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets)一節中所述。

- 如果您慣用，可以使用**移動**工作，而不是**複製**工作。 如果您選擇該選項，而且您是在 Windows 上建立，則必須使用完整的工作名稱 `<Microsoft.Build.Tasks.Move>`，以避免 XamarinVS 組建工作發生不明確的情況。

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Xamarin Studio 6.0.0.5174 之前的版本 |適用于 Visual Studio 4.1.0.530 的 Xamarin

是，您可以使用自訂的 MSBuild 目標來達到這個目的。 最簡單的選項可能是在建立之後複製 `.ipa` 檔案。

這些步驟適用于在 Mac 或 Windows 上使用 MSBuild 組建引擎的任何 iOS 專案。 （注意：所有 Unified API 專案都會使用 MSBuild 組建引擎）。

1. 在文字編輯器中開啟 iOS 應用程式專案的 `.csproj` 檔案，然後在結尾新增下列幾行（緊接在右 `</Project>` 標記之前）。

    ```xml
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

2. 將 `DestinationFolder` 設定為所要的輸出檔案夾。 如往常一般，您可以視需要使用此引數中的 MSBuild 屬性（如 `$(OutputPath)`）。

## <a name="notes"></a>備註

- `CreateIpaDependsOn` 屬性是在屬於 Xamarin 的 `Xamarin.iOS.Common.targets` 檔案中定義。 t 的行為方式如[如何：擴充 Visual Studio 組建進程](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)一文的覆[寫預先定義的目標](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets)一節中所述。

- 如果您慣用，可以使用**移動**工作，而不是**複製**工作。 如果您選擇該選項，而且您是在 Windows 上建立，則必須使用完整的工作名稱 `<Microsoft.Build.Tasks.Move>`，以避免 XamarinVS 組建工作發生不明確的情況。

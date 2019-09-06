---
title: 我可以變更 .IPA 檔案的輸出路徑嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: b8006b1ffe253ac57c1ab435690c5b378cc709fb
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278661"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>我可以變更 .IPA 檔案的輸出路徑嗎？

## <a name="for-cycle-7-and-higher"></a>適用于週期7和更新版本
是，您可以使用自訂的 MSBuild 目標來達到這個目的。 最簡單的選項可能是在建立`.ipa`檔案之後複製該檔案。

這些步驟適用于在 Mac 或 Windows 上使用 MSBuild 組建引擎的任何 iOS 專案。 （注意：所有 Unified API 專案都會使用 MSBuild 組建引擎）。

1. 在文本`.csproj`編輯器中開啟 iOS 應用程式專案的檔案，然後在結尾新增下列幾行（緊接`</Project>`在結束記號之前）：

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

## <a name="notes"></a>注意
- 屬性是在檔案中定義，該檔案屬於 Xamarin. iOS 的一部分。 `Xamarin.iOS.Common.targets` `CreateIpaDependsOn` 其行為如下列文章[中的覆[寫預先定義的目標](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets)一節中所述：如何：擴充 Visual Studio 的組建](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)程式。

- 如果您慣用，可以使用**移動**工作，而不是**複製**工作。 如果您選擇該選項，而且您是在 Windows 上建立，則必須使用完整的工作名稱`<Microsoft.Build.Tasks.Move>` ，以避免 XamarinVS 組建工作發生不明確的情況。

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Xamarin Studio 6.0.0.5174 之前的版本 |適用于 Visual Studio 4.1.0.530 的 Xamarin

是，您可以使用自訂的 MSBuild 目標來達到這個目的。 最簡單的選項可能是在建立`.ipa`檔案之後複製該檔案。

這些步驟適用于在 Mac 或 Windows 上使用 MSBuild 組建引擎的任何 iOS 專案。 （注意：所有 Unified API 專案都會使用 MSBuild 組建引擎）。

1. 在文本`.csproj`編輯器中開啟 iOS 應用程式專案的檔案，然後在結尾新增下列幾行（緊接`</Project>`在結束記號之前）。

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

2. 將設定`DestinationFolder`為所要的輸出檔案夾。 如往常一般，您可以視需要在`$(OutputPath)`此引數中使用 MSBuild 屬性（例如）。

## <a name="notes"></a>注意
- 屬性是在檔案中定義，該檔案屬於 Xamarin. iOS 的一部分。 `Xamarin.iOS.Common.targets` `CreateIpaDependsOn` t 的行為方式如下列文章[中的覆[寫預先定義的目標](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets)一節中所述：如何：擴充 Visual Studio 的組建](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process)程式。

- 如果您慣用，可以使用**移動**工作，而不是**複製**工作。 如果您選擇該選項，而且您是在 Windows 上建立，則必須使用完整的工作名稱`<Microsoft.Build.Tasks.Move>` ，以避免 XamarinVS 組建工作發生不明確的情況。

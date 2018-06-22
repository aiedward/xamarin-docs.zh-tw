---
title: 可以變更以及 IPA 檔案的輸出路徑嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 9c80a209279a2f032eb6c9efcba1398ca0e267a5
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
ms.locfileid: "31882798"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>可以變更以及 IPA 檔案的輸出路徑嗎？

## <a name="for-cycle-7-and-higher"></a>循環 7 及更新版本
是，您可以使用自訂的 MSBuild 目標，以達到這個目的。 最簡單的選項可能是複製`.ipa`檔案之後已經建置。

這些步驟適用於任何使用 MSBuild 建置引擎 Mac 或 Windows 的 iOS 專案。 (注意： 統一的 API 的所有專案都使用 MSBuild 建置引擎。)

1. 開啟`.csproj`iOS 應用程式專案，在文字編輯器中的檔案，然後再結尾加入下列幾行 (結尾之前立即`</Project>`標記):
    
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

2. 想要的輸出資料夾設定 DestinationFolder。 像往常一樣，您可以使用 MSBuild 屬性 （例如 $(OutputPath)) 這個引數，如果您想內。

## <a name="notes"></a>注意
- `CreateIpaDependsOn`屬性已定義於`Xamarin.iOS.Common.targets`檔案亦即 Xamarin.iOS 的一部分。 它的行為如底下所述*正在覆寫 'DependsOn' 屬性*上[ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx)。

- 您可以使用**移動**工作而非**複製**如果您偏好的工作。 如果您選擇選項，您要在 Windows 上建置，您必須使用完整的工作名稱`<Microsoft.Build.Tasks.Move>`以避免與 XamarinVS 模稜兩可建置工作。

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Xamarin Studio 6.0.0.5174 以前的版本 |Xamarin for Visual Studio 4.1.0.530

是，您可以使用自訂的 MSBuild 目標，以達到這個目的。 最簡單的選項可能是複製`.ipa`檔案之後已經建置。

這些步驟適用於任何使用 MSBuild 建置引擎 Mac 或 Windows 的 iOS 專案。 (注意： 統一的 API 的所有專案都使用 MSBuild 建置引擎。)

1. 開啟`.csproj`iOS 應用程式專案，在文字編輯器中的檔案，然後再結尾加入下列幾行 (結尾之前立即`</Project>`標記)。

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

2. 設定`DestinationFolder`到想要的輸出資料夾。 像往常一樣，您可以使用 MSBuild 屬性 (例如`$(OutputPath)`) 這個引數，如果您想內。

## <a name="notes"></a>注意
- `CreateIpaDependsOn`屬性已定義於`Xamarin.iOS.Common.targets`檔案亦即 Xamarin.iOS 的一部分。 它的行為如底下所述*正在覆寫 「 DependsOn"屬性*上[ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx)。

- 您可以使用**移動**工作而非**複製**如果您偏好的工作。 如果您選擇選項，您要在 Windows 上建置，您必須使用完整的工作名稱`<Microsoft.Build.Tasks.Move>`以避免與 XamarinVS 模稜兩可建置工作。

---
title: 如何將 .IPA 輸出檔案複製到 TFS 投遞資料夾？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 4493b1a0d06e2f44ee9a11a250395f058baa0548
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031026"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>如何將 .IPA 輸出檔案複製到 TFS 投遞資料夾？

在文字編輯器中開啟 iOS 應用程式專案的 `.csproj` 檔案，然後在結尾新增下列幾行（緊接在結束 `</Project>` 標記之前）：

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
        And '$(BuildIpa)' == 'true'
        And '$(TF_BUILD)' == 'true'">
    <Copy
        SourceFiles="$(OutputPath)$(IpaPackageName)"
        DestinationFolder="$(TF_BUILD_BINARIESDIRECTORY)"
    />
</Target>
```

## <a name="notes"></a>備註

- 這是我在上所討論的一般技巧，[我可以變更 .ipa 檔案的輸出路徑嗎？](~/ios/troubleshooting/questions/ipa-output-path.md)。 這兩個重點是將 `$(TF_BUILD_BINARIESDIRECTORY)` 設定為目的地資料夾，並新增額外的條件，讓 `CopyIpa` 只會針對 TFS 組建執行。

- 如需 `TF_BUILD_BINARIESDIRECTORY` 的描述，請參閱[預先定義的組建變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables)。

## <a name="additional-references"></a>其他參考

- [安裝 TFS 以搭配 Xamarin 使用的相關檔](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)
- [Azure DevOps 組建工作： Xamarin. Android](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-android)
- [Azure DevOps 組建工作： Xamarin. iOS](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>後續步驟

本檔討論 Mac 組建主機上的 Xamarin 3.11.666 for Visual Studio 和8.10.3 的目前行為。 如需進一步的協助，請洽詢我們，或即使在使用上述資訊之後仍然會發生此問題，請參閱[什麼是適用于 Xamarin 的支援選項？](~/cross-platform/troubleshooting/support-options.md)以取得連絡人選項、建議，以及如何在需要時提出新 bug 的相關資訊.

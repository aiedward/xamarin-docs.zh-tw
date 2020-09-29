---
title: 如何將 IPA 輸出檔案複製到 TFS 投遞資料夾？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 2542eace19cf08a994be99379566e9e621c27190
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435579"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>如何將 IPA 輸出檔案複製到 TFS 投遞資料夾？

`.csproj`在文字編輯器中開啟 iOS 應用程式專案的檔案，然後將下列幾行新增至結束標記) 的結尾 (`</Project>` ：

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

- 這是 [我可以變更 IPA 檔案的輸出路徑](~/ios/troubleshooting/questions/ipa-output-path.md)時，所討論的相同一般技術。 這兩個重點是設定 `$(TF_BUILD_BINARIESDIRECTORY)` 為目的資料夾，並新增額外的條件，因此 `CopyIpa` 只會針對 TFS 組建執行。

- 如需詳細說明， `TF_BUILD_BINARIESDIRECTORY` 請參閱 [預先定義的組建變數](/azure/devops/pipelines/build/variables)。

## <a name="additional-references"></a>其他參考資料

- [安裝 TFS 以搭配 Xamarin 使用的相關檔](/azure/devops/repos/tfvc/overview)
- [Azure DevOps 組建工作： Xamarin. Android](/azure/devops/pipelines/tasks/build/xamarin-android)
- [Azure DevOps 組建工作： Xamarin](/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>後續步驟

本檔討論在 Mac 組建主機上，Visual Studio 和 Xamarin 8.10.3 的 Xamarin 3.11.666 目前的行為。 如需進一步的協助，以聯繫我們，或即使在使用上述資訊之後仍有此問題，請參閱 [適用于 Xamarin 的支援選項有哪些？](~/cross-platform/troubleshooting/support-options.md) 如需連絡人選項、建議和如何提出新 bug 的詳細資訊，請參閱。
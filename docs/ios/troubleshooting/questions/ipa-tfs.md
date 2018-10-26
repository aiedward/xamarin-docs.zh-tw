---
title: 如何將 IPA 輸出檔案複製到 TFS 放置資料夾？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 74e2f2219dcb0908edce7f109844932639038b25
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113036"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>如何將 IPA 輸出檔案複製到 TFS 放置資料夾？

開啟`.csproj`iOS 應用程式專案，在文字編輯器中的檔案，並在結尾，然後新增下列幾行 (結尾之前，立即`</Project>`標記):

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

## <a name="notes"></a>注意

- 這是所討論的相同一般技巧[我可以變更輸出路徑的 IPA 檔案？](~/ios/troubleshooting/questions/ipa-output-path.md)。 兩個重要點會設定`$(TF_BUILD_BINARIESDIRECTORY)`做為目的地資料夾，並因此加入的額外條件`CopyIpa`只會執行 TFS 組建。

- 如需描述`TF_BUILD_BINARIESDIRECTORY`請參閱 <<c2> [ 預先定義建置變數](https://docs.microsoft.com/azure/devops/pipelines/build/variables)。

## <a name="additional-references"></a>其他參考資料

- [安裝適用於搭配 Xamarin 使用 TFS 的相關文件](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)
- [Azure DevOps 建置工作： Xamarin.Android](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-android)
- [Azure DevOps 建置工作： Xamarin.iOS](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>後續步驟

本文件討論適用於 Visual Studio 的 Xamarin 3.11.666 截至目前的行為和在 Mac 上的 Xamarin.iOS 8.10.3 組建主機。 如需進一步協助，請連絡我們，或如果此問題持續，即使利用上述的資訊，請參閱[適用於 Xamarin 有哪些支援選項？](~/cross-platform/troubleshooting/support-options.md)如需連絡選項，建議，以及如何如有需要請提出新錯誤報告。

---
title: 如何將 IPA 輸出檔案複製到 TFS 置放資料夾
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 087a20ea3b573595e6cbd2b40d77de649676391e
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
ms.locfileid: "31883705"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>如何將 IPA 輸出檔案複製到 TFS 置放資料夾

開啟`.csproj`iOS 應用程式專案，在文字編輯器中的檔案，然後再結尾加入下列幾行 (結尾之前立即`</Project>`標記):

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

-   這是相同的一般技術以討論[我可以變更輸出檔案的路徑 IPA？](~/ios/troubleshooting/questions/ipa-output-path.md)。 兩個重點是要設定`$(TF_BUILD_BINARIESDIRECTORY)`作為目的地資料夾，並因此加入額外的條件`CopyIpa`才會執行的 TFS 組建。

-   如需說明的`TF_BUILD_BINARIESDIRECTORY`看到[ https://msdn.microsoft.com/library/hh850448.aspx ](https://msdn.microsoft.com/library/hh850448.aspx)。

## <a name="additional-references"></a>其他參考資料

- [文件上使用 Xamarin 的安裝 TFS](https://docs.microsoft.com/vsts/tfvc/overview)
- [TFS 組建工作： Xamarin.Android](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-android)
- [TFS 組建工作： Xamarin.iOS](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-ios)

### <a name="next-steps"></a>後續步驟
本文將討論 Visual Studio 的 Xamarin 3.11.666 為準，目前的行為，並在 Mac 上的 8.10.3 Xamarin.iOS 組建主機。 如需進一步協助，請連絡我們，或如果此問題仍然發生即使利用上述的資訊，請參閱[xamarin 有哪些支援的選項？](~/cross-platform/troubleshooting/support-options.md)有關連絡人的選項，建議，以及如何如有需要的檔案新的 bug。 




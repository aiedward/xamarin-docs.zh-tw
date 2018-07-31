---
title: IBTool 錯誤： 無法完成作業。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/03/2018
ms.openlocfilehash: 593706de23d370d6bff03c97bb50e064d77a52ef
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350728"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>IBTool 錯誤： 無法完成作業。

## <a name="fixed-in-xcode-611"></a>已修正在 Xcode 6.1.1 中

Apple[修正](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1)這`ibtool`Xcode 6.1.1，因此升級到 Xcode 6.1.1 或更高版本中的 bug 是最簡單的修正程式。

* * *

## <a name="description-of-the-problem"></a>問題的描述

`ibtool` Xcode 6.0 中的命令在 OS X 10.10 Yosemite 上有錯誤。 Xamarin.iOS 使用 Xcode 的`ibtool`編譯的分鏡腳本和`XIB`檔案。

與 Xcode 相關錯誤的詳細資訊可於下列 Stack Overflow 文章： [http://stackoverflow.com/questions/25754763/cant-open-storyboard](http://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>錯誤訊息

> 無法開啟文件 」 MainStoryboard.storyboard"。 無法完成作業。 （com.apple.InterfaceBuilder 錯誤為-1。）

## <a name="workarounds-for-xcode-60"></a>因應措施 （Xcode 6.0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>選項 1： 管理所有`UIImageView.Image`在程式碼中的屬性

而非設定`Image`的屬性`UIImageView`分鏡腳本中或`.xib`檔案中，您可以設定屬性在檢視表的其中一個檢視控制器中的生命週期覆寫方法 (例如，在`ViewDidLoad()`)。 另請參閱[處理映像](~/ios/app-fundamentals/images-icons/index.md)如需使用提示`UIImage.FromBundle()`與`UIImage.FromFile()`。

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>選項 2︰ 將所有的影像資源移到最上層`Resources`資料夾

之後將映像移至最上層`Resources`資料夾中，您必須更新的分鏡腳本和`.xib`使用新的映像路徑的檔案。

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>選項 3： 設定`LogicalName`任何有問題的影像資產，以便將它們複製到最上層`.app`套件組合

例如，假設您的原始`.csproj`檔案包含下列項目：

`<BundleResource Include="Resources\Images\image.png" />`

您可以變更此項目，並新增`LogicalName`使映像將會改為複製的最上層`.app `套件組合：

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

在 Visual Studio for Mac`LogicalName`也可以設定使用`Resource ID`下方影像欄位**檢視 > 板 > 屬性**。 (另請參閱： [ http://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545 ](http://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

此變更後，您必須更新的分鏡腳本和`.xib`使用新的最上層的映像路徑的檔案。 Visual Studio for Mac 會自動更新為 autocompletions 份`Image`iOS 設計工具中的屬性。 在 Visual Studio 中，您必須以手動方式編輯路徑。 IOS 設計工具會再顯示為遺失的映像，但專案會建置及正確執行。

### <a name="next-steps"></a>後續步驟

如需進一步協助，請連絡我們，或如果此問題持續，即使利用上述的資訊，請參閱[適用於 Xamarin 有哪些支援選項？](~/cross-platform/troubleshooting/support-options.md)如需連絡選項，建議，以及如何如有需要請提出新錯誤報告。 


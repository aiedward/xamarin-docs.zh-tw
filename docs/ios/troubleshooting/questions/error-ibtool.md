---
title: IBTool 錯誤：無法完成操作。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: ddeffd2dd01553b5beb8bca7827603a416c3955b
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603018"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>IBTool 錯誤：無法完成操作。

## <a name="fixed-in-xcode-611"></a>修正于 Xcode 6.1。1

Apple [修正](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) `ibtool` 了 Xcode 6.1.1 中的此錯誤，因此升級至 Xcode 6.1.1 或更高版本是最簡單的修正程式。

* * *

## <a name="description-of-the-problem"></a>問題描述

`ibtool`Xcode 6.0 中的命令在 OS X 10.10 Yosemite 上有錯誤。 Xamarin 使用 Xcode 來編譯分鏡腳本 `ibtool` 和檔案 `XIB` 。

如需有關 Xcode 中 bug 的詳細資訊，請參閱下列堆疊溢位文章： [https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>錯誤訊息

> 無法開啟檔 "MainStoryboard"。 作業無法完成。  (InterfaceBuilder 錯誤-1. ) 

## <a name="workarounds-for-xcode-60"></a>Xcode 6.0) 的因應措施 (

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>選項1：在程式 `UIImageView.Image` 代碼中管理所有屬性

除了在腳本或檔案中設定的 `Image` 屬性之外 `UIImageView` `.xib` ，您還可以在 view controller 中的其中一個 view 生命週期覆寫方法中設定屬性 (例如 `ViewDidLoad()`) 。 另請參閱使用[影像](~/ios/app-fundamentals/images-icons/index.md)來取得使用 vs 的秘訣。 `UIImage.FromBundle()` `UIImage.FromFile()`

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>選項2：將所有影像資源移至最上層 `Resources` 資料夾

將映射移至最上層資料夾之後 `Resources` ，您必須更新腳本和檔案， `.xib` 以使用新的影像路徑。

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>選項3： `LogicalName` 為任何有問題的影像資產設定，以便將它們複製到套件組合的最上層 `.app`

例如，假設您 `.csproj` 的原始檔案包含下列專案：

`<BundleResource Include="Resources\Images\image.png" />`

您可以變更此元素並新增， `LogicalName` 以將影像複製到組合的最上層 `.app` ：

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

在 Visual Studio for Mac 中， `LogicalName` 也可以使用 [View] `Resource ID` 下的影像欄位來設定， **> pad > 屬性**。  (另請參閱： [https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545)) 

變更之後，您必須更新腳本和檔案， `.xib` 以使用新的最上層影像路徑。 在 Visual Studio 和 Visual Studio for Mac 中，您必須手動編輯屬性的路徑 `Image` 。

### <a name="next-steps"></a>後續步驟

如需進一步的協助，以聯繫我們，或即使在使用上述資訊之後仍有此問題，請參閱 [適用于 Xamarin 的支援選項有哪些？](~/cross-platform/troubleshooting/support-options.md) 如需連絡人選項、建議和如何提出新 bug 的詳細資訊，請參閱。 

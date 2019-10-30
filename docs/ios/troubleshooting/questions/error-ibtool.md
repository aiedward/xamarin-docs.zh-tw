---
title: IBTool 錯誤：無法完成操作。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 4ff7b88ad63870246acbf2b29d01775f6711a8ce
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031182"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>IBTool 錯誤：無法完成操作。

## <a name="fixed-in-xcode-611"></a>已修正 Xcode 6.1。1

Apple 已[修正](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1)此 `ibtool` Xcode 6.1.1 中的錯誤，因此升級至 Xcode 6.1.1 或更高版本是最簡單的修正程式。

* * *

## <a name="description-of-the-problem"></a>問題的描述

Xcode 6.0 中的 `ibtool` 命令在 OS X 10.10 Yosemite 上有錯誤。 Xamarin 會使用 Xcode 的 `ibtool` 來編譯分鏡腳本和 `XIB` 檔案。

如需有關 Xcode 的詳細資訊，請參閱下列 Stack Overflow 文章： [https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>錯誤訊息

> 無法開啟檔 "Mainstoryboard.storyboard"。 作業無法完成。 （InterfaceBuilder 錯誤-1）。

## <a name="workarounds-for-xcode-60"></a>因應措施（適用于 Xcode 6.0）

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>選項1：管理程式碼中的所有 `UIImageView.Image` 屬性

您不需要在腳本或 `.xib` 檔案中設定 `UIImageView` 的 `Image` 屬性，而是在視圖控制器中的其中一個 [觀看生命週期] 覆寫方法中設定屬性（例如，在 [`ViewDidLoad()`] 中）。 如需有關使用 `UIImage.FromBundle()` 與 `UIImage.FromFile()`的秘訣，另請參閱使用[影像](~/ios/app-fundamentals/images-icons/index.md)。

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>選項2：將所有影像資源移到最上層的 `Resources` 資料夾

將影像移至最上層 `Resources` 資料夾之後，您必須更新腳本和 `.xib` 檔案，才能使用新的影像路徑。

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>選項3：設定任何有問題之影像資產的 `LogicalName`，使其複製到`.app` 配套的最上層

例如，假設您的原始 `.csproj` 檔案包含下列專案：

`<BundleResource Include="Resources\Images\image.png" />`

您可以變更此元素並新增 `LogicalName`，以便將映射複製到 `.app` 配套的最上層：

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

在 Visual Studio for Mac 也可以使用 View 下影像的 `Resource ID` 欄位來設定 `LogicalName`， **> pad > 屬性**。 （另請參閱： [https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545)）

在這種變更之後，您必須更新腳本和 `.xib` 檔案，以使用新的最上層影像路徑。 Visual Studio for Mac 會自動更新 iOS 設計工具中 [`Image`] 屬性的 autocompletions 清單。 在 Visual Studio 中，您必須手動編輯路徑。 然後 iOS 設計工具會將此顯示為遺失的影像，但專案會正確地建立和執行。

### <a name="next-steps"></a>後續步驟

如需進一步的協助，請洽詢我們，或即使在使用上述資訊之後仍然會發生此問題，請參閱[什麼是適用于 Xamarin 的支援選項？](~/cross-platform/troubleshooting/support-options.md)以取得連絡人選項、建議，以及如何在需要時提出新 bug 的相關資訊. 

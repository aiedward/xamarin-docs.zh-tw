---
title: IBTool 錯誤：作業無法完成。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 39b522af5bdc3587e3d5aa1451ed4879c6af65f5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769342"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>IBTool 錯誤：作業無法完成。

## <a name="fixed-in-xcode-611"></a>已修正 Xcode 6.1。1

Apple [已修正](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1)`ibtool` Xcode 6.1.1 中的這個錯誤，因此升級至 Xcode 6.1.1 或更新版本是最簡單的修正程式。

* * *

## <a name="description-of-the-problem"></a>問題的描述

Xcode `ibtool` 6.0 中的命令在 OS X 10.10 Yosemite 上有錯誤。 Xamarin 會使用 Xcode `ibtool`來編譯分鏡腳本和`XIB`檔案。

如需有關 Xcode 的詳細資訊，請參閱下列 Stack Overflow 文章：[https://stackoverflow.com/questions/25754763/cant-open-storyboard](https://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>錯誤訊息

> 無法開啟檔 "Mainstoryboard.storyboard"。 作業無法完成。 （InterfaceBuilder 錯誤-1）。

## <a name="workarounds-for-xcode-60"></a>因應措施（適用于 Xcode 6.0）

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>選項 1：在程式`UIImageView.Image`代碼中管理所有屬性

不是`UIImageView`在腳本`Image`或`.xib`檔案中設定的屬性，您可以在視圖控制器中的其中一個「視圖生命週期」覆寫方法中設定屬性（例如，在中`ViewDidLoad()`）。 如需使用`UIImage.FromBundle()` vs. `UIImage.FromFile()`的秘訣，另請參閱[使用影像](~/ios/app-fundamentals/images-icons/index.md)。

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>選項 2：將所有影像資源移到最上層`Resources`資料夾

將影像移至最上層`Resources`資料夾之後，您必須更新腳本和`.xib`檔案，以使用新的影像路徑。

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>選項3：針對任何有問題的影像資產設定，使其複製到配套的最上層`.app` `LogicalName`

例如，假設您`.csproj`的原始檔案包含下列專案：

`<BundleResource Include="Resources\Images\image.png" />`

您可以變更這個元素並加入， `LogicalName`如此一來，影像就會複製到配套的最上層： `.app`

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

在 Visual Studio for Mac 中`LogicalName` ，您也可以`Resource ID`使用 View 下影像的欄位來設定， **> pad > 屬性**。 （另請參閱[https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545](https://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545)：）

在這種變更之後，您必須更新腳本和`.xib`檔案，以使用新的最上層影像路徑。 Visual Studio for Mac 會自動更新 iOS 設計工具中`Image`屬性的 autocompletions 清單。 在 Visual Studio 中，您必須手動編輯路徑。 然後 iOS 設計工具會將此顯示為遺失的影像，但專案會正確地建立和執行。

### <a name="next-steps"></a>後續步驟

如需進一步的協助，請洽詢我們，或即使在使用上述資訊之後仍然會發生此問題，請參閱[什麼是適用于 Xamarin 的支援選項？](~/cross-platform/troubleshooting/support-options.md)以取得連絡人選項、建議，以及如何在需要時提出新 bug 的相關資訊. 

---
title: 更新現有的 iOS 應用程式
description: 本檔說明將 Classic API 的 Xamarin iOS 應用程式更新為 Unified API 時必須遵循的步驟。
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 9b531bd095781c80c5f3418725d57f8f6bbb06fd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015036"
---
# <a name="updating-existing-ios-apps"></a>更新現有的 iOS 應用程式

_請遵循下列步驟來更新現有的 Xamarin iOS 應用程式，以使用 Unified API。_

將現有的應用程式更新為使用 Unified API 需要變更專案檔本身，以及應用程式代碼中所使用的命名空間和 Api。

## <a name="the-road-to-64-bits"></a>The Road to 64 Bits

需要新的整合 Api，才能從 Xamarin iOS 行動應用程式支援64位裝置架構。 從2015年2月1日起，所有新的應用程式提交至 iTunes App Store 都必須支援64位架構。

Xamarin 提供 Visual Studio for Mac 和 Visual Studio 的工具，可將 Classic API 的遷移程式自動化至 Unified API，或者您可以手動轉換專案檔。 雖然強烈建議使用自動工具，但本文將涵蓋這兩種方法。

### <a name="before-you-start"></a>開始之前 。

在您將現有的程式碼更新為 Unified API 之前，強烈建議您排除所有的*編譯警告*。 當您遷移至整合之後，Classic API 中的許多*警告*都會變成錯誤。 在開始之前修正這些問題，會比較容易，因為來自 Classic API 的編譯器訊息通常會提供要更新之專案的提示。

## <a name="automated-updating"></a>自動更新

解決警告之後，請在 Visual Studio for Mac 或 Visual Studio 中選取現有的 iOS 專案，然後從 [**專案**] 功能表選擇 [**遷移至 Xamarin iOS Unified API** ]。 例如:

![](updating-ios-apps-images/beta-tool1.png "Choose Migrate to Xamarin.iOS Unified API from the Project menu")

您必須先同意此警告，才會執行自動遷移（顯然，您應該先確定您擁有備份/原始檔控制，再于此登機上進行此動作）：

![](updating-ios-apps-images/beta-tool2.png "Agree to this warning before the automated migration will run")

此工具基本上會自動執行 [**手動更新**] 一節中所述的所有步驟，這是將現有的 Xamarin iOS 專案轉換為 Unified API 的建議方法。

## <a name="steps-to-update-manually"></a>手動更新的步驟

同樣地，在修正警告之後，請遵循下列步驟來手動更新 Xamarin iOS 應用程式，以使用新的 Unified API：

### <a name="1-update-project-type--build-target"></a>1. 更新專案類型 & 組建目標

將 **.csproj**檔案中的專案類別從 `6BC8ED88-2882-458C-8E55-DFD12B67127B` 變更為 `FEACFBD2-3405-455C-9665-78FE426C6842`。 在文字編輯器中編輯 **.csproj**檔案，取代 `<ProjectTypeGuids>` 元素中的第一個專案，如下所示：

![](updating-ios-apps-images/csproj.png "Edit the csproj file in a text editor, replacing the first item in the ProjectTypeGuids element as shown")

將包含 `Xamarin.MonoTouch.CSharp.targets` 的**Import**元素變更為 `Xamarin.iOS.CSharp.targets`，如下所示：

![](updating-ios-apps-images/csproj2.png "Change the Import element that contains Xamarin.MonoTouch.CSharp.targets to Xamarin.iOS.CSharp.targets as shown")

### <a name="2-update-project-references"></a>2. 更新專案參考

展開 [iOS 應用程式] 專案的 [**參考**] 節點。 它一開始會顯示 * 與此螢幕擷取畫面類似的**monotouch**參考（因為我們剛剛變更了專案類型）：

![](updating-ios-apps-images/references.png "It will initially show a broken- monotouch reference similar to this screenshot because the project type changed")

以滑鼠右鍵按一下 iOS 應用程式專案以**編輯 [參考**]，然後按一下 [ **monotouch** ] 參考，並使用紅色的 [X] 按鈕將其刪除。

![](updating-ios-apps-images/references-delete-monotouch-sml.png "Right-click on the iOS application project to Edit References, then click on the monotouch reference and delete it using the red X button")

現在，流覽至 [參考] 清單結尾，然後勾選 [ **Xamarin. iOS** ] 元件。

![](updating-ios-apps-images/references-add-xamarinios-sml.png "Now scroll to the end of the references list and tick the Xamarin.iOS assembly")

按 **[確定]** 以儲存專案參考變更。

### <a name="3-remove-monotouch-from-namespaces"></a>3. 從命名空間移除 MonoTouch

從 `using` 語句中的命名空間移除**MonoTouch**前置詞，或在 classname 已完整限定的任何位置（例如 `MonoTouch.UIKit` 只會變成 `UIKit`）。

### <a name="4-remap-types"></a>4. 重新對應類型

引進的[原生類型](~/cross-platform/macios/nativetypes.md)會取代先前使用的某些類型，例如具有 `CoreGraphics.CGRect` 的 `System.Drawing.RectangleF` 實例（例如）。 您可以在 [[原生類型](~/cross-platform/macios/nativetypes.md)] 頁面上找到完整的類型清單。

### <a name="5-fix-method-overrides"></a>5. 修正方法覆寫

某些 `UIKit` 方法的簽章已變更為使用新的[原生類型](~/cross-platform/macios/nativetypes.md)（例如 `nint`）。 如果自訂子類別覆寫這些方法，簽章將不再相符，而且會導致錯誤。 使用原生類型變更子類別，使其符合新的簽章，以修正這些方法覆寫。

範例包括將 `public override int NumberOfSections (UITableView tableView)` 變更為傳回 `nint`，並將 `public override int RowsInSection (UITableView tableView, int section)` 中的傳回型別和參數類型變更為 `nint`。

## <a name="considerations"></a>考量

當現有的 Xamarin iOS 專案從 Classic API 轉換成新的 Unified API 時，如果該應用程式依賴一或多個元件或 NuGet 套件，則應該考慮下列事項。

### <a name="components"></a>元件

您包含在應用程式中的任何元件也必須更新為 Unified API，否則當您嘗試編譯時，將會發生衝突。 針對任何包含的元件，以支援 Unified API 的 Xamarin 元件存放區中的新版本取代目前的版本，並執行全新的組建。 作者尚未轉換的任何元件，都只會在元件存放區中顯示32位的警告。

### <a name="nuget-support"></a>NuGet 支援

雖然我們已將 NuGet 的變更提供給 Unified API 的支援，但並沒有新版本的 NuGet，因此我們正在評估如何取得 NuGet 來辨識新的 Api。

在該時間之前，您必須將包含在專案中的任何 NuGet 套件，切換到支援統一 Api 的版本，之後再執行全新的組建。

> [!IMPORTANT]
> If you have an error in the form _"Error 3 Cannot include both 'monotouch.dll' and 'Xamarin.iOS.dll' in the same Xamarin.iOS project - 'Xamarin.iOS.dll' is referenced explicitly, while 'monotouch.dll' is referenced by 'xxx, Version=0.0.000, Culture=neutral, PublicKeyToken=null'"_ after converting your application to the Unified APIs, it is typically due to having either a component or NuGet Package in the project that has not been updated to the Unified API. You'll need to remove the existing component/NuGet, update to a version that supports the Unified APIs and do a clean build.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>啟用 Xamarin iOS 應用程式的64位組建

對於已轉換為 Unified API 的 Xamarin iOS 行動應用程式，開發人員仍然必須從應用程式的選項啟用64位電腦的應用程式建立。 如需有關啟用64位組建的詳細指示，請參閱啟用[32/64 位平臺考慮](~/cross-platform/macios/32-and-64/index.md#enable-64)檔中的**64 位組建應用程式**。

## <a name="finishing-up"></a>完成

無論您是否選擇使用自動或手動方法，將您的 Xamarin iOS 應用程式從傳統轉換成統一的 Api，有數個實例需要進一步的手動介入。 如需已知問題和解決辦法，請參閱我們將[程式碼更新至 Unified API](~/cross-platform/macios/unified/updating-tips.md)檔的秘訣。

## <a name="related-links"></a>相關連結

- [將程式碼更新至 Unified API 的祕訣](~/cross-platform/macios/unified/updating-tips.md)
- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
- [Classic vs Unified API differences](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)

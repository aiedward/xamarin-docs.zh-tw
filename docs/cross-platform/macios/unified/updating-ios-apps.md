---
title: "更新現有的 iOS 應用程式"
description: "請依照下列步驟來更新現有使用統一的 API Xamarin.iOS 應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4eaa486fddc23ad18670bef4043a5adf30a1e9ac
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="updating-existing-ios-apps"></a>更新現有的 iOS 應用程式

_請依照下列步驟來更新現有使用統一的 API Xamarin.iOS 應用程式。_

更新現有的應用程式使用的統一的 API 需要變更專案檔本身以及命名空間和應用程式程式碼中使用的應用程式開發介面。

## <a name="the-road-to-64-bits"></a>為 64 位元路段圖

新的統一的 Api，才能支援從 Xamarin.iOS 行動應用程式的 64 位元裝置架構。 自 2015 年 2 月 1 日，Apple 要求所有新的應用程式送出至 iTunes App Store 支援 64 位元架構。

Xamarin 提供適用於 Mac 的 Visual Studio 和 Visual Studio 從傳統 API 整合應用程式開發介面的移轉程序自動化的工具，或您可以手動將轉換的專案檔。 強烈建議使用的工具，自動，而這份文件將涵蓋這兩種方法。

### <a name="before-you-start"></a>開始之前...

在統一的 API 來更新現有的程式碼之前，強烈建議您排除所有*編譯警告*。 許多*警告*傳統 API 中將會變成錯誤一旦移轉至整合。 在開始之前，請修正這些是更容易，因為傳統的應用程式開發介面的編譯器訊息通常會提供提示上要更新的項目。

## <a name="automated-updating"></a>自動更新

一旦已獲得修正警告，Mac 或 Visual Studio 的 Visual Studio 選取現有的 iOS 專案，然後選擇 **移轉到 Xamarin.iOS 統一的 API**從**專案**功能表。 例如: 

![](updating-ios-apps-images/beta-tool1.png "選擇移轉 Xamarin.iOS 統一的 API，從 [專案] 功能表")

必須先同意這個警告，才會執行自動的移轉 （當然您應該確定您採用此 adventure 之前有備份/原始檔控制）：

![](updating-ios-apps-images/beta-tool2.png "同意才能自動的移轉將會執行這項警告")

此工具基本上會自動化中概述的步驟**手動更新**區段下方所顯示，而且是轉換統一的 API Xamarin.iOS 現有專案的建議的方法。

## <a name="steps-to-update-manually"></a>若要手動更新的步驟

同樣地，一旦已獲得修正警告，請依照下列步驟來手動更新以使用新的統一的 API Xamarin.iOS 應用程式：

### <a name="1-update-project-type--build-target"></a>1.更新專案類型 （& s) 建置目標

變更專案類別，在您**csproj**檔案從`6BC8ED88-2882-458C-8E55-DFD12B67127B`至`FEACFBD2-3405-455C-9665-78FE426C6842`。 編輯**csproj**檔案在文字編輯器中，取代中的第一個項目`<ProjectTypeGuids>`項目所示：

![](updating-ios-apps-images/csproj.png "編輯於 csproj 檔案，在文字編輯器中，取代 ProjectTypeGuids 項目中的第一個項目所示")

變更**匯入**包含項目`Xamarin.MonoTouch.CSharp.targets`至`Xamarin.iOS.CSharp.targets`所示：

![](updating-ios-apps-images/csproj2.png "變更包含 Xamarin.iOS.CSharp.targets 的 Xamarin.MonoTouch.CSharp.targets 所示的匯入項目")

### <a name="2-update-project-references"></a>2.更新專案參考

展開 iOS 應用程式專案的**參考**節點。 一開始都會顯示 * 中斷- **monotouch**類似這個螢幕擷取畫面 （因為剛才已變更的專案類型） 的參考：

![](updating-ios-apps-images/references.png "它一開始都會顯示中斷 monotouch 參考類似這個螢幕擷取畫面因專案類型變更")

以滑鼠右鍵按一下 iOS 應用程式專案來**編輯參考**，然後按一下  **monotouch**參考，然後刪除它使用紅色"X"按鈕。

![](updating-ios-apps-images/references-delete-monotouch-sml.png "以滑鼠右鍵按一下要編輯的參考，在 iOS 應用程式專案上按一下 monotouch 參考上，刪除使用紅色 X 按鈕")

現在捲動到結尾 參考清單和刻度**Xamarin.iOS**組件。

![](updating-ios-apps-images/references-add-xamarinios-sml.png "現在捲動到結尾 參考清單和刻度 Xamarin.iOS 組件")

按**確定**儲存專案的參考變更。

### <a name="3-remove-monotouch-from-namespaces"></a>3.MonoTouch 移除命名空間

移除**MonoTouch**從中的命名空間前置詞`using`陳述式或任何類別名稱完全合格 （例如 `MonoTouch.UIKit` 只會變成`UIKit`)。

### <a name="4-remap-types"></a>4.重新對應型別

[原生類型](~/cross-platform/macios/nativetypes.md)已導入的取代先前使用過的例如執行個體的某些類型`System.Drawing.RectangleF`與`CoreGraphics.CGRect`（舉例來說）。 類型的完整清單位於[原生類型](~/cross-platform/macios/nativetypes.md)頁面。

### <a name="5-fix-method-overrides"></a>5.修正方法覆寫

某些`UIKit`方法都已經將其變更為使用新的簽章[原生類型](~/cross-platform/macios/nativetypes.md)(例如`nint`)。 自訂子類別覆寫這些方法的簽章就不再相符，將會導致錯誤。 變更以符合新的簽章使用原生類型的子類別，以修正這些方法的覆寫。

範例包括變更`public override int NumberOfSections (UITableView tableView)`傳回`nint`，並將變更傳回型別和參數類型中的`public override int RowsInSection (UITableView tableView, int section)`至`nint`。

## <a name="considerations"></a>考量

下列考量轉換現有的 Xamarin.iOS 專案從傳統應用程式開發介面，新的統一的 api 如果該應用程式依賴一或多個元件或 NuGet 封裝時應該納入考量。

### <a name="components"></a>元件

您的應用程式中加入的任何元件也需要更新，以統一的 API，或當您嘗試編譯時，您會收到發生衝突。 任何包含的元件，以支援統一的 API 的 Xamarin 元件存放區中的新版本取代目前的版本，執行乾淨的組建。 尚未轉換作者，任何元件會顯示只有警告元件存放區中的 32 位元。

### <a name="nuget-support"></a>NuGet 支援

雖然我們提供 NuGet 使用統一的 API 支援的變更時，有尚未新版的 NuGet，所以我們會評估如何取得 NuGet 辨識新的 Api。

這段時間，就像元件，直到您必須切換任何您要加入專案，以支援統一的 Api 版本的 NuGet 封裝，之後執行乾淨的組建。

> [!IMPORTANT]
> 如果您在表單中有錯誤_"錯誤 3 不能在相同的 Xamarin.iOS 專案中包含 'monotouch.dll' 和 'Xamarin.iOS.dll'-'monotouch.dll' 正由時明確地參考 'Xamarin.iOS.dll' ' xxx，版本 = 0.0.000，Culture = neutral，PublicKeyToken = null'"_之後轉換您的應用程式，以統一的 Api，它通常是因為有尚未更新統一的 API 的專案中的元件或 NuGet 封裝。 您必須移除現有元件/NuGet、 更新為支援統一的 Api 版本，執行乾淨的組建。

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>啟用 64 位元建置的 Xamarin.iOS 應用程式

Xamarin.iOS 行動應用程式已經轉換成統一的 API，開發人員仍必須啟用 64 位元電腦的應用程式，從應用程式的選項建立。 請參閱**啟用 64 位元建置的 Xamarin.iOS 應用程式**的[32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md#enable-64)建置文件，如需有關啟用 64 位元的詳細指示。

## <a name="finishing-up"></a>結束

不論您選擇使用自動或手動方法來從傳統轉換統一的 Api Xamarin.iOS 應用程式，有數個會進一步，需要手動介入的執行個體。 請參閱我們[統一的 API 更新程式碼的秘訣](~/cross-platform/macios/unified/updating-tips.md)文件的已知問題及解決方法。

## <a name="related-links"></a>相關連結

- [將程式碼更新至 Unified API 的祕訣](~/cross-platform/macios/unified/updating-tips.md)
- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
- [傳統的 vs 統一的 API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [移轉至統一的 API （影片）](http://university.xamarin.com/lightninglectures/migrating-to-the-unified-api)

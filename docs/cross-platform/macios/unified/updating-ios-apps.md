---
title: 更新現有的 iOS 應用程式
description: 本文件說明您必須依照更新至 Unified API 從 Classic API 的 Xamarin.iOS 應用程式的步驟。
ms.prod: xamarin
ms.assetid: 303C36A8-CBF4-48C0-9412-387E95024CAB
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4d506232903d4a94ac20a1fb9f93a39884d9099c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61213646"
---
# <a name="updating-existing-ios-apps"></a>更新現有的 iOS 應用程式

_請遵循下列步驟來更新現有的 Xamarin.iOS 應用程式，以使用統一的 API。_

更新現有的應用程式，以使用統一的 API 需要變更專案檔本身以及命名空間和 Api 應用程式程式碼中使用。

## <a name="the-road-to-64-bits"></a>邁向 64 位元

新的統一的 Api，才能支援從 Xamarin.iOS 行動應用程式的 64 位元裝置架構。 截至 2015 年 2 月 1 日起，Apple 會要求所有新應用程式提交至 iTunes App Store 支援 64 位元架構。

Xamarin 提供適用於 Visual Studio for Mac 和 Visual Studio 自動化移轉程序從傳統的 API，統一的 api 的工具，或您可以手動將轉換的專案檔。 但強烈建議使用自動工具，這篇文章將涵蓋這兩種方法。

### <a name="before-you-start"></a>開始之前...

在統一的 API 來更新現有的程式碼之前，強烈建議您排除所有*編譯警告*。 許多*警告*傳統 API 中會變成錯誤一旦移轉至整合。 在開始之前，請修正這些是您更輕鬆，因為編譯器訊息從傳統的 API 通常會提供提示上要更新的項目。

## <a name="automated-updating"></a>自動更新

已修正警告後, for Mac 或 Visual Studio，在 Visual Studio 中選取現有的 iOS 專案，然後選擇**移轉至 Xamarin.iOS 統一 API**從**專案**功能表。 例如：

![](updating-ios-apps-images/beta-tool1.png "從 [專案] 功能表中選擇 移轉至 Xamarin.iOS 統一 API")

您必須同意此警告，才會執行自動化的轉換 （顯然您應該確定您有備份/原始檔控制，再開始此 adventure）：

![](updating-ios-apps-images/beta-tool2.png "同意這項警告，才會執行自動化的轉換")

此工具基本上會自動化中概述的步驟**手動更新**區段下方顯示和是建議的方法，將現有的 Xamarin.iOS 專案轉換至統一的 API。

## <a name="steps-to-update-manually"></a>若要手動更新的步驟

同樣地，一旦已修正警告，請遵循下列步驟來手動更新以使用新的 Unified API Xamarin.iOS 應用程式：

### <a name="1-update-project-type--build-target"></a>1.更新專案類型] & [建置目標

變更專案類別，在您**csproj**從檔案`6BC8ED88-2882-458C-8E55-DFD12B67127B`至`FEACFBD2-3405-455C-9665-78FE426C6842`。 編輯**csproj**檔案，在文字編輯器中，取代中的第一個項目`<ProjectTypeGuids>`元素所示：

![](updating-ios-apps-images/csproj.png "編輯 csproj 檔案，在文字編輯器中，取代 ProjectTypeGuids 項目中的第一個項目，如所示")

變更**匯入**包含的項目`Xamarin.MonoTouch.CSharp.targets`到`Xamarin.iOS.CSharp.targets`所示：

![](updating-ios-apps-images/csproj2.png "變更包含 Xamarin.iOS.CSharp.targets 的 Xamarin.MonoTouch.CSharp.targets 所示的匯入項目")

### <a name="2-update-project-references"></a>2.更新專案參考

展開 iOS 應用程式專案**參考**節點。 它一開始會顯示 * 中斷- **monotouch**類似這個螢幕擷取畫面 （因為我們剛才已變更的專案類型） 的參考：

![](updating-ios-apps-images/references.png "它一開始都會顯示中斷 monotouch 參考類似此螢幕擷取畫面的專案類型變更因此")

以滑鼠右鍵按一下 iOS 應用程式專案，以**編輯參考**，然後按一下**monotouch**參考，並使用紅色"X"按鈕刪除它。

![](updating-ios-apps-images/references-delete-monotouch-sml.png "以滑鼠右鍵按一下 iOS 應用程式專案，以編輯的參考，則 monotouch 參考上按一下，然後刪除它使用紅色的 X 按鈕")

現在捲動至結尾的參考清單和刻度**Xamarin.iOS**組件。

![](updating-ios-apps-images/references-add-xamarinios-sml.png "現在捲動至 [參考清單] 和 刻度 Xamarin.iOS 組件的結尾")

按下**確定**以儲存專案的參考變更。

### <a name="3-remove-monotouch-from-namespaces"></a>3.MonoTouch 移除命名空間

移除**MonoTouch**中的命名空間前置詞`using`陳述式，或只要類別名稱完全合格 （例如。 `MonoTouch.UIKit` 只會變成`UIKit`)。

### <a name="4-remap-types"></a>4.重新對應類型

[原生型別](~/cross-platform/macios/nativetypes.md)已導入其取代了先前使用過的例如執行個體的某些型別`System.Drawing.RectangleF`與`CoreGraphics.CGRect`（舉例來說）。 類型的完整清單可於[原生類型](~/cross-platform/macios/nativetypes.md)頁面。

### <a name="5-fix-method-overrides"></a>5.修正方法覆寫

有些`UIKit`方法有其變更為使用新的簽章[原生型別](~/cross-platform/macios/nativetypes.md)(例如`nint`)。 如果自訂的子類別覆寫這些方法的簽章將不再符合，並會導致錯誤。 變更以符合新的簽章使用原生類型的子類別，以修正這些方法的覆寫。

範例包括變更`public override int NumberOfSections (UITableView tableView)`返回`nint`和變更傳回型別和參數中的型別`public override int RowsInSection (UITableView tableView, int section)`至`nint`。

## <a name="considerations"></a>考量

下列考量轉換現有的 Xamarin.iOS 專案從傳統的 API，為新的統一 API 如果該應用程式依賴一或多個元件或 NuGet 套件時應該納入考量。

### <a name="components"></a>元件

您已在您的應用程式中包含的任何元件也必須更新至 Unified API，或當您嘗試編譯時，您會收到發生衝突。 任何包含的元件，取代目前的版本中支援統一的 API 的 Xamarin 元件存放區的新版本並執行乾淨的組建。 任何尚未轉換作者的元件會顯示 32 位元元件存放區中僅有警告。

### <a name="nuget-support"></a>NuGet 支援

雖然我們提供 NuGet 使用的統一 API 支援的變更，有已有新版的 NuGet，因此我們正在評估如何取得 NuGet 來辨識新的 Api。

之前，請在這段時間，就像元件，您必須切換任何您已包含在您的專案，以支援統一的 Api 版本的 NuGet 套件，並在之後執行清除組建。

> [!IMPORTANT]
> 如果您在表單中有錯誤 _"錯誤 3 不能在相同的 Xamarin.iOS 專案中包含 'monotouch.dll' 和 'Xamarin.iOS.dll'-'monotouch.dll' 所參考時明確地參考 'Xamarin.iOS.dll' ' xxx，版本 = 0.0.000，文化特性 = 中性，PublicKeyToken = null'"_ 之後轉換至 Unified Api 的應用程式，它通常是因為有尚未更新至 Unified API 專案中的元件或 NuGet 套件。 您必須移除現有的元件/NuGet 更新為支援統一的 Api 版本，執行乾淨的組建。

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>啟用 64 位元組建的 Xamarin.iOS 應用程式

Xamarin.iOS 行動應用程式已轉換至統一的 API，開發人員仍然需要啟用建置 64 位元電腦的應用程式，從應用程式的選項。 請參閱**啟用 64 位元建置的 Xamarin.iOS 應用程式**的[32/64 位元平台考量](~/cross-platform/macios/32-and-64/index.md#enable-64)建置文件，如需啟用 64 位元的詳細指示。

## <a name="finishing-up"></a>結論

您選擇使用自動或手動方法來將您的 Xamarin.iOS 應用程式從傳統轉換至 Unified Api 時，有數個會進一步，需要手動介入的執行個體。 請參閱我們[更新至 Unified API 的程式碼的秘訣](~/cross-platform/macios/unified/updating-tips.md)文件的已知問題及解決方法。

## <a name="related-links"></a>相關連結

- [將程式碼更新至 Unified API 的祕訣](~/cross-platform/macios/unified/updating-tips.md)
- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
- [傳統的 vs 統一的 API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [移轉至 Unified API （影片）](http://university.xamarin.com/lightninglectures/migrating-to-the-unified-api)

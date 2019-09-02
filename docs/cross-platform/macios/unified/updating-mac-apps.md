---
title: 更新現有的 Mac 應用程式
description: 本檔說明將 Classic API 的 Xamarin. Mac 應用程式更新為 Unified API 時必須遵循的步驟。
ms.prod: xamarin
ms.assetid: 26673CC5-C1E5-4BAC-BEF4-9A386B296FD5
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: a6d245e60a0d163c054fd74e0fb64780225da031
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197801"
---
# <a name="updating-existing-mac-apps"></a>更新現有的 Mac 應用程式

將現有的應用程式更新為使用 Unified API 需要變更專案檔本身, 以及應用程式代碼中所使用的命名空間和 Api。

## <a name="the-road-to-64-bits"></a>64位的道路

需要新的整合 Api, 才能從 Xamarin 應用程式支援64位的裝置架構。 從2015年2月1日起, Apple 要求所有新的應用程式提交至 Mac App Store 都支援64位架構。

Xamarin 提供 Visual Studio for Mac 和 Visual Studio 的工具, 可將 Classic API 的遷移程式自動化至 Unified API, 或者您可以手動轉換專案檔。 雖然強烈建議使用自動工具, 但本文將涵蓋這兩種方法。

### <a name="before-you-start"></a>開始之前 。

在您將現有的程式碼更新為 Unified API 之前, 強烈建議您排除所有的*編譯警告*。 當您遷移至整合之後, Classic API 中的許多*警告*都會變成錯誤。 在開始之前修正這些問題, 會比較容易, 因為來自 Classic API 的編譯器訊息通常會提供要更新之專案的提示。

## <a name="automated-updating"></a>自動更新

解決警告之後, 請在 Visual Studio for Mac 或 Visual Studio 中選取現有的 Mac 專案, 然後從 [**專案**] 功能表選擇 [**遷移至 Xamarin. Mac Unified API** ]。 例如：

![](updating-mac-apps-images/beta-tool1.png "從 [專案] 功能表選擇 遷移至 Xamarin. Mac Unified API")

您必須先同意此警告, 才會執行自動遷移 (顯然, 您應該先確定您擁有備份/原始檔控制, 再于此登機上進行此動作):

![](updating-mac-apps-images/migrate01.png "在執行自動遷移之前, 請同意此警告")

在 Xamarin. Mac 應用程式中使用 Unified API 時, 可以選取兩種支援的目標架構類型:

- **Xamarin. Mac 行動架構**-這是 Xamarin. IOS 和 xamarin 所使用的微調 .net Framework, 支援完整**桌面**架構的子集。 這是建議的架構, 因為它會提供較高的平均二進位檔, 因為這是上層連結行為。
- **Xamarin .net 4.5 framework** -此架構又是**桌面**架構的子集。 不過, 它會修剪比行動架構更少的完整**桌面**架構 , 而且應該「_僅_」與大部分的 NuGet 套件或協力廠商程式庫搭配使用。 這可讓開發人員取用標準**桌面**元件, 同時仍使用支援的架構, 但此選項會產生較大的應用程式套件組合。 這是建議的架構, 其中使用的是協力廠商 .NET 元件, 與**Xamarin. Mac 行動架構**不相容。 如需支援的元件清單, 請參閱我們的[元件](~/cross-platform/internals/available-assemblies.md)檔。

如需有關目標 Framework 的詳細資訊, 以及為您的 Xamarin 應用程式選取特定目標的含意, 請參閱我們的[目標 framework](~/mac/platform/target-framework.md)檔。 

此工具基本上會自動執行 [**手動更新**] 一節中所述的所有步驟, 這是將現有的 Xamarin. Mac 專案轉換為 Unified API 的建議方法。

## <a name="steps-to-update-manually"></a>手動更新的步驟

同樣地, 在修正警告之後, 請遵循下列步驟來手動更新 Xamarin. Mac 應用程式, 以使用新的 Unified API:

### <a name="1-update-project-type--build-target"></a>1.更新專案類型 & 組建目標

將您的 **.csproj**檔案中的專案類別`42C0BBD9-55CE-4FC1-8D90-A7348ABAFB23` , `A3F8F2AB-B479-4A4A-A458-A89E7DC349F1`從變更為。 在文字編輯器中編輯 **.csproj**檔案, 取代`<ProjectTypeGuids>`元素中的第一個專案, 如下所示:

![](updating-mac-apps-images/csproj.png "在文字編輯器中編輯 .csproj 檔案, 取代 ProjectTypeGuids 元素中的第一個專案, 如下所示")

將`Xamarin.Mac.targets`包含**的匯入**元素變更為,如下所示:`Xamarin.Mac.CSharp.targets`

![](updating-mac-apps-images/csproj2.png "將包含 Xamarin .targets 目標的 Import 元素變更為 Xamarin。")

在`<AssemblyName>`元素後面新增下列程式程式碼:

```xml
<TargetFrameworkVersion>v2.0</TargetFrameworkVersion>
<TargetFrameworkIdentifier>Xamarin.Mac</TargetFrameworkIdentifier>

```

範例：

![在\<AssemblyName > 元素後面加入這些程式程式碼](updating-mac-apps-images/csproj3.png)

### <a name="2-update-project-references"></a>2.更新專案參考

展開 [Mac 應用程式] 專案的 [**參考**] 節點。 它一開始會顯示 * 與此螢幕擷取畫面類似的**XamMac**參考 (因為我們剛剛變更了專案類型):

![](updating-mac-apps-images/references.png "它一開始會顯示類似此螢幕擷取畫面的 XamMac 參考")

按一下 [ **XamMac** ] 專案旁邊的**齒輪圖示**, 然後選取 [**刪除**] 以移除中斷的參考。

接下來, 以滑鼠右鍵按一下**方案總管**中的 [**參考**] 資料夾, 然後選取 [**編輯參考**]。 向下流覽至參考清單底部, 並勾選 [ **Xamarin. Mac**除外]。

![](updating-mac-apps-images/references2.png "流覽至參考清單底部, 並加上 Xamarin. Mac 以外的檢查")

按 **[確定]** 以儲存專案參考變更。

### <a name="3-remove-monomac-from-namespaces"></a>3.從命名空間移除 MonoMac

從語句中的命名空間移除`using` MonoMac 前置詞, 或在 classname 已完整限定的任何位置 (例如 `MonoMac.AppKit`只`AppKit`會變成)。

### <a name="4-remap-types"></a>4.重新對應類型

引進的[原生類型](~/cross-platform/macios/nativetypes.md)會取代先前使用的某些類型, 例如`System.Drawing.RectangleF`具有`CoreGraphics.CGRect`的實例 (例如)。 您可以在 [[原生類型](~/cross-platform/macios/nativetypes.md)] 頁面上找到完整的類型清單。

### <a name="5-fix-method-overrides"></a>5.修正方法覆寫

某些`AppKit`方法的簽章已變更為使用新的[原生類型](~/cross-platform/macios/nativetypes.md)(例如`nint`)。 如果自訂子類別覆寫這些方法, 簽章將不再相符, 而且會導致錯誤。 使用原生類型變更子類別, 使其符合新的簽章, 以修正這些方法覆寫。 

## <a name="considerations"></a>考量

將現有的 Xamarin. Mac 專案從 Classic API 轉換成新的 Unified API 時, 如果該應用程式依賴一或多個元件或 NuGet 套件, 則應該考慮下列事項。 

### <a name="components"></a>元件

您包含在應用程式中的任何元件也必須更新為 Unified API, 否則當您嘗試編譯時, 將會發生衝突。 針對任何包含的元件, 以支援 Unified API 的 Xamarin 元件存放區中的新版本取代目前的版本, 並執行全新的組建。 作者尚未轉換的任何元件, 都只會在元件存放區中顯示32位的警告。

### <a name="nuget-support"></a>NuGet 支援

雖然我們已將 NuGet 的變更提供給 Unified API 的支援, 但並沒有新版本的 NuGet, 因此我們正在評估如何取得 NuGet 來辨識新的 Api。 

在該時間之前, 您必須將包含在專案中的任何 NuGet 套件, 切換到支援統一 Api 的版本, 之後再執行全新的組建。

> [!IMPORTANT]
> 如果您的錯誤格式為「_錯誤3不能同時在同一個 Xamarin. mac 專案中同時包含 ' monomac ' 和 ' xamarin ', 則會明確參考 ' monomac ', 而 ' ' 則是由 ' xxx, Version = 0.0.000, Culture = 中性,PublicKeyToken = null ' "_ 將您的應用程式轉換成統一的 api 之後, 通常是因為專案中的元件或 NuGet 套件尚未更新為 Unified API。 您必須移除現有的元件/NuGet、更新為支援統一 Api 的版本, 並執行全新的組建。

## <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>啟用 Xamarin. Mac 應用程式的64位組建

針對已轉換為 Unified API 的 Xamarin 行動應用程式, 開發人員仍然需要從應用程式的選項啟用64位電腦的應用程式建立。 如需有關啟用64位組建的詳細指示, 請參閱啟用[32/64 位平臺考慮](~/cross-platform/macios/32-and-64/index.md)檔中的**64 位組建 Xamarin. Mac 應用程式**。

## <a name="finishing-up"></a>完成

無論您是否選擇使用自動或手動方法, 將您的 Xamarin. Mac 應用程式從傳統轉換成統一的 Api, 有數個實例需要進一步的手動介入。 如需已知問題和解決辦法, 請參閱我們將[程式碼更新至 Unified API](~/cross-platform/macios/unified/updating-tips.md)檔的秘訣。

## <a name="related-links"></a>相關連結

- [將程式碼更新至 Unified API 的祕訣](~/cross-platform/macios/unified/updating-tips.md)
- [在跨平台應用程式中使用原生型別](~/cross-platform/macios/native-types-cross-platform.md)
- [傳統與 Unified API 差異](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)

---
title: XAML 熱重載 Xamarin.Forms
description: 在您的執行中應用程式上，立即重載 XAML 檔案的變更，讓您不必在 Xamarin.Forms 每次 XAML 變更之後建立您的專案。
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/01/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7a0eb6eadb43fd191c5939270dca70a8c0ac7f3e
ms.sourcegitcommit: 3aa9bdcaaedca74ab5175cb2338a1df122300243
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749365"
---
# <a name="xaml-hot-reload-for-xamarinforms"></a>XAML 熱重載 Xamarin.Forms

XAML 熱重載會插入您現有的工作流程，以提高生產力並節省時間。 如果沒有使用 XAML 熱重載，每當您想要查看 XAML 變更時，都必須建立和部署應用程式。 使用熱重載時，當您儲存 XAML 檔案時，變更會反映在執行中的應用程式中。 此外，您的流覽狀態和資料將會保留，讓您可以快速地逐一查看您的 UI，而不會遺失您在應用程式中的位置。 因此，使用 XAML 熱重載，您將會花較少的時間來重建及部署應用程式，以驗證 UI 變更。

[!NOTE]
如果您要撰寫原生 UWP 或 WPF 應用程式，而不是使用 Xamarin.Forms ，請參閱 [適用于 UWP 和 WPF 的 XAML 熱重載](/visualstudio/debugger/xaml-hot-reload)。

## <a name="system-requirements"></a>系統需求

| IDE/架構 | 需要的最低版本 |
|------|------------------|
|Visual Studio 2019 | 16.9 適用于僅限變更模式，16.4 適用于全頁模式
Visual Studio 2019 for Mac | 8.9 適用于僅限變更模式，8.4 適用于全頁模式
Xamarin.Forms | 5.0 （僅適用于變更模式）;適用于整頁模式的4。1

## <a name="enable-xaml-hot-reload-for-xamarinforms"></a>啟用 XAML 熱重載 Xamarin.Forms

如果您是從範本開始，則預設會開啟 XAML 熱重載，而且會將專案設定為不使用額外的設定。 在模擬器或實體裝置上進行 Android、iOS 或 UWP 應用程式的偵錯工具，並變更 XAML 以觸發 XAML 熱重載。

如果您是從現有的 Xamarin.Forms 解決方案工作，則不需要額外的安裝就能使用 XAML 熱重載，但您可能必須再次檢查您的設定，以確保獲得最佳體驗。 首先，在您的 IDE 設定中啟用它：

* 在 Windows 上，核取 [**啟用 XAML 熱重載**] 核取方塊 (以及在 [**工具** 選項] 中偵測  >    >    >  **熱重載**) 所需的平臺。
  * 在舊版的 Visual Studio 2019 中，此核取方塊位於 **工具**  >  **選項**[  >  **Xamarin**  >  **熱重載**]。
* 在 Mac 上，核取 [ **Visual Studio** 喜好設定工具] 中的 [**啟用 xamarin 熱重載**] 核取方塊，  >    >  **適用于 Xamarin**  >  **XAML 熱重載**。
  * 在舊版 visual studio for Mac 中，此核取方塊位於 **visual studio**  >  **喜好** 設定  >  **專案**  >  **Xamarin 熱重載**。

然後，在您的 Android 和 iOS 組建設定中，檢查連結器設定為 [不要連結] 或 [連結無]。 若要在實體 iOS 裝置上使用 XAML 熱重載，您也必須核取 **[啟用 Mono 解釋** 器] (visual studio 16.4 和更新版本) 或將 **--解釋** 器新增至 **其他 Mtouch** 引數 (visual studio 16.3 和以下) 。

您可以使用下列流程圖來檢查現有專案的設定，以搭配使用 XAML 熱重載：

![XAML 熱重載設定](hot-reload-images/hotreloadflowchart.png "XAML 熱重載設定流程圖")

<!-- https://aka.ms/xamarin-hot-reload-mode points to this section -->
## <a name="hot-reload-modes"></a>熱重載模式

XAML 熱重載可在兩種不同的模式下運作，也就是較新的 *變更模式* 和較舊的 *整頁模式*。

從 Visual Studio 16.9 和 Visual Studio for Mac 8.9，預設行為是針對使用 Xamarin.Forms 5.0 或更新版本的所有應用程式使用只變更模式。 針對較舊的版本 Xamarin.Forms ，會使用完整的頁面模式。 不過，您可以在 [熱重載 IDE 設定] 中，針對所有應用程式強制使用完整頁面模式 (**工具**  >  **選項**：  >    >  在 Windows 上進行 **熱重載** 的偵錯工具，或在) Mac 上使用 **Visual Studio 的 Visual Studio**  >  **喜好** 設定工具上的 [  >  **Xamarin**  >  **XAML 熱**

*只有* 當您進行編輯時，才會剖析 XAML 以查看變更的內容，並只將這些變更傳送至執行中的應用程式。 這是用於 WPF 和 UWP 熱重載的相同技術。 它會保留 UI 狀態，因為它不會重新建立整個頁面的 UI，只是更新受編輯影響之控制項的變更屬性。 [僅變更] 模式也可讓您使用 [即時視覺化樹狀結構](live-visual-tree.md)。

依預設，使用 [僅變更] 模式時，您不需要儲存檔案來查看變更，而是在您輸入時立即套用更新。
不過，您可以將此行為變更為只在儲存檔案時才更新。 若要完成此動作，請勾選 [在 **檔儲存時套用 XAML 熱重載** ] 核取方塊 (目前僅適用于 Windows) 的熱重載 IDE 設定。 如果您進行較大的 XAML 更新，而不希望它們在完成之前都能顯示，則只有在檔儲存上的更新有時可能會很有用。 

當您進行編輯並儲存之後，*完整頁面模式* 會將完整的 XAML 檔案傳送至執行中的應用程式。 接著，執行中的應用程式會重載頁面、重新建立其控制項，您將會看到 UI 重新整理。

只變更模式是熱重載的未來，建議您盡可能使用它。 它很快就會保留 UI 狀態，並支援 [即時視覺化樹狀結構](live-visual-tree.md)。 仍會為尚未更新為5.0 的應用程式提供完整的頁面模式 Xamarin.Forms 。 

> [!NOTE]
> 切換模式時，您必須重新開機調試會話。

## <a name="xaml-errors"></a>XAML 錯誤

*僅變更模式*：如果您進行變更，當熱重載 XAML 剖析器看到無效時，它會在編輯器中顯示標示為不正確錯誤，並將其包含在錯誤視窗中。 這些熱重載錯誤的錯誤碼開頭為 "XHR" (for XAML 熱重載) 。 如果頁面上有任何這類錯誤，熱重載將不會套用變更，即使是在頁面的其他部分也一樣。 修正熱重載的所有錯誤，以再次開始動作頁面。

*整頁模式*：如果您進行 XAML 熱重載無法重載的變更，它會在編輯器中顯示標示為底線的錯誤，並將其包含在錯誤視窗中。 這些變更（稱為「修改中」）包括不正確的 XAML，或將控制項連接到不存在的事件處理常式。 即使有了可進行的編輯，您仍可繼續重載，而不需要重新開機應用程式-請在 XAML 檔案中的其他位置進行其他變更，然後點擊儲存。 不會重載 [強制編輯]，但會繼續套用其他變更。

## <a name="reload-on-multiple-platforms-at-once"></a>一次在多個平臺上重載

XAML 熱重載支援在 Visual Studio 和 Visual Studio for Mac 中同時進行偵錯工具。 您可以同時部署 Android 和 iOS 目標，以查看您的變更一次反映在兩個平臺上。 若要在多個平臺上進行調試，請參閱：

* **Windows** how [To：設定多個啟始專案](/visualstudio/ide/how-to-set-multiple-startup-projects?view=vs-2019)
* **Mac** [設定多個啟始專案](/visualstudio/mac/set-startup-projects?view=vsmac-2019)

## <a name="known-limitations"></a>已知的限制

* Xamarin.Forms Android、iOS 和 UWP 以外的目標 (例如，目前不支援 macOS) 。
* 使用 [XamlCompilation (XamlCompilationOptions. Skip) ]，不支援停用 XAML 編譯，而且可能會導致即時視覺化樹狀結構發生問題。
* 您無法在 XAML 熱重載會話期間加入、移除或重新命名檔案或 NuGet 套件。 如果您新增或移除檔案或 NuGet 套件，請重建並重新部署您的應用程式，以繼續使用 XAML 熱重載。
* 將連結器設定為 [ **不連結** ] 或 [ **無] 連結** ，以獲得最佳體驗。 [ **僅連結 SDK** ] 設定會在大部分的時間內運作，但在某些情況下可能會失敗。 連結器設定可以在您的 Android 和 iOS 組建選項中找到。
* 若要在實體 iPhone 上進行偵錯工具，解譯器必須使用 XAML 熱重載。 若要這樣做，請開啟專案設定，選取 [iOS 組建] 索引標籤，並確定已啟用 **[啟用 Mono 解釋** 器] 設定。 您可能需要將屬性頁面頂端的 **平臺** 選項變更為 **iPhone**。
* XAML 熱重載無法重載 c # 程式碼，包括事件處理常式、自訂控制項、頁面程式碼後端和其他類別。


## <a name="troubleshooting"></a>疑難排解

* 開啟 XAML 熱重載輸出以查看狀態訊息，這有助於進行疑難排解：
  * **Windows**：顯示具有 **View**  >  **output** 的輸出，然後選取 [**顯示輸出來源**] 下方的 [ **Xamarin 熱重載**]   
  * **Mac**：將滑鼠停留在狀態列中的 **XAML 熱重載** 上方，以顯示該板
* 如果 XAML 熱重載無法初始化：
  * 更新您的 Xamarin.Forms 版本。
  * 確定您是在最新版本的 IDE 上。
  * 將您的 Android 或 iOS 連結器設定設為 [ **不要連結** ] 專案的組建設定。
* 若儲存 XAML 檔案時沒有發生任何事，請確定已在 IDE 中啟用 XAML 熱重載。
* 如果您是在實體 iPhone 上進行偵錯工具，而您的應用程式變成沒有回應，請檢查是否已啟用解譯器。 若要開啟它，請核取 [ **啟用 Mono 解釋** 器] (visual studio 16.4/8.4 和 up) 或將 **--解釋** 器新增至 [ **其他 mtouch 引數** ] 欄位 (Visual Studio 16.3/8.3，以及 iOS 組建設定中的先前) 。

若要回報 bug，請 **使用 [** 說明  >  **傳送意見** 反應]  >  **報告 Windows 上的問題**，並 **協助** 您  >  回報 Mac 上 **的問題**。

## <a name="related-links"></a>相關連結

- [即時視覺化樹狀結構](live-visual-tree.md)
- [XAML 熱重載的秘訣和訣竅](https://devblogs.microsoft.com/xamarin/tips-tricks-xaml-hot-reload/)
- [深入瞭解 XAML 熱重載 Xamarin.Forms ： Xamarin 節目](https://www.youtube.com/watch?v=crhjjPjzknk)

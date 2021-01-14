---
title: XAML 熱重新載入 Xamarin.Forms
description: 在您的執行中應用程式上，立即重載 XAML 檔案的變更，讓您不必在 Xamarin.Forms 每次 XAML 變更之後建立您的專案。
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 01/14/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 62c7be9bd737b4d1860dca9f75cdde6acfadaa96
ms.sourcegitcommit: 99e340360e8615fbc2971f48d6856f8701594825
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98204095"
---
# <a name="xaml-hot-reload-for-no-locxamarinforms"></a>XAML 熱重新載入 Xamarin.Forms

XAML 熱重新載入插入您現有的工作流程，以提高生產力並節省時間。 若沒有 XAML 熱重新載入，您就必須在每次想要查看 XAML 變更時，建立及部署您的應用程式。 使用熱重新載入時，當您儲存 XAML 檔案時，變更會即時反映在執行中的應用程式中。 此外，您的流覽狀態和資料將會保留，讓您可以快速地逐一查看您的 UI，而不會遺失您在應用程式中的位置。 因此，使用 XAML 熱重新載入時，您將會花較少的時間來重建及部署應用程式，以驗證 UI 變更。

> [!NOTE]
> 如果您要撰寫 WPF 或 UWP 應用程式，請參閱 [適用于 UWP 和 WPF 的 XAML 熱重新載入](/visualstudio/debugger/xaml-hot-reload)。
>
> 針對 Xamarin.Forms UWP 專案目前 _無法_ 使用的 XAML 熱重新載入 Xamarin.Forms 。

## <a name="system-requirements"></a>系統需求

| IDE/架構 | 需要的版本 |
|------|------------------|
|Visual Studio 2019 | 16.4 或更高版本
Visual Studio 2019 for Mac | 8.4 或更高版本
Xamarin.Forms | 4.1 或更高版本

## <a name="enable-xaml-hot-reload-for-no-locxamarinforms"></a>啟用 XAML 熱重新載入 Xamarin.Forms

如果您是從範本開始，XAML 熱重新載入預設為開啟，且專案設定為不使用額外的設定。 在 Android 或 iOS 模擬器、模擬器或實體裝置上對應用程式進行偵錯工具、變更您的 XAML，並儲存您的檔案以觸發 XAML 熱重新載入。

如果您是從現有的 Xamarin.Forms 解決方案工作，則不需要額外的安裝就能使用 XAML 熱重新載入，但您可能必須再次檢查您的設定，以確保獲得最佳體驗。 首先，在您的 IDE 設定中啟用它：

* 在 Windows 上，勾選 [**啟用 XAML 熱重新載入**] 核取方塊 (和 [**工具**  >  **選項**]  >    >  **熱重新載入** 所需的平臺) 。
  * 在舊版的 Visual Studio 2019 中，此核取方塊位於[  >    >  **Xamarin**  >  **熱重新載入** 的 [工具選項]。
* 在 Mac 上， 勾選  >    >  **適用于 xamarin**  >  **XAML 熱重新載入** Visual Studio 喜好設定工具的 [啟用 Xamarin 熱重新載入] 核取方塊。
  * 在舊版 Visual Studio for Mac 中，此核取方塊位於 **Visual Studio**  >  **喜好** 設定  >  **專案**  >  **Xamarin 熱重新載入**。

然後，在您的 Android 和 iOS 組建設定中，檢查連結器設定為 [不要連結] 或 [連結無]。 若要搭配使用 XAML 熱重新載入與實體 iOS 裝置，您也必須核取 **[啟用 Mono 解釋** 器] (Visual Studio 16.4 和更新版本) 或將 **--解釋** 器新增至 **其他 mtouch** 引數 (Visual Studio 16.3 和以下) 。

您可以使用下列流程圖來檢查現有專案的設定，以搭配 XAML 熱重新載入使用：

![XAML 熱重新載入設定](hot-reload-images/hotreloadflowchart.png "XAML 熱重新載入設定流程圖")

## <a name="resilient-reloading"></a>復原重載

如果您進行 XAML 熱重新載入無法重載的變更，它會使用 IntelliSense 顯示錯誤訊息。 這些變更（稱為「修改中」）包括不正確的 XAML，或將控制項連接到不存在的事件處理常式。 即使有了可進行的編輯，您仍可繼續重載，而不需要重新開機應用程式-請在 XAML 檔案中的其他位置進行其他變更，然後點擊儲存。 不會重載 [強制編輯]，但會繼續套用其他變更。

## <a name="reload-on-multiple-platforms-at-once"></a>一次在多個平臺上重載

XAML 熱重新載入支援 Visual Studio 和 Visual Studio for Mac 的同時調試。 您可以同時部署 Android 和 iOS 目標，以查看您的變更一次反映在兩個平臺上。 若要在多個平臺上進行調試，請參閱：
* **Windows** how [To：設定多個啟始專案](/visualstudio/ide/how-to-set-multiple-startup-projects?view=vs-2019)
* **Mac** [設定多個啟始專案](/visualstudio/mac/set-startup-projects?view=vsmac-2019)

## <a name="known-limitations"></a>已知的限制

* Xamarin.Forms尚 *不* 支援其他目標（例如 UWP 和 macOS）。 您可以在 [這裡](https://developercommunity.visualstudio.com/idea/661682/xaml-hot-reload-for-xamarinforms-on-uwp.html)追蹤 UWP 支援的進度。
* 您無法在 XAML 熱重新載入會話期間新增、移除或重新命名檔案或 NuGet 套件。 如果您新增或移除檔案或 NuGet 套件，請重建並重新部署您的應用程式，以繼續使用 XAML 熱重新載入。
* 將連結器設定為 [ **不連結** ] 或 [ **無] 連結** ，以獲得最佳體驗。 [ **僅連結 SDK** ] 設定會在大部分的時間內運作，但在某些情況下可能會失敗。 連結器設定可以在您的 Android 和 iOS 組建選項中找到。
* 若要在實體 iPhone 上進行偵錯工具，必須使用 XAML 熱重新載入的解譯器。 若要這樣做，請開啟專案設定，選取 [iOS 組建] 索引標籤，並確定已啟用 **[啟用 Mono 解釋** 器] 設定。 您可能需要將屬性頁面頂端的 **平臺** 選項變更為 **iPhone**。
* 藉由將控制項指派給另一個使用其值的欄位或屬性所建立的參考， `x:Name` 將不會重載。
* 在 AppShell 中更新 Shell 應用程式的視覺化階層，可能會造成問題，以維護應用程式的狀態。 如果您遇到問題，請重建應用程式以繼續重載。
* XAML 熱重新載入無法重載 c # 程式碼，包括事件處理常式、自訂控制項、頁面程式碼後端和其他類別。

## <a name="more-resources"></a>其他資源

* [XAML 熱重新載入的秘訣和訣竅](https://devblogs.microsoft.com/xamarin/tips-tricks-xaml-hot-reload/)
* [深入瞭解的 XAML 熱重新載入 Xamarin.Forms ： Xamarin 節目](https://www.youtube.com/watch?v=crhjjPjzknk)

## <a name="troubleshooting"></a>疑難排解

* 如果 XAML 熱重新載入無法初始化：
  * 更新您的 Xamarin.Forms 版本。
  * 確定您是在最新版本的 IDE 上。
  * 將您的 Android 或 iOS 連結器設定設為 [ **不要連結** ] 專案的組建設定。
* 若儲存 XAML 檔案時沒有發生任何事，請確定已在 IDE 中啟用 XAML 熱重新載入。
* 如果您是在實體 iPhone 上進行偵錯工具，而您的應用程式變成沒有回應，請檢查是否已啟用解譯器。 若要開啟它，請核取 [ **啟用 Mono 解釋** 器 (Visual Studio 16.4/8.4 和向上) ，或將 **--解釋** 器新增至 [ **其他 mtouch 引數** ] 欄位 (Visual Studio 16.3/8.3 和先前在 iOS 組建設定中) 。

若要回報 bug，請 **使用 [** 說明  >  **傳送意見** 反應]  >  **報告 Windows 上的問題**，並 **協助** 您  >  回報 Mac 上 **的問題**。

---
title: 的 XAML 熱重載Xamarin.Forms
description: 在執行中的應用程式上立即重載 XAML 檔案的變更，讓您不需要在 Xamarin.Forms 每次 XAML 變更之後建立專案。
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/14/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0655739c95ba58b8d93aae6d3987d54bd0582c7b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84127448"
---
# <a name="xaml-hot-reload-for-xamarinforms"></a>的 XAML 熱重載Xamarin.Forms

XAML 熱重載會插入您現有的工作流程，以提高您的生產力並節省您的時間。 如果沒有 XAML 熱重載，您就必須在每次想要查看 XAML 變更時，建立並部署您的應用程式。 使用熱重載時，當您儲存 XAML 檔案時，變更會即時反映在執行中的應用程式中。 此外，您的導覽狀態和資料將會維護，讓您快速反復查看 UI，而不會遺失您在應用程式中的位置。 因此，使用 XAML 熱重載，您會花較少的時間重建和部署應用程式，以驗證 UI 變更。

> [!NOTE]
> 如果您要撰寫 WPF 或 UWP 應用程式，請參閱[適用于 UWP 和 WPF 的 XAML 熱重載](/visualstudio/debugger/xaml-hot-reload)。
>
> 的 XAML 熱重載 Xamarin.Forms 目前_不適_用於 Xamarin.Forms UWP 專案。

## <a name="system-requirements"></a>系統需求

| IDE/架構 | 需要的版本 |
|------|------------------|
|Visual Studio 2019 | 16.4 或更高版本
Visual Studio 2019 for Mac | 8.4 或更高版本
Xamarin.Forms | 4.1 或更高版本

## <a name="enable-xaml-hot-reload-for-xamarinforms"></a>啟用的 XAML 熱重載Xamarin.Forms

如果您是從範本開始，則預設會開啟 XAML 經常性載入，且專案會設定為使用，而不需要額外的設定。 在 Android 或 iOS 模擬器、模擬器或實體裝置上，對您的應用程式進行 Debug，變更您的 XAML，並儲存您的檔案以觸發 XAML 熱重載。

如果您是從現有的 Xamarin.Forms 解決方案工作，則不需要進行其他安裝即可使用 XAML 熱重載，但您可能必須再次檢查您的設定，以確保獲得最佳體驗。 首先，在您的 IDE 設定中啟用它：

* 在 Windows 上，核取 [**工具**] 選項 [xamarin 熱重載] 的 [**啟用 Xamarin 熱重載**] 核取方塊  >  **Options**  >  **Xamarin**  >  ** **。
* 在 Mac 上，核取 [ **Visual Studio**為 xamarin XAML 熱重載 Visual Studio 喜好設定工具] 上的 [**啟用 xamarin 熱重載**] 核取方塊  >  **Preferences**  >  **Tools for Xamarin**  >  ** **。
  * 在舊版的 Visual Studio for Mac 中，功能表位於**Visual Studio**  >  **喜好**設定  >  **專案**[  >  **Xamarin 熱重載**]。

然後，在您的 Android 和 iOS 組建設定中，檢查連結器是否設定為 [不要連結] 或 [無連結]。 若要搭配使用 XAML 熱重載與實體 iOS 裝置，您也必須核取 **[啟用 Mono 解釋**器（Visual Studio 16.4 和更新版本）] 或 [新增 **--解釋**器] 至您的**其他 mtouch**引數（Visual Studio 16.3 和更新版本）。

您可以使用下列流程圖來檢查現有專案的設定，以便與 XAML 熱重載搭配使用：

![XAML 熱重載設定](hot-reload-images/hotreloadflowchart.png "XAML 熱重載設定流程圖")

## <a name="resilient-reloading"></a>復原重載

如果您進行的變更是 XAML 熱重載無法重載，則會使用 IntelliSense 顯示錯誤訊息。 這些變更（稱為「強制編輯」）包含不正確的 XAML，或將控制項連接到不存在的事件處理常式。 即使使用 [編輯]，您仍可繼續重載而不重新開機應用程式-在 XAML 檔案中的其他位置進行其他變更，然後點擊 [儲存]。 不會重載 [強制編輯]，但會繼續套用您的其他變更。

## <a name="reload-on-multiple-platforms-at-once"></a>一次在多個平臺上重載

XAML 熱重載支援在 Visual Studio 和 Visual Studio for Mac 中同時進行調試。 您可以同時部署 Android 和 iOS 目標，以查看您的變更一次反映在兩個平臺上。 若要在多個平臺上進行調試，請參閱：
* **Windows** how [To：設定多個啟始專案](https://docs.microsoft.com/visualstudio/ide/how-to-set-multiple-startup-projects?view=vs-2019)
* **Mac** [設定多個啟始專案](https://docs.microsoft.com/visualstudio/mac/set-startup-projects?view=vsmac-2019)

## <a name="known-limitations"></a>已知限制

* Xamarin.Forms尚*不*支援其他目標（例如 UWP 和 macOS）。 您可以在[這裡](https://developercommunity.visualstudio.com/idea/661682/xaml-hot-reload-for-xamarinforms-on-uwp.html)追蹤 UWP 支援的進度。
* 您無法在 XAML 熱重載會話期間加入、移除或重新命名檔案或 NuGet 套件。 如果您新增或移除檔案或 NuGet 套件，請重建並重新部署您的應用程式，以繼續使用 XAML 熱重載。
* 將您的連結器設定為 [**不要連結**] 或 [**無連結**] 以獲得最佳體驗。 「**僅限連結 SDK** 」設定會在大部分的時間內運作，但在某些情況下可能會失敗。 您可以在 Android 和 iOS 組建選項中找到連結器設定。
* 在實體 iPhone 上進行偵錯工具時，解譯器必須使用 XAML 熱重載。 若要這麼做，請開啟專案設定，選取 [iOS 組建] 索引標籤，並確定已啟用 **[Mono 解釋**器] 設定。 您可能需要將屬性頁頂端的 [**平臺**] 選項變更為 [ **iPhone**]。
* 使用其值將控制項指派給另一個欄位或屬性所建立的任何參考 `x:Name` 都不會重載。
* 在 AppShell 中更新 Shell 應用程式的視覺階層，可能會導致維護應用程式狀態的問題。 如果您遇到問題，請重建應用程式以繼續重載。
* XAML 熱重載無法重載 c # 程式碼，包括事件處理常式、自訂控制項、分頁程式碼後置和其他類別。

## <a name="more-resources"></a>其他資源

* [XAML 熱重載的秘訣和訣竅](https://devblogs.microsoft.com/xamarin/tips-tricks-xaml-hot-reload/)
* [深入的 XAML 熱重載 Xamarin.Forms ： Xamarin Show](https://www.youtube.com/watch?v=crhjjPjzknk)

## <a name="troubleshooting"></a>疑難排解

* 如果 XAML 熱重載無法初始化：
  * 更新您的 Xamarin.Forms 版本。
  * 請確定您是在最新版本的 IDE 上。
  * 將您的 Android 或 iOS 連結器設定設定為 [**不要連結**] 專案的組建設定。
* 如果儲存 XAML 檔案時沒有發生任何事，請確定已在 IDE 中啟用 XAML 熱重載。
* 如果您要在實體 iPhone 上進行偵錯工具，而您的應用程式變得沒有回應，請檢查解譯器是否已啟用。 若要開啟它，請核取 [在您的 iOS 組建設定中**啟用 Mono 解釋**器（Visual Studio 16.4/8.4 和 up）] 或 [新增 **--解釋**器] 至 [**其他 mtouch 引數**] 欄位（Visual Studio 16.3/8.3 和之前的版本）。

若要報告錯誤，請**使用 [** 說明] [  >  **傳送意見**反應]  >  **報告 Windows 上的問題**，並**協助**  >  在 Mac 上回報**問題**。

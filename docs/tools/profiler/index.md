---
title: Xamarin Profiler
description: 本指南會探索 Xamarin Profiler 的主要功能。 它會探討分析工具、程式碼剖析和應該使用的時機，以及用於剖析 Xamarin 應用程式的標準工作流程。
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: conceptdev
ms.author: crdun
ms.date: 06/03/2018
ms.openlocfilehash: b567985fcfd9e133587c5c285e17d21465fda659
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250083"
---
# <a name="xamarin-profiler"></a>Xamarin Profiler

_本指南會探索 Xamarin Profiler 的主要功能。它會探討分析工具、程式碼剖析和應該使用的時機，以及用於剖析 Xamarin 應用程式的標準工作流程。_

應用程式的成功與否取決於使用者體驗。 身為開發人員，您可能已在應用程式中實做一些非常棒的功能，但如果應用程式的損毀或已滿損毀，則使用者可能會被移除。

在過去，Mono 已提供強大的命令列分析工具，用來收集 Mono 執行時間中執行之程式的相關資訊，稱為[mono 記錄 profiler](https://www.mono-project.com/docs/debug+profile/profile/profiler/)。 Xamarin Profiler Mono 記錄分析工具的圖形化介面，並支援在 Mac 上進行 Android、iOS、tvOS 和 Mac 應用程式的分析，以及 Windows 上的 Android、iOS 和 tvOS 應用程式。

Xamarin Profiler 有一些可用於分析的檢測，包括配置、迴圈和時間 Profiler。 本指南將探討這些檢測措施，以及它們如何分析您的應用程式，並說明每個畫面上所呈現之資料的意義。

本指南會檢查常見的分析案例，並以工具的形式引進 profiler，以協助分析和優化 iOS 和 Android 應用程式。

## <a name="download-and-install"></a>下載並安裝

> [!NOTE]
> 您必須是[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/)訂閱者，才能在 Windows 上的 Visual Studio Enterprise 或 Mac 上的 Visual Studio for Mac 解除鎖定這項功能。

Xamarin Profiler 是獨立應用程式，並已與 Visual Studio for Mac 和 Visual Studio 整合，以啟用從 IDE 內進行分析。

下載適用于您平臺的安裝套件：

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

下載完成後，請啟動安裝程式，將 Xamarin Profiler 新增至您的系統。

## <a name="profilers-and-profiling"></a>分析工具和程式碼剖析

分析是應用程式開發中很重要且經常被忽略的步驟。 分析是一種**動態程式分析**的形式，它會在程式執行中且正在使用中時分析它。 Profiler 是一種資料採礦工具，它會收集時間複雜性、特定方法的使用方式，以及所配置記憶體的相關資訊。 分析工具可讓您深入剖析這些計量，並在程式碼中找出問題區域。

設計和開發應用程式時，請務必不要提前優化;也就是說，花時間將程式碼開發到很少存取的區域。 這就是分析的威力。 分析工具可讓您深入瞭解程式碼基底最常使用的部分，並協助找出您應該花時間進行改善的地方。 開發人員應該負責瞭解大部分時間花費在應用程式中的位置，以及您的應用程式使用記憶體的方式。

分析在所有類型的開發方面都很有用，但在行動裝置開發方面特別重要。 未優化的程式碼在行動平臺上比在桌上型電腦上更明顯，而且您的應用程式成功與否取決於可有效率地執行的美觀且優化的程式碼。

## <a name="xamarin-profiler"></a>Xamarin Profiler

Xamarin Profiler 為開發人員提供一種方法，讓您能夠從 Visual Studio for Mac 或 Visual Studio 內部分析應用程式。 分析工具會收集並顯示應用程式的相關資訊，供開發人員用來分析應用程式的行為。 有幾種不同的方式可以使用 Xamarin Profiler （也就是記憶體分析和統計取樣）來分析應用程式。 這些會分別透過配置和時間分析工具檢測來執行。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

目前，Xamarin Profiler 可用來測試 Mac 上的 Xamarin. iOS、Xamarin 和 Xamarin. Mac 應用程式（經由 Visual Studio for Mac）。 分析工具與 IDE 是不同的進程，因此除了從 Visual Studio for Mac 啟動之外，它也可以做為獨立的應用程式，以檢查已從`.mlpd` [mono 記錄 profiler](https://www.mono-project.com/docs/debug+profile/profile/profiler/)產生的 .exe 和檔案。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

目前，Xamarin Profiler 可以用來測試 Windows 上的 Xamarin Android 應用程式（透過 Visual Studio 和 Visual Studio for Mac）。 分析工具與 IDE 是不同的進程，因此除了從 Visual Studio 啟動之外，它也可以做為獨立的應用程式，以檢查已從`.mlpd` [mono 記錄 profiler](https://www.mono-project.com/docs/debug+profile/profile/profiler/)產生的 .exe 和檔案。

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Profiler 支援

Xamarin Profiler 的支援可在下列平臺上取得：

- Visual Studio for Mac （macOS，含 Enterprise 授權）
  - Android
    - 裝置和模擬器
  - iOS
    - 裝置和模擬器
  - tvOS （不支援時間檢測）
    - 裝置和模擬器
  - Mac

- Visual Studio （僅限**企業**版）
  - Android
    - 裝置和模擬器
  - iOS [實驗性]
    - 裝置和模擬器
  - tvOS
    - 裝置和模擬器

請注意，您**只能**分析**調試**程式設定。

## <a name="profiler-basics"></a>Profiler 基本概念

本節介紹 Xamarin Profiler 的各個部分，並導覽其功能。

### <a name="allow-profiling-in-your-app"></a>允許在您的應用程式中進行分析

您必須允許在應用程式的專案選項中進行分析，才能成功分析應用程式。

- IOS

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  **組建 > iOS Debug > 啟用分析**

  ![Visual Studio for Mac 中的 [iOS 選項] 對話方塊](images/ios-options-mac.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **> IOS 組建的屬性 > 啟用分析**

  ![Visual Studio 中的 [iOS 選項] 對話方塊](images/ios-project-options-vs.png)

-----

- 面向

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  **組建 > Android Debug > 啟用開發人員檢測**

  ![Visual Studio for Mac 中的 Android 選項對話方塊](images/android-project-options.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **組建 > Android Debug > 啟用開發人員檢測**

  ![Visual Studio for Mac 中的 Android 選項對話方塊](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>啟動 Profiler

當您在分析 iOS 或 Android 應用程式時，或作為獨立應用程式時，可以從 IDE 啟動 Xamarin Profiler。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>從 Visual Studio for Mac 啟動

1. 首先，請確定您已在 Visual Studio for Mac 中載入應用程式，並選取 [（預設）] [Debug] 設定。
2. 流覽以在 Visual Studio for Mac 中**執行 > 開始分析**，或分析 Visual Studio 中的 **> Xamarin Profiler** ，以開啟 Profiler，如下圖所示：

  ![從 Visual Studio for Mac 啟動 Profiler](images/start-profiling-xs.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>從 Visual Studio 啟動

1. 首先，請確定您已在 Visual Studio 中載入應用程式，並選取（預設） [Debug] 設定，如上面所指定。
2. 流覽以分析 Visual Studio 中的 **> Xamarin Profiler** ，以開啟 Profiler，如下圖所示：

![從 Visual Studio 啟動 Profiler](images/start-profiling-vs.png)

-----

如果功能表項目沒有出現，請參閱[疑難排解指南](~/tools/profiler/troubleshooting.md)。

這會啟動 Profiler，並自動開始分析應用程式。

分析工具可以用來測量記憶體和效能。 它會透過配置和時間分析工具檢測來達到此目標，我們將在下一節中詳細探討。

#### <a name="saving-and-loading-profiler-sessions"></a>儲存和載入 Profiler 會話

若要隨時儲存分析會話，請選擇 [檔案] > [程式碼剖析工具] 功能表列中的 [**另存**新檔 ...]。 這會以_mlpd_格式儲存檔案，這是一種特殊的高度壓縮格式，用於分析資料。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

安裝之後，您可以在 [應用程式] 資料夾中找到 Xamarin Profiler，如下列螢幕擷取畫面所示：

![從 Mac 開啟獨立 Profiler](images/applications.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

安裝之後，您可以在應用程式目錄中找到 Xamarin Profiler 應用程式：

![從 Windows 開啟獨立 Profiler](images/applications-vs.png)

-----

您可以藉由開啟獨立應用程式，選取 **[選擇目標**] 和 [載入檔案]，將*mlpd*檔載入分析工具。

如需詳細資訊，請參閱[產生 mlpd](~/tools/profiler/troubleshooting.md#gen_mlpd)檔案。

## <a name="profiler-features"></a>Profiler 功能

Xamarin Profiler 是由五個區段組成，如下所示：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![Visual Studio for Mac 中的 Profiler 區段](images/profiler-mac-sml.png)](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Visual Studio 中的 Profiler 區段](images/profiler-vs.png)](images/profiler-vs.png#lightbox)

-----

- **工具列**–位於 profiler 的頂端，這提供了啟動/停止分析、選取目標進程、查看應用程式執行時間，以及選取組成 profiler 應用程式之分割視圖的選項。
- **檢測清單**–這會列出針對分析會話載入的所有儀器。
- **繪製圖表**–這些圖表會水準關聯至檢測清單中的相關儀器。 滑杆（在 [時間分析工具] 下方顯示）可以用來變更尺規。
- **檢測詳細資料區域**-包含目前檢測的選取視圖所顯示的資料。 在下一節中，我們將更詳細地探討這些 views。
- **Inspector View** –這包含分段控制項可選取的區段。 這些區段取決於選取的檢測，並包含：設定、統計資料、堆疊追蹤資訊，以及根目錄的路徑。

### <a name="allocations"></a>配額

配置檢測會在建立和垃圾收集時，提供應用程式中物件的詳細資訊。

分析工具的頂端是 [配置] 圖表，它會顯示在程式碼剖析期間，以固定間隔分配的記憶體數量。 目前，配置圖表是配置的總數，而不是該時間點的堆積大小。 就這一點而言，它永遠不會停機，只會增加。 這包括在堆疊上配置的物件。 視所使用的執行階段版本而定，圖表看起來會不同–即使是針對相同的應用程式也一樣。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![配置檢測](images/allocations1.png)](images/allocations1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![配置檢測](images/allocations1-vs.png)](images/allocations1-vs.png#lightbox)

-----

配置檢測中有不同的資料檢視，可讓開發人員分析其應用程式使用和釋放記憶體的方式。 這些視圖如下所述：

- 配置 **–這**會顯示所有配置的清單，並依類別名稱將它們分組。 這可讓您大致瞭解所使用的類別和方法、使用的頻率，以及所使用之類別的集體大小。 按兩下類別將會顯示配置的記憶體： 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![[配置] 索引標籤](images/allocations3.png)](images/allocations3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![[配置] 索引標籤](images/allocations2-vs.png)](images/allocations2-vs.png#lightbox)

-----

配置的 [偵測器] 視圖會提供篩選和分組物件的選項、提供所配置記憶體的統計資料和最上層配置，以及堆疊追蹤的視圖和根的路徑。

- **呼叫樹狀結構**–這會顯示應用程式中所有線程的整個呼叫樹狀結構，並包含每個節點上所配置之記憶體的相關資訊。 在清單中選取專案時，所有的同輩節點都會顯示為灰色。 您可以展開樹狀結構，或按兩下元素以向下切入。顯示此資料檢視時，可以使用 [顯示設定] 偵測器視圖來變更其呈現方式。 目前有兩個選項：
    1. **反向呼叫樹狀結構**–這會將堆疊追蹤從上到下考慮。 這是一個方便的視圖選項，因為它會指出 CPU 已花費時間的最深方法。
    2. **以執行緒分隔**–此選項會依執行緒組織呼叫樹狀結構。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![[呼叫樹] 索引標籤](images/allocations2.png)](images/allocations2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![[呼叫樹] 索引標籤](images/allocations3-vs.png)](images/allocations3-vs.png#lightbox)

-----

- **快照**–此窗格會顯示記憶體快照集的相關資訊。 若要在分析即時應用程式時產生這些內容，請按一下工具列中的 [_相機_] 按鈕，您會在每個想要查看保留和釋放記憶體的位置。 然後您可以按一下每個快照集，以探索幕後發生的情況。 請注意，只有在即時分析應用程式時，才可以執行快照集。 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![快照集索引標籤](images/allocations4.png)](images/allocations4.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![快照集索引標籤](images/allocations4-vs.png)](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>時間 Profiler

時間分析工具檢測會確切測量應用程式的每個方法所花費的時間。 應用程式會定期暫停，並且在每個使用中線程上執行堆疊追蹤。 [檢測] 詳細資料區域中的每個資料列都會顯示已遵循的執行路徑。

如下列螢幕擷取畫面所示，繪製圖表會顯示應用程式在執行時所收到的樣本數目：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![時間分析工具檢測](images/time1.png)](images/time1.png#lightbox) 

[![時間分析工具檢測-範例清單](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![時間分析工具檢測](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![時間分析工具檢測-範例清單](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **呼叫樹狀結構**-顯示每個方法所花費的時間量：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![時間分析工具檢測-呼叫樹狀結構](images/time2.png)](images/time2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![時間分析工具檢測-呼叫樹狀結構](images/time2-vs.png)](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>循環

透過使用C#和F# managed 程式碼，可能會很常見，而且很容易就能建立對物件的參考，而永遠不會加以處置。 此檢測可讓您找出這些物件，並顯示應用程式中所參考的迴圈。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![迴圈檢測](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![迴圈檢測](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>分析應用程式

目前，只有預設的 Debug 設定可以進行分析。

如果您使用任何其他設定來分析應用程式，您會看到下列訊息對話方塊：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![分析錯誤對話方塊](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![分析錯誤對話方塊](images/image1vs.png)](images/image1vs.png#lightbox) 

-----

選取 [**更新**] 以繼續。

### <a name="sgen-garbage-collector-and-profiling"></a>SGen 垃圾收集行程和分析

[SGen](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/)垃圾收集行程會用於所有 Xamarin 平臺。

SGen 是一種產生的 GC，它會將應用程式的物件配置成三個堆積：托兒所、主要堆積和大型物件空間。 這可讓您 speedier 執行垃圾收集。 SGen 目前是 Xamarin 和 Xamarin 整合應用程式的預設 GC。

使用 Classic API 的 Xamarin iOS 應用程式會使用 Boehm GC –保守的非代垃圾收集行程。 正如保守，流量分析工具時，不太可能釋出可用的記憶體，這可能會導致不正確的結果。 因此，配置檢測無法與 Boehm 垃圾收集行程搭配使用。

當您的應用程式使用 Boehm GC 時，系統會提示您提供訊息對話方塊，而 Xamarin 不建議將使用 Boehm 的現有 iOS 應用程式切換到 SGen，而不需要仔細研究和徹底測試。 Xamarin 也不建議切換到 SGen 進行分析，然後再切換回來，因為這些結果不會提供正確的記憶體使用效能評定。

如需記憶體管理的詳細資訊，請參閱[記憶體和效能最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)指南。

## <a name="summary"></a>總結

在本指南中，我們探討了何種分析，以及它對開發人員的好處。 我們接著引進了 Xamarin Profiler，提供一些歷程記錄和資訊給它的運作方式。 最後，我們會球隊遠征 Xamarin Profiler 的功能，並探索配置和時間分析工具檢測。

## <a name="related-links"></a>相關連結

- [記憶體和效能最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [版本資訊](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/profiler/preview/index.md)

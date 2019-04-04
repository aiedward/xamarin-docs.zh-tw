---
title: Xamarin Profiler
description: 本指南將探索 Xamarin Profiler 的主要功能。 它看起來在程式碼剖析工具、 程式碼剖析及何時使用它們，而是在標準工作流程的 Xamarin 應用程式程式碼剖析。
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: lobrien
ms.author: laobri
ms.date: 06/03/2018
ms.openlocfilehash: 15739ff191953e4730d44c6ad9f63dccb9a0017e
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668409"
---
# <a name="xamarin-profiler"></a>Xamarin Profiler

_本指南將探索 Xamarin Profiler 的主要功能。它看起來在程式碼剖析工具、 程式碼剖析及何時使用它們，而是在標準工作流程的 Xamarin 應用程式程式碼剖析。_

應用程式的成功取決於使用者體驗。 身為開發人員您可能會有一些真的很棒的功能中實作您的應用程式，但如果應用程式效能不彰或完整的損毀，使用者將可能刪除它。

在過去，Mono 具有功能強大的命令列分析工具收集有關 Mono 執行階段中執行的程式，稱為[Mono 的記錄程式碼剖析工具](https://www.mono-project.com/docs/debug+profile/profile/profiler/)。 Xamarin Profiler Mono 的記錄程式碼剖析工具，並支援 Android、 iOS、 tvOS 和在 Mac 及 Android、 iOS、 Mac 應用程式和 Windows 上的 tvOS 應用程式程式碼剖析的圖形化介面中。

Xamarin Profiler 有許多工具可供分析 — 配置、 週期和時間 Profiler。 本指南會探討這些工具的測量，以及它們如何分析您的應用程式，並釐清每個畫面上顯示資料的意義。

本指南會檢查常見分析案例，並介紹如何使用分析工具來協助分析和最佳化 iOS 和 Android 的應用程式的工具。

## <a name="download-and-install"></a>下載並安裝

> [!NOTE]
> 您必須[Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/)解除這項功能在 Windows 上的其中一個 Visual Studio Enterprise 或 Visual Studio for Mac 在 mac 上的訂閱者

Xamarin Profiler 是獨立應用程式，並且已整合於 Visual Studio for Mac 和 Visual Studio 可讓從 IDE 中進行分析。

下載您的平台的安裝套件：

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

下載完成後，啟動安裝程式將 Xamarin Profiler 新增至您的系統。

## <a name="profilers-and-profiling"></a>分析工具和程式碼剖析

分析是在應用程式開發的重要且經常被忽略的步驟。 程式碼剖析是一種**動態程式分析**-它會分析程式，而它在執行中，使用中。 分析工具是一種資料採礦工具所收集的時間複雜性、 特定方法的使用方式和所配置的記憶體相關資訊。 分析工具可讓您深入鑽研和分析這些計量，以找出程式碼中的問題區域。

當設計和開發的應用程式，請務必提前; 最佳化也就花時間來開發您的程式碼，將不常存取的區域中。 這是強大的程式碼剖析。 分析工具提供深入了解最常使用的程式碼基底，組件，並協助找出應該花費時間進行改善的位置的區域。 開發人員應該謹慎地了解其中大部分的時間都花在您的應用程式，以及如何使用您的應用程式的記憶體。

分析很有幫助各種開發，但它是特別是非常重要的行動應用程式開發。 未最佳化的程式碼更明顯比行動平台上桌面的電腦上，而且您的應用程式的成功與否取決於執行有效率的美觀且經過最佳化的程式碼。

## <a name="xamarin-profiler"></a>Xamarin Profiler

Xamarin Profiler 分析應用程式，從一種方式提供開發人員在 Visual Studio for Mac 或 Visual Studio。 分析工具會收集並顯示應用程式，然後可由開發人員分析的應用程式行為的相關資訊。 有許多種不同的方式，來分析應用程式與 Xamarin Profiler 中，也就是記憶體分析和統計資料取樣。 這些透過配置和時間的 Profiler 執行分別檢測。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

目前，Xamarin Profiler 可以用來測試 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac Mac (透過 Visual Studio for Mac) 上的應用程式。 分析工具是不同的處理序，從 IDE，並因此，除了從 Visual Studio for Mac 所啟動，它可用來當做獨立應用程式來檢查.exe 和`.mlpd`檔案從產生[mono 的記錄程式碼剖析工具](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

目前，Xamarin Profiler 可以用來測試 （透過 Visual Studio 和 Visual Studio for Mac） 的 Windows 上的 Xamarin.Android 應用程式中。 分析工具是不同的處理序，從 IDE，並因此，除了從 Visual Studio 啟動，它可用來當做獨立應用程式來檢查.exe 和`.mlpd`從產生的檔案[mono 的記錄程式碼剖析工具](https://www.mono-project.com/docs/debug+profile/profile/profiler/).

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>Profiler 支援

支援 Xamarin Profiler 是適用於下列平台：

 - Visual Studio for Mac (企業授權 macOS)
    - Android
        - 裝置和模擬器
    - iOS
        - 裝置和模擬器
    - tvOS （不支援時間檢測）
        - 裝置和模擬器
    - Mac

 - Visual Studio (僅**企業**版本)
    - Android
        - 裝置和模擬器
    - iOS [實驗性]
        - 裝置和模擬器
    - tvOS
        - 裝置和模擬器

請注意，您可以**僅**設定檔**偵錯**組態。

## <a name="profiler-basics"></a>Profiler 基本概念

本節介紹 Xamarin Profiler 的部分，並了解其功能。

### <a name="allow-profiling-in-your-app"></a>允許在您的應用程式中進行分析

您可以成功剖析您的應用程式之前，您必須允許應用程式的專案選項中的程式碼剖析。

 - iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  **建置 > iOS 偵錯 > 啟用分析**

  ![](images/ios-options-mac.png "iOS 的 Visual Studio for Mac 中的 [選項] 對話方塊")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **屬性 > iOS 組建 > 啟用分析**

  ![](images/ios-project-options-vs.png "iOS 在 Visual Studio 中的 [選項] 對話方塊")

-----

 - Android:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  **建置 > Android 偵錯 > 啟用開發人員檢測設備**

  ![在 Visual Studio for Mac 的 android 選項 對話方塊](images/android-project-options.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  **建置 > Android 偵錯 > 啟用開發人員檢測設備**

  ![在 Visual Studio for Mac 的 android 選項 對話方塊](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>啟動 Profiler

從您的 IDE，當您要分析您的 iOS 或 Android 應用程式，或當作獨立的應用程式，就可以啟動 Xamarin Profiler。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

#### <a name="launching-from-visual-studio-for-mac"></a>啟動從 Visual Studio for Mac

1. 首先，請確定您已載入在 Visual Studio for Mac，您的應用程式，並選取 （預設值） 偵錯組態。
2. 瀏覽至**執行 > 啟動程式碼剖析**在 Visual Studio for Mac，或**分析 > Xamarin Profiler**在 Visual Studio 中，開啟 Profiler，如下圖所示：

  ![](images/start-profiling-xs.png "啟動 Profiler，從 Visual Studio for Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

#### <a name="launching-from-visual-studio"></a>從 Visual Studio 啟動

1.  首先，請確定您已在 Visual Studio 中載入的應用程式，並選取 （預設值） 偵錯組態，如上述所指定。
2.  瀏覽至**分析 > Xamarin Profiler**在 Visual Studio 中，開啟 Profiler，如下圖所示：

![啟動從 Visual Studio Profiler](images/start-profiling-vs.png)

-----

如果未出現的功能表項目，請參閱[疑難排解指南](~/tools/profiler/troubleshooting.md)。

這會啟動 Profiler，並會自動啟動分析應用程式。

Profiler 可以用來測量記憶體和效能。 它可達到此目的的配置和時間 Profiler 透過 instruments，我們將在下一節中詳細探討。

#### <a name="saving-and-loading-profiler-sessions"></a>儲存及載入 Profiler 工作階段

若要儲存在任何時間的程式碼剖析工作階段，請選擇**檔案 > 另存新檔...** 從 Profiler 功能表列。 這會儲存在檔案_mlpd_格式、 程式碼剖析資料的特殊的高度壓縮格式。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

安裝完成後 Xamarin Profiler 可以找到您的應用程式資料夾中，如以下螢幕擷取畫面所示：

![](images/applications.png "從 Mac 中開啟獨立 Profiler")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

安裝完成後 Xamarin Profiler 應用程式可在您的應用程式目錄中：

![](images/applications-vs.png "從 Windows 開啟獨立 Profiler ")

-----

您可以載入 *.mlpd*檔案 Profiler 開啟獨立應用程式中，選取**選擇目標**和載入檔案。

如需詳細資訊，請參閱 <<c0> [ 產生.mlpd 檔案](~/tools/profiler/troubleshooting.md#gen_mlpd)。

## <a name="profiler-features"></a>Profiler 功能

Xamarin Profiler 是如下所示的五個區段所組成：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](images/profiler-mac-sml.png "在 Visual Studio for Mac 的 Profiler 區段")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/profiler-vs.png "在 Visual Studio Profiler 區段")](images/profiler-vs.png#lightbox)

-----

- **工具列**– 位於頂端的程式碼剖析工具，這提供選項來啟動/停止程式碼剖析，選取目標處理序、 檢視應用程式中的執行時間和選取的分割檢視撰寫程式碼剖析工具應用程式。
- **檢測清單**– 這會列出所有的 instruments 載入程式碼剖析工作階段。
- **繪製圖表**– 這些圖表水平與相關的 instruments 工具清單中。 （時間 Profiler 下方顯示） 的滑桿可用來變更小數位數。
- **檢測詳細資料區域**-包含所選檢視的目前檢測所要顯示的資料。 我們將探討這些在下一節中詳細的檢視。
- **Inspector 檢視**– 這包含可以區隔控制項所選取的區段。 各節會相依於選取的工具，並包含：組態設定、 統計資料，堆疊追蹤的詳細資訊和根目錄的路徑。

### <a name="allocations"></a>配置

配置檢測會提供有關應用程式中的物件的詳細的資訊，因為它們已建立，且記憶體回收。

在程式碼剖析工具的頂端是記憶體的配置圖表，其中會顯示在分析期間定期配置數量。 配置圖表目前配置的記憶體總數和沒有堆積的大小在該點的時間。 在某方面來說，它永遠不會停機，它只會增加。 這包含在堆疊上配置的物件。 根據執行階段版本使用的情況下，圖表外觀不同 – 甚至是相同的應用程式。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](images/allocations1.png "配置檢測")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/allocations1-vs.png "配置檢測")](images/allocations1-vs.png#lightbox)

-----

有配置檢測中的不同資料檢視可讓開發人員能夠分析其應用程式是如何使用及釋放記憶體。 這些檢視如下所示：

 -   **配置**– 這會顯示所有配置的清單，並分組類別名稱。 這提供絕佳概觀，說明類別所使用的方法、 使用頻率和使用的類別的整體大小。 在類別上按兩下，將會顯示配置的記憶體： 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![](images/allocations3.png "配置 索引標籤")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations2-vs.png "配置 索引標籤")](images/allocations2-vs.png#lightbox)

-----

配置的偵測器檢視提供選項來篩選和群組物件、 提供統計資料的記憶體配置，以及熱門的配置，以及檢視堆疊追蹤和路徑根。

 -   **呼叫樹狀圖**– 這會顯示應用程式中的所有執行緒的整個呼叫樹狀結構，並包含每個節點上配置的記憶體的相關資訊。 在清單中選取項目時，所有同層級節點會出現灰色。 您可以展開樹狀目錄，或按兩下要向下切入到其中的項目。顯示這個資料檢視，顯示設定偵測器檢視可用來變更會在呈現的方式。 目前有兩個選項：
    1.  **反轉呼叫樹狀結構**– 這會考慮從上到下堆疊追蹤。 這是一個方便存取的檢視選項指出最深的方法 CPU 具有已花費的時間。
    2.  **個別執行緒**– 此選項會將呼叫樹狀結構組織的執行緒。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![](images/allocations2.png "呼叫樹狀結構索引標籤")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations3-vs.png "呼叫樹狀結構索引標籤")](images/allocations3-vs.png#lightbox)

-----

 -   **快照集**– 此窗格會顯示記憶體快照集的相關資訊。 若要產生這些分析即時的應用程式時，請按一下_相機_每個點，您想要保留和發行哪些記憶體，請參閱 < 請在工具列中的按鈕。 您也可以按一下每個快照集，以探索在幕後的事情。 請注意即時分析應用程式時，才可以採取快照集。 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![](images/allocations4.png "快照集 索引標籤")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/allocations4-vs.png "快照集 索引標籤")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>時間 Profiler

時間 Profiler 工具測量應用程式的每個方法完全花費多少時間。 應用程式已暫停定期，每個作用中執行緒上執行的堆疊追蹤。 在檢測的詳細資料區域的每個資料列會顯示已遵循的執行路徑。

盒狀圖中，如以下螢幕擷取畫面所示會顯示執行應用程式接收的樣本數目：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![時間 Profiler 檢測](images/time1.png)](images/time1.png#lightbox) 

[![時間 Profiler 檢測 – 範例清單](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![時間 Profiler 檢測](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![時間 Profiler 檢測 – 範例清單](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----

- **呼叫樹狀圖**– 顯示花的時間中的每個方法：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

  [![](images/time2.png "時間 Profiler 工具-呼叫樹狀圖")](images/time2.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  [![](images/time2-vs.png "時間 Profiler 工具-呼叫樹狀圖")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>循環

使用C#和F#managed 程式碼，可能會相當常見，而且不幸的是很容易就能建立將永遠不會處置物件的參考。 此工具可讓您找出這些物件，並顯示在您的應用程式中參考的循環。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![循環檢測](images/cycles.m751-sml.png)](images/cycles.m751.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![循環檢測](images/cycles-vs-sml.png)](images/cycles-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>程式碼剖析的應用程式

目前，只有預設偵錯設定可以進行分析。

如果您剖析具有的任何其他設定的應用程式時，您會看到下列對話方塊中，訊息：


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![程式碼剖析的錯誤對話方塊](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](images/image1vs.png "程式碼剖析的錯誤對話方塊")](images/image1vs.png#lightbox) 

-----

選取 **更新**以繼續。

### <a name="sgen-garbage-collector-and-profiling"></a>SGen 記憶體回收行程和程式碼剖析

[SGen](https://www.mono-project.com/docs/advanced/garbage-collector/sgen/)記憶體回收行程會用於所有的 Xamarin 平台。

SGen 會 generational GC，所配置的應用程式分成三個堆積的物件 — 托兒所、 主要堆積和大型物件空間。 這可讓您更快速執行記憶體回收。 SGen 目前是預設 GC 的 Xamarin.Android 和 Xamarin.iOS 統一的應用程式。

使用傳統 API 的 Xamarin.iOS 應用程式使用 Boehm GC – 保守，非新一代的記憶體回收行程。 它為保守估計，就比較不可能以釋出可用的記憶體，可能會導致不正確的結果時使用的程式碼剖析工具。 基於這個理由，配置檢測不搭配 Boehm 記憶體回收行程。

雖然系統會提示訊息對話方塊，如果您的應用程式使用 Boehm GC，Xamarin 不建議使用 SGen 的 Boehm，而不需要仔細研究和徹底測試現有 iOS 應用程式切換。 Xamarin 也不建議切換至 SGen 的程式碼剖析，然後在這些結果將不會提供正確的記憶體使用量的基準測試時，切換 [上一步]。

如需有關記憶體管理的詳細資訊，請參閱[記憶體和效能最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)指南。

## <a name="summary"></a>總結

本指南中，我們討論過哪些分析，和如何會有幫助開發人員。 然後，我們引進了 Xamarin Profiler，提供一些歷程記錄和它的運作方式的資訊。 最後我們遠征 Xamarin Profiler 中，功能，並探索時間 Profiler Instruments 與配置。

## <a name="related-links"></a>相關連結

- [記憶體和效能的最佳作法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [版本資訊](https://developer.xamarin.com/releases/profiler/preview/)

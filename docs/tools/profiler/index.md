---
title: Xamarin Profiler
description: 本指南中，瀏覽 Xamarin 分析工具的主要功能。 它看起來在程式碼剖析工具、 進行分析，以及何時應該使用它們，而在標準的工作流程的 Xamarin 應用程式程式碼剖析。
ms.prod: xamarin
ms.assetid: 3247fcee-6acc-470d-ab87-c1c511d67363
author: topgenorth
ms.author: toopge
ms.date: 10/27/2017
ms.openlocfilehash: 7e28e2513b74faa884fb7bc3d7194f1b02844734
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="xamarin-profiler"></a>Xamarin Profiler

_本指南中，瀏覽 Xamarin 分析工具的主要功能。它看起來在程式碼剖析工具、 進行分析，以及何時應該使用它們，而在標準的工作流程的 Xamarin 應用程式程式碼剖析。_

應用程式的成功取決於使用者體驗。 身為開發人員您可能會有實作了一些真的實用功能在您的應用程式，但如果緩慢或完整的當機之應用程式，使用者將可能刪除它。

在過去，Mono 具有功能強大的命令列分析工具收集資訊單聲道的執行階段中執行的程式稱為[單聲道記錄程式碼剖析工具](http://www.mono-project.com/docs/debug+profile/profile/profiler/)。 Xamarin 分析工具單聲道記錄程式碼剖析工具，並支援 Android、 iOS、 tvOS，並在 Mac 及 Android、 iOS、 Mac 應用程式和 tvOS Windows 上的應用程式程式碼剖析的圖形介面。

Xamarin 分析工具有許多可用的程式碼剖析工具： 配置、 週期和時間的程式碼剖析工具。 本指南會探討這些工具的測量和它們如何分析您的應用程式，並將每個螢幕上顯示資料的意義，釐清。

本指南會檢查程式碼剖析的常見案例，並介紹分析工具以協助分析和最佳化 iOS 和 Android 的應用程式的工具。

## <a name="contents"></a>內容

- [下載並安裝](#Download_and_Install)
- [分析工具和程式碼剖析](#Profilers_and_Profiling)
- [Xamarin Profiler](#Xamarin_Profiler)
- [程式碼剖析工具支援](#Profiler_Support)
- [程式碼剖析工具的基本概念](#Profiler_Basics)
    - [允許在您的應用程式中進行分析](#Allowing_Profiling_in_your_App)
    - [啟動分析工具](#Launching_the_Profiler)
        - [從 Visual Studio 啟動 mac](#Launching_from_Xamarin_Studio)
        - [從 Visual Studio 啟動](#Launching_from_Visual_Studio)
        - [儲存和載入分析工具工作階段](#Saving_and_Loading_Profiler_Sessions)
        - [程式碼剖析工具功能和工具](#Profiler_Features)
    - [配置](#Allocations)
    - [時間程式碼剖析工具](#Time_Profiler)
    - [循環](#Cycles)
- [剖析應用程式](#Profiling_Applications)
- [摘要](#Summary)

## <a name="download-and-install"></a>下載並安裝

> [!NOTE]
> **注意：**必須是[Visual Studio Enterprise](https://www.visualstudio.com/vs/compare/)解除這項功能在 Windows 上的其中一個 Visual Studio Enterprise 或 Visual Studio 在 mac 上的 mac 的訂閱者

Xamarin 分析工具是獨立應用程式，以及與 Visual Studio for Mac 和 Visual Studio 啟用程式碼剖析從 IDE 中整合。

### <a name="download"></a>下載

下載您的平台的安裝套件：

- [**macOS**](https://dl.xamarin.com/profiler/profiler-mac.pkg)
- [**Windows**](https://dl.xamarin.com/profiler/profiler-windows.msi)

在下載後，啟動安裝程式新增至系統的 Xamarin 分析工具。


## <a name="profilers-and-profiling"></a>分析工具和程式碼剖析

程式碼剖析是重要且經常被忽略的步驟，在開發應用程式。 程式碼剖析是一種**動態程式分析**-執行中，使用中時，分析程式。 分析工具是一種資料採礦工具，收集資訊的時間複雜性、 特定的方法的使用方式和所配置的記憶體。 分析工具可讓您深入鑽研和分析這些度量能找出程式碼中的問題區域。

當設計和開發的應用程式，務必提前; 最佳化也就花時間來開發您的程式碼，將不常存取的區域中。 這是程式碼剖析的電源。 分析工具提供深入了解最常使用的程式碼基底，部分，並可協助找出您應該在其中花時間讓改善功能的區域。 開發人員應該謹慎地了解大部分時間花費在應用程式中的地方，以及您的應用程式如何使用記憶體。

分析是在所有類型的程式開發，很有幫助，但在行動應用程式開發中特別重要。 以未最佳化的程式碼中會更明顯比行動平台上桌面的電腦上，您的應用程式的成功取決於美觀且最佳化的程式碼，可以有效地執行。

## <a name="xamarin-profiler"></a>Xamarin Profiler

Xamarin 分析工具會提供開發人員得以將應用程式設定檔從 Visual Studio for Mac 或 Visual Studio 內。 分析工具會收集並顯示應用程式，然後可由開發人員分析的應用程式行為的相關資訊。 有許多種不同的方式，若要從頭到尾分析應用程式搭配 Xamarin 分析工具，也就是記憶體分析和統計資料取樣。 這些配置和時間的程式碼剖析工具透過實行分別檢測。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

目前，Xamarin 分析工具可以用來測試 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac Mac (透過 Visual Studio for Mac) 上的應用程式。 分析工具會在 IDE 中不同的處理序，因此，除了從 Visual Studio 啟動 mac，它可以當做獨立應用程式來檢查.exe 和`.mlpd`從產生的檔案[單聲道記錄程式碼剖析工具](http://www.mono-project.com/docs/debug+profile/profile/profiler/).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

目前，Xamarin 分析工具可以用來測試 Xamarin.Android （透過 Visual Studio 和 Visual Studio for Mac） 的 Windows 上的應用程式。 分析工具會在 IDE 中不同的處理序，因此，除了從 Visual Studio 中啟動，才能使用當做獨立應用程式來檢查.exe 和`.mlpd`從產生的檔案[單聲道記錄程式碼剖析工具](http://www.mono-project.com/docs/debug+profile/profile/profiler/).

-----

<a name="Profiler_Support" />

## <a name="profiler-support"></a>程式碼剖析工具支援

支援 Xamarin 分析工具是適用於下列平台：

 - Visual Studio for Mac (與企業授權 macOS)
    - Android
        - 裝置與模擬器
    - iOS
        - 裝置與模擬器
    - tvOS （不支援時間檢測）
        - 裝置與模擬器
    - Mac

 - Visual Studio (僅**企業**版本)
    - Android
        - 裝置與模擬器
    - iOS [實驗]
        - 裝置與模擬器
    - tvOS
        - 裝置與模擬器

請注意，您可以**只**設定檔**偵錯**組態。

## <a name="profiler-basics"></a>程式碼剖析工具的基本概念

本節介紹 Xamarin 分析工具的組件，並 tours 其功能。

### <a name="allow-profiling-in-your-app"></a>允許在您的應用程式中進行分析

您可以成功地分析您的應用程式之前，您必須在應用程式的專案選項讓程式碼剖析。

 - iOS:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  **建置 > 偵錯 iOS > 啟用分析**

  ![](images/ios-options-mac.png "iOS 適用於 Mac 的 Visual Studio 中的 [選項] 對話方塊")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  **內容 > iOS 組建 > 啟用分析**

  ![](images/ios-project-options-vs.png "iOS Visual Studio 中的 [選項] 對話方塊")

-----

 - Android:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  **建置 > Android 偵錯 > 啟用開發人員檢測**

  ![在 Visual Studio for Mac 的 android 選項 對話方塊](images/android-project-options.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  **建置 > Android 偵錯 > 啟用開發人員檢測**

  ![在 Visual Studio for Mac 的 android 選項 對話方塊](images/android-project-options-vs-sml.png)

-----

### <a name="launching-the-profiler"></a>啟動分析工具

Xamarin 分析工具可以從您的 IDE，當您要分析您的 iOS 或 Android 應用程式，或當作獨立的應用程式啟動。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

#### <a name="launching-from-visual-studio-for-mac"></a>從 Visual Studio 啟動 mac

1. 首先，請確定您有適用於 Mac，載入 Visual Studio 中的應用程式，並選取 （預設值） 偵錯組態。
2. 瀏覽至**執行 > 啟動程式碼剖析**Visual Studio for Mac、 中或**分析 > Xamarin 分析工具**在 Visual Studio 中開啟程式碼剖析工具，如下圖所示：

  ![](images/start-profiling-xs.png "啟動分析工具，從 Visual Studio for Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

#### <a name="launching-from-visual-studio"></a>從 Visual Studio 啟動

1.  首先，請確定您已在 Visual Studio 中載入的應用程式，並選取 （預設值） 偵錯組態，如上所述。
2.  瀏覽至**分析 > Xamarin 分析工具**在 Visual Studio 中開啟程式碼剖析工具，如下圖所示：

![正在啟動從 Visual Studio 程式碼剖析工具](images/start-profiling-vs.png)

-----

如果未出現的功能表項目，請參閱[疑難排解指南](~/tools/profiler/troubleshooting.md)。

這會啟動分析工具，並會自動啟動應用程式程式碼剖析。

分析工具可用來測量記憶體和效能。 它可達到此目的的配置和時間的程式碼剖析工具透過儀器，我們將在下一節中詳細探討。

#### <a name="saving-and-loading-profiler-sessions"></a>儲存和載入分析工具工作階段

若要儲存在任何時間的程式碼剖析工作階段，請選擇**檔案 > 另存新檔...** 從程式碼剖析工具功能表列。 這會將儲存在檔案_mlpd_格式、 程式碼剖析資料的特殊高度壓縮格式。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

安裝完成後 Xamarin 分析工具可以找到您的應用程式資料夾中，如以下螢幕擷取畫面所示：

![](images/applications.png "從 Mac 開啟獨立程式碼剖析工具")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

安裝完成後 Xamarin 分析工具應用程式可以找到您的應用程式目錄中：

![](images/applications-vs.png "開啟 windows 的獨立程式碼剖析工具 ")

-----

您可以載入 *.mlpd*開啟獨立應用程式程式碼剖析工具檔案選取**選擇目標**和載入檔案。

如需詳細資訊，請參閱[產生.mlpd 檔案](~/tools/profiler/troubleshooting.md#gen_mlpd)。


## <a name="profiler-features"></a>程式碼剖析工具功能

Xamarin 分析工具是由五個區段如下所示：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](images/profiler-mac-sml.png "適用於 Mac 的 Visual Studio 中的程式碼剖析工具章節")](images/profiler-mac.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/profiler-vs.png "在 Visual Studio 中的程式碼剖析工具章節")](images/profiler-vs.png#lightbox)

-----

- **工具列**– 位於頂端的程式碼剖析工具，這會提供選項來啟動/停止程式碼剖析，選取目標處理序、 檢視的應用程式時，執行時間和選取的分割檢視撰寫程式碼剖析工具應用程式。
- **檢測清單**– 這會列出載入的程式碼剖析工作階段的所有工具。
- **繪製圖表**– 這些圖表水平與相關工具，檢測清單中。 若要變更小數位數可用滑桿 （時間分析工具下方所示）。
- **檢測詳細資料區域**-包含所選取的目前檢測檢視所顯示的資料。 我們將探討這些檢視下一節中詳細說明。
- **偵測器檢視**– 這包含可以由不同客層之控制項選取的區段。 區段是取決於選取，檢測，包含： 組態設定、 統計資料，堆疊追蹤資訊，以及根路徑。

### <a name="allocations"></a>配置

正在建立並回收配置檢測會提供應用程式中之物件的詳細的資訊。

在程式碼剖析工具的頂端是記憶體的配置圖表中，會顯示在分析期間定期配置數量。 目前配置圖形中該時間點的時間是配置的記憶體總數和堆積的大小。 在某方面來說，它絕不會向下，它永遠只會增加。 這包括在堆疊上配置的物件。 根據使用的執行階段版本，圖表外觀不同 – 即使同一個應用程式。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](images/allocations1.png "配置檢測")](images/allocations1.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/allocations1-vs.png "配置檢測")](images/allocations1-vs.png#lightbox)

-----

有不同的資料檢視中配置檢測，讓開發人員分析是如何使用他們的應用程式並將其釋放記憶體。 以下將詳述這些檢視：

 -   **配置**– 這會顯示所有配置的清單，以及分組類別名稱。 這樣的類別所使用的方法、 使用頻率和集合大小所使用的類別。 按兩下類別會顯示配置的記憶體： 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  [![](images/allocations3.png "配置 索引標籤")](images/allocations3.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations2-vs.png "配置 索引標籤")](images/allocations2-vs.png#lightbox)

-----

配置的偵測器檢視提供選項來篩選和群組物件、 提供統計資料的記憶體配置，以及最上層配置，以及檢視堆疊追蹤和路徑根。

 -   **呼叫樹狀圖**– 這會顯示應用程式中的所有執行緒的整個呼叫樹狀結構，並包含每個節點上配置的記憶體的相關資訊。 在清單中選取項目時，所有同層級節點會以灰色顯示。 您可以展開樹狀目錄，或按兩下要向下鑽研到它的項目。當顯示這個資料檢視，顯示設定偵測器檢視可用來變更它顯示的方式。 目前有兩個選項：
    1.  **反轉呼叫樹狀圖**– 這會考慮從上到下堆疊追蹤。 這是方便檢視選項代表最深方法 CPU 具有已花費的時間。
    2.  **個別執行緒**– 此選項會將呼叫樹狀結構組織執行緒。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  [![](images/allocations2.png "呼叫樹狀結構 索引標籤")](images/allocations2.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations3-vs.png "呼叫樹狀結構 索引標籤")](images/allocations3-vs.png#lightbox)

-----

 -   **快照集**– 此窗格會顯示記憶體快照的相關資訊。 若要產生這些程式碼剖析即時應用程式時，請按一下_相機_在您想要保留並釋出記憶體，請參閱每個點工具列中的按鈕。 接著，您可以按一下瀏覽發生什麼事實際上每個快照集。 請注意即時程式碼剖析應用程式時，才可以採取快照集。 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  [![](images/allocations4.png "快照集 索引標籤")](images/allocations4.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/allocations4-vs.png "快照集 索引標籤")](images/allocations4-vs.png#lightbox)

-----

### <a name="time-profiler"></a>時間程式碼剖析工具

時間程式碼剖析工具檢測測量完全應用程式的每個方法中所花費的時間量。 應用程式已暫停的固定間隔和堆疊追蹤在每個作用中的執行緒上執行。 在檢測詳細資料區域的每個資料列會顯示已遵循的執行路徑。

盒狀圖中，如下列螢幕擷取畫面所示會顯示執行時，應用程式所收到的樣本數目：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![時間程式碼剖析工具檢測](images/time1.png)](images/time1.png#lightbox) 

[![時間程式碼剖析工具檢測 – 範例清單](images/time3.png)](images/time3.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![時間程式碼剖析工具檢測](images/time1-vs.png)](images/time1-vs.png#lightbox) 

[![時間程式碼剖析工具檢測 – 範例清單](images/time3-vs.png)](images/time3-vs.png#lightbox) 

-----


- **呼叫樹狀圖**– 顯示一段時間花費在每個方法：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

  [![](images/time2.png "時間程式碼剖析工具檢測 – 呼叫樹狀圖")](images/time2.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

  [![](images/time2-vs.png "時間程式碼剖析工具檢測 – 呼叫樹狀圖")](images/time2-vs.png#lightbox) 

-----

### <a name="cycles"></a>循環

透過使用 C# 和 F # 的 managed 程式碼，它可以是很常見，和不幸的是很容易就能建立永遠不會處置物件的參考。 此 intrument 可讓您找出這些物件，並顯示您的應用程式中參考的循環。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![循環檢測](images/cycles-vs.png)](images/time1-vs.png#lightbox) 

-----

## <a name="profiling-applications"></a>剖析應用程式

目前，只有預設偵錯設定可以進行剖析。

如果您剖析應用程式，但任何其他設定，將會看見下列訊息對話方塊：


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![程式碼剖析錯誤對話方塊](images/image001.png)](images/image001.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](images/image1vs.png "程式碼剖析錯誤對話方塊")](images/image1vs.png#lightbox) 

-----


選取**更新**才能繼續。

<!---
## Profiling Android Applications


Due to the recent inclusion of the profiling libraries into any new Android project template, you will find that when profiling any legacy applications you are greeted with the message dialog above.

You will need to enable this to make sure that the profiling libraries are included in your Android application, for debug builds. This should not be checked for release builds as it creates overhead.


## Profiling iOS Applications

### Profiling tvOS

## Profiling Mac Applications
-->

### <a name="sgen-garbage-collector-and-profiling"></a>SGen 記憶體回收行程和程式碼剖析

[SGen](http://www.mono-project.com/docs/advanced/garbage-collector/sgen/)記憶體回收行程會用於所有 Xamarin 平台。

SGen 是衍生的 GC，所配置的應用程式三個堆積物件 — Nursery、 主要堆積和大型物件空間。 這可以加快執行記憶體回收。 SGen 正在 Xamarin.Android 的預設 GC 及 Xamarin.iOS 整合的應用程式。

使用傳統的 API Xamarin.iOS 應用程式使用 Boehm GC – 保守、 非衍生的記憶體回收行程。 因為它是保守，較不可能是以釋出可用的記憶體，使用程式碼剖析工具時可能導致不正確的結果。 基於這個理由，配置檢測不能與 Boehm 記憶體回收行程。

當您將會收到提示訊息對話方塊如果您的應用程式使用 Boehm GC 時，Xamarin 不建議切換至 SGen Boehm 使用而不需要仔細研究和要進行徹底測試的現有 iOS 應用程式。 Xamarin 也不會不建議您先切換至 SGen 程式碼剖析，然後在這些結果將不會提供的記憶體使用量精確效能評定基準時，切換 [上一步]。

如需記憶體管理的詳細資訊，請參閱[記憶體和效能最佳做法](~/cross-platform/deploy-test/memory-perf-best-practices.md)指南。

## <a name="summary"></a>總結

本指南中我們探討了何種程式碼剖析已而如何幫助開發人員。 然後，我們引進了 Xamarin 分析工具，提供一些記錄與成它的運作方式的資訊。 最後我們參觀 Xamarin 分析工具的功能，並瀏覽時間程式碼剖析工具儀器與配置。


## <a name="related-links"></a>相關連結

- [記憶體和效能的最佳作法](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [版本資訊](https://developer.xamarin.com/releases/profiler/preview/)

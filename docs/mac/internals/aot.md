---
title: 預先編譯的 Xamarin.Mac
description: 本文件說明預先編譯 xamarin.mac。 它會比較 JIT 編譯的 AOT 編譯、 說明如何啟用 AOT，並探討混合式 AOT。
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: e155a394afd68d9970ee32785f6d0aeda6e2d129
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034200"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>預先編譯的 Xamarin.Mac

## <a name="overview"></a>總覽

預先 (aot) 編譯是改善啟動效能功能強大的最佳化技術。 不過，它也會影響您的建置時間，應用程式大小和程式執行深遠的方式。 若要了解它將加諸的折衷辦法，我們更深入的編譯和執行應用程式。

撰寫的程式碼 managed 語言，例如C#和F#，會編譯成中繼的表示法，呼叫 IL。 此 IL，儲存在您程式庫和程式的組件，是相當精簡且可攜式處理器架構之間。 IL，不過，是只中繼設定的指示，並在 IL 必須轉譯成處理器特定機器碼的某個時間點。

有兩個點，可以在其中完成這項處理：

- **Just-in-time (JIT)** – 啟動和 IL 編譯成機器碼的記憶體中的應用程式執行期間。
- **預先 (aot) 的**– IL 編譯並寫出至原生程式庫和儲存在您的應用程式套件組合的建置期間。

每個選項都有一些優點和權衡取捨：

- **JIT**
  - **啟動時間**– JIT 編譯必須在啟動時完成。 這是大約 100 毫秒，大部分的應用程式，但對於大型應用程式這個時間可能需要更多。
  - **執行**– 為 JIT 程式碼可以針對所使用的特定處理器進行最佳化，可以產生更好的程式碼。 在大部分的應用程式這是更快幾個百分點執行下列最多。
- **AOT**
  - **啟動時間**– 載入預先編譯 dylib 來處理速度明顯比 JIT 組件。
  - **磁碟空間**– 這些 dylib 來處理不過可能需要大量的磁碟空間。 根據哪些組件是 AOTed，它可以按兩下或更多您的應用程式的程式碼部分的大小。
  - **建置階段**– AOT 編譯會明顯變慢的 JIT 和會變慢的組建使用它。 這個速度變慢的範圍可以從秒最多一分鐘或更多，根據大小和編譯的組件的數目。
  - **模糊化**– 以 IL，大幅提升了進行反向工程，比機器的程式碼，則不一定需要它可以協助模糊化敏感的程式碼移除。 這需要 「 混合 」 下方的選項描述。

## <a name="enabling-aot"></a>啟用 AOT

AOT 選項會將在未來的更新中的 [Mac 組建] 窗格。 在那之前，啟用 AOT 需要透過 「 其他 mmp 引數 」 欄位，在 Mac 組建中傳遞的命令列引數。 選項如下所示：


      --aot[=VALUE]          Specify assemblies that should be AOT compiled
                               - none - No AOT (default)
                               - all - Every assembly in MonoBundle
                               - core - Xamarin.Mac, System, mscorlib
                               - sdk - Xamarin.Mac.dll and BCL assemblies
                               - |hybrid after option enables hybrid AOT which
                               allows IL stripping but is slower (only valid
                               for 'all')
                                - Individual files can be included for AOT via +
                               FileName.dll and excluded via -FileName.dll

                               Examples:
                                 --aot:all,-MyAssembly.dll
                                 --aot:core,+MyOtherAssembly.dll,-mscorlib.dll



## <a name="hybrid-aot"></a>混合式 AOT

MacOS 應用程式執行階段執行期間使用機器碼的預設值是從 AOT 編譯所產生的原生程式庫載入。 有，不過，某些區域的程式碼 trampolines，例如其中 JIT 編譯會產生更大幅最佳化的結果。 這需要 managed 組件 IL，才能使用。 在 iOS 上，從使用 JIT 編譯; 的任何限制應用程式這些區段中的程式碼如下 AOT 編譯。

混合式選項會指示編譯器將這兩個編譯這些區段 （例如 iOS) 但也將假設 IL 將無法在執行階段使用。 然後可以移除此 IL 公佈組建。 如先前所述，將會強制執行階段，在某些地方使用較不最佳化的常式。

## <a name="further-considerations"></a>進一步考量

AOT 的負面後果調整大小和處理的組件數目。 完整[目標 framework](~/mac/platform/target-framework.md)範例包含更大基底類別庫 (BCL) 比現代化，並因此 AOT 將會明顯較久，並產生較大的套件組合。 這複合性和連結的完整的目標 framework 不相容，由它去除未使用的程式碼。 請考慮移轉您的應用程式現代化和啟用的連結，為獲得最佳結果。

AOT 的額外的好處之一隨附於與原生的偵錯和程式碼剖析工具鏈的改良互動。 因為大部分的程式碼基底會預先編譯，它會有的函數名稱與容易閱讀內原生損毀報告、 分析和偵錯的符號。 產生的 JIT 函式沒有這些名稱，並通常顯示為未命名十六進位的位移，則很難解決。

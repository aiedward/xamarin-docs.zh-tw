---
title: Xamarin.Mac 預先編譯時間
description: 本文件說明預先 Xamarin.Mac 編譯時間。 它會比較 AOT 編譯 JIT 編譯，說明如何啟用 AOT，並會查看混合式 AOT。
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: ec8474293fbb7372529e0f850e2d16db7ebf17be
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792235"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Xamarin.Mac 預先編譯時間

## <a name="overview"></a>總覽

繼續的時間 (AOT) 編譯的改善啟動效能的功能強大的最佳化技術。 不過，它也會影響您的建置時間，應用程式的大小和程式執行深遠的方式。 若要了解它會加諸的利弊，我們要深入了解位元的編譯和執行應用程式。

撰寫的 managed 語言，例如 C# 和 F # 程式碼會編譯為中繼表示呼叫 IL。 此 IL，儲存在您的程式庫和程式組件，是相當精簡且可攜式處理器架構之間。 IL，不過，是只中繼設定的指示，並在需要 IL 轉譯成處理器特定機器碼的某個時間點。

有兩個點，可以在其中完成這項處理：

- **Just-in-time (JIT)** – 啟動和 IL 編譯成機器碼的記憶體中的應用程式執行期間。
- **前的時間 (AOT)** – 在建置 IL 編譯並寫出至原生程式庫和儲存在您的應用程式套件組合。

每個選項的優點及缺點數目：

- **JIT**
  - **啟動時間**– JIT 編譯必須在啟動時完成。 這是大約 100 毫秒，對於大部分應用程式，但是大型應用程式的這個時間可能需要相當多。
  - **執行**– 為 JIT 程式碼可以針對所使用的特定處理器進行最佳化，可產生更好的程式碼。 在大部分的應用程式就更快幾個百分點最多。
- **AOT**
  - **啟動時間**– 載入先行編譯 dylib 檔案的速度明顯比 JIT 組件。
  - **磁碟空間**– 這些 dylib 檔案但是可能需要大量的磁碟空間。 它可以根據哪一個組件所 AOTed，按兩下或更多您的應用程式的程式碼部分的大小。
  - **建置時間**– AOT 編譯是明顯變慢的 JIT 並且將會使用它的組建。 這個速度變慢的範圍從秒最多一分鐘或更多，根據大小和編譯的組件的數目。
  - **模糊化**– 作為稍微簡單一點進行反向工程，比電腦的程式碼，就不一定需要 IL 可以協助模糊化敏感程式碼已移除。 這需要 「 混合 」 以下所述的選項。

## <a name="enabling-aot"></a>啟用 AOT

AOT 選項將會新增到 Mac 建置窗格的後續更新中。 之前，啟用 AOT 需要透過在 Mac Build 中的 「 其他 mmp 引數 」 欄位傳遞的命令列引數。 選項如下所示：


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

MacOS 應用程式執行階段執行期間要使用機器碼的預設值是從 AOT 編譯所產生的原生程式庫載入。 有，不過，某些區域的程式碼，例如 trampolines，其中 JIT 編譯可能會產生更大幅最佳化的結果。 這需要 managed 組件 IL，才能使用。 在 iOS 上，應用程式受到限制，無法使用任何的 JIT 編譯。這些區段中的程式碼如下 AOT 編譯。

混合式選項會指示編譯器這兩個編譯這些區段 （例如 iOS) 但也將假設 IL 將無法在執行階段使用。 然後會刪除此 IL 公佈的組建。 如先前所述，將會強制執行階段，在某些地方使用較少的最佳化的常式。

## <a name="further-considerations"></a>進一步考量

AOT 縮放比例和大小和處理的組件數目負面結果。 完整[目標 framework](~/mac/platform/target-framework.md)範例包含更大基底類別程式庫 (BCL) 比現代化，並因此 AOT 將需要更長的時間，並產生較大的組合。 這複合性和連結的完整的目標架構不相容，由其用法刪除未使用的程式碼。 請考慮移現代再啟用連結，取得最佳結果，您的應用程式。

一個額外的優點，AOT 隨附於與原生偵錯和程式碼剖析 toolchains 改善互動。 大部分的程式碼基底會事先編譯，因此會有函式名稱和您更輕鬆地讀取內原生的當機報告，進行分析，以及偵錯符號。 產生 JIT 函式沒有這些名稱，且通常顯示為未命名十六進位的位移，則很難解決。

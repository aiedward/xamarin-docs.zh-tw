---
title: Xamarin 的時間先行編譯
description: 本檔說明在 Xamarin. Mac 中的時間編譯。 它會比較 AOT 編譯與 JIT 編譯、說明如何啟用 AOT，並探討混合式 AOT。
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: 6797428596fddb0361fb307240bf8237a1e8554d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769824"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Xamarin 的時間先行編譯

## <a name="overview"></a>總覽

早于時間（AOT）編譯是一種強大的優化技術，可改善啟動效能。 不過，它也會影響您的組建時間、應用程式大小，以及程式執行的方式。 為了瞭解它所強加的取捨，我們將深入探討應用程式的編譯和執行。

以 managed 語言撰寫的程式碼（ C#例如F#和）會編譯成稱為 IL 的中繼標記法。 這個 IL （儲存在您的程式庫和程式元件中）在處理器架構之間相當精簡且可移植。 不過，IL 只是一組中繼的指示，而在某個時間點，IL 必須轉譯為處理器特定的機器碼。

有兩個點可以完成這項處理：

- **即時（JIT）** –在啟動和執行您的應用程式期間，IL 會在記憶體中編譯為機器碼。
- **早于時間（AOT）** –在組建期間，IL 會進行編譯並寫出至原生程式庫，並儲存在您的應用程式套件組合中。

每個選項都有一些優點和取捨：

- **JIT**
  - **啟動時間**– JIT 編譯必須在啟動時完成。 對於大部分的應用程式而言，這是100毫秒的順序，但對於大型應用程式，這次可能會大幅增加。
  - **執行**–因為 JIT 程式碼可以針對所使用的特定處理器進行優化，所以可以產生稍微好一點的程式碼。 在大部分的應用程式中，這是最快速的幾個百分比點。
- **AOT**
  - **啟動時間**–載入預先編譯的 DYLIB 來處理比 JIT 元件快很多。
  - **磁碟空間**–這些 dylib 來處理可能會耗用大量的磁碟空間。 視 AOTed 的元件而定，它可以比對應用程式的程式碼部分的大小。
  - **組建時間**–如果使用此 JIT 編譯，則會將它變得很慢，而且會使組建的速度緩慢。 這種緩慢的範圍可以從秒到一分鐘以上，視編譯的元件大小和數目而定。
  - **模糊**–做為 IL （比機器碼更容易進行反向工程）並不是必要的，因此您不一定要將它移除，以協助模糊的敏感程式碼。 這需要下面所述的「混合式」選項。

## <a name="enabling-aot"></a>啟用 AOT

在未來的更新中，系統會將 AOT 選項新增至 [Mac 組建] 窗格。 在那之前，啟用 AOT 必須透過 Mac Build 中的 [其他 mmp 引數] 欄位來傳遞命令列引數。 選項如下所示：

```
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
```

## <a name="hybrid-aot"></a>混合式 AOT

在執行 macOS 應用程式期間，執行時間預設為使用從 AOT 編譯所產生的原生程式庫載入的機器碼。 不過，有些程式碼區域（例如 trampolines），JIT 編譯可以產生明顯更優化的結果。 這需要有 managed 元件 IL 才能使用。 在 iOS 上，應用程式會受限於任何使用 JIT 編譯的情況;這些程式碼區段也是已編譯的 AOT。

混合式選項會指示編譯器同時編譯這些區段（例如 iOS），同時假設 IL 將無法在執行時間使用。 然後，可以在建立後移除此 IL。 如先前所述，執行時間將會強制在某些地方使用較不優化的常式。

## <a name="further-considerations"></a>進一步考量

使用已處理的元件大小和數目來調整的 AOT 的負面影響。 例如，完整的[目標架構](~/mac/platform/target-framework.md)包含比現代化更大的基類庫（BCL），因此，AOT 會花費更長的時間，而且會產生較大的組合。 這會因為完整的目標 framework 與連結不相容而變得更加複雜，這會去除未使用的程式碼。 請考慮將您的應用程式移至現代化，並啟用連結以獲得最佳結果。

AOT 的另一個優點是已改善與原生偵錯工具和程式碼剖析工具鏈的互動。 因為大部分的程式碼基底會在一段時間後編譯，所以它會有可在原生損毀報告、分析和偵錯工具中更容易閱讀的函數名稱和符號。 JIT 產生的函式沒有這些名稱，而且通常會顯示為難以解析的未命名十六進位位移。

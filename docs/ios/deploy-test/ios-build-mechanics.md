---
title: iOS 組建機制
description: 本指南探索如何測定應用程式的時間，以及如何使用可用來針對所有組建組態取得更快速組建的方法。
ms.prod: xamarin
ms.assetid: 06FD3940-D666-4C9E-BC3E-BBE481EF8012
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 92bf7934b1ad4f6d959fc458f536cf3b3426df51
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026361"
---
# <a name="ios-build-mechanics"></a>iOS 組建機制

_本指南探索如何測定應用程式的時間，以及如何使用可用來針對所有組建組態取得更快速組建的方法。_

想要開發出色的應用程式不是撰寫可運作的程式碼就行。 嚴謹撰寫的應用程式應該要有最佳化措施，以便在較小且執行速度更快的應用程式中更快速地完成組建。 這些最佳化措施不只能讓使用者獲得更好的體驗，也能讓您或任何處理專案的開發人員獲得更好的體驗。 請務必要在處理應用程式時，經常記錄所有作業的時間。 

請記住，預設選項都是安全且快速的選項，但不一定適用於每一種情況。 此外，許多選項也可能會因為專案的不同而使得開發週期變慢或變快。 比方說，原生移除功能很花時間，但如果能讓大小變得很小，則移除功能所花費的時間並不會由於部署速度加快而彌補回來。 換句話說，原生移除功能可以大幅縮減應用程式的大小，從而讓部署速度加快。 不同專案的狀況不盡相同，測試是得到答案的唯一方法。

Xamarin 建置速度也會受到各種容量和電腦功能所影響，進而影響效能：處理器功能、匯流排速度、實體記憶體數量、磁碟速度、網路速度。 這些效能限制不在本文件的討論範圍內，開發人員有責任來處理這些限制。

## <a name="timing-apps"></a>對應用程式計時

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要在 Visual Studio for Mac 內啟用診斷 MSBuild 輸出：

1. 按一下 [Visual Studio for Mac] > [喜好設定...]****
2. 在左側樹狀檢視中，選取 [專案] > [建置]****
3. 在右側面板中,將「日誌詳細度」下拉設定為**診斷**:[![](ios-build-mechanics-images/image2.png "設定記錄詳細資訊")](ios-build-mechanics-images/image2.png#lightbox)
4. 按一下 [確定]****。
5. 重新啟動 Visual Studio for Mac
6. 清除並重建您的套件
7. 按一下 [建置輸出] 按鈕，檢視錯誤板內的診斷輸出 ([檢視] > [板] > [錯誤])

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 內啟用診斷 MSBuild 輸出：

1. 按一下 [工具] > [選項...]****
2. 在左側樹狀檢視中，選取 [專案和方案] > [建置並執行]****
3. 在右邊面板中,將*MSBuild 產生輸出詳細度下拉下設定為***診斷**:[![](ios-build-mechanics-images/image2-vs.png "設定 MSBuild 建置輸出詳細資訊")](ios-build-mechanics-images/image2-vs.png#lightbox)
4. 按一下 [確定]****。
5. 清除並重建您的套件。
6. 診斷輸出會顯示在 [輸出] 面板中。

-----

## <a name="timing-mtouch"></a>對 mtouch 計時

若要顯示 mtouch 建置程序專屬的資訊，請將 `--time --time` 傳遞至**專案選項**中的 mtouch 引數。 搜尋 `MTouch` 工作即可在建置輸出中找到結果：

```csharp
Setup: 36 ms
Resolve References: 982 ms
Extracted native link info: 987 ms
...
Total time: 1554 ms
```

## <a name="connecting-from-visual-studio-with-build-host"></a>使用組建主機從 Visual Studio 中連線

就技術上來說，Xamarin 工具能夠適用於任何可執行 OS X 10.10 Yosemite 或更新版本的 Mac。 不過，開發人員體驗和建置時間可能會因為 Mac 的效能而受到影響。

在斷開連接狀態下,Windows 上的 Visual Studio 僅執行 C# 編譯階段,不會嘗試執行連結或 AOT 編譯、將應用打包到 _.app_ 捆綁包或對應用捆綁包進行簽名。 (C# 編譯階段很少是性能瓶頸。嘗試透過 Mac Visual Studio 中的 Mac 構建主機直接建構,來精確定位建構在管道中速度的變慢位置。

此外，其中一個更常見會造成速度變慢的位置是 Windows 機器和 Mac 組建主機之間的網路連線。 這可能是因為網路上有實體阻礙，例如使用無線連線，或是必須行經飽和的機器 (例如 Mac-in-the-cloud 服務)。

## <a name="simulator-tricks"></a>模擬器訣竅

在開發行動應用程式時，必須要快速部署程式碼。 由於各種不同的原因 (包括速度和缺乏裝置佈建需求)，開發人員往往會選擇部署至預先安裝的模擬器 (simulator) 或仿真器 (emulator)。 對於開發人員工具的製造商來說，決定要提供模擬器還是仿真器取決於速度和相容性的取捨。 

Apple 提供模擬器來進行 iOS 開，藉由建立一個較不受限制的程式碼執行環境來提升開發速度而非相容性。 這個較不受限制的環境可讓 Xamarin 使用 Just In Time (JIT) 編譯器以供模擬器執行作業 (而非在裝置上使用 [AOT](~/ios/internals/architecture.md))，這表示組建會在執行階段編譯成機器碼。 Mac 的速度比裝置快很多，因此可以提供更好的效能。

模擬器會使用共用應用程式啟動器，讓啟動器得以重複使用，不像裝置上每次都要建置啟動器。

在將上述資訊納入考量時，下列清單可提供一些於模擬器上建置和部署應用程式時所需採取之步驟的資訊，以便為您提供最佳效能。

### <a name="tips"></a>提示

- 針對組建： 
  - 在 [專案選項] 中取消選取 [最佳化 PNG 影像]**** 選項。 模擬器上的組建並不需要此最佳化。
  - 將連結器設定為 [不要連結]****。 停用連結器會提升速度，原因是其需要大量執行時間。
  - 使用 `--nofastsim` 旗標停用共用應用程式啟動器會使模擬器組建變得很慢。 不再需要此旗標時，請將其移除。
  - 使用原生程式庫會讓速度變慢，原因是共用的 simlauncher 主要可執行檔無法在此情況下重複使用，因此必須對每個組建編譯應用程式專屬的可執行檔。
- 針對部署
  - 請盡可能讓模擬器保持隨時執行。 讓模擬器冷啟動最多需要 12 秒的時間。
- 其他秘訣
  - 建置好過重建，因為在建置前會先清除重建。 清除會移除可使用的參考，因此需要很長的時間。
  - 請利用模擬器不會強制建立沙箱的事實。 將視訊或其他資產等大型資源納入專案中，將會因為每次在模擬器中啟動應用程式時需要進行檔案複製作業，而產生高昂成本。 請將這些檔案放在主目錄中以避免進行這些成本高昂的作業，並在應用程式中以完整檔案路徑參考這些檔案。  
  - 有疑慮時，請使用 `--time --time` 旗標來測量您的變更

下列螢幕擷取畫面說明如何在 iOS 選項中設定模擬器的這些選項：

[![](ios-build-mechanics-images/image3.png "Setting the options")](ios-build-mechanics-images/image3.png#lightbox)

## <a name="device-tricks"></a>裝置訣竅

部署至裝置與部署至模擬器類似，因為模擬器是用於 iOS 裝置之組建的一小部分。 裝置的建置需要更多步驟，但優點是能讓您有額外的機會可將應用程式最佳化。

### <a name="build-configurations"></a>組建組態

我們提供了許多在部署 iOS 應用程式時的組建組態。 請務必確實了解每個組態，以便知道應該最佳化的時機和原因。

- 偵錯
  - 這是應用程式在開發階段時所應使用的主要組態，因此，其速度應盡可能地快速。
- 版本
  - 發行組建則會提供給使用者，而且必須著重於效能。 在使用發行組態時，建議您使用 LLVM 最佳化編譯器並將 PNG 檔案最佳化。

也請務必了解建置和部署之間的關聯性。 部署時間是應用程式大小的函式。 應用程式越大，部署時間就越長。 將應用程式大小降到最低，就能減少部署時間。

將應用程式大小降到最低，也能減少建置時間。 這是因為從應用程式中移除程式碼所需的時間，比為不會用到之程式碼進行原生編譯所需的時間少。 物件檔案越小意味著連結速度越快，從而會建立較小的可執行檔，而且要產生的符號也較少。 節省空間會有雙倍回報，這也是為何所有裝置組建都會以**連結 SDK** 作為預設值。 

> [!NOTE]
> 根據所使用的 IDE，**連結 SDK** 選項可能會顯示為「僅連結 Framework SDK」或「僅連結 SDK 組件」。

### <a name="tips"></a>提示

- 組建： 
  - 建置單一架構 (例如 ARM64) 的速度快過 FAT 二進位檔 (例如 ARMv7 + ARM64)
  - 避免在偵錯時對 PNG 檔案最佳化
  - 考慮連結所有組件。 將每個組件最佳化 
  - 使用  `--dsym=false` 來停用偵錯符號的建立。 但請注意，停用此功能代表著您只能在建置應用程式的該部機器上將當機報告符號化，而且只能在應用程式未移除的情況下進行。

應避免的事項如下：

- Fat 二進位檔 (偵錯) 
- 停用連結器 `--nolink` 
- 停用移除 
  - 符號 `--nosymbolstrip` 
  - IL (發行) `--nostrip`.  

其他秘訣 

- 和模擬器一樣，建置好過重建 
  - 會快取已 AOT 的組件 (物件檔) 
- 偵錯組建所花的時間比較長，因為其有符號、會執行 dsymutil，而且因為它最終的大小較大，所以需要額外的時間來上傳至裝置。 
- 發行組建依預設則會進行組件的 IL 移除。 其所花的時間很短，而且這點時間有可能在將較小的 .app 部署至裝置時彌補回來。
- 避免在每個組件 (偵錯) 部署大型的靜態檔案 
  - 使用 UIFileSharingEnabled (info.plist) 
    - 資產可以一次全部上傳 
- 有疑慮時，請使用 `--time --time` 旗標來測量您的變更

下列螢幕擷取畫面說明如何在 iOS 選項中設定模擬器的這些選項：

[![](ios-build-mechanics-images/image4.png "Setting the options")](ios-build-mechanics-images/image4.png#lightbox)

## <a name="using-the-linker"></a>使用連結器

在建置應用程式時，mtouch 會對受控碼使用連結器，以移除應用程式未使用的程式碼。 理論上，這可提供較小，因此更為快速的組建。 如需連結器的詳細資訊，請參閱[在 iOS 上連結](~/ios/deploy-test/linker.md)指南。

在使用連結器時，請考慮下列事項：

- 為裝置組建選取 [不要連結]**** 會花更久的時間，而且也會產生較大的應用程式。 
  - Apple 會拒絕大小超過限制的應用程式。 取決於 `MinimumOSVersion`，此限制可能小到只有 60 MB。 
  - 會包含原生可執行檔。 
  - 對於模擬器組建來說，使用 [不要連結] 的速度會較快，因為它會使用 JIT 編譯 (而非在裝置上使用 AOT)。
- 連結 SDK 是預設選項。
- 使用 [全部連結] 可能並不安全，特別是當您使用的並非自有程式碼時，例如 NuGet 或元件。 如果您選擇不要連結組件，來自這些服務的所有程式碼就會隨附於應用程式中，而可能建立更大的應用程式。 
  - 不過，如果您選擇 [全部連結]****，應用程式可能會損毀，特別是使用了外部元件時。 這是因為某些元件會在特定類型上使用反映。
  - 靜態分析和反映無法一起運作。 

可以使用[`[Preserve]`屬性](~/ios/deploy-test/linker.md)可以指示工具將內容保留在應用程式中。 

如果您沒有原始程式碼的存取權，或者原始程式碼是由工具產生且您沒有要加以變更，則仍可藉由建立 XML 檔案來描述需要保留的所有類型和成員來連結原始程式碼。 然後，您可以在專案選項中新增 `--xml={file.name}.xml` 旗標，以利用和使用屬性時相同的方式，來精確地處理程式碼。

### <a name="partially-linking-applications"></a>部分連結應用程式 

您也可以部分連結應用程式，以協助將應用程式的建置時間最佳化：

- 使用 `Link All` 並略過某些組件 
  - 會失去某些應用程式的大小最佳化。
  - 不需要原始程式碼的存取權。
  - 例如，`--linkall --linkskip=fieldserviceiOS`。

- 使用 `Link SDK` 選項，然後在您所需的組件上使用 `[LinkerSafe]` 屬性 
  - 需要原始程式碼的存取權。
  - 告訴系統可放心地連結組件，而且組件會和 Xamarin SDK 一樣地進行處理。

### <a name="objective-c-bindings"></a>Objective-C 繫結 

- 在繫結上使用 `[Assembly: LinkerSafe]` 屬性可節省時間和大小。

- SmartLink 
  - 在原生端上執行 
  - 使用 `[LinkWith (SmartLink=true)]` 屬性
  - 這可協助原生連結器從您連結時所依據的程式庫消除機器碼。 
  - 請注意，動態查閱符號不適用於此案例。 

## <a name="summary"></a>總結

本指南探討了要如何對 iOS 應用程式計時，以及取決於專案組建組態和選項的考量選項。 

<!-----
# Benchmarks

## Layer 1: building again after making modifications, but _without_ cleaning should be faster 

The app should build a bit more quickly if you have only made changes to a subset of the libraries and you do not clean the build before re-deploying. 

### Clean build time 
178 seconds 

### Build again (without cleaning) after making _no changes_ 
12.5 seconds 

### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
3 trials: 45 seconds, 43 seconds, 43 seconds 

### Build again (without cleaning) after changing 1 line in each of the following files 

- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 

3 trials: 45 seconds, 45 seconds, 45 seconds 

### Build again (without cleaning) after changing 1 line in each of the following files 

- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 
- Sales.Native.Core.IOS.Ext/ServiceInterfaces/AlertDialog/Dialog.cs 
- Sales.Native.Core.Tools.IOS.Ext/BaseViews/BaseNavigationViewController.cs 
- View.Common/Services/DataTransferResult.cs 

45 seconds 

## Layer 2: "app thinning" aka "device specific builds" 

The idea of "app thinning" is that the IDE will only build the 1 architecture needed for the specific device that you're deploying to (rather than _both_ 32-bit and 64-bit architectures). 

As of the latest "Xamarin 4" builds, you can now enable "app thinning" in Visual Studio via the "Project Options -> iOS Build -> Enable device-specific builds" setting. 

Or if you prefer you can achieve a similar result by changing the "Project Options -> iOS Build -> Advanced [tab] -> Supported architectures" to select just _one_ architecture (for example ARM64 if you are developing on a 64-bit device). 

(Caveat: I ran the following builds in Visual Studio for Mac on the Mac rather than on the command line.) 

### Clean build time without "device specific builds" 
177 seconds 

### Clean build time _with_ "device specific builds"  
2 trials: 106 seconds, 98 seconds 

### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 31 seconds, 31 seconds 

* * * 

## Using the same strategy, but explicitly setting "Supported architectures" to select ARM64 _only_ (rather than using "device specific builds") 

(These builds were again run on the command line using `xbuild`.) 

### Clean build time with "Supported architectures" set to ARM64 _only_ 
2 trials: 80 seconds, 91 seconds 

### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 26 seconds, 26 seconds 

[1] Mac system used for testing: MacBookAir5,2 

- 2.0 GHz Core i7 (I7-3667U) 

2 Cores with hyper-threading 

L2 Cache (per Core): 256 KB 
L3 Cache: 4 MB 

- Standard MacBook soldered-in solid-state storage 

- 8 GB RAM 
---->

## <a name="related-links"></a>相關連結

- [博客文章](https://blog.xamarin.com/xamarin-ios-build-improvements/)
- [在 iOS 上連結](~/ios/deploy-test/linker.md)
- [自訂連結器設定](~/cross-platform/deploy-test/linker.md)

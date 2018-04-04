---
title: Renderscript 簡介
description: 本指南介紹 Renderscript，並說明如何使用內建 Renderscript API 的 Xamarin.Android 應用程式中目標應用程式開發介面層級 17 或更高版本。
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: f9e21a51c409c5444f137a63eb2c6fadfef03cbe
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="an-introduction-to-renderscript"></a>Renderscript 簡介

_本指南介紹 Renderscript，並說明如何使用內建 Renderscript API 的 Xamarin.Android 應用程式中目標應用程式開發介面層級 17 或更高版本。_

## <a name="overview"></a>總覽

Renderscript 是為了改善效能的 Android 應用程式需要大量計算資源來建立由 Google 的程式設計架構。 它是低層級、 高效能應用程式開發介面，根據[C99](http://en.wikipedia.org/wiki/C99)。 因為它是低層級會在 Cpu、 Gpu 或 DSPs 執行的應用程式開發介面，Renderscript 是適用於 Android 應用程式，可能需要執行下列其中一項：

* 圖形
* 映像處理
* 加密
* 訊號處理
* 數學常式

將使用 Renderscript`clang`並編譯配套到 APK LLVM 位元組程式碼的指令碼。 第一次執行應用程式時，就會將 LLVM 位元組程式碼編譯成機器碼，以在裝置上的處理器。 此架構可讓 Android 應用程式本身需要撰寫它針對每個處理器在裝置上本身利用機器沒有開發人員提供的程式碼的優點。

有兩個元件 Renderscript 常式：

1. **Renderscript 執行階段**&ndash;這是負責執行 Renderscript 原生 Api。 這包括任何 Renderscripts 撰寫的應用程式。

2. **從 Android 架構 managed 包裝函式** &ndash; Managed 允許控制並與其互動的 Renderscript 執行階段和指令碼的 Android 應用程式的類別。 除了控制 Renderscript 執行階段提供的架構類別，Android 工具鏈會檢查 Renderscript 原始程式碼，並產生 Android 應用程式所使用的 managed 包裝函式類別。

下圖說明這些元件彼此的關係：

![說明如何與 Renderscript 執行階段互動 Android 架構圖表](renderscript-images/renderscript-01.png)

有三個重要的概念，在 Android 應用程式中使用 Renderscripts:

1. **在內容** &ndash; managed 配置資源給 Renderscript 並讓傳送和接收來自 Renderscript 資料 Android 應用程式的 Android SDK 所提供的 API。

2. **A_計算核心_** &ndash;也稱為_根核心_或_核心_，這個常式，它會執行這項工作。 核心是非常類似於 C 函式。可並行的常式配置的記憶體中的所有資料將會執行它。

3. **記憶體配置**&ndash;資料會與透過核心_[配置](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_。 核心的環境可能有一個輸入和/或一個輸出配置。

[Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)命名空間包含與 Renderscript 執行階段互動的類別。 特別是， [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)類別會管理生命週期和 Renderscript 引擎的資源。 Android 應用程式必須初始化一或多個[ `Android.Renderscripts.Allocation` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)物件。 會負責配置和存取的記憶體，Android 應用程式及 Renderscript 執行階段之間共用的受管理的 API 所配置。 一般而言，為輸入，建立一個配置，並選擇另一個配置是用來保留核心的輸出。 Renderscript 執行階段引擎和相關聯的 managed 包裝函式類別會管理記憶體配置所持有的存取，就不必再 Android 應用程式開發人員執行任何額外的工作。

配置將包含一或多個[Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/)。
元素會描述每個配置中的資料是特殊的類型。
必須符合配置的輸出，項目類型的輸入項目的類型。 Renderscript 執行時，會逐一輸入配置以平行方式，在每個項目，並將結果寫入至輸出配置。 有兩種類型的項目：

- **簡單型別**&ndash;在概念上這等同於 C 資料類型，`float`或`char`。

- **複雜型別**&ndash;此類型是類似於 C `struct`。

Renderscript 引擎會執行執行階段檢查，以確保每個配置中的項目相容所需的核心。 如果項目在配置的資料類型不相符核心所預期的資料類型，將會擲回例外狀況。

所有 Renderscript 核心會都包裝的下階類型[ `Android.Renderscripts.Script` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/)類別。 `Script`類別用來設定 Renderscript 參數，設定適當`Allocations`，並執行 Renderscript。 有兩個`Script`Android SDK 中的子類別：


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; 一些較常見的 Renderscript 工作會集結在 Android SDK，使用的子類別存取[ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/)類別。 沒有需要開發人員可能需要在應用程式中使用這些指令碼，依照已提供任何額外的步驟。

- **`ScriptC_XXXXX`** &ndash; 也稱為_使用者指令碼_，這些是由開發人員所撰寫，且封裝在 APK 的指令碼。 在編譯時期，Android 工具鏈會產生可用於 Android 的應用程式中的指令碼的 managed 包裝函式類別。
  這些產生的類別名稱是 Renderscript 檔案，加上名稱`ScriptC_`。 撰寫並納入使用者指令碼未正式支援 Xamarin.Android 和超出本指南的範圍。

這兩種類型，只的`StringIntrinsic`Xamarin.Android 支援。 本指南將討論如何在 Xamarin.Android 應用程式中使用內建函式的指令碼。

## <a name="requirements"></a>需求

此指南適用於 Xamarin.Android 應用程式的目標應用程式開發介面層級 17 或更高版本。 使用_使用者指令碼_未涵蓋在本指南中。

[Xamarin.Android V8 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/)backports 內建 Renderscript API，以較舊版本的 Android sdk 為目標的應用程式。 將此封裝加入至 Xamarin.Android 專案應該允許應用程式以利用內建函式的指令碼的 Android sdk 的目標舊版。

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>使用內建 Renderscripts Xamarin.Android 中

內建函式的指令碼會執行少量的額外的程式碼需要大量運算工作的好方法。 已調整為提供最佳效能，大型的交叉區段的裝置上的游標。
是很常見的內建函式的指令碼來執行 10 倍較快，managed 程式碼和 2-3 倍之後比自訂 C 實作的位置。 內建函式的指令碼所涵蓋的許多一般處理案例。 這份內建函式的指令碼描述 Xamarin.Android 中的目前指令碼：

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash;轉換 RGB RGBA 使用 3D 查閱資料表。 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh 效能 Renderscript Api 來[BLAS](http://www.netlib.org/blas/)。 BLAS （基本線性代數複） 是提供標準建置組塊執行基本的向量和矩陣作業的常式。 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash;混合在一起的兩個配置。

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash;高斯模糊套用於配置。

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash;適用於色彩矩陣的配置 （也就是變更色彩調整色調）。

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash; 3x3 色彩矩陣套用於配置。

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash; 5x5 色彩矩陣套用於配置。

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash;長條圖的內建篩選器。

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash;緩衝區適用於每個通道的查閱資料表。

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash;指令碼執行的 2D 配置調整大小。

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash;將 RGB YUV 緩衝區。

請如需每個內建函式的指令碼的詳細資訊，參閱 API 文件。

在 Android 應用程式中使用 Renderscript 的基本步驟說明如下。

**建立 Renderscript 內容** &ndash; [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)類別是 Renderscript 內容的 managed 包裝函式會控制資源管理、 初始化及清除。 使用建立 Renderscript 物件`RenderScript.Create`採用 Android 的內容 （例如活動） 做為參數的 factory 方法。 下列程式碼會示範如何初始化 Renderscript 內容：

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**建立配置**&ndash;根據內建函式的指令碼，可能需要建立一或兩個`Allocation`s。 [ `Android.Renderscripts.Allocation` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)類別有數種可協助進行具現化的內建配置的 factory 方法。 例如，下列程式碼片段會示範如何建立點陣圖的配置。

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

通常，您必須在此處建立`Allocation`來保存的指令碼的輸出資料。 這個下列程式碼片段示範如何使用`Allocation.CreateTyped`helper 來具現化第二個`Allocation`相同類型與原始：

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**具現化指令碼包裝函式**&ndash;每個內建函式的指令碼包裝函式類別應該有的 helper 方法 (通常稱為`Create`) 以具現化該指令碼的包裝函式物件。 下列程式碼片段是如何具現化的範例`ScriptIntrinsicBlur`模糊物件。 `Element.U8_4` Helper 方法會建立描述為 4 的 8 位元不帶正負號的整數值，適用於保存的資料欄位的資料類型的項目`Bitmap`物件：

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**指派 Allocation(s)、 設定參數，與執行指令碼** &ndash; `Script`類別提供`ForEach`實際執行 Renderscript 方法。 這個方法會逐一查看每個`Element`中`Allocation`保留輸入的資料。 在某些情況下，可能需要提供`Allocation`保存輸出。
`ForEach` 將會覆寫輸出的內容配置。 繼續執行程式碼片段，先前的步驟，此範例示範如何指派輸入的配置、 設定參數，以及最後執行的指令碼 (將結果複製到輸出配置):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

您可能想要簽出[模糊的影像 Renderscript](https://developer.xamarin.com/recipes/android/other_ux/drawing/blur_an_image_with_renderscript/)配方，它是如何在 Xamarin.Android 中使用內建函式的指令碼的完整範例。

## <a name="summary"></a>總結

本指南會引進 Renderscript 以及如何在 Xamarin.Android 應用程式中使用它。 簡要討論過 Renderscript 為何，Android 應用程式中的運作方式。 它描述 Renderscript 和之間的差異中的索引鍵元件_使用者指令碼_和_內建指令碼_。 最後，本指南所討論的 Xamarin.Android 應用程式中使用內建函式的指令碼中的步驟。



## <a name="related-links"></a>相關連結

- [Android.Renderscripts namespace](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [模糊 Renderscript 的映像](https://developer.xamarin.com/recipes/android/other_ux/drawing/blur_an_image_with_renderscript/)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [教學課程： 開始使用 Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)

---
title: Renderscript 簡介
description: 本指南介紹 Renderscript，並說明如何使用內建 Renderscript API 的 Xamarin.Android 應用程式中的目標 API 層級 17 或更高版本。
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 5369542552a41100443c5e91ceca9e110c5c7c3c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108726"
---
# <a name="an-introduction-to-renderscript"></a>Renderscript 簡介

_本指南介紹 Renderscript，並說明如何使用內建 Renderscript API 的 Xamarin.Android 應用程式中的目標 API 層級 17 或更高版本。_

## <a name="overview"></a>總覽

Renderscript 是由 Google 建立改進需要大量計算資源的 Android 應用程式的效能用途的程式設計架構。 它是低層級、 高效能基礎 API [C99](http://en.wikipedia.org/wiki/C99)。 因為它是低層級的 Cpu、 Gpu，或是 Dsp 執行的 API，Renderscript 是非常適用於 Android 的應用程式，可能需要執行下列其中一項：

* 圖形
* 映像處理
* 加密
* 訊號處理
* 數學常式

將使用 Renderscript`clang`並編譯成會打包成 APK LLVM 位元組程式碼的指令碼。 第一次執行應用程式時，LLVM 位元組程式碼會編譯成機器碼，以在裝置上的處理器。 此架構可讓 Android 應用程式利用自己必須為撰寫程式碼的每一個處理器上的裝置本身的機器碼，而不需要開發人員的優點。

有兩個元件 Renderscript 常式：

1. **Renderscript 執行階段**&ndash;這是負責執行 Renderscript 的原生 Api。 這包括任何 Renderscripts 撰寫的應用程式。

2. **從 Android 架構 managed 包裝函式** &ndash; Managed 類別，可讓 Android 應用程式控制項並與其互動的 Renderscript 執行階段和指令碼。 除了控制 Renderscript 執行階段提供架構類別，會檢查 Renderscript 原始程式碼 Android 工具鏈，並將其產生 managed 包裝函式類別使用於 Android 的應用程式中。

下圖說明這些元件彼此的關係：

![說明 Android 架構與 Renderscript 執行階段之間的互動方式的圖表](renderscript-images/renderscript-01.png)

有三個重要的概念，在 Android 應用程式中使用 Renderscripts:

1. **內容** &ndash; managed Renderscript 來配置資源，並可讓 Android 應用程式，以傳遞，並接收來自 Renderscript 資料之 Android SDK 所提供的 API。

2. **A_計算核心_** &ndash;也稱為_根核心_或_核心_，這個常式，會執行的工作。 核心是非常類似於 C 函式;它是一個可平行處理常式，將會執行配置的記憶體中的所有資料。

3. **配置的記憶體**&ndash;核心，以透過來回傳遞資料_[配置](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_。 核心可能會有一個輸入和/或一個輸出配置。

[Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)命名空間包含與 Renderscript 執行階段互動的類別。 特別是， [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)類別會管理生命週期和 Renderscript 引擎的資源。 Android 應用程式必須初始化一或多個 [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
物件。 配置是 managed 的 API 會負責配置和存取 Android 應用程式及 Renderscript 執行階段之間共用的記憶體。 一般而言，一次配置系統會為輸入，並選擇性地在另一個配置建立保留核心的輸出。 Renderscript 執行階段引擎和相關聯的 managed 包裝函式類別會管理記憶體配置所持有的存取，則不需要 Android 應用程式開發人員執行任何額外的工作。

配置將會包含一或多個[Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/)。
項目所特製化的型別描述每個配置中的資料。
項目類型輸出的配置必須符合輸入的項目型別。 Renderscript 執行時，會逐一查看輸入配置，以平行方式，在每個項目，並將結果寫入至輸出配置。 有兩種類型的項目：

- **簡單的型別**&ndash;在概念上來說這是 C 資料類型，與相同`float`或`char`。

- **複雜型別**&ndash;這個型別是類似於 C `struct`。

Renderscript 引擎會執行執行階段檢查，以確認每個配置中的項目相容所需的核心。 如果配置中的項目資料類型不相符的核心所預期的資料類型，將會擲回例外狀況。

所有 Renderscript 核心就會是子系的型別換都行 [`Android.Renderscripts.Script`](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/)
類別的新執行個體。 `Script`類別用來設定 Renderscript 參數，設定適當`Allocations`，並執行 Renderscript。 有兩個`Script`Android SDK 中的子類別：


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; 一些較常見的 Renderscript 工作會配套在 Android SDK，並可存取的子類別的[ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/)類別。 開發人員需要任何額外的步驟，以在其應用程式中使用這些指令碼，因為它們已經有提供，則不需要。

- **`ScriptC_XXXXX`** &ndash; 也稱為_使用者指令碼_，這些是開發人員所撰寫和封裝在 APK 中的指令碼。 在編譯時期，Android 工具鏈會產生可用於 Android 的應用程式中的指令碼的 managed 包裝函式類別。
  這些產生的類別名稱是 Renderscript 檔案名稱，前面會加上`ScriptC_`。 撰寫，並將使用者指令碼未正式支援 xamarin.android 和超出本指南的範圍。

這兩個類型，只`StringIntrinsic`Xamarin.Android 支援。 本指南會討論如何在 Xamarin.Android 應用程式中使用內建函式的指令碼。

## <a name="requirements"></a>需求

本指南適用於 Xamarin.Android 應用程式的目標 API 層級 17 或更高版本。 善用_使用者指令碼_未涵蓋在本指南中。

[Xamarin.Android V8 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/)反向移植舊版 Android SDK 為目標的應用程式內建 Renderscript API。 將此封裝加入到 Xamarin.Android 專案應該允許應用程式較舊版本為目標的 Android sdk，利用內建函式的指令碼。

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>在 Xamarin.Android 中使用內建 Renderscripts

內建函式的指令碼會執行密集運算的工作，利用最少量的額外程式碼的好方法。 已手動微調，以提供最佳效能，大型的交叉區段的裝置上。
您不常見內建函式的指令碼，才能執行 10 倍的速度比受管理的程式碼和 2-3 倍之後比自訂的 C 實作。 內建函式的指令碼所涵蓋的許多一般的處理案例。 這份內建函式的指令碼說明在 Xamarin.Android 中目前的指令碼：

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash;轉換 RGB RGBA 使用 3D 的查閱資料表。 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh 效能 Renderscript Api 來[BLAS](http://www.netlib.org/blas/)。 BLAS （基本線性代數複） 是提供來執行基本的向量和矩陣作業的標準建置組塊的常式。 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash;混合這兩個配置在一起。

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash;高斯模糊套用於配置。

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash;適用於色彩矩陣的配置 （也就是變更色彩調整色調）。

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash; 3x3 色彩矩陣套用至配置。

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash; 5x5 色彩矩陣套用至配置。

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash;長條圖的內建篩選器。

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash;緩衝區適用於每個通道的查閱資料表。

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash;執行 2D 配置調整大小的指令碼。

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash;將 RGB YUV 緩衝區。

請如需每個內建函式的指令碼的詳細資訊，參閱 API 文件。

接下來說明使用 Renderscript Android 應用程式中的基本步驟。

**建立 Renderscript 內容** &ndash; [`Renderscript`](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)
類別是 Renderscript 內容周圍的 managed 包裝函式和會將控制初始化、 資源管理，並清除。 Renderscript 物件使用建立`RenderScript.Create`factory 方法，後者會採用 Android 內容 （例如活動） 做為參數。 下列程式碼示範如何初始化 Renderscript 內容：

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**建立配置**&ndash;根據內建函式的指令碼，可能需要建立一或兩個`Allocation`s。 的 [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
類別有數個的 factory 方法，來協助進行具現化的內建函式配置。 例如，下列程式碼片段會示範如何建立點陣圖的配置。

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

通常，您必須在此處建立`Allocation`來保存指令碼的輸出資料。 此下列程式碼片段示範如何使用`Allocation.CreateTyped`具現化第二個協助程式`Allocation`相同類型與原始：

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**指令碼包裝函式具現化**&ndash;每個內建函式的指令碼包裝函式類別應該有 helper 方法 (通常稱為`Create`) 具現化該指令碼的包裝函式物件。 下列程式碼片段是如何具現化的範例`ScriptIntrinsicBlur`模糊物件。 `Element.U8_4` Helper 方法會建立描述適用於保存的資料的 8 位元不帶正負號的整數值的 4 個欄位的資料類型的項目`Bitmap`物件：

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**指派 Allocation(s)、 設定參數，執行指令碼** &ndash; `Script`類別會提供`ForEach`實際執行 Renderscript 的方法。 這個方法會反覆查看每個`Element`在`Allocation`保留輸入的資料。 在某些情況下，它可能需要提供`Allocation`保存輸出。
`ForEach` 將會覆寫輸出的內容配置。 為了繼續使用先前步驟中的程式碼片段，此範例會示範如何指派輸入的配置、 設定參數，以及最後執行指令碼 (將結果複製到輸出配置):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

您可能想要查看[模糊的映像具有 Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)配方時，它是如何在 Xamarin.Android 中使用的內建函式的指令碼的完整範例。

## <a name="summary"></a>總結

本指南介紹 Renderscript，以及如何在 Xamarin.Android 應用程式中使用它。 它簡短討論 Renderscript 是什麼，以及 Android 應用程式中的運作方式。 它會描述一些 Renderscript 以及之間的差異的關鍵元件_使用者指令碼_並_內建指令碼_。 最後，本指南會討論在 Xamarin.Android 應用程式中使用的內建函式的指令碼中的步驟。



## <a name="related-links"></a>相關連結

- [Android.Renderscripts 命名空間](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [模糊 Renderscript 的映像](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [教學課程： 開始使用 Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)

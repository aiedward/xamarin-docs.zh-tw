---
title: Renderscript 簡介
description: 本指南介紹 Renderscript, 並說明如何在以 API 層級17或更高版本為目標的 Xamarin 應用程式中使用內部 Renderscript API。
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 9f15ef73e51a2e94e1a1174134f3e69d2cb2c4a3
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511433"
---
# <a name="an-introduction-to-renderscript"></a>Renderscript 簡介

_本指南介紹 Renderscript, 並說明如何在以 API 層級17或更高版本為目標的 Xamarin 應用程式中使用內部 Renderscript API。_

## <a name="overview"></a>總覽

Renderscript 是 Google 所建立的程式設計架構, 目的是為了改善需要大量計算資源的 Android 應用程式效能。 這是以[C99](https://en.wikipedia.org/wiki/C99)為基礎的低層級高效能 API。 因為這是將在 Cpu、Gpu 或 Dsp 上執行的低層級 API, 所以 Renderscript 非常適用于可能需要執行下列任一項的 Android 應用程式:

* 圖形
* 影像處理
* 加密
* 信號處理
* 數學常式

Renderscript 會使用`clang`並編譯腳本, 以 LLVM 已配套到 APK 中的位元組程式碼。 第一次執行應用程式時, 會將 LLVM 的位元組碼編譯成裝置上的處理器電腦程式代碼。 此架構可讓 Android 應用程式入侵機器碼的優點, 而開發人員本身不需要針對裝置本身的每個處理器撰寫它。

Renderscript 常式有兩個元件:

1. **Renderscript 運行**時間&ndash;這是負責執行 Renderscript 的原生 api。 這包括為應用程式撰寫的任何 Renderscripts。

2. **來自 Android Framework 的受控包裝**函式&ndash;可讓 Android 應用程式控制和與 Renderscript 執行時間和腳本互動的 Managed 類別。 除了架構提供用來控制 Renderscript 執行時間的類別之外, Android 工具鏈也會檢查 Renderscript 的原始程式碼, 並產生可供 Android 應用程式使用的受控包裝函式類別。

下圖說明這些元件的關聯性:

![說明 Android Framework 如何與 Renderscript 執行時間互動的圖表](renderscript-images/renderscript-01.png)

在 Android 應用程式中使用 Renderscripts 有三個重要概念:

1. **內容**&ndash; Android SDK 所提供的 managed API, 可將資源配置給 Renderscript, 並允許 Android 應用程式傳遞和接收來自 Renderscript 的資料。

2. **_計算核心_** 也稱為_根核心_或核心, 這是執行工作的常式。 &ndash; 核心與 C 函數非常類似;它是一個可並行常式, 會在配置記憶體中的所有資料上執行。

3. **配置的記憶體**&ndash;資料會透過 _[配置](xref:Android.Renderscripts.Allocation)傳遞至核心或從中傳送。 核心可能會有一個輸入和/或一個輸出配置。

[Renderscripts](xref:Android.Renderscripts)命名空間包含用來與 Renderscript 執行時間互動的類別。 特別是[`Renderscript`](xref:Android.Renderscripts.RenderScript) , 類別會管理 Renderscript 引擎的生命週期和資源。 Android 應用程式必須初始化一或多個[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
物件. 配置是受控 API, 負責配置和存取 Android 應用程式與 Renderscript 執行時間之間共用的記憶體。 通常會針對輸入建立一個配置, 並選擇性地建立另一個配置來保存核心的輸出。 Renderscript 執行時間引擎和相關聯的 managed 包裝函式類別會管理配置所持有之記憶體的存取權, 因此 Android 應用程式開發人員不需要執行任何額外的工作。

配置會包含一或多個[Renderscripts 元素](xref:Android.Renderscripts.Element)。
元素是一種專門的類型, 可描述每個配置中的資料。
輸出配置的元素類型必須符合 input 元素的類型。 執行時, Renderscript 會平行逐一查看輸入配置中的每個元素, 並將結果寫入輸出配置。 元素有兩種類型:

- **簡單類型**在概念上, 這與 C 資料類型相同, `float`或`char`為。 &ndash;

- **複雜類型**此類型類似于 C `struct`。 &ndash;

Renderscript 引擎會執行執行時間檢查, 以確保每個配置中的元素都與核心所需的專案相容。 如果配置中元素的資料類型不符合核心預期的資料類型, 則會擲回例外狀況。

所有 Renderscript 核心都將以屬於子系的類型包裝。[`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script)
類別的新執行個體。 類別是用來設定 Renderscript 的參數、設定適當`Allocations`的, 然後執行 Renderscript。 `Script` Android SDK 中有`Script`兩個子類別:


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash;一些較常見的 Renderscript 工作會配套在 Android SDK 中, 而且可由 [ScriptIntrinsic](xref:Android.Renderscripts.ScriptIntrinsic) 類別的子類別存取。 開發人員不需要採取任何額外的步驟, 就能在應用程式中使用這些腳本, 因為它們已經提供。

- **`ScriptC_XXXXX`** 也稱為_使用者腳本_, 這些是由開發人員撰寫並封裝在 APK 中的腳本。 &ndash; 在編譯時期, Android 工具鏈會產生 managed 包裝函式類別, 以允許在 Android 應用程式中使用腳本。
  這些產生的類別名稱是 Renderscript 檔案的名稱, 前面加`ScriptC_`上。 除了本指南的討論範圍之外, Xamarin 不會正式支援撰寫和併入使用者腳本。

在`StringIntrinsic`這兩種類型中, 只支援 Xamarin. Android。 本指南將討論如何在 Xamarin Android 應用程式中使用內部腳本。

## <a name="requirements"></a>需求

本指南適用于以 API 層級17或更高版本為目標的 Xamarin Android 應用程式。 本指南未涵蓋_使用者腳本_的使用方式。

[V8 支援程式庫](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/)反向移植內部 Renderscript API, 適用于以舊版 Android SDK 為目標的應用程式。 將此套件新增到 Xamarin. Android 專案應允許以舊版 Android SDK 為目標的應用程式利用內建的腳本。

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>在 Xamarin 中使用內部 Renderscripts

內建腳本是以最少量的額外程式碼執行密集運算工作的絕佳方式。 他們已進行微調, 以在大型裝置上提供最佳效能。
內建腳本的執行速度比 managed 程式碼快10倍, 以及自訂 C 實作為之後的 2-3 倍。 內建腳本涵蓋許多一般處理案例。 此內建腳本清單描述 Xamarin 中目前的腳本:

- [ScriptIntrinsic3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT)&ndash;使用3d 查閱資料表將 RGB 轉換成 RGBA。 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html)將效能 Renderscript api Provideshigh 到[BLAS。](http://www.netlib.org/blas/) &ndash; BLAS (基本線性代數 Subprograms) 是提供標準建立組塊來執行基本向量和矩陣作業的常式。 

- [ScriptIntrinsicBlend](xref:Android.Renderscripts.ScriptIntrinsicBlend)&ndash;將兩個配置混合在一起。

- [ScriptIntrinsicBlur](xref:Android.Renderscripts.ScriptIntrinsicBlur)&ndash;將高斯模糊套用至配置。

- [ScriptIntrinsicColorMatrix](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix)&ndash;將色彩矩陣套用至配置 (亦即變更 colours、調整色調)。

- [ScriptIntrinsicConvolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3)&ndash;將3x3 色彩矩陣套用至配置。

- [ScriptIntrinsicConvolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5)&ndash;將5x5 色彩矩陣套用至配置。

- [ScriptIntrinsicHistogram](xref:Android.Renderscripts.ScriptIntrinsicHistogram)&ndash;內部長條圖篩選準則。

- [ScriptIntrinsicLUT](xref:Android.Renderscripts.ScriptIntrinsicLUT)&ndash;將每個通道的查閱資料表套用至緩衝區。

- [ScriptIntrinsicResize](xref:Android.Renderscripts.ScriptIntrinsicResize)&ndash;執行2d 配置調整大小的腳本。

- [ScriptIntrinsicYuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB)&ndash;將 YUV 緩衝區轉換成 RGB。

如需每個內建腳本的詳細資料, 請參閱 API 檔。

接下來會說明在 Android 應用程式中使用 Renderscript 的基本步驟。

**建立 Renderscript 內容**&ndash;的[`Renderscript`](xref:Android.Renderscripts.RenderScript)
類別是 Renderscript 內容的 managed 包裝函式, 可控制初始化、資源管理和清除。 Renderscript 物件是使用`RenderScript.Create` factory 方法建立的, 它會採用 Android 內容 (例如活動) 做為參數。 下面這行程式碼示範如何初始化 Renderscript 內容:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**建立**配置視內建腳本而定, 可能需要建立一或兩個`Allocation`。 &ndash; 該[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
類別有數個 factory 方法, 可協助您具現化內建函式的配置。 例如, 下列程式碼片段示範如何建立點陣圖的配置。

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

通常需要建立`Allocation`來保存腳本的輸出資料。 下列程式碼片段示範如何使用`Allocation.CreateTyped` helper 來具現化與原始類型相同的第二個: `Allocation`

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

具現**化腳本包裝**函式每個內建腳本包裝函式類別都應該要有 helper `Create`方法 (通常稱為), 才能為該腳本初始化包裝函式物件。 &ndash; 下列程式碼片段是如何具現化`ScriptIntrinsicBlur`模糊物件的範例。 Helper 方法會建立一個專案, 描述屬於8位不帶正負號整數值4個欄位的資料類型, 適合用`Bitmap`來保存物件的資料: `Element.U8_4`

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**指派配置, 設定參數, & 執行腳本**類別提供了實際執行 Renderscript 的方法。`ForEach` &ndash; `Script` 這個方法會逐一查看保存`Element`輸入資料`Allocation`的中的每個。 在某些情況下, 您可能必須提供`Allocation`保存輸出的。
`ForEach`將會覆寫輸出配置的內容。 為了與先前步驟中的程式碼片段搭配執行, 此範例示範如何指派輸入配置、設定參數, 然後最後執行腳本 (將結果複製到輸出配置):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

您可能想要查看[模糊影像與 Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)配方, 這是如何在 Xamarin 中使用內部腳本的完整範例。

## <a name="summary"></a>總結

本指南介紹了 Renderscript, 以及如何在 Xamarin 應用程式中使用它。 它會簡短討論什麼是 Renderscript, 以及它在 Android 應用程式中的運作方式。 其中說明了 Renderscript 中的一些主要元件, 以及_使用者腳本_與_內部腳本_之間的差異。 最後, 本指南討論在 Xamarin Android 應用程式中使用內建腳本的步驟。



## <a name="related-links"></a>相關連結

- [Renderscripts 命名空間](xref:Android.Renderscripts)
- [使用 Renderscript 模糊影像](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [教學課程：使用 Renderscript 消費者入門](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)

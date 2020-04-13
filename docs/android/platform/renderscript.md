---
title: 轉譯文稿簡介
description: 本指南介紹 Renderscript,並說明如何在針對 API 級別 17 或更高的 Xamarin.Android 應用程式中使用內部呈現腳本 API。
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 884b69b0cdecf4f979cec314b6440974c5bac97d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019801"
---
# <a name="an-introduction-to-renderscript"></a>轉譯文稿簡介

_本指南介紹 Renderscript,並說明如何在針對 API 級別 17 或更高的 Xamarin.Android 應用程式中使用內部呈現腳本 API。_

## <a name="overview"></a>概觀

Renderscript 是 Google 創建的程式設計框架,旨在提高需要大量計算資源的 Android 應用程式的性能。 它是一種基於[C99](https://en.wikipedia.org/wiki/C99)的低級高性能 API。 因為它是將在 CPU、GPU 或 DSP 上執行的低級 API,因此呈現腳本非常適合可能需要執行以下任何操作的 Android 應用:

- 圖形
- 影像處理
- 加密
- 信號處理
- 數學例程

呈現文稿將使用`clang`並編譯腳本到捆綁到 APK 中的 LLVM 位元組碼。 首次運行應用時,LLVM 位元組碼將編譯為設備上處理器的電腦代碼。 此體系結構允許 Android 應用程式利用計算機代碼的優勢,而無需開發人員自己為設備上的每個處理器編寫它。

轉譯文稿例的兩個元件:

1. **呈現文稿執行時**&ndash;這是負責執行呈現腳本的本機 API。 這包括為應用程式編寫的任何呈現腳本。

2. 來自 Android&ndash;框架 託管類**的託管包裝器**,允許 Android 應用控制和與 Renderscript 運行時和腳本進行交互。 除了為控制 Renderscript 運行時提供的框架外,Android 工具鏈還將檢查 Renderscript 原始程式碼並生成託管包裝類供 Android 應用程式使用。

下圖說明了這些元件之間的關係:

![說明 Android 框架如何與呈現文稿執行時互動的圖表](renderscript-images/renderscript-01.png)

在 Android 應用程式中使用呈現腳本有三個重要概念:

1. **上下文**&ndash;由 Android SDK 提供的託管 API,用於將資源分配給呈現腳本,並允許 Android 應用從呈現腳本傳遞和接收數據。

2. **_計算內核_**&ndash;也稱為_根內核_或_內核_,這是一個執行該工作例程。 內核與C函數非常相似;它是一個可並行的例程,將運行在已分配記憶體中的所有數據上。

3. **分配的記憶體**&ndash;數據通過_[分配](xref:Android.Renderscripts.Allocation)_ 傳入內核並從內核傳遞。 內核可能具有一個輸入和/或一個輸出分配。

[Android.Renderscript](xref:Android.Renderscripts)命名空間包含用於與渲染腳本運行時交互的類。 特別是,[`Renderscript`](xref:Android.Renderscripts.RenderScript)該類將管理 Renderscript 引擎的生命週期和資源。 Android 應用程式必須初始化一個或多個[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
物件。 分配是一個託管 API,負責分配和訪問在 Android 應用和 Renderscript 運行時之間共用的記憶體。 通常,為輸入創建一個分配,並選擇性地創建另一個分配來保存內核的輸出。 Renderscript 運行時引擎和關聯的託管包裝類將管理對分配持有的記憶體的訪問,Android 應用開發人員無需執行任何額外的工作。

分配將包含一個或多個[Android.Renderscripts.元素](xref:Android.Renderscripts.Element)。
元素是描述每個分配中數據的專用類型。
輸出分配的元素類型必須與輸入元素的類型匹配。 執行時,渲染腳本將並行反覆運算輸入分配中的每個元素,並將結果寫入輸出分配。 元素有兩種類型:

- **簡單類型**&ndash;概念上,這與 C`float`數據類型`char`或相同。

- **複合類型**&ndash;這個類型類似於`struct`C 。

Renderscript 引擎將執行執行時檢查,以確保每個分配中的元素與內核所需的元素相容。 如果分配中元素的數據類型與內核預期的數據類型不匹配,則將引發異常。

所有 Renderscript 內核都將由[`Android.Renderscripts.Script`](xref:Android.Renderscripts.Script)
類別的新執行個體。 該`Script`類用於設置渲染腳本的參數、設置適當`Allocations`的 和運行渲染腳本。 Android SDK`Script`中有兩個子類:

- **`Android.Renderscripts.ScriptIntrinsic`**&ndash;某些更常見的呈現腳本任務捆綁在 Android SDK 中,並且可通過[腳本內部類](xref:Android.Renderscripts.ScriptIntrinsic)的子類訪問。 開發人員無需採取任何額外的步驟來在其應用程式中使用這些腳本,因為它們已經提供。

- **`ScriptC_XXXXX`**&ndash;也稱為_使用者腳本_,這些腳本由開發人員編寫並打包在 APK 中。 在編譯時,Android 工具鏈將生成託管包裝類,這些類將允許在 Android 應用中使用腳本。
  這些產生的類別名稱是 Renderscript 檔的名稱,預先`ScriptC_`設定 。 Xamarin.Android 並不正式支援編寫和合併使用者腳本,超出了本指南的範圍。

在這兩種類型中,`StringIntrinsic`只有 Xamarin.安卓支援。 本指南將討論如何在 Xamarin.Android 應用程式中使用內部腳本。

## <a name="requirements"></a>需求

本指南適用於針對 API 級別 17 或更高級別的 Xamarin.Android 應用程式。 本指南不包括_使用者腳本_的使用。

[Xamarin.Android V8 支援庫](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/)為面向舊版本的 Android SDK 的應用提供非串渲染 API 的反向埠。 將此包添加到 Xamarin.Android 專案應允許針對舊版本的 Android SDK 的應用利用內部腳本。

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>在 Xamarin.安卓中使用內在渲染腳本

內部腳本是使用最少的額外代碼執行密集型計算任務的好方法。 它們經過手工調整,可對大型器件橫截面提供最佳性能。
內部腳本的運行速度比託管代碼快 10 倍,運行速度比自定義 C 實現快 2-3 倍,這種情況並不少見。 許多典型的處理方案都由內部腳本涵蓋。 此內部文本清單描述了 Xamarin.Android 中的當前文本:

- [腳本內部3DLUT](xref:Android.Renderscripts.ScriptIntrinsic3DLUT)&ndash;使用 3D 尋找表將 RGB 轉換為 RGBA。 

- [腳本內在BLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html)&ndash;為[BLAS](http://www.netlib.org/blas/)提供高性能渲染 API。 BLAS(基本線性代數子程式)是為執行基本向量和矩陣操作提供標準構建基塊的例程。 

- [腳本內部混合](xref:Android.Renderscripts.ScriptIntrinsicBlend)&ndash;混合兩個分配在一起。

- [腳本內在模糊](xref:Android.Renderscripts.ScriptIntrinsicBlur)&ndash;將高斯模糊應用於分配。

- [腳本內在顏色矩陣](xref:Android.Renderscripts.ScriptIntrinsicColorMatrix)&ndash;將顏色矩陣應用於分配(即更改顏色、調整色調)。

- [腳本內在Convolve3x3](xref:Android.Renderscripts.ScriptIntrinsicConvolve3x3)&ndash;將 3x3 顏色矩陣應用於分配。

- [腳本內在 Convolve5x5](xref:Android.Renderscripts.ScriptIntrinsicConvolve5x5)&ndash;將 5x5 顏色矩陣應用於分配。

- [腳本內生直方圖](xref:Android.Renderscripts.ScriptIntrinsicHistogram)&ndash;一個內在直方圖濾波器。

- [腳本內在LUT](xref:Android.Renderscripts.ScriptIntrinsicLUT)&ndash;將每個通道查找表應用於緩衝區。

- [腳本內部重新調整](xref:Android.Renderscripts.ScriptIntrinsicResize)&ndash;腳本,用於執行 2D 分配的調整大小。

- [腳本內部YuvToRGB](xref:Android.Renderscripts.ScriptIntrinsicYuvToRGB)&ndash;將YUV緩衝區轉換為RGB。

有關每個內部腳本的詳細資訊,請參閱 API 文檔。

接下來將介紹在 Android 應用程式中使用 Renderscript 的基本步驟。

**建立成像文稿**&ndash;[`Renderscript`](xref:Android.Renderscripts.RenderScript)
類是圍繞 Renderscript 上下文的託管包裝,將控制初始化、資源管理和清理。 Renderscript 物件是`RenderScript.Create`使用 工廠方法創建的,該方法將 Android 上下文(如活動)作為參數。 以下代碼行示範如何初始化呈現文稿內容:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**創建分配**&ndash;根據內部腳本,可能需要創建一個或`Allocation`兩個。 的[`Android.Renderscripts.Allocation`](xref:Android.Renderscripts.Allocation)
類有幾個工廠方法來幫助實例化對內部項的分配。 例如,以下代碼段演示如何為位圖創建分配。

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

通常,需要創建一個`Allocation`來保存腳本的輸出數據。 以下程式碼段展示如何`Allocation.CreateTyped`使用 說明器實體與原始類型相同的秒: `Allocation`

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**實例化腳本包裝每個**&ndash;內部腳本包裝器類都應具有幫助器方法(通常`Create`稱為 ),用於實例化該腳本的包裝物件。 以下代碼段是如何實例化`ScriptIntrinsicBlur`模糊物件的範例。 `Element.U8_4`說明器方法將建立一個元素,該元素描述一個資料類型,該資料類型為 4 個字段,包含 8 位元、未`Bitmap`符號整數值 ,適合儲存物件的資料:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**分配分配、設置參數、&运行脚本**&ndash;`Script`類提供了`ForEach`實際 運行呈現腳本的方法。 此方法將反覆運算保存輸入數據`Element`中的`Allocation`每個數據。 在某些情況下,可能需要提供儲存輸出的`Allocation`。
`ForEach`將覆蓋輸出分配的內容。 要繼續執行上述步驟中的代碼段,此範例展示如何分配輸入分配、設定參數,然後最終執行文稿(將結果複製到輸出配置):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

您可能希望查看[具有渲染腳本配方的模糊圖像](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript),這是如何在 Xamarin.Android 中使用內部腳本的完整示例。

## <a name="summary"></a>總結

本指南介紹了 Renderscript 以及如何在 Xamarin.Android 應用程式中使用它。 它簡要討論了什麼是 Renderscript,以及它在 Android 應用程式中的工作方式。 它描述了 Renderscript 中的一些關鍵元件以及_使用者腳本_和_系統腳本_之間的區別。 最後,本指南討論了在 Xamarin.Android 應用程式中使用內部腳本的步驟。

## <a name="related-links"></a>相關連結

- [安卓.渲染腳本命名空間](xref:Android.Renderscripts)
- [使用成像文稿模糊影像](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [教學:開始使用呈現文稿](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)

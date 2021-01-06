---
title: Xamarin.Forms 快速轉譯器
description: 本文介紹快速轉譯器，藉 Xamarin.Forms 由壓平合併產生的原生控制項階層，減少 Android 上控制項的擴大和轉譯成本。
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c9d3b023acc3c38bf1ad056a140145680fbbba10
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939793"
---
# <a name="no-locxamarinforms-fast-renderers"></a>Xamarin.Forms 快速轉譯器

傳統上，大部分的 Android 原始控制項轉譯器都是由兩個觀點組成：

- 原生控制項，例如 `Button` 或 `TextView` 。
- `ViewGroup`處理部分版面配置工作、手勢處理和其他工作的容器。

不過，這種方法的效能暗示在於每個邏輯控制項都要建立兩個視圖，這會產生較複雜的視覺化樹狀結構，需要更多的記憶體，並且在螢幕上呈現更多處理。

快速轉譯器會將控制項的膨脹和轉譯成本縮減為 Xamarin.Forms 單一視圖。 因此，我們不會建立兩個視圖，並將其加入至視圖樹狀結構中，只會建立一個。 這會藉由建立較少的物件來改善效能，而這也是較不復雜的視圖樹狀結構，而且記憶體使用量較少 (也會導致較少的垃圾收集暫停) 。

Android 上的下列控制項可使用快速轉譯器 Xamarin.Forms ：

- [`Button`](xref:Xamarin.Forms.Button)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)

在功能上，這些快速轉譯器與舊版轉譯器並無不同。 從 Xamarin.Forms 4.0 開始，以預設為目標的所有應用程式 `FormsAppCompatActivity` 都會使用這些快速轉譯器。 所有新控制項的轉譯器（包括 [`ImageButton`](xref:Xamarin.Forms.ImageButton) 和 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ）都會使用快速轉譯器方法。

使用快速轉譯器時的效能改善會因每個應用程式而異，視配置的複雜度而定。 例如，當您滾動 [`ListView`](xref:Xamarin.Forms.ListView) 包含上千個數據列的資料時，可以改善 x2 的效能，其中每個資料列中的資料格是由使用快速轉譯器的控制項所組成，如此可讓滾動更平滑。

> [!NOTE]
> 您可以使用針對舊版轉譯器所使用的相同方法來建立快速轉譯器的自訂轉譯器。 如需詳細資訊，請參閱[自訂呈現方式](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

## <a name="backwards-compatibility"></a>回溯相容性

您可以使用下列方法來覆寫快速轉譯器：

1. 在呼叫之前，將下列程式程式碼新增至您的類別，以啟用舊版轉譯器 `MainActivity` `Forms.Init` ：

    ```csharp
    Forms.SetFlags("UseLegacyRenderers");
    ```

1. 使用以舊版轉譯器為目標的自訂轉譯器。 任何現有的自訂轉譯器都會繼續與舊版轉譯器搭配運作。
1. 指定不同的 `View.Visual` ，例如 `Material` 使用不同的轉譯器。 如需材質視覺效果的詳細資訊，請參閱[ Xamarin.Forms 材質視覺效果](~/xamarin-forms/user-interface/visual/material-visual.md)。

## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)

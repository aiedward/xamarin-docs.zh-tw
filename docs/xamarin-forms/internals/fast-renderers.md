---
title: Xamarin.Forms快速轉譯器
description: 本文介紹快速轉譯器，藉由簡維 Xamarin.Forms 產生的原生控制項階層，減少 Android 上控制項的擴大和呈現成本。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3f25f4c2da5b2a426673b49045b5d2d05b0c6ac4
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139070"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.Forms快速轉譯器

傳統上，大部分的 Android 原始控制項轉譯器都是由兩個視圖所組成：

- 原生控制項，例如 `Button` 或 `TextView` 。
- 容器 `ViewGroup` ，可處理一些版面配置工作、手勢處理和其他工作。

不過，這種方法的效能隱含，因為每個邏輯控制項都會建立兩個視圖，這會產生更複雜的視覺化樹狀結構，需要更多的記憶體，以及更多處理以在螢幕上呈現。

快速轉譯器會將控制項的擴大和呈現成本降 Xamarin.Forms 到單一視圖中。 因此，不會建立兩個視圖並將它們加入至視圖樹狀結構，而是只會建立一個。 這會藉由建立較少的物件來改善效能，這又表示較不復雜的視圖樹狀結構和較少的記憶體使用（這也會導致較少的垃圾收集暫停）。

快速轉譯器適用于 Android 上的下列控制項 Xamarin.Forms ：

- [`Button`](xref:Xamarin.Forms.Button)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`Frame`](xref:Xamarin.Forms.Frame)

就功能而言，這些快速轉譯器與舊版轉譯器並無不同。 從 Xamarin.Forms 4.0 開始，所有以為目標的應用程式 `FormsAppCompatActivity` 預設都會使用這些快速轉譯器。 所有新控制項的轉譯器（包括 [`ImageButton`](xref:Xamarin.Forms.ImageButton) 和 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ）都會使用快速轉譯器方法。

針對每個應用程式使用快速轉譯器時的效能改進，會因配置的複雜性而異。 例如，在 [`ListView`](xref:Xamarin.Forms.ListView) 包含數千個數據列時，可以改善 x2 的效能，其中每個資料列中的儲存格是由使用快速轉譯器的控制項所組成，這會導致更平滑的滾動。

> [!NOTE]
> 您可以使用舊版轉譯器所使用的相同方法，來建立快速轉譯器的自訂轉譯器。 如需詳細資訊，請參閱[自訂呈現方式](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

## <a name="backwards-compatibility"></a>回溯相容性

您可以使用下列方法來覆寫快速轉譯器：

1. 在呼叫之前，將下列程式碼新增至類別，以啟用舊版轉譯器 `MainActivity` `Forms.Init` ：

    ```csharp
    Forms.SetFlags("UseLegacyRenderers");
    ```

1. 使用目標為舊版轉譯器的自訂轉譯器。 任何現有的自訂轉譯器都會繼續與舊版轉譯器搭配運作。
1. 指定 `View.Visual` 使用不同轉譯器的不同（例如 `Material` ）。 如需材質視覺效果的詳細資訊，請參閱[ Xamarin.Forms 材質視覺效果](~/xamarin-forms/user-interface/visual/material-visual.md)。

## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)

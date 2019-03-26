---
title: Xamarin.Forms 快速轉譯器
description: 這篇文章介紹透過壓平所產生的原生控制項階層減少膨脹和轉譯成本 Xamarin.Forms 控制項在 Android 上的快速轉譯器。
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 9a40644df6abcbbcc327b1b0c2dcb26c2dbc4db5
ms.sourcegitcommit: 247a6d00a95fd7f4cf918d923e5f357c8db56761
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58420206"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.Forms 快速轉譯器

![預覽](~/media/shared/preview.png)

_這篇文章介紹快速轉譯器 （新增 Xamarin.Forms 2.4），可減少膨脹和轉譯成本 Xamarin.Forms 控制項在 Android 上透過壓平所產生的原生控制項階層。_

傳統上，大多在 Android 上的原始控制項轉譯器是由兩個檢視組成：

- 原生控制項，例如`Button`或`TextView`。
- 容器`ViewGroup`可處理一些版面配置工作、 筆勢處理和其他工作。

不過，這種方法都有效能含意，兩個檢視會針對每個邏輯控制項，會導致更複雜的視覺化樹狀結構，需要更多的記憶體和更多的處理螢幕上呈現。

快速轉譯器會減少膨脹和轉譯成本 Xamarin.Forms 控制項到單一檢視。 因此，而不被建立兩個檢視，並將它們新增至檢視樹狀目錄中，會建立只有一個。 這可改善效能，藉由建立較少的物件，這表示較不複雜的檢視樹狀目錄，並降低記憶體使用量 （這也會導致較少記憶體回收暫停）。

快速轉譯器可供在 Android 上 Xamarin.Forms 2.4 中的下列控制項：

- [`Button`](xref:Xamarin.Forms.Button)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`Frame`](xref:Xamarin.Forms.Frame)

在功能上，這些快速轉譯器是沒有不同，原始的轉譯器。 不過，目前在實驗，只用於藉由將下列這一行的程式碼，以您`MainActivity`類別，然後再呼叫`Forms.Init`:

```csharp
Forms.SetFlags("FastRenderers_Experimental");
```

> [!NOTE]
> 讓前應用程式相容性的活動，將會忽略此設定，都只適用於應用程式相容性 Android 後端，快速轉譯器。

每個應用程式，取決於配置的複雜度而定會有不同的效能改進。 比方說，效能改善的 x2 進行捲動時[ `ListView` ](xref:Xamarin.Forms.ListView)包含數千個資料列的資料，其中每個資料列中的資料格由使用快速轉譯器的控制項，這會導致明顯較平滑的捲動。


## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)

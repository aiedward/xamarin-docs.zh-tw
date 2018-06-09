---
title: Xamarin.Forms 快速轉譯器
description: 本文介紹快速轉譯器，減少所產生的原生控制項階層簡維擴大和 Xamarin.Forms 控制項在 Android 上的轉譯成本。
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 40cc095da41aaae5cb474987d8b03f7cf4a17322
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243057"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.Forms 快速轉譯器

_本文介紹快速轉譯器，減少所產生的原生控制項階層簡維擴大和 Xamarin.Forms 控制項在 Android 上的轉譯成本。_

傳統上，大部分的原始在 Android 上的控制項轉譯器所組成兩個檢視：

- 原生控制項，例如`Button`或`TextView`。
- 容器`ViewGroup`可處理一些版面配置工作、 軌跡處理和其他工作。

不過，這個方法有效能含意，兩個檢視會針對每個邏輯的控制項，導致更複雜的視覺化樹狀結構需要更多的記憶體和更多的處理來呈現在螢幕上所建立的。

快速的轉譯器會減少擴大和 Xamarin.Forms 控制項的轉譯成本為單一檢視。 因此，而不被建立兩個檢視，並將它們加入至檢視樹狀目錄，會建立只有一個。 藉由建立較少的物件，而表示較不複雜的檢視樹狀目錄，和較低的記憶體使用 （這也會導致較少記憶體回收暫停），這可改善效能。

快速的轉譯器可供 Xamarin.Forms 2.4 中在 Android 上的下列控制項：

- [`Button`](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)
- [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)
- [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)

在功能上，這些快速的轉譯器的相同到原始的轉譯器。 不過，是目前實驗，只用於透過將下列這一行的程式碼加入您`MainActivity`類別，然後再呼叫`Forms.Init`:

```csharp
Forms.SetFlags("FastRenderers_Experimental");
```

> [!NOTE]
> 因此會忽略此設定前應用程式相容性的活動，都只適用於應用程式相容 Android 後端，快速轉譯器。

效能改進會每個應用程式，根據配置的複雜度而有所不同。 例如，效能會提升 x2 可能會捲動時[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)包含數千個資料列，其中每個資料列中的資料格的控制項，使用快速的轉譯器進行，這會導致明顯地平滑捲動。


## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)

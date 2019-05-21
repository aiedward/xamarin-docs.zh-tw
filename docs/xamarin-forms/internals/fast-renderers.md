---
title: Xamarin.Forms 快速轉譯器
description: 這篇文章介紹透過壓平所產生的原生控制項階層減少膨脹和轉譯成本 Xamarin.Forms 控制項在 Android 上的快速轉譯器。
ms.prod: xamarin
ms.assetid: 097f87f2-d891-4f3c-be02-fb7d195a481a
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 861d9e3f898dcd61015d9aca27ae66c3fe72d1a9
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970715"
---
# <a name="xamarinforms-fast-renderers"></a>Xamarin.Forms 快速轉譯器

傳統上，大多在 Android 上的原始控制項轉譯器是由兩個檢視組成：

- 原生控制項，例如`Button`或`TextView`。
- 容器`ViewGroup`可處理一些版面配置工作、 筆勢處理和其他工作。

不過，這種方法都有效能含意，兩個檢視會針對每個邏輯控制項，會導致更複雜的視覺化樹狀結構，需要更多的記憶體和更多的處理螢幕上呈現。

快速轉譯器會減少膨脹和轉譯成本 Xamarin.Forms 控制項到單一檢視。 因此，而不被建立兩個檢視，並將它們新增至檢視樹狀目錄中，會建立只有一個。 這可改善效能，藉由建立較少的物件，這表示較不複雜的檢視樹狀目錄，並降低記憶體使用量 （這也會導致較少記憶體回收暫停）。

快速轉譯器可供在 Android 上在 Xamarin.Forms 中的下列控制項：

- [`Button`](xref:Xamarin.Forms.Button)
- [`Image`](xref:Xamarin.Forms.Image)
- [`Label`](xref:Xamarin.Forms.Label)
- [`Frame`](xref:Xamarin.Forms.Frame)

在功能上，這些快速轉譯器是沒有不同，舊版的轉譯器。 Xamarin.Forms 4.0 及更新版本，為目標的所有應用程式從`FormsAppCompatActivity`預設會使用這些快速轉譯器。 轉譯器，針對所有新的控制項，包括[ `ImageButton` ](xref:Xamarin.Forms.ImageButton)並[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)，使用快速轉譯器的方法。

使用快速轉譯器會每個應用程式，視版面配置的複雜度而有所不同時的效能改進。 比方說，效能改善的 x2 進行捲動時[ `ListView` ](xref:Xamarin.Forms.ListView)包含數千個資料列的資料，其中每個資料列中的資料格由使用快速轉譯器的控制項，這會導致明顯較平滑的捲動。

> [!NOTE]
> 快速轉譯器所使用的相同的方法用於舊版的轉譯器可以建立自訂轉譯器。 如需詳細資訊，請參閱[自訂呈現方式](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

## <a name="backwards-compatibility"></a>回溯相容性

快速轉譯器都可以使用下列方法覆寫：

1. 藉由將下列這一行程式碼來啟用舊版的轉譯器您`MainActivity`類別，然後再呼叫`Forms.Init`:

    ```csharp
    Forms.SetFlags("UseLegacyRenderers");
    ```

1. 使用舊版的轉譯器為目標的自訂轉譯器。 任何現有的自訂轉譯器會繼續使用舊版的轉譯器函式。
1. 指定不同`View.Visual`，例如`Material`，使用不同的轉譯器。 如需資料視覺化的詳細資訊，請參閱[Xamarin.Forms 資料視覺化](~/xamarin-forms/user-interface/visual/material-visual.md)。

## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)

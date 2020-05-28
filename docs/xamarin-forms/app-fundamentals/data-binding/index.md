---
title: Xamarin.Forms資料系結
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4b99877342ddbaf9ad5606ec5dc284818fcfeffd
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139694"
---
# <a name="xamarinforms-data-binding"></a>Xamarin.Forms資料系結

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_資料系結是連結兩個物件屬性的技術，因此某個屬性中的變更會自動反映在另一個屬性中。資料系結是模型 ViewModel （MVVM）應用程式架構不可或缺的一部分。_

## <a name="the-data-linking-problem"></a>資料連結問題

Xamarin.Forms應用程式是由一或多個頁面所組成，其中每一頁通常包含多個稱為*views*的使用者介面物件。 程式的主要工作之一是讓這些檢視保持同步，並追蹤其所代表的不同值或選取項目。 檢視通常代表來自基礎資料來源的值，而使用者操作這些檢視來變更該資料。 檢視變更時，基礎資料必須反映該變更；同樣地，當基礎資料變更時，該變更必須反映在檢視中。

若要成功處理這項作業，程式必須收到在這些檢視或基礎資料中進行變更的通知。 定義在變更發生時發出訊號的事件，是常見的解決方案。 因此可安裝會收到這些變更通知的事件處理常式。 事件處理常式藉由將資料從某個物件傳輸到另一個來回應。 不過，當存在許多檢視時，也必須要有許多事件處理常式，會牽涉到大量程式碼。

## <a name="the-data-binding-solution"></a>資料繫結解決方案

資料繫結會自動執行此作業，並轉譯不必要的事件處理常式。 資料繫結可透過程式碼或 XAML 來實作，而 XAML 較為普遍，因其可協助降低程式碼後置檔案的大小。 使用宣告式程式碼或標記來取代事件處理常式中的程序性程式碼，可簡化並釐清應用程式。

涉及資料繫結的兩個物件之一，幾乎都是衍生自 `View` 的元素，並會形成頁面視覺化介面的一部分。 另一個物件是：

- 另一個 `View` 的衍生項目，通常在相同頁面上。
- 或是程式碼檔案中的物件。

在示範程式 (例如在 [**DataBindingDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) 範例中的程式) 中，為清楚和簡單起見，通常會顯示兩個 `View` 衍生項目之間的資料繫結。 不過，相同準則也可以套用至 `View` 和其他物件之間的資料繫結。 使用模型 ViewModel （MVVM）架構來建立應用程式時，具有基礎資料的類別通常稱為「ViewModel」。

您可以在下列一系列文章中探索資料繫結：

## <a name="basic-bindings"></a>[基本繫結](basic-bindings.md)

了解資料繫結目標與來源之間的差異，並查看程式碼和 XAML 中的簡單資料繫結。

## <a name="binding-mode"></a>[繫結模式](binding-mode.md)

探索繫結模式可如何控制兩個物件之間的資料流程。

## <a name="string-formatting"></a>[字串格式化](string-formatting.md)

使用資料繫結進行格式化，並將物件顯示為字串。

## <a name="binding-path"></a>[繫結路徑](binding-path.md)

深入探索資料繫結的 `Path` 屬性，以存取子屬性和集合成員。

## <a name="binding-value-converters"></a>[繫結值轉換器](converters.md)

使用繫結值轉換器來改變資料繫結中的值。

## <a name="relative-bindings"></a>[相對繫結](relative-bindings.md)

使用相對系結，將系結來源設定為相對於系結目標的位置。

## <a name="binding-fallbacks"></a>[繫結後援](binding-fallbacks.md)

定義繫結程序失敗時要使用的後援值，讓資料繫結更穩固。

## <a name="the-command-interface"></a>[命令介面](commanding.md)

以資料繫結實作 `Command` 屬性。

## <a name="compiled-bindings"></a>[編譯的繫結](compiled-bindings.md)

使用編譯的繫結來改善資料繫結效能。

## <a name="related-links"></a>相關連結

- [Data Binding Demos (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (資料繫結示範 (範例))
- [書籍中的資料系結章節 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)

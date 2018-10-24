---
title: Xamarin.Forms 資料繫結
description: 資料繫結是連結的兩個物件的屬性，使其中一個屬性的變更會自動反映在另一個屬性的技術。 資料繫結是 Model View ViewModel (MVVM) 應用程式架構不可或缺的一部分。
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: 60bf0bdc5f1a4472dfd12424c3cc0375d3f34c24
ms.sourcegitcommit: 11c1df7594064e4e141470e092e55cc50c138068
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2018
ms.locfileid: "35240349"
---
# <a name="xamarinforms-data-binding"></a>Xamarin.Forms 資料繫結

_資料繫結是連結的兩個物件的屬性，使其中一個屬性的變更會自動反映在另一個屬性的技術。資料繫結是 Model View ViewModel (MVVM) 應用程式架構不可或缺的一部分。_

## <a name="the-data-linking-problem"></a>資料連結問題

Xamarin.Forms 應用程式包含一或多個頁面，其中每個通常包含多個呼叫的使用者介面物件*檢視*。 其中一個程式的主要工作是要保持同步處理，這些檢視並來追蹤不同的值或其所代表的選取項目。 檢視通常代表值從基礎資料來源，及使用者操作這些檢視來變更該資料。 檢視變更時，基礎資料必須反映這項變更，而且同樣地，當基礎資料變更時，該變更必須反映檢視中。

若要成功地處理這項作業，程式必須在這些檢視或基礎資料中的變更通知。 常見的解決方法是定義事件發出訊號變更時發生。 事件處理常式進行安裝，會收到這些變更的通知。 它會回應將資料從某個物件轉移到另一個。 不過，許多檢視時，必須也有許多的事件處理常式，並取得牽涉到大量程式碼。

## <a name="the-data-binding-solution"></a>資料繫結解決方案

資料繫結會自動執行此作業，並呈現不必要的事件處理常式。 （事件是仍有必要，不過，因為資料繫結基礎結構會使用它們）。可以實作資料繫結，程式碼或 XAML，但是更為普遍的 XAML，這些資訊可協助減少程式碼後置檔案的大小。 使用宣告式程式碼或標記中取代事件處理常式中的程序性程式碼，是簡化應用程式，並將其釐清中。

其中一個參與資料繫結的兩個物件幾乎都是衍生自項目`View`和形成頁面的視覺介面的一部分。 另一個物件是：

- 另一個`View`衍生，通常在相同頁面上。
- 程式碼檔案中的物件。

在示範程式，例如中[ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)範例，兩個資料繫結`View`衍生項目通常會顯示為目的清楚明瞭。 不過，相同的原則可以套用至資料繫結之間`View`和其他物件。 使用 Model View ViewModel (MVVM) 架構來建置應用程式時，與基礎資料的類別通常稱為 ViewModel。

資料繫結會在下面一系列文章中探索：

## <a name="basic-bindingsbasic-bindingsmd"></a>[基本繫結](basic-bindings.md)

了解資料繫結目標與來源之間的差異，請參閱中的程式碼和 XAML 的簡單資料繫結。

## <a name="binding-modebinding-modemd"></a>[繫結模式](binding-mode.md)

探索如何繫結模式可以控制兩個物件之間的資料流程。

## <a name="string-formattingstring-formattingmd"></a>[字串格式化](string-formatting.md)

使用資料繫結進行格式化並顯示為字串的物件。

## <a name="binding-pathbinding-pathmd"></a>[繫結路徑](binding-path.md)

深入探索`Path`資料繫結來存取子的屬性和集合成員的屬性。

## <a name="binding-value-convertersconvertersmd"></a>[繫結值轉換器](converters.md)

您可以使用繫結值轉換器來改變資料繫結中的值。

## <a name="binding-fallbacksbinding-fallbacksmd"></a>[繫結後援](binding-fallbacks.md)

定義要使用的繫結程序失敗時的後援值，讓資料繫結更穩固。

## <a name="the-command-interfacecommandingmd"></a>[命令介面](commanding.md)

實作`Command`以資料繫結的屬性。

## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)

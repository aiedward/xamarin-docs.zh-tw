---
title: Xamarin.Forms 資料繫結
description: 資料繫結是連結兩個物件的屬性，使其中一個屬性的變更會自動反映在另一個屬性的技巧。 資料繫結是模型-檢視-ViewModel (MVVM) 應用程式架構中不可或缺的一部分。
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: a5ea5dcb5b108da52634f131fd36a91ba82f7da4
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240349"
---
# <a name="xamarinforms-data-binding"></a>Xamarin.Forms 資料繫結

_資料繫結是連結兩個物件的屬性，使其中一個屬性的變更會自動反映在另一個屬性的技巧。資料繫結是模型-檢視-ViewModel (MVVM) 應用程式架構中不可或缺的一部分。_

## <a name="the-data-linking-problem"></a>資料連結問題

Xamarin.Forms 應用程式包含一個或多個頁面，其中每個通常包含多個呼叫的使用者介面物件*檢視*。 其中一個程式的主要工作是要讓同步處理，這些檢視，並以不同的值或它們所代表的選取項目追蹤。 檢視通常代表值從基礎資料來源，以及使用者操控這些檢視來變更該資料。 當檢視變更時，基礎資料必須反映該變更，而且同樣地，當基礎資料變更時，該變更必須反映檢視中。

若要成功地處理這項作業，程式必須在這些檢視或基礎資料中的變更告知。 常見的解決方法是定義發生變更時發出信號的事件。 事件處理常式進行安裝，會收到這些變更的通知。 它的回應將資料從一個物件轉移到另一個。 不過，許多檢視時，必須也有許多的事件處理常式，並取得涉及大量程式碼。

## <a name="the-data-binding-solution"></a>資料繫結解決方案

資料繫結會自動執行這項作業，並呈現不必要的事件處理常式。 （事件是仍有必要，不過，因為資料繫結基礎結構會使用它們）。資料繫結可以實作程式碼或 XAML 中，但更常見在 XAML 中，這些資訊可協助減少程式碼後置檔案的大小。 事件處理常式中的程序性程式碼取代宣告式程式碼或標記，應用程式是簡化，並且已釐清。

其中涉及的資料繫結的兩個物件幾乎都是衍生自項目`View`和頁面的視覺介面的一部分。 另一個物件是：

- 另一個`View`衍生，通常是在相同頁面上。
- 程式碼檔案中的物件。

在示範的程式，例如中[ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)範例中，資料繫結兩個`View`衍生項目通常會顯示基於的清晰度和簡單性。 不過，相同的原則可以套用至資料繫結之間`View`和其他物件。 應用程式建置時使用的模型-檢視-ViewModel (MVVM) 架構，與基礎資料類別通常稱為 ViewModel。

資料繫結會在下面一系列文章中瀏覽：

## <a name="basic-bindingsbasic-bindingsmd"></a>[基本繫結](basic-bindings.md)

了解資料繫結目標與來源之間的差異，請參閱程式碼和 XAML 中的簡單資料繫結。

## <a name="binding-modebinding-modemd"></a>[繫結模式](binding-mode.md)

探索如何繫結模式可以控制兩個物件之間的資料流程。

## <a name="string-formattingstring-formattingmd"></a>[字串格式化](string-formatting.md)

用於格式化並以字串形式顯示物件的資料繫結。

## <a name="binding-pathbinding-pathmd"></a>[繫結路徑](binding-path.md)

到更深入的探討`Path`屬性存取子的屬性和集合成員的資料繫結。

## <a name="binding-value-convertersconvertersmd"></a>[繫結值轉換器](converters.md)

您可以使用繫結值轉換器來變更資料繫結中的值。

## <a name="the-command-interfacecommandingmd"></a>[命令介面](commanding.md)

實作`Command`具有資料繫結屬性。



## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)

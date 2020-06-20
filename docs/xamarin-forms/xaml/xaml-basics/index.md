---
title: Xamarin.FormsXAML 基本概念
description: 本指南說明如何開始使用行動裝置的跨平臺 XAML。 XAML 可讓開發人員 Xamarin.Forms 使用標記而非程式碼，在應用程式中定義使用者介面。
ms.prod: xamarin
ms.custom: video
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8f5f69038f7d5cfe7ad46f013acb1519c4e9569f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138615"
---
# <a name="xamarinforms-xaml-basics"></a>Xamarin.FormsXAML 基本概念

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

可延伸應用程式標記語言（XAML）是由 Microsoft 建立的 XML 語言，做為程式碼的替代方案，用於將物件具現化及初始化，以及在父子式階層中組織這些物件。 XAML 已針對 .NET framework 中的數個技術進行調整，但它在定義 Windows Presentation Foundation （WPF）、Silverlight、Windows 執行階段和通用 Windows 平臺（UWP）內的使用者介面配置時，找到了其最大的公用程式。

XAML 可讓開發人員 Xamarin.Forms 使用標記而非程式碼，在應用程式中定義使用者介面。 程式中絕對不需要 XAML Xamarin.Forms ，但它通常比對等程式碼更簡潔且更具視覺效果，而且可能會 toolable。 XAML 非常適合搭配熱門的 MVVM （模型-View-ViewModel）應用程式架構使用： XAML 會定義透過 XAML 型資料系結連結至 ViewModel 程式碼的視圖。

在 XAML 檔案中， Xamarin.Forms 開發人員可以使用所有的 Xamarin.Forms 視圖、版面配置和頁面，以及自訂類別來定義使用者介面。 XAML 檔案可以在可執行檔中編譯或內嵌。 不論是哪一種方式，XAML 資訊都會在組建時間進行剖析，以找出已命名的物件，並在執行時間再次具現化和初始化物件，以及在這些物件和程式設計程式碼之間建立連結。

XAML 與對等程式碼有數個優點：

- XAML 通常會比對等程式碼更簡潔且更容易閱讀。
- XML 中固有的父子式階層可讓 XAML 以更清楚明瞭的方式模擬使用者介面物件的父子式階層。
- XAML 可以輕鬆地由程式設計人員撰寫，但也會讓它本身 toolable，並由視覺化設計工具產生。

還有一些缺點，主要與標記語言內建的限制有關：

- XAML 不能包含程式碼。 所有事件處理常式都必須在程式碼檔中定義。
- XAML 不能包含重複處理的迴圈。 （不過，有幾個 Xamarin.Forms 視覺物件 [`ListView`](xref:Xamarin.Forms.ListView) 可以根據其集合中的物件產生多個子系 `ItemsSource` ）。
- XAML 不能包含條件式處理（不過，資料系結可以參考以程式碼為基礎的系結轉換器，可有效地允許某些條件式處理）。
- XAML 通常無法具現化未定義無參數的函式的類別。 （不過，有時候會有一種方法會圍繞此限制）。
- XAML 通常無法呼叫方法。 （同樣地，這種限制有時可以克服）。

尚未提供在應用程式中產生 XAML 的視覺化設計工具 Xamarin.Forms 。 所有 XAML 都必須手動撰寫，但有[Xaml 預覽](~/xamarin-forms/xaml/xaml-previewer/index.md)器。 XAML 的新程式設計人員可能會想要經常建立及執行其應用程式，特別是在任何可能不正確的專案之後。 即使是在 XAML 中具有大量經驗的開發人員，也知道測試是有益的。

XAML 基本上是 XML，但 XAML 具有一些獨特的語法功能。 最重要的是：

- Property 元素
- 附加屬性
- 標記延伸

這些功能*不*是 XML 延伸模組。 XAML 完全是合法的 XML。 但是，這些 XAML 語法功能以獨特的方式使用 XML。 下列文章將詳細討論它們，其中最後會介紹如何使用 XAML 來執行 MVVM。

## <a name="requirements"></a>需求

本文假設您已熟悉 Xamarin.Forms 。 本文也假設您已熟悉 XML，包括瞭解 XML 命名空間宣告的用法，以及詞彙*元素*、*標記*和*屬性*。

熟悉 Xamarin.Forms 和 XML 之後，請開始閱讀[第1部分。使用 XAML 消費者入門](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)。

## <a name="related-links"></a>相關連結

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [建立 Mobile Apps 書籍](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Xamarin.Forms範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-UI-with-XAML-5-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]

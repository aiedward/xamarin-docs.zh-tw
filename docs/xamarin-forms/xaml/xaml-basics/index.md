---
title: Xamarin.Forms XAML 基本概念
description: 本指南說明如何開始使用適用于行動裝置的跨平臺 XAML。 XAML 可讓開發人員 Xamarin.Forms 使用標記而非程式碼，在應用程式中定義使用者介面。
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
ms.openlocfilehash: 3d2620014c9554712c746b98de3b9a0eeaa20864
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562752"
---
# <a name="no-locxamarinforms-xaml-basics"></a>Xamarin.Forms XAML 基本概念

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

 (XAML) 的可延伸應用程式標記語言，是 Microsoft 所建立的 XML 架構語言，可用於將物件具現化和初始化，以及在父子式階層中組織這些物件。 XAML 已經調整為 .NET framework 中的數種技術，但它已找到其最大的公用程式，可在 Windows Presentation Foundation (WPF) 、Silverlight、Windows 執行階段和通用 Windows 平臺 (UWP) 中定義使用者介面的版面配置。

XAML 可讓開發人員 Xamarin.Forms 使用標記而非程式碼，在應用程式中定義使用者介面。 程式中永遠不需要 XAML Xamarin.Forms ，但通常會比對等程式碼更簡潔且更具視覺效果，而且可能會 toolable。 XAML 非常適合搭配熱門 MVVM (模型 ViewModel) 應用程式架構使用： XAML 定義了透過以 XAML 為基礎的資料系結連結至 ViewModel 程式碼的視圖。

在 XAML 檔案中， Xamarin.Forms 開發人員可以使用所有的 Xamarin.Forms 視圖、版面配置和頁面，以及自訂類別來定義使用者介面。 XAML 檔案可以編譯或內嵌在可執行檔中。 無論是哪一種方式，都會在組建階段剖析 XAML 資訊以找出命名物件，並在執行時間再次設定以具現化和初始化物件，以及在這些物件與程式設計程式碼之間建立連結。

XAML 具有比對等程式碼更多的優點：

- XAML 通常比對等程式碼更簡潔且更容易閱讀。
- XML 中的父子式階層可讓 XAML 以更清楚的視覺效果清楚地模仿使用者介面物件的父子式階層。
- XAML 很容易由程式設計人員撰寫，但也可供視覺化設計工具 toolable 和產生。

另外還有一些缺點，大多與標記語言內建的限制有關：

- XAML 不能包含程式碼。 所有事件處理常式都必須定義在程式碼檔案中。
- XAML 不能包含重複處理的迴圈。  (不過，有幾個 Xamarin.Forms 視覺物件  [`ListView`](xref:Xamarin.Forms.ListView) 可以根據集合中的物件來產生多個子系 `ItemsSource` 。 ) 
- XAML 不能包含條件式處理 (不過，資料系結可以參考以程式碼為基礎的系結轉換器，以有效地允許某些條件式處理。 ) 
- XAML 通常無法具現化未定義無參數函式的類別。  (不過，有時候這項限制會有一種方法。 ) 
- XAML 通常無法呼叫方法。  (，有時可能會克服此限制。 ) 

尚未有可在應用程式中產生 XAML 的視覺化設計工具 Xamarin.Forms 。 所有 XAML 都必須手動寫入，但是有一個 [Xaml 預覽](~/xamarin-forms/xaml/xaml-previewer/index.md)程式。 XAML 的新程式設計人員可能會想要經常建立和執行其應用程式，特別是在可能不正確的任何情況下。 即使是在 XAML 中有很多經驗的開發人員都知道，測試也會獲得獎勵。

XAML 基本上是 XML，但 XAML 有一些獨特的語法功能。 最重要的是：

- Property 元素
- 附加屬性
- 標記延伸

這些功能 *不* 是 XML 延伸模組。 XAML 是完全合法的 XML。 但是這些 XAML 語法功能以獨特的方式使用 XML。 下列文章將詳細討論這些詳細資料，其中最後有使用 XAML 來實現 MVVM 的簡介。

## <a name="requirements"></a>需求

本文假設您已熟悉使用 Xamarin.Forms 。 本文也假設您已熟悉 XML，包括瞭解 XML 命名空間宣告的使用方式，以及詞彙 *元素*、 *標記*和 *屬性*。

當您熟悉 Xamarin.Forms 和 XML 時，請先閱讀 [第1部分。使用 XAML 消費者入門](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)。

## <a name="related-links"></a>相關連結

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [建立 Mobile Apps 書籍](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Xamarin.Forms 樣品](/samples/browse/?products=xamarin&term=Xamarin.Forms)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-UI-with-XAML-5-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
---
title: Xamarin.Forms XAML 基本知識
description: 本指南說明如何開始使用跨平台行動裝置的 XAML。 XAML 可讓開發人員在 Xamarin.Forms 應用程式中使用標記，而不是程式碼定義使用者介面。
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: bd6013a0fc1d4f3dfc2d863860052310fed55e4d
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292138"
---
# <a name="xamarinforms-xaml-basics"></a>Xamarin.Forms XAML 基本知識

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)

XAML (eXtensible Application Markup Language) 讓開發人員可以在 Xamarin.Forms 應用程式中使用標記定義使用者介面，而不是使用程式碼。 XAML 永遠不需要在 Xamarin.Forms 程式中，但它通常會較簡潔和呈現更一致的比對等的程式碼，而且可能靈活。 XAML 是非常適合用於搭配熱門的 MVVM (模型-檢視-ViewModel) 應用程式架構：XAML 會定義連結到 ViewModel 的程式碼透過以 XAML 為基礎的資料繫結的檢視。

## <a name="xaml-basics-contents"></a>XAML 基本內容

* [概觀](#Overview)
* [第 1 部分：開始使用 XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
* [第 2 部分：基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
* [第 3 部分：XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
* [第 4 部分：資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
* [第 5 部分：從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

除了這些 XAML 基本知識文章中，您可以下載一書的章節[使用 Xamarin.Forms 建立行動應用程式](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "書封面")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

中的活頁簿，許多章節深入涵蓋 XAML 主題包括：

<table style="border:0px; box-shadow:0 0px 0px" cellpadding="0" cellspacing="2" border="0" width="85%">
<tr style="background:#ecf0f1">
  <td style="border:0px;">
    <h4>第 7 章。 XAML vs。程式碼</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf">下載 PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md">摘要</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>第 8 章。 程式碼和 XAML 搭配</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf">下載 PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter08.md">摘要</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>第 10 章。 XAML 標記延伸</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf">下載 PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md">摘要</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>第 18 章。 MVVM</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf">下載 PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md">摘要</a></td></tr>
</table>

可以是這些章節[免費下載](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)。

<a name="Overview" />

## <a name="overview"></a>總覽

XAML 是由 Microsoft 所建立的具現化並初始化物件，並組織父子式階層中的這些物件的程式碼的替代方式為 XML 架構語言。 XAML 有數種技術，在.NET framework 中，已調整，但發現其最大的公用程式中定義的 Windows Presentation Foundation (WPF)、 Silverlight、 Windows 執行階段和通用 Windows 內的使用者介面版面配置平台 (UWP)。

XAML 也是一部分的跨平台原生型程式設計介面適用於 iOS、 Android 和 UWP 的 Xamarin.Forms 行動裝置。 在 XAML 檔案中，Xamarin.Forms 開發人員可以定義使用所有 Xamarin.Forms 檢視、 配置和頁數，以及自訂類別的使用者介面。 XAML 檔案可以編譯或內嵌在可執行檔。 無論如何，在建置階段找出已命名的物件，並一次在執行階段具現化並初始化物件，以及建立這些物件和程式碼之間的連結，會剖析 XAML 資訊。

XAML 會具有對等程式碼的幾項優點：

-  XAML 通常會較簡潔、 比對等的程式碼可讀性更高。
-  在 XML 中固有的父子式階層可讓 XAML 來模擬與較高的視覺化清晰度父子式階層的使用者介面物件。
-  XAML 可以輕鬆地手動撰寫程式設計師，但也十分靈活且由視覺化設計工具產生。

當然，也有缺點，大部分與相關的標記語言內建函式的限制：

-  XAML 不能包含程式碼。 所有事件處理常式必須在程式碼檔案中都定義。
-  XAML 不能包含重複的處理迴圈。 (不過，有幾個 Xamarin.Forms 視覺物件，最值得注意的是[ `ListView` ](xref:Xamarin.Forms.ListView) — 可以產生多個中的物件為基礎的子系其`ItemsSource`集合。)
-  XAML 不能包含條件式處理 （不過，資料繫結可以參考實際上可讓某些條件式處理的程式碼為基礎的繫結轉換子。）
-  XAML 通常無法具現化未定義的無參數建構函式的類別。 （不過，沒有有時什麼方法可以解決這項限制。）
-  XAML 通常無法呼叫方法。 （同樣地，這項限制有時是可以克服的。）

尚未有視覺化設計工具來產生 XAML 在 Xamarin.Forms 應用程式。 所有 XAML 都必須手動撰寫，但沒有[XAML 預覽程式](~/xamarin-forms/xaml/xaml-previewer.md)。 新增程式設計人員以 XAML 可能會想要經常建置和執行應用程式，特別是在任何可能不是很明顯地正確的項目之後。 透過眾多經驗，在 XAML 中的開發人員知道測試會獎勵。

基本上，XAML 就是 XML，但 XAML 有一些獨特的語法功能。 最重要的是：

- Property 項目
- 附加的屬性
- 標記延伸

這些功能都*不*XML 延伸模組。 XAML 是完全合法的 XML。 但這些 XAML 語法功能獨特的方式來使用 XML。 它們會詳細討論下列文章中的以此實作 MVVM 使用 XAML 的簡介。

## <a name="requirements"></a>需求

本文假設 Xamarin.Forms 工作熟悉。 本文也假設 XML，包括了解使用方式的 XML 命名空間宣告和詞彙疐裾*項目*，*標記*，並*屬性*。

當您熟悉使用 Xamarin.Forms 和 XML 時，開始讀取[第 1 部分。開始使用 XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)。

## <a name="related-links"></a>相關連結

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [建立 Mobile Apps 課本](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)

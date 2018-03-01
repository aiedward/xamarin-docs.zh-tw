---
title: "Xamarin.Forms XAML 基本概念"
description: "開始使用行動裝置的跨平台標記"
ms.topic: article
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: a3f3dbbe0f12cfa7cc1fc6606ec8bd48a96e407c
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-xaml-basics"></a>Xamarin.Forms XAML 基本概念

XAML — 可延伸的應用程式標記語言，可讓開發人員定義的使用者介面，在 Xamarin.Forms 應用程式中使用標記，而不是程式碼。 XAML 永遠不需要在 Xamarin.Forms 程式中，但更簡潔和比對等的程式碼，更視覺化的方式一致和潛在 toolable。 XAML 是特別適合搭配熱門 MVVM (模型-檢視-ViewModel) 應用程式架構： XAML 定義的檢視，透過以 XAML 為基礎的資料繫結連結到 ViewModel 程式碼。

## <a name="xaml-basics-contents"></a>XAML 的基本內容

* [概觀](#Overview)
* [第 1 部分。開始使用 XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
* [第 2 部分。基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
* [第 3 部分。XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
* [第 4 部分。資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
* [第 5 部分。從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

除了這些基本 XAML 概念文件中，您可以下載活頁簿的章節[建立行動應用程式使用 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "封面")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

活頁簿，許多章節中的更深入討論 XAML 主題包括：

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
    <h4>第 8 章。 程式碼和 XAML 中互聯</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf">下載 PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter08.md">摘要</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>章 10。 XAML 標記延伸</h4>
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

XAML 是以 XML 為基礎的語言由 Microsoft 建立替代程式碼來具現化並初始化物件，及組織父子式階層中的物件。 XAML 具有已適用於數種技術，在.NET framework 中，但發現其最大的公用程式中定義的 Windows Presentation Foundation (WPF)、 Silverlight、 Windows 執行階段和通用 Windows 內的使用者介面版面配置平台 (UWP)。

XAML 也是透過 Xamarin.Forms 的跨平台原生型程式設計介面 iOS、 Android 和 UWP 部分行動裝置。 Xamarin.Forms 開發人員可以在 XAML 檔案後，定義使用者介面使用所有 Xamarin.Forms 檢視、 配置和頁面，也為自訂類別。 XAML 檔案可以編譯或內嵌在可執行檔。 不管哪種方式，在建置階段找出具名的物件，並一次在執行階段具現化，並初始化物件，以及建立這些物件和程式碼之間的連結，會剖析 XAML 資訊。

XAML 中有了許多優於同等程式碼：

-  XAML 來得更簡潔易懂比對等程式碼。
-  在 XML 中固有的父子式階層可讓具有較高的視覺化清晰度模仿父子式階層的使用者介面物件的 XAML。
-  XAML 可以輕鬆地手寫的程式設計人員，但也借用本身為 toolable 和視覺化設計工具所產生。

當然，也有缺點，這些大部分與相關都內建到標記語言的限制：

-  XAML 中不能包含程式碼。 所有事件處理常式必須都定義在程式碼檔案。
-  XAML 中不能包含重複處理的迴圈。 (不過，有幾個 Xamarin.Forms visual 物件 — 最值得注意的是[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) — 可以產生多個中的物件為基礎的子系其`ItemsSource`集合。)
-  XAML 中不能包含條件式處理 （不過，資料繫結可以參考程式碼為基礎的繫結轉換子，有效地讓某些條件式處理。）
-  XAML 通常無法具現化未定義的無參數建構函式的類別。 （不過，沒有有時解決這項限制的方式。）
-  XAML 通常無法呼叫方法。 （同樣地，這項限制可以有時克服。）

沒有任何尚未視覺化設計工具來產生 XAML Xamarin.Forms 應用程式中。 所有 XAML 必須都是手動撰寫，但是沒有[XAML 預覽程式](~/xamarin-forms/xaml/xaml-previewer.md)。 程式設計人員的新 XAML 可能想要經常建置並執行應用程式，特別是在任何卻不一定很明顯地正確的項目之後。 甚至在 XAML 中的體驗的許多開發人員知道薪水試驗。

XAML 基本上是 XML，但 XAML 有一些獨特的語法功能。 最重要的是：

- 屬性項目
- 附加的屬性
- 標記延伸

這些功能*不*XML 延伸模組。 XAML 是完全合法的 XML。 但這些 XAML 語法功能獨特的方式來使用 XML。 它們會詳細討論以下文章中的以此實作 MVVM 使用 XAML 的簡介。

## <a name="requirements"></a>需求

本文章假設工作熟悉 Xamarin.Forms。 讀取[Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)強烈建議。

本文也假設疐裾 XML，包括了解使用方式的 XML 命名空間宣告及條款*元素*，*標記*，和*屬性*。

當您熟悉 Xamarin.Forms 和 XML 時，開始讀取[第 1 部分。開始使用 XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)。



## <a name="related-links"></a>相關連結

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Xamarin.Forms 簡介](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [建立行動應用程式的活頁簿](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Xamarin.Forms 範例](https://developer.xamarin.com/samples/xamarin-forms/all/)

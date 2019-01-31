---
title: Windows 平台功能
description: 這篇文章說明可在 Xamarin.Forms 中的 Windows 平台支援。
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: f14ea52e419ae5d639319f49ae4779de6a2eed31
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292177"
---
# <a name="windows-platform-features"></a>Windows 平台功能

開發適用於 Windows 平台 Xamarin.Forms 應用程式需要 Visual Studio。 [需求頁面](~/get-started/installation.md)包含必要條件的詳細資訊。

![](images/allhanselman.png "Windows 上執行的 Xamarin.Forms 應用程式")

## <a name="platform-specifics"></a>平台特性

平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。

下列平台特有的功能被供 Xamarin.Forms 檢視、 頁面和配置在通用 Windows 平台 (UWP):

- 設定存取金鑰[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 < [VisualElement 存取金鑰，在 Windows 上](#visualelement-accesskeys)。
- 停用上支援的舊版的色彩模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 < [VisualElement 舊版色彩模式在 Windows 上](#legacy-color-mode)。

為 UWP 的 Xamarin.Forms 檢視提供了下列平台特有的功能：

- 正在偵測讀取順序，從文字中的內容[ `Entry` ](xref:Xamarin.Forms.Entry)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Label` ](xref:Xamarin.Forms.Label)執行個體。 如需詳細資訊，請參閱 < [InputView 在 Windows 上的讀取順序](#inputview-readingorder)。
- 啟用在點選 筆勢支援[ `ListView` ](xref:Xamarin.Forms.ListView)。 如需詳細資訊，請參閱 <<c0> [ 在 Windows 上的 ListView SelectionMode](#listview-selectionmode)。
- 啟用[ `SearchBar` ](xref:Xamarin.Forms.SearchBar)與拼字檢查引擎互動。 如需詳細資訊，請參閱 < [SearchBar 拼字檢查 Windows 上](#searchbar-spellcheck)。
- 啟用[ `WebView` ](xref:Xamarin.Forms.WebView) UWP 訊息對話方塊中顯示 JavaScript 的警示。 如需詳細資訊，請參閱 <<c0> [ 在 Windows 上的 WebView JavaScript 警示](#webview-javascript-alert)。

在 UWP 的 Xamarin.Forms 頁面提供下列平台特有的功能：

- 摺疊[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)導覽列中。 如需詳細資訊，請參閱 < [MasterDetailPage 導覽列在 Windows 上](#collapsable_navigation_bar)。
- 設定工具列的放置選項。 如需詳細資訊，請參閱 <<c0> [ 在 Windows 上的頁面工具列位置](#toolbar_placement)。
- 啟用顯示在頁面圖示[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)工具列。 如需詳細資訊，請參閱 < [TabbedPage 圖示，在 Windows 上](#tabbedpage-icons)。

## <a name="platform-support"></a>平台支援

Visual Studio 中可用的 Xamarin.Forms 範本包含通用 Windows 平台 (UWP) 專案。

> [!NOTE]
> 支援的 Xamarin.Forms 1.x 和 2.x _Windows Phone 8 Silverlight_， _Windows Phone 8.1_，並_Windows 8.1_應用程式開發。 不過，這些專案類型已被取代。

## <a name="getting-started"></a>使用者入門

移至**檔案 > 新增 > 專案**Visual Studio 中選擇其中一個**跨平台 > 空白應用程式 (Xamarin.Forms)** 範本開始著手。

舊的 Xamarin.Forms 方案，或在 macOS 上，建立不會有上面所列的所有 Windows 專案 （但它們必須以手動方式加入）。 如果您想要為目標的 Windows 平台已經不在您的方案，vist[安裝指示](installation/index.md)新增所需的 Windows 專案類型。

## <a name="samples"></a>範例

[所有樣本](https://github.com/xamarin/xamarin-forms-book-preview-2)Charles Petzold 的書籍[*使用 Xamarin.Forms 建立行動應用程式*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)包含通用 Windows 平台 （適用於 Windows 10) 專案。

["Scott Hanselman 」 示範應用程式](https://github.com/jamesmontemagno/Hanselman.Forms)分開，而且也包含 Apple Watch 和 Android Wear 的專案 （分別使用 Xamarin.iOS 和 Xamarin.Android，Xamarin.Forms 不會執行這些平台）。

## <a name="related-links"></a>相關連結

- [安裝 Windows 專案](~/xamarin-forms/platform/windows/installation/index.md)

---
title: Windows 平臺功能
description: 本文說明 Xamarin 中提供的 Windows 平臺支援。
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: 0e2db2a054c871668b5787a53ffbe4464f982174
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696923"
---
# <a name="windows-platform-features"></a>Windows 平臺功能

開發適用于 Windows 平臺的 Xamarin. Forms 應用程式需要 Visual Studio。 [[需求] 頁面](~/get-started/requirements.md)包含必要元件的詳細資訊。

![](images/allhanselman.png "Xamarin.Forms Applications Running on Windows")

## <a name="platform-specifics"></a>平臺細節

平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。

下列平臺特定功能適用于通用 Windows 平臺（UWP）上的 Xamarin、表單檢視、頁面和版面配置：

- 設定[`VisualElement`](xref:Xamarin.Forms.VisualElement)的存取金鑰。 如需詳細資訊，請參閱[Windows 上的 VisualElement 存取金鑰](visualelement-access-keys.md)。
- 在支援的[`VisualElement`](xref:Xamarin.Forms.VisualElement)上停用舊版色彩模式。 如需詳細資訊，請參閱[在 Windows 上 VisualElement 舊版色彩模式](legacy-color-mode.md)。

下列平臺特定功能是針對 UWP 提供的表單檢視：

- 從[`Entry`](xref:Xamarin.Forms.Entry)、 [`Editor`](xref:Xamarin.Forms.Editor)和[`Label`](xref:Xamarin.Forms.Label)實例中的文字內容偵測讀取順序。 如需詳細資訊，請參閱[InputView Windows 上的讀取順序](inputview-reading-order.md)。
- 在[`ListView`](xref:Xamarin.Forms.ListView)中啟用攻點手勢支援。 如需詳細資訊，請參閱[Windows 上的 ListView SelectionMode](listview-selectionmode.md)。
- 啟用要變更 `RefreshView` 的提取方向。 如需詳細資訊，請參閱[Windows 上的 RefreshView 提取方向](refreshview-pulldirection.md)。
- 啟用[`SearchBar`](xref:Xamarin.Forms.SearchBar)與拼寫檢查引擎互動。 如需詳細資訊，請參閱[Windows 上的搜尋列拼寫檢查](searchbar-spell-check.md)。
- 讓[`WebView`](xref:Xamarin.Forms.WebView)在 UWP 訊息對話方塊中顯示 JavaScript 警示。 如需詳細資訊，請參閱[在 Windows 上進行 web 程式的 JavaScript 警示](webview-javascript-alert.md)。

下列平臺特定功能是針對 UWP 上的 Xamarin. Forms 頁面所提供：

- 折迭[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)巡覽列。 如需詳細資訊，請參閱[MasterDetailPage Windows 上的巡覽列](masterdetailpage-navigation-bar.md)。
- 設定工具列位置選項。 如需詳細資訊，請參閱[Windows 上的頁面工具列位置](page-toolbar-placement.md)。
- 啟用要在[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)工具列上顯示的頁面圖示。 如需詳細資訊，請參閱[TabbedPage Windows 上的圖示](tabbedpage-icons.md)。

## <a name="platform-support"></a>平台支援

Visual Studio 中提供的 Xamarin 範本包含通用 Windows 平臺（UWP）專案。

> [!NOTE]
> Xamarin. x 和2.x 支援_Windows Phone 8 Silverlight_、 _Windows Phone 8.1_，以及_Windows 8.1_應用程式開發。 不過，這些專案類型已被取代。

## <a name="getting-started"></a>使用者入門

移至 [檔案] > Visual Studio 中的**新 > 專案**，然後選擇其中一個**跨平臺 > 空白應用程式（Xamarin）** 範本以開始使用。

舊版的 Xamarin 或在 macOS 上建立的方案，將不會有上述所有 Windows 專案（但必須手動新增）。 如果您想要做為目標的 Windows 平臺尚未存在於您的解決方案中，請造訪[設定指示](installation/index.md)，以新增所需的 windows 專案類型/s。

## <a name="samples"></a>範例

Charles Petzold 本書的[所有範例都](https://github.com/xamarin/xamarin-forms-book-preview-2)是[*使用 Xamarin 建立 Mobile Apps*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) ，包括通用 Windows 平臺（適用于 Windows 10）專案。

「 [Scott Hanselman」示範應用程式](https://github.com/jamesmontemagno/Hanselman.Forms)可個別使用，而且也包含 Apple Watch 和 android 磨損專案（分別使用 Xamarin 和 xamarin，而 xamarin 則不會在這些平臺上執行）。

## <a name="related-links"></a>相關連結

- [設定 Windows 專案](~/xamarin-forms/platform/windows/installation/index.md)

---
title: Windows 平臺功能
description: 本文說明提供的 Windows 平臺支援 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 52246ce5d54ba97e91777f598f25c187901f89c4
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137770"
---
# <a name="windows-platform-features"></a>Windows 平臺功能

開發 Xamarin.Forms 適用于 Windows 平臺的應用程式需要 Visual Studio。 [[支援的平臺] 頁面](~/get-started/supported-platforms.md)包含必要元件的詳細資訊。

![](images/allhanselman.png "Xamarin.Forms Applications Running on Windows")

## <a name="platform-specifics"></a>平台特定功能

平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。

Xamarin.Forms通用 Windows 平臺（UWP）上的視圖、頁面和版面配置提供下列平臺特定功能：

- 設定的存取金鑰 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 如需詳細資訊，請參閱[Windows 上的 VisualElement 存取金鑰](visualelement-access-keys.md)。
- 在支援的上停用舊版色彩模式 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 如需詳細資訊，請參閱[在 Windows 上 VisualElement 舊版色彩模式](legacy-color-mode.md)。

以下是針對 UWP 上的 views 提供的平臺特定功能 Xamarin.Forms ：

- 偵測 [`Entry`](xref:Xamarin.Forms.Entry) 、和實例中文字內容的讀取順序 [`Editor`](xref:Xamarin.Forms.Editor) [`Label`](xref:Xamarin.Forms.Label) 。 如需詳細資訊，請參閱[InputView Windows 上的讀取順序](inputview-reading-order.md)。
- 在中啟用點按手勢支援 [`ListView`](xref:Xamarin.Forms.ListView) 。 如需詳細資訊，請參閱[Windows 上的 ListView SelectionMode](listview-selectionmode.md)。
- 啟用要變更的提取方向 `RefreshView` 。 如需詳細資訊，請參閱[Windows 上的 RefreshView 提取方向](refreshview-pulldirection.md)。
- 啟用 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 以與拼寫檢查引擎互動。 如需詳細資訊，請參閱[Windows 上的搜尋列拼寫檢查](searchbar-spell-check.md)。
- 啟用 [`WebView`](xref:Xamarin.Forms.WebView) 以在 UWP 訊息對話方塊中顯示 JavaScript 警示。 如需詳細資訊，請參閱[在 Windows 上進行 web 程式的 JavaScript 警示](webview-javascript-alert.md)。

以下是針對 UWP 上的頁面提供的平臺特定功能 Xamarin.Forms ：

- 折迭 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 導覽列。 如需詳細資訊，請參閱[MasterDetailPage Windows 上的巡覽列](masterdetailpage-navigation-bar.md)。
- 設定工具列位置選項。 如需詳細資訊，請參閱[Windows 上的頁面工具列位置](page-toolbar-placement.md)。
- 啟用要在工具列上顯示的頁面圖示 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 如需詳細資訊，請參閱[TabbedPage Windows 上的圖示](tabbedpage-icons.md)。

以下是針對 UWP 上的類別提供的平臺特定功能 Xamarin.Forms [`Application`](xref:Xamarin.Forms.Application) ：

- 指定專案中將從中載入影像資產的目錄。 如需詳細資訊，請參閱[Windows 上的預設影像目錄](default-image-directory.md)。

## <a name="platform-support"></a>平台支援

Xamarin.FormsVisual Studio 中提供的範本包含通用 Windows 平臺（UWP）專案。

> [!NOTE]
> Xamarin.Forms1.x 和2.x 支援_Windows Phone 8 Silverlight_、 _Windows Phone 8.1_，以及_Windows 8.1_應用程式開發。 不過，這些專案類型已被取代。

## <a name="getting-started"></a>開始使用

移至 [檔案] > Visual Studio 中的**新 > 專案**，然後選擇其中一個**跨平臺 > 空白應用程式（ Xamarin.Forms ）** 範本以開始使用。

較舊的 Xamarin.Forms 解決方案（或在 macOS 上建立的方案）將不會有上面列出的所有 Windows 專案（但必須手動新增）。 如果您想要做為目標的 Windows 平臺尚未存在於您的解決方案中，請造訪[設定指示](installation/index.md)，以新增所需的 windows 專案類型/s。

## <a name="samples"></a>範例

Charles Petzold 本書的[所有範例都](https://github.com/xamarin/xamarin-forms-book-preview-2)使用[* Xamarin.Forms *](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)包含通用 Windows 平臺（適用于 Windows 10）專案來建立 Mobile Apps。

「 [Scott Hanselman」示範應用程式](https://github.com/jamesmontemagno/Hanselman.Forms)可分別使用，而且也包含 Apple Watch 和 Android 磨損專案（分別使用 Xamarin 和 xamarin），而不 Xamarin.Forms 會在這些平臺上執行。

## <a name="related-links"></a>相關連結

- [設定 Windows 專案](~/xamarin-forms/platform/windows/installation/index.md)

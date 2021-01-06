---
title: Windows 平臺功能
description: 本文說明中提供的 Windows 平臺支援 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d5f65deaad97bd69641fa4cc577d9a476106ac3a
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940352"
---
# <a name="windows-platform-features"></a>Windows 平臺功能

開發 Xamarin.Forms Windows 平臺的應用程式需要 Visual Studio。 [ [支援的平臺] 頁面](~/get-started/supported-platforms.md) 包含先決條件的詳細資訊。

![：：：非 loc (Xamarin. Forms) ：：：在 Windows 上執行的應用程式](images/allhanselman.png)

## <a name="platform-specifics"></a>平台特定功能

平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。

下列平臺特定功能提供給 Xamarin.Forms 通用 Windows 平臺 (UWP) 上的視圖、頁面和配置：

- 設定的存取金鑰 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 如需詳細資訊，請參閱 [Windows 上的 VisualElement 存取金鑰](visualelement-access-keys.md)。
- 在支援的上停用舊版色彩模式 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 如需詳細資訊，請參閱 [在 Windows 上 VisualElement 舊版色彩模式](legacy-color-mode.md)。

以下是針對 UWP 上的觀點提供的平臺特定功能 Xamarin.Forms ：

- 從 [`Entry`](xref:Xamarin.Forms.Entry) 、和實例中的文字內容偵測讀取順序 [`Editor`](xref:Xamarin.Forms.Editor) [`Label`](xref:Xamarin.Forms.Label) 。 如需詳細資訊，請參閱 [Windows 上的 InputView 讀取順序](inputview-reading-order.md)。
- 啟用中的分流手勢支援 [`ListView`](xref:Xamarin.Forms.ListView) 。 如需詳細資訊，請參閱 [Windows 上的 ListView SelectionMode](listview-selectionmode.md)。
- 啟用的提取方向 `RefreshView` 會變更。 如需詳細資訊，請參閱 [Windows 上的 Refreshview 拖動提取方向](refreshview-pulldirection.md)。
- 啟用 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 以與拼寫檢查引擎進行互動。 如需詳細資訊，請參閱 [SearchBar Windows 的拼寫檢查](searchbar-spell-check.md)。
- 設定用來 [`WebView`](xref:Xamarin.Forms.WebView) 裝載其內容的執行緒。 如需詳細資訊，請參閱 [Windows 上的 web 程式執行模式](webview-executionmode.md)。
- 啟用 [`WebView`](xref:Xamarin.Forms.WebView) 以顯示 UWP 訊息對話方塊中的 JavaScript 警示。 如需詳細資訊，請參閱 [在 Windows 上將 JavaScript 警示進行 web](webview-javascript-alert.md)程式。

下列平臺特定功能是針對 Xamarin.Forms UWP 上的頁面所提供：

- 折迭 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) 巡覽列。 如需詳細資訊，請參閱 [Windows 上的 FlyoutPage 巡覽列](flyoutpage-navigation-bar.md)。
- 設定工具列位置選項。 如需詳細資訊，請參閱 [Windows 上的頁面工具列放置](page-toolbar-placement.md)。
- 啟用要在工具列上顯示的頁面圖示 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 如需詳細資訊，請參閱 [Windows 上的 TabbedPage 圖示](tabbedpage-icons.md)。

UWP 上的類別會提供下列平臺特定功能 Xamarin.Forms [`Application`](xref:Xamarin.Forms.Application) ：

- 在專案中指定將載入影像資產的目錄。 如需詳細資訊，請參閱 [Windows 上的預設映射目錄](default-image-directory.md)。

## <a name="platform-support"></a>平台支援

Xamarin.FormsVisual Studio 中提供的範本包含通用 Windows 平臺 (UWP) 專案。

> [!NOTE]
> Xamarin.Forms 1.x 和2.x 支援 _Windows Phone 8 Silverlight_、 _Windows Phone 8.1_ 和 _Windows 8.1_ 應用程式開發。 不過，這些專案類型已被取代。

## <a name="getting-started"></a>開始使用

移至 Visual Studio 中的 [檔案] **> 新的 > 專案** ，然後選擇其中一個 **跨平臺 > 空白應用程式 (Xamarin.Forms)** 範本以開始使用。

舊版 Xamarin.Forms 解決方案或在 macOS 上建立的方案，將不會有上述所有的 Windows 專案 (但需要以手動方式新增) 。 如果您想要設為目標的 Windows 平臺尚未存在於您的方案中，請流覽 [安裝指示](installation/index.md) ，以新增所需的 windows 專案類型/s。

## <a name="samples"></a>範例

Charles Petzold 的 [所有範例都會](https://github.com/xamarin/xamarin-forms-book-preview-2) [*Xamarin.Forms 建立 Mobile Apps，其中*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)包含 Windows 10) 專案的通用 Windows 平臺 (。

「 [Scott Hanselman」示範應用程式](https://github.com/jamesmontemagno/Hanselman.Forms) 是分開提供的，也包括使用 Xamarin 和 xamarin (的 Apple Watch 和 android 磨損專案，不 Xamarin.Forms 會在這些平臺上執行) 。

## <a name="related-links"></a>相關連結

- [設定 Windows 專案](~/xamarin-forms/platform/windows/installation/index.md)

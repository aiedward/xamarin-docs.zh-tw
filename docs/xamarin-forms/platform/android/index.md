---
title: Android 平台功能
description: 本文說明如何將 Android 特有的功能新增至 Xamarin. Forms 應用程式。
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
ms.openlocfilehash: 73e838b3a63132230cf594a3461c9d7ee6f302b8
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696943"
---
# <a name="android-platform-features"></a>Android 平台功能

開發適用于 Android 的 Xamarin. Forms 應用程式需要 Visual Studio。 [[需求] 頁面](~/get-started/requirements.md)包含必要元件的詳細資訊。

## <a name="platform-specifics"></a>平臺細節

平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。

下列平臺特定功能是針對在 Android 上的表單檢視、頁面和版面配置而提供的：

- 控制視覺元素的迭置順序，以決定繪製順序。 如需詳細資訊，請參閱[Android 上的 VisualElement 提升許可權](visualelement-elevation.md)。
- 在支援的[`VisualElement`](xref:Xamarin.Forms.VisualElement)上停用舊版色彩模式。 如需詳細資訊，請參閱[在 Android 上 VisualElement 舊版色彩模式](legacy-color-mode.md)。

下列平臺特定功能適用于在 Android 上的表單檢視：

- 使用 Android 按鈕的預設填補和陰影值。 如需詳細資訊，請參閱[Android 上的按鈕填補和陰影](button-padding-shadow.md)。
- 針對[`Entry`](xref:Xamarin.Forms.Entry)的螢幕小鍵盤設定 [輸入法] 編輯器選項。 如需詳細資訊，請參閱[Android 上的專案輸入方法編輯器選項](entry-ime-options.md)。
- 在 `ImageButton` 上啟用投影。 如需詳細資訊，請參閱[Android 上的 ImageButton 投影](imagebutton-drop-shadow.md)。
- 啟用[`ListView`](xref:Xamarin.Forms.ListView)中的快速滾動如需詳細資訊，請參閱[在 Android 上進行 ListView 快速滾動](listview-fast-scrolling.md)。
- 控制[`WebView`](xref:Xamarin.Forms.WebView)是否可以顯示混合內容。 如需詳細資訊，請參閱[在 Android 上進行 web](webview-mixed-content.md)程式的混合式內容。
- 啟用[`WebView`](xref:Xamarin.Forms.WebView)的縮放。 如需詳細資訊，請參閱[Android 上的 web 程式顯示縮放](webview-zoom-controls.md)。

下列平臺特定功能適用于 Android 上的 Xamarin. 表單資料格：

- 啟用[`ViewCell`](xref:Xamarin.Forms.ViewCell)內容動作舊版模式，以便在[`ListView`](xref:Xamarin.Forms.ListView)中選取的專案變更時，不會更新內容動作功能表。 如需詳細資訊，請參閱[Android 上的 ViewCell 內容動作](viewcell-context-actions.md)。

下列平臺特定功能是針對 Android 上的 Xamarin. Forms 頁面所提供：

- 在[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)上設定導覽列的高度。 如需詳細資訊，請參閱[Android 上的 NavigationPage 列高度](navigationpage-bar-height.md)。
- 流覽[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)中的頁面時，停用轉換動畫。 如需詳細資訊，請參閱[Android 上的 TabbedPage 頁面轉換動畫](tabbedpage-transition-animations.md)。
- 在[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)的頁面之間啟用輕刷。 如需詳細資訊，請參閱[TabbedPage Page 刷 On Android](tabbedpage-page-swiping.md)。
- 在[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)上設定工具列位置和色彩。 如需詳細資訊，請參閱[TabbedPage 工具列位置和色彩 On Android](tabbedpage-toolbar-placement-color.md)。

下列平臺特定功能是針對 Android 上的 Xamarin. Forms [`Application`](xref:Xamarin.Forms.Application)類別提供的：

- 設定軟鍵盤的操作模式。 如需詳細資訊，請參閱[Android 上的軟鍵盤輸入模式](soft-keyboard-input-mode.md)。
- 針對使用 AppCompat 的應用程式，分別停用 [暫停] 和 [繼續] 的[`Disappearing`](xref:Xamarin.Forms.Page.Appearing)和[`Appearing`](xref:Xamarin.Forms.Page.Appearing)頁面生命週期事件。 如需詳細資訊，請參閱[Android 上的頁面生命週期事件](page-lifecycle-events.md)。

## <a name="platform-support"></a>平台支援

原本，預設的 Xamarin Android 專案使用舊版的控制項轉譯，這在 Android 5.0 之前是常見的。 使用範本建立的應用程式具有 `FormsApplicationActivity` 做為其主要活動的基類。

## <a name="material-design-via-appcompat"></a>透過 AppCompat 的材質設計

Xamarin： Android 專案現在會使用 `FormsAppCompatActivity` 做為其主要活動的基類。 此類別會使用 Android 所提供的**AppCompat**功能來執行材質設計主題。

若要將材質設計主題新增至您的 Xamarin. 表單 Android 專案，請遵循[AppCompat 支援的安裝指示](appcompat-material-design.md)

以下是預設 `FormsApplicationActivity` 的**Todo**範例：

[![](images/before-appcompat-sml.png "Todo Sample Application Without AppCompat")](images/before-appcompat.png#lightbox "Todo Sample Application Without AppCompat")

在升級專案以使用 `FormsAppCompatActivity` （並新增其他主題資訊）之後，這是相同的程式碼：

[![](images/post-appcompat-sml.png "Todo Sample Application With AppCompat and Theming")](images/post-appcompat.png#lightbox "Todo Sample Application With AppCompat and Theming")

> [!NOTE]
> 使用 `FormsAppCompatActivity` 時，[某些 Android 自訂](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)轉譯器的基類會不同。

## <a name="related-links"></a>相關連結

- [新增材質設計支援](appcompat-material-design.md)

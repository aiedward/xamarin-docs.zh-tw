---
title: Android 平台功能
description: 本文說明如何將 Android 特有的功能新增至 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3045db1248aa16529d4e43b9a8afc97377cfd9cb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128937"
---
# <a name="android-platform-features"></a>Android 平台功能

開發 Xamarin.Forms Android 應用程式需要 Visual Studio。 [[支援的平臺] 頁面](~/get-started/supported-platforms.md)包含必要元件的詳細資訊。

## <a name="platform-specifics"></a>平台特定功能

平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。

以下是針對 Xamarin.Forms Android 上的視圖、頁面和版面配置所提供的平臺特定功能：

- 控制視覺元素的迭置順序，以決定繪製順序。 如需詳細資訊，請參閱[Android 上的 VisualElement 提升許可權](visualelement-elevation.md)。
- 在支援的上停用舊版色彩模式 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 如需詳細資訊，請參閱[在 Android 上 VisualElement 舊版色彩模式](legacy-color-mode.md)。

以下是針對 Android 上的 views 提供的平臺特定功能 Xamarin.Forms ：

- 使用 Android 按鈕的預設填補和陰影值。 如需詳細資訊，請參閱[Android 上的按鈕填補和陰影](button-padding-shadow.md)。
- 為的螢幕小鍵盤設定 [輸入法] 編輯器選項 [`Entry`](xref:Xamarin.Forms.Entry) 。 如需詳細資訊，請參閱[Android 上的專案輸入方法編輯器選項](entry-ime-options.md)。
- 啟用上的投影 `ImageButton` 。 如需詳細資訊，請參閱[Android 上的 ImageButton 投影](imagebutton-drop-shadow.md)。
- 啟用中的快速滾動 [`ListView`](xref:Xamarin.Forms.ListView) 如需詳細資訊，請參閱[在 Android 上進行 ListView 快速滾動](listview-fast-scrolling.md)。
- 控制開啟時所使用的轉換 `SwipeView` 。 如需詳細資訊，請參閱[SwipeView 滑動轉換模式](swipeview-swipetransitionmode.md)。
- 控制是否 [`WebView`](xref:Xamarin.Forms.WebView) 可以顯示混合內容。 如需詳細資訊，請參閱[在 Android 上進行 web](webview-mixed-content.md)程式的混合式內容。
- 啟用 [放大] [`WebView`](xref:Xamarin.Forms.WebView) 。 如需詳細資訊，請參閱[Android 上的 web 程式顯示縮放](webview-zoom-controls.md)。

針對 Android 上的儲存格提供下列平臺特定功能 Xamarin.Forms ：

- 啟用 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 內容動作的舊版模式，如此一來，當中選取的專案變更時，就不會更新內容動作功能表 [`ListView`](xref:Xamarin.Forms.ListView) 。 如需詳細資訊，請參閱[Android 上的 ViewCell 內容動作](viewcell-context-actions.md)。

以下是針對 Android 上的頁面提供的平臺特定功能 Xamarin.Forms ：

- 設定上巡覽列的高度 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 如需詳細資訊，請參閱[Android 上的 NavigationPage 列高度](navigationpage-bar-height.md)。
- 在中流覽頁面時，停用轉換動畫 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 如需詳細資訊，請參閱[Android 上的 TabbedPage 頁面轉換動畫](tabbedpage-transition-animations.md)。
- 在中的頁面之間啟用輕刷 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 如需詳細資訊，請參閱[TabbedPage Page 刷 On Android](tabbedpage-page-swiping.md)。
- 在上設定工具列位置和色彩 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 如需詳細資訊，請參閱[TabbedPage 工具列位置和色彩 On Android](tabbedpage-toolbar-placement-color.md)。

Android 上的類別提供下列平臺特定功能 Xamarin.Forms [`Application`](xref:Xamarin.Forms.Application) ：

- 設定軟鍵盤的操作模式。 如需詳細資訊，請參閱[Android 上的軟鍵盤輸入模式](soft-keyboard-input-mode.md)。
- [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) 針對使用 AppCompat 的應用程式，分別停用暫停和繼續的和頁面生命週期事件。 如需詳細資訊，請參閱[Android 上的頁面生命週期事件](page-lifecycle-events.md)。

## <a name="platform-support"></a>平台支援

一開始，預設的 Xamarin.Forms android 專案會使用 android 5.0 之前常見的舊版控制項轉譯樣式。 使用範本建立的應用程式，具有做 `FormsApplicationActivity` 為其主要活動的基類。

## <a name="material-design-via-appcompat"></a>透過 AppCompat 的材質設計

Xamarin.FormsAndroid 專案現在會使用 `FormsAppCompatActivity` 做為其主要活動的基類。 此類別會使用 Android 所提供的**AppCompat**功能來執行材質設計主題。

若要將材質設計主題新增至 Xamarin.Forms Android 專案，請[遵循 AppCompat 支援的安裝指示](appcompat-material-design.md)

以下是具有預設值的**Todo**範例 `FormsApplicationActivity` ：

[![](images/before-appcompat-sml.png "Todo Sample Application Without AppCompat")](images/before-appcompat.png#lightbox "Todo Sample Application Without AppCompat")

在升級要使用的專案之後，這會是相同的程式碼 `FormsAppCompatActivity` （並新增其他主題資訊）：

[![](images/post-appcompat-sml.png "Todo Sample Application With AppCompat and Theming")](images/post-appcompat.png#lightbox "Todo Sample Application With AppCompat and Theming")

> [!NOTE]
> 使用時 `FormsAppCompatActivity` ，[某些 Android 自訂](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)轉譯器的基類將會不同。

## <a name="androidx-migration"></a>AndroidX 移轉

AndroidX 取代了 Android 支援程式庫。 若要瞭解 AndroidX 以及如何將 Xamarin.Forms 應用程式遷移至使用 AndroidX 程式庫，請參閱[中 Xamarin.Forms 的 AndroidX 遷移](~/xamarin-forms/platform/android/androidx-migration.md)。

## <a name="related-links"></a>相關連結

- [新增材質設計支援](appcompat-material-design.md)

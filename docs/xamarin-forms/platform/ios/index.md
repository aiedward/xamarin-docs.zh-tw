---
title: 中的 iOS 平臺功能Xamarin.Forms
description: 將 iOS 特有的功能新增至 Xamarin.Forms 應用程式。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1008eab6e56be7a235498e01ffd3ea1b27d2bbae
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130165"
---
# <a name="ios-platform-features-in-xamarinforms"></a>中的 iOS 平臺功能Xamarin.Forms

開發 Xamarin.Forms iOS 應用程式需要 Visual Studio。 [[支援的平臺] 頁面](~/get-started/supported-platforms.md)包含必要元件的詳細資訊。

## <a name="platform-specifics"></a>平台特定功能

平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。

Xamarin.FormsIOS 上的視圖、頁面和版面配置提供下列平臺特定功能：

- 對任何的模糊支援 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 如需詳細資訊，請參閱[VisualElement 模糊 On iOS](visualelement-blur.md)。
- 在支援的上停用舊版色彩模式 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 如需詳細資訊，請參閱[在 iOS 上 VisualElement 舊版色彩模式](legacy-color-mode.md)。
- 啟用上的投影 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 如需詳細資訊，請參閱[VisualElement Drop Shadows On iOS](visualelement-drop-shadow.md)。
- 讓 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 物件成為觸控事件的第一個回應程式。 如需詳細資訊，請參閱[VisualElement 第一個回應](visualelement-first-responder.md)。

IOS 上的視圖提供下列平臺特定功能 Xamarin.Forms ：

- 設定 [`Cell`](xref:Xamarin.Forms.Cell) 背景色彩。 如需詳細資訊，請參閱[iOS 上的儲存格背景色彩](cell-background-color.md)。
- 控制何時會在中進行專案選擇 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 。 如需詳細資訊，請參閱[iOS 上的 DatePicker 專案選取](datepicker-selection.md)。
- 藉由調整字型大小，確保輸入的文字元合 [`Entry`](xref:Xamarin.Forms.Entry) 。 如需詳細資訊，請參閱[iOS 上的輸入字型大小](entry-font-size.md)。
- 在中設定游標色彩 [`Entry`](xref:Xamarin.Forms.Entry) 。 如需詳細資訊，請參閱[iOS 上的輸入游標色彩](entry-cursor-color.md)。
- 控制是否要 [`ListView`](xref:Xamarin.Forms.ListView) 在滾動期間浮動標頭儲存格。 如需詳細資訊，請參閱[iOS 上的 ListView 群組標頭樣式](listview-group-header-style.md)。
- 控制更新專案集合時，是否要停用資料列動畫 [`ListView`](xref:Xamarin.Forms.ListView) 。 如需詳細資訊，請參閱[在 iOS 上 ListView 資料列動畫](listview-row-animations.md)。
- 在上設定分隔符號樣式 [`ListView`](xref:Xamarin.Forms.ListView) 。 如需詳細資訊，請參閱[iOS 上的 ListView 分隔符號樣式](listview-separator-style.md)。
- 控制何時會在中進行專案選擇 [`Picker`](xref:Xamarin.Forms.Picker) 。 如需詳細資訊，請參閱[iOS 上的選擇器專案選擇](picker-selection.md)。
- 控制是否 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 有背景。 如需詳細資訊，請參閱[iOS 上的搜尋列樣式](searchbar-style.md)。
- 藉 [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) 由在橫條圖上的位置上點擊 [`Slider`](xref:Xamarin.Forms.Slider) ，而不是拖曳捲動方塊，讓屬性得以設定 `Slider` 。 如需詳細資訊，請參閱[iOS 上的滑杆](slider-thumb.md)。
- 控制開啟時所使用的轉換 `SwipeView` 。 如需詳細資訊，請參閱[SwipeView 滑動轉換模式](swipeview-swipetransitionmode.md)。
- 控制何時會在中進行專案選擇 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 。 如需詳細資訊，請參閱[iOS 上的 TimePicker 專案選取](timepicker-selection.md)。

IOS 上的頁面提供下列平臺特定功能 Xamarin.Forms ：

- 控制是否在顯示主版頁面時，將的詳細資料頁面套用 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 到它。 如需詳細資訊，請參閱[MasterDetailPage Shadow](masterdetailpage-shadow.md)。
- 在上隱藏導覽列分隔符號 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 如需詳細資訊，請參閱[iOS 上的 NavigationPage Bar 分隔符號](navigation-bar-separator.md)。
- 控制巡覽列是否為透明。 如需詳細資訊，請參閱[iOS 上的巡覽列半透明度](navigation-bar-translucent.md)。
- 控制上的狀態列文字色彩是否 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 會調整以符合導覽列的亮度。 如需詳細資訊，請參閱[iOS 上的 NavigationPage Bar 文本色彩模式](status-bar-text-color.md)。
- 控制頁面標題在頁面導覽列中是否顯示為大型標題。 如需詳細資訊，請參閱[iOS 上的大型頁面標題](page-large-title.md)。
- 在上設定主指示器的可見度 [`Page`](xref:Xamarin.Forms.Page) 。 如需詳細資訊，請參閱[iOS 上的主指示器可見度](page-home-indicator.md)。
- 在上設定狀態列可見度 [`Page`](xref:Xamarin.Forms.Page) 。 如需詳細資訊，請參閱[iOS 上的頁面狀態列可見度](page-status-bar-visibility.md)。
- 確保頁面內容位於所有 iOS 裝置都安全的螢幕區域上。 如需詳細資訊，請參閱[iOS 上的安全區域版面配置指南](page-safe-area-layout.md)。
- 設定模式頁面的呈現樣式。 如需詳細資訊，請參閱強制回應[頁面呈現樣式](page-presentation-style.md)。
- 設定上索引標籤欄的半透明度模式 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 如需詳細資訊，請參閱[TabbedPage 半透明 TabBar On iOS](tabbedpage-translucent-tabbar.md)。

IOS 上的版面配置提供下列平臺特定功能 Xamarin.Forms ：

- 控制是否 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 處理觸控手勢，或將它傳遞至其內容。 如需詳細資訊，請參閱[在 iOS 上 ScrollView 內容的觸控](scrollview-content-touches.md)。

IOS 上的類別提供下列平臺特定功能 Xamarin.Forms [`Application`](xref:Xamarin.Forms.Application) ：

- 停用已命名字型大小的協助工具縮放。 如需詳細資訊，請參閱[iOS 上命名的字型大小的協助工具調整](named-font-size-scaling.md)。
- 啟用控制項版面配置，並在主執行緒上執行轉譯更新。 如需詳細資訊，請參閱[iOS 上的主要執行緒控制更新](main-thread-updates-ui.md)。
- [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer)在 [滾動] 視圖中啟用，以使用 [滾動] 視圖來捕捉和共用平移手勢。 如需詳細資訊，請參閱[iOS 上的同時](application-pan-gesture.md)移動流覽手勢辨識。

## <a name="ios-specific-formatting"></a>iOS 特定格式

Xamarin.Forms可設定跨平臺使用者介面樣式和色彩-但還有其他選項可供您使用 iOS 專案中的平臺 Api 來設定 iOS 的主題。

[深入](formatting.md)瞭解如何使用 IOS 特定 api （例如**plist**設定和 API）來格式化使用者介面 `UIAppearance` 。

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>其他 iOS 功能

您可以使用[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器、 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)和[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)，將各種原生功能納入 Xamarin.Forms iOS 應用程式中。

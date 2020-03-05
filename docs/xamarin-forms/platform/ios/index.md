---
title: Xamarin 中的 iOS 平臺功能
description: 將 iOS 特有的功能新增至 Xamarin. Forms 應用程式。
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: 015db40ce983d979109d4cce32c011f8c61a729c
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292545"
---
# <a name="ios-platform-features-in-xamarinforms"></a>Xamarin 中的 iOS 平臺功能

開發適用于 iOS 的 Xamarin 應用程式需要 Visual Studio。 [[支援的平臺] 頁面](~/get-started/supported-platforms.md)包含必要元件的詳細資訊。

## <a name="platform-specifics"></a>平台特定功能

平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。

以下是針對 Xamarin 提供的平臺特定功能：表單檢視、頁面和版面配置。

- 對任何[`VisualElement`](xref:Xamarin.Forms.VisualElement)的模糊支援。 如需詳細資訊，請參閱[VisualElement 模糊 On iOS](visualelement-blur.md)。
- 在支援的[`VisualElement`](xref:Xamarin.Forms.VisualElement)上停用舊版色彩模式。 如需詳細資訊，請參閱[在 iOS 上 VisualElement 舊版色彩模式](legacy-color-mode.md)。
- 在[`VisualElement`](xref:Xamarin.Forms.VisualElement)上啟用投影。 如需詳細資訊，請參閱[VisualElement Drop Shadows On iOS](visualelement-drop-shadow.md)。
- 讓[`VisualElement`](xref:Xamarin.Forms.VisualElement)物件成為觸控事件的第一個回應程式。 如需詳細資訊，請參閱[VisualElement 第一個回應](visualelement-first-responder.md)。

下列平臺特定功能適用于在 iOS 上的 Xamarin. 表單檢視：

- 設定[`Cell`](xref:Xamarin.Forms.Cell)背景色彩。 如需詳細資訊，請參閱[iOS 上的儲存格背景色彩](cell-background-color.md)。
- 控制[`DatePicker`](xref:Xamarin.Forms.DatePicker)中發生專案選取的時機。 如需詳細資訊，請參閱[iOS 上的 DatePicker 專案選取](datepicker-selection.md)。
- 藉由調整字型大小，確保輸入的文字元合[`Entry`](xref:Xamarin.Forms.Entry) 。 如需詳細資訊，請參閱[iOS 上的輸入字型大小](entry-font-size.md)。
- 在[`Entry`](xref:Xamarin.Forms.Entry)中設定游標色彩。 如需詳細資訊，請參閱[iOS 上的輸入游標色彩](entry-cursor-color.md)。
- 控制是否[`ListView`](xref:Xamarin.Forms.ListView)標頭儲存格在滾動期間浮動。 如需詳細資訊，請參閱[iOS 上的 ListView 群組標頭樣式](listview-group-header-style.md)。
- 控制當更新[`ListView`](xref:Xamarin.Forms.ListView)專案集合時，是否要停用資料列動畫。 如需詳細資訊，請參閱[在 iOS 上 ListView 資料列動畫](listview-row-animations.md)。
- 在[`ListView`](xref:Xamarin.Forms.ListView)上設定分隔符號樣式。 如需詳細資訊，請參閱[iOS 上的 ListView 分隔符號樣式](listview-separator-style.md)。
- 控制[`Picker`](xref:Xamarin.Forms.Picker)中發生專案選取的時機。 如需詳細資訊，請參閱[iOS 上的選擇器專案選擇](picker-selection.md)。
- 藉由在 [ [`Slider`](xref:Xamarin.Forms.Slider) ] 列上的位置上點擊，而不是拖曳 `Slider` 捲動方塊，讓[`Slider.Value`](xref:Xamarin.Forms.Slider.Value)屬性得以設定。 如需詳細資訊，請參閱[iOS 上的滑杆](slider-thumb.md)。
- 控制開啟 `SwipeView`時所使用的轉換。 如需詳細資訊，請參閱[SwipeView 滑動轉換模式](swipeview-swipetransitionmode.md)。
- 控制[`TimePicker`](xref:Xamarin.Forms.TimePicker)中發生專案選取的時機。 如需詳細資訊，請參閱[iOS 上的 TimePicker 專案選取](timepicker-selection.md)。

下列平臺特定功能是針對 iOS 上的 Xamarin. Forms 頁面所提供：

- 在[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)上隱藏導覽列分隔符號。 如需詳細資訊，請參閱[iOS 上的 NavigationPage Bar 分隔符號](navigation-bar-separator.md)。
- 控制巡覽列是否為透明。 如需詳細資訊，請參閱[iOS 上的巡覽列半透明度](navigation-bar-translucent.md)。
- 控制[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)上的狀態列文字色彩是否會調整以符合導覽列的亮度。 如需詳細資訊，請參閱[iOS 上的 NavigationPage Bar 文本色彩模式](status-bar-text-color.md)。
- 控制頁面標題在頁面導覽列中是否顯示為大型標題。 如需詳細資訊，請參閱[iOS 上的大型頁面標題](page-large-title.md)。
- 在[`Page`](xref:Xamarin.Forms.Page)上設定主指示器的可見度。 如需詳細資訊，請參閱[iOS 上的主指示器可見度](page-home-indicator.md)。
- 在[`Page`](xref:Xamarin.Forms.Page)上設定狀態列可見度。 如需詳細資訊，請參閱[iOS 上的頁面狀態列可見度](page-status-bar-visibility.md)。
- 確保頁面內容位於所有 iOS 裝置都安全的螢幕區域上。 如需詳細資訊，請參閱[iOS 上的安全區域版面配置指南](page-safe-area-layout.md)。
- 設定模式頁面的呈現樣式。 如需詳細資訊，請參閱強制回應[頁面呈現樣式](page-presentation-style.md)。
- 在[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)上設定索引標籤欄的半透明度模式。 如需詳細資訊，請參閱[TabbedPage 半透明 TabBar On iOS](tabbedpage-translucent-tabbar.md)。

下列平臺特定功能是針對 Xamarin 提供的。表單版面配置在 iOS 上：

- 控制[`ScrollView`](xref:Xamarin.Forms.ScrollView)是否處理觸控手勢，或將其傳遞至其內容。 如需詳細資訊，請參閱[在 iOS 上 ScrollView 內容的觸控](scrollview-content-touches.md)。

IOS 上的 Xamarin [`Application`](xref:Xamarin.Forms.Application)類別提供下列平臺特定功能：

- 停用已命名字型大小的協助工具縮放。 如需詳細資訊，請參閱[iOS 上命名的字型大小的協助工具調整](named-font-size-scaling.md)。
- 啟用控制項版面配置，並在主執行緒上執行轉譯更新。 如需詳細資訊，請參閱[iOS 上的主要執行緒控制更新](main-thread-updates-ui.md)。
- 在 [滾動] 視圖中啟用[`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) ，以使用 [滾動] 視圖來捕捉和共用平移手勢。 如需詳細資訊，請參閱[iOS 上的同時](application-pan-gesture.md)移動流覽手勢辨識。

## <a name="ios-specific-formatting"></a>iOS 特定格式

Xamarin 可讓您設定跨平臺使用者介面樣式和色彩-但還有其他選項可以在 iOS 專案中使用平臺 Api 來設定 iOS 的主題。

[深入](formatting.md)瞭解如何使用 IOS 特定 api （例如**plist**設定和 `UIAppearance` API）來格式化使用者介面。

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>其他 iOS 功能

您可以使用[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器、 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)和[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)，將各種原生功能併入適用于 iOS 的 Xamarin Forms 應用程式中。

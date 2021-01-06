---
title: 中的 iOS 平臺功能 Xamarin.Forms
description: 將 iOS 特定功能新增至 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b9c000f0a30aba0e5ed8cc1d1458355770a26ffc
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940261"
---
# <a name="ios-platform-features-in-no-locxamarinforms"></a>中的 iOS 平臺功能 Xamarin.Forms

開發 Xamarin.Forms iOS 應用程式需要 Visual Studio。 [ [支援的平臺] 頁面](~/get-started/supported-platforms.md) 包含先決條件的詳細資訊。

## <a name="platform-specifics"></a>平台特定功能

平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。

針對 Xamarin.Forms iOS 上的視圖、頁面和版面配置提供下列平臺特定功能：

- 對任何的模糊支援 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 如需詳細資訊，請參閱 [在 iOS 上 VisualElement 模糊](visualelement-blur.md)。
- 在支援的上停用舊版色彩模式 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 如需詳細資訊，請參閱 [VisualElement iOS 上的舊版色彩模式](legacy-color-mode.md)。
- 在上啟用投影 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。 如需詳細資訊，請參閱 [在 iOS 上 VisualElement 陰影](visualelement-drop-shadow.md)。
- 讓 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 物件成為觸控事件的第一個回應者。 如需詳細資訊，請參閱 [VisualElement 第一個回應](visualelement-first-responder.md)程式。

針對 iOS 上的視圖提供下列平臺特定功能 Xamarin.Forms ：

- 設定 [`Cell`](xref:Xamarin.Forms.Cell) 背景色彩。 如需詳細資訊，請參閱 [iOS 上的資料格背景色彩](cell-background-color.md)。
- 控制專案選取何時出現在中 [`DatePicker`](xref:Xamarin.Forms.DatePicker) 。 如需詳細資訊，請參閱 [iOS 上的 DatePicker 專案選取專案](datepicker-selection.md)。
- 藉由調整字型大小，確保輸入的文字元合 [`Entry`](xref:Xamarin.Forms.Entry) 。 如需詳細資訊，請參閱 [iOS 上的專案字型大小](entry-font-size.md)。
- 在中設定資料指標色彩 [`Entry`](xref:Xamarin.Forms.Entry) 。 如需詳細資訊，請參閱 [iOS 上的進入游標色彩](entry-cursor-color.md)。
- 控制 [`ListView`](xref:Xamarin.Forms.ListView) 標頭儲存格在滾動時是否為浮點數。 如需詳細資訊，請參閱 [iOS 上的 ListView 群組標題樣式](listview-group-header-style.md)。
- 控制更新專案集合時是否停用資料列動畫 [`ListView`](xref:Xamarin.Forms.ListView) 。 如需詳細資訊，請參閱 [iOS 上的 ListView 資料列動畫](listview-row-animations.md)。
- 在上設定分隔符號樣式 [`ListView`](xref:Xamarin.Forms.ListView) 。 如需詳細資訊，請參閱 [iOS 上的 ListView 分隔符號樣式](listview-separator-style.md)。
- 控制專案選取何時出現在中 [`Picker`](xref:Xamarin.Forms.Picker) 。 如需詳細資訊，請參閱 [iOS 上的選擇器專案選取專案](picker-selection.md)。
- 控制是否 [`SearchBar`](xref:Xamarin.Forms.SearchBar) 有背景。 如需詳細資訊，請參閱 [iOS 上的 SearchBar 樣式](searchbar-style.md)。
- 藉 [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) 由在橫條圖上點擊位置來設定屬性 [`Slider`](xref:Xamarin.Forms.Slider) ，而不需要拖曳 `Slider` thumb。 如需詳細資訊，請參閱 [滑動軸上的滑杆 Thumb](slider-thumb.md)。
- 控制開啟時使用的轉換 `SwipeView` 。 如需詳細資訊，請參閱 [SwipeView 滑動轉換模式](swipeview-swipetransitionmode.md)。
- 控制專案選取何時出現在中 [`TimePicker`](xref:Xamarin.Forms.TimePicker) 。 如需詳細資訊，請參閱 [iOS 上的 TimePicker 專案選取專案](timepicker-selection.md)。

IOS 上的頁面會提供下列平臺特定功能 Xamarin.Forms ：

- 控制中的 [詳細資料] 頁面是否 [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) 已套用陰影，在顯示飛出視窗頁面時。 如需詳細資訊，請參閱 [FlyoutPage 陰影](flyoutpage-shadow.md)。
- 隱藏上的巡覽列分隔符號 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 如需詳細資訊，請參閱 [iOS 上的 NavigationPage Bar 分隔符號](navigation-bar-separator.md)。
- 控制巡覽列是否為透明。 如需詳細資訊，請參閱 [iOS 上的巡覽列半透明度](navigation-bar-translucent.md)。
- 控制上的狀態列文字色彩是否 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 會調整，以符合巡覽列的亮度。 如需詳細資訊，請參閱 [iOS 上的 NavigationPage Bar 文字色彩模式](status-bar-text-color.md)。
- 控制頁面標題是否顯示為頁面巡覽列中的大型標題。 如需詳細資訊，請參閱 [iOS 上的大型頁面標題](page-large-title.md)。
- 在上設定 home 指標的可見度 [`Page`](xref:Xamarin.Forms.Page) 。 如需詳細資訊，請參閱 [iOS 上的主指標可見度](page-home-indicator.md)。
- 設定上的狀態列可見度 [`Page`](xref:Xamarin.Forms.Page) 。 如需詳細資訊，請參閱 [iOS 上的頁面狀態列可見度](page-status-bar-visibility.md)。
- 確保頁面內容位於螢幕上所有 iOS 裝置都安全的區域。 如需詳細資訊，請參閱 [iOS 上的安全區域版面配置指南](page-safe-area-layout.md)。
- 設定模式頁面的呈現樣式。 如需詳細資訊，請參閱強制回應 [頁面呈現樣式](page-presentation-style.md)。
- 設定上索引標籤列的半透明度模式 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 如需詳細資訊，請參閱 [TabbedPage 在 iOS 上的透明 TabBar](tabbedpage-translucent-tabbar.md)。

以下是針對 iOS 的版面配置提供的平臺特定功能 Xamarin.Forms ：

- 控制是否 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 處理觸控手勢或將其傳遞至其內容。 如需詳細資訊，請參閱 [ScrollView 在 iOS 上的內容觸控](scrollview-content-touches.md)。

IOS 上的類別會提供下列平臺特定功能 Xamarin.Forms [`Application`](xref:Xamarin.Forms.Application) ：

- 停用命名字型大小的協助工具調整。 如需詳細資訊，請參閱 [iOS 上的命名字型大小的協助工具調整](named-font-size-scaling.md)。
- 啟用要在主執行緒上執行的控制項配置和轉譯更新。 如需詳細資訊，請參閱 [iOS 上的主執行緒控制項更新](main-thread-updates-ui.md)。
- [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer)在滾動視圖中啟用，以使用捲軸來捕捉和共用平移手勢。 如需詳細資訊，請參閱 [iOS 上的並行平移手勢](application-pan-gesture.md)辨識。

## <a name="ios-specific-formatting"></a>iOS 特定格式

Xamarin.Forms 可設定跨平臺使用者介面樣式和色彩，但有其他選項可讓您在 iOS 專案中使用平臺 Api 來設定 iOS 的主題。

[深入](formatting.md) 瞭解如何使用 IOS 特定 api 來格式化使用者介面，例如 **plist** 設定和 `UIAppearance` API。

![iOS 主題](images/status-white-sml.png)

## <a name="other-ios-features"></a>其他 iOS 功能

您可以使用 [自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器、 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)和 [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)，將各種原生功能併入 Xamarin.Forms iOS 的應用程式。

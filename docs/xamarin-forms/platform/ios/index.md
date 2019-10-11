---
title: Xamarin 中的 iOS 平臺功能
description: 將 iOS 特有的功能新增至 Xamarin. Forms 應用程式。
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/22/2019
ms.openlocfilehash: c90cfc297914b585403ae84e7dbac11fd6e02836
ms.sourcegitcommit: eb23b7d745d1090376f9def07e0f11cb089494d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170935"
---
# <a name="ios-platform-features-in-xamarinforms"></a>Xamarin 中的 iOS 平臺功能

開發適用于 iOS 的 Xamarin 應用程式需要 Visual Studio。 [[需求] 頁面](~/get-started/requirements.md)包含必要元件的詳細資訊。

## <a name="platform-specifics"></a>平臺細節

平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。

以下是針對 Xamarin 提供的平臺特定功能：表單檢視、頁面和版面配置。

- 模糊的任何支援[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱[VisualElement 模糊 On iOS](visualelement-blur.md)。
- 停用上支援的舊版的色彩模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱[在 iOS 上 VisualElement 舊版色彩模式](legacy-color-mode.md)。
- 在啟用下拉式陰影[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱[VisualElement Drop Shadows On iOS](visualelement-drop-shadow.md)。

下列平臺特定功能適用于在 iOS 上的 Xamarin. 表單檢視：

- 設定[`Cell`](xref:Xamarin.Forms.Cell)背景色彩。 如需詳細資訊，請參閱[iOS 上的儲存格背景色彩](cell-background-color.md)。
- 確保所輸入文字融入[ `Entry` ](xref:Xamarin.Forms.Entry)藉由調整字型大小。 如需詳細資訊，請參閱[iOS 上的輸入字型大小](entry-font-size.md)。
- 在 設定資料指標色彩[ `Entry` ](xref:Xamarin.Forms.Entry)。 如需詳細資訊，請參閱[iOS 上的輸入游標色彩](entry-cursor-color.md)。
- 控制在滾動期間， [@no__t 1 的](xref:Xamarin.Forms.ListView)標頭儲存格是否浮動。 如需詳細資訊，請參閱[iOS 上的 ListView 群組標頭樣式](listview-group-header-style.md)。
- 控制更新[@no__t 1](xref:Xamarin.Forms.ListView)專案集合時，是否要停用資料列動畫。 如需詳細資訊，請參閱[在 iOS 上 ListView 資料列動畫](listview-row-animations.md)。
- 設定分隔符號樣式[ `ListView` ](xref:Xamarin.Forms.ListView)。 如需詳細資訊，請參閱[iOS 上的 ListView 分隔符號樣式](listview-separator-style.md)。
- 控制中的項目選取項目發生時[ `Picker` ](xref:Xamarin.Forms.Picker)。 如需詳細資訊，請參閱[iOS 上的選擇器專案選擇](picker-selection.md)。
- 啟用[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)點選位置上設定上的屬性[ `Slider` ](xref:Xamarin.Forms.Slider)列，而不是由拖曳`Slider`捲動方塊。 如需詳細資訊，請參閱[iOS 上的滑杆](slider-thumb.md)。

下列平臺特定功能是針對 iOS 上的 Xamarin. Forms 頁面所提供：

- 在上隱藏巡覽列分隔符號[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 如需詳細資訊，請參閱[iOS 上的 NavigationPage Bar 分隔符號](navigation-bar-separator.md)。
- 控制是否半透明的導覽列。 如需詳細資訊，請參閱[iOS 上的巡覽列半透明度](navigation-bar-translucent.md)。
- 控制是否狀態列文字的色彩上[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)調整成符合要求的導覽列的明暗度。 如需詳細資訊，請參閱[iOS 上的 NavigationPage Bar 文本色彩模式](status-bar-text-color.md)。
- 控制是否要將頁面標題顯示為頁面巡覽列中的大型標題。 如需詳細資訊，請參閱[iOS 上的大型頁面標題](page-large-title.md)。
- 在[`Page`](xref:Xamarin.Forms.Page)上設定主指示器的可見度。 如需詳細資訊，請參閱[iOS 上的主指示器可見度](page-home-indicator.md)。
- 設定狀態軸可見度[ `Page` ](xref:Xamarin.Forms.Page)。 如需詳細資訊，請參閱[iOS 上的頁面狀態列可見度](page-status-bar-visibility.md)。
- 確保內容該頁面位於而言是安全的所有 iOS 裝置的螢幕區域。 如需詳細資訊，請參閱[iOS 上的安全區域版面配置指南](page-safe-area-layout.md)。
- 設定模式頁面的呈現樣式。 如需詳細資訊，請參閱強制回應[頁面呈現樣式](page-presentation-style.md)。

下列平臺特定功能是針對 Xamarin 提供的。表單版面配置在 iOS 上：

- 控制是否[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)處理觸控筆勢，或將它傳遞給它的內容。 如需詳細資訊，請參閱[在 iOS 上 ScrollView 內容的觸控](scrollview-content-touches.md)。

IOS 上的 Xamarin [@no__t 1](xref:Xamarin.Forms.Application)類別提供下列平臺特定功能：

- 停用已命名字型大小的協助工具縮放。 如需詳細資訊，請參閱[iOS 上命名的字型大小的協助工具調整](named-font-size-scaling.md)。
- 啟用控制項的版面配置，並呈現在主執行緒上執行的更新。 如需詳細資訊，請參閱[iOS 上的主要執行緒控制更新](main-thread-updates-ui.md)。
- 啟用[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)捲動檢視來擷取，並分享捲動檢視中的移動瀏覽軌跡中。 如需詳細資訊，請參閱[iOS 上的同時](application-pan-gesture.md)移動流覽手勢辨識。

## <a name="ios-specific-formatting"></a>iOS 特定格式

Xamarin 可讓您設定跨平臺使用者介面樣式和色彩-但還有其他選項可以在 iOS 專案中使用平臺 Api 來設定 iOS 的主題。

[深入瞭解如何使用](formatting.md)IOS 特定 api （例如**plist**設定和 @no__t 2 api）來格式化使用者介面。

![](images/status-white-sml.png "iOS 主題")

## <a name="other-ios-features"></a>其他 iOS 功能

您可以使用[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器、 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)和[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)，將各種原生功能併入適用于 iOS 的 Xamarin Forms 應用程式中。

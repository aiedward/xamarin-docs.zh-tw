---
title: 在 Xamarin.Forms 中的 iOS 平台功能
description: 將 iOS 特有的功能新增至 Xamarin.Forms 應用程式中。
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/22/2019
ms.openlocfilehash: 471e09f236be505190ad2c08169bd445dcfca0a3
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2019
ms.locfileid: "57981740"
---
# <a name="ios-platform-features-in-xamarinforms"></a>在 Xamarin.Forms 中的 iOS 平台功能

開發適用於 iOS 的 Xamarin.Forms 應用程式需要 Visual Studio。 [需求頁面](~/get-started/requirements.md)包含必要條件的詳細資訊。

## <a name="platform-specifics"></a>平台特性

平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。

下列平台特有的功能被供 Xamarin.Forms 檢視、 頁面和 iOS 上的版面配置：

- 模糊的任何支援[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 < [VisualElement 模糊，在 iOS 上](visualelement-blur.md)。
- 停用上支援的舊版的色彩模式[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 < [VisualElement 舊版色彩模式，在 iOS 上](legacy-color-mode.md)。
- 在啟用下拉式陰影[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。 如需詳細資訊，請參閱 <<c0> [ 在 iOS 上的 VisualElement 卸除 Shadows](visualelement-drop-shadow.md)。

在 iOS 上的 Xamarin.Forms 檢視被提供下列平台特有的功能：

- 設定[ `Cell` ](xref:Xamarin.Forms.Cell)背景色彩。 如需詳細資訊，請參閱 <<c0> [ 在 iOS 上的資料格背景色彩](cell-background-color.md)。
- 確保所輸入文字融入[ `Entry` ](xref:Xamarin.Forms.Entry)藉由調整字型大小。 如需詳細資訊，請參閱 <<c0> [ 在 iOS 上的項目字型大小](entry-font-size.md)。
- 在 設定資料指標色彩[ `Entry` ](xref:Xamarin.Forms.Entry)。 如需詳細資訊，請參閱 <<c0> [ 在 iOS 上的項目資料指標色彩](entry-cursor-color.md)。
- 控制是否[ `ListView` ](xref:Xamarin.Forms.ListView)捲動期間浮點數的標頭資料格。 如需詳細資訊，請參閱 < [ListView 群組標頭樣式，在 iOS 上](listview-group-header-style.md)。
- 控制資料列動畫是否已停用時[ `ListView` ](xref:Xamarin.Forms.ListView)正在更新項目集合。 如需詳細資訊，請參閱 < [ListView 在 iOS 上的資料列動畫](listview-row-animations.md)。
- 設定分隔符號樣式[ `ListView` ](xref:Xamarin.Forms.ListView)。 如需詳細資訊，請參閱 < [ListView 分隔符號樣式，在 iOS 上](listview-separator-style.md)。
- 控制中的項目選取項目發生時[ `Picker` ](xref:Xamarin.Forms.Picker)。 如需詳細資訊，請參閱 <<c0> [ 在 iOS 上的選擇器項目選取](picker-selection.md)。
- 啟用[ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value)點選位置上設定上的屬性[ `Slider` ](xref:Xamarin.Forms.Slider)列，而不是由拖曳`Slider`捲動方塊。 如需詳細資訊，請參閱 <<c0> [ 在 iOS 上的滑桿捲動方塊點選](slider-thumb.md)。

在 iOS 上的 Xamarin.Forms 頁面提供下列平台特有的功能：

- 在上隱藏巡覽列分隔符號[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 如需詳細資訊，請參閱 < [NavigationPage Bar 分隔符號，在 iOS 上](navigation-bar-separator.md)。
- 控制是否半透明的導覽列。 如需詳細資訊，請參閱 <<c0> [ 在 iOS 上的導覽列半透明](navigation-bar-translucent.md)。
- 控制是否狀態列文字的色彩上[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)調整成符合要求的導覽列的明暗度。 如需詳細資訊，請參閱 < [NavigationPage 列文字的色彩模式在 iOS 上](status-bar-text-color.md)。
- 控制是否要將頁面標題顯示為頁面巡覽列中的大型標題。 如需詳細資訊，請參閱 <<c0> [ 在 iOS 上的大型頁面標題](page-large-title.md)。
- 設定狀態軸可見度[ `Page` ](xref:Xamarin.Forms.Page)。 如需詳細資訊，請參閱 <<c0> [ 在 iOS 上的頁面狀態列可見性](page-status-bar-visibility.md)。
- 確保內容該頁面位於而言是安全的所有 iOS 裝置的螢幕區域。 如需詳細資訊，請參閱 <<c0> [ 在 iOS 上的安全區域版面配置輔助線](page-safe-area-layout.md)。
- 在 iPad 上設定強制回應頁面的呈現樣式。 如需詳細資訊，請參閱 < [iPad 強制回應頁面呈現樣式](ipad-page-presentation-style.md)。

在 iOS 上的 Xamarin.Forms 版面配置被提供下列平台特有的功能：

- 控制是否[ `ScrollView` ](xref:Xamarin.Forms.ScrollView)處理觸控筆勢，或將它傳遞給它的內容。 如需詳細資訊，請參閱 < [ScrollView 在 iOS 上的內容修飾](scrollview-content-touches.md)。

下列平台特有的功能提供適用於 Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application)在 iOS 上的類別：

- 啟用控制項的版面配置，並呈現在主執行緒上執行的更新。 如需詳細資訊，請參閱 <<c0> [ 主要在 iOS 上，執行緒控制更新](main-thread-updates-ui.md)。
- 啟用[ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer)捲動檢視來擷取，並分享捲動檢視中的移動瀏覽軌跡中。 如需詳細資訊，請參閱 <<c0> [ 在 iOS 上的同時移動瀏覽軌跡辨識](application-pan-gesture.md)。

## <a name="ios-specific-formatting"></a>iOS 特有的格式

Xamarin.Forms 可讓跨平台使用者介面樣式和色彩設定-，但還有其他選項來設定您的 iOS 使用 iOS 專案中的平台 Api 的佈景主題。

[閱讀更多](formatting.md)格式化使用 iOS 專用的 Api，例如使用者介面的相關**Info.plist**組態和`UIAppearance`API。

![](images/status-white-sml.png "iOS 佈景主題")

## <a name="other-ios-features"></a>其他 iOS 功能

使用[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)，則[DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)，而[MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)，就可以將各種不同的原生功能適用於 iOS 的 Xamarin.Forms 應用程式。

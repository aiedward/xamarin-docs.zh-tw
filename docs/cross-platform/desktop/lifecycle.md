---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: WPF 與 Xamarin. Forms 應用程式生命週期
description: 本檔會比較 Xamarin 和 WPF 應用程式之應用程式生命週期的相似性和差異。 它也會查看視覺化樹狀結構、圖形、資源和樣式。
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: 6e959156aada0451efa13f6b67b6637f4e5deaa5
ms.sourcegitcommit: 836d54779190b1bef1b43bc0c2016c9b3034bfda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93281231"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>WPF 與 Xamarin. Forms 應用程式生命週期

Xamarin 會從 XAML 架構的架構（特別是 WPF）取得許多設計指引。 不過，在其他方式中，它會大幅偏離，而這可能是嘗試進行遷移的人員所需的重點。 本檔會嘗試找出其中一些問題，並提供可將 WPF 知識橋接到 Xamarin 的指引。

## <a name="app-lifecycle"></a>應用程式生命週期

WPF 和 Xamarin 之間的應用程式生命週期很類似。 兩者都是從外部 (平臺開始) 程式碼，並透過方法呼叫啟動 UI。 不同之處在于，Xamarin 一律會以平臺特定的元件啟動，然後初始化並建立應用程式的 UI。

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> `Main`根據預設，此方法是自動產生的，而且不會顯示在程式碼中。

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash;`MainActivity > App > ContentPage`
- **UWP** &ndash;`Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>應用程式類別

WPF 和 Xamarin 都有一個 `Application` 建立為 singleton 的類別。 在大部分情況下，應用程式會衍生自這個類別，以提供自訂應用程式，雖然 WPF 中並不是絕對必要的。 兩者都會公開 `Application.Current` 屬性，以找出所建立的 singleton。

### <a name="global-properties--persistence"></a>全域屬性 + 持續性

WPF 和 Xamarin 都有一個可用的 `Application.Properties` 字典，您可以在其中儲存可在應用程式任何地方存取的全域應用層級物件。 主要差異在於，當應用程式暫停時，Xamarin 會 _保存_ 儲存在集合中的任何基本類型，並在 relaunched 時重載。 WPF 不會自動支援該行為，而是大部分的開發人員都依賴隔離儲存區，或利用內建的 `Settings` 支援。

## <a name="defining-pages-and-the-visual-tree"></a>定義頁面和視覺化樹狀結構

WPF 會使用 `Window` 做為任何最上層視覺元素的根項目。 這會定義 Windows 世界中的 HWND 以顯示資訊。 您可以在 WPF 中同時建立及顯示任意數量的視窗。

在 Xamarin 中，最上層的視覺效果一律會由平臺定義-例如，在 iOS 上，它是 `UIWindow` 。 Xamarin 會使用類別，將其內容轉譯為這些原生平臺標記法 `Page` 。 `Page`在 Xamarin 中，每個都代表應用程式中唯一的「頁面」，一次只能顯示一個。

WPFs `Window` 和 Xamarin 都會 `Page` 包含一個 `Title` 屬性來影響顯示的標題，而且兩者都有一個 `Icon` 屬性可顯示頁面的特定圖示 ( **請注意** ，在 Xamarin) 中不一定會顯示標題和圖示。 此外，您可以變更背景色彩或影像這類的一般視覺效果屬性。

在技術上，您可以將兩個不同的平臺視圖轉譯 (例如定義兩個 `UIWindow` 物件，然後再將第二個物件轉譯為外部顯示器或 AirPlay) ，它需要平臺特定程式碼才能這麼做，而且不是 Xamarin 本身直接支援的功能。

### <a name="views"></a>檢視

這兩種架構的視覺階層都很類似。 WPF 會因為其對 WYSIWYG 檔的支援而更加深入。

**WPF**

```
DependencyObject - base class for all bindable things
   Visual - rendering mechanics
      UIElement - common events + interactions
         FrameworkElement - adds layout
            Shape - 2D graphics
            Control - interactive controls
```

**Xamarin.Forms**

```
BindableObject - base class for all bindable things
   Element - basic parent/child support + resources + effects
      VisualElement - adds visual rendering properties (color, fonts, transforms, etc.)
         View - layout + gesture support
```

### <a name="view-lifecycle"></a>檢視生命週期

Xamarin 主要是以行動案例為導向。 如此一來，當使用者與應用程式互動時，應用程式就會啟動、 _暫停_ 及 _重新__啟用_ 。 這類似于從 `Window` WPF 應用程式中按一下離開，並有一組方法和對應的事件可供您覆寫或掛上以監視此行為。

| 用途 | WPF 方法 | Xamarin. Forms 方法 |
|--- |--- |--- |
|初始啟用|ctor + Window. OnLoaded|ctor + Page. OnStart|
|顯示|IsVisibleChanged|頁面。出現|
|Hidden|IsVisibleChanged|頁面。消失|
|暫止/遺失焦點|OnDeactivated|OnSleep|
|已啟用/取得焦點|OnActivated|OnResume|
|已關閉|依序 onclosing + Window. OnClosed|n/a|

兩者都支援隱藏/顯示子控制項，在 WPF 中，它是三態的屬性 `IsVisible` (可見、隱藏和折迭的) 。 在 Xamarin 中，它只會顯示或隱藏于屬性中 `IsVisible` 。

### <a name="layout"></a>Layout

頁面配置會在 WPF 中發生的相同2階段 (量值/排列) 中進行。 您可以藉由覆寫 Xamarin 類別中的下列方法，以連接到頁面配置 `Page` ：

| 方法 | 用途 |
|--- |--- |
|OnChildMeasureInvalidated|子系的慣用大小已變更。|
|OnSizeAllocated|頁面已獲指派寬度/高度。|
|LayoutChanged 事件|頁面的版面配置/大小已經變更。|

目前沒有立即呼叫的全域配置事件，也不會有 `CompositionTarget.Rendering` 像在 WPF 中找到的全域事件。

#### <a name="common-layout-properties"></a>一般版面配置屬性

WPF 和 Xamarin 支援 `Margin` 控制專案周圍的間距，以及 `Padding` 控制元素 _內_ 的間距。 此外，大部分的 Xamarin 版面配置視圖都有屬性可控制間距 (例如，資料列或資料行) 。

此外，大部分的專案都會有屬性來影響它們在父容器中的位置：

| WPF | Xamarin.Forms | 用途 |
|--- |--- |--- |
|>horizontalalignment|HorizontalOptions|左/置/右/延展選項|
|VerticalAlignment|VerticalOptions|頂端/中間/下/延展選項|

> [!NOTE]
> 這些屬性的實際解讀取決於父容器。

#### <a name="layout-views"></a>版面配置視圖

WPF 和 Xamarin 都會使用版面配置控制項來定位子項目。 在大部分的情況下，這些功能在功能方面非常接近。

| WPF | Xamarin.Forms | 版面配置樣式 |
|--- |--- |--- |
|StackPanel|StackLayout|由左至右或由上到下無限堆疊|
|方格|方格| (列和資料行的表格式格式) |
|DockPanel|n/a|停駐于視窗邊緣|
|畫布|AbsoluteLayout|圖元/座標定位|
|WrapPanel|n/a|包裝堆疊|
|n/a|RelativeLayout|以規則為基礎的相對定位|

> [!NOTE]
> Xamarin 不支援 `GridSplitter` 。

這兩個平臺都使用 _附加屬性_ 來微調子系。

### <a name="rendering"></a>轉譯

WPF 和 Xamarin 的轉譯機制完全不同。 在 WPF 中，您建立的控制項直接將內容轉譯為螢幕上的圖元。 WPF 會維護兩個物件圖形 ( _樹狀_ 結構) 以表示此 _邏輯樹狀結構_ 代表在程式碼或 XAML 中定義的控制項，而 _視覺化樹狀結構_ 代表在螢幕上進行的實際轉譯，而視覺 (元素會直接透過虛擬 draw 方法) ，或透過 `ControlTemplate` 可取代或自訂的 XAML 定義來執行。 視覺化樹狀結構通常較複雜，因為它包含控制項周圍框線、隱含內容標籤等專案。WPF 包含一組 Api (`LogicalTreeHelper` 和 `VisualTreeHelper`) 來檢查這兩個物件圖形。

在 Xamarin 中，您在中定義的控制項 `Page` 其實只是簡單的資料物件。 它們類似于邏輯樹狀結構標記法，但絕對不會自行呈現內容。 相反地，它們是影響元素轉譯的 _資料模型_ 。 實際轉譯是由一 [組對應到每個控制項類型的個別 _視覺化_](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器所完成。 這些轉譯器會在每個平臺特定的專案中，由平臺專屬的 Xamarin. Forms 元件註冊。 您可以在 [這裡](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)看到清單。 除了取代或擴充轉譯器之外，Xamarin 也支援各種 [效果](~/xamarin-forms/app-fundamentals/effects/index.md) ，可用於影響每個平臺上的原生轉譯。

#### <a name="the-logicalvisual-tree"></a>邏輯/視覺化樹狀結構

沒有公開的 API 可引導 Xamarin 中的邏輯樹狀結構，但您可以使用反映來取得相同的資訊。 例如，以下是可以使用反映 [來列舉邏輯子](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) 系的方法。

## <a name="graphics"></a>圖形

Xamarin 包含圖形系統來繪製基本專案，也就是所謂的圖形。 如需圖形的詳細資訊，請參閱 [Xamarin 圖形](~/xamarin-forms/user-interface/shapes/index.md)。 此外，您可以包含協力廠商程式庫（例如 [SkiaSharp](~/xamarin-forms/user-interface/graphics/index.md) ），以取得跨平臺2d 繪圖。

## <a name="resources"></a>資源

WPF 和 Xamarin 都有資源和資源字典的概念。 您可以使用索引鍵將任何物件類型放置在中， `ResourceDictionary` 然後查詢 `{StaticResource}` 不會變更的專案，或在 `{DynamicResource}` 執行時間可在字典中變更的專案。 使用方式和機制都相同，但有一項差異： Xamarin 需要您定義將 `ResourceDictionary` 指派給 `Resources` 屬性，而 WPF 會預先建立一個並為您指派。

例如，請參閱下列定義：

**WPF**

```xaml
<Window.Resources>
   <Color x:Key="redColor">#ff0000</Color>
   ...
</Window.Resources>
```

**Xamarin.Forms**

```xaml
<ContentPage.Resources>
   <ResourceDictionary>
      <Color x:Key="redColor">#ff0000</Color>
      ...
   </ResourceDictionary>
</ContentPage.Resources>
```

如果您未定義 `ResourceDictionary` ，就會產生執行階段錯誤。

## <a name="styles"></a>樣式

Xamarin 中的樣式也受到完整的支援，而且可以用來為組成 UI 的 Xamarin 專案提供主題。 它們支援觸發程式 (屬性、事件和資料) 、繼承 `BasedOn` ，以及資源查閱值。 樣式會透過屬性明確地套用至專案 `Style` ，或透過不提供資源索引鍵（如同 WPF）隱含地套用至專案。

### <a name="device-styles"></a>裝置樣式

WPF 有一組預先定義的屬性 (在一組靜態類別上儲存為靜態值（例如 `SystemColors`) ），以值和資源金鑰的形式來指定系統色彩、字型和度量。 Xamarin 很類似，但會定義一組 [裝置樣式](~/xamarin-forms/user-interface/styles/device.md) 來代表相同的專案。 這些樣式是由架構所提供，並根據執行時間環境設定為值 (例如存取範圍) 。

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```

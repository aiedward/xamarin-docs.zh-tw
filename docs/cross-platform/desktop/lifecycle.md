---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: WPF 與Xamarin.Forms App 生命週期
description: 本檔會比較 Xamarin. Forms 和 WPF 應用程式的應用程式生命週期之間的相似性和差異。 它也會查看視覺化樹狀結構、圖形、資源和樣式。
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: 1d1c207672baf37c91d39fca1d9f628d85971577
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765910"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>WPF 與Xamarin.Forms App 生命週期

Xamarin 會從以 XAML 為基礎的架構（特別是 WPF）中取得許多設計指引。 不過，在其他情況下，它會明顯偏離，這可能是嘗試進行遷移的人的一個重點。 本檔會嘗試找出其中一些問題，並提供盡可能將 WPF 知識橋接至 Xamarin 的指引。

## <a name="app-lifecycle"></a>應用程式生命週期

WPF 與 Xamarin 之間的應用程式生命週期類似。 兩者都是從外部（平臺）程式碼開始，並透過方法呼叫啟動 UI。 差別在於 Xamarin 會一律在平臺特定元件中啟動，然後初始化並建立應用程式的 UI。

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> 根據預設，方法是自動產生的，而且不會顯示在程式碼中。 `Main`

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP**&ndash;`Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>應用程式類別

WPF 和 Xamarin 都有一個`Application`類別，它會建立為 singleton。 在大部分情況下，應用程式會從這個類別衍生來提供自訂應用程式，不過在 WPF 中並不是必要的。 兩者都會公開`Application.Current`屬性，以找出所建立的 singleton。

### <a name="global-properties--persistence"></a>全域屬性 + 持續性

WPF 和 Xamarin 都有`Application.Properties`可用的字典，您可以在其中儲存應用程式中任何地方都可存取的全域應用層級物件。 主要的差別在於，當應用程式暫止時，Xamarin 會_保存_儲存在集合中的任何基本類型，並在 relaunched 時重載。 WPF 不會自動支援該行為，而是大部分的開發人員都依賴隔離儲存區，或利用內`Settings`建的支援。

## <a name="defining-pages-and-the-visual-tree"></a>定義頁面和視覺化樹狀結構

WPF 會使用`Window`做為任何最上層視覺元素的根項目。 這會在 Windows 世界中定義 HWND 以顯示資訊。 您可以在 WPF 中同時建立和顯示多個視窗。

在 [Xamarin] 中，最上層的視覺效果一律由平臺定義-例如，在 iOS 上，它`UIWindow`是。 Xamarin 會使用`Page`類別，將其內容轉譯成這些原生平臺標記法。 在`Page` Xamarin 中，每個表單在應用程式中都代表一個唯一的「頁面」，一次只會顯示一個。

WPFs `Window`和 Xamarin `Page`都會包含一個`Title`屬性來影響顯示的標題，而且兩者都有一個`Icon`屬性可顯示頁面的特定圖示（**請注意**，標題和圖示不一定是在 Xamarin 中可見）。 此外，您可以變更這兩者的通用視覺屬性，例如背景色彩或影像。

在技術上，您可以轉譯成兩個不同的平臺視圖（例如`UIWindow` ，定義兩個物件，並讓第二個物件呈現給外部顯示或 AirPlay），它需要平臺特定的程式碼才能執行這項操作，而且不是直接支援的功能Xamarin。表單本身。

### <a name="views"></a>檢視

這兩種架構的視覺化階層很類似。 WPF 因為其對 WYSIWYG 檔的支援而更深入。

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

Xamarin 主要面向行動案例。 如此一來，當使用者與其互動時，應用程式就會啟動、_暫停_和_重新_ _啟用_。 這類似于按一下 WPF 應用程式中`Window`的，而且有一組方法和對應的事件可供您覆寫或連結以監視此行為。

| 用途 | WPF 方法 | Xamarin. Forms 方法 |
|--- |--- |--- |
|初始啟用|ctor + Window. OnLoaded|ctor + Page. OnStart|
|所|Window.IsVisibleChanged|Page.Appearing|
|Hidden|Window.IsVisibleChanged|Page.Disappearing|
|暫停/遺失焦點|Window.OnDeactivated|Page.OnSleep|
|已啟用/獲得焦點|Window.OnActivated|Page. OnResume|
|Closed|OnClosing + Window. OnClosed|N/A|

同時也支援隱藏/顯示子控制項，在 WPF 中，它是三個狀態的`IsVisible`屬性（可見、隱藏和折迭）。 在 [ `IsVisible` Xamarin] 中，它只會透過屬性顯示或隱藏。

### <a name="layout"></a>配置

頁面配置會在 WPF 中發生的相同2階段（測量/排列）中進行。 您可以藉由覆寫 Xamarin. Forms `Page`類別中的下列方法，來連結至頁面版面配置：

| 方法 | 用途 |
|--- |--- |
|OnChildMeasureInvalidated|子系的慣用大小已變更。|
|OnSizeAllocated|頁面已獲指派寬度/高度。|
|LayoutChanged 事件|版面配置/頁面的大小已變更。|

目前不會呼叫全域配置事件，也不會在 WPF 中找到類似`CompositionTarget.Rendering`的全域事件。

#### <a name="common-layout-properties"></a>一般版面配置屬性

WPF 和 Xamarin。表單兩者`Margin`都支援控制元素周圍的間距， `Padding`以及控制元素_內_的間距。 此外，大部分的 Xamarin 版面配置視圖都具有可控制間距的屬性（例如，資料列或資料行）。

此外，大部分的專案都有屬性，可影響它們在父容器中的位置：

| WPF | Xamarin.Forms | 用途 |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|左/置/靠右/伸展選項|
|VerticalAlignment|VerticalOptions|上/中/下/伸展選項|

> [!NOTE]
> 這些屬性的實際轉譯取決於父容器。

#### <a name="layout-views"></a>版面配置視圖

WPF 和 Xamarin。表單都使用版面配置控制項來定位子項目。 在大部分的情況下，這些都是在功能方面非常接近彼此。

| WPF | Xamarin.Forms | 版面配置樣式 |
|--- |--- |--- |
|StackPanel|StackLayout|由左至右或由上而下的無限堆疊|
|Grid|Grid|表格式格式（資料列和資料行）|
|DockPanel|N/A|停駐于視窗邊緣|
|Canvas|AbsoluteLayout|圖元/座標定位|
|WrapPanel|N/A|包裝堆疊|
|N/A|RelativeLayout|相對規則的定位|

> [!NOTE]
> Xamarin 不支援`GridSplitter`。

這兩個平臺都使用_附加屬性_來微調子系。

### <a name="rendering"></a>正在轉譯

WPF 和 Xamarin 的轉譯機制截然不同。 在 WPF 中，您建立的控制項直接將內容轉譯成螢幕上的圖元。 WPF 會維護兩個物件圖形（_樹狀_結構）來表示這個-_邏輯樹狀結構_代表在程式碼或 XAML 中定義的控制項，而_視覺化樹狀結構_代表在執行的畫面上發生的實際轉譯。直接由視覺專案（透過虛擬 draw 方法），或透過可取代或自訂的`ControlTemplate` XAML 定義。 一般而言，視覺化樹狀結構更為複雜，因為它包含了控制項周圍的框線、隱含內容的標籤等專案。WPF 包含一組 api （`LogicalTreeHelper`和`VisualTreeHelper`），可檢查這兩個物件圖形。

在 [Xamarin] 中，您在中`Page`定義的控制項其實只是簡單的資料物件。 它們類似于邏輯樹狀結構標記法，但絕不會自行呈現內容。 相反地，它們是影響元素轉譯的_資料模型_。 實際的轉譯是由一組個別的視覺化轉譯器所完成，這些轉譯器[會對應到每個控制項類型 ](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。 這些轉譯器會由平臺特定的 Xamarin 元件在每個平臺特定專案中註冊。 您可以在[這裡](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)看到清單。 除了取代或擴充轉譯器之外，Xamarin 也支援各種[效果](~/xamarin-forms/app-fundamentals/effects/index.md)，可用於影響每個平臺上的原生轉譯。

#### <a name="the-logicalvisual-tree"></a>邏輯/視覺化樹狀結構

沒有公開的 API 可引導 Xamarin 中的邏輯樹狀結構，但您可以使用反映來取得相同的資訊。 例如，以下是可以使用反映[來列舉邏輯子](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108)系的方法。

## <a name="graphics"></a>圖形

Xamarin 不包括簡單矩形（`BoxView`）以外基本類型的圖形系統。 您可以包含協力廠商程式庫（例如[SkiaSharp](~/graphics-games/skiasharp/index.md) ）以取得跨平臺2d 繪圖，或用於3D 的[UrhoSharp](~/graphics-games/urhosharp/index.md) 。

## <a name="resources"></a>資源

WPF 和 Xamarin。表單兩者都有資源和資源字典的概念。 您可以`ResourceDictionary`使用索引鍵將任何物件類型放入中，然後`{StaticResource}`針對不會變更的專案，或`{DynamicResource}`在執行時間可在字典中變更的專案進行查詢。 使用方式和機制都相同，但有一項差異：Xamarin 會要求您定義要指派`ResourceDictionary` `Resources`給屬性的，而 WPF 則會預先建立一個，並為您指派。

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

如果您未定義`ResourceDictionary`，則會產生執行階段錯誤。

## <a name="styles"></a>樣式

在 Xamarin 中也完全支援樣式，而且可以用來建立組成 UI 的 Xamarin 元素的主題。 它們支援觸發程式（屬性、事件和資料）、繼承`BasedOn`和資源查閱以取得值。 樣式會透過`Style`屬性明確地套用至專案，或藉由不提供資源索引鍵（就像 WPF）隱含地套用至元素。

### <a name="device-styles"></a>裝置樣式

WPF 具有一組預先定義的屬性（在一組靜態類別上儲存為靜態值， `SystemColors`例如），其會以值和資源索引鍵的形式來指定系統色彩、字型和計量。 Xamarin 很類似，但會定義一組[裝置樣式](~/xamarin-forms/user-interface/styles/device.md)來表示相同的專案。 這些樣式是由架構提供，並根據執行時間環境（例如協助工具）設定為值。

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```

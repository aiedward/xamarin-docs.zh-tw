---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: WPF 和。Xamarin.Forms 應用程式生命週期
description: 這份文件相比較的相似性與差異 Xamarin.Forms 和 WPF 應用程式的應用程式生命週期。 它也會查看視覺化樹狀結構、 圖形、 資源和樣式。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: abb7773873fa181085464b5985cc8233715cc4be
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781578"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>WPF 和。Xamarin.Forms 應用程式生命週期

Xamarin.Forms 會耗用大量的之前它，特別是 WPF XAML 為基礎的架構設計指導方針。 不過，其他方式它偏差大幅可以是人嘗試透過移轉的自黏點。 這份文件會嘗試識別這些問題的某些並提供指引，盡可能橋接器 WPF 知識 Xamarin.Forms。

## <a name="app-lifecycle"></a>應用程式生命週期

WPF 和 Xamarin.Forms 之間的應用程式生命週期很類似。 開始在外部 （平台） 的程式碼和啟動 UI 透過方法呼叫。 差別在於 Xamarin.Forms 一律啟動初始化，然後建立應用程式 UI 的平台專屬組件中。

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> `Main`方法是，根據預設，自動產生和程式碼中不可見。

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>應用程式類別

WPF 和 Xamarin.Forms 有`Application`建立為單一類別。 在大部分情況下，應用程式將衍生自此類別以提供自訂的應用程式中，雖然這完全不需要在 WPF 中。 同時公開`Application.Current`找出建立之單一個體的屬性。

### <a name="global-properties--persistence"></a>全域屬性 + 持續性

WPF 和 Xamarin.Forms 有`Application.Properties`字典可用您可以在其中儲存應用程式中的任何位置存取的全域應用程式層級物件。 主要差異是 Xamarin.Forms 將要_保存_儲存在集合中，當應用程式已擱置，而且它會重新啟動時重新載入任何基本型別。 WPF 會自動不支援該行為-請改大部分的開發人員依賴隔離儲存區，或利用內建`Settings`支援。

## <a name="defining-pages-and-the-visual-tree"></a>定義頁面和視覺化樹狀結構

WPF 使用`Window`任何最上層的視覺項目是根項目。 這會定義在 Windows 世界中顯示資訊的 HWND。 您可以建立及顯示多同時依您在 WPF 中的視窗。

在透過 Xamarin.Forms，最上層的視覺效果一律來定義平台-例如在 iOS 上，就`UIWindow`。 Xamarin.Forms 會轉譯成使用這些原生平台表示內容是`Page`類別。 每個`Page`Xamarin.Forms 中代表唯一 「 頁面 」 中應用程式，其中只有一個為一次可見。

這兩個 WPFs`Window`和 Xamarin.Forms`Page`包含`Title`屬性來影響顯示的標題，並同時有`Icon`屬性來顯示頁面的特定圖示 (**注意**，標題和圖示不一定在 Xamarin.Forms 中顯示）。 此外，您可以變更常見的視覺屬性上兩個背景色彩或影像等。

技術上可以轉譯為兩個不同的平台檢視 (例如定義兩個`UIWindow`物件，並有一個第二個轉譯至外部顯示器或 AirPlay)，它需要平台專屬程式碼，若要這樣做並不直接支援的功能Xamarin.Forms 本身。

### <a name="views"></a>檢視

這兩個架構為視覺階層很類似。 WPF 是位元更深入，因為它的支援 WYSIWYG 文件。

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

### <a name="view-lifcycle"></a>檢視 Lifcycle

Xamarin.Forms 是主要導向周圍行動裝置。 因此，應用程式是_啟動_，_暫停_，和_重新啟動_與它們互動使用者。 這是類似於按一下離開的`Window`WPF 應用程式中有一組方法和對應的事件，您可以覆寫或連接到監視這種行為。

| 用途 | WPF 方法 | Xamarin.Forms 方法 |
|--- |--- |--- |
|初始啟動|ctor + Window.OnLoaded|ctor + Page.OnStart|
|顯示|Window.IsVisibleChanged|Page.Appearing|
|Hidden|Window.IsVisibleChanged|Page.Disapearing|
|暫停/遺失焦點|Window.OnDeactivated|Page.OnSleep|
|啟用/已取得焦點|Window.OnActivated|Page.OnResume|
|Closed|Window.OnClosing + Window.OnClosed|N/A|


隱藏/顯示子控制項以及兩個支援，在 WPF 中是三種狀態屬性`IsVisible`（可見、 隱藏和摺疊）。 在透過 Xamarin.Forms，就只是可見或隱藏透過`IsVisible`屬性。

### <a name="layout"></a>配置

頁面配置就會發生在相同 2-階段 （量值/排列） WPF 中發生的。 您可以藉由覆寫下列方法，在 Xamarin.Forms 中的連結到頁面配置`Page`類別：

| 方法 | 用途 |
|--- |--- |
|OnChildMeasureInvalidated|子系的慣用的大小已變更。|
|OnSizeAllocated|頁面已被指派的寬度/高度。|
|LayoutChanged 事件|頁面版面配置/大小已經變更。|

那里稱為現在，任何全域配置事件也沒有全域`CompositionTarget.Rendering`WPF 中找到類似的事件。

#### <a name="common-layout-properties"></a>一般的版面配置屬性

WPF 和 Xamarin.Forms 都支援`Margin`為控制項項目，周圍的間距和`Padding`控制項間距_內_項目。 此外，大部分的 Xamarin.Forms 版面配置檢視有屬性以控制間距 （例如資料列或資料行）。

此外，大部分項目會有影響在父容器中的置放方式的屬性：

| WPF | Xamarin.Forms | 用途 |
|--- |--- |--- |
|水平對齊|HorizontalOptions|左/中心/右/縮放選項|
|垂直對齊|VerticalOptions|Top/中心/底部/縮放選項|

> [!NOTE]
> 這些屬性的實際的解譯取決於父容器。

#### <a name="layout-views"></a>配置檢視

WPF 和 Xamarin.Forms 使用的版面配置控制項定位子項目。 在大部分情況下，它們是非常接近彼此就功能而言。

| WPF | Xamarin.Forms | 配置樣式 |
|--- |--- |--- |
|StackPanel|StackLayout|左到右或靠上到下的無限堆疊|
|Grid|Grid|表格格式 （資料列和資料行）|
|DockPanel|N/A|將停駐視窗的邊緣|
|Canvas|AbsoluteLayout|像素/座標位置|
|WrapPanel|N/A|包裝堆疊|
|N/A|RelativeLayout|相對規則為基礎的位置|

> [!NOTE]
> 不支援 Xamarin.Forms `GridSplitter`。

這兩個平台都使用_附加屬性_微調子系。

### <a name="rendering"></a>正在轉譯

WPF 和 Xamarin.Forms 的轉譯機制，不是完全不同。 在 WPF 中，您會直接建立控制項以呈現在螢幕上的像素為單位的內容。 WPF 會維護兩個物件圖形 (_樹_) 來表示這-_邏輯樹狀結構_定義程式碼或 XAML 中表示的控制項和_視覺化樹狀結構_代表這是在螢幕，就會發生的實際呈現所執行的是直接由視覺項目 （透過虛擬繪製方法），或是透過 XAML 定義`ControlTemplate`它可以取代或自訂。 一般而言，視覺化樹狀結構是更複雜，因為它包含此等控制項，隱含的內容等的標籤周圍的框線。WPF 包含一組 Api (`LogicalTreeHelper`和`VisualTreeHelper`) 來檢查這些兩個物件圖形。

在透過 Xamarin.Forms，控制項您定義在`Page`其實只是簡單的資料物件。 這些邏輯樹狀結構表示，類似，但永遠不會呈現在自己的內容。 相反地，它們是_資料模型_這會影響轉譯項目。 實際的呈現方式是[分隔組_視覺化轉譯器_該資料會對應至每個控制項型別](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。 這些轉譯器都由平台專屬 Xamarin.Forms 組件登錄中的每個平台特定專案。 您可以查看清單[這裡](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。 取代或擴充的轉譯器，除了 Xamarin.Forms 也有支援[效果](~/xamarin-forms/app-fundamentals/effects/index.md)可用來影響每個 plaform 基礎原生的轉譯。

#### <a name="the-logicalvisual-tree"></a>邏輯/Visual 樹狀結構

沒有任何公開的 API，在 Xamarin.Forms-查核邏輯樹狀結構，但您可以使用反映來取得相同的資訊。 例如，[以下是一種方法可以列舉邏輯子系](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108)使用反映。

## <a name="graphics"></a>圖形

Xamarin.Forms 不包含超過一個簡單的矩形的基本類型的圖形系統 (`BoxView`)。 您可以包括第 3 個合作對象文件庫，例如[SkiaSharp](~/graphics-games/skiasharp/index.md)取得跨平台 2D 繪圖，或[UrhoSharp](~/graphics-games/urhosharp/index.md) 3d。

## <a name="resources"></a>資源

WPF 和 Xamarin.Forms 兩者都有資源與資源字典的概念。 您可以將放入任何物件型別`ResourceDictionary`具有索引鍵，然後再查閱與`{StaticResource}`進行的操作不會變更，或`{DynamicResource}`可以變更在執行階段字典中的物件。 使用方式和機制，不會有一點相同： Xamarin.Forms 會要求您定義`ResourceDictionary`要指派給`Resources`屬性而 WPF 預先建立一個，並將它指派您。

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

如果您沒有定義`ResourceDictionary`，就會產生執行階段錯誤。

## <a name="styles"></a>樣式

樣式也完全支援在 Xamarin.Forms 中，而且可以是用來佈景主題 UI 構成 Xamarin.Forms 項目。 它們支援透過觸發程序 （屬性、 事件和資料） 繼承`BasedOn`，和值的資源查閱。 樣式會套用至項目可能是透過明確`Style`屬性或表示已經透過不提供資源的索引鍵-如同 WPF。

### <a name="device-styles"></a>裝置樣式

WPF 的一組預先定義的屬性 (例如儲存成一組靜態類別上的靜態值`SystemColors`) 的指示系統色彩、 字型及格式的值和資源的索引鍵的度量。 Xamarin.Forms 類似，但定義一組[裝置樣式](~/xamarin-forms/user-interface/styles/device.md)來代表相同的動作。 這些樣式是依照 frameowrk 提供，且設定為根據的執行階段環境 （例如協助工具） 的值。

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```

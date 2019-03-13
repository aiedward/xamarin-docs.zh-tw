---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: WPF 和。Xamarin.Forms 應用程式生命週期
description: 這份文件比較 Xamarin.Forms 和 WPF 應用程式的應用程式生命週期之間的差異與相似之處。 它也會探討視覺化樹狀結構、 圖形、 資源和樣式。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 653e2f849a74948d3636f594eae91cdeabfae138
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526789"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>WPF 和。Xamarin.Forms 應用程式生命週期

Xamarin.Forms 會耗用大量的 XAML 為基礎的架構，它，特別是 WPF 之前出現的設計指引。 不過，以其他方式它偏差大幅可以是嘗試移轉的人的自黏便箋點。 這份文件會嘗試找出這些問題，並提供給 Xamarin.Forms 指引，盡可能橋接器 WPF 知識。

## <a name="app-lifecycle"></a>應用程式生命週期

WPF 和 Xamarin.Forms 之間的應用程式生命週期很類似。 兩個外部 （平台） 程式碼中啟動，而啟動 UI 透過方法呼叫。 差別在於 Xamarin.Forms，永遠會啟動然後初始化並建立應用程式的 UI 平台特定組件中。

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> `Main`方法是，根據預設，自動產生和程式碼中看不到。

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>應用程式類別

WPF 和 Xamarin.Forms 有`Application`類別是建立為單一值。 在大部分情況下，應用程式會衍生自這個類別提供自訂的應用程式中，雖然這並非必要在 WPF 中。 同時公開`Application.Current`找出所建立之單一個體的屬性。

### <a name="global-properties--persistence"></a>全域屬性 + 持續性

WPF 和 Xamarin.Forms 有`Application.Properties`字典提供您可以在其中儲存應用程式中的任何位置存取的全域應用程式層級物件。 主要差異在於 Xamarin.Forms 可_保存_時應用程式已擱置，而且它會重新啟動時重新載入這些儲存在集合中任何基本類型。 WPF 不會自動支援該行為-相反地，大部分的開發人員在隔離儲存區，或利用內建`Settings`支援。

## <a name="defining-pages-and-the-visual-tree"></a>定義網頁和視覺化樹狀結構

WPF 使用`Window`為任何最上層的視覺元素的根項目。 這會定義在 Windows 世界中，以顯示資訊的 HWND。 您可以建立並顯示同時隨意在 WPF 中的多個視窗。

在 Xamarin.Forms 中，最上層的視覺效果一律已定義的平台-iOS 上，比方說，它是`UIWindow`。 它是內容到使用這些原生平台表示法的 Xamarin.Forms 轉譯`Page`類別。 每個`Page`在 Xamarin.Forms 中代表唯一 「 頁面 」 的應用程式，其中只有一個一次為可見。

這兩個 WPFs`Window`和 Xamarin.Forms`Page`包括`Title`屬性來影響顯示的標題，並同時有`Icon`屬性以顯示頁面的特定圖示 (**注意**，標題和圖示不會永遠顯示在 Xamarin.Forms 中）。 此外，您可以變更常見的視覺屬性，例如背景色彩或影像上。

就技術上而言可轉譯為兩個不同的平台檢視 (例如定義兩個`UIWindow`物件而且具有第二項轉譯到外部顯示器或 AirPlay)，它需要平台特定程式碼，若要這樣做並不直接支援的功能Xamarin.Forms 本身。

### <a name="views"></a>檢視

這兩種架構視覺階層很類似。 WPF 是有點更深入，因為它對 WYSIWYG 的文件的支援。

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

Xamarin.Forms 主要是針對行動情節的方向。 因此，應用程式會_啟動_，_暫止_，並_重新啟動_使用者與它們互動。 這是類似於以外的位置按一下`Window`WPF 應用程式中有一組方法和對應的事件，您可以覆寫或連結到要監視此行為。

| 用途 | WPF 方法 | Xamarin.Forms 方法 |
|--- |--- |--- |
|初始啟動|ctor + Window.OnLoaded|ctor + Page.OnStart|
|顯示|Window.IsVisibleChanged|Page.Appearing|
|Hidden|Window.IsVisibleChanged|Page.Disappearing|
|暫停/遺失焦點|Window.OnDeactivated|Page.OnSleep|
|啟動/但取得焦點|Window.OnActivated|Page.OnResume|
|Closed|Window.OnClosing + Window.OnClosed|N/A|


隱藏/顯示子控制項也都支援，在 WPF 中是三種狀態屬性`IsVisible`（顯示、 隱藏和摺疊）。 在 Xamarin.Forms 中，就只是可見或隱藏透過`IsVisible`屬性。

### <a name="layout"></a>配置

頁面配置就會發生相同 2-在階段中 （量值/排列） 在 WPF 中發生。 您可以藉由覆寫在 Xamarin.Forms 中的下列方法將連結到 頁面配置`Page`類別：

| 方法 | 用途 |
|--- |--- |
|OnChildMeasureInvalidated|子系的慣用的大小已變更。|
|OnSizeAllocated|頁面已被指派寬度/高度。|
|LayoutChanged 事件|頁面的版面配置/大小已經變更。|

那里任何全域版面配置事件稱為今天，也沒有全域`CompositionTarget.Rendering`在 WPF 中找到類似的事件。

#### <a name="common-layout-properties"></a>常見的版面配置屬性

WPF 和 Xamarin.Forms 都支援`Margin`來控制元素，周圍的間距和`Padding`控制項的間距_內_項目。 此外，大部分的 Xamarin.Forms 版面配置檢視有屬性，以控制之間的間隔 （例如資料列或資料行）。

此外，大部分項目有屬性，以影響它們的父容器中的放置方式：

| WPF | Xamarin.Forms | 用途 |
|--- |--- |--- |
|水平對齊|HorizontalOptions|左/Center/右/Stretch 選項|
|垂直對齊|VerticalOptions|頂端/Center/底端/Stretch 選項|

> [!NOTE]
> 這些屬性的實際的解譯取決於父容器。

#### <a name="layout-views"></a>版面配置檢視

WPF 和 Xamarin.Forms 使用版面配置控制項来放置子項目。 在大部分情況下，這些是非常接近彼此就功能而言。

| WPF | Xamarin.Forms | 版面配置樣式 |
|--- |--- |--- |
|StackPanel|StackLayout|左到右或由上到下的無限堆疊|
|Grid|Grid|（資料列和資料行） 的表格式格式|
|DockPanel|N/A|停駐於視窗邊緣|
|Canvas|AbsoluteLayout|像素/座標位置|
|WrapPanel|N/A|文繞圖堆疊|
|N/A|RelativeLayout|相對的規則為基礎位置|

> [!NOTE]
> Xamarin.Forms nepodporuje `GridSplitter`。

使用兩種平台_附加屬性_微調子系。

### <a name="rendering"></a>正在轉譯

適用於 WPF 和 Xamarin.Forms 的轉譯機制是完全不同。 在 WPF 中，您建立的控制項直接將內容呈現至螢幕上的像素為單位。 WPF 會維護兩個物件圖形 (_樹狀結構_) 來代表這位_邏輯樹狀結構_表示的控制項，定義程式碼或 XAML，和_視覺化樹狀結構_代表這是在螢幕，就會發生的實際轉譯所執行的是直接透過視覺元素 （透過虛擬 draw 方法），或透過 XAML 定義`ControlTemplate`可以取代或自訂。 一般而言，視覺化樹狀結構是更複雜，因為它包含這例如控制項、 隱含的內容等的標籤周圍的框線。WPF 包含一組 Api (`LogicalTreeHelper`和`VisualTreeHelper`) 若要檢查這些項目中，兩個物件圖形。

在 Xamarin.Forms 中，控制您定義在`Page`是其實只是簡單的資料物件。 它們類似於邏輯樹狀結構表示法，但永遠不會呈現在他們自己的內容。 相反地，它們是_資料模型_這會影響轉譯項目。 實際轉譯由[分隔組_視覺化轉譯器_這會對應至每個控制項類型](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。 這些轉譯器都由平台特有的 Xamarin.Forms 組件登錄中的每個平台特定專案。 您可以看到清單[此處](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。 除了取代或擴充的轉譯器，Xamarin.Forms 也有支援[效果](~/xamarin-forms/app-fundamentals/effects/index.md)可用以影響每個平台原生的轉譯。

#### <a name="the-logicalvisual-tree"></a>邏輯/視覺樹狀結構

沒有任何公開的 API，以依 Xamarin.Forms-邏輯樹狀結構，但您可以使用反映來取得相同的資訊。 例如，[以下是方法，這個方法可以列舉邏輯子系會](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108)使用反映。

## <a name="graphics"></a>圖形

Xamarin.Forms 不包含超過一個簡單的矩形的基本類型的圖形系統 (`BoxView`)。 您可以包括第 3 個廠商程式庫，例如[SkiaSharp](~/graphics-games/skiasharp/index.md)若要取得跨平台 2D 繪圖，或是[UrhoSharp](~/graphics-games/urhosharp/index.md) 3d。

## <a name="resources"></a>資源

WPF 和 Xamarin.Forms 兩者都有資源與資源字典的概念。 您可以將任何物件型別`ResourceDictionary`具有索引鍵然後查詢它與`{StaticResource}`進行的操作不會變更，或`{DynamicResource}`可以變更在執行階段字典中的項目。 機制與使用方式完全相同，但其中一項差異： Xamarin.Forms 會要求您定義`ResourceDictionary`若要指派給`Resources`屬性而 WPF 預先會建立一個，並將它指派您。

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

樣式也完全支援在 Xamarin.Forms 中，而且可以是用於 Xamarin.Forms 項目構成 UI 佈景主題。 它們支援觸發程序 （屬性、 事件和資料） 繼承，透過`BasedOn`，和值的資源查閱。 樣式會套用至項目是透過明確`Style`屬性，或隱含地不提供資源的索引鍵-就像 WPF。

### <a name="device-styles"></a>裝置樣式

WPF 有一組預先定義的屬性 (例如儲存成一組靜態類別上的靜態值`SystemColors`) 的指定系統色彩、 字型和度量值和資源索引鍵的形式。 Xamarin.Forms 很類似，但定義一組[裝置樣式](~/xamarin-forms/user-interface/styles/device.md)來代表相同的項目。 這些樣式是 framework 所提供，並設定為在執行階段環境 （例如協助工具） 的值。

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```

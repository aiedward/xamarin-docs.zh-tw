---
title: Xamarin.Forms Shell 版面配置
description: 飛出視窗之後，Shell 應用程式中的下一個導覽層級是底部的索引標籤列。 或者，應用程式的導覽模式可以從底部索引標籤開始，且不使用飛出視窗。 在這兩種情況下，當底部索引標籤包含多個頁面時，頁面將可透過頂端索引標籤來導覽。
ms.prod: xamarin
ms.assetid: 318D81DB-E456-4E44-B083-36A27DBD9523
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/23/2019
ms.openlocfilehash: cd3bfd9186c87594fc42702e2d62b33e68973db6
ms.sourcegitcommit: 10b4ccbfcf182be940899c00fc0fecae1e199c5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252287"
---
# <a name="xamarinforms-shell-tabs"></a>Xamarin.Forms Shell 索引標籤

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

當應用程式的導覽模式包含飛出視窗時，應用程式中的下一層導覽是底部索引標籤列。 此外，當飛出視窗關閉時，底部的索引標籤列可視為導覽的最上層。

或者，應用程式的導覽模式可以從底部索引標籤開始，且不使用飛出視窗。 在此案例中，`Shell` 物件的子系應該是 `TabBar` 物件，它表示底部的索引標籤列。

> [!NOTE]
> `TabBar` 型別會停用飛出視窗。

每個 `FlyoutItem` 物件或 `TabBar` 物件都可以包含一或多個 `Tab` 物件，其中每個 `Tab` 物件都表示底部索引標籤列上的一個索引標籤。 每個 `Tab` 物件都可以包含一或多個 `ShellContent` 物件，而且每個 `ShellContent` 物件將會顯示單一 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件。 當多個 `ShellContent` 物件出現在 `Tab` 物件中時，`ContentPage` 物件將可透過頂端索引標籤導覽。

在每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件中，都可以導覽至其他 `ContentPage` 物件。 如需有關導覽的詳細資訊，請參閱 [Xamarin.Forms Shell 導覽](navigation.md)。

## <a name="single-page-application"></a>單一頁面應用程式

最簡單的 Shell 應用程式是單一頁面應用程式，可以透過將單一 `Tab` 物件新增至 `TabBar` 物件來建立。 在 `Tab` 物件中，`ShellContent` 物件應該設定為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

此程式碼範例會產生下列單一頁面應用程式：

[![Shell 單一頁面應用程式在 iOS 和 Android 上的螢幕擷取畫面](tabs-images/single-page-app.png "Shell 單一頁面應用程式")](tabs-images/single-page-app-large.png#lightbox "Shell 單一頁面應用程式")

> [!NOTE]
> 如有需要，可以隱藏導覽列，方法是，針對 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件將 `Shell.NavBarIsVisible` 附加屬性設為 `false`。

Shell 具有隱含的轉換運算子，可簡化 Shell 視覺階層，而不需要將其他檢視引進視覺化樹狀結構。 這是可能的，因為子類別化的 `Shell` 物件只能包含 `FlyoutItem` 物件或 `TabBar` 物件，其中只能包含 `Tab` 物件，且其中只能包含 `ShellContent` 物件。 這些隱含的轉換運算子可以用來移除上一個範例中的 `TabBar`、`Tab` 和 `ShellContent` 物件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <views:CatsPage />
</Shell>
```

這項隱含的轉換會自動將 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件包裝在 `ShellContent` 物件中，這會包裝在 `Tab` 物件中，然後再包裝在 `FlyoutItem` 物件中。 單一頁面應用程式中不需要有飛出視窗，因此 `Shell.FlyoutBehavior` 屬性會設定為 `Disabled`。

> [!IMPORTANT]
> 在 Shell 應用程式中，屬於 `ShellContent` 物件子系的每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 都會在應用程式啟動期間建立。 使用此方法新增其他 `ShellContent` 物件將會導致在應用程式啟動期間建立其他頁面，進而可能導致啟動經驗不佳。 不過，Shell 也能夠依需求建立頁面，以回應導覽。 如需詳細資訊，請參閱[有效率的頁面載入](tabs.md#efficient-page-loading)。

## <a name="bottom-tabs"></a>底部索引標籤

`Tab` 物件會呈現為底部索引標籤，前提是在單一 `TabBar` 物件中有多個 `Tab` 物件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

索引標籤標題和圖示是在每個 `Tab` 物件上設定的，並顯示在底部的索引標籤上：

[![含底部索引標籤的 Shell 兩頁應用程式在 iOS 和 Android 上的螢幕擷取畫面](tabs-images/two-page-app-bottom-tabs.png "含底部索引標籤的 Shell 兩頁應用程式")](tabs-images/two-page-app-bottom-tabs-large.png#lightbox "含底部索引標籤的 Shell 兩頁應用程式")

或者，Shell 的隱含轉換運算子可以用來移除上一個範例中的 `ShellContent` 和 `Tab` 物件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <views:CatsPage IconImageSource="cat.png" />
        <views:DogsPage IconImageSource="dog.png" />
    </TabBar>
</Shell>
```

這項隱含的轉換會自動將每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件包裝在 `ShellContent` 物件中，然後再一起包裝到 `Tab` 物件中。

> [!IMPORTANT]
> 在 Shell 應用程式中，屬於 `ShellContent` 物件子系的每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 都是在應用程式啟動期間建立的。 使用此方法新增其他 `ShellContent` 物件將會導致在應用程式啟動期間建立其他頁面，進而可能導致啟動經驗不佳。 不過，Shell 也能夠依需求建立頁面，以回應導覽。 如需詳細資訊，請參閱[有效率的頁面載入](tabs.md#efficient-page-loading)。

### <a name="tab-class"></a>Tab 類別

`Tab` 類別包含可控制索引標籤外觀和行為的下列屬性：

- `CurrentItem`，屬於 `ShellContent` 類型，這是選取的項目。
- `FlyoutDisplayOptions`，屬於 `FlyoutDisplayOptions` 類型，可定義項目及其子項目如何顯示在飛出視窗中。 預設值為 `AsSingleItem`。
- `FlyoutIcon`，屬於 `ImageSource` 類型，可定義將顯示在飛出視窗中的圖示。
- `Icon`，屬於 `ImageSource` 類型，可定義要顯示在不是飛出視窗的 Chrome 部分中的圖示。
- `IsChecked`，屬於 `boolean` 類型，可定義項目目前是否在飛出視窗中反白顯示。
- 型別為 `boolean` 的 `IsEnabled` 可定義是否可在 Chrome 中選取該項目。
- 型別為 `bool` 的 `IsTabStop` 會指出 TAB 導覽中是否包含 `Tab`。 其預設值為 `true`，而當其值為 `false` 時，不論是否設定 `TabIndex`，TAB 導覽基礎結構均會略過 `Tab`。
- `Items`，屬於 `IList<ShellContent>` 類型，可定義 `Tab` 中的所有內容。
- 型別為 `int` 的 `TabIndex` 屬性會指定當使用者按下 Tab 鍵來巡覽項目時，`Tab` 物件接收焦點的順序。 屬性的預設值為 0。
- `Title`，屬於 `string` 類型，這是在 UI 的索引標籤上顯示的標題。

## <a name="shell-content"></a>Shell 內容

每個 `Tab` 物件的子系都是一個 `ShellContent` 物件，其 `Content` 屬性設定為 [`ContentPage`](xref:Xamarin.Forms.ContentPage)：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

在每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件中，都可以導覽至其他 `ContentPage` 物件。 如需有關導覽的詳細資訊，請參閱 [Xamarin.Forms Shell 導覽](navigation.md)。

### <a name="shellcontent-class"></a>ShellContent 類別

`ShellContent` 類別包含可控制索引標籤內容外觀和行為的下列屬性：

- `Content`，屬於 `object` 類型，這是 `ShellContent` 的內容。
- `ContentTemplate`，屬於 `DataTemplate` 類型，這是用來動態擴充 `ShellContent` 內容的範本。
- `FlyoutIcon`，屬於 `ImageSource` 類型，可定義將顯示在飛出視窗中的圖示。
- `Icon`，屬於 `ImageSource` 類型，可定義要顯示在不是飛出視窗的 Chrome 部分中的圖示。
- `IsChecked`，屬於 `boolean` 類型，可定義項目目前是否在飛出視窗中反白顯示。
- `IsEnabled`，屬於 `boolean` 類型，可定義在 Chrome 中是否可以選取項目。
- `MenuItems`，屬於 `MenuItemCollection` 類型，這是在此 `ShellContent` 為呈現的頁面時，顯示在飛出視窗中的功能表項目。
- `Title`，屬於 `string` 類型，這是在 UI 中顯示的標題。

所有這些屬性都以 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件為後盾，也就是說，這些屬性可以是資料繫結的目標。

## <a name="bottom-and-top-tabs"></a>底部和頂端的索引標籤

當多個 `ShellContent` 物件出現在一個 `Tab` 物件中時，頂端索引標籤列會加入至底部索引標籤中，[`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件可以透過此索引標籤導覽：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Monkeys"
             Icon="monkey.png">
            <ShellContent>
                <views:MonkeysPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

這會導致下列螢幕擷取畫面中顯示的版面配置：

[![含頂端和底部索引標籤的 Shell 兩頁應用程式在 iOS 和 Android 上的螢幕擷取畫面](tabs-images/two-page-app-top-tabs.png "含頂端和底部索引標籤的 Shell 兩頁應用程式")](tabs-images/two-page-app-top-tabs-large.png#lightbox "含頂端和底部索引標籤的 Shell 兩頁應用程式")

或者，Shell 的隱含轉換運算子可以用來移除上一個範例中的 `ShellContent` 物件和第二個 `Tab` 物件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <views:CatsPage />
            <views:DogsPage />
        </Tab>
        <views:MonkeysPage IconImageSource="monkey.png" />
    </TabBar>
</Shell>
```

這項隱含的轉換會自動將 `MonkeysPage` 包裝在 `ShellContent` 物件中，然後再包裝到 `Tab` 物件中。 此外，`CatsPage` 和 `DogsPage` 會隱含地包裝在 `ShellContent` 物件中。

## <a name="efficient-page-loading"></a>有效率的頁面載入

在 Shell 應用程式中，`ShellContent` 物件中的每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件都是在應用程式啟動期間建立的，這可能會導致啟動經驗不佳。 但是，Shell 也允許依需求建立頁面，以回應導覽。 這可以藉由使用 `DataTemplate` 標記延伸模組，將每個 `ContentPage` 轉換成 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，然後將結果設定為 `ShellContent.ContentTemplate` 屬性值來完成：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">    
    <TabBar>
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png"
                          ContentTemplate="{DataTemplate views:CatsPage}" />
            <ShellContent Title="Dogs"
                          Icon="dog.png"
                          ContentTemplate="{DataTemplate views:DogsPage}" />
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png"
                      ContentTemplate="{DataTemplate views:MonkeysPage}" />
    </TabBar>    
</Shell>
```

此 XAML 會建立並顯示 `CatsPage`，因為它是子類別化 `Shell` 物件中宣告之內容的第一個項目。 透過底部索引標籤可以導覽至 `CatsPage` 和 `MonkeysPage`，而且只有在使用者導覽至這些項目時，才會建立這些頁面。 這種方法的優點是，可避免啟動經驗不佳，因為頁面是依需求建立以回應導覽的，而不是在應用程式啟動時建立。

## <a name="tab-appearance"></a>索引標籤外觀

`Shell` 類別會定義可控制索引標籤外觀的下列附加屬性：

- 型別為 `Color` 的 `TabBarBackgroundColor`，用於定義索引標籤列的背景色彩。 如果未設定屬性，則會使用 `BackgroundColor` 屬性值。
- 型別為 `Color` 的 `TabBarDisabledColor`，用於定義索引標籤列的停用色彩。 如果未設定屬性，則會使用 `DisabledColor` 屬性值。
- 型別為 `Color` 的 `TabBarForegroundColor`，用於定義索引標籤列的前景色彩。 如果未設定屬性，則會使用 `ForegroundColor` 屬性值。
- 型別為 `Color` 的 `TabBarTitleColor`，用於定義索引標籤列的標題色彩。 如果未設定屬性，將會使用 `TitleColor` 屬性值。
- 型別為 `Color` 的 `TabBarUnselectedColor`，用於定義索引標籤列未選取的色彩。 如果未設定屬性，則會使用 `UnselectedColor` 屬性值。

所有這些屬性都以 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件為後盾，也就是說，這些屬性可以是資料繫結的目標，並且可以設定樣式。

下列範例顯示設定不同索引標籤色彩屬性的 XAML 樣式：

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.TabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.TabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.TabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

此外，您也可以使用階層式樣式表 (CSS) 設定索引標籤的樣式。 如需詳細資訊，請參閱 [Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
- [Xamarin.Forms Shell 導覽](navigation.md)
- [Xamarin.Forms CSS Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)

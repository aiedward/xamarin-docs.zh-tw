---
title: Xamarin.Forms Shell 索引標籤
description: 飛出視窗之後，Shell 應用程式中的下一個導覽層級是底部的索引標籤列。 或者，應用程式的導覽模式可以從底部索引標籤開始，而不使用飛出視窗。 在這兩種情況下，當底部索引標籤包含多個頁面時，頁面將可透過頂端索引標籤來導覽。
ms.prod: xamarin
ms.assetid: 318D81DB-E456-4E44-B083-36A27DBD9523
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4dcc1e30a89fc5a1e804ffa07272dd2b28b37c04
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101758363"
---
# <a name="xamarinforms-shell-tabs"></a>Xamarin.Forms Shell 索引標籤

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Shell 所提供的導覽體驗是以 flyouts 和索引標籤 Xamarin.Forms 為基礎。 Shell 應用程式中的最上層導覽是飛出視窗或底部索引標籤列，具體取決於應用程式的導覽要求。 當應用程式的導覽體驗以底部索引標籤開始時，子類別化物件的子系 [`Shell`](xref:Xamarin.Forms.Shell) 應該是代表底部索引標籤列的 [`TabBar`](xref:Xamarin.Forms.TabBar) 物件。

[`TabBar`](xref:Xamarin.Forms.TabBar)物件可以包含一或多個 [`Tab`](xref:Xamarin.Forms.Tab) 物件，每個物件都代表底部索引標籤列上的索引標籤 `Tab` 。 每個 `Tab` 物件都可以包含一個或多個 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 物件，每個 `ShellContent` 物件會顯示單一 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 當多個 `ShellContent` 物件出現在 `Tab` 物件中時，`ContentPage` 物件將可透過頂端索引標籤導覽。 在索引標籤中，您 `ContentPage` 可以流覽其他稱為詳細資料頁面的物件。

> [!IMPORTANT]
> 型別會 [`TabBar`](xref:Xamarin.Forms.TabBar) 停用飛出視窗。

## <a name="single-page"></a>單一頁面

您可以將物件加入至物件，藉以建立單一頁面 Shell 應用程式 [`Tab`](xref:Xamarin.Forms.Tab) [`TabBar`](xref:Xamarin.Forms.TabBar) 。 在 `Tab` 物件中， [`ShellContent`](xref:Xamarin.Forms.ShellContent) 物件應該設定為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
       <Tab>
           <ShellContent ContentTemplate="{DataTemplate views:CatsPage}" />
       </Tab>
    </TabBar>
</Shell>
```

此程式碼範例會產生下列單一頁面應用程式：

[![螢幕擷取畫面： iOS 和 Android 上的 Shell 單一頁面應用程式](tabs-images/single-page-app.png)](tabs-images/single-page-app-large.png#lightbox)

Shell 具有隱含的轉換運算子，可簡化 Shell 視覺階層，而不需要將其他檢視引進視覺化樹狀結構。 這是可能的，因為子類別化 `Shell` 物件只能包含 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 物件或 [`TabBar`](xref:Xamarin.Forms.TabBar) 物件，而 [`Tab`](xref:Xamarin.Forms.Tab) 該物件只能包含物件，而這些物件只可包含物件 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 。 這些隱含轉換運算子可以用來移除 `Tab` 上一個範例中的物件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Tab>
        <ShellContent ContentTemplate="{DataTemplate views:CatsPage}" />
    </Tab>
</Shell>
```

這項隱含的轉換會自動將物件包裝在物件 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 中，該物件 [`Tab`](xref:Xamarin.Forms.Tab) 會包裝在 [`TabBar`](xref:Xamarin.Forms.TabBar) 物件中。

> [!IMPORTANT]
> 在 Shell 應用程式中，會依需求建立頁面，以回應導覽。 這是藉由使用 [`DataTemplate`](xref:Xamarin.Forms.Xaml.DataTemplateExtension) 標記延伸，將 [`ContentTemplate`](xref:Xamarin.Forms.ShellContent.ContentTemplate) 每個物件的屬性設定 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件來完成。

## <a name="bottom-tabs"></a>底部索引標籤

[`Tab`](xref:Xamarin.Forms.Tab) 物件會轉譯為底部索引標籤，前提是 `Tab` 在單一物件中有多個物件 [`TabBar`](xref:Xamarin.Forms.TabBar) ：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
       <Tab Title="Cats"
            Icon="cat.png">
           <ShellContent ContentTemplate="{DataTemplate views:CatsPage}" />
       </Tab>
       <Tab Title="Dogs"
            Icon="dog.png">
           <ShellContent ContentTemplate="{DataTemplate views:DogsPage}" />
       </Tab>
    </TabBar>
</Shell>
```

型別為 [`Title`](xref:Xamarin.Forms.BaseShellItem.Title) 的屬性 `string` 定義了索引標籤標題。 型別為 [`Icon`](xref:Xamarin.Forms.BaseShellItem.Icon) 的屬性會 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 定義 tab 圖示：

[![螢幕擷取畫面： iOS 和 Android 上有底部索引標籤的 Shell 兩頁應用程式](tabs-images/two-page-app-bottom-tabs.png)](tabs-images/two-page-app-bottom-tabs-large.png#lightbox)

當上有五個以上的索引標籤時， [`TabBar`](xref:Xamarin.Forms.TabBar) 會出現 **更多** 索引標籤，可用來存取其他索引標籤：

[![螢幕擷取畫面：在 iOS 和 Android 上有更多索引標籤的 Shell 應用程式](tabs-images/more-tabs.png)](tabs-images/more-tabs-large.png#lightbox)

此外，Shell 的隱含轉換運算子可以用來移除 [`ShellContent`](xref:Xamarin.Forms.ShellContent) [`Tab`](xref:Xamarin.Forms.Tab) 上一個範例中的和物件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
       <ShellContent Title="Cats"
                     Icon="cat.png"
                     ContentTemplate="{DataTemplate views:CatsPage}" />
       <ShellContent Title="Dogs"
                     Icon="dog.png"
                     ContentTemplate="{DataTemplate views:DogsPage}" />
    </TabBar>
</Shell>
```

這項隱含的轉換會自動包裝 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 物件中的每個物件 [`Tab`](xref:Xamarin.Forms.Tab) 。

> [!IMPORTANT]
> 在 Shell 應用程式中，會依需求建立頁面，以回應導覽。 這是藉由使用 [`DataTemplate`](xref:Xamarin.Forms.Xaml.DataTemplateExtension) 標記延伸，將 [`ContentTemplate`](xref:Xamarin.Forms.ShellContent.ContentTemplate) 每個物件的屬性設定 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件來完成。

## <a name="bottom-and-top-tabs"></a>底部和頂端的索引標籤

當物件中有一個以上的物件時，會將頂端索引標籤列 [`ShellContent`](xref:Xamarin.Forms.ShellContent) [`Tab`](xref:Xamarin.Forms.Tab) 新增至底部索引標籤，以供 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件導覽：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
       <Tab Title="Domestic"
            Icon="paw.png">
           <ShellContent Title="Cats"
                         ContentTemplate="{DataTemplate views:CatsPage}" />
           <ShellContent Title="Dogs"
                         ContentTemplate="{DataTemplate views:DogsPage}" />
       </Tab>
       <Tab Title="Monkeys"
            Icon="monkey.png">
           <ShellContent ContentTemplate="{DataTemplate views:MonkeysPage}" />
       </Tab>
    </TabBar>
</Shell>
```

這會導致下列螢幕擷取畫面中顯示的版面配置：

[![IOS 和 Android 上具有頂端和底部索引標籤的 Shell 兩頁應用程式螢幕擷取畫面](tabs-images/two-page-app-top-tabs.png "具有頂端和底部索引標籤的 Shell 兩頁應用程式")](tabs-images/two-page-app-top-tabs-large.png#lightbox "具有頂端和底部索引標籤的 Shell 兩頁應用程式")

此外，Shell 的隱含轉換運算子可以用來移除 [`Tab`](xref:Xamarin.Forms.Tab) 上一個範例中的第二個物件：

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

這項隱含的轉換會自動將第三個 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 物件包裝在 [`Tab`](xref:Xamarin.Forms.Tab) 物件中。

## <a name="tab-appearance"></a>索引標籤外觀

[`Shell`](xref:Xamarin.Forms.Shell)類別會定義可控制索引標籤外觀的下列附加屬性：

- [`TabBarBackgroundColor`](xref:Xamarin.Forms.Shell.TabBarBackgroundColorProperty)型別為的，定義索引標籤列的 [`Color`](xref:Xamarin.Forms.Color) 背景色彩。 如果未設定屬性，則會使用 `BackgroundColor` 屬性值。
- [`TabBarDisabledColor`](xref:Xamarin.Forms.Shell.TabBarDisabledColorProperty)型別為的，定義索引卷 [`Color`](xref:Xamarin.Forms.Color) 標列的停用色彩。 如果未設定屬性，則會使用 `DisabledColor` 屬性值。
- [`TabBarForegroundColor`](xref:Xamarin.Forms.Shell.TabBarForegroundColorProperty)型別為的，定義索引標籤列的 [`Color`](xref:Xamarin.Forms.Color) 前景色彩。 如果未設定屬性，則會使用 `ForegroundColor` 屬性值。
- [`TabBarTitleColor`](xref:Xamarin.Forms.Shell.TabBarTitleColorProperty)型別為的，定義索引標籤列的 [`Color`](xref:Xamarin.Forms.Color) 標題色彩。 如果未設定屬性，將會使用 `TitleColor` 屬性值。
- [`TabBarUnselectedColor`](xref:Xamarin.Forms.Shell.TabBarUnselectedColorProperty)型別為的，定義索引卷 [`Color`](xref:Xamarin.Forms.Color) 標欄的未選取色彩。 如果未設定屬性，則會使用 `UnselectedColor` 屬性值。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示這些屬性可以是資料系結的目標，並且具有樣式。

下列範例顯示的 XAML 樣式會設定不同的索引標籤列色彩屬性：

```xaml
<Style TargetType="TabBar">
    <Setter Property="Shell.TabBarBackgroundColor"
            Value="CornflowerBlue" />
    <Setter Property="Shell.TabBarTitleColor"
            Value="Black" />
    <Setter Property="Shell.TabBarUnselectedColor"
            Value="AntiqueWhite" />
</Style>
```

此外，您也可以使用階層式樣式表 (CSS) 設定索引標籤的樣式。 如需詳細資訊，請參閱[ Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

## <a name="tab-selection"></a>索引標籤選項

第一次執行使用索引標籤列的 Shell 應用程式時， [`Shell.CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) 會將屬性設定為子類別化物件中的第一個 [`Tab`](xref:Xamarin.Forms.Tab) 物件 [`Shell`](xref:Xamarin.Forms.Shell) 。 不過，您可以將屬性設定為另一個 `Tab`，如下列範例所示：

```xaml
<Shell ...
       CurrentItem="{x:Reference dogsItem}">
    <TabBar>
        <ShellContent Title="Cats"
                      Icon="cat.png"
                      ContentTemplate="{DataTemplate views:CatsPage}" />
        <ShellContent x:Name="dogsItem"
                      Title="Dogs"
                      Icon="dog.png"
                      ContentTemplate="{DataTemplate views:DogsPage}" />
    </TabBar>
</Shell>
```

這個範例會將 [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) 屬性設定為 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 名為的物件 `dogsItem` ，這會導致它被選取並顯示。 在此範例中，會使用隱含轉換來包裝物件中的每個 `ShellContent` 物件 [`Tab`](xref:Xamarin.Forms.Tab) 。

對等的 c # 程式碼而言，假設有一個名為的 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 物件 `dogsItem` ，就是：

```csharp
CurrentItem = dogsItem;
```

在此範例中， [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) 屬性是在子類別化 [`Shell`](xref:Xamarin.Forms.Shell) 類別中設定。 或者，您 `CurrentItem` 可以透過靜態屬性在任何類別中設定屬性 `Shell.Current` ：

```csharp
Shell.Current.CurrentItem = dogsItem;
```

## <a name="tabbar-and-tab-visibility"></a>TabBar 和定位字元可見度

依預設，Shell 應用程式中會顯示索引標籤列和索引標籤。 不過，您可以藉由將附加屬性設定為來隱藏索引標籤列 [`Shell.TabBarIsVisible`](xref:Xamarin.Forms.Shell.TabBarIsVisibleProperty) `false` 。

雖然您可以在子類別化物件上設定此屬性 [`Shell`](xref:Xamarin.Forms.Shell) ，但通常會在想要讓索引標籤列不可見的任何或物件上設定此屬性 [`ShellContent`](xref:Xamarin.Forms.ShellContent) [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```xaml
<TabBar>
   <Tab Title="Domestic"
        Icon="paw.png">
       <ShellContent Title="Cats"
                     ContentTemplate="{DataTemplate views:CatsPage}" />
       <ShellContent Shell.TabBarIsVisible="false"
                     Title="Dogs"
                     ContentTemplate="{DataTemplate views:DogsPage}" />
   </Tab>
   <Tab Title="Monkeys"
        Icon="monkey.png">
       <ShellContent ContentTemplate="{DataTemplate views:MonkeysPage}" />
   </Tab>
</TabBar>
```

在此範例中，選取上方的 [ **狗** ] 索引標籤時，會隱藏索引標籤列。

此外，您 [`Tab`](xref:Xamarin.Forms.Tab) 可以將可系結屬性設定 [`IsVisible`](xref:Xamarin.Forms.BaseShellItem.IsVisible) 為來隱藏物件 `false` ：

```xaml
<TabBar>
    <ShellContent Title="Cats"
                  Icon="cat.png"
                  ContentTemplate="{DataTemplate views:CatsPage}" />
    <ShellContent Title="Dogs"
                  Icon="dog.png"
                  ContentTemplate="{DataTemplate views:DogsPage}"
                  IsVisible="False" />
    <ShellContent Title="Monkeys"
                  Icon="monkey.png"
                  ContentTemplate="{DataTemplate views:MonkeysPage}" />
</TabBar>
```

在此範例中，會隱藏第二個索引標籤。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms Shell 導覽](navigation.md)
- [Xamarin.Forms CSS Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)

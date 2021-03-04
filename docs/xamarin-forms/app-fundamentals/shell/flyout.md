---
title: Xamarin.Forms Shell 飛出視窗
description: 飛出視窗是 Shell 應用程式的選擇性根功能表，可透過圖示或從螢幕側邊刷來存取。 飛出視窗包含選擇性的標頭、飛出視窗專案、選擇性功能表項目和選擇性頁尾。
ms.prod: xamarin
ms.assetid: FEDE51EB-577E-4B3E-9890-B7C1A5E52516
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 82a8297d393e82119156509f20a7306d2ea34540
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757956"
---
# <a name="xamarinforms-shell-flyout"></a>Xamarin.Forms Shell 飛出視窗

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Shell 所提供的導覽體驗是以 flyouts 和索引標籤 Xamarin.Forms 為基礎。 飛出視窗是 Shell 應用程式的選擇性根功能表，可完全自訂。 您可以透過圖示或從螢幕側邊刷來存取它。 飛出視窗是由選擇性的標頭、飛出視窗專案、選擇性功能表項目和選擇性頁尾所組成：

![Shell 批註飛出視窗的螢幕擷取畫面](flyout-images/flyout-annotated.png)

## <a name="flyout-items"></a>飛出視窗項目

您可以將一或多個飛出視窗專案加入至飛出視窗，而且每個飛出視窗專案都是以 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 物件表示。 每個 `FlyoutItem` 物件都應該是子類別化物件的子系 [`Shell`](xref:Xamarin.Forms.Shell) 。 當飛出視窗標題不存在時，飛出視窗專案就會出現在飛出視窗的頂端。

下列範例會建立包含兩個飛出視窗專案的飛出視窗：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <FlyoutItem Title="Cats"
                Icon="cat.png">
       <Tab>
           <ShellContent ContentTemplate="{DataTemplate views:CatsPage}" />
       </Tab>
    </FlyoutItem>
    <FlyoutItem Title="Dogs"
                Icon="dog.png">
       <Tab>
           <ShellContent ContentTemplate="{DataTemplate views:DogsPage}" />
       </Tab>
    </FlyoutItem>
</Shell>
```

型別為 [`FlyoutItem.Title`](xref:Xamarin.Forms.BaseShellItem.Title) 的屬性 `string` 定義了飛出視窗專案的標題。 型別為 [`FlyoutItem.Icon`](xref:Xamarin.Forms.BaseShellItem.Icon) 的屬性 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 定義了飛出視窗專案的圖示：

[![螢幕擷取畫面：在 iOS 和 Android 上具有飛出視窗專案的 Shell 兩頁應用程式](flyout-images/two-page-app-flyout.png)](flyout-images/two-page-app-flyout-large.png#lightbox)

在此範例中，每個 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 物件只能透過飛出視窗專案來存取，而不能透過索引標籤存取。 這是因為依預設，只有在飛出視窗專案包含一個以上的索引標籤時，才會顯示索引標籤。

> [!IMPORTANT]
> 在 Shell 應用程式中，會依需求建立頁面，以回應導覽。 這是藉由使用 [`DataTemplate`](xref:Xamarin.Forms.Xaml.DataTemplateExtension) 標記延伸，將 [`ContentTemplate`](xref:Xamarin.Forms.ShellContent.ContentTemplate) 每個物件的屬性設定 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 為 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件來完成。

Shell 具有隱含的轉換運算子，可簡化 Shell 視覺階層，而不需要將其他檢視引進視覺化樹狀結構。 這是可能的，因為子類別化 [`Shell`](xref:Xamarin.Forms.Shell) 物件只能包含 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 物件或 [`TabBar`](xref:Xamarin.Forms.TabBar) 物件，而 [`Tab`](xref:Xamarin.Forms.Tab) 該物件只能包含物件，而這些物件只可包含物件 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 。 這些隱含轉換運算子可以用來移除 `FlyoutItem` `Tab` 上一個範例中的和物件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
   <ShellContent Title="Cats"
                 Icon="cat.png"
                 ContentTemplate="{DataTemplate views:CatsPage}" />
   <ShellContent Title="Dogs"
                 Icon="dog.png"
                 ContentTemplate="{DataTemplate views:DogsPage}" />
</Shell>
```

這項隱含的轉換會自動 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 將物件中的每個物件包裝在物件中 [`Tab`](xref:Xamarin.Forms.Tab) [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 。

> [!NOTE]
> 子類別化 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 物件中的所有物件 [`Shell`](xref:Xamarin.Forms.Shell) 都會自動加入至 `Shell.FlyoutItems` 集合，該集合會定義要顯示在飛出視窗中的專案清單。

### <a name="flyout-display-options"></a>飛出視窗顯示選項

[`FlyoutItem.FlyoutDisplayOptions`](xref:Xamarin.Forms.ShellGroupItem.FlyoutDisplayOptions)屬性會設定飛出視窗專案和其子系在飛出視窗中顯示的方式。 這個屬性應該設定為 [`FlyoutDisplayOptions`](xref:Xamarin.Forms.FlyoutDisplayOptions) 列舉成員：

- `AsSingleItem`，指出項目將顯示為單一項目。 這是屬性的預設值 [`FlyoutDisplayOptions`](xref:Xamarin.Forms.ShellGroupItem.FlyoutDisplayOptions) 。
- `AsMultipleItems`，指出項目及其子系都會在飛出視窗中顯示為一組項目。

您 [`Tab`](xref:Xamarin.Forms.Tab) [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 可以藉由將屬性設定為，來顯示中每個物件的飛出視窗專案 [`FlyoutItem.FlyoutDisplayOptions`](xref:Xamarin.Forms.ShellGroupItem.FlyoutDisplayOptions) `AsMultipleItems` ：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       FlyoutHeaderBehavior="CollapseOnScroll"
       x:Class="Xaminals.AppShell">

    <FlyoutItem FlyoutDisplayOptions="AsMultipleItems">
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
        <ShellContent Title="Elephants"
                      Icon="elephant.png"
                      ContentTemplate="{DataTemplate views:ElephantsPage}" />  
        <ShellContent Title="Bears"
                      Icon="bear.png"
                      ContentTemplate="{DataTemplate views:BearsPage}" />
    </FlyoutItem>

    <ShellContent Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />    
</Shell>
```

在此範例中，會針對物件的 [`Tab`](xref:Xamarin.Forms.Tab) 子系 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) ，以及物件的子物件，建立飛出視窗專案 [`ShellContent`](xref:Xamarin.Forms.ShellContent) `FlyoutItem` 。 這是因為每個 `ShellContent` 物件 (其為 `FlyoutItem` 物件的子系) 都會自動包裝於 `Tab` 物件中。 此外，也會為最後一個物件建立飛出視窗專案 `ShellContent` ，此物件會自動包裝在 `Tab` 物件中，然後再放在 `FlyoutItem` 物件中。

> [!NOTE]
> 當包含一個以上的物件時，會顯示索引標籤 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) [`ShellContent`](xref:Xamarin.Forms.ShellContent) 。

這會產生下列飛出視窗項目：

[![螢幕擷取畫面： iOS 和 Android 上包含 FlyoutItem 物件的飛出視窗](flyout-images/flyout-reduced.png "包含 FlyoutItem 物件的 Shell 飛出視窗")](flyout-images/flyout-reduced-large.png#lightbox "包含 FlyoutItem 物件的 Shell 飛出視窗")

### <a name="define-flyoutitem-appearance"></a>定義 FlyoutItem 外觀

將 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 附加屬性設定為，即可自訂每個的外觀 [`Shell.ItemTemplate`](xref:Xamarin.Forms.Shell.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<Shell ...>
    ...
    <Shell.ItemTemplate>
        <DataTemplate>
            <Grid ColumnDefinitions="0.2*,0.8*">
                <Image Source="{Binding FlyoutIcon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Title}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.ItemTemplate>
</Shell>
```

此範例會以斜體顯示每個物件的標題 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) ：

[![IOS 和 Android 上樣板化 FlyoutItem 物件的螢幕擷取畫面](flyout-images/flyoutitem-templated.png)](flyout-images/flyoutitem-templated-large.png#lightbox)

因為 [`Shell.ItemTemplate`](xref:Xamarin.Forms.Shell.ItemTemplate) 是附加屬性，所以可以將不同的範本附加至特定 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 物件。

> [!NOTE]
> Shell 會提供的 [`Title`](xref:Xamarin.Forms.BaseShellItem.Title) 和 [`FlyoutIcon`](xref:Xamarin.Forms.BaseShellItem.FlyoutIcon) 屬性至的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) `ItemTemplate` 。

此外，Shell 還包含三個會自動套用至物件的樣式類別 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 。 如需詳細資訊，請參閱 [Style FlyoutItem 和 MenuItem 物件](#style-flyoutitem-and-menuitem-objects)。

### <a name="default-template-for-flyoutitems"></a>FlyoutItems 的預設範本

每個使用的預設值如下 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 所示：

```xaml
<DataTemplate x:Key="FlyoutTemplate">
    <Grid x:Name="FlyoutItemLayout"
          HeightRequest="{x:OnPlatform Android=50}"
          ColumnSpacing="{x:OnPlatform UWP=0}"
          RowSpacing="{x:OnPlatform UWP=0}">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroupList>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Selected">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor"
                                    Value="{x:OnPlatform Android=#F2F2F2, iOS=#F2F2F2}" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateGroupList>
        </VisualStateManager.VisualStateGroups>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="{x:OnPlatform Android=54, iOS=50, UWP=Auto}" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Image x:Name="FlyoutItemImage"
               Source="{Binding FlyoutIcon}"
               VerticalOptions="Center"
               HorizontalOptions="{x:OnPlatform Default=Center, UWP=Start}"
               HeightRequest="{x:OnPlatform Android=24, iOS=22, UWP=16}"
               WidthRequest="{x:OnPlatform Android=24, iOS=22, UWP=16}">
            <Image.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="UWP"
                            Value="12,0,12,0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Image.Margin>
        </Image>
        <Label x:Name="FlyoutItemLabel"
               Grid.Column="1"
               Text="{Binding Title}"
               FontSize="{x:OnPlatform Android=14, iOS=Small}"
               HorizontalOptions="{x:OnPlatform UWP=Start}"
               HorizontalTextAlignment="{x:OnPlatform UWP=Start}"
               FontAttributes="{x:OnPlatform iOS=Bold}"
               VerticalTextAlignment="Center">
            <Label.TextColor>
                <OnPlatform x:TypeArguments="Color">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="#D2000000" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.TextColor>
            <Label.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="20, 0, 0, 0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.Margin>
            <Label.FontFamily>
                <OnPlatform x:TypeArguments="x:String">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="sans-serif-medium" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.FontFamily>
        </Label>
    </Grid>
</DataTemplate>
```

您可以使用這個範本做為現有飛出視窗版面配置的改變基礎，也會顯示針對飛出視窗專案所執行的視覺狀態。

此外， [`Grid`](xref:Xamarin.Forms.Grid) 、 [`Image`](xref:Xamarin.Forms.Image) 和 [`Label`](xref:Xamarin.Forms.Label) 元素都具有 `x:Name` 值，因此可以使用視覺狀態管理員來設定目標。 如需詳細資訊，請參閱 [設定多個元素的狀態](~/xamarin-forms/user-interface/visual-state-manager.md#set-state-on-multiple-elements)。

> [!NOTE]
> 相同的範本也可用於 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件。

### <a name="replace-flyout-content"></a>取代飛出視窗內容

您可以將可系結屬性設定為，以選擇性地將可系結屬性取代為您自己的內容，以將飛出視窗內容取代為 `Shell.FlyoutContent` `object` ：

```xaml
<Shell ...
       x:Name="shell">
    ...
    <Shell.FlyoutContent>
        <CollectionView BindingContext="{x:Reference shell}"
                        IsGrouped="True"
                        ItemsSource="{Binding FlyoutItems}">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <Label Text="{Binding Title}"
                           TextColor="White"
                           FontSize="Large" />
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </Shell.FlyoutContent>
</Shell>
```

在此範例中，會將飛出視窗內容取代為 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，以顯示集合中每個專案的標題 `FlyoutItems` 。

> [!NOTE]
> `FlyoutItems`類別中的屬性 [`Shell`](xref:Xamarin.Forms.Shell) 是飛出視窗專案的唯讀集合。

或者，您可以藉由將可系結屬性設定為，來定義飛出視窗內容 `Shell.FlyoutContentTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<Shell ...
       x:Name="shell">
    ...
    <Shell.FlyoutContentTemplate>
        <DataTemplate>
            <CollectionView BindingContext="{x:Reference shell}"
                            IsGrouped="True"
                            ItemsSource="{Binding FlyoutItems}">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Label Text="{Binding Title}"
                               TextColor="White"
                               FontSize="Large" />
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </DataTemplate>
    </Shell.FlyoutContentTemplate>
</Shell>
```

> [!IMPORTANT]
> 飛出視窗標題可以選擇性地顯示在您的飛出視窗內容上方，而飛出視窗頁尾可以選擇性地顯示在您的飛出視窗內容下方。 如果您的飛出視窗內容是可滾動的，Shell 將會嘗試接受您的飛出視窗標題的滾動行為。

## <a name="menu-items"></a>功能表項目

您可以選擇性地將功能表項目加入至飛出視窗，每個功能表項目都由一個物件來表示 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 。 飛出視窗上 `MenuItem` 物件的位置，取決於其在 Shell 視覺階層中的宣告順序。 因此， `MenuItem` 在物件之前宣告 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 的任何物件都會出現在 `FlyoutItem` 飛出視窗中的物件之前，並且在物件之後宣告的任何 `MenuItem` 物件 `FlyoutItem` 都會出現在 `FlyoutItem` 飛出視窗中的物件之後。

[`MenuItem`](xref:Xamarin.Forms.MenuItem)類別具有 [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) 事件和 [`Command`](xref:Xamarin.Forms.MenuItem.Command) 屬性。 因此，`MenuItem` 物件會啟用執行動作以回應點選 `MenuItem` 的案例。

[`MenuItem`](xref:Xamarin.Forms.MenuItem) 您可以將物件加入至飛出視窗，如下列範例所示：

```xaml
<Shell ...>
    ...            
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />    
</Shell>
```

此範例會將 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件加入至飛出視窗中的所有飛出視窗專案底下：

[![螢幕擷取畫面：在 iOS 和 Android 上包含 MenuItem 物件的飛出視窗](flyout-images/flyout.png)](flyout-images/flyout-large.png#lightbox)

[`MenuItem`](xref:Xamarin.Forms.MenuItem)物件 `ICommand` 會執行名為的 `HelpCommand` ，這會 `CommandParameter` 在系統網頁瀏覽器中開啟屬性所指定的 URL。

> [!NOTE]
> [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)每個都繼承自子類別化的 [`MenuItem`](xref:Xamarin.Forms.MenuItem) [`Shell`](xref:Xamarin.Forms.Shell) 物件。

### <a name="define-menuitem-appearance"></a>定義 MenuItem 的外觀

將 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 附加屬性設定為，即可自訂每個的外觀 [`Shell.MenuItemTemplate`](xref:Xamarin.Forms.Shell.MenuItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid ColumnDefinitions="0.2*,0.8*">
                <Image Source="{Binding Icon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Text}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.MenuItemTemplate>
    ...
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />  
</Shell>
```

此範例會將附加 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 至每個 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件，以斜體顯示 `MenuItem` 物件的標題：

[![IOS 和 Android 上樣板化 MenuItem 物件的螢幕擷取畫面](flyout-images/menuitem-templated.png)](flyout-images/menuitem-templated-large.png#lightbox)

因為 [`Shell.MenuItemTemplate`](xref:Xamarin.Forms.Shell.ItemTemplate) 是附加屬性，所以可以將不同的範本附加至特定 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件。

> [!NOTE]
> Shell 會提供的 [`Text`](xref:Xamarin.Forms.MenuItem.Text) 和 [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) 屬性至的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) [`MenuItemTemplate`](xref:Xamarin.Forms.Shell.MenuItemTemplate) 。 您也可以使用 `Title` 取代 `Text` 和取代， `Icon` `IconImageSource` 讓您針對功能表項目和飛出視窗專案重複使用相同的範本。

物件的預設範本 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 也可用於 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件。 如需詳細資訊，請參閱 [FlyoutItems 的預設範本](#default-template-for-flyoutitems)。

## <a name="style-flyoutitem-and-menuitem-objects"></a>Style FlyoutItem 和 MenuItem 物件

Shell 包含三個樣式的類別，這些類別會自動套用至 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 和 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件。 樣式類別名稱為 `FlyoutItemLabelStyle` 、 `FlyoutItemImageStyle` 和 `FlyoutItemLayoutStyle` 。

下列 XAML 顯示為這些樣式類別定義樣式的範例：

```xaml
<Style TargetType="Label"
       Class="FlyoutItemLabelStyle">
    <Setter Property="TextColor"
            Value="Black" />
    <Setter Property="HeightRequest"
            Value="100" />
</Style>

<Style TargetType="Image"
       Class="FlyoutItemImageStyle">
    <Setter Property="Aspect"
            Value="Fill" />
</Style>

<Style TargetType="Layout"
       Class="FlyoutItemLayoutStyle"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Teal" />
</Style>
```

這些樣式會自動套用至 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 和 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件，而不需要將其 [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) 屬性設定為樣式類別名稱。

此外，您可以定義自訂樣式類別，並將其套用至 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 和 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件。 如需樣式類別的詳細資訊，請參閱[ Xamarin.Forms 樣式類別](~/xamarin-forms/user-interface/styles/xaml/style-class.md)。

## <a name="flyout-header"></a>飛出視窗標題

飛出視窗標題是在飛出視窗頂端選擇性顯示的內容，其外觀是由 `object` 可以使用可系結屬性設定的所定義 [`Shell.FlyoutHeader`](xref:Xamarin.Forms.Shell.FlyoutHeader) ：

```xaml
<Shell ...>
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
</Shell>
```

下列範例中會顯示 `FlyoutHeader` 型別：

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Xaminals.Controls.FlyoutHeader"
             HeightRequest="200">
    <Grid BackgroundColor="Black">
        <Image Aspect="AspectFill"
               Source="xamarinstore.jpg"
               Opacity="0.6" />
        <Label Text="Animals"
               TextColor="White"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentView>
```

這會產生下列飛出視窗標題：

![飛出視窗標題的螢幕擷取畫面](flyout-images/flyout-header.png)

或者，您可以藉由將可系結屬性設定為，來定義飛出視窗標題的外觀 [`Shell.FlyoutHeaderTemplate`](xref:Xamarin.Forms.Shell.FlyoutHeaderTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<Shell ...>
    <Shell.FlyoutHeaderTemplate>
        <DataTemplate>
            <Grid BackgroundColor="Black"
                  HeightRequest="200">
                <Image Aspect="AspectFill"
                       Source="xamarinstore.jpg"
                       Opacity="0.6" />
                <Label Text="Animals"
                       TextColor="White"
                       FontAttributes="Bold"
                       HorizontalTextAlignment="Center"
                       VerticalTextAlignment="Center" />
            </Grid>            
        </DataTemplate>
    </Shell.FlyoutHeaderTemplate>
</Shell>
```

根據預設，飛出視窗標題將會固定在飛出視窗中，而如果有足夠的項目，以下內容就會捲動。 不過，您可以藉由將可系結 [`Shell.FlyoutHeaderBehavior`](xref:Xamarin.Forms.Shell.FlyoutHeaderBehavior) 屬性設定為其中一個列舉成員，來變更此行為 [`FlyoutHeaderBehavior`](xref:Xamarin.Forms.FlyoutHeaderBehavior) ：

- `Default`：指出將使用平台的預設行為。 這是屬性的預設值 [`FlyoutHeaderBehavior`](xref:Xamarin.Forms.Shell.FlyoutHeaderBehavior) 。
- `Fixed`：指出飛出視窗標題隨時都會維持可見且不變。
- `Scroll`：指出當使用者捲動項目時，飛出視窗標題會捲動到檢視之外。
- `CollapseOnScroll`：指出當使用者捲動項目時，飛出視窗標題會摺疊到只剩標題。

下列範例示範當使用者捲動時，如何摺疊飛出視窗標題：

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

## <a name="flyout-footer"></a>飛出視窗頁尾

飛出視窗頁尾是選擇性顯示在飛出視窗底部的內容，其外觀是由 `object` 可以使用可系結屬性設定的所定義 `Shell.FlyoutFooter` ：

```xaml
<Shell ...>
    <Shell.FlyoutFooter>
        <controls:FlyoutFooter />
    </Shell.FlyoutFooter>
</Shell>
```

下列範例中會顯示 `FlyoutFooter` 型別：

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="Xaminals.Controls.FlyoutFooter">
    <StackLayout>
        <Label Text="Xaminals"
               TextColor="GhostWhite"
               FontAttributes="Bold"
               HorizontalOptions="Center" />
        <Label Text="{Binding Source={x:Static sys:DateTime.Now}, StringFormat='{0:MMMM dd, yyyy}'}"
               TextColor="GhostWhite"
               HorizontalOptions="Center" />
    </StackLayout>
</ContentView>
```

這會產生下列飛出視窗頁尾：

![飛出視窗頁尾的螢幕擷取畫面](flyout-images/flyout-footer.png "飛出視窗頁尾")

或者，您可以藉由將屬性設定為，來定義飛出視窗頁尾的外觀 `Shell.FlyoutFooterTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<Shell ...>
    <Shell.FlyoutFooterTemplate>
        <DataTemplate>
            <StackLayout>
                <Label Text="Xaminals"
                       TextColor="GhostWhite"
                       FontAttributes="Bold"
                       HorizontalOptions="Center" />
                <Label Text="{Binding Source={x:Static sys:DateTime.Now}, StringFormat='{0:MMMM dd, yyyy}'}"
                       TextColor="GhostWhite"
                       HorizontalOptions="Center" />
            </StackLayout>
        </DataTemplate>
    </Shell.FlyoutFooterTemplate>
</Shell>
```

飛出視窗頁尾會固定到飛出視窗的底部，而且可以是任何高度。 此外，頁尾也永遠不會遮住任何功能表項目。

## <a name="flyout-width-and-height"></a>飛出視窗寬度和高度

藉由將 `Shell.FlyoutWidth` 和 `Shell.FlyoutHeight` 附加屬性設定為值，即可自訂飛出視窗的寬度和高度 `double` ：

```xaml
<Shell ...
       FlyoutWidth="400"
       FlyoutHeight="200">
    ...
</Shell>
```

這可讓您在整個畫面上展開飛出視窗，或減少飛出視窗的高度，使其不會遮蔽索引標籤列。

## <a name="flyout-icon"></a>飛出視窗圖示

根據預設，Shell 應用程式有個漢堡圖示，按下圖示時，即會開啟飛出視窗。 您可以將類型的可系結 [`Shell.FlyoutIcon`](xref:Xamarin.Forms.Shell.FlyoutIcon) 屬性設定 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 為適當的圖示，以變更此圖示：

```xaml
<Shell ...
       FlyoutIcon="flyouticon.png">
    ...       
</Shell>
```

## <a name="flyout-background"></a>飛出視窗背景

您可以使用可系結屬性來設定飛出視窗的背景色彩 [`Shell.FlyoutBackgroundColor`](xref:Xamarin.Forms.Shell.FlyoutBackgroundColor) ：

```xaml
<Shell ...
       FlyoutBackgroundColor="AliceBlue">
    ...
</Shell>
```

> [!NOTE]
> [`Shell.FlyoutBackgroundColor`](xref:Xamarin.Forms.Shell.FlyoutBackgroundColor)也可以從級聯樣式表 (CSS) 來設定。 如需詳細資訊，請參閱[ Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

或者，您可以藉由將可系結屬性設定為，來指定飛出視窗的背景 [`Shell.FlyoutBackground`](xref:Xamarin.Forms.Shell.FlyoutBackground) [`Brush`](xref:Xamarin.Forms.Brush) ：

```xaml
<Shell ...
       FlyoutBackground="LightGray">
    ...
</Shell>
```

在此範例中，會以淺灰色繪製飛出視窗背景 [`SolidColorBrush`](xref:Xamarin.Forms.SolidColorBrush) 。

下列範例顯示如何將飛出視窗背景設定為 [`LinearGradientBrush`](xref:Xamarin.Forms.LinearGradientBrush) ：

```xaml
<Shell ...>
    <Shell.FlyoutBackground>
        <LinearGradientBrush StartPoint="0,0"
                             EndPoint="1,1">
            <GradientStop Color="#8A2387"
                          Offset="0.1" />
            <GradientStop Color="#E94057"
                          Offset="0.6" />
            <GradientStop Color="#F27121"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Shell.FlyoutBackground>
    ...
</Shell>
```

如需筆刷的詳細資訊，請參閱[ Xamarin.Forms 筆刷](~/xamarin-forms/user-interface/brushes/index.md)。

## <a name="flyout-background-image"></a>飛出視窗背景影像

飛出視窗可以有選擇性的背景影像，它會出現在飛出視窗標題下方，以及任何飛出視窗專案、功能表項目和飛出視窗頁尾的後方。 您可以將類型的可系結 [`FlyoutBackgroundImage`](xref:Xamarin.Forms.Shell.FlyoutBackgroundImage) 屬性設定為檔案 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 、內嵌資源、URI 或資料流程，藉以指定背景影像。

您可以設定背景影像的外觀比例，方法是將類型的可系結 [`FlyoutBackgroundImageAspect`](xref:Xamarin.Forms.Shell.FlyoutBackgroundImageAspect) 屬性設定 [`Aspect`](xref:Xamarin.Forms.Aspect) 為其中一個 `Aspect` 列舉成員：

- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -裁剪影像，使其填滿顯示區域，同時保留外觀比例。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -letterboxes 影像（如有必要），讓影像符合顯示區域，並根據影像的寬度或高度，將空格新增至頂端/底部或側邊。 這是屬性的預設值 [`FlyoutBackgroundImageAspect`](xref:Xamarin.Forms.Shell.FlyoutBackgroundImageAspect) 。
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -將影像完全填滿，完全填滿顯示區域。 這可能會導致影像失真。

下列範例會示範如何設定這些屬性：

```xaml
<Shell ...
       FlyoutBackgroundImage="photo.jpg"
       FlyoutBackgroundImageAspect="AspectFill">
    ...
</Shell>
```

這會導致背景影像出現在飛出視窗的飛出視窗標題下方：

![飛出視窗背景影像的螢幕擷取畫面](flyout-images/flyout-backgroundimage.png)

## <a name="flyout-backdrop"></a>飛出視窗的背景

您可以藉由將附加屬性設定為，來指定飛出視窗的背景（飛出視窗重迭的外觀） [`Shell.FlyoutBackdrop`](xref:Xamarin.Forms.Shell.FlyoutBackdrop) [`Brush`](xref:Xamarin.Forms.Brush) ：

```xaml
<Shell ...
       FlyoutBackdrop="Silver">
    ...
</Shell>
```

在此範例中，會以銀級繪製飛出視窗的背景 [`SolidColorBrush`](xref:Xamarin.Forms.SolidColorBrush) 。

> [!IMPORTANT]
> [`FlyoutBackdrop`](xref:Xamarin.Forms.Shell.FlyoutBackdrop)附加屬性可以在任何 Shell 專案上設定，但只適用于在 `Shell` 、或物件上設定時 `FlyoutItem` `TabBar` 。

下列範例顯示如何將飛出視窗的背景設定為 [`LinearGradientBrush`](xref:Xamarin.Forms.LinearGradientBrush) ：

```xaml
<Shell ...>
    <Shell.FlyoutBackdrop>
        <LinearGradientBrush StartPoint="0,0"
                             EndPoint="1,1">
            <GradientStop Color="#8A2387"
                          Offset="0.1" />
            <GradientStop Color="#E94057"
                          Offset="0.6" />
            <GradientStop Color="#F27121"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Shell.FlyoutBackdrop>
    ...
</Shell>
```

如需筆刷的詳細資訊，請參閱[ Xamarin.Forms 筆刷](~/xamarin-forms/user-interface/brushes/index.md)。

## <a name="flyout-behavior"></a>飛出視窗行為

飛出視窗可透過漢堡圖示或從螢幕側邊撥動來存取。 不過，您可以藉由將 [`Shell.FlyoutBehavior`](xref:Xamarin.Forms.Shell.FlyoutBehavior) 附加屬性設定為其中一個列舉成員來變更此行為 [`FlyoutBehavior`](xref:Xamarin.Forms.FlyoutBehavior) ：

- `Disabled`：指出使用者無法開啟飛出視窗。
- `Flyout`：指出使用者可以開啟和關閉飛出視窗。 這是屬性的預設值 [`FlyoutBehavior`](xref:Xamarin.Forms.Shell.FlyoutBehavior) 。
- `Locked`：指出使用者無法關閉飛出視窗，而且它不會重疊內容。

下列範例示範如何停用飛出視窗：

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

> [!NOTE]
> `FlyoutBehavior` 附加屬性可設定於 `Shell`、`FlyoutItem`、`ShellContent` 及頁面物件上，以覆寫預設的飛出視窗行為。

## <a name="flyout-vertical-scroll"></a>飛出視窗垂直捲動

根據預設，當飛出視窗專案不符合飛出視窗時，可以垂直捲動飛出視窗。 藉由將可系結屬性設定 [`Shell.FlyoutVerticalScrollMode`](xref:Xamarin.Forms.Shell.FlyoutVerticalScrollMode) 為其中一個列舉成員，即可變更此行為 [`ScrollMode`](xref:Xamarin.Forms.ScrollMode) ：

- `Disabled` –表示將停用垂直捲動。
- `Enabled` –表示將啟用垂直捲動。
- `Auto` –表示如果飛出視窗中的飛出視窗專案無法容納，則會啟用垂直捲動。 這是屬性的預設值 [`FlyoutVerticalScrollMode`](xref:Xamarin.Forms.Shell.FlyoutVerticalScrollMode) 。

下列範例顯示如何停用垂直捲動：

```xaml
<Shell ...
       FlyoutVerticalScrollMode="Disabled">
    ...
</Shell>
```

## <a name="flyoutitem-tab-order"></a>FlyoutItem 定位順序

依預設，物件的定位順序 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 會與它們在 XAML 中的順序相同，或以程式設計方式加入至子集合。 此順序為透過鍵盤巡覽 `FlyoutItem` 物件的順序，而這個預設順序通常也是最佳順序。

您可以設定屬性來變更預設的定位順序 [`FlyoutItem.TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex) ，指出 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 當使用者按下 tab 鍵流覽專案時，物件接收焦點的順序。 屬性的預設值為 0，而且可以設定為任何 `int` 值。

使用預設定位順序或設定屬性時，適用下列規則 [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex) ：

- [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 具有 [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex) 等於0的物件會根據其在 XAML 或子集合中的宣告順序，新增至定位順序。
- [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem)[`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex)大於0的物件會根據其值新增至定位順序 `TabIndex` 。
- [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem)[`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex)小於0的物件會加入至定位順序，並顯示在任何零值之前。
- 的衝突 [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex) 是由宣告順序來解決。

定義定位順序之後，按下 Tab 鍵會以遞增順序將焦點放在 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 物件上 [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex) ，並在到達最後一個物件之後換行一開始。

除了設定物件的定位順序之外 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) ，可能還需要從定位順序中排除某些物件。 您可以使用屬性來達成此目的 [`FlyoutItem.IsTabStop`](xref:Xamarin.Forms.BaseShellItem.IsTabStop) ，指出是否要在索引標籤 `FlyoutItem` 導覽中包含。 其預設值為 `true` ，而且當其值為時，索引標籤 `false` `FlyoutItem` 流覽基礎結構會忽略，而不論 [`TabIndex`](xref:Xamarin.Forms.BaseShellItem.TabIndex) 是否已設定。

## <a name="flyoutitem-selection"></a>FlyoutItem 選取專案

當第一次執行使用飛出視窗的 Shell 應用程式時， [`Shell.CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) 屬性會設定為子類別化物件中的第一個 `FlyoutItem` 物件 [`Shell`](xref:Xamarin.Forms.Shell) 。 不過，您可以將屬性設定為另一個 `FlyoutItem`，如下列範例所示：

```xaml
<Shell ...
       CurrentItem="{x:Reference aboutItem}">
    <FlyoutItem FlyoutDisplayOptions="AsMultipleItems">
        ...
    </FlyoutItem>
    <ShellContent x:Name="aboutItem"
                  Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />
</Shell>
```

這個範例會將 [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) 屬性設定為 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 名為的物件 `aboutItem` ，這會導致它被選取並顯示。 在此範例中，會使用隱含轉換將物件包裝在物件 `ShellContent` 中 [`Tab`](xref:Xamarin.Forms.Tab) ，該物件會包裝在 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 物件中。

對等的 c # 程式碼而言，假設有一個名為的 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 物件 `aboutItem` ，就是：

```csharp
CurrentItem = aboutItem;
```

在此範例中， [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) 屬性是在子類別化 [`Shell`](xref:Xamarin.Forms.Shell) 類別中設定。 或者，您 `CurrentItem` 可以透過靜態屬性在任何類別中設定屬性 `Shell.Current` ：

```csharp
Shell.Current.CurrentItem = aboutItem;
```

> [!NOTE]
> 應用程式可能會進入選取飛出視窗專案的狀態，而這不是有效的作業。 在這種情況下，您 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 可以藉由將其 `IsEnabled` 屬性設定為來停用 `false` 。 這會讓使用者無法選取飛出視窗專案。

## <a name="flyoutitem-visibility"></a>FlyoutItem 可見度

飛出視窗專案預設會顯示在飛出視窗中。 不過，您可以在具有屬性的飛出視窗中隱藏專案 `FlyoutItemIsVisible` ，並從飛出視窗中移除 [`IsVisible`](xref:Xamarin.Forms.BaseShellItem.IsVisible) 屬性：

- `FlyoutItemIsVisible`型別為的， `bool` 表示專案是否隱藏在飛出視窗中，但仍可透過 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) 流覽方法來存取。 此屬性的預設值為 `true`。
- [`IsVisible`](xref:Xamarin.Forms.BaseShellItem.IsVisible)型別為的， `bool` 表示專案是否應該從視覺化樹狀結構中移除，因此不會出現在飛出視窗中。 其預設值為 `true`。

下列範例顯示隱藏飛出視窗中的專案：

```xaml
<Shell ...>
    <FlyoutItem ...
                FlyoutItemIsVisible="False">
        ...
    </FlyoutItem>
</Shell>
```

> [!NOTE]
> 另外還有 `Shell.FlyoutItemIsVisible` 附加屬性，可以在 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 、 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 、 [`Tab`](xref:Xamarin.Forms.Tab) 和物件上設定 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 。

## <a name="open-and-close-the-flyout-programmatically"></a>以程式設計方式開啟和關閉飛出視窗

您可以藉由將可系結屬性設定為值，以程式設計方式開啟和關閉飛出視窗， [`Shell.FlyoutIsPresented`](xref:Xamarin.Forms.Shell.FlyoutIsPresented) 以 `boolean` 指出是否目前開啟飛出視窗：

```xaml
<Shell ...
       FlyoutIsPresented="{Binding IsFlyoutOpen}">
</Shell>
```

或者，您也可以在程式碼中執行這項作業：

```csharp
Shell.Current.FlyoutIsPresented = false;
```

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms 樣式類別](~/xamarin-forms/user-interface/styles/xaml/style-class.md)
- [Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)
- [Xamarin.Forms 刷](~/xamarin-forms/user-interface/brushes/index.md)
- [Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)

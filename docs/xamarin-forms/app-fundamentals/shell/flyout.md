---
title: Xamarin.Forms Shell 飛出視窗
description: 飛出視窗為 Shell 應用程式的根功能表，且可透過圖示或從螢幕側邊撥動來存取。 飛出視窗會由選用標題、飛出視窗項目及選用功能表項目所組成。
ms.prod: xamarin
ms.assetid: FEDE51EB-577E-4B3E-9890-B7C1A5E52516
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2019
ms.openlocfilehash: 4049b3bdfdd6077dcfa151df9553722e63def0ba
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912971"
---
# <a name="xamarinforms-shell-flyout"></a>Xamarin.Forms Shell 飛出視窗

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

飛出視窗為 Shell 應用程式的根功能表，且可透過圖示或從螢幕側邊撥動來存取。 飛出視窗會由選用標頭、飛出視窗項目及選用功能表項目所組成：

![Shell 批註飛出視窗的螢幕擷取畫面](flyout-images/flyout-annotated.png "標注的飛出視窗")

如有需要，可以透過 [ 可繫結屬性來將飛出視窗的背景色彩設定為 `Color`](xref:Xamarin.Forms.Color)`Shell.FlyoutBackgroundColor`。 這個屬性也可以從階層式樣式表 (CSS) 進行設定。 如需詳細資訊，請參閱 [Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

## <a name="flyout-icon"></a>飛出視窗圖示

根據預設，Shell 應用程式有個漢堡圖示，按下圖示時，即會開啟飛出視窗。 透過將型別為 `Shell.FlyoutIcon`[`ImageSource` 的 ](xref:Xamarin.Forms.ImageSource) 可繫結屬性設定為適當的圖示，即可變更此圖示：

```xaml
<Shell ...
       FlyoutIcon="flyouticon.png">
    ...       
</Shell>
```

## <a name="flyout-behavior"></a>飛出視窗行為

飛出視窗可透過漢堡圖示或從螢幕側邊撥動來存取。 不過，透過將 `Shell.FlyoutBehavior` 附加屬性設定為其中一個 `FlyoutBehavior` 列舉成員，即可變更此行為：

- `Disabled`：指出使用者無法開啟飛出視窗。
- `Flyout`：指出使用者可以開啟和關閉飛出視窗。 這是 `FlyoutBehavior` 屬性的預設值。
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

此外，透過將 `Shell.FlyoutIsPresented` 可繫結屬性設定為 `boolean` 值，即可以程式設計的方式開啟和關閉飛出視窗，此值會出飛出視窗目前是否可見：

```csharp
Shell.Current.FlyoutIsPresented = false;
```

## <a name="flyout-header"></a>飛出視窗標題

飛出視窗標題是在飛出視窗頂端選擇性顯示的內容，其外觀會由可透過 `object` 屬性值設定的 `Shell.FlyoutHeader` 來定義：

```xaml
<Shell.FlyoutHeader>
    <controls:FlyoutHeader />
</Shell.FlyoutHeader>
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

![飛出視窗標題的螢幕擷取畫面](flyout-images/flyout-header.png "飛出視窗標題")

或者，透過將 `Shell.FlyoutHeaderTemplate` 屬性設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，即可定義飛出視窗標題的外觀：

```xaml
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
```

根據預設，飛出視窗標題將會固定在飛出視窗中，而如果有足夠的項目，以下內容就會捲動。 不過，透過將 `Shell.FlyoutHeaderBehavior` 可繫結屬性設定為其中一個 `FlyoutHeaderBehavior` 列舉成員，即可變更此行為：

- `Default`：指出將使用平台的預設行為。 此為 `FlyoutHeaderBehavior` 屬性的預設值。
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

## <a name="flyout-background-image"></a>飛出視窗背景影像

飛出視窗可有選擇性的背景影像，它會出現在飛出視窗的標題下方，和所有飛出視窗項目和功能表項目的後方。 將類型為 `FlyoutBackgroundImage`[`ImageSource` 的 ](xref:Xamarin.Forms.ImageSource) 可繫結屬性設定為檔案、內嵌資源、URI 或資料流，即可指定背景影像。

將類型為 `FlyoutBackgroundImageAspect`[`Aspect` 的 ](xref:Xamarin.Forms.Aspect) 可繫結屬性設定為 `Aspect` 其中一個列舉成員，即可設定背景影像的外觀比例：

- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) - 裁剪影像使其填滿顯示區域，同時保留外觀比例。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) - 視需要為影像加上上下黑邊，使影像與顯示區域相契合，並根據影像的寬度或高度，在上下左右新增空白空間。
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) - 完全自動縮放影像並剛好填滿顯示區域。 這可能會導致影像失真。

根據預設，`FlyoutBackgroundImageAspect` 屬性會設定為 `AspectFit`。

下列範例會示範如何設定這些屬性：

```xaml
<Shell ...
       FlyoutBackgroundImage="photo.jpg"
       FlyoutBackgroundImageAspect="AspectFill">
    ...
</Shell>
```

這會導致背景影像出現在飛出視窗中：

![飛出視窗背景影像的螢幕擷取畫面](flyout-images/flyout-backgroundimage.png "飛出視窗背景影像")

## <a name="flyout-items"></a>飛出視窗項目

當應用程式的導覽模式包含飛出視窗時，每個子類別化的 `Shell` 物件都必須包含一或多個 `FlyoutItem` 物件，以及每個代表飛出視窗上一個項目的 `FlyoutItem` 物件。 每個 `FlyoutItem` 物件應該都是 `Shell` 物件的子系。

下列範例會建立飛出視窗，其中包含一個飛出視窗標題和兩個飛出視窗項目：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <FlyoutItem Title="Cats"
                Icon="cat.png">
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
    <FlyoutItem Title="Dogs"
                Icon="dog.png">
        <Tab>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
</Shell>
```

在此範例中，每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 都只能透過飛出視窗項目來存取：

[![在 iOS 和 Android 上具有飛出視窗專案的 Shell 兩頁應用程式螢幕擷取畫面](flyout-images/two-page-app-flyout.png "使用飛出視窗專案的 Shell 兩頁應用程式")](flyout-images/two-page-app-flyout-large.png#lightbox "使用飛出視窗專案的 Shell 兩頁應用程式")

> [!NOTE]
> 如果沒有飛出視窗標題，飛出視窗項目就會出現在飛出視窗頂端。 否則，它們會出現在飛出視窗標題下方。

Shell 具有隱含的轉換運算子，可簡化 Shell 視覺階層，而不需要將其他檢視引進視覺化樹狀結構。 這是可能的，因為子類別化的 `Shell` 物件只能包含 `FlyoutItem` 物件或 `TabBar` 物件，其中只能包含 `Tab` 物件，且其中只能包含 `ShellContent` 物件。 這些隱含的轉換運算子可以用來移除上一個範例中的 `FlyoutItem`、`Tab` 和 `ShellContent` 物件：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <views:CatsPage IconImageSource="cat.png" />
    <views:DogsPage IconImageSource="dog.png" />
</Shell>
```

這個隱含的轉換會自動將每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件包裝於 `ShellContent` 物件中，其會包裝於 `Tab` 物件中，其會包裝於 `FlyoutItem` 物件中。

> [!IMPORTANT]
> 在 Shell 應用程式中，屬於 [ 物件子系的每個 `ContentPage`](xref:Xamarin.Forms.ContentPage)`ShellContent` 都會在應用程式啟動期間建立。 使用此方法新增其他 `ShellContent` 物件將會導致在應用程式啟動期間建立其他頁面，進而可能導致啟動經驗不佳。 不過，Shell 也能夠依需求建立頁面，以回應導覽。 如需詳細資訊，請參閱 [Xamarin.Forms Shell 索引標籤](tabs.md#efficient-page-loading)指南中的[有效率的頁面載入](tabs.md)。

### <a name="flyoutitem-class"></a>FlyoutItem 類別

`FlyoutItem` 類別包含下列屬性來控制飛出視窗項目的外觀和行為：

- 型別為 `FlyoutDisplayOptions` 的 `FlyoutDisplayOptions` 可定義項目及其子系如何顯示於飛出視窗中。 預設值是 `AsSingleItem`。
- 型別為 `CurrentItem` 的 `Tab`，這是選取的項目。
- 型別為 `Items` 的 `IList<Tab>` 會在 `FlyoutItem` 內定義所有索引標籤。
- 型別為 `FlyoutIcon` 的 `ImageSource`，這是要針對項目使用的圖示。 如果未設定這個屬性，它將轉而使用 `Icon` 屬性值。
- 型別為 `Icon` 的 `ImageSource` 可定義要在不是飛出視窗的 Chrome 組件中顯示的圖示。
- 型別為 `IsChecked` 的`boolean` 可定義目前是否已在飛出視窗中強調顯示該項目。
- 型別為 `IsEnabled` 的 `boolean` 可定義是否可在 Chrome 中選取該項目。
- 型別為 `IsTabStop` 的 `bool` 會指出 TAB 導覽中是否包含 `FlyoutItem`。 其預設值為 `true`，而當其值為 `false` 時，不論是否設定 `FlyoutItem`，TAB 導覽基礎結構均會略過 `TabIndex`。
- 型別為 `TabIndex` 的 `int` 屬性會指定當使用者按下 Tab 鍵來巡覽項目時，`FlyoutItem` 物件接收焦點的順序。 屬性的預設值為 0。
- 型別為 `Title` 的 `string`，這是在 UI 中顯示的標題。
- 型別為 `Route` 的 `string`，這是用來處理此項目的字串。

所有這些屬性 (`Route` 屬性除外) 都以 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件為後盾，也就是說，這些屬性可以是資料繫結的目標。

> [!NOTE]
> 子類別化的 Shell 物件中的所有 `FlyoutItem` 物件均會加入至 `Shell.Items` 集合，其會定義將顯示於飛出視窗中的項目清單。

此外，`FlyoutItem` 類別會公開下列可覆寫的方法：

- `OnTabIndexPropertyChanged`，每當 `TabIndex` 屬性變更時，就會呼叫。
- `OnTabStopPropertyChanged`，每當 `IsTabStop` 屬性變更時，就會呼叫。
- `TabIndexDefaultValueCreator` 會傳回 `int`，並呼叫來設定 `TabIndex` 屬性的預設值。
- `TabStopDefaultValueCreator` 會傳回 `bool`，並呼叫來設定 `TabStop` 屬性的預設值。

## <a name="flyout-vertical-scroll"></a>飛出視窗垂直捲動

根據預設，當飛出視窗專案無法放入飛出視窗中時，飛出視窗可以垂直捲動。 將 `Shell.FlyoutVerticalScrollMode` 可系結屬性設定為其中一個 `ScrollMode` 列舉成員，即可變更此行為：

- `Disabled` –表示垂直捲動將會停用。
- `Enabled` –表示將會啟用垂直捲動。
- `Auto` –表示如果飛出視窗專案無法放入飛出視窗中，則會啟用垂直捲動。 此為 `Shell.FlyoutVerticalScrollMode` 屬性的預設值。

下列範例顯示如何停用垂直捲動：

```xaml
<Shell ...
       FlyoutVerticalScrollMode="Disabled"
    ...
</Shell>
```

## <a name="flyout-display-options"></a>飛出視窗顯示選項

`FlyoutDisplayOptions` 列舉會定義下列成員：

- `AsSingleItem`，指出項目將顯示為單一項目。
- `AsMultipleItems`，指出項目及其子系都會在飛出視窗中顯示為一組項目。

透過將 `FlyoutItem.FlyoutDisplayOptions` 屬性設定為 `AsMultipleItems`，即會針對 `Tab` 內的每個 `FlyoutItem` 建立飛出視窗項目：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       FlyoutHeaderBehavior="CollapseOnScroll"
       x:Class="Xaminals.AppShell">

    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>

    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
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

在此範例中，會針對 `Tab` 物件 (其為 `FlyoutItem` 物件的子系) 和 `ShellContent` 物件 (其為 `FlyoutItem` 物件的子系) 建立飛出視窗項目。 這是因為每個 `ShellContent` 物件 (其為 `FlyoutItem` 物件的子系) 都會自動包裝於 `Tab` 物件中。 此外，會針對最後一個 `ShellContent` 物件建立飛出視窗項目，其包裝於 `Tab` 物件中，然後包裝於 `FlyoutItem` 物件中。

這會產生下列飛出視窗項目：

[![IOS 和 Android 上包含 FlyoutItem 物件的飛出視窗螢幕擷取畫面](flyout-images/flyout-reduced.png "包含 FlyoutItem 物件的 Shell 飛出視窗")](flyout-images/flyout-reduced-large.png#lightbox "包含 FlyoutItem 物件的 Shell 飛出視窗")

## <a name="define-flyoutitem-appearance"></a>定義 FlyoutItem 外觀

透過將 `FlyoutItem` 附加屬性設定為 `Shell.ItemTemplate`[`DataTemplate`，可自訂每個 ](xref:Xamarin.Forms.DataTemplate) 的外觀：

```xaml
<Shell ...>
    ...
    <Shell.ItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
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

此範例會以斜體顯示每個 `FlyoutItem` 物件的標題：

[![IOS 和 Android 上樣板化 FlyoutItem 物件的螢幕擷取畫面](flyout-images/flyoutitem-templated.png "Shell 樣板化 FlyoutItem 物件")](flyout-images/flyoutitem-templated-large.png#lightbox "Shell 樣板化 FlyoutItem 物件")


由於 `Shell.ItemTemplate` 是附加屬性，因此可以將不同的範本附加至特定的 `FlyoutItem` 物件。

> [!NOTE]
> Shell 會將 `Title` 和 `FlyoutIcon` 屬性提供給 [ 的 `BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)`ItemTemplate`。


### <a name="default-template-for-flyoutitems-and-menuitems"></a>FlyoutItems 和 MenuItems 的預設範本
Shell 會在內部使用下列範本來執行其預設值。 如果您只想要對現有的版面配置進行小型調整，這是很好的起點。 這也會示範飛出視窗專案的視覺狀態管理員功能。 這個相同的範本也可以用於 MenuItems

```xaml
<DataTemplate x:Key="FlyoutTemplates">
    <Grid HeightRequest="{x:OnPlatform Android=50}">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroupList>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal">
                    </VisualState>
                    <VisualState x:Name="Selected">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="#F2F2F2" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateGroupList>
        </VisualStateManager.VisualStateGroups>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="{x:OnPlatform Android=54, iOS=50}"></ColumnDefinition>
            <ColumnDefinition Width="*"></ColumnDefinition>
        </Grid.ColumnDefinitions>
        <Image Source="{Binding FlyoutIcon}"
            VerticalOptions="Center"
            HorizontalOptions="Center"
            HeightRequest="{x:OnPlatform Android=24, iOS=22}"
            WidthRequest="{x:OnPlatform Android=24, iOS=22}">
        </Image>
        <Label VerticalOptions="Center"
                Text="{Binding Title}"
                FontSize="{x:OnPlatform Android=14, iOS=Small}"
                FontAttributes="Bold" Grid.Column="1">
            <Label.TextColor>
                <OnPlatform x:TypeArguments="Color">
                    <OnPlatform.Platforms>
                        <On Platform="Android" Value="#D2000000" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.TextColor>
            <Label.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="Android" Value="20, 0, 0, 0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.Margin>
            <Label.FontFamily>
                <OnPlatform x:TypeArguments="x:String">
                    <OnPlatform.Platforms>
                        <On Platform="Android" Value="sans-serif-medium" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.FontFamily>
        </Label>
    </Grid>
</DataTemplate>
```

## <a name="flyoutitem-tab-order"></a>FlyoutItem 定位順序

根據預設，`FlyoutItem` 物件的定位順序會與其列在 XAML 中的順序或以程式設計方式新增至子集合的順序相同。 此順序為透過鍵盤巡覽 `FlyoutItem` 物件的順序，而這個預設順序通常也是最佳順序。

透過設定 `FlyoutItem.TabIndex` 屬性，即可變更預設定位順序，其會指定當使用者按下 Tab 鍵來巡覽項目時，`FlyoutItem` 物件接收焦點的順序。 屬性的預設值為 0，而且可以設定為任何 `int` 值。

在使用預設定位順序或設定 `TabIndex` 屬性時，會套用下列規則：

- `FlyoutItem` 等於 0 的 `TabIndex` 物件會根據其在 XAML 或子集合中的宣告順序新增至定位順序。
- `FlyoutItem` 大於 0 的 `TabIndex` 物件則會根據其 `TabIndex` 值新增至定位順序。
- `FlyoutItem` 小於 0 的 `TabIndex` 物件會新增至定位順序並顯示在任何零值之前。
- `TabIndex` 的衝突由宣告順序來解決。

在定義定位順序之後，按下 Tab 鍵將會以遞增的 `FlyoutItem` 順序循環 `TabIndex` 物件的焦點，從開頭一直循環到最後一個物件。

除了設定 `FlyoutItem` 物件的定位順序，可能也需要從定位順序中排除一些物件。 上述作業可透過 `FlyoutItem.IsTabStop` 屬性來完成，其指出 TAB 導覽中是否包含 `FlyoutItem`。 其預設值為 `true`，而當其值為 `false` 時，不論是否設定 `FlyoutItem`，TAB 導覽基礎結構均會略過 `TabIndex`。

## <a name="set-the-current-flyoutitem"></a>設定目前的 FlyoutItem

`Shell` 類別有一個名為 `CurrentItem` 且型別為 `FlyoutItem` 的可繫結屬性，其代表目前選取的 `FlyoutItem`。 當 Shell 應用程式第一次執行時，會將這個屬性設定為子類別化之 `FlyoutItem` 物件中的第一個 `Shell`。 不過，您可以將屬性設定為另一個 `FlyoutItem`，如下列範例所示：

```xaml
<Shell ...
       CurrentItem="{x:Reference aboutItem}">
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        ...
    </FlyoutItem>
    <ShellContent x:Name="aboutItem"
                  Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />
</Shell>
```

此程式碼會設定名為 `ShellContent` 的 `aboutItem` 物件作為 `CurrentItem` 屬性，從而顯示它。 在此範例中，隱含的轉換會用來將 `ShellContent` 物件包裝於 `Tab` 物件中，其包裝於 `FlyoutItem` 物件中。

對等的 C# 程式碼為：

```csharp
Shell.Current.CurrentItem = aboutItem;
```

## <a name="menu-items"></a>功能表項目

可以選擇性地將功能表項目加入至飛出視窗，每個功能表項目都由一個 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件來表示。 飛出視窗上 `MenuItem` 物件的位置，取決於其在 Shell 視覺階層中的宣告順序。 因此，在 `MenuItem` 物件之前宣告的任何 `FlyoutItem` 物件將出現在飛出視窗的頂端，而在 `MenuItem` 物件之後宣告的任何 `FlyoutItem` 物件將出現在飛出視窗的底部。

> [!NOTE]
> `MenuItem` 類別具有 [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) 事件及 [`Command`](xref:Xamarin.Forms.MenuItem.Command) 屬性。 因此，`MenuItem` 物件會啟用執行動作以回應點選 `MenuItem` 的案例。 這些案例包括執行導覽，以及在特定 Web 網頁上開啟網頁瀏覽器。

[`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件可以被加入飛出視窗，如下列範例所示：

```xaml
<Shell ...>
    ...            
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />    
</Shell>
```

此程式碼在飛出視窗中加入兩個 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件 (在所有飛出視窗項目底下)：

[![IOS 和 Android 上包含 MenuItem 物件的飛出視窗螢幕擷取畫面](flyout-images/flyout.png "包含 MenuItem 物件的 Shell 飛出視窗")](flyout-images/flyout-large.png#lightbox "包含 MenuItem 物件的 Shell 飛出視窗")

第一個 [`MenuItem`](xref:Xamarin.Forms.MenuItem) 物件會執行名為 `ICommand` 的 `RandomPageCommand`，這會巡覽至應用程式中的隨機頁面。 第二個 `MenuItem` 物件會執行名為 `ICommand` 的 `HelpCommand`，這會在網頁瀏覽器中開啟 `CommandParameter` 屬性所指定的 URL。

> [!NOTE]
> 每個 [ 的 `BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)`MenuItem` 皆繼承自子類別化的 `Shell` 物件。

## <a name="define-menuitem-appearance"></a>定義 MenuItem 的外觀

透過將 `MenuItem` 附加屬性設定為 `Shell.MenuItemTemplate`[`DataTemplate`，可自訂每個 ](xref:Xamarin.Forms.DataTemplate) 的外觀：

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
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
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />  
</Shell>
```

此範例會將 Shell 層級的 `MenuItemTemplate` 附加到每個 `MenuItem` 物件，以斜體顯示每個 `MenuItem` 物件的標題：

[![IOS 和 Android 上樣板化 MenuItem 物件的螢幕擷取畫面](flyout-images/menuitem-templated.png "Shell 樣板化 MenuItem 物件")](flyout-images/menuitem-templated-large.png#lightbox "Shell 樣板化 MenuItem 物件")

> [!NOTE]
> Shell 會將[`Text`](xref:Xamarin.Forms.MenuItem.Text)和[`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)屬性提供給 `MenuItemTemplate`的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。 您也可以使用 `Title` 取代 `Text` 和 `Icon` 來取代 `IconImageSource`，這可讓您針對功能表項目和飛出視窗專案重複使用相同的範本

因為 `Shell.MenuItemTemplate` 是附加屬性，所以可以將不同的範本附加至特定 `MenuItem` 物件：

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
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
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              Icon="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell">
        <Shell.MenuItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </Shell.MenuItemTemplate>
    </MenuItem>
</Shell>
```


> [!NOTE]
> 用於[飛出視窗專案](#default-template-for-flyoutitems-and-menuitems)的相同範本也可以用於功能表項目。

此範例會將 Shell 層級的 `MenuItemTemplate` 附加到第一個 `MenuItem` 物件，並將內嵌 `MenuItemTemplate` 附加到第二個 `MenuItem`。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

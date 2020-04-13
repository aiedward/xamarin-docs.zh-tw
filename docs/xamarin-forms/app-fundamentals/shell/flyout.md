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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305168"
---
# <a name="xamarinforms-shell-flyout"></a>Xamarin.Forms Shell 飛出視窗

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

飛出視窗為 Shell 應用程式的根功能表，且可透過圖示或從螢幕側邊撥動來存取。 飛出視窗會由選用標頭、飛出視窗項目及選用功能表項目所組成：

![Shell 批次螢幕截圖](flyout-images/flyout-annotated.png "有名的飛出")

如果需要,可以[`Color`](xref:Xamarin.Forms.Color)`Shell.FlyoutBackgroundColor`透過可綁定屬性將彈出視窗的背景顏色設定為 。 這個屬性也可以從階層式樣式表 (CSS) 進行設定。 如需詳細資訊，請參閱 [Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

## <a name="flyout-icon"></a>飛出視窗圖示

根據預設，Shell 應用程式有個漢堡圖示，按下圖示時，即會開啟飛出視窗。 可以通過可`Shell.FlyoutIcon`結合屬性(類型)[`ImageSource`](xref:Xamarin.Forms.ImageSource)設定為適當的圖示來更改此圖示:

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

飛出視窗標題是在飛出視窗頂端選擇性顯示的內容，其外觀會由可透過 `Shell.FlyoutHeader` 屬性值設定的 `object` 來定義：

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

![彈出視窗頭的螢幕截圖](flyout-images/flyout-header.png "飛出視窗標題")

或者,可以通過`Shell.FlyoutHeaderTemplate`將 屬性設置為 來定義彈出視窗標[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)頭外觀:

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

飛出視窗可有選擇性的背景影像，它會出現在飛出視窗的標題下方，和所有飛出視窗項目和功能表項目的後方。 背景圖像可以通過將`FlyoutBackgroundImage`可 綁定屬性([`ImageSource`](xref:Xamarin.Forms.ImageSource)類型類型 )設置為檔、嵌入資源、URI 或流來指定。

背景影像的垂直比可以透過可`FlyoutBackgroundImageAspect`結合屬性類型 (type)[`Aspect`](xref:Xamarin.Forms.Aspect)設定為`Aspect`encless 的屬性類型 (type) 設定為 Encles

- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)- 剪輯圖像,使其填充顯示區域,同時保持縱橫比。
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)- 如果需要,將圖像的字母盒放在圖像中,以便圖像適合顯示區域,並根據圖像是寬還是高,將空白添加到頂部/底部或側面。
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)- 將圖像拉伸到完全和精確填充顯示區域。 這可能會導致影像失真。

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

![飛出背景影像的螢幕擷圖](flyout-images/flyout-backgroundimage.png "飛出視窗背景影像")

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

此範例中,只能透過飛出[`ContentPage`](xref:Xamarin.Forms.ContentPage)項存取每個項:

[![在 iOS 和 Android 上使用帶彈出視窗項目的 Shell 兩頁應用程式的螢幕截圖](flyout-images/two-page-app-flyout.png "包含飛出項目的兩頁應用")](flyout-images/two-page-app-flyout-large.png#lightbox "包含飛出項目的兩頁應用")

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

此隱式轉換會自動包裝物件[`ContentPage`](xref:Xamarin.Forms.ContentPage)中的`ShellContent`每個 物件,這些物件包`Tab`在物件 中,物件`FlyoutItem`包在物件 中。

> [!IMPORTANT]
> 在 Shell 應用程式[`ContentPage`](xref:Xamarin.Forms.ContentPage)中, 在應用程式啟動期間建立`ShellContent`物件的子 級的每個物件。 使用此方法新增其他 `ShellContent` 物件將會導致在應用程式啟動期間建立其他頁面，進而可能導致啟動經驗不佳。 不過，Shell 也能夠依需求建立頁面，以回應導覽。 如需詳細資訊，請參閱 [Xamarin.Forms Shell 索引標籤](tabs.md)指南中的[有效率的頁面載入](tabs.md#efficient-page-loading)。

### <a name="flyoutitem-class"></a>FlyoutItem 類別

`FlyoutItem` 類別包含下列屬性來控制飛出視窗項目的外觀和行為：

- `FlyoutDisplayOptions`，屬於 `FlyoutDisplayOptions` 類型，可定義項目及其子項目如何顯示在飛出視窗中。 預設值是 `AsSingleItem`。
- `CurrentItem`，屬於 `Tab` 類型，這是選取的項目。
- 型別為 `IList<Tab>` 的 `Items` 會在 `FlyoutItem` 內定義所有索引標籤。
- 型別為 `ImageSource` 的 `FlyoutIcon`，這是要針對項目使用的圖示。 如果未設定這個屬性，它將轉而使用 `Icon` 屬性值。
- 型別為 `ImageSource` 的 `Icon` 可定義要在不是飛出視窗的 Chrome 組件中顯示的圖示。
- `IsChecked`，屬於 `boolean` 類型，可定義項目目前是否在飛出視窗中反白顯示。
- `IsEnabled`，屬於 `boolean` 類型，可定義在 Chrome 中是否可以選取項目。
- 型別為 `bool` 的 `IsTabStop` 會指出 TAB 導覽中是否包含 `FlyoutItem`。 其預設值為 `true`，而當其值為 `false` 時，不論是否設定 `TabIndex`，TAB 導覽基礎結構均會略過 `FlyoutItem`。
- 型別為 `int` 的 `TabIndex` 屬性會指定當使用者按下 Tab 鍵來巡覽項目時，`FlyoutItem` 物件接收焦點的順序。 屬性的預設值為 0。
- `Title`，屬於 `string` 類型，這是在 UI 中顯示的標題。
- 型別為 `string` 的 `Route`，這是用來處理此項目的字串。

所有這些屬性(屬性除外`Route`)都[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)由 物件支援,這意味著這些屬性可以是數據綁定的目標。

> [!NOTE]
> 子類別化的 Shell 物件中的所有 `FlyoutItem` 物件均會加入至 `Shell.Items` 集合，其會定義將顯示於飛出視窗中的項目清單。

此外，`FlyoutItem` 類別會公開下列可覆寫的方法：

- `OnTabIndexPropertyChanged`，每當 `TabIndex` 屬性變更時，就會呼叫。
- `OnTabStopPropertyChanged`，每當 `IsTabStop` 屬性變更時，就會呼叫。
- `TabIndexDefaultValueCreator` 會傳回 `int`，並呼叫來設定 `TabIndex` 屬性的預設值。
- `TabStopDefaultValueCreator` 會傳回 `bool`，並呼叫來設定 `TabStop` 屬性的預設值。

## <a name="flyout-vertical-scroll"></a>飛出視窗垂直捲動

默認情況下,當彈出視窗專案不適合彈出視窗時,可以垂直滾動彈出視窗。 可以通過將`Shell.FlyoutVerticalScrollMode`可 綁定屬性設置`ScrollMode`為枚 舉成員之一來更改此行為:

- `Disabled`• 表示將禁用垂直滾動。
- `Enabled`• 指示將啟用垂直滾動。
- `Auto`• 指示如果彈出視窗專案不適合彈出視窗,將啟用垂直滾動。 此為 `Shell.FlyoutVerticalScrollMode` 屬性的預設值。

下面的範例示範如何關閉垂直捲動:

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

透過將 `FlyoutItem.FlyoutDisplayOptions` 屬性設定為 `AsMultipleItems`，即會針對 `FlyoutItem` 內的每個 `Tab` 建立飛出視窗項目：

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

[![在 iOS 和 Android 上包含飛出專案物件的彈出視窗螢幕截圖](flyout-images/flyout-reduced.png "包含彈出項目的殼體彈出視窗")](flyout-images/flyout-reduced-large.png#lightbox "包含彈出項目的殼體彈出視窗")

## <a name="define-flyoutitem-appearance"></a>定義 FlyoutItem 外觀

透過額外`FlyoutItem``Shell.ItemTemplate`屬性設定 : 可以自訂每個屬性的外觀[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。

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

[![iOS 和 Android 上樣本化飛出專案物件的螢幕截圖](flyout-images/flyoutitem-templated.png "外殼範本化飛出項目物件")](flyout-images/flyoutitem-templated-large.png#lightbox "外殼範本化飛出項目物件")


由於`Shell.ItemTemplate`是附加屬性,因此可以將不同的範本附加到`FlyoutItem`特定 物件。

> [!NOTE]
> 命令行對`Title``FlyoutIcon`的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)提供`ItemTemplate`和 屬性。


### <a name="default-template-for-flyoutitems-and-menuitems"></a>飛出項目與選單項目的預設樣本
Shell 在內部使用以下範本進行預設實現。 這是一個很好的起點,如果你想做的只是對現有布局進行小的調整。 這還演示了彈出視窗項的可視化狀態管理器功能。 此相同的樣本也可用於選單專案

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

- `TabIndex` 等於 0 的 `FlyoutItem` 物件會根據其在 XAML 或子集合中的宣告順序新增至定位順序。
- `TabIndex` 大於 0 的 `FlyoutItem` 物件則會根據其 `TabIndex` 值新增至定位順序。
- `TabIndex` 小於 0 的 `FlyoutItem` 物件會新增至定位順序並顯示在任何零值之前。
- `TabIndex` 的衝突由宣告順序來解決。

在定義定位順序之後，按下 Tab 鍵將會以遞增的 `TabIndex` 順序循環 `FlyoutItem` 物件的焦點，從開頭一直循環到最後一個物件。

除了設定 `FlyoutItem` 物件的定位順序，可能也需要從定位順序中排除一些物件。 上述作業可透過 `FlyoutItem.IsTabStop` 屬性來完成，其指出 TAB 導覽中是否包含 `FlyoutItem`。 其預設值為 `true`，而當其值為 `false` 時，不論是否設定 `TabIndex`，TAB 導覽基礎結構均會略過 `FlyoutItem`。

## <a name="set-the-current-flyoutitem"></a>設定目前的 FlyoutItem

`Shell` 類別有一個名為 `CurrentItem` 且型別為 `FlyoutItem` 的可繫結屬性，其代表目前選取的 `FlyoutItem`。 當 Shell 應用程式第一次執行時，會將這個屬性設定為子類別化之 `Shell` 物件中的第一個 `FlyoutItem`。 不過，您可以將屬性設定為另一個 `FlyoutItem`，如下列範例所示：

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

此程式碼會設定名為 `aboutItem` 的 `ShellContent` 物件作為 `CurrentItem` 屬性，從而顯示它。 在此範例中，隱含的轉換會用來將 `ShellContent` 物件包裝於 `Tab` 物件中，其包裝於 `FlyoutItem` 物件中。

對等的 C# 程式碼為：

```csharp
Shell.Current.CurrentItem = aboutItem;
```

## <a name="menu-items"></a>功能表項目

功能表項可以選擇添加到彈出視窗中,並且每個功能表項都由一個[`MenuItem`](xref:Xamarin.Forms.MenuItem)物件表示。 飛出視窗上 `MenuItem` 物件的位置，取決於其在 Shell 視覺階層中的宣告順序。 因此，在 `FlyoutItem` 物件之前宣告的任何 `MenuItem` 物件將出現在飛出視窗的頂端，而在 `FlyoutItem` 物件之後宣告的任何 `MenuItem` 物件將出現在飛出視窗的底部。

> [!NOTE]
> 類`MenuItem`具有事件[`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked)和[`Command`](xref:Xamarin.Forms.MenuItem.Command)屬性。 因此，`MenuItem` 物件會啟用執行動作以回應點選 `MenuItem` 的案例。 這些案例包括執行導覽，以及在特定 Web 網頁上開啟網頁瀏覽器。

[`MenuItem`](xref:Xamarin.Forms.MenuItem)物件可以新增到彈出視窗中,如以下範例所示:

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

此代碼在所有彈出視窗[`MenuItem`](xref:Xamarin.Forms.MenuItem)項目下向彈出視窗新增兩個物件:

[![在 iOS 與 Android 上顯示包含 MenuItem 物件的彈出視窗的螢幕截圖](flyout-images/flyout.png "包含選單項目物件的外殼彈出視窗")](flyout-images/flyout-large.png#lightbox "包含選單項目物件的外殼彈出視窗")

第一[`MenuItem`](xref:Xamarin.Forms.MenuItem)個物件執行`ICommand``RandomPageCommand`名為 的 ,該物件導航到應用程式中的隨機頁。 第二個 `MenuItem` 物件會執行名為 `HelpCommand` 的 `ICommand`，這會在網頁瀏覽器中開啟 `CommandParameter` 屬性所指定的 URL。

> [!NOTE]
> 每個[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)`MenuItem`物件的 是從子類別繼承`Shell`的 。

## <a name="define-menuitem-appearance"></a>定義 MenuItem 的外觀

透過額外`MenuItem``Shell.MenuItemTemplate`屬性設定 : 可以自訂每個屬性的外觀[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)。

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

[![iOS 和 Android 上樣本化選單專案物件的螢幕截圖](flyout-images/menuitem-templated.png "外殼範本化選單項目")](flyout-images/menuitem-templated-large.png#lightbox "外殼範本化選單項目")

> [!NOTE]
> 命令行對[`Text`](xref:Xamarin.Forms.MenuItem.Text)[`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)提供`MenuItemTemplate`和 屬性。 您還可以使用`Title``Text`代替`Icon`與`IconImageSource`代替 ,這會允許您重用相同的樣本的選單項目和飛出專案

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
> 用於[飛出專案](#default-template-for-flyoutitems-and-menuitems)的相同範本也可用於功能表項。

此範例會將 Shell 層級的 `MenuItemTemplate` 附加到第一個 `MenuItem` 物件，並將內嵌 `MenuItemTemplate` 附加到第二個 `MenuItem`。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

---
title: 建立跨平臺 Xamarin.Forms 應用程式的樣式
description: 本文說明如何使用 XAML 樣式來建立跨平臺 Xamarin.Forms Shell 應用程式的樣式，並使用 XAML 熱重新載入來查看這些變更。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 1C6ED8AF-41B4-4D6C-9BD8-C72D3F05E541
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/26/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0424f3c9cdcde98cef4c414ada33046cd8ce7ee6
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/08/2021
ms.locfileid: "99818081"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>建立跨平臺 Xamarin.Forms 應用程式的樣式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

在本快速入門中，您將了解如何：

- Xamarin.Forms使用 XAML 樣式來為 Shell 應用程式建立樣式。
- 使用 XAML 熱重新載入來查看 UI 變更，而不需要重建應用程式。

本快速入門會逐步解說如何使用 XAML 樣式來建立跨平臺 Xamarin.Forms 應用程式的樣式。 此外，快速入門會使用 XAML 熱重新載入來更新執行中應用程式的 UI，而不需要重建應用程式。 如需 XAML 熱重新載入的詳細資訊，請參閱[XAML 熱重新載入 Xamarin.Forms ](~/xamarin-forms/xaml/hot-reload.md)。

最終的應用程式如下所示：

關於頁面的[ ![ 附注頁面](styling-images/screenshots1-sml.png)](styling-images/screenshots1.png#lightbox) 
 [ ![ 筆記輸入頁面](styling-images/screenshots2-sml.png)](styling-images/screenshots2.png#lightbox) 
 [ ![ ](styling-images/screenshots3-sml.png)](styling-images/screenshots3.png#lightbox)

### <a name="prerequisites"></a>必要條件

您應該先成功完成[先前的快速入門](database.md)，再嘗試本快速入門。 或者，下載[先前的快速入門範例](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)，並使用此範例作為本快速入門的起點。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio 並開啟 Notes 方案。

2. 在您選擇的平臺上建立並執行專案。 如需詳細資訊，請參閱[建置快速入門](app.md#building-the-quickstart)。

    讓應用程式保持執行狀態，並返回 Visual Studio。

3. 在 **方案總管** 的 **Notes** 專案中，開啟 **app.xaml**。 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

        <!-- Resources used by multiple pages in the application -->         
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppPrimaryColor">#1976D2</Color>
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="PrimaryColor">Black</Color>
            <Color x:Key="SecondaryColor">White</Color>
            <Color x:Key="TertiaryColor">Silver</Color>

            <!-- Implicit styles -->
            <Style TargetType="ContentPage"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button">
                <Setter Property="FontSize"
                        Value="Medium" />
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="TextColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="CornerRadius"
                        Value="5" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    此程式碼會定義 [`Thickness`](xref:Xamarin.Forms.Thickness) 值、一系列的 [`Color`](xref:Xamarin.Forms.Color) 值，以及和類型的隱含樣式 [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`Button`](xref:Xamarin.Forms.Button) 。 請注意，在應用層級中，您可以在 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 整個應用程式中使用這些樣式。 如需 XAML 樣式的詳細資訊，請參閱快速入門中的[樣式](deepdive.md#styling)設定[ Xamarin.Forms 深入探討](deepdive.md)。

    按 **CTRL + S**，將變更儲存到 **app.xaml** 。 XAML 熱重新載入將會更新正在執行之應用程式的 UI，而不需要重建應用程式。 具體而言，每個頁面的背景色彩會變更。

4. 在 **方案總管** 的 **Notes** 專案中，開啟 [ **AppShell**]。 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">

        <Shell.Resources>
            <!-- Style Shell elements -->
            <Style x:Key="BaseStyle"
                   TargetType="Element">
                <Setter Property="Shell.BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="Shell.ForegroundColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TitleColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TabBarUnselectedColor"
                        Value="#95FFFFFF"/>
            </Style>
            <Style TargetType="TabBar"
                   BasedOn="{StaticResource BaseStyle}" />
        </Shell.Resources>

        <!-- Display a bottom tab bar containing two tabs -->   
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```

    這段程式碼會在資源字典中新增兩種樣式 `Shell` ，以定義 [`Color`](xref:Xamarin.Forms.Color) 應用程式所使用的一連串值。

    按 **CTRL + S**，將變更儲存至 **AppShell。** XAML 熱重新載入將會更新正在執行之應用程式的 UI，而不需要重建應用程式。 明確地說，Shell chrome 的背景色彩會變更。

5. 在 **方案總管** 的 **Notes** 專案中，在 [ **Views** ] 資料夾中開啟 **>notespage.xaml.cs。** 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">

        <ContentPage.Resources>
            <!-- Define a visual state for the Selected state of the CollectionView -->
            <Style TargetType="StackLayout">
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal" />
                            <VisualState x:Name="Selected">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor"
                                            Value="{StaticResource AppPrimaryColor}" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>
        </ContentPage.Resources>

        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="{StaticResource PageMargin}"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium" />
                        <Label Text="{Binding Date}"
                               TextColor="{StaticResource TertiaryColor}"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    這段程式碼會為定義的隱含樣式 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，定義中每個選取專案的外觀 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 並將 `CollectionView.Margin`  和屬性設定 `Label.TextColor` 為應用層級中定義的值 `ResourceDictionary` 。 請注意，`StackLayout` 隱含樣式已新增至頁面層級的 `ResourceDictionary`，因為它只能由 `NotesPage` 使用。

    按 **CTRL + S**，將變更儲存至 **>notespage.xaml.cs。** XAML 熱重新載入將會更新正在執行之應用程式的 UI，而不需要重建應用程式。 具體而言，中選取專案的色彩 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會變更。

6. 在 **方案總管** 的 **Notes** 專案中，在 [ **Views** ] 資料夾中開啟 **>noteentrypage.xaml.cs。** 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>         
        </ContentPage.Resources>

        <!-- Layout children vertically -->
        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid ColumnDefinitions="*,*">
                <!-- Layout children in two columns -->
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    這段程式碼會將的隱含樣式新增 [`Editor`](xref:Xamarin.Forms.Editor) 至頁面層級 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，並將 `StackLayout.Margin` 屬性設定為應用層級中定義的值 `ResourceDictionary` 。 請注意， `Editor` 隱含樣式已新增至頁面層級， `ResourceDictionary` 因為它只能由使用 `NoteEntryPage` 。

7. 在正在執行的應用程式中，流覽至 `NoteEntryPage` 。

8. 在 Visual Studio 中，選擇 [檔案] **> [儲存** (] 或按 **&#8984; + S**) ，儲存 >noteentrypage.xaml.cs 的變更 **。**

    XAML 熱重新載入將會更新應用程式的 UI，而不需要重建。 具體來說，的背景色彩 [`Editor`](xref:Xamarin.Forms.Editor) 會在執行中的應用程式中變更，就像物件的外觀一樣 [`Button`](xref:Xamarin.Forms.Button) 。

9. 在 **方案總管** 的 **Notes** 專案中，在 [ **Views** ] 資料夾中開啟 **>aboutpage。** 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{StaticResource AppPrimaryColor}"
                   Opacity="0.85"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->       
            <StackLayout Grid.Row="1"
                         Margin="{StaticResource PageMargin}"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    這段程式碼會將 `Image.BackgroundColor` 和 `StackLayout.Margin` 屬性設定為應用層級中定義的值 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。

10. 在正在執行的應用程式中，流覽至 `AboutPage` 。

11. 在 Visual Studio 中，按 **CTRL + S**，將變更儲存至 **>aboutpage。**

    XAML 熱重新載入將會更新應用程式的 UI，而不需要重建。 具體來說，的背景色彩 [`Image`](xref:Xamarin.Forms.Editor) 會在執行中的應用程式中變更。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac 並開啟 Notes 專案。

2. 在您選擇的平臺上建立並執行專案。 如需詳細資訊，請參閱[建置快速入門](app.md#building-the-quickstart)。

    讓應用程式保持執行狀態，並返回 Visual Studio for Mac。

3. 在 **Solution Pad** 的 **Notes** 專案中，開啟 **app.xaml**。 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

        <!-- Resources used by multiple pages in the application -->                 
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppPrimaryColor">#1976D2</Color>
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="PrimaryColor">Black</Color>
            <Color x:Key="SecondaryColor">White</Color>
            <Color x:Key="TertiaryColor">Silver</Color>

            <!-- Implicit styles -->
            <Style TargetType="ContentPage"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button">
                <Setter Property="FontSize"
                        Value="Medium" />
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="TextColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="CornerRadius"
                        Value="5" />
            </Style>  
        </Application.Resources>
    </Application>
    ```

    此程式碼會定義 [`Thickness`](xref:Xamarin.Forms.Thickness) 值、一系列的 [`Color`](xref:Xamarin.Forms.Color) 值，以及和類型的隱含樣式 [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`Button`](xref:Xamarin.Forms.Button) 。 請注意，在應用層級中，您可以在 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 整個應用程式中使用這些樣式。 如需 XAML 樣式的詳細資訊，請參閱快速入門中的[樣式](deepdive.md#styling)設定[ Xamarin.Forms 深入探討](deepdive.md)。

    選擇 [檔案] **> 儲存** (或按 **&#8984; + S**) ，以將變更儲存到 **app.xaml。** XAML 熱重新載入將會更新正在執行之應用程式的 UI，而不需要重建應用程式。 具體而言，每個頁面的背景色彩會變更。

4. 在 [ **Solution Pad**] 的 [ **附注** ] 專案中，開啟 [ **AppShell**]。 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">

        <Shell.Resources>
            <!-- Style Shell elements -->
            <Style x:Key="BaseStyle"
                   TargetType="Element">
                <Setter Property="Shell.BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="Shell.ForegroundColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TitleColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TabBarUnselectedColor"
                        Value="#95FFFFFF"/>
            </Style>
            <Style TargetType="TabBar"
                   BasedOn="{StaticResource BaseStyle}" />
        </Shell.Resources>

        <!-- Display a bottom tab bar containing two tabs -->
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```

    這段程式碼會在資源字典中新增兩種樣式 `Shell` ，以定義 [`Color`](xref:Xamarin.Forms.Color) 應用程式所使用的一連串值。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 AppShell 的變更 **。** XAML 熱重新載入將會更新正在執行之應用程式的 UI，而不需要重建應用程式。 明確地說，Shell chrome 的背景色彩會變更。

5. 在 [ **Solution Pad**] 的 [**附注**] 專案中，在 [ **Views** ] 資料夾中開啟 **>notespage.xaml.cs。** 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">

        <ContentPage.Resources>
            <!-- Define a visual state for the Selected state of the CollectionView -->
            <Style TargetType="StackLayout">
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal" />
                            <VisualState x:Name="Selected">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor"
                                            Value="{StaticResource AppPrimaryColor}" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>
        </ContentPage.Resources>

        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="{StaticResource PageMargin}"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium" />
                        <Label Text="{Binding Date}"
                               TextColor="{StaticResource TertiaryColor}"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    這段程式碼會為定義的隱含樣式 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，定義中每個選取專案的外觀 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 並將 `CollectionView.Margin`  和屬性設定 `Label.TextColor` 為應用層級中定義的值 `ResourceDictionary` 。 請注意，`StackLayout` 隱含樣式已新增至頁面層級的 `ResourceDictionary`，因為它只能由 `NotesPage` 使用。

    選擇 [檔案 **> 儲存** (或按 **&#8984; + S**) ，儲存 >notespage.xaml.cs 的變更 **。** XAML 熱重新載入將會更新正在執行之應用程式的 UI，而不需要重建應用程式。 具體而言，中選取專案的色彩 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會變更。

6. 在 [ **Solution Pad**] 的 [**附注**] 專案中，在 [ **Views** ] 資料夾中開啟 **>noteentrypage.xaml.cs。** 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>       
        </ContentPage.Resources>

        <!-- Layout children vertically -->
        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <!-- Layout children in two columns -->
            <Grid ColumnDefinitions="*,*">
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    這段程式碼會將的隱含樣式新增 [`Editor`](xref:Xamarin.Forms.Editor) 至頁面層級 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，並將 `StackLayout.Margin` 屬性設定為應用層級中定義的值 `ResourceDictionary` 。 請注意， `Editor` 隱含樣式已新增至頁面層級， `ResourceDictionary` 因為它只能由使用 `NoteEntryPage` 。

7. 在正在執行的應用程式中，流覽至 `NoteEntryPage` 。

8. 在 Visual Studio for Mac 中，選擇 [檔案] **> [儲存** (] 或按 **&#8984; + S**) ，儲存 >noteentrypage.xaml.cs 的變更 **。**

    XAML 熱重新載入將會更新應用程式的 UI，而不需要重建。 具體來說，的背景色彩 [`Editor`](xref:Xamarin.Forms.Editor) 會在執行中的應用程式中變更，就像物件的外觀一樣 [`Button`](xref:Xamarin.Forms.Button) 。

9. 在 [ **Solution Pad**] 的 [**附注**] 專案中，在 [ **Views** ] 資料夾中開啟 **>aboutpage。** 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{StaticResource AppPrimaryColor}"
                   Opacity="0.85"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->
            <StackLayout Grid.Row="1"
                         Margin="{StaticResource PageMargin}"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    這段程式碼會將 `Image.BackgroundColor` 和 `StackLayout.Margin` 屬性設定為應用層級中定義的值 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。

10. 在正在執行的應用程式中，流覽至 `AboutPage` 。

11. 在 Visual Studio for Mac 中，選擇 [檔案] **> [儲存** (] 或按 **&#8984; + S**) ，儲存 >aboutpage 的變更 **。**

    XAML 熱重新載入將會更新應用程式的 UI，而不需要重建。 具體來說，的背景色彩 [`Image`](xref:Xamarin.Forms.Editor) 會在執行中的應用程式中變更。

::: zone-end

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

- Xamarin.Forms使用 XAML 樣式來為 Shell 應用程式建立樣式。
- 使用 XAML 熱重新載入來查看 UI 變更，而不需要重建應用程式。

若要深入瞭解使用 Shell 進行應用程式開發的基本概念 Xamarin.Forms ，請繼續進行快速入門深入探討。

> [!div class="nextstepaction"]
> [下一步](deepdive.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [XAML 熱重新載入 Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md)
- [Xamarin.Forms 快速入門深入探討](deepdive.md)

---
title: 設計跨平台 Xamarin.Forms 應用程式
description: 這篇文章說明如何使用 XAML 樣式設定樣式的跨平台 Xamarin.Forms 應用程式。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 1b68afc1f3d3c57a5c336e9d30c97ce2375acb9f
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864351"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>跨平台 Xamarin.Forms 應用程式

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)

在本快速入門中，您將了解如何：

- 設定 Xamarin.Forms 應用程式使用 XAML 樣式的樣式。

快速入門逐步解說如何使用 XAML 樣式設定樣式的跨平台 Xamarin.Forms 應用程式。 最終的應用程式如下所示：

[![](styling-images/screenshots1-sml.png "備忘稿")](styling-images/screenshots1.png#lightbox "備忘稿")
[![](styling-images/screenshots2-sml.png "附註項目頁面")](styling-images/screenshots2.png#lightbox "附註項目頁面")

### <a name="prerequisites"></a>必要條件

您應該已成功完成[先前的快速入門](database.md)之前嘗試本快速入門。 或者，下載[先前的快速入門範例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)並使用它做為起點，在本快速入門。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio，並開啟備忘稿方案。

2. 在 [**方案總管] 中**，請在**備忘稿**專案中，按兩下**App.xaml**加以開啟。 然後以下列程式碼取代現有的程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    此程式碼定義[ `Thickness` ](xref:Xamarin.Forms.Thickness)值，就會有一系列[ `Color` ](xref:Xamarin.Forms.Color)值，以及隱含樣式[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)和[ `ContentPage`](xref:Xamarin.Forms.ContentPage). 注意，這些樣式，也就是在應用程式層級[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，可取用整個應用程式。 如需有關 XAML 樣式設定的詳細資訊，請參閱[樣式](deepdive.md#styling)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    變更儲存到**App.xaml**藉由按下**CTRL + S**，並關閉檔案。

3. 在 [**方案總管] 中**，請在**備忘稿**專案中，按兩下**NotesPage.xaml**加以開啟。 然後以下列程式碼取代現有的程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    這個程式碼加入的隱含樣式[ `ListView` ](xref:Xamarin.Forms.ListView)到頁面層級[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，並設定`ListView.Margin`屬性設為值定義於應用程式層級`ResourceDictionary`. 請注意，`ListView`隱含樣式已新增至 頁面層級`ResourceDictionary`，因為它只會取用`NotesPage`。 如需有關 XAML 樣式設定的詳細資訊，請參閱[樣式](deepdive.md#styling)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    變更儲存到**NotesPage.xaml**藉由按下**CTRL + S**，並關閉檔案。

4. 在 [**方案總管] 中**，請在**備忘稿**專案中，按兩下**NoteEntryPage.xaml**加以開啟。 然後以下列程式碼取代現有的程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    這個程式碼加入隱含樣式[ `Editor` ](xref:Xamarin.Forms.Editor)並[ `Button` ](xref:Xamarin.Forms.Button)頁面層級的檢視[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，並設定`StackLayout.Margin`屬性設為值定義於應用程式層級`ResourceDictionary`。 請注意，`Editor`並`Button`隱含樣式已新增至 頁面層級`ResourceDictionary`，因為它們僅供`NoteEntryPage`。 如需有關 XAML 樣式設定的詳細資訊，請參閱[樣式](deepdive.md#styling)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    變更儲存到**NoteEntryPage.xaml**藉由按下**CTRL + S**，並關閉檔案。

5. 建置並執行專案，每個平台。 如需詳細資訊，請參閱 <<c0> [ 建置快速入門](single-page.md#building-the-quickstart)。

    在上**NotesPage**按下 **+** 按鈕，巡覽至**NoteEntryPage**和輸入的附註。 在每個頁面上，觀察如何樣式已從先前的快速入門。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac，並開啟備忘稿專案。

2. 在  **Solution Pad**，請在**備忘稿**專案中，按兩下**App.xaml**加以開啟。 然後以下列程式碼取代現有的程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    此程式碼定義[ `Thickness` ](xref:Xamarin.Forms.Thickness)值，就會有一系列[ `Color` ](xref:Xamarin.Forms.Color)值，以及隱含樣式[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)和[ `ContentPage`](xref:Xamarin.Forms.ContentPage). 注意，這些樣式，也就是在應用程式層級[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，可取用整個應用程式。 如需有關 XAML 樣式設定的詳細資訊，請參閱[樣式](deepdive.md#styling)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    變更儲存到**App.xaml**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

3. 在  **Solution Pad**，請在**備忘稿**專案中，按兩下**NotesPage.xaml**加以開啟。 然後以下列程式碼取代現有的程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    這個程式碼加入的隱含樣式[ `ListView` ](xref:Xamarin.Forms.ListView)到頁面層級[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，並設定`ListView.Margin`屬性設為值定義於應用程式層級`ResourceDictionary`. 請注意，`ListView`隱含樣式已新增至 頁面層級`ResourceDictionary`，因為它只會取用`NotesPage`。 如需有關 XAML 樣式設定的詳細資訊，請參閱[樣式](deepdive.md#styling)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    變更儲存到**NotesPage.xaml**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

4. 在  **Solution Pad**，請在**備忘稿**專案中，按兩下**NoteEntryPage.xaml**加以開啟。 然後以下列程式碼取代現有的程式碼：

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    這個程式碼加入隱含樣式[ `Editor` ](xref:Xamarin.Forms.Editor)並[ `Button` ](xref:Xamarin.Forms.Button)頁面層級的檢視[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，並設定`StackLayout.Margin`屬性設為值定義於應用程式層級`ResourceDictionary`。 請注意，`Editor`並`Button`隱含樣式已新增至 頁面層級`ResourceDictionary`，因為它們僅供`NoteEntryPage`。 如需有關 XAML 樣式設定的詳細資訊，請參閱[樣式](deepdive.md#styling)中[Xamarin.Forms 快速入門深入探討](deepdive.md)。

    變更儲存到**NoteEntryPage.xaml**選擇**檔案 > 儲存**(或按下 **&#8984; + S**)，然後關閉檔案。

5. 建置並執行專案，每個平台。 如需詳細資訊，請參閱 <<c0> [ 建置快速入門](single-page.md#building-the-quickstart)。

    在上**NotesPage**按下 **+** 按鈕，巡覽至**NoteEntryPage**和輸入的附註。 在每個頁面上，觀察如何樣式已從先前的快速入門。

::: zone-end


## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

- 設定 Xamarin.Forms 應用程式使用 XAML 樣式的樣式。

若要深入了解使用 Xamarin.Forms 開發應用程式的基本概念，繼續進行快速入門深入探討。

> [!div class="nextstepaction"]
> [下一個](deepdive.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)
- [Xamarin.Forms 快速入門深入探討](deepdive.md)

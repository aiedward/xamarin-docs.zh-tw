---
title: 設計跨平台 Xamarin.Forms 應用程式
description: 本文說明如何使用 XAML 樣式，為跨平臺的 Xamarin. Forms 應用程式建立樣式。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 688b0e87bb6281923d3099c0d269b1c2554b6c7a
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70756762"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>建立跨平臺 Xamarin 應用程式的樣式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

在本快速入門中，您將瞭解如何：

- 使用 XAML 樣式，將 Xamarin. Forms 應用程式樣式。

本快速入門將逐步解說如何使用 XAML 樣式來為跨平臺的 Xamarin. Forms 應用程式建立樣式。 最終的應用程式如下所示：

[![](styling-images/screenshots1-sml.png "Notes Page")](styling-images/screenshots1.png#lightbox "Notes Page")
[![](styling-images/screenshots2-sml.png "Note Entry Page")](styling-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Prerequisites

您應該先成功完成[先前的快速入門](database.md)，再嘗試進行本快速入門。 或者，下載[先前的快速入門範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)，並使用它做為本快速入門的起點。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio 並開啟 [附注] 解決方案。

2. 在**方案總管**的 [ **Notes** ] 專案中，按兩下 [App.xaml] 以開啟**應用程式**。 然後將現有的程式碼取代為下列程式碼：

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

    此程式碼會定義[`Thickness`](xref:Xamarin.Forms.Thickness)值、一系列的[`Color`](xref:Xamarin.Forms.Color)值，以及[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)和[`ContentPage`](xref:Xamarin.Forms.ContentPage)的隱含樣式。 請注意，應用層級[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中的這些樣式可在整個應用程式中使用。 如需 XAML 樣式的詳細資訊，請參閱 Xamarin 中的[樣式](deepdive.md#styling)[快速入門深入探討](deepdive.md)。

    按下**CTRL + S**，將變更儲存至**app.xaml** ，然後關閉檔案。

3. 在**方案總管**的**Notes**專案中，按兩下**NotesPage**以將其開啟。 然後將現有的程式碼取代為下列程式碼：

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

    這段程式碼會將[`ListView`](xref:Xamarin.Forms.ListView)的隱含樣式加入頁面層級[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，並將 `ListView.Margin` 屬性設定為應用層級 `ResourceDictionary` 中定義的值。 請注意，`ListView` 的隱含樣式已加入頁面層級 `ResourceDictionary`，因為它只會由 `NotesPage` 使用。 如需 XAML 樣式的詳細資訊，請參閱 Xamarin 中的[樣式](deepdive.md#styling)[快速入門深入探討](deepdive.md)。

    按下**CTRL + S**，將變更儲存到**NotesPage** ，然後關閉檔案。

4. 在**方案總管**的**Notes**專案中，按兩下**NoteEntryPage**以將其開啟。 然後將現有的程式碼取代為下列程式碼：

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

    此程式碼會將[`Editor`](xref:Xamarin.Forms.Editor)和[`Button`](xref:Xamarin.Forms.Button)視圖的隱含樣式加入頁面層級[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，並將 `StackLayout.Margin` 屬性設定為應用層級 `ResourceDictionary` 中定義的值。 請注意，`Editor` 和 `Button` 隱含樣式已加入頁面層級 `ResourceDictionary`，因為它們只會由 `NoteEntryPage` 使用。 如需 XAML 樣式的詳細資訊，請參閱 Xamarin 中的[樣式](deepdive.md#styling)[快速入門深入探討](deepdive.md)。

    按下**CTRL + S**，將變更儲存到**NoteEntryPage** ，然後關閉檔案。

5. 在每個平臺上建立並執行專案。 如需詳細資訊，請參閱[建立快速入門](single-page.md#building-the-quickstart)。

    在 [ **NotesPage** ] 上，按 [ **+** ] 按鈕以流覽至**NoteEntryPage**並輸入附注。 在每一頁上，觀察如何從先前的快速入門中變更樣式。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac 並開啟 [Notes] 專案。

2. 在  **Solution Pad**的**Notes**專案中，按兩下  **app.xaml**  加以開啟。 然後將現有的程式碼取代為下列程式碼：

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

    此程式碼會定義[`Thickness`](xref:Xamarin.Forms.Thickness)值、一系列的[`Color`](xref:Xamarin.Forms.Color)值，以及[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)和[`ContentPage`](xref:Xamarin.Forms.ContentPage)的隱含樣式。 請注意，應用層級[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中的這些樣式可在整個應用程式中使用。 如需 XAML 樣式的詳細資訊，請參閱 Xamarin 中的[樣式](deepdive.md#styling)[快速入門深入探討](deepdive.md)。

    選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），然後關閉檔案，以儲存對**app.xaml**的變更。

3. 在  **Solution Pad**的**Notes**專案中，按兩下  **NotesPage**  加以開啟。 然後將現有的程式碼取代為下列程式碼：

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

    這段程式碼會將[`ListView`](xref:Xamarin.Forms.ListView)的隱含樣式加入頁面層級[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，並將 `ListView.Margin` 屬性設定為應用層級 `ResourceDictionary` 中定義的值。 請注意，`ListView` 的隱含樣式已加入頁面層級 `ResourceDictionary`，因為它只會由 `NotesPage` 使用。 如需 XAML 樣式的詳細資訊，請參閱 Xamarin 中的[樣式](deepdive.md#styling)[快速入門深入探討](deepdive.md)。

    選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），將變更儲存到**NotesPage** ，然後關閉檔案。

4. 在  **Solution Pad**的**Notes**專案中，按兩下  **NoteEntryPage**  加以開啟。 然後將現有的程式碼取代為下列程式碼：

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

    此程式碼會將[`Editor`](xref:Xamarin.Forms.Editor)和[`Button`](xref:Xamarin.Forms.Button)視圖的隱含樣式加入頁面層級[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，並將 `StackLayout.Margin` 屬性設定為應用層級 `ResourceDictionary` 中定義的值。 請注意，`Editor` 和 `Button` 隱含樣式已加入頁面層級 `ResourceDictionary`，因為它們只會由 `NoteEntryPage` 使用。 如需 XAML 樣式的詳細資訊，請參閱 Xamarin 中的[樣式](deepdive.md#styling)[快速入門深入探討](deepdive.md)。

    選擇 [檔案] **> [儲存**] （或按 **&#8984; + S**），將變更儲存到**NoteEntryPage** ，然後關閉檔案。

5. 在每個平臺上建立並執行專案。 如需詳細資訊，請參閱[建立快速入門](single-page.md#building-the-quickstart)。

    在 [ **NotesPage** ] 上，按 [ **+** ] 按鈕以流覽至**NoteEntryPage**並輸入附注。 在每一頁上，觀察如何從先前的快速入門中變更樣式。

::: zone-end

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已瞭解如何：

- 使用 XAML 樣式，將 Xamarin. Forms 應用程式樣式。

若要深入瞭解使用 Xamarin 進行應用程式開發的基本概念，請繼續進行深入瞭解的快速入門。

> [!div class="nextstepaction"]
> [下一步](deepdive.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Xamarin. 表單快速入門深入探討](deepdive.md)

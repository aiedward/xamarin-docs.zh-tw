---
title: 設計跨平台 Xamarin.Forms 應用程式
description: 本文說明如何使用 XAML 樣式設定跨平台 Xamarin.Forms 應用程式樣式。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/07/2020
ms.openlocfilehash: fbd957c68d7a9aa2f8e44c91fab6174d8ed72014
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "77068733"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>設定跨平台的 Xamarin.Forms 應用程式樣式

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

在本快速入門中，您將了解如何：

- 使用 XAML 樣式設定 Xamarin. Forms 應用程式樣式。

快速入門會逐步說明如何使用 XAML 樣式設定跨平台的 Xamarin.Forms 應用程式樣式。 最終的應用程式如下所示：

[![](styling-images/screenshots1-sml.png "Notes Page")](styling-images/screenshots1.png#lightbox "Notes Page")
[![](styling-images/screenshots2-sml.png "Note Entry Page")](styling-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Prerequisites

您應該先成功完成[先前的快速入門](database.md)，再嘗試本快速入門。 或者，下載[先前的快速入門範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)，並使用此範例作為本快速入門的起點。

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>使用 Visual Studio 更新應用程式

1. 啟動 Visual Studio 並開啟 Notes 方案。

2. 在 [方案總管]**** 的 **Notes** 專案中，按兩下 **App.xaml** 以開啟此檔案。 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    此[`Thickness`](xref:Xamarin.Forms.Thickness)代碼定義[`Color`](xref:Xamarin.Forms.Color)[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)和[`ContentPage`](xref:Xamarin.Forms.ContentPage)的值、一系列值和隱式樣式。 請注意,這些樣式位於應用程式級別[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary),可以在整個應用程式中使用。 如需設定 XAML 樣式的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[設定樣式](deepdive.md#styling)。

    按下 **CTRL+S** 以將變更儲存到 **App.xaml**，然後關閉檔案。

3. 在 [方案總管]**** 的 **Notes** 專案中，按兩下 **NotesPage.xaml** 以開啟此檔案。 將現有程式碼取代成下列程式碼：

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
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    此代碼向[`ListView`](xref:Xamarin.Forms.ListView)頁[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)級 添加隱式樣式`ListView.Margin`,並將 屬性設置`ResourceDictionary`到應用程式級別 中定義的值。 請注意，`ListView` 隱含樣式已新增至頁面層級的 `ResourceDictionary`，因為它只能由 `NotesPage` 使用。 如需設定 XAML 樣式的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[設定樣式](deepdive.md#styling)。

    按下 **CTRL+S** 將變更儲存到 **NotesPage.xaml**，然後關閉檔案。

4. 在 [方案總管]**** 的 **Notes** 專案中，按兩下 **NoteEntryPage.xaml** 以開啟此檔案。 將現有程式碼取代成下列程式碼：

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
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
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

    此[`Editor`](xref:Xamarin.Forms.Editor)代碼將和[`Button`](xref:Xamarin.Forms.Button)檢視的隱式樣式添加到頁級別[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary),`StackLayout.Margin`並將 屬性設置到`ResourceDictionary`應用程式級別 中定義的值。 請注意，`Editor` 和 `Button` 隱含樣式已新增至頁面層級的 `ResourceDictionary`，因為它們只能由 `NoteEntryPage` 使用。 如需設定 XAML 樣式的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[設定樣式](deepdive.md#styling)。

    按下 **CTRL+S** 將變更儲存到 **NoteEntryPage.xaml**，然後關閉檔案。

5. 在每個平台上建置並執行專案。 如需詳細資訊，請參閱[建置快速入門](single-page.md#building-the-quickstart)。

    在 **NotesPage** 上按 **+** 按鈕，以巡覽至 **NoteEntryPage** 並輸入備註。 在每張頁面中，觀察樣式設定如何自上一個快速入門變更。

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>使用 Visual Studio for Mac 更新應用程式

1. 啟動 Visual Studio for Mac 並開啟 Notes 專案。

2. 在 **Solution Pad** 的 **Notes** 專案中，按兩下 **App.xaml** 以開啟此檔案。 將現有程式碼取代成下列程式碼：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    此[`Thickness`](xref:Xamarin.Forms.Thickness)代碼定義[`Color`](xref:Xamarin.Forms.Color)[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)和[`ContentPage`](xref:Xamarin.Forms.ContentPage)的值、一系列值和隱式樣式。 請注意,這些樣式位於應用程式級別[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary),可以在整個應用程式中使用。 如需設定 XAML 樣式的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[設定樣式](deepdive.md#styling)。

    選擇 [檔案] > [儲存]**** (或按下 **&#8984; + S**) 將變更儲存到 **App.xaml**，然後關閉檔案。

3. 在 **Solution Pad** 的 **Notes** 專案中，按兩下 **NotesPage.xaml** 以開啟此檔案。 將現有程式碼取代成下列程式碼：

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
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    此代碼向[`ListView`](xref:Xamarin.Forms.ListView)頁[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)級 添加隱式樣式`ListView.Margin`,並將 屬性設置`ResourceDictionary`到應用程式級別 中定義的值。 請注意，`ListView` 隱含樣式已新增至頁面層級的 `ResourceDictionary`，因為它只能由 `NotesPage` 使用。 如需設定 XAML 樣式的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[設定樣式](deepdive.md#styling)。

    選擇 [檔案] > [儲存]**** (或按下 **&#8984; + S**) 將變更儲存到 **NotesPage.xaml**，然後關閉檔案。

4. 在 **Solution Pad** 的 **Notes** 專案中，按兩下 **NoteEntryPage.xaml** 以開啟此檔案。 將現有程式碼取代成下列程式碼：

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
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
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

    此[`Editor`](xref:Xamarin.Forms.Editor)代碼將和[`Button`](xref:Xamarin.Forms.Button)檢視的隱式樣式添加到頁級別[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary),`StackLayout.Margin`並將 屬性設置到`ResourceDictionary`應用程式級別 中定義的值。 請注意，`Editor` 和 `Button` 隱含樣式已新增至頁面層級的 `ResourceDictionary`，因為它們只能由 `NoteEntryPage` 使用。 如需設定 XAML 樣式的詳細資訊，請參閱 [Xamarin.Forms 快速入門深入探討](deepdive.md)中的[設定樣式](deepdive.md#styling)。

    選擇 [檔案] > [儲存]**** (或按下 **&#8984; + S**) 將變更儲存到 **NoteEntryPage.xaml**，然後關閉檔案。

5. 在每個平台上建置並執行專案。 如需詳細資訊，請參閱[建置快速入門](single-page.md#building-the-quickstart)。

    在 **NotesPage** 上按 **+** 按鈕，以巡覽至 **NoteEntryPage** 並輸入備註。 在每張頁面中，觀察樣式設定如何自上一個快速入門變更。

::: zone-end

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何：

- 使用 XAML 樣式設定 Xamarin. Forms 應用程式樣式。

若要深入了解使用 Xamarin.Forms 開發應用程式的基礎，請繼續下一篇快速入門深入探討。

> [!div class="nextstepaction"]
> [下一步](deepdive.md)

## <a name="related-links"></a>相關連結

- [Notes (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Xamarin.Forms 快速入門深入探討](deepdive.md)

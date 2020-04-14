---
ms.openlocfilehash: d87289e481b69592b68627d053e937856d3d6067
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "61375384"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中，修改 [`Grid`](xref:Xamarin.Forms.Grid) 宣告以定義資料行和資料列，並在資料行和資料列中放置內容：

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    此程式碼會定義 [`Grid`](xref:Xamarin.Forms.Grid) 的資料行和資料列，並將 [`Label`](xref:Xamarin.Forms.Label) 執行個體放置到特定資料行和資料列中。 資料行和資料列資料會儲存在 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) 和 [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 屬性中，其分別是 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 和 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 物件的集合。 每個 `ColumnDefinition` 的寬度都是由 [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 屬性設定，而每個 `RowDefinition` 的高度則是由 [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height) 屬性設定。 有效的寬度和高度值為：

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto)，可將資料行或資料列的大小調整為適合內容。
    - 比例值，可將資料行和資料列的大小調整為剩餘空間的比例。 比例值會以結尾是 `*` 來表示。
    - 絕對值，可以特定的固定值調整資料行或資料列的大小。

    因此在上述程式碼中，每個資料行的寬度都是 [`Grid`](xref:Xamarin.Forms.Grid) 的一半，而每個資料列的高度均為 50 個裝置獨立單位。

    [`Label`](xref:Xamarin.Forms.Label) 中每個 [`Grid`](xref:Xamarin.Forms.Grid) 的位置都是以 [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) 和 [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) 附加屬性指定，並使用以零為起始的索引。 因此，第一個資料行是資料行 0，而第一個資料列是資料列 0。 第一個 `Label` 缺少這些附加屬性，因為未設定這些屬性的所有子檢視都會自動呈現於資料行 0、資料列 0。

    > [!NOTE]
    > 您可使用 [`Grid`](xref:Xamarin.Forms.Grid) 和 [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) 屬性設定 [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) 中資料行與資料列之間的間距。 如需詳細資訊，請參閱 [Xamarin.Forms 方格](~/xamarin-forms/user-interface/layouts/grid.md#spacing)指南中的[間距](~/xamarin-forms/user-interface/layouts/grid.md)。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：在 iOS 和 Android 上，以欄和列顯示內容的 Grid](../images/columns-rows.png "以欄和列顯示內容的 Grid")](../images/columns-rows-large.png#lightbox "以欄和列顯示內容的 Grid")

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中，修改 [`Grid`](xref:Xamarin.Forms.Grid) 宣告以定義資料行和資料列，並在資料行和資料列中放置內容：

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="50" />
        </Grid.RowDefinitions>
        <Label Text="Column 0, Row 0" />
        <Label Grid.Column="1"
               Text="Column 1, Row 0" />
        <Label Grid.Row="1"
               Text="Column 0, Row 1" />
        <Label Grid.Column="1"
               Grid.Row="1"
               Text="Column 1, Row 1" />
    </Grid>
    ```

    此程式碼會定義 [`Grid`](xref:Xamarin.Forms.Grid) 的資料行和資料列，並將 [`Label`](xref:Xamarin.Forms.Label) 執行個體放置到特定資料行和資料列中。 資料行和資料列資料會儲存在 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) 和 [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 屬性中，其分別是 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 和 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 物件的集合。 每個 `ColumnDefinition` 的寬度都是由 [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 屬性設定，而每個 `RowDefinition` 的高度則是由 [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height) 屬性設定。 有效的寬度和高度值為：

    - [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto)，可將資料行或資料列的大小調整為適合內容。
    - 比例值，可將資料行和資料列的大小調整為剩餘空間的比例。 比例值會以結尾是 `*` 來表示。
    - 絕對值，可以特定的固定值調整資料行或資料列的大小。

    因此在上述程式碼中，每個資料行的寬度都是 [`Grid`](xref:Xamarin.Forms.Grid) 的一半，而每個資料列的高度均為 50 個裝置獨立單位。

    [`Label`](xref:Xamarin.Forms.Label) 中每個 [`Grid`](xref:Xamarin.Forms.Grid) 的位置都是以 [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) 和 [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) 附加屬性指定，並使用以零為起始的索引。 因此，第一個資料行是資料行 0，而第一個資料列是資料列 0。 第一個 `Label` 缺少這些附加屬性，因為未設定這些屬性的所有子檢視都會自動呈現於資料行 0、資料列 0。

    > [!NOTE]
    > 您可使用 [`Grid`](xref:Xamarin.Forms.Grid) 和 [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) 屬性設定 [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) 中資料行與資料列之間的間距。 如需詳細資訊，請參閱 [Xamarin.Forms 方格](~/xamarin-forms/user-interface/layouts/grid.md#spacing)指南中的[間距](~/xamarin-forms/user-interface/layouts/grid.md)。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：在 iOS 和 Android 上，以欄和列顯示內容的 Grid](../images/columns-rows.png "以欄和列顯示內容的 Grid")](../images/columns-rows-large.png#lightbox "以欄和列顯示內容的 Grid")

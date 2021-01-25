---
ms.openlocfilehash: 89b56e3733d1c75ed616f79508d555ccb0c51f00
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634864"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`Grid`](xref:Xamarin.Forms.Grid) 宣告，以定義資料行和資料列，並放置跨越資料行和資料列的內容：

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Grid.ColumnSpan="2"
               Text="This text uses the ColumnSpan property to span both columns." />
        <Label Grid.Row="1"
               Grid.RowSpan="2"
               Text="This text uses the RowSpan property to span two rows." />
    </Grid>
    ```

    此程式碼會定義 [`Grid`](xref:Xamarin.Forms.Grid) 的資料行和資料列，並將 [`Label`](xref:Xamarin.Forms.Label) 執行個體放置到特定資料行和資料列中。 第一個 `Label` 會設定 [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) 附加屬性，使其文字跨越多個資料行。 `ColumnSpan` 屬性會設定為 2，這表示 `Label` 將會跨越的資料行數目。 第二個 `Label` 會設定 [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) 附加屬性，使其文字跨越多個資料列。 `RowSpan` 屬性會設定為 2，這表示 `Label` 將會跨越的資料列數目。

1. 如果應用程式仍在執行，請將變更儲存至檔案，應用程式使用者介面將會在您的模擬器中自動更新。 否則，請在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，啟動所選 iOS 遠端模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：在 iOS 和 Android 上，內容跨越多個欄和列的 Grid](../images/span-columns-rows.png "內容跨越欄和列的 Grid")](../images/span-columns-rows-large.png#lightbox "內容跨越欄和列的 Grid")

    在 Visual Studio 中，停止應用程式。

    如需如何跨越資料行和資料列的詳細資訊，請參閱 [Xamarin.Forms 方格](~/xamarin-forms/user-interface/layouts/grid.md)指南 (機器翻譯) 中的[資料列和資料行](~/xamarin-forms/user-interface/layouts/grid.md#rows-and-columns)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`Grid`](xref:Xamarin.Forms.Grid) 宣告，以定義資料行和資料列，並放置跨越資料行和資料列的內容：

    ```xaml
    <Grid Margin="20,35,20,20">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.5*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Grid.ColumnSpan="2"
               Text="This text uses the ColumnSpan property to span both columns." />
        <Label Grid.Row="1"
               Grid.RowSpan="2"
               Text="This text uses the RowSpan property to span two rows." />
    </Grid>
    ```

    此程式碼會定義 [`Grid`](xref:Xamarin.Forms.Grid) 的資料行和資料列，並將 [`Label`](xref:Xamarin.Forms.Label) 執行個體放置到特定資料行和資料列中。 第一個 `Label` 會設定 [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) 附加屬性，使其文字跨越多個資料行。 `ColumnSpan` 屬性會設定為 2，這表示 `Label` 將會跨越的資料行數目。 第二個 `Label` 會設定 [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) 附加屬性，使其文字跨越多個資料列。 `RowSpan` 屬性會設定為 2，這表示 `Label` 將會跨越的資料列數目。

1. 如果應用程式仍在執行，請將變更儲存至檔案，應用程式使用者介面將會在您的模擬器中自動更新。 否則，請在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：在 iOS 和 Android 上，內容跨越多個欄和列的 Grid](../images/span-columns-rows.png "內容跨越欄和列的 Grid")](../images/span-columns-rows-large.png#lightbox "內容跨越欄和列的 Grid")

    在 Visual Studio for Mac 中，停止應用程式。

    如需如何跨越資料行和資料列的詳細資訊，請參閱 [Xamarin.Forms 方格](~/xamarin-forms/user-interface/layouts/grid.md)指南 (機器翻譯) 中的[資料列和資料行](~/xamarin-forms/user-interface/layouts/grid.md#rows-and-columns)。

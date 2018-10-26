---
title: Xamarin.Forms TableView
description: 這篇文章說明如何使用 Xamarin.Forms TableView 類別來呈現應用程式中的捲動功能表、 設定和輸入的表單。
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/04/2018
ms.openlocfilehash: b8e851e735fa39d015e22ce511c39ad825bc97c9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119983"
---
# <a name="xamarinforms-tableview"></a>Xamarin.Forms TableView

[TableView](xref:Xamarin.Forms.TableView)是一種檢視來顯示可捲動清單的資料或選擇在不會共用相同的範本的資料列。 不同於[ListView](~/xamarin-forms/user-interface/listview/index.md)，TableView 沒有別的`ItemsSource`，因此項目必須做為子系手動加入。

本指南是由下列各節：

- **[使用案例](#Use_Cases)** &ndash;何時使用 TableView，而不是 ListView 或自訂的檢視。
- **[TableView 結構](#TableView_Structure)** &ndash;需要在 TableView 內的檢視階層。
- **[TableView 外觀](#TableView_Appearance)** &ndash; TableView 的自訂選項。
- **[內建的儲存格](#Built-In_Cells)** &ndash;內建的儲存格的選項，包括[EntryCell](#EntryCell)並[SwitchCell](#SwitchCell)。
- **[自訂資料格](#Custom_Cells)** &ndash;如何製作您自己自訂的資料格。

![](tableview-images/tableview-all-sml.png "TableView 範例")

<a name="Use_Cases" />

## <a name="use-cases"></a>使用案例

TableView 很有用：

- 顯示設定清單
- 在表單中收集資料或
- 顯示資料列 （例如數字、 百分比和映像） 從資料列以不同的方式呈現的資料。

捲動和吸引人的各節，一般都需要針對上述案例中的資料列的版面配置，則會處理 TableView。 `TableView`控制項可讓您使用每個平台的基礎時可供使用，建立每個平台的原生外觀的對等檢視。

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>TableView 結構

中的項目`TableView`區分成區段。 根目錄`TableView`已`TableRoot`，這是一或多個父`TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

每個`TableSection`標題和一或多個 ViewCells 所組成。 我們在這裡看到`TableSection`的`Title`屬性設定為 *「 環 」* 建構函式：

```csharp
var section = new TableSection ("Ring") { //TableSection constructor takes title as an optional parameter
    new SwitchCell {Text = "New Voice Mail"},
    new SwitchCell {Text = "New Mail", On = true}
};
```

若要完成相同的配置與以上所述在 XAML 中：

```xaml
<TableView Intent="Settings">
    <TableRoot>
        <TableSection Title="Ring">
            <SwitchCell Text="New Voice Mail" />
      <SwitchCell Text="New Mail" On="true" />
        </TableSection>
    </TableRoot>
</TableView>
```

<a name="TableView_Appearance" />

## <a name="tableview-appearance"></a>TableView 外觀

TableView 公開`Intent`屬性，這是列舉下列其中一個選項：

- **資料**&ndash;用於顯示資料的項目時。 請注意， [ListView](~/xamarin-forms/user-interface/listview/index.md)可能是更好的選項，可捲動的資料清單。
- **表單** &ndash; TableView 做為表單時。
- **功能表**&ndash;呈現一個功能表的 選取項目時使用。
- **設定**&ndash;時顯示的組態設定清單。

`TableIntent`您選擇可能會影響如何`TableView`會出現在每個平台上。 即使有未清除的差異，最好在其中選取`TableIntent`最符合您想要使用資料表的方式。

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>內建的儲存格

Xamarin.Forms 會隨附內建的儲存格，來收集和顯示資訊。 雖然 ListView 和 TableView 可以使用所有相同的資料格，以下是最相關 TableView 案例：

- **SwitchCell** &ndash;與展示和擷取為 true/false 狀態，以及文字標籤。
- **EntryCell** &ndash;與展示和擷取文字。

請參閱[ListView 儲存格的外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)如需詳細的說明[TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell)並[ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell)。

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 是用來呈現和擷取控制項開啟/關閉或`true` / `false`狀態。

SwitchCells 有一行文字來編輯和刳弮屬性。 這兩個屬性是可繫結。

- `Text` &ndash; 要顯示參數旁邊的文字。
- `On` &ndash; 是否為 on 或 off，會顯示參數。

請注意，`SwitchCell`公開`OnChanged`事件，讓您回應中的儲存格狀態變更。

![](tableview-images/switch-cell.png "SwitchCell 範例")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](xref:Xamarin.Forms.EntryCell) 您需要顯示使用者可以編輯的文字資料時很有用。 `EntryCell`s 提供了可自訂的下列屬性：

- `Keyboard` &ndash; 若要在編輯時顯示鍵盤。 有件事，例如數值、 電子郵件、 電話號碼等選項。[請參閱 API 文件](xref:Xamarin.Forms.Keyboard)。
- `Label` &ndash; 要顯示的文字輸入欄位右邊的標籤文字。
- `LabelColor` &ndash; 標籤文字的色彩。
- `Placeholder` &ndash; 它是 null 或空白時顯示的項目欄位中的文字。 文字項目開始時，這段文字就會消失。
- `Text` &ndash; 中的項目欄位的文字。
- `HorizontalTextAlignment` &ndash; 文字的水平對齊方式。 可以 center，左邊或右邊對齊。 [請參閱 API 文件](xref:Xamarin.Forms.TextAlignment)。

請注意，`EntryCell`公開`Completed`事件，當使用者叫用 [完成] 鍵盤上編輯文字時引發。

![](tableview-images/entry-cell.png "EntryCell 範例")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>自訂資料格
當內建的儲存格不足夠時，自訂資料格可用來呈現，並擷取資料對您的應用程式有意義的方式。 例如，您可能要呈現的滑桿，以允許使用者選擇的映像不透明度。

所有自訂儲存格必須衍生自[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，所有的內建的儲存格類型使用的相同基底類別。

這是自訂的資料格的範例：

![](tableview-images/custom-cell.png "自訂儲存格範例")

### <a name="xaml"></a>XAML
若要建立上述的版面配置 XAML 如下：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="DemoTableView.TablePage" Title="TableView">
    <ContentPage.Content>
        <TableView Intent="Settings">
            <TableRoot>
                <TableSection Title="Getting Started">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Image Source="bulb.png" />
                            <Label Text="left"
                              TextColor="#f35e20" />
                            <Label Text="right"
                              HorizontalOptions="EndAndExpand"
                              TextColor="#503026" />
                        </StackLayout>
                    </ViewCell>
                </TableSection>
            </TableRoot>
        </TableView>
    </ContentPage.Content>
</ContentPage>

```

上述 XAML 進行的很多。 我們細分：

- 根項目底下`TableView`是`TableRoot`。
- 沒有`TableSection`正下方緊接`TableRoot`。
- `ViewCell`正下方區段定義。 不同於`ListView`，`TableView`不需要該自訂 （或任何） 中所定義的資料格`ItemTemplate`。
- StackLayout 用來管理自訂的儲存格的版面配置。 可以在這裡使用任何版面配置。

### <a name="cnum"></a>C&num;

因為`TableView`適用於靜態資料，或以手動方式變更的資料，它並沒有項目範本的概念。 相反地，自訂資料格可以是以手動方式建立並放入資料表。 建立自訂的技術資料格，請注意繼承自`ViewCell`，然後將它新增至`TableView`像您一樣的內建的儲存格，也支援。
以下是 c# 程式碼以完成上述的版面配置：

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() {Source = "bulb.png"});
layout.Children.Add (new Label() {
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label () {
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot () {
    new TableSection("Getting Started") {
        new ViewCell() {View = layout}
    }
};

Content = table;
```

C# 上面執行很多。 我們細分：

- 根項目底下`TableView`是`TableRoot`。
- 沒有`TableSection`正下方緊接`TableRoot`。
- `ViewCell`正下方區段定義。 不同於`ListView`，`TableView`不需要該自訂 （或任何） 中所定義的資料格`ItemTemplate`。
- StackLayout 用來管理自訂的儲存格的版面配置。 可以在這裡使用任何版面配置。

請注意，永遠不會定義自訂的儲存格的類別。 相反地，`ViewCell`的檢視設定的特定執行個體`ViewCell`。

## <a name="row-height"></a>資料列高度

[ `TableView` ](xref:Xamarin.Forms.TableView)類別具有可用來變更資料格的資料列高度的兩個屬性：

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) – 設定每個資料列的高度`int`。
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) -資料列有不同的高度，如果設定為`true`。 請注意，當此屬性設定為`true`，資料列高度會自動計算並套用 Xamarin.Forms。

當內容中的資料格的高度[ `TableView` ](xref:Xamarin.Forms.TableView)變更時，資料列高度會隱含地更新 Android 和通用 Windows 平台 (UWP) 上。 不過，在 iOS 上它必須強制設定，更新[ `HasUnevenRows` ](xref:Xamarin.Forms.TableView.HasUnevenRows)屬性設`true`並藉由呼叫[ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法。

下列 XAML 範例所示[ `TableView` ](xref:Xamarin.Forms.TableView) ，其中包含[ `ViewCell` ](xref:Xamarin.Forms.ViewCell):

```xaml
<ContentPage ...>
    <TableView ...
               HasUnevenRows="true">
        <TableRoot>
            ...
            <TableSection ...>
                ...
                <ViewCell x:Name="_viewCell"
                          Tapped="OnViewCellTapped">
                    <Grid Margin="15,0">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Label Text="Tap this cell." />
                        <Label x:Name="_target"
                               Grid.Row="1"
                               Text="The cell has changed size."
                               IsVisible="false" />
                    </Grid>
                </ViewCell>
            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

當[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)點選時，`OnViewCellTapped`事件處理常式會執行：

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

`OnViewCellTapped`事件處理常式顯示或隱藏第二個[ `Label` ](xref:Xamarin.Forms.Label)中[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，並明確更新資料格的大小，藉由呼叫[ `Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法。

下列螢幕擷取畫面顯示儲存格之前被點選時：

![](tableview-images/cell-beforeresize.png "調整大小之前的 ViewCell")

下列螢幕擷取畫面會顯示儲存格後被點選時：

![](tableview-images/cell-afterresize.png "調整大小之後的 ViewCell")

> [!IMPORTANT]
> 如果這項功能過度使用而使，沒有強式可能的效能降低。

## <a name="related-links"></a>相關連結

- [TableView （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)

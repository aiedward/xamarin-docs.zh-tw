---
title: Xamarin. Forms TableView
description: 本文說明如何使用 TableView 類別，在應用程式中呈現捲動功能表、設定和輸入表單。
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: 67625aa413880023cce6d3e5e21e4d3bd0ec8e4c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695991"
---
# <a name="xamarinforms-tableview"></a>Xamarin. Forms TableView

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView)是一種視圖，用於顯示可滾動的資料或選項清單，其中有不會共用相同範本的資料列。 不同于[ListView](~/xamarin-forms/user-interface/listview/index.md)，`TableView` 不具有 `ItemsSource` 的概念，因此必須以手動方式將專案新增為子系。

![TableView 範例](tableview-images/tableview-all-sml.png)

<a name="Use_Cases" />

## <a name="use-cases"></a>使用案例

當下列情況時， [`TableView`](xref:Xamarin.Forms.TableView)非常有用：

- 顯示設定清單，
- 在表單中收集資料，或
- 顯示以不同于資料列的方式呈現的資料（例如數位、百分比和影像）。

[`TableView`](xref:Xamarin.Forms.TableView)在吸引人的章節中處理資料列的滾動和版面配置，這是上述案例的常見需求。 @No__t_0 控制項會在可用時使用每個平臺的基礎對等視圖，建立每個平臺的原生外觀。

<a name="TableView_Structure" />

## <a name="structure"></a>結構

[@No__t_1](xref:Xamarin.Forms.TableView)中的元素會組織成幾個區段。 在 `TableView` 的根目錄是[`TableRoot`](xref:Xamarin.Forms.TableRoot)，這是一個或多個[`TableSection`](xref:Xamarin.Forms.TableSection)實例的父系。 每個[`TableSection`](xref:Xamarin.Forms.TableSection)都包含一個標題，以及一或多個[`ViewCell`](xref:Xamarin.Forms.ViewCell)實例：

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

對等的 C# 程式碼為：

```csharp
Content = new TableView
{
    Root = new TableRoot
    {
        new TableSection("Ring")
        {
          // TableSection constructor takes title as an optional parameter
          new SwitchCell { Text = "New Voice Mail" },
          new SwitchCell { Text = "New Mail", On = true }
        }
    },
    Intent = TableIntent.Settings
};
```

<a name="TableView_Appearance" />

## <a name="appearance"></a>外觀

[`TableView`](xref:Xamarin.Forms.TableView)會公開[`Intent`](xref:Xamarin.Forms.TableView.Intent)屬性，它可以設定為任何[`TableIntent`](xref:Xamarin.Forms.TableIntent)列舉成員：

- `Data` –在顯示資料項目時使用。 請注意， [ListView](~/xamarin-forms/user-interface/listview/index.md)可能是滾動資料清單的較佳選項。
- `Form` –在 TableView 作為表單時使用。
- `Menu` –在呈現選取專案的功能表時使用。
- `Settings` –在顯示設定清單時使用。

您選擇的[`TableIntent`](xref:Xamarin.Forms.TableIntent)值可能會影響[`TableView`](xref:Xamarin.Forms.TableView)在每個平臺上的顯示方式。 即使沒有清楚的差異，最佳的作法是選取最符合您打算使用資料表的 `TableIntent`。

此外，針對每個[`TableSection`](xref:Xamarin.Forms.TableSection)顯示的文字色彩，可以藉由將 `TextColor` 屬性設定為[`Color`](xref:Xamarin.Forms.Color)來加以變更。

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>內建資料格

[Xamarin] 隨附內建的儲存格，用於收集和顯示資訊。 雖然[`ListView`](xref:Xamarin.Forms.ListView)和[`TableView`](xref:Xamarin.Forms.TableView)可以使用所有相同的儲存格， [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)和[`EntryCell`](xref:Xamarin.Forms.EntryCell)與 `TableView` 案例最為相關。

如需[TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell)和[ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell)的詳細說明，請參閱[ListView 儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)是用來呈現和捕捉開啟/關閉或 `true` / `false` 狀態的控制項。 它會定義下列屬性：

- `Text` –要顯示在參數旁邊的文字。
- `On` –參數是否顯示為開啟或關閉。
- `OnColor` –參數在 on 位置時的[`Color`](xref:Xamarin.Forms.Color) 。

所有這些屬性都是可系結的。

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)也會公開 `OnChanged` 事件，讓您能夠回應儲存格狀態的變更。

![SwitchCell 範例](tableview-images/switch-cell.png)

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell

當您需要顯示使用者可編輯的文字資料時， [`EntryCell`](xref:Xamarin.Forms.EntryCell)會很有用。 它會定義下列屬性：

- `Keyboard` –編輯時顯示的鍵盤。 如需數值、電子郵件、電話號碼等專案的選項，[請參閱 API](xref:Xamarin.Forms.Keyboard)檔。
- `Label` –要顯示在文字輸入欄位左側的標籤文字。
- `LabelColor` –標籤文字的色彩。
- `Placeholder` –當專案為 null 或空白時，要顯示在 [輸入] 欄位中的文字。 當文字輸入開始時，此文字就會消失。
- `Text` – [輸入] 欄位中的文字。
- `HorizontalTextAlignment` –文字的水準對齊方式。 值為置中、靠左或靠右對齊。 [請參閱 API](xref:Xamarin.Forms.TextAlignment)檔。
- `VerticalTextAlignment` –文字的垂直對齊方式。 值為 `Start`、`Center` 或 `End`。

[`EntryCell`](xref:Xamarin.Forms.EntryCell)也會公開 `Completed` 事件，當使用者在編輯文字時，在鍵盤上叫用 [完成] 按鈕時，就會引發此事件。

![EntryCell 範例](tableview-images/entry-cell.png)

<a name="Custom_Cells" />

## <a name="custom-cells"></a>自訂資料格

當內建的儲存格不夠時，自訂資料格可用來以對您應用程式有意義的方式來呈現和捕捉資料。 例如，您可能會想要呈現滑杆，讓使用者選擇影像的不透明度。

所有自訂資料格都必須衍生自[`ViewCell`](xref:Xamarin.Forms.ViewCell)，這是所有內建資料格類型所使用的相同基類。

這是自訂資料格的範例：

![自訂資料格範例](tableview-images/custom-cell.png)

下列範例會顯示用來在上述螢幕擷取畫面中建立[`TableView`](xref:Xamarin.Forms.TableView)的 XAML：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoTableView.TablePage"
             Title="TableView">
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
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() { Source = "bulb.png"});
layout.Children.Add (new Label()
{
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label ()
{
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot ()
{
    new TableSection("Getting Started")
    {
        new ViewCell() {View = layout}
    }
};
Content = table;
```

[@No__t_1](xref:Xamarin.Forms.TableView)下的根項目是[`TableRoot`](xref:Xamarin.Forms.TableRoot)，而且 `TableRoot` 正下方有[`TableSection`](xref:Xamarin.Forms.TableSection) 。 [@No__t_1](xref:Xamarin.Forms.ViewCell)是直接在 `TableSection` 之下定義，而[`StackLayout`](xref:Xamarin.Forms.StackLayout)則是用來管理自訂資料格的配置，不過這裡可以使用任何版面配置。

> [!NOTE]
> 不同于[`ListView`](xref:Xamarin.Forms.ListView)， [`TableView`](xref:Xamarin.Forms.TableView)不需要在 `ItemTemplate` 中定義自訂（或任何）資料格。

## <a name="row-height"></a>資料列高度

[@No__t_1](xref:Xamarin.Forms.TableView)類別有兩個屬性可以用來變更資料格的資料列高度：

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) –將每個資料列的高度設為 `int`。
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) –如果設定為 `true`，資料列會有不同的高度。 請注意，將此屬性設定為 `true` 時，資料列高度會自動由 Xamarin 來計算和套用。

當[`TableView`](xref:Xamarin.Forms.TableView)中資料格中的內容高度變更時，資料列高度會在 Android 和通用 WINDOWS 平臺（UWP）上隱含地更新。 不過，在 iOS 上，必須藉由將[`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)屬性設定為 `true`，並藉由呼叫[`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法來強制更新。

下列 XAML 範例顯示包含[`ViewCell`](xref:Xamarin.Forms.ViewCell)的[`TableView`](xref:Xamarin.Forms.TableView) ：

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

當您按下[`ViewCell`](xref:Xamarin.Forms.ViewCell)時，就會執行 `OnViewCellTapped` 事件處理常式：

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

@No__t_0 事件處理常式會顯示或隱藏[`ViewCell`](xref:Xamarin.Forms.ViewCell)中的第二個[`Label`](xref:Xamarin.Forms.Label) ，並藉由呼叫[`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法來明確更新資料格的大小。

下列螢幕擷取畫面顯示在進行點擊之前的儲存格：

![調整大小之前的 ViewCell](tableview-images/cell-beforeresize.png)

下列螢幕擷取畫面顯示在按下之後的資料格：

![調整大小之後的 ViewCell](tableview-images/cell-afterresize.png)

> [!IMPORTANT]
> 如果這項功能過度使用，則可能會降低效能。

## <a name="related-links"></a>相關連結

- [TableView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

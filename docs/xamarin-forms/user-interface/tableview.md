---
title: Xamarin.Forms TableView
description: 這篇文章說明如何使用 Xamarin.Forms TableView 類別來呈現應用程式中的捲動功能表、 設定和輸入的表單。
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: c18eba873dc1a1dae36c401507d55652ed233b00
ms.sourcegitcommit: 236a346838c421c7d8951f50abbf4f5365559372
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2019
ms.locfileid: "58641435"
---
# <a name="xamarinforms-tableview"></a>Xamarin.Forms TableView

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)

[`TableView`](xref:Xamarin.Forms.TableView) 是一種檢視來顯示可捲動清單的資料或選擇在不會共用相同的範本的資料列。 不同於[ListView](~/xamarin-forms/user-interface/listview/index.md)，`TableView`沒有別的`ItemsSource`，因此項目必須以手動方式加入做為子系。

![](tableview-images/tableview-all-sml.png "TableView 範例")

<a name="Use_Cases" />

## <a name="use-cases"></a>使用案例

[`TableView`](xref:Xamarin.Forms.TableView) 將很有用：

- 顯示設定清單
- 在表單中收集資料或
- 顯示資料列 （例如數字、 百分比和映像） 從資料列以不同的方式呈現的資料。

[`TableView`](xref:Xamarin.Forms.TableView) 處理捲動和吸引人的各節，一般都需要針對上述案例中的資料列的版面配置。 `TableView`控制項可讓您使用每個平台的基礎時可供使用，建立每個平台的原生外觀的對等檢視。

<a name="TableView_Structure" />

## <a name="structure"></a>結構

中的項目[ `TableView` ](xref:Xamarin.Forms.TableView)區分成區段。 根目錄`TableView`已[ `TableRoot` ](xref:Xamarin.Forms.TableRoot)，這是一或多個父[ `TableSection` ](xref:Xamarin.Forms.TableSection)執行個體。 每個[ `TableSection` ](xref:Xamarin.Forms.TableSection)包含標題和一或多個[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)執行個體：

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

對等的 C# 程式碼是：

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

[`TableView`](xref:Xamarin.Forms.TableView) 會公開[ `Intent` ](xref:Xamarin.Forms.TableView.Intent)屬性，可以設定的任何[ `TableIntent` ](xref:Xamarin.Forms.TableIntent)列舉成員：

- `Data` – 用於顯示資料的項目時。 請注意， [ListView](~/xamarin-forms/user-interface/listview/index.md)可能是更好的選項，可捲動的資料清單。
- `Form` – 用於 TableView 做為表單時。
- `Menu` – 用於呈現一個功能表的 選取項目時。
- `Settings` – 用於時顯示的組態設定清單。

[ `TableIntent` ](xref:Xamarin.Forms.TableIntent)值，您可以選擇可能會影響如何[ `TableView` ](xref:Xamarin.Forms.TableView)會出現在每個平台上。 即使有未清除的差異，最好在其中選取`TableIntent`最符合您想要使用資料表的方式。

此外，文字的色彩顯示每個[ `TableSection` ](xref:Xamarin.Forms.TableSection)可以藉由設定變更`TextColor`屬性設[ `Color` ](xref:Xamarin.Forms.Color)。

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>內建的儲存格

Xamarin.Forms 會隨附內建的儲存格，來收集和顯示資訊。 雖然[ `ListView` ](xref:Xamarin.Forms.ListView)並[ `TableView` ](xref:Xamarin.Forms.TableView)可以使用所有相同的儲存格[ `SwitchCell` ](xref:Xamarin.Forms.SwitchCell)並[ `EntryCell` ](xref:Xamarin.Forms.EntryCell)最相關的`TableView`案例。

請參閱[ListView 儲存格的外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)如需詳細的說明[TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell)並[ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell)。

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 是用來呈現和擷取控制項開啟/關閉或`true` / `false`狀態。 它會定義下列屬性：

- `Text` – 要顯示參數旁邊的文字。
- `On` – 切換是否顯示為 開啟或關閉。
- `OnColor` – [ `Color` ](xref:Xamarin.Forms.Color)處於 on 的位置時的交換器。

所有這些屬性都是可繫結。

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 也會公開`OnChanged`事件，讓您回應中的儲存格狀態變更。

![](tableview-images/switch-cell.png "SwitchCell 範例")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell) 您需要顯示使用者可以編輯的文字資料時很有用。 它會定義下列屬性：

- `Keyboard` – 若要在編輯時顯示鍵盤。 有件事，例如數值、 電子郵件、 電話號碼等選項。[請參閱 API 文件](xref:Xamarin.Forms.Keyboard)。
- `Label` – 要顯示的文字輸入欄位左邊的標籤文字。
- `LabelColor` – 在標籤文字的色彩。
- `Placeholder` – 它是 null 或空白時顯示的項目欄位中的文字。 文字項目開始時，這段文字就會消失。
- `Text` – 文字輸入欄位中。
- `HorizontalTextAlignment` – 水平對齊的文字。 可以 center，左邊或右邊對齊。 [請參閱 API 文件](xref:Xamarin.Forms.TextAlignment)。

[`EntryCell`](xref:Xamarin.Forms.EntryCell) 也會公開`Completed`時使用者按下 'done' 的按鈕，在鍵盤上編輯文字時引發的事件。

![](tableview-images/entry-cell.png "EntryCell 範例")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>自訂資料格

當內建的儲存格不足夠時，自訂資料格可用來呈現，並擷取資料對您的應用程式有意義的方式。 例如，您可能要呈現的滑桿，以允許使用者選擇的映像不透明度。

所有自訂儲存格必須衍生自[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，所有的內建的儲存格類型使用的相同基底類別。

這是自訂的資料格的範例：

![](tableview-images/custom-cell.png "自訂儲存格範例")

下列範例示範用來建立 XAML [ `TableView` ](xref:Xamarin.Forms.TableView)在上述螢幕擷取畫面：

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

對等的 C# 程式碼是：

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

根項目底下[ `TableView` ](xref:Xamarin.Forms.TableView)是[ `TableRoot` ](xref:Xamarin.Forms.TableRoot)，而且沒有[ `TableSection` ](xref:Xamarin.Forms.TableSection)立即下方`TableRoot`。 [ `ViewCell` ](xref:Xamarin.Forms.ViewCell)定義正下方`TableSection`，和[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)用以管理配置的自訂儲存格，不過這裡可以使用任何版面配置。

> [!NOTE]
> 不同於[ `ListView` ](xref:Xamarin.Forms.ListView)， [ `TableView` ](xref:Xamarin.Forms.TableView)不需要該自訂 （或任何） 中所定義的資料格`ItemTemplate`。

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

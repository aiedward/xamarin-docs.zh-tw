---
title: Xamarin.Forms TableView
description: 這篇文章說明如何使用 Xamarin.Forms TableView 類別來呈現應用程式中的捲動功能表、 設定和輸入的表單。
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: 195ed82325ed681f31f9963d5ff0ca0a7fef48ab
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228147"
---
# <a name="xamarinforms-tableview"></a>Xamarin.Forms TableView

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView)這是一個視圖, 用於顯示不共用相同範本之資料列的可滾動清單。 `TableView`不同[于 ListView](~/xamarin-forms/user-interface/listview/index.md), 沒有的概念,因此必須以手動方式將專案新增為子系。`ItemsSource`

![TableView 範例](tableview-images/tableview-all-sml.png)

<a name="Use_Cases" />

## <a name="use-cases"></a>使用案例

[`TableView`](xref:Xamarin.Forms.TableView)適用于:

- 顯示設定清單
- 在表單中收集資料或
- 顯示資料列 （例如數字、 百分比和映像） 從資料列以不同的方式呈現的資料。

[`TableView`](xref:Xamarin.Forms.TableView)在吸引人的區段中處理資料列的滾動和版面配置, 這是上述案例的常見需求。 `TableView`控制項可讓您使用每個平台的基礎時可供使用，建立每個平台的原生外觀的對等檢視。

<a name="TableView_Structure" />

## <a name="structure"></a>結構

中的專案[`TableView`](xref:Xamarin.Forms.TableView)會組織成幾個區段。 在的根目錄`TableView`是, 它是[`TableRoot`](xref:Xamarin.Forms.TableRoot)一個或多個[`TableSection`](xref:Xamarin.Forms.TableSection)實例的父系。 每[`TableSection`](xref:Xamarin.Forms.TableSection)個都包含一個標題和一個或[`ViewCell`](xref:Xamarin.Forms.ViewCell)多個實例:

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

[`TableView`](xref:Xamarin.Forms.TableView)公開屬性, 可設定為任何[`TableIntent`](xref:Xamarin.Forms.TableIntent)列舉成員: [`Intent`](xref:Xamarin.Forms.TableView.Intent)

- `Data`–在顯示資料項目時使用。 請注意， [ListView](~/xamarin-forms/user-interface/listview/index.md)可能是更好的選項，可捲動的資料清單。
- `Form`–在 TableView 做為表單時使用。
- `Menu`–在呈現選取專案的功能表時使用。
- `Settings`–在顯示設定的清單時使用。

您[`TableIntent`](xref:Xamarin.Forms.TableIntent)選擇的值可能會影響每[`TableView`](xref:Xamarin.Forms.TableView)個平臺上顯示的方式。 即使有未清除的差異，最好在其中選取`TableIntent`最符合您想要使用資料表的方式。

此外, 您可以藉由[`TableSection`](xref:Xamarin.Forms.TableSection) `TextColor`將屬性設定為[`Color`](xref:Xamarin.Forms.Color), 來變更每個顯示的文字色彩。

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>內建資料格

Xamarin.Forms 會隨附內建的儲存格，來收集和顯示資訊。 雖然[`ListView`](xref:Xamarin.Forms.ListView) [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)和[`TableView`](xref:Xamarin.Forms.TableView)可以使用所有相同的儲存格, [`EntryCell`](xref:Xamarin.Forms.EntryCell)但與`TableView`案例最為相關。

請參閱[ListView 儲存格的外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)如需詳細的說明[TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell)並[ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell)。

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 是用來呈現和擷取控制項開啟/關閉或`true` / `false`狀態。 它會定義下列屬性:

- `Text`–要顯示在參數旁邊的文字。
- `On`–參數是否顯示為開啟或關閉。
- `OnColor`[`Color`](xref:Xamarin.Forms.Color) –參數在 on 位置時的。

所有這些屬性都是可系結的。

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)也會公開`OnChanged`事件, 讓您能夠回應儲存格狀態的變更。

![SwitchCell 範例](tableview-images/switch-cell.png)

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell) 您需要顯示使用者可以編輯的文字資料時很有用。 它會定義下列屬性:

- `Keyboard`–編輯時要顯示的鍵盤。 有件事，例如數值、 電子郵件、 電話號碼等選項。[請參閱 API 文件](xref:Xamarin.Forms.Keyboard)。
- `Label`–要顯示在文字輸入欄位左側的標籤文字。
- `LabelColor`–標籤文字的色彩。
- `Placeholder`–當專案為 null 或空白時, 要顯示在輸入欄位中的文字。 文字項目開始時，這段文字就會消失。
- `Text`–輸入欄位中的文字。
- `HorizontalTextAlignment`–文字的水準對齊方式。 可以 center，左邊或右邊對齊。 [請參閱 API 文件](xref:Xamarin.Forms.TextAlignment)。

[`EntryCell`](xref:Xamarin.Forms.EntryCell)也會公開`Completed`事件, 當使用者在編輯文字時按下鍵盤上的 [完成] 按鈕時, 就會引發此事件。

![EntryCell 範例](tableview-images/entry-cell.png)

<a name="Custom_Cells" />

## <a name="custom-cells"></a>自訂資料格

當內建的儲存格不足夠時，自訂資料格可用來呈現，並擷取資料對您的應用程式有意義的方式。 例如，您可能要呈現的滑桿，以允許使用者選擇的映像不透明度。

所有自訂儲存格必須衍生自[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，所有的內建的儲存格類型使用的相同基底類別。

這是自訂的資料格的範例：

![自訂資料格範例](tableview-images/custom-cell.png)

下列範例顯示用來建立上述螢幕擷取畫面中[`TableView`](xref:Xamarin.Forms.TableView)的 XAML:

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

下[`TableView`](xref:Xamarin.Forms.TableView)的根項目[`TableRoot`](xref:Xamarin.Forms.TableRoot)是[`TableSection`](xref:Xamarin.Forms.TableSection) , 而且緊接在`TableRoot`正下方的。 會直接定義`TableSection`在底下, 而且[`StackLayout`](xref:Xamarin.Forms.StackLayout)會用來管理自訂資料格的配置, 不過這裡可以使用任何版面配置。 [`ViewCell`](xref:Xamarin.Forms.ViewCell)

> [!NOTE]
> 不同[`ListView`](xref:Xamarin.Forms.ListView)于[`TableView`](xref:Xamarin.Forms.TableView) , 不需要在中定義自訂 (或任何) 資料`ItemTemplate`格。

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

![調整大小之前的 ViewCell](tableview-images/cell-beforeresize.png)

下列螢幕擷取畫面會顯示儲存格後被點選時：

![調整大小之後的 ViewCell](tableview-images/cell-afterresize.png)

> [!IMPORTANT]
> 如果這項功能過度使用而使，沒有強式可能的效能降低。

## <a name="related-links"></a>相關連結

- [TableView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

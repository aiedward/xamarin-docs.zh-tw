---
title: Xamarin.Forms TableView
description: 本文說明如何使用 Xamarin.Forms TableView 類別，在應用程式中呈現捲動功能表、設定和輸入表單。
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: afb2c7c5c82a7ce530846c266d231b893bbbf39d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371219"
---
# <a name="no-locxamarinforms-tableview"></a>Xamarin.Forms TableView

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView) 是一種視圖，可顯示資料列的可滾動清單，或有不共用相同範本的選項。 與 [ListView](~/xamarin-forms/user-interface/listview/index.md)不同的是，沒有的 `TableView` 概念 `ItemsSource` ，因此必須以手動方式將專案新增為子系。

![TableView 範例](tableview-images/tableview-all-sml.png)

## <a name="use-cases"></a>使用案例

[`TableView`](xref:Xamarin.Forms.TableView) 在下列情況中非常有用：

- 呈現設定清單
- 以表單收集資料，或
- 顯示不同于資料列與資料列的資料 (例如，數位、百分比和影像) 。

[`TableView`](xref:Xamarin.Forms.TableView) 在有吸引力的區段中處理資料列的滾動和配置，這是上述案例的常見需求。 `TableView`控制項會使用每個平臺的基礎對等視圖（如果有的話），以建立每個平臺的原生外觀。

## <a name="structure"></a>結構

中的專案 [`TableView`](xref:Xamarin.Forms.TableView) 會組織成各區段。 的根目錄 `TableView` 是 [`TableRoot`](xref:Xamarin.Forms.TableRoot) ，它是一或多個實例的父系 [`TableSection`](xref:Xamarin.Forms.TableSection) 。 每個都 [`TableSection`](xref:Xamarin.Forms.TableSection) 包含一個標題和一個或多個 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 實例：

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

## <a name="appearance"></a>外觀

[`TableView`](xref:Xamarin.Forms.TableView) 公開 [`Intent`](xref:Xamarin.Forms.TableView.Intent) 屬性，此屬性可以設定為任何 [`TableIntent`](xref:Xamarin.Forms.TableIntent) 列舉成員：

- `Data` –用來顯示資料項目目。 請注意， [ListView](~/xamarin-forms/user-interface/listview/index.md) 可能是滾動資料清單的較佳選項。
- `Form` –可在 TableView 作為表單時使用。
- `Menu` –可在呈現選取專案的功能表時使用。
- `Settings` –在顯示設定的清單時使用。

[`TableIntent`](xref:Xamarin.Forms.TableIntent)您選擇的值可能會影響在 [`TableView`](xref:Xamarin.Forms.TableView) 每個平臺上顯示的方式。 即使沒有明顯的差異，最佳的作法是選取 `TableIntent` 最符合您想要使用資料表的方式。

此外，您 [`TableSection`](xref:Xamarin.Forms.TableSection) 可以藉由將屬性設定為，來變更每個顯示的文字色彩 `TextColor` [`Color`](xref:Xamarin.Forms.Color) 。

## <a name="built-in-cells"></a>內建資料格

Xamarin.Forms 隨附內建資料格，用於收集和顯示資訊。 雖然 [`ListView`](xref:Xamarin.Forms.ListView) 和 [`TableView`](xref:Xamarin.Forms.TableView) 可以使用所有相同的資料格，但對 [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) [`EntryCell`](xref:Xamarin.Forms.EntryCell) 案例而言是最相關的 `TableView` 。

如需[TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell)和[ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell)的詳細說明，請參閱[ListView 資料格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)是用於呈現和捕捉開啟/關閉或狀態的控制項 `true` / `false` 。 它會定義下列屬性：

- `Text` –要在參數旁邊顯示的文字。
- `On` –參數是否顯示為開啟或關閉。
- `OnColor` – [`Color`](xref:Xamarin.Forms.Color) 位於開啟位置時之參數的。

所有這些屬性都是可系結的。

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 也會公開 `OnChanged` 事件，讓您能夠回應儲存格狀態的變更。

![SwitchCell 範例](tableview-images/switch-cell.png)

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell) 當您需要顯示使用者可以編輯的文字資料時，這會很有用。 它會定義下列屬性：

- `Keyboard` –編輯時要顯示的鍵盤。 有一些選項可供您選擇，例如數值、電子郵件、電話號碼等， [請參閱 API](xref:Xamarin.Forms.Keyboard)檔。
- `Label` –要顯示在文字輸入欄位左邊的標籤文字。
- `LabelColor` –標籤文字的色彩。
- `Placeholder` –當輸入欄位為 null 或空白時，要顯示在輸入欄位中的文字。 文字輸入開始時，此文字會消失。
- `Text` –輸入欄位中的文字。
- `HorizontalTextAlignment` –文字的水準對齊方式。 值為置中、靠左或靠右對齊。 [請參閱 API](xref:Xamarin.Forms.TextAlignment)檔。
- `VerticalTextAlignment` –文字的垂直對齊方式。 值為 `Start` 、 `Center` 或 `End` 。

[`EntryCell`](xref:Xamarin.Forms.EntryCell) 也會公開 `Completed` 事件，此事件會在使用者按下鍵盤上的 [完成] 按鈕時引發，並在編輯文字時引發。

![EntryCell 範例](tableview-images/entry-cell.png)

## <a name="custom-cells"></a>自訂資料格

當內建資料格不足時，您可以使用自訂資料格，以對您的應用程式有意義的方式來呈現和捕捉資料。 例如，您可能會想要顯示一個滑杆，讓使用者選擇影像的不透明度。

所有的自訂資料格都必須衍生自 [`ViewCell`](xref:Xamarin.Forms.ViewCell) ，這是所有內建資料格類型所使用的相同基類。

以下是自訂資料格的範例：

![自訂資料格範例](tableview-images/custom-cell.png)

下列範例顯示在 [`TableView`](xref:Xamarin.Forms.TableView) 上述螢幕擷取畫面中用來建立的 XAML：

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

下的根項目 [`TableView`](xref:Xamarin.Forms.TableView) 是 [`TableRoot`](xref:Xamarin.Forms.TableRoot) ，而且正 [`TableSection`](xref:Xamarin.Forms.TableSection) 下方的是 `TableRoot` 。 [`ViewCell`](xref:Xamarin.Forms.ViewCell)會直接定義在下 `TableSection` ，而 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 會使用來管理自訂資料格的配置，雖然可以在這裡使用任何版面配置。

> [!NOTE]
> 與不同的 [`ListView`](xref:Xamarin.Forms.ListView) [`TableView`](xref:Xamarin.Forms.TableView) 是，不需要在中定義自訂 (或任何) 的資料格 `ItemTemplate` 。

## <a name="row-height"></a>資料列高度

[`TableView`](xref:Xamarin.Forms.TableView)類別有兩個屬性，可用來變更資料格的資料列高度：

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) –將每一個資料列的高度設定為 `int` 。
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) –如果設定為，資料列會有不同的高度 `true` 。 請注意，將這個屬性設定為時 `true` ，將會自動計算和套用資料列高度 Xamarin.Forms 。

當中資料格中的內容高度 [`TableView`](xref:Xamarin.Forms.TableView) 變更時，資料列高度會在 Android 上隱含地更新，而通用 Windows 平臺 (UWP) 。 不過，在 iOS 上，必須透過 [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) 呼叫方法，將屬性設定為 `true` 和來強制更新 [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) 。

下列 XAML 範例顯示包含的 [`TableView`](xref:Xamarin.Forms.TableView) [`ViewCell`](xref:Xamarin.Forms.ViewCell) ：

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

當按 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 下時， `OnViewCellTapped` 就會執行事件處理常式：

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

`OnViewCellTapped`事件處理常式會顯示或隱藏中的第二個 [`Label`](xref:Xamarin.Forms.Label) [`ViewCell`](xref:Xamarin.Forms.ViewCell) ，並藉由呼叫方法來明確更新資料格的大小 [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) 。

下列螢幕擷取畫面顯示在進行點擊之前的儲存格：

![調整大小之前的 ViewCell](tableview-images/cell-beforeresize.png)

下列螢幕擷取畫面顯示在進行點擊之後的儲存格：

![調整大小之後的 ViewCell](tableview-images/cell-afterresize.png)

> [!IMPORTANT]
> 如果這項功能過度使用，則效能可能會降低。

## <a name="related-links"></a>相關連結

- [TableView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-tableview)
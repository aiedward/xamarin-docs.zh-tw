---
title: TableView
description: 在捲動功能表、 設定與輸入的表單。
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: dc55f3fe70450c71b639cf33166720fc27d45a10
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="tableview"></a>TableView

[TableView](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/)是用於顯示可捲動的資料或選擇清單檢視中不會共用相同的範本的資料列。 不同於[ListView](~/xamarin-forms/user-interface/listview/index.md)，TableView 沒有的概念`ItemsSource`，因此，項目必須做為子系手動加入。

本指南是由下列各節：

- **[使用案例](#Use_Cases)** &ndash;何時使用 TableView 而不是清單檢視或自訂的檢視。
- **[TableView 結構](#TableView_Structure)** &ndash;需要 TableView 內檢視的階層。
- **[TableView 外觀](#TableView_Appearance)** &ndash; TableView 的自訂選項。
- **[內建的儲存格](#Built-In_Cells)** &ndash;內建的儲存格選項，包括[EntryCell](#EntryCell)和[SwitchCell](#SwitchCell)。
- **[自訂儲存格](#Custom_Cells)** &ndash;如何讓您自己自訂的資料格。

![](tableview-images/tableview-all-sml.png "TableView 範例")

<a name="Use_Cases" />

## <a name="use-cases"></a>使用案例

TableView 很有用：

- 呈現設定的清單
- 收集資料，在表單中，或
- 顯示資料列 （例如數字、 百分比及映像） 從資料列以不同的方式呈現資料。

TableView 處理捲動和吸引人的區段中，一般需要前述案例中的資料列的版面配置。 `TableView`控制項使用的每個平台基礎的對等的檢視表可用時，建立每個平台的原生外觀。

<a name="TableView_Structure" />

## <a name="tableview-structure"></a>TableView 結構

中的項目`TableView`組織成數個區段。 根目錄的`TableView`是`TableRoot`，這是一或多個父`TableSections`:

```csharp
Content = new TableView {
    Root = new TableRoot {
        new TableSection...
    },
    Intent = TableIntent.Settings
};
```

每個`TableSection`的標題和一或多個 ViewCells 所組成。 我們在這裡看到`TableSection`的`Title`屬性設定為*「 環 」*建構函式：

```csharp
var section = new TableSection ("Ring") { //TableSection constructor takes title as an optional parameter
    new SwitchCell {Text = "New Voice Mail"},
    new SwitchCell {Text = "New Mail", On = true}
};
```

若要完成相同的配置，在 XAML 中與以上所述：

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

TableView 公開`Intent`屬性，這是列舉下列選項：

- **資料**&ndash;時顯示資料的項目。 請注意， [ListView](~/xamarin-forms/user-interface/listview/index.md)可能是更好的選項，如捲動資料的清單。
- **表單** &ndash; TableView 做為表單時使用。
- **功能表**&ndash;呈現的功能表選取項目時使用。
- **設定**&ndash;時顯示的組態設定的清單。

`TableIntent`您選擇，可能會影響如何`TableView`會出現在每個平台上。 即使有未清除的差異，最好在其中選取`TableIntent`最符合您想要使用資料表的方式。

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>內建資料格

Xamarin.Forms 隨附內建的儲存格，收集和顯示資訊。 雖然 ListView 和 TableView 可以使用所有相同的資料格，以下是最相關 TableView 案例：

- **SwitchCell** &ndash;呈現和 true/false 狀態，以及文字標籤。
- **EntryCell** &ndash;呈現，和擷取文字。

請參閱[ListView 儲存格外觀](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)如需詳細的說明[TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell)和[ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell)。

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell
[`SwitchCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/) 是要用於呈現，並擷取控制項開啟/關閉或`true` / `false`狀態。

SwitchCells 有一行文字，以編輯和執行刳弮屬性。 這兩個屬性都可繫結。

- `Text` &ndash; 要顯示參數旁邊的文字。
- `On` &ndash; 是否為 on 或 off，會顯示參數。

請注意，`SwitchCell`公開`OnChanged`事件，可讓您在回應中的儲存格狀態變更。

![](tableview-images/switch-cell.png "SwitchCell 範例")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell
[`EntryCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/) 您需要顯示使用者可以編輯的文字資料時很有用。 `EntryCell`s 提供可自訂的下列屬性：

- `Keyboard` &ndash; 若要在編輯時顯示鍵盤。 有之類的數值、 電子郵件、 電話號碼等的選項。[請參閱 API 文件](http://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/)。
- `Label` &ndash; 要顯示的文字輸入欄位右邊的標籤文字。
- `LabelColor` &ndash; 標籤文字的色彩。
- `Placeholder` &ndash; 當它是 null 或空白時，項目欄位中顯示的文字。 文字項目開始時，此文字就會消失。
- `Text` &ndash; 項目欄位中的文字。
- `HorizontalTextAlignment` &ndash; 文字的水平對齊方式。 可以在中心，左邊或右邊對齊。 [請參閱 API 文件](http://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/)。

請注意，`EntryCell`公開`Completed`事件，當使用者叫用 'done' 鍵盤上的編輯文字時引發。

![](tableview-images/entry-cell.png "EntryCell 範例")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>自訂儲存格
當內建資料格不足夠時，自訂資料格可用來呈現，和擷取資料，對您的應用程式有意義的方式。 例如，您可能想要呈現滑桿來允許使用者選擇的映像不透明度。

所有自訂儲存格必須衍生自[ `ViewCell` ](http://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)，類型之所有內建的儲存格使用相同的基底類別。

這是自訂的儲存格的範例：

![](tableview-images/custom-cell.png "自訂儲存格範例")

### <a name="xaml"></a>XAML
若要建立上述配置 XAML 如下：

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

正在經常進行上面的 XAML。 讓我們將它分解：

- 根項目底下`TableView`是`TableRoot`。
- 沒有`TableSection`立即下方`TableRoot`。
- `ViewCell`正下方區段定義。 不同於`ListView`，`TableView`不需要該自訂 （或任何） 中所定義的資料格`ItemTemplate`。
- StackLayout 用來管理自訂的儲存格的版面配置。 可以使用任何配置。

### <a name="cnum"></a>C&num;

因為`TableView`適用於靜態資料，或以手動方式變更的資料，它並沒有項目範本的概念。 相反地，自訂資料格可以手動建立，並放入資料表。 技術建立自訂儲存格，請注意繼承自`ViewCell`，然後將它加入至`TableView`也會內建資料格，則也支援。
以下是 c# 程式碼以完成上述版面配置：

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

C# 上方正在進行許多。 讓我們將它分解：

- 根項目底下`TableView`是`TableRoot`。
- 沒有`TableSection`立即下方`TableRoot`。
- `ViewCell`正下方區段定義。 不同於`ListView`，`TableView`不需要該自訂 （或任何） 中所定義的資料格`ItemTemplate`。
- StackLayout 用來管理自訂的儲存格的版面配置。 可以使用任何配置。

請注意，永遠不會定義自訂的儲存格的類別。 相反地，`ViewCell`的檢視屬性設為特定執行個體`ViewCell`。



## <a name="related-links"></a>相關連結

- [TableView （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TableView)

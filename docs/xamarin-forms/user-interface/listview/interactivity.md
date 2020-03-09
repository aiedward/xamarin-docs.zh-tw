---
title: ListView 互動性
description: 本文說明如何藉由執行選取專案、內容動作和提取重新整理，將互動功能新增至 Xamarin. 表單 ListView。
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: aa717792bdaefe24d957c9781934933b67aaf92b
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78914526"
---
# <a name="listview-interactivity"></a>ListView 互動性

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

[`ListView`](xref:Xamarin.Forms.ListView)類別的 Xamarin 可支援使用者與它所呈現的資料互動。

## <a name="selection-and-taps"></a>選取和點擊

[`ListView`](xref:Xamarin.Forms.ListView)選取模式的控制方式，是將[`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode)屬性設定為[`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode)列舉的值：

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)表示可以選取單一專案，並反白顯示選取的專案。 這是預設值。
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)指出無法選取專案。

當使用者按下專案時，會引發兩個事件：

- 選取新的專案時， [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)引發。
- 點擊專案時， [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)引發。

點擊兩次相同的專案會引發兩個[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件，但只會引發單一[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件。

> [!NOTE]
> [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs)類別，其中包含[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件的事件引數、具有[`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group)和[`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item)屬性，以及一個 `ItemIndex` 屬性，其值代表分專案[`ListView`](xref:Xamarin.Forms.ListView)中的索引。 同樣地， [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs)類別（其中包含[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件的事件引數）具有[`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem)屬性，而 `SelectedItemIndex` 屬性的值代表所選項目 `ListView` 中的索引。

當  [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 屬性設定為 [ [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)] 時，可以選取 [ [`ListView`](xref:Xamarin.Forms.ListView)中的專案、將會引發[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)和[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件，而  [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) ] 屬性會設定為所選取專案的值。

當 [ [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) ] 屬性設定為 [ [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)] 時，無法選取[`ListView`](xref:Xamarin.Forms.ListView)中的專案，將不會引發[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件，而且[`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)屬性仍會 `null`。 不過， [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件仍會引發，而按下點的專案會在點按時短暫反白顯示。

當已選取專案，且[`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode)屬性從[`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)變更為[`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)時， [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)屬性會設定為 `null`，而且[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件將會以 `null` 專案引發。

下列螢幕擷取畫面顯示具有預設選取模式的[`ListView`](xref:Xamarin.Forms.ListView) ：

![](interactivity-images/selection-default.png "ListView with Selection Enabled")

### <a name="disable-selection"></a>停用選取範圍

若要停用[`ListView`](xref:Xamarin.Forms.ListView)選取範圍，請將[`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode)屬性設定為[`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)：

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>內容動作

使用者通常會想要對 `ListView`中的專案採取動作。 例如，請考慮郵件應用程式中的電子郵件清單。 在 iOS 上，您可以滑動以刪除訊息：

![](interactivity-images/context-default.png "ListView with Context Actions")

內容動作可以在和 XAML C#中執行。 您可以在下面找到這兩者的特定指南，但首先讓我們來瞭解這兩者的一些重要的執行詳細資料。

內容動作是使用 `MenuItem` 元素所建立。 `MenuItem` 本身會引發 `MenuItems` 物件的事件，而不是 `ListView`。 這不同于資料格的點按事件處理方式，其中 `ListView` 會引發事件，而不是資料格。 因為 `ListView` 會引發事件，所以會提供其事件處理常式的重要資訊，例如選取或點擊的專案。

根據預設，`MenuItem` 無法得知它所屬的資料格。 `CommandParameter` 屬性可在 `MenuItem` 用來儲存物件，例如 `MenuItem`的 `ViewCell`後方的物件。 `CommandParameter` 屬性可以同時在 XAML 和C#中設定。

### <a name="xaml"></a>XAML

可以在 XAML 集合中建立 `MenuItem` 元素。 下列 XAML 示範已實作為兩個內容動作的自訂資料格：

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore"
                      CommandParameter="{Binding .}"
                      Text="More" />
            <MenuItem Clicked="OnDelete"
                      CommandParameter="{Binding .}"
                      Text="Delete" IsDestructive="True" />
         </ViewCell.ContextActions>
         <StackLayout Padding="15,0">
              <Label Text="{Binding title}" />
         </StackLayout>
      </ViewCell>
    </DataTemplate>
  </ListView.ItemTemplate>
</ListView>
```

在程式碼後置檔案中，確定已執行 `Clicked` 方法：

```csharp
public void OnMore (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> Android 的 `NavigationPageRenderer` 具有可覆寫的 `UpdateMenuItemIcon` 方法，可以用來從自訂 `Drawable`載入圖示。 此覆寫可讓您使用 SVG 影像做為 Android 上 `MenuItem` 實例上的圖示。

### <a name="code"></a>程式碼

您可以藉由建立 `MenuItem` 實例並將其加入至儲存格的 `ContextActions` 集合，在任何 `Cell` 的子類別中執行內容動作（只要它不會當做群組標頭使用）。 您可以針對內容動作設定下列屬性：

- **文字**&ndash; 出現在功能表項目中的字串。
- **按一下即可**在按下專案時 &ndash; 事件。
- **IsDestructive** &ndash; （選擇性）如果為 true，則會在 iOS 上以不同的方式呈現專案。

您可以將多個內容動作加入至資料格，但只有一個會將 `IsDestructive` 設定為 `true`。 下列程式碼示範如何將內容動作新增至 `ViewCell`：

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

## <a name="pull-to-refresh"></a>提取至重新整理

使用者會預期在資料清單中向下提取會重新整理該清單。 [`ListView`](xref:Xamarin.Forms.ListView)控制項支援這個現成的。 若要啟用提取重新整理功能，請將[`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled)設定為 `true`：

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

對等的 C# 程式碼為：

```csharp
listView.IsPullToRefreshEnabled = true;
```

重新整理期間會出現一個微調，預設為黑色。 不過，您可以在 iOS 和 Android 上變更微調色彩，方法是將 [`RefreshControlColor`] 屬性設定為[`Color`](xref:Xamarin.Forms.Color)：

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

對等的 C# 程式碼為：

```csharp
listView.RefreshControlColor = Color.Red;
```

下列螢幕擷取畫面顯示在使用者提取時的提取重新整理：

![](interactivity-images/refresh-start.png "ListView Pull to Refresh In-Progress")

下列螢幕擷取畫面顯示在使用者釋放提取之後的提取重新整理，並在[`ListView`](xref:Xamarin.Forms.ListView)更新時顯示微調按鈕：

![](interactivity-images/refresh-in-progress.png "ListView Pull to Refresh Complete")

[`ListView`](xref:Xamarin.Forms.ListView)會引發[`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing)事件來起始重新整理，而且[`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing)屬性會設定為 `true`。 重新整理 `ListView` 內容所需的任何程式碼，都應該由 `Refreshing` 事件的事件處理常式或[`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand)所執行的方法來執行。 重新整理 `ListView` 之後，`IsRefreshing` 屬性應該設定為 `false`，或應呼叫[`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh)方法，以表示重新整理已完成。

> [!NOTE]
> 定義[`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand)時，可以指定命令的 `CanExecute` 方法來啟用或停用命令。

## <a name="detect-scrolling"></a>偵測滾動

[`ListView`](xref:Xamarin.Forms.ListView)定義引發的 `Scrolled` 事件，以指出發生滾動。 下列 XAML 範例顯示設定 `Scrolled` 事件之事件處理常式的 `ListView`：

```xaml
<ListView Scrolled="OnListViewScrolled">
    ...
</ListView>
```

對等的 C# 程式碼為：

```csharp
ListView listView = new ListView();
listView.Scrolled += OnListViewScrolled;
```

在此程式碼範例中，當 `Scrolled` 事件引發時，會執行 `OnListViewScrolled` 事件處理常式：

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

`OnListViewScrolled` 事件處理常式會輸出事件隨附之 `ScrolledEventArgs` 物件的值。

## <a name="related-links"></a>相關連結

- [ListView 互動性（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

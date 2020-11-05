---
title: ListView 互動性
description: 本文說明如何藉 Xamarin.Forms 由執行選取專案、內容動作和提取至重新整理，來將互動加入至 ListView。
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4a922a841452e5e934cc7dcb88a9f84373ae3ded
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375491"
---
# <a name="listview-interactivity"></a>ListView 互動性

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 類別支援使用者與它所呈現的資料互動。

## <a name="selection-and-taps"></a>選取專案和點擊

[`ListView`](xref:Xamarin.Forms.ListView)選取模式是藉由將屬性設定 [`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 為列舉值來控制 [`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode) ：

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) 表示可以選取單一專案，並反白顯示選取的專案。 這是預設值。
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) 表示無法選取專案。

當使用者按下某個專案時，就會引發兩個事件：

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 在選取新專案時引發。
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 在點擊專案時引發。

兩次使用相同的專案會引發兩個 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件，但只會引發一個 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。

> [!NOTE]
> [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs)類別，其中包含事件的事件引數 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 、具有 [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) 和 [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) 屬性，以及 `ItemIndex` 其值代表點擊專案中之索引的屬性 [`ListView`](xref:Xamarin.Forms.ListView) 。 同樣地， [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) 包含事件之事件引數的類別 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 具有 [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) 屬性，而 `SelectedItemIndex` 屬性的值代表所選項目中的索引 `ListView` 。

當 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 屬性設定為時，中的專案就會被 [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) [`ListView`](xref:Xamarin.Forms.ListView) 選取， [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 並且會 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 引發和事件，而且 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 屬性會設定為選取專案的值。

當 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 屬性設定為時 [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) ，無法選取中的專案 [`ListView`](xref:Xamarin.Forms.ListView) ，將不會 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 引發事件，而且 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 會保留屬性 `null` 。 不過， [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 仍然會引發事件，並在點一下時短暫醒目提示點擊的專案。

當選取專案且 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 屬性從變更 [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) 為時 [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) ， [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 屬性會設定為， `null` 而且會 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 以專案引發事件 `null` 。

下列螢幕擷取畫面顯示 [`ListView`](xref:Xamarin.Forms.ListView) 具有預設選取模式的：

![已啟用選取專案的 ListView](interactivity-images/selection-default.png)

### <a name="disable-selection"></a>停用選取範圍

若要停用 [`ListView`](xref:Xamarin.Forms.ListView) 選取範圍，請將 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 屬性設定為 [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) ：

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>內容動作

使用者通常會想要對中的專案採取動作 `ListView` 。 例如，請考慮郵件應用程式中的電子郵件清單。 在 iOS 上，您可以使用滑動來刪除訊息：

![具有內容動作的 ListView](interactivity-images/context-default.png)

您可以在 c # 和 XAML 中執行內容動作。 您可以在下面找到這兩者的特定指南，但首先讓我們看看一些重要的學習詳細資料。

內容動作是使用 `MenuItem` 元素建立。 點 `MenuItems` 按物件的事件會由 `MenuItem` 本身引發，而不是由所引發 `ListView` 。 這與資料格的資料格處理方式不同，後者會 `ListView` 引發事件而非儲存格。 因為 `ListView` 會引發事件，所以它的事件處理常式會獲得索引鍵資訊，例如選取或點擊的專案。

根據預設，a `MenuItem` 無法得知它所屬的資料格。 `CommandParameter`屬性可用 `MenuItem` 來儲存物件，例如中的物件 `MenuItem` `ViewCell` 。 您 `CommandParameter` 可以在 XAML 和 c # 中設定此屬性。

### <a name="xaml"></a>XAML

`MenuItem` 您可以在 XAML 集合中建立元素。 下列 XAML 示範自訂資料格，其中包含兩個執行的內容動作：

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

在程式碼後端檔案中，確定 `Clicked` 已執行方法：

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
> `NavigationPageRenderer`Android 的可覆寫 `UpdateMenuItemIcon` 方法可以用來從自訂載入圖示 `Drawable` 。 此覆寫可使用 SVG 影像作為 Android 實例上的圖示 `MenuItem` 。

### <a name="code"></a>程式碼

您可以在任何子類別中執行內容動作 `Cell` (只要它不是當做群組標) 頭使用，只要建立 `MenuItem` 實例，並將它們加入至資料 `ContextActions` 格的集合即可。 您可以針對內容動作來設定下列屬性：

- **文字** &ndash; 出現在功能表項目中的字串。
- **按一下** &ndash; 按一下專案時的事件。
- **IsDestructive** &ndash; (選擇性) 若為 true，則會在 iOS 上以不同的方式呈現專案。

您可以將多個內容動作加入至資料格，但只有一個可 `IsDestructive` 設定為 `true` 。 下列程式碼示範如何將內容動作加入至 `ViewCell` ：

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

## <a name="pull-to-refresh"></a>拖動以重新整理

使用者預期會在資料清單上向下切入，以重新整理該清單。 [`ListView`](xref:Xamarin.Forms.ListView)控制項支援這個現成的。 若要啟用提取重新整理功能，請將設定 [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) 為 `true` ：

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

對等的 C# 程式碼為：

```csharp
listView.IsPullToRefreshEnabled = true;
```

重新整理期間會出現一個微調，預設為黑色。 不過，您可以將屬性設定為，以在 iOS 和 Android 上變更微調色彩 `RefreshControlColor` [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

對等的 C# 程式碼為：

```csharp
listView.RefreshControlColor = Color.Red;
```

下列螢幕擷取畫面顯示使用者正在提取時的提取重新整理：

![ListView 提取以重新整理 In-Progress](interactivity-images/refresh-start.png)

下列螢幕擷取畫面顯示在使用者放開提取之後的提取重新整理，並在更新時顯示微調顯示 [`ListView`](xref:Xamarin.Forms.ListView) ：

![ListView 提取以重新整理完成](interactivity-images/refresh-in-progress.png)

[`ListView`](xref:Xamarin.Forms.ListView) 引發 [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) 事件以起始重新整理，並將 [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) 屬性設定為 `true` 。 任何需要重新整理之內容的 `ListView` 程式碼，都應該由事件的事件處理常式或由 `Refreshing` 執行的方法執行 [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) 。 一旦重新整理之後 `ListView` ，就 `IsRefreshing` 應該將屬性設定為 `false` ，或 [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) 應該呼叫方法來表示重新整理已完成。

> [!NOTE]
> 定義時 [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) ， `CanExecute` 可以指定命令的方法來啟用或停用此命令。

## <a name="detect-scrolling"></a>偵測滾動

[`ListView`](xref:Xamarin.Forms.ListView) 定義 `Scrolled` 引發的事件以表示發生滾動。 下列 XAML 範例顯示 `ListView` 設定事件之事件處理常式的 `Scrolled` ：

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

在此程式碼範例中， `OnListViewScrolled` 事件處理常式會在 `Scrolled` 事件引發時執行：

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

`OnListViewScrolled`事件處理常式 `ScrolledEventArgs` 會輸出伴隨事件的物件值。

## <a name="related-links"></a>相關連結

- [ListView 互動 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
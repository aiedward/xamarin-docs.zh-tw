---
title: ListView 互動性
description: 本文說明如何藉 Xamarin.Forms 由執行選取專案、內容動作和提取重新整理，將互動性新增至 ListView。
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 287b116d9ba2cb84e4e196fff080b8212e4eca3b
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938407"
---
# <a name="listview-interactivity"></a>ListView 互動性

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 類別支援使用者與它所呈現的資料互動。

## <a name="selection-and-taps"></a>選取和點擊

[`ListView`](xref:Xamarin.Forms.ListView)選取模式的控制方式是將 [`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 屬性設定為列舉的值 [`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode) ：

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)表示可以選取單一專案，並反白顯示選取的專案。 這是預設值。
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)指出無法選取專案。

當使用者按下專案時，會引發兩個事件：

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)在選取新專案時引發。
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)在按了專案時引發。

點擊兩次相同的專案會引發兩個 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件，但只會引發單一 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件。

> [!NOTE]
> [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs)類別，其中包含事件的事件引數 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 、具有 [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) 和 [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) 屬性，以及 `ItemIndex` 屬性，其值代表分 [`ListView`](xref:Xamarin.Forms.ListView) 項專案之中的索引。 同樣地， [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) 類別（包含事件的事件引數 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) ）具有 [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) 屬性，而屬性（property）的 `SelectedItemIndex` 值代表所 `ListView` 選項目之中的索引。

當 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 屬性設定為時 [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) ，可以選取中的專案 [`ListView`](xref:Xamarin.Forms.ListView) ，將會 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 引發和 [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 事件，而且 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 屬性會設定為所選取專案的值。

當 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 屬性設定為時 [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) ，將無法選取中的專案 [`ListView`](xref:Xamarin.Forms.ListView) ， [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 也不會引發事件，且 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 屬性將會保留 `null` 。 不過， [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 仍然會引發事件，而且在點按時將會簡短醒目提示按下的專案。

當已選取專案，且 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 屬性從變更 [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) 為時 [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) ， [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) 屬性將會設定為， `null` 且 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件將會與專案一起引發 `null` 。

下列螢幕擷取畫面顯示 [`ListView`](xref:Xamarin.Forms.ListView) 具有預設選取模式的：

![已啟用選取範圍的 ListView](interactivity-images/selection-default.png)

### <a name="disable-selection"></a>停用選取範圍

若要停用 [`ListView`](xref:Xamarin.Forms.ListView) 選取範圍，請將 [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) 屬性設為 [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) ：

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>內容動作

使用者通常會想要對中的專案採取動作 `ListView` 。 例如，請考慮郵件應用程式中的電子郵件清單。 在 iOS 上，您可以滑動以刪除訊息：

![含有內容動作的 ListView](interactivity-images/context-default.png)

內容動作可透過 c # 和 XAML 來執行。 您可以在下面找到這兩者的特定指南，但首先讓我們來瞭解這兩者的一些重要的執行詳細資料。

內容動作是使用 `MenuItem` 元素建立。 點一下物件的事件 `MenuItems` 會由 `MenuItem` 本身引發，而不是 `ListView` 。 這不同于資料格的點按事件處理方式，其中會 `ListView` 引發事件，而不是資料格。 因為 `ListView` 正在引發事件，所以會提供其事件處理常式的重要資訊，例如選取或點擊的專案。

根據預設， `MenuItem` 無法得知它所屬的資料格。 `CommandParameter`屬性可在上用 `MenuItem` 來儲存物件，例如的後方物件 `MenuItem` `ViewCell` 。 `CommandParameter`屬性可以同時以 XAML 和 c # 來設定。

### <a name="xaml"></a>XAML

`MenuItem`元素可以在 XAML 集合中建立。 下列 XAML 示範已實作為兩個內容動作的自訂資料格：

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

在程式碼後置檔案中，確定 `Clicked` 已執行方法：

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
> `NavigationPageRenderer`適用于 Android 的可覆 `UpdateMenuItemIcon` 寫方法，可以用來從自訂載入圖示 `Drawable` 。 此覆寫可讓您使用 SVG 影像做為 Android 實例上的圖示 `MenuItem` 。

### <a name="code"></a>程式碼

您可以藉 `Cell` 由建立 `MenuItem` 實例並將其加入儲存格的集合中，在任何子類別中執行內容動作（只要它不是用來做為群組標頭） `ContextActions` 。 您可以針對內容動作設定下列屬性：

- **文字** &ndash;出現在功能表項目中的字串。
- 已**按一下** &ndash;按一下專案時的事件。
- **IsDestructive** &ndash;（選擇性）如果為 true，則會在 iOS 上以不同的方式轉譯專案。

您可以將多個內容動作加入至資料格，但只能有一個 `IsDestructive` 設定為 `true` 。 下列程式碼示範如何將內容動作新增至 `ViewCell` ：

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

使用者會預期在資料清單中向下提取會重新整理該清單。 [`ListView`](xref:Xamarin.Forms.ListView)此控制項支援這個現成的。 若要啟用提取重新整理功能，請將設定 [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) 為 `true` ：

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

對等的 C# 程式碼為：

```csharp
listView.IsPullToRefreshEnabled = true;
```

重新整理期間會出現一個微調，預設為黑色。 不過，您可以藉由將屬性設定為，在 iOS 和 Android 上變更微調色彩 `RefreshControlColor` [`Color`](xref:Xamarin.Forms.Color) ：

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

![ListView 提取以重新整理進行中](interactivity-images/refresh-start.png)

下列螢幕擷取畫面顯示在使用者釋放提取之後的提取重新整理，並在更新時顯示微調按鈕 [`ListView`](xref:Xamarin.Forms.ListView) ：

![ListView 提取以重新整理完成](interactivity-images/refresh-in-progress.png)

[`ListView`](xref:Xamarin.Forms.ListView)引發 [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) 事件以起始重新整理，並將 [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) 屬性設定為 `true` 。 要重新整理之內容所需的任何 `ListView` 程式碼，都應該由事件的事件處理常式 `Refreshing` 或由執行的方法執行 [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) 。 重新整理之後 `ListView` ，應該將 `IsRefreshing` 屬性設定為 `false` ，或 [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) 應呼叫方法，以表示重新整理已完成。

> [!NOTE]
> 定義時 [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) ， `CanExecute` 可以指定命令的方法來啟用或停用命令。

## <a name="detect-scrolling"></a>偵測滾動

[`ListView`](xref:Xamarin.Forms.ListView)定義 `Scrolled` 引發的事件，表示已發生捲軸。 下列 XAML 範例顯示 `ListView` ，它會設定事件的事件處理常式 `Scrolled` ：

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

在此程式碼範例中，事件 `OnListViewScrolled` 處理常式會在 `Scrolled` 事件引發時執行：

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

`OnListViewScrolled`事件處理常式 `ScrolledEventArgs` 會輸出事件隨附之物件的值。

## <a name="related-links"></a>相關連結

- [ListView 互動性（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
